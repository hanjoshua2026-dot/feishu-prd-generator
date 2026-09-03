# 飞书 XML 格式与 Block 操作指南

## 前置准备

在操作飞书文档前，必须先读取最新版 lark-cli 技能指南：

```bash
lark-cli skills read lark-doc references/lark-doc-update.md
lark-cli skills read lark-doc references/lark-doc-xml.md
```

## 常用 Block 类型

| XML 标签 | 用途 | 示例 |
|----------|------|------|
| `<h1>` | 一级标题 | `<h1 id="xxx">章节标题</h1>` |
| `<h3>` | 三级标题 | `<h3 id="xxx">子标题</h3>` |
| `<h4>` | 四级标题 | `<h4 id="xxx">小标题</h4>` |
| `<p>` | 段落 | `<p id="xxx">正文内容</p>` |
| `<ul><li>` | 无序列表 | `<ul><li id="xxx">项</li></ul>` |
| `<table>` | 表格 | 见下方模板 |
| `<pre>` | 代码块 | `<pre id="xxx" lang="Plain Text"><code>代码</code></pre>` |
| `<whiteboard>` | SVG图表 | `<whiteboard type="svg"><![CDATA[...]]></whiteboard>` |

## 文本样式

### 加粗

```xml
<p id="xxx"><b>加粗文字</b>正常文字</p>
```

### 红色标注（用于修改标记）

```xml
<p id="xxx"><span text-color="rgb(216,57,49)">红色文字</span></p>
```

### 混合样式

```xml
<p id="xxx"><b><span text-color="rgb(216,57,49)">红色加粗</span></b></p>
```

## 表格 XML 模板

```xml
<table id="tableId">
  <colgroup>
    <col width="160"/>
    <col width="345"/>
  </colgroup>
  <thead>
    <tr>
      <th><p id="th1">列标题1</p></th>
      <th><p id="th2">列标题2</p></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><p id="td1">单元格内容</p></td>
      <td><p id="td2">单元格内容</p></td>
    </tr>
  </tbody>
</table>
```

**表格垂直对齐**：

```xml
<td vertical-align="top"><p id="td1">内容</p></td>
```

## 代码块 XML 模板

```xml
<pre id="codeBlockId" caption="标题" lang="Plain Text">
  <code>每个客服工单量 = 总工单量 ÷ 客服数量</code>
</pre>
```

多行代码用 `<br/>` 分隔：

```xml
<pre id="codeBlockId" caption="稳定期公式" lang="Plain Text">
  <code>① 基础保底 = 10% × (池子 ÷ 客服数量)<br/><br/>② 竞争池 = 池子 − 保底总额<br/><br/>③ 实际工单量 = 保底 + 竞争池 × (解决率 ÷ Σ解决率)</code>
</pre>
```

## Whiteboard (SVG) XML 模板

> ⚠️ **`overwrite` 和 `create --content` 不支持 whiteboard 标签** — CDATA 内容会被丢弃。必须通过 SVG → PNG → `docs +media-insert` → `block_move_after` 插入图片（详见 `visual-design-guide.md`）。
>
> ⚠️ **`docs +media-insert --file` 只接受相对路径**，绝对路径会被拒绝。先 `cp` 到工作目录再用 `./filename` 传入。

```xml
<!-- ❌ 不要在 overwrite 中使用以下标签 -->
<whiteboard type="svg"><![CDATA[<svg viewBox="0 0 680 400" width="100%" xmlns="http://www.w3.org/2000/svg">
  <text x="340" y="30" text-anchor="middle" font-size="14" font-weight="500">图表标题</text>
</svg>]]></whiteboard>
```

## 操作命令

### 创建文档

```bash
lark-cli docs +create --title "PRD标题" --folder "folderToken"
```

### 获取文档大纲

```bash
lark-cli docs +fetch --doc "token" --scope outline
```

### 获取文档完整内容（XML + block ID）

```bash
lark-cli docs +fetch --doc "token" --scope full --doc-format xml --detail with-ids
```

### 在指定 block 后插入内容

```bash
lark-cli docs +update --doc "token" --command block_insert_after --block-id "targetBlockId" --content @content.xml
```

### 替换指定 block 的内容

```bash
lark-cli docs +update --doc "token" --command block_replace --block-id "targetBlockId" --content @content.xml
```

### 删除 block（支持批量）

```bash
lark-cli docs +update --doc "token" --command block_delete --block-id "id1,id2,id3"
```

## 长内容处理

XML 内容超过 50 行时，写入临时文件再用 `@file` 传入：

```bash
# 1. 将 XML 写入临时文件
cat > /tmp/prd_content.xml << 'EOF'
<h1 id="xxx">章节标题</h1>
<p id="xxx">内容</p>
...
EOF

# 2. 用 @file 方式传入
lark-cli docs +update --doc "token" --command block_insert_after --block-id "targetId" --content @/tmp/prd_content.xml
```

## block_replace 注意事项

**block_replace 替换 H1 标题 block 时，旧的子 block 不会自动删除**，会导致内容重复。

正确操作流程：

1. 先 `fetch --scope full --detail with-ids` 获取该章节所有 block ID
2. 用 `block_replace` 替换 H1 标题 block（新内容包含整个章节）
3. 用 `block_delete` 批量删除旧的子 block ID（H1 之外的所有旧 block）
4. 再次 `fetch --scope outline` 验证结构

## Python 生成 XML 模板

当 XML 内容包含动态数据时，用 Python 脚本生成：

```python
def cdata_svg(svg_content):
    """将 SVG 包装为 CDATA"""
    return f"<![CDATA[{svg_content}]]>"

# 生成章节 XML
chapter_xml = f'''<h1 id="ch1">一、章节标题</h1>
<whiteboard type="svg">{cdata_svg(svg_content)}</whiteboard>
<p id="p1"><b>核心原则：</b>一句话说明</p>
<table id="t1">
  <colgroup><col width="160"/><col width="345"/></colgroup>
  <thead><tr><th><p id="th1">列1</p></th><th><p id="th2">列2</p></th></tr></thead>
  <tbody><tr><td><p id="td1">数据1</p></td><td><p id="td2">数据2</p></td></tr></tbody>
</table>'''

# 写入临时文件
with open('/tmp/prd_chapter.xml', 'w') as f:
    f.write(chapter_xml)
```

## 获取文档 Wiki token

飞书 wiki 文档的 URL 格式：`https://xxx.feishu.cn/wiki/WikiToken`

Wiki token 不等于 doc token。从 wiki token 获取 doc token：

```bash
lark-cli wiki +node --token "wikiToken" --fields doc_token
```
