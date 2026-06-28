# Incidents — 踩坑记录

这些是开发 html-design-workflow 过程中发生过的严重问题。SKILL.md 引用此文件以备查阅。

## ContentEditable 的 Python regex 损坏

### 背景
需要给 HTML 中的知识点文本（`.tl`、`.ss` 等类）添加 `contenteditable` 属性，方便打印版填页码。第一次尝试用 Python regex 修改 HTML source 文件。

### 问题
Python regex 不可靠地处理中文文本 + 嵌套 HTML 标签。一个典型的错误：

```python
# 错误：class="" 匹配的是空属性，不是 class="ul" 或 class="tag"
re.sub(r'class=""(?:ul|tag)', ...)
```

`class=""` 这个 regex pattern 匹配了空的 class 属性（`<span class="">`），而不是意图中的 `class="ul"` 或 `class="tag"`。这导致 regex 跨越元素边界吞掉了内容，把子元素包错了父级。

### 恢复步骤
1. 识别损坏模式：`</span>` 被截断为裸 `<`
2. 用 tag-whitelist 恢复：扫描 `<` 后跟已知标签名（span、div、b、/span 等），不是已知标签的裸 `<` → 恢复为 `</span>`
3. 重新平衡 span 深度：移除多余的 `</span>`
4. 修复嵌套：把变成兄弟节点的 `.ul` 移回 `.tl` 内部

### 教训
**永远不要用 Python/regex 修改 HTML 结构来实现 contenteditable。** 正确的做法是用 JS DOM TreeWalker 在浏览器运行时处理。

## Bash heredoc 传递 Python 导致转义损坏

### 问题
```bash
python3 << 'PYEOF'
# Python 代码里有 \\begin → bash 解释为退格字符
# \\quad → 被 mangled
# 最终 HTML 文件含有控制字符，KaTeX 渲染出错
PYEOF
```

即使加了 `'PYEOF'`（单引号阻止变量展开），反斜杠序列仍会被 bash 在传递到 Python 解释器的过程中损坏。

### 修复
先 Write 一个 `.py` 文件，再 `python3 file.py`。不要用 heredoc。

## Edit tool 改中文报 "String to replace not found"

### 问题
Edit 工具的 `old_string` 参数无法可靠匹配中文字符。即使肉眼看起来完全一样，encoding 不匹配导致替换失败。

### 修复
涉及中文的修改永远用 `.py` 脚本来做。

## div 不平衡导致卡片横着排

### 问题
多章 HTML 中，一个多余的 `</div>` 会关闭父级 `.container`，让后续所有 chapter 失去 sidebar offset（`margin-left`），横着水平排列。

### 检查
```bash
python3 -c "import re; c=open('file.html').read(); print(len(re.findall(r'<div[\s>]',c)) - len(re.findall(r'</div>',c)))"
```
必须输出 0。

## Write tool 的 JSON 编码吃掉 LaTeX 反斜杠

### 背景
用 Write tool 创建 Python 脚本时，脚本内容作为 JSON 字符串参数传输。JSON 有自己的一套转义序列，其中 `\f`、`\b`、`\t`、`\r`、`\n`、`\\`、`\"`、`\/`、`\uXXXX` 都是合法的 JSON 转义。

### 问题
如果 Python 脚本的 raw string 里写了 LaTeX 命令（如 `r'''\frac{1}{s+1}'''`），Write tool 的 JSON 解码器会处理这些反斜杠：

| 你写的 LaTeX | JSON 解码后变成 |  Python raw string 实际存的是 | 渲染结果 |
|-------------|----------------|------------------------------|---------|
| `\frac` | `\f`(换页符 0x0C) + `rac` | `\x0crac` | `rac` ❌ |
| `\beta` | `\b`(退格符 0x08) + `eta` | `\x08eta` | `eta` ❌ |
| `\tau` | `\t`(制表符 0x09) + `au` | `\tau` | `\tau` ❌ |
| `\rightarrow` | `\r`(回车符 0x0D) + `rightarrow` | `\x0drightarrow` | `rightarrow` ❌ |
| `\mathcal` | `\n`(换行符 0x0A) + `mathcal` | `\nmathcal` | `mathcal` ❌ |

注意：`\d`、`\i`、`\s`、`\a`、`\c`、`\o` 等**不是** JSON 转义序列，它们不受影响（`\displaystyle`、`\int`、`\sigma`、`\alpha` 等可正常通过）。

