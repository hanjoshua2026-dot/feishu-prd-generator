---
name: feishu-prd-generator
description: "飞书 PRD 文档自动生成器。从需求输入到结构完整、视觉丰富、表达专业的飞书 PRD 文档的端到端生成。支持三种产品类型（新功能/迭代优化/系统重构）的模板差异化，内置黄金表达法则和可视化设计规范，关键章节自动填充与人工校验点结合，支持历史优秀 PRD 案例参考。触发场景：写 PRD、生成飞书文档、产品需求文档、feature spec、需求评审文档、PRD 模板、飞书 PRD。"
agent_created: true
---

# 飞书 PRD 文档自动生成器

## 概述

将产品需求转化为结构完整、视觉丰富、表达专业的飞书 PRD 文档。核心理念：**PRD 不是功能清单，是价值论证**。

## 触发条件

- 用户要求编写 PRD / 需求文档 / Feature Spec
- 用户要求生成飞书文档并涉及产品需求
- 用户提供需求描述并希望转化为结构化文档
- 用户要求优化已有 PRD 的可读性/结构/表达

## 端到端工作流

### Phase 1: 需求采集（输入）

收集以下信息（缺失项主动询问，但避免一次性问太多）：

| 信息项 | 必需 | 示例 |
|--------|------|------|
| 产品类型 | 是 | 新功能 / 迭代优化 / 系统重构 |
| 需求一句话描述 | 是 | "客服工单按一次解决率动态分派" |
| 目标用户 | 是 | 内部业务方 / C端用户 / 运营 |
| 核心问题 | 是 | "抢单靠手速，质量没人管" |
| 业务约束 | 否 | DAU、时间线、技术限制 |
| 竞品参考 | 否 | 阿里妈妈/巨量引擎 |
| 历史数据 | 否 | 当前解决率、工单量 |

**产品类型判定**：
- **新功能**：从 0 到 1 的全新功能，需要完整背景论述和 Non-goals
- **迭代优化**：已有功能的改进，重点是变更分析和影响评估
- **系统重构**：底层架构调整，重点是迁移方案和兼容性

### Phase 2: 模板选择与骨架生成

根据产品类型加载对应模板（详见 `references/prd-templates.md`）：

1. 读取模板结构，生成 PRD 骨架（章节标题 + 占位符）
2. 将骨架展示给用户确认，标注自动填充项和需人工补充项
3. 用户确认后进入 Phase 3

### Phase 3: 内容填充（核心）

按章节顺序填充，每章遵循「黄金表达法则」（详见 `references/golden-expression-rules.md`）：

#### 自动填充章节（基于输入信息推导）

| 章节 | 自动填充逻辑 |
|------|-------------|
| 一句话说清楚 | **直接复用"问题与目标"中的"问题"部分**（即核心痛点/现状问题），不要另写 [谁][做什么][达到什么效果] 句式——那类套模板的话易空泛。问题已上移至此，故"问题与目标"只保留"目标"。 |
| 问题与目标 | 只保留"目标"子章节（"问题"已上移至"一句话说清楚"）；目标不超过 3 个，用 SMART 表述 |
| 关键参数 | 从业务约束提取，无约束则标注"待补充" |
| Non-goals | 根据需求范围自动排除明显不做项 |

#### 人工校验点（必须用户确认）

| 校验点 | 位置 | 原因 |
|--------|------|------|
| 核心机制确认 | 第三章 | 机制设计是 PRD 灵魂，不可自动决定 |
| 公式/规则确认 | 第四章 | 业务逻辑必须人工验证 |
| 优先级确认 | 功能清单 | P0/P1 判断需要业务上下文 |
| 指标基线确认 | 指标章节 | 基线数据需要从实际系统获取 |

#### 填充顺序

```
1. 一句话说清楚 → 2. 问题与目标 → 3. 核心机制（校验点）
→ 4. 详细需求（校验点）→ 5. 功能清单（校验点）
→ 6. Non-goals → 7. 数据指标（校验点）→ 8. 异常处理
→ 9. 风险 → 10. 实施计划
```

### Phase 4: 可视化生成（必选，不可跳过）

> ⛔ **GATE：此阶段为强制步骤，不可跳过。** 完成 Phase 3 内容填充后，必须先完成本阶段图表生成，才能进入 Phase 5 文档创建。跳过此阶段会导致 PRD 可读性严重下降——纯文字 PRD 是不合格的交付物。

