# 飞书 PRD 文档自动生成器

一个给产品经理用的 AI Skill：把零散的需求输入，端到端变成结构完整、带可视化图表、可直接落进飞书文档的 PRD。

核心理念：**PRD 不是功能清单，是价值论证。**

> 纯文字 PRD 是不合格的交付物 —— 核心机制必须有流程图，实施计划必须有时间轴。

---

## 解决什么问题

| 常见痛点 | 本 Skill 的处理 |
|---------|---------------|
| 写出来的 PRD 是流水账，看不出重点 | 内置「黄金表达法则」，每章首句必须是最核心的结论（金字塔原理） |
| 通篇文字，没人愿意读 | Phase 4 强制生成 SVG 图表，规范化的配色与版式 |
| 范围越写越大 | Non-goals 为必写章节，且每条要附注理由 |
| 关键机制没想清楚就开写 | 核心机制、公式、优先级、指标基线设人工校验点，不可自动决定 |
| 写完了还得手动搬进飞书 | 直接生成飞书文档 XML 并写入，返回链接 |

---

## 安装

```bash
# 用户级安装（所有项目可用）
git clone https://github.com/hanjoshua2026-dot/feishu-prd-generator.git ~/.workbuddy/skills/feishu-prd-generator

# 或项目级安装（团队共享）
git clone https://github.com/hanjoshua2026-dot/feishu-prd-generator.git .workbuddy/skills/feishu-prd-generator
```

依赖：WorkBuddy（或其他支持 `SKILL.md` 规范的 Agent）+ 已配置的飞书连接器（`lark-cli`）。

> **离线分发**：不想走 GitHub 的话，直接把整个目录拷到目标机器的上述路径即可，效果完全相同——Skill 本质就是一个带 `SKILL.md` 的文件夹。

---

## 使用方法

直接用自然语言触发即可：

```
写一份 PRD：客服工单按一次解决率动态分派
帮我优化这份 PRD 的结构和表达
把这份需求整理成飞书文档
```

Skill 会自动走完 6 个阶段：

| 阶段 | 做什么 | 是否可跳过 |
|------|--------|-----------|
| Phase 1 需求采集 | 收集产品类型、目标用户、核心问题、业务约束 | 否 |
| Phase 2 模板选择 | 按「新功能 / 迭代优化 / 系统重构」加载对应模板并生成骨架 | 否 |
| Phase 3 内容填充 | 按黄金表达法则逐章填充，4 个人工校验点 | 否 |
| Phase 4 可视化生成 | 设计 SVG 图表，转 PNG，渲染确认 | **⛔ 强制，不可跳过** |
| Phase 5 文档生成 | 写 XML → 插入飞书 → 嵌入图表 → 校验大纲 | 否 |
| Phase 6 质量检查 | 8 项 checklist，含「核心机制是否有流程图」 | 否 |

三种产品类型的模板差异化：

- **新功能** —— 从 0 到 1，需要完整背景论述和 Non-goals
- **迭代优化** —— 重点在变更分析和影响评估
- **系统重构** —— 重点在迁移方案和兼容性

---

## 目录结构

```
feishu-prd-generator/
├── SKILL.md                            # 主入口：工作流与操作命令
├── references/
│   ├── prd-templates.md                # 三种产品类型的 PRD 模板
│   ├── golden-expression-rules.md      # 黄金表达法则与正反例
│   ├── visual-design-guide.md          # SVG 图表设计规范 + 转 PNG 工作流
│   ├── feishu-xml-guide.md             # 飞书 XML 格式与 block 操作
│   └── case-library.md                 # PRD 案例库（虚构教学示例）
├── assets/                             # 预留：图片等静态资源
└── scripts/                            # 预留：自动化脚本
```

---

## 关键约束（踩过的坑）

这些是实践中撞出来的，改代码前先看一眼：

1. **SVG 必须转成 PNG 才能进飞书。** 飞书的 `overwrite` / `create --content` 不支持 `<whiteboard type="svg">`，CDATA 会被静默丢弃，只剩一个空壳标签。
2. **`--file` 和 `--content` 只接受相对路径。** 传绝对路径会报 `invalid file path`，必须先 `cd` 到工作目录。
3. **推荐用 XML 里的 `<img path="@./fig.png"/>` 直接插图**，位置由 XML 顺序天然决定，比 `media-insert` + `block_move_after` 少一步且不会错位。
4. **`block_replace` 不会自动删除旧子 block**，替换标题后需手动 batch delete。
5. **SVG viewBox 固定 `0 0 680 H`**，PNG 按 2x 宽（1360px）渲染保证高清。
6. **SVG 里别用 HTML 实体**（如 `&nbsp;`），改用普通空格。

---

## 隐私说明

本仓库不含任何真实业务数据。案例库中的「智能客服工单自动分派系统」是为教学目的编写的**虚构示例**，所有量化指标均为示例值；飞书文档链接与 Doc Token 均已替换为占位符（`https://<your-tenant>.feishu.cn/wiki/<WikiToken>`、`<DocToken>`）。

向案例库添加真实 PRD 前，请务必先脱敏：

- 去掉内部文档链接与 Doc Token
- 把真实经营指标替换为示例值或数量级描述
- 把内部管理问题抽象为通用场景
