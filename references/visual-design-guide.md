# SVG 可视化设计规范

为飞书 PRD 文档设计嵌入式 SVG 图表。所有图表通过 `<whiteboard type="svg">` 标签嵌入飞书文档。

---

## 基础规范

### 画布

```
viewBox="0 0 680 H"  // 宽度固定 680px，高度按内容自适应
width="100%"          // 响应式宽度
```

### 配色体系

| 用途 | 颜色 | 填充 | 描边 |
|------|------|------|------|
| 主流程 | 蓝 | #E6F1FB | #378ADD |
| 深蓝节点 | 蓝 | #B5D4F4 | #378ADD |
| 正常/成功 | 绿 | #C0DD97 | #639922 |
| 警告/注意 | 橙 | #FCE8B2 | #E8A33D |
| 异常/错误 | 红 | #F4CCCC | #D83931 |
| 中性/容器 | 灰 | #F0F0F0 | #999999 |
| 背景 | 白 | #FFFFFF | - |

### 字体

| 类型 | 大小 | 用途 |
|------|------|------|
| 标题 | 14px / font-weight:500 | 图表标题、节点标题 |
| 正文 | 13px | 节点内容 |
| 辅助 | 12px | 说明文字、注释 |

### 通用样式

```css
.t  { font-family: var(--font-sans); font-size: 13px; fill: var(--color-text-primary) }
.th { font-family: var(--font-sans); font-size: 14px; font-weight: 500; fill: var(--color-text-primary) }
.ts { font-family: var(--font-sans); font-size: 12px; fill: var(--color-text-secondary) }
.box { rx: 8; stroke-width: 0.5; fill-opacity: 0.15 }
.arr { fill: none; stroke-width: 1.5; marker-end: url(#arrow) }
```

### 箭头定义

```xml
<defs>
  <marker id="arrow" viewBox="0 0 10 10" refX="8" refY="5"
          markerWidth="6" markerHeight="6" orient="auto-start-reverse">
    <path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke"
          stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
  </marker>
</defs>
```

---

## 图表类型模板

### 1. 流程图（纵向）

适用：核心机制、数据流、状态转换

```xml
<svg viewBox="0 0 680 400" width="100%" xmlns="http://www.w3.org/2000/svg">
  <defs><!-- arrow marker --></defs>
  <style><!-- 通用样式 --></style>

  <!-- 标题 -->
  <text class="th" x="340" y="30" text-anchor="middle">[流程标题]</text>

  <!-- 步骤节点 -->
  <g transform="translate(X, Y)">
    <rect x="0" y="0" width="W" height="H" rx="8"
          fill="#E6F1FB" stroke="#378ADD" stroke-width="0.5"/>
    <text class="th" x="W/2" y="20" text-anchor="middle">[步骤标题]</text>
    <text class="ts" x="W/2" y="38" text-anchor="middle">[说明]</text>
  </g>

  <!-- 连接箭头 -->
  <line x1="X1" y1="Y1" x2="X2" y2="Y2"
        fill="none" stroke="#378ADD" stroke-width="1.5" marker-end="url(#arrow)"/>
</svg>
```

### 2. 对比图（左右分栏）

适用：探索期 vs 利用期、变更前 vs 变更后

```xml
<svg viewBox="0 0 680 420" width="100%" xmlns="http://www.w3.org/2000/svg">
  <!-- 左栏 -->
  <g transform="translate(40, 60)">
    <rect x="0" y="0" width="290" height="330" rx="8"
          fill="#E6F1FB" stroke="#378ADD" stroke-width="0.5" fill-opacity="0.15"/>
    <text class="th" x="145" y="30" text-anchor="middle">[左栏标题]</text>
    <!-- 子节点 -->
  </g>

  <!-- 右栏 -->
  <g transform="translate(350, 60)">
    <rect x="0" y="0" width="290" height="330" rx="8"
          fill="#C0DD97" stroke="#639922" stroke-width="0.5" fill-opacity="0.15"/>
    <text class="th" x="145" y="30" text-anchor="middle">[右栏标题]</text>
    <!-- 子节点 -->
  </g>
</svg>
```

### 3. 决策树

适用：优先级判定、降级逻辑、分支处理

```xml
<svg viewBox="0 0 680 400" width="100%" xmlns="http://www.w3.org/2000/svg">
  <!-- 决策菱形 -->
  <polygon points="340,60 440,100 340,140 240,100"
           fill="#FCE8B2" stroke="#E8A33D" stroke-width="0.5"/>
  <text class="th" x="340" y="105" text-anchor="middle">[决策条件]</text>

  <!-- 分支标签 -->
  <text class="ts" x="280" y="160" text-anchor="end">是</text>
  <text class="ts" x="400" y="160">否</text>

  <!-- 分支结果节点 -->
  <rect x="200" y="170" width="160" height="50" rx="8"
        fill="#C0DD97" stroke="#639922"/>
  <rect x="320" y="170" width="160" height="50" rx="8"
        fill="#F4CCCC" stroke="#D83931"/>
</svg>
```

### 4. 分层架构图

适用：指标体系、系统架构、数据分层

```xml
<svg viewBox="0 0 680 360" width="100%" xmlns="http://www.w3.org/2000/svg">
  <!-- Layer 1 -->
  <g transform="translate(40, 50)">
    <rect width="600" height="80" rx="8" fill="#E6F1FB" stroke="#378ADD"/>
    <text class="th" x="300" y="30" text-anchor="middle">[第一层标题]</text>
    <text class="ts" x="300" y="50" text-anchor="middle">[说明]</text>
  </g>

  <!-- Layer 2 -->
  <g transform="translate(40, 150)">
    <rect width="290" height="80" rx="8" fill="#C0DD97" stroke="#639922"/>
    <rect x="310" width="290" height="80" rx="8" fill="#FCE8B2" stroke="#E8A33D"/>
  </g>
</svg>
```

