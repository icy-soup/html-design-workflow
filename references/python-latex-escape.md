# Python LaTeX 三层转义详解

在 Python 脚本中生成含 LaTeX 的 HTML 时，反斜杠会在三个不同层面被吃。**写 .py 文件前必须读这篇，一次性防住三层。**

---

## 第一层：Write tool 的 JSON 编码

### 问题
Write tool 的 `content` 参数是 JSON 字符串。JSON 有自己的转义序列：`\f`、`\b`、`\t`、`\r`、`\n`、`\\` 等。

```python
# 如果你在 Write tool 里写了这个：
ch4 = r'''\frac{1}{s+1}'''
# JSON 解码器看到 \f → 换页符(0x0C) → 文件里实际存的是：
ch4 = r'''\x0crac{1}{s+1}'''   # \frac 已损坏！
```

### 受影响 vs 不受影响的 LaTeX 命令

| 你写的 LaTeX | JSON 解码后 | 结果 |
|-------------|------------|------|
| `\frac` | `\f`(0x0C)+`rac` | `rac` ❌ |
| `\beta` | `\b`(0x08)+`eta` | `eta` ❌ |
| `\tau` | `\t`(0x09)+`au` | `\tau` ❌ |
| `\rightarrow` | `\r`(0x0D)+`rightarrow` | `rightarrow` ❌ |
| `\mathcal` | `\n`(0x0A)+`mathcal` | `mathcal` ❌ |
| `\text` | `\t`(0x09)+`ext` | `ext` ❌ |
| `\underbrace` | `\uXXXX`(解析失败) | 取决于 JSON 解析器 |
| **`\displaystyle`** | 不受影响（`\d`非JSON转义） | ✅ |
| **`\int`, `\sigma`** | 不受影响（`\i`/`\s`非JSON转义） | ✅ |
| **`\alpha`** | 不受影响（`\a`非JSON转义） | ✅ |
| **`\omega`, `\infty`** | 不受影响 | ✅ |
| **`\sin`, `\cos`, `\lim`** | 不受影响 | ✅ |
| **`\prod`, `\sum`** | 不受影响 | ✅ |
| **`\cdots`** | 不受影响 | ✅ |

### 修复
在 Write tool 的 content 里，把受影响的命令加倍反斜杠：

```python
# 错误：\f 被 JSON 吃掉
ch4 = r'''\frac{1}{s+1}'''     # → rac ❌
ch4 = r'''\beta$$'''           # → eta ❌
ch4 = r'''\mathcal{L}'''       # → mathcal ❌

# 正确：加倍，JSON 把 \\ 解码为 \，Python 保留 \
ch4 = r'''\\frac{1}{s+1}'''    # → \frac ✅
ch4 = r'''\\beta$$'''          # → \beta ✅
ch4 = r'''\\mathcal{L}'''      # → \mathcal ✅
```

记忆法：只有 `\b` `\f` `\t` `\r` `\n` `\\` `\u` 开头的 LaTeX 命令需要加倍，其他不用。

---

## 第二层：Python 字符串解析

### 问题
普通 Python 字符串（非 raw string）把 `\f`、`\a`、`\b`、`\n`、`\t` 解释为转义序列：

| 你写的 | Python 实际存的是 | 结果 |
|--------|------------------|------|
| `'\frac{1}{T}'` | `\x0crac{1}{T}`（`\f`=换页符） | `rac{1}{T}` ❌ |
| `'\alpha'` | `\x07lpha`（`\a`=响铃符） | `lpha` ❌ |
| `'\beta'` | `\x08eta`（`\b`=退格符） | `eta` ❌ |

注：经过第一层后文件里已经是 `\frac`（单反斜杠），所以 raw string `r'\frac'` 能正确保留。

### 修复
**始终用 raw string（`r'''...'''`）写含 LaTeX 的字符串。**

---

## 第三层：`re.sub` replacement 再解释

### 问题
即使 Python 字符串正确，`re.sub` 的 replacement 会再次解释转义：

```python
# \a 在 re.sub replacement 里再被转为响铃
re.sub(r'pat', '\\alpha', c)    # → 还是 \x07lpha ❌
```

### 修复
replacement 也要用 raw string：
```python
re.sub(r'pat', r'\\alpha', c)   # → \alpha ✅
```

---

## 总纲

| 层 | 防范手法 | 对 `\frac` 写什么 |
|---|---------|-----------------|
| ① JSON（Write tool） | 加倍 `\\` | 在 Write tool 里写 `\\frac` |
| ② Python 字符串 | 用 raw string `r''` | `r'\\frac'`（JSON 后文件里是 `r'\frac'`） |
| ③ `re.sub` replacement | replacement 也用 raw string | `r'\\frac'` |

**关键：这三件事在写 .py 文件时一次做对。** 不要等 HTML 生成后发现公式坏了再修。

---

## 事后验证

```bash
python3 -c "
import re
c = open('output.html').read()
# 检查控制字符（来自 JSON/Python 转义损坏）
ctrl = len(re.findall(r'[\x00-\x08\x0b\x0c\x0e-\x1f]', c))
# 检查裸 rac{（来自 \frac 或 \dfrac 损坏）
frac_ok = c.count(chr(92)+'frac') + c.count(chr(92)+'dfrac')
rac_total = c.count('rac{')
print(f'控制字符: {ctrl}, 裸露 rac{{: {rac_total - frac_ok}')
if ctrl > 0 or rac_total > frac_ok:
    print('WARNING: LaTeX 损坏！')
"
```

验证技巧：不要用 `c[i-1] == '\\'` 判断 `\frac` 是否完整。`\frac` = `\f` + `rac{`，`rac{` 前面是 `f` 不是 `\`。正确方式：grep 控制字符，或 grep `[^\\]rac{` 找裸 `rac{`。

---

## 另一个坑：Python 脚本不要读写同一个文件

如果脚本流程是"读 source.html → 改 → 写回 source.html"，**第二次运行会出问题**——第一次已经把文件结构改了，第二次再读到的章节标记重复、提取边界错乱。

```python
# 错误：读写同文件
with open('file.html') as f: html = f.read()
# ...修改 html...
with open('file.html', 'w') as f: f.write(html)   # 第二次运行炸了

# 正确：源文件和输出文件分离
SOURCE = "original.html"   # 只读
OUTPUT = "modified.html"   # 只写
```

如果必须写回同一文件，每次运行前从备份恢复。（参考 incidents.md 详解）