### 根因
这是三层转义的叠加（类似 Python 字符串 → re.sub 的二重打击，但这里第一层是 JSON）：
1. **JSON 层**：Write tool 的 content 参数是 JSON 字符串，`\f` → 换页符
2. **Python 层**：raw string 保字面量，所以 `\x0crac` 就真的是 `\x0crac`
3. **输出层**：HTML 文件里的 LaTeX 命令已损坏

### 修复方法
**在 Write tool 的 content 里，把 LaTeX 反斜杠加倍：**
- `\\frac` → JSON 把 `\\` 解码为 `\` → 文件里有 `\frac` → Python raw string 正确
- `\\beta` → 同理 → `\beta` 正确
- `\\tau` → 同理 → `\tau` 正确

{% raw %}
```python
# 正确：在 Write tool 的 content 参数里写双反斜杠
ch4 = r'''\frac{1}{s+1}'''  # 不对！\f 被 JSON 吃掉
ch4 = r'''\\frac{1}{s+1}''' # 正确！JSON 把 \\ 变 \，Python 保留 \frac
```
{% endraw %}

不需要加倍的非 JSON 敏感序列：`\displaystyle`、`\int`、`\sigma`、`\alpha`、`\omega`、`\sin`、`\cos`、`\lim`、`\infty`、`\prod`、`\sum`、`\cdots` 等可直接写。

### 事后检查
```bash
# 检查生成的 HTML 里有没有被 JSON 吃掉的 LaTeX
# 正确的 \frac 应该出现，而不是裸 rac{
python3 -c "
import re
c = open('output.html').read()
# 检查控制字符
bad = len(re.findall(r'[\x00-\x08\x0b\x0c\x0e-\x1f]', c))
# 检查常见损坏模式
rac = c.count('rac{') - c.count(chr(92)+'frac') - c.count(chr(92)+'dfrac')
if bad > 0 or rac > 0:
    print(f'WARNING: {bad} control chars, ~{rac} broken \\\\frac')
"
```

### 教训
**永远不要在 Write tool 的 Python 脚本 raw string 里直接写 `\frac`、`\beta`、`\tau`、`\rightarrow`、`\mathcal` 等以 JSON 敏感字符开头的 LaTeX 命令。必须加倍反斜杠：`\\frac`、`\\beta` 等。** 这件事应该在写脚本时一次做对，不要等到出问题再事后修复。

## Python 脚本读写同文件 → 二次运行内容重复

### 背景
设计 Python 脚本来修改 HTML 文件，脚本的流程是：读取源文件 → 提取章节 → 替换部分内容 → 写回同一文件。

### 问题
第一次运行正常（从原始文件中提取章节）。但第二次运行时，文件已经被第一次运行改写过了（比如加了双栏分隔符、替换了章节内容）。脚本再次读取时：

- 章节标记 `<!-- ===== Ch4 ===== -->` 出现了两次（第一次运行新增的 Ch4 + 被移入另一栏的旧标记）
- 提取边界错乱：`pos['ch0']` 到 `pos['ch2']` 之间的内容包含了新插入的 Ch4 内容 + 栏分隔符
- 最终输出中同一章节出现两次，HTML 结构被破坏，div 数量激增（270→312，多出 42 个 div）

### 根因
脚本的输入和输出是同一个文件路径。第一次运行已经修改了文件内容的结构（插入了新章节、添加了栏分隔符），第二次运行的提取逻辑基于的假设（"每个章节标记在 body 中只出现一次"）不再成立。

### 修复
两种方案：

**方案 A（推荐）**：设计脚本时，源文件和输出文件用不同路径。
```python
# 好：源文件和输出文件分离
SOURCE = "original.html"
OUTPUT = "modified.html"
# 从不覆盖输入文件
```

**方案 B（必须检查）**：如果必须读写同一文件，每次运行前从备份恢复。
```python
# 如果一定要写回同一文件，先检查备份是否存在
import os
assert os.path.exists(BACKUP), "请先创建备份！"
os.system(f'cp "{BACKUP}" "{FILE}"')  # 恢复后再处理
```

### 教训
**Python 脚本读写的目标如果是同一个文件，第二次运行会读到上一次修改后的内容。设计阶段就应该把输入和输出路径分开。** 如果做不到，至少要先从备份恢复再运行，但不能指望"只运行一次"——调试过程中必然需要反复测试。