### 5. 时间轴

适用：实施计划、生命周期、灰度方案

```xml
<svg viewBox="0 0 680 200" width="100%" xmlns="http://www.w3.org/2000/svg">
  <!-- 主轴线 -->
  <line x1="60" y1="100" x2="620" y2="100" stroke="#999" stroke-width="2"/>

  <!-- 阶段节点 -->
  <circle cx="120" cy="100" r="8" fill="#378ADD"/>
  <text class="th" x="120" y="80" text-anchor="middle">[阶段1]</text>
  <text class="ts" x="120" y="130" text-anchor="middle">[时间]</text>

  <circle cx="300" cy="100" r="8" fill="#639922"/>
  <text class="th" x="300" y="80" text-anchor="middle">[阶段2]</text>
  <text class="ts" x="300" y="130" text-anchor="middle">[时间]</text>

  <circle cx="500" cy="100" r="8" fill="#E8A33D"/>
  <text class="th" x="500" y="80" text-anchor="middle">[阶段3]</text>
  <text class="ts" x="500" y="130" text-anchor="middle">[时间]</text>
</svg>
```

---

## 嵌入飞书文档

**⚠️ 重要：`overwrite` 不支持 `<whiteboard type="svg">` 标签** — CDATA 内容会被静默丢弃，只留下空 `<whiteboard token="..."/>` 壳。必须换用图片嵌入方式。

### 正确方式 A（推荐）：SVG → PNG → 在正文 XML 中用 img 标签直接插入

在章节 XML 中引用 PNG 文件，随正文一起写入。图片位置由 XML 顺序天然决定，无需事后移动，一次请求同时完成正文与图片：

```xml
<h1 id="c3">三、核心机制</h1>
<img path="@./prd-assets/fig1.png" width="680" caption="图1：核心机制流程图"/>
<p id="p1">机制说明文字</p>
```

```bash
cd <工作目录>    # 必须先 cd，--content 只接受相对路径
lark-cli docs +update --doc "docToken" --command append --content @./part.xml
# 返回的 data.new_blocks 中出现 block_type=image 即表示图片插入成功
```

### 正确方式 B（备选）：SVG → PNG → media-insert → block_move_after

```bash
# Step 1: 生成 SVG（按本规范设计，viewBox 0 0 680 H）
# Step 2: 先用 show_widget 渲染确认效果
# Step 3: 将 SVG 转为 PNG
# Step 4: 用 docs +media-insert 插入图片
# Step 5: 用 block_move_after 将图片移动到正确位置
```

> ⚠️ **`--file` 与 `--content` 都必须是相对路径**（如 `./diagram.png`、`./part.xml`），绝对路径会被 lark-cli 拒绝（报 `invalid file path`）。先 `cd` 到工作目录或 `cp` 到工作目录再操作。

```bash
# Step 3: SVG 转 PNG
# 方式 A: Node.js sharp（推荐，无需系统 cairo 库）
# 注意 node 版本目录以实际安装为准（常见为 22.22.2-2），先 ls versions/ 确认
cd ~/.workbuddy/binaries/node/workspace && \
NODE_PATH=~/.workbuddy/binaries/node/workspace/node_modules \
node -e "
const sharp = require('sharp'); const fs = require('fs');
sharp(fs.readFileSync('/tmp/diagram.svg')).resize({width:1360}).png()
  .toFile('/tmp/diagram.png').then(()=>console.log('done'));
"

# 方式 B: Python cairosvg（需要系统 cairo 库，macOS 默认无，不推荐）
# python3 -c "import cairosvg; cairosvg.svg2png(url='in.svg', write_to='out.png', output_width=1360)"

# Step 4: 插入图片（注意相对路径！）
cp /tmp/diagram.png ./diagram.png
lark-cli docs +media-insert --doc "docToken" --file ./diagram.png \
  --caption "图1：流程图标题" --align center --width 680

# Step 5: 移动图片到正确位置（media-insert 返回 block_id）
lark-cli docs +update --doc "docToken" --command block_move_after \
  --block-id "targetBlockId" --src-block-ids "imgBlockId"
```

### SVG 转 PNG 工具推荐

| 工具 | 安装 | 优点 | 缺点 |
|------|------|------|------|
| **Node.js sharp（推荐）** | `npm install sharp` | 无需系统库，开箱即用 | 需要 Node.js 环境 |
| Python cairosvg | `pip install cairosvg` | API 简洁 | 需要系统 cairo 库，macOS 默认缺失 |

### 注意事项
- SVG 中**禁止使用 HTML 实体**（如 `&nbsp;`），改用普通空格或 Unicode 字符
- PNG 生成时渲染 2x 宽（1360px），确保高清显示
- 不要在 SVG 中使用外部资源引用
- 文本使用 `text-anchor` 和 `dominant-baseline` 精确定位
- 颜色使用十六进制，不用 rgba/hsl
- 嵌入前先用 `show_widget` 渲染确认效果

---

## 图表设计检查清单

- [ ] viewBox 宽度为 680
- [ ] 配色符合规范（蓝/绿/橙/红四色体系）
- [ ] 字体大小 12-14px，可读
- [ ] 箭头方向正确
- [ ] 文本居中对齐
- [ ] 无渐变、无阴影、无外部引用
- [ ] 图表标题清晰
- [ ] 节点间距合理，不重叠