为 PRD 中适合可视化的章节自动设计 SVG 图表（设计规范详见 `references/visual-design-guide.md`）。

#### 必须生成图表的章节（缺一不可）

| 章节 | 图表类型 | 说明 |
|------|---------|------|
| 核心机制 | 流程图 | PRD 灵魂章节，必须有流程图/状态图 |
| 架构方案（系统重构类） | 架构图 | 新架构概览必须可视化 |
| 实施计划 | 时间轴 | 阶段划分用时间轴比表格更直观 |

#### 按需生成图表的章节

| 场景 | 图表类型 | 典型章节 |
|------|---------|---------|
| 公式/分配逻辑 | 公式图解 | 流量分配、计算规则 |
| 决策链路 | 决策树 | 优先级判定、降级逻辑 |
| 指标体系 | 分层架构图 | 数据指标、监控体系 |
| 对比分析 | 表格 | 竞品分析、方案对比（表格即可，不需 SVG） |

#### SVG → PNG → 飞书文档 完整工作流（必读）

飞书文档的 `overwrite` / `create --content` 不支持 `<whiteboard type="svg">` 标签，SVG 会被静默丢弃。必须按以下步骤操作：

```bash
# Step 1: 生成 SVG 文件（按 visual-design-guide.md 规范设计）
# viewBox 固定 "0 0 680 H"，宽度 680px

# Step 2: 用 show_widget 先渲染给用户确认效果
# 确认无误后再转 PNG

# Step 3: SVG 转 PNG（2x 宽度，确保高清）
# 方式 A: Node.js sharp（推荐，无需系统库）
# 注意：node 版本目录名以实际安装为准（常见为 22.22.2-2，而非 22.22.2），
#       执行前先 ls ~/.workbuddy/binaries/node/versions/ 确认，否则报 no such file
cd ~/.workbuddy/binaries/node/workspace && \
NODE_PATH=~/.workbuddy/binaries/node/workspace/node_modules \
node -e "
const sharp = require('sharp');
const fs = require('fs');
sharp(fs.readFileSync('/tmp/diagram.svg'))
  .resize({ width: 1360 }).png()
  .toFile('/tmp/diagram.png')
  .then(() => console.log('done'));
"

# 方式 B: Python cairosvg（需要系统 cairo 库，macOS 可能缺失）
# python3 -c "import cairosvg; cairosvg.svg2png(url='in.svg', write_to='out.png', output_width=1360)"

# Step 4: 插入图片（推荐方式 A，一步到位且位置精确）

# 方式 A（推荐）：在正文 XML 中直接用 <img> 标签插入，随正文一起写入
#   章节 XML 中写：<img path="@./prd-assets/fig1.png" width="680" caption="图1：标题"/>
#   然后正常写正文即可：
#     lark-cli docs +update --doc "docToken" --command append --content @./part.xml
#   优点：图片位置由 XML 顺序天然决定，无需事后 block_move_after；
#         一次请求同时完成正文与图片，返回 new_blocks 中 block_type=image 即成功

# 方式 B（备选）：先单独插入图片，再移动到目标位置
#   注意 --file 必须是相对路径，绝对路径会被 CLI 拒绝
cp /tmp/diagram.png ./diagram.png
lark-cli docs +media-insert --doc "docToken" --file ./diagram.png \
  --caption "图1：流程图标题" --align center --width 680
lark-cli docs +update --doc "docToken" --command block_move_after \
  --block-id "目标位置前面的blockId" --src-block-ids "图片blockId"
```

**SVG 设计规范**：
- viewBox 固定 `0 0 680 H`（宽度 680px，高度按内容）
- 配色：蓝(#378ADD) 主流程 / 绿(#639922) 正常 / 橙(#E8A33D) 警告 / 红(#D83931) 异常
- 字体：13px 正文 / 14px 标题 / 12px 辅助说明
- 风格：扁平化、无渐变、圆角矩形(rx=8)
- 禁止使用 HTML 实体（如 `&nbsp;`），改用普通空格
- PNG 渲染 2x 宽（1360px），确保高清显示

### Phase 5: 飞书文档生成

将 PRD 内容转化为飞书文档（详见 `references/feishu-xml-guide.md`）：

1. **创建文档**：`lark-cli docs +create` 或在已有文档中追加
2. **内容写入**：使用 `append`（等价于 `block_insert_after --block-id -1`）逐章写入 XML 内容，或 `overwrite` 全文重写
   - ⚠️ `--content @file` **只接受相对路径**，传绝对路径会报 `invalid file path`。必须先 `cd` 到目标目录，再写 `@./part.xml`
   - 长内容（超过 50 行）先写入临时 XML 文件，再用 `@./file.xml` 传入
   - 分批写入时每批都用 `append`，写入顺序即文档顺序
3. **嵌入图表（必选）**：⚠️ `overwrite` / `create --content` 不支持 `<whiteboard type="svg">` 标签，SVG 会被静默丢弃。推荐**在正文 XML 中用 `<img path="@./fig.png"/>` 直接插入**（详见 Phase 4 方式 A），位置精确且一步到位；备选 `docs +media-insert` + `block_move_after`。**如果 Phase 4 生成了图表，本步骤不可跳过——否则图表只存在于临时文件中，文档内看不到。**
4. **格式校验**：fetch 文档大纲确认结构完整
5. **链接交付**：返回飞书文档 URL

### Phase 6: 质量检查

生成后自动执行以下检查：

- [ ] 每章首句是否为该章核心结论（金字塔原理）
- [ ] 是否有 Non-goals 章节
- [ ] 指标是否有基线和目标值
- [ ] 异常处理是否覆盖边界情况
- [ ] **核心机制章节是否有流程图**（⛔ 不通过则文档不合格，需补图后重新交付）
- [ ] 实施计划章节是否有时间轴/甘特图
- [ ] 表格是否替代了长段文字列表
- [ ] 是否存在重复表述

## 历史案例参考机制

当用户提供历史 PRD 案例时（飞书链接或本地文件）：

1. 提取案例的章节结构、表达方式、可视化模式
2. 存入 `references/case-library.md` 作为参考
3. 后续生成时自动匹配相似场景的案例

**内置参考案例**：智能客服工单自动分派系统 PRD（虚构教学示例，详见 `references/case-library.md`）

## 关键操作命令

```bash
# 创建飞书文档
lark-cli docs +create --title "PRD标题" --folder "文件夹token"

# 写入内容（XML格式）
lark-cli docs +update --doc "token" --command block_insert_after --block-id "targetBlockId" --content @content.xml

# 替换内容
lark-cli docs +update --doc "token" --command block_replace --block-id "targetBlockId" --content @content.xml

# 删除block
lark-cli docs +update --doc "token" --command block_delete --block-id "blockId1,blockId2"

# 获取文档大纲
lark-cli docs +fetch --doc "token" --scope outline

# 获取文档内容（XML + block ID）
lark-cli docs +fetch --doc "token" --scope full --doc-format xml --detail with-ids

# 读取飞书文档技能指南
lark-cli skills read lark-doc references/lark-doc-update.md
lark-cli skills read lark-doc references/lark-doc-xml.md
```

## 参考文档索引

| 文件 | 用途 | 何时读取 |
|------|------|---------|
| `references/prd-templates.md` | 三种产品类型的 PRD 模板 | Phase 2 模板选择时 |
| `references/golden-expression-rules.md` | 黄金表达法则与示例 | Phase 3 内容填充时 |
| `references/visual-design-guide.md` | SVG 可视化设计规范 | Phase 4 可视化增强时 |
| `references/feishu-xml-guide.md` | 飞书 XML 格式与 block 操作 | Phase 5 文档生成时 |
| `references/case-library.md` | 历史 PRD 案例库 | 需要参考时 |

## 注意事项

- **Why 优先于 What**：每章先说"为什么做"，再说"做什么"
- **Non-goals 必写**：防止范围蔓延
- **表格优于列表**：结构化信息一律用表格
- **校验点不可跳过**：核心机制和公式必须人工确认后才写入
- **⛔ 图表生成不可跳过**：核心机制章节必须有流程图，实施计划章节必须有时间轴。纯文字 PRD 不合格。Phase 4 是强制步骤，不可跳到 Phase 5
- **SVG 先渲染确认**：生成 SVG 后先用 show_widget 展示给用户确认，再转为 PNG，用 `docs +media-insert` 插入飞书文档
- **media-insert 用相对路径**：`--file` 参数必须是当前目录下的相对路径（如 `./diagram.png`），绝对路径会被拒绝。先 `cp` 到工作目录再操作
- **长内容用文件**：XML 内容超过 50 行时写入临时文件，用 `@file` 方式传入 lark-cli
- **block_replace 清理**：替换 H1 标题 block 后，旧子 block 不会自动删除，需手动 batch delete
