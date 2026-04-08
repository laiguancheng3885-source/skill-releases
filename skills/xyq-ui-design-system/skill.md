---
name: xyq-ui-design-system
description: >
  Apply the XYQ畅玩服 Fantasy/RPG retro-modern UI design system when creating,
  reviewing, or refactoring game interface HTML components. Enforces immutable
  color locks, typography scale (14px/16px only), component dimensions, and
  layout rules. Make sure to use this skill whenever the user asks to design,
  build, review, or fix any XYQ畅玩服 game UI — even if they don't explicitly
  mention "design system" or "spec". Triggers on: 做界面, 设计界面, 做个弹窗,
  做个窗口, 游戏UI, 西游Q传界面, 畅玩服UI, 游戏界面设计, 按钮组件, 颜色规范,
  排版规范, 组件规范, RPG game UI, retro game interface, fantasy UI, HTML game
  design, color spec, design system, review this HTML, check my UI.
allowed-tools: Bash Read Write Edit Glob Grep
metadata:
  tags: ui-design, game-ui, xyq, fantasy-rpg, design-system, html, retro-modern
  version: "2.5"
---

# XYQ畅玩服 UI Design System

> Style: Fantasy/RPG Retro-Modern | Output: HTML only | Max window: 800×600px

## When to use this skill

- 创建新游戏 UI 组件：窗口、按钮、页签、输入框、列表、头像
- 审查或审计现有 HTML/CSS 的设计规范合规性
- 解决颜色、排版、间距或布局约束的设计决策
- 为 XYQ畅玩服 生成完整 HTML 界面导出文件
- 扩展或组合已有面板时确保视觉一致性
- 将不合规组件转换为符合设计规范的版本

---

## 🗂️ Master Index（使用前必查此表）

> ⛔ **强制规则**：遇到任何组件/颜色/布局/路径问题，必须先查此索引找到对应章节，再读取。**禁止凭记忆直接输出任何控件 HTML。**

### 📦 组件规格 → `references/components.md`

| 需要的内容 | 章节标题 |
|-----------|---------|
| 完整窗口骨架（组装结构） | `## 🏗️ Window Assembly` |
| 按钮（主/次/图标） | `## Buttons` |
| 下拉按钮 | `## Dropdown Button` |
| 详情按钮（? 圆形） | `## Detail Button` |
| 页签（二级/三级/外挂） | `## Tabs` |
| 输入框 | `## Input` |
| 单选框/复选框 | `## Radio & Checkbox` |
| 翻页控件 | `## Pagination` |
| 窗体 Header/Body 规格 | `## Windows / Panels` |
| 区域衬底（浅色/深色） | `## Area Background` |
| 道具图标（标准/小/圆形） | `## Item Icon` |
| 玩家头像 | `## Player Avatar` |
| 召唤兽头像 | `## Pet Avatar` |
| 人物/召唤兽模型 | `## Character & Pet Models` |
| 预设图片资源使用约束 | `## Preset Image Assets` |
| 背包格子 | `## Inventory` |
| 列表项 | `## List Item` |
| 数据表格/排行榜 | `## Data Table` |
| 星级评分 | `## Stars Rating` |
| 滚动区域 | `## Scroll Area` |
| 奖励进度条 | `## Reward Progress Bar` |

### 🎨 颜色/字体 → `references/color-system.md`

| 需要的内容 | 章节标题 |
|-----------|---------|
| 颜色锁定值（Header/Body） | `## Immutable Colors` |
| CSS 变量完整列表 | `## CSS Variables` |
| 主色/渐变色 | `## Primary Colors` |
| 背景色对照表 | `## Backgrounds` |
| 文字颜色（深色/浅色背景） | `## Text Colors` |
| 颜色深浅层级（Level 0–4） | `## Color Depth Levels` |
| 字体字号规则 | `## Typography` |

### 📐 布局间距 → `references/layout-rules.md`

| 需要的内容 | 章节标题 |
|-----------|---------|
| 间距数值系统 | `## Spacing` |
| 水平分层布局规则 | `## Modular Split Layout` |
| 功能分组规则 | `## Section Grouping` |
| 行内/列对齐规则 | `## Alignment Rules` |
| 中文标签等宽对齐 | `## Alignment Rules` |
| 滚动区域规则 | `## Scroll Rules` |
| 对齐检查清单 | `## Alignment Checklist` |

### 🗃️ 资源文件 → `assets/`

| 需要的内容 | 文件 | 说明 |
|-----------|------|------|
| **每个控件的实际 class 名和样式** | `assets/ui_components.css` | ⛔ 使用任何控件前必须对照确认，不可凭印象 |
| 输出 HTML 的起点模板 | `assets/设计模板.html` | Step 5 输出时复制到 `output/` 使用 |
| 查看控件实际外观 | `assets/控件展示.html` | 浏览器打开查阅视觉效果 |
| 路径约定 | SKILL.md `## Assets` 章节 | 所有资源的正确引用路径 |

---

## Instructions

> **任务类型判断（开始前先确认）**：
> - **新界面 / 大改版**：从 Step 0 开始，走完整流程
> - **局部修改 / 加一个组件**：直接跳到 Step 3，无需 Step 0 确认

---

### Step 0: 界面骨架确认（新界面设计时必做）

> 读取 `references/color-system.md`（本步骤一次加载，Step 1 & 2 直接复用，无需重复读取）

1. **分析内容** — 识别界面中的所有功能模块和内容区域
2. **声明本次组件清单** — 列出本次界面将用到的所有组件，标注尺寸锁定控件：

```
本次用到的组件：
☐ ui-window（骨架）
☐ ui-tabs-secondary（二级页签）
☐ ui-list-item（列表项）
☐ ui-avatar（玩家头像）⚠️ 尺寸锁定
☐ ui-btn-primary（主按钮）
```

3. **画骨架结构图** — 以窗口分区为主线，在 ASCII 图上同时标注颜色层级（参考 `color-system.md` Color Depth Levels 章节）：

```
┌─────────────────────────────────────────────────┐
│  标题栏 [Level 1]：[窗口标题]             [?][X] │
├─────────────────────────────────────────────────┤
│  [分组1 Level 3] 内容描述                        │
├───────────────────────────┬─────────────────────┤
│  [分组2 Level 3] 左侧内容  │  [分组3 Level 3] 右 │
└───────────────────────────┴─────────────────────┘
```

**绘制规则**: 使用 `┌ ┐ └ ┘ ├ ┤ ┬ ┴ ┼ │ ─` 字符，标注分组位置、颜色层级和大致占比。

4. ⛔ **硬停止** — 输出骨架图和组件清单后，**必须立即停止**，不得继续任何设计步骤。用以下格式结束本步骤：
   > "以上是界面骨架方案，请确认或告知需要调整的部分。确认后我将继续执行 Step 1。"

   **在收到用户明确的"确认"/"可以"/"继续"/"OK"等回复之前，禁止进入 Step 1 及后续任何步骤。**

---

### Step 1: 验证颜色锁定

> （`color-system.md` 已在 Step 0 加载；若跳过 Step 0，此处补充读取）

两个颜色**绝对不可更改**（无论主题或用户要求，这是品牌一致性的基石）：

| 元素 | 值 |
|------|----|
| Window Body Background | `#11151c` + `repeating-linear-gradient(0deg, #11151c, #11151c 1px, #1a212c 1px, #1a212c 2px)` |
| Window Header | `linear-gradient(to right, #233263, #2E4077 30%, #2E4077 70%, #233263)` |

所有其他颜色查阅 `color-system.md` 的 CSS Variables、Primary Colors、Backgrounds 章节。

---

### Step 2: 验证排版

> （`color-system.md` 已加载，查阅其 **Typography** 章节）

- 允许字号：`16px`（主要）和 `14px`（次要）— **这是唯一允许的两种尺寸**
- 字体通过 `assets/ui_components.css` 的 `@font-face` 自动注入，无需在 HTML 内重写
- 如有文本 `<14px`，必须输出 ⚠️ 警告并征得用户确认后方可使用

---

### Step 3: 应用组件规格

> ⛔ **每个组件必须完成双读，缺一不可**：
> 1. 先查 **Master Index** → 找到 `references/components.md` 对应章节 → 读取规格
> 2. 再对照 **`assets/ui_components.css`** → 确认实际 class 名和样式
>
> 两步都完成后，才能输出该组件的 HTML。局部修改时同样适用，仅读取本次涉及的组件部分。

**关键规则速查（完整规格请读 components.md）**：

| 组件 | 关键约束 |
|------|---------|
| Button | 高度 `26px`（强制），宽度可调（默认 `96px`） |
| Tab（二级） | 高度 `26px`，宽度可调（默认 `88px`） |
| Tab（三级） | 高度 `26px`，宽度可调（默认 `80px`） |
| Input | 高度 `26px`，`border-radius: 6px` |
| Window | Header 24px（颜色锁定）+ Body 扫描线（颜色锁定） |
| Data Table | 行高锁定 `36px` |

---

### Step 4: 遵循布局规则

> 读取 `references/layout-rules.md` 查阅完整布局规则、对齐规则和滚动规则。

**核心原则速查（完整规则请读 layout-rules.md）**：
- 最小间距 `4px`，标准 `6–8px`，窗口内边距 `12px`
- 使用 **Modular Split Layout**（水平分层），避免单列
- 内容面板必须"浮起"：主功能区用 `.ui-area-bg` 包裹
- 设计完成后运行 layout-rules.md 中的**对齐检查清单**和**滚动区域检查清单**

---

### Step 4.5: 输出前 Checkpoint（必须逐项确认）

输出任何 HTML 前，逐项自检：

- [ ] 已通过 Master Index 找到并读取本次**所有组件**的 components.md 章节
- [ ] 已对照 `assets/ui_components.css` 确认**每个控件**的 class 名
- [ ] CSS 路径：`../assets/ui_components.css` ✓
- [ ] 图片路径：`../assets/resource/xxx.png` ✓
- [ ] Window Body 背景色 `#11151c` 未被修改 ✓
- [ ] Window Header 渐变色 `#233263→#2E4077` 未被修改 ✓
- [ ] 所有文字字号在 `14px` / `16px` 范围内 ✓

以上未全部确认，不得输出 HTML。

---

### Step 5: 输出为 HTML

所有设计必须导出为 **完整可运行的 HTML 文件**，最大窗口 **800px × 600px**。

**输出路径**：统一保存到 `output/界面名称.html`（`output/` 与 `assets/` 同级）。

**起点**：从 `assets/` 资源库中复制 `assets/设计模板.html` 到 `output/`，也可调用 `assets/` 里的任意控件片段。或使用以下完整骨架：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=1024, initial-scale=1.0">
    <title>界面名称</title>
    <link rel="stylesheet" href="../assets/ui_components.css">
    <!-- 如需额外样式，在此添加 <style> 块 -->
</head>
<body class="design-bg">
    <!-- 可选遮罩层：需要时同时做两步：
         1. 取消注释此 div
         2. 将 body 的 class 改为 "design-bg design-bg-with-overlay" -->
    <!-- <div class="design-bg-overlay"></div> -->
    <div class="design-container">

        <!-- 在此放置 .ui-window 窗口 -->

    </div>
</body>
</html>
```

**路径说明**（HTML 在 `output/`，`assets/` 在上级目录）：
- CSS：`href="../assets/ui_components.css"` → 自动载入字体 + 设计底图
- 图片资源：`src="../assets/resource/xxx.png"`

---

### Step 6: 新组件发现

完成设计任务后，识别任何新创建的 UI 模式，询问用户：
> "是否将 [组件名称] 添加到设计规范中？"

---

## CSS Class Reference（快速查阅）

| 组件 | Class Name(s) |
|------|---------------|
| 主按钮 | `.ui-btn .ui-btn-primary` |
| 次按钮 | `.ui-btn .ui-btn-secondary` |
| 图标按钮 | `.ui-btn-icon` / `.ui-btn-icon-primary` |
| 详情按钮 | `.ui-btn-detail` |
| 下拉按钮 | `.ui-btn .ui-btn-secondary .ui-btn-dropdown` |
| 二级页签 | `.ui-tab-secondary` / Container: `.ui-tabs-secondary` |
| 三级页签 | `.ui-tab-tertiary` / Container: `.ui-tabs-tertiary` |
| 外挂页签 | `.ui-window-with-side-tabs`, `.ui-side-tabs`, `.ui-side-tab` |
| 窗口 | `.ui-window`, `.ui-window-header`, `.ui-window-title`, `.ui-window-controls`, `.ui-window-body` |
| 输入框 | `.ui-input` |
| 单选/复选 | `.ui-radio` / `.ui-checkbox` |
| 区域衬底 | `.ui-area-bg`（浅色）/ `.ui-area-bg-dark`（深色） |
| 设计背景 | `.design-bg`（`<body>`）/ `.design-container`（包裹 UI） |
| 道具图标 | `.ui-item-icon`, `.ui-item-level`, `.ui-item-icon-small`, `.ui-item-icon-round` |
| 背包 | `.ui-inventory`, `.ui-inv-cell`（格内图标加 `.ui-item-icon--cell`） |
| 列表项 | `.ui-list-item` |
| 数据表格 | `.ui-table`, `.ui-table-header`, `.ui-table-body`, `.ui-table-row` |
| 星级 | `.ui-stars`, `.ui-stars--1/2/3` |
| 玩家头像 | `.ui-avatar`（空状态: `.ui-avatar.empty`，带加号: `.ui-avatar.empty.show-plus`） |
| 召唤兽头像 | `.ui-pet-avatar`（标准52px）/ `.ui-pet-avatar-small`（小30px） |
| 人物/召唤兽模型 | `.ui-model-character` / `.ui-model-pet`（空div，图片由CSS背景自动加载） |
| 滚动区域 | `.ui-scroll-area`（自带overflow+滚动条）/ `.ui-scrollbar`（仅滚动条样式） |
| 奖励进度条 | `.ui-reward-progress`（详见 components.md Reward Progress Bar 章节） |

---

## Best Practices

1. **颜色锁定优先** — 开始任何任务前先验证 Header 和 Body 颜色未被改动
2. **排版门控** — `<14px` 文本必须可见警告；只允许 `14px` 和 `16px`
3. **组件约束** — 遵守每个组件的"禁止修改"规则（详见 `references/components.md`）
4. **尺寸锁定控件** — 人物头像、召唤兽头像、道具图标框**禁止直接调整尺寸**；如用户要求修改，须先询问确认，说明原因后方可操作
5. **分层布局默认** — 始终优先使用水平分层布局，而非单列
6. **仅 HTML 输出** — 所有交付物为完整 HTML 文件（含 `<style>` 块或内联样式）
7. **使用 CSS 类** — 优先使用 `ui_components.css` 中的 `ui-*` 类名
8. **尺寸上限** — 单个窗口不超过 800×600px

## Assets（资源库）与 Output（输出目录）

```
xyq-ui-design-system/
├── assets/               ← 资源库：可读取、调用、复制，但不在此直接创建成品
│   ├── ui_components.css      ← 基础控件样式库
│   ├── 控件展示.html          ← 所有控件可视化预览（浏览器打开查阅）
│   ├── 设计模板.html          ← 新界面起始模板（复制到 output/ 后使用）
│   └── resource/
│       ├── 设计底图.jpg       ← 游戏背景底图（1024×768px）⚠️ 禁止替换
│       ├── 华康圆体W7.TTC     ← 主字体文件 ⚠️ 禁止替换
│       ├── 人物头像.png        ⚠️ 禁止替换
│       ├── 人物模型.png        ⚠️ 禁止替换
│       ├── 召唤兽头像.png      ⚠️ 禁止替换
│       ├── 召唤兽模型.png      ⚠️ 禁止替换
│       └── 道具图标.png        ⚠️ 禁止替换
└── output/               ← 成品输出目录：所有生成的 HTML 保存在此
    └── 界面名称.html
```

**路径约定**（HTML 在 `output/`）：
- CSS：`../assets/ui_components.css`
- 图片资源：`../assets/resource/xxx.png`

## References

- `references/color-system.md` — 颜色系统与排版规范（Step 1 & 2）
- `references/components.md` — 全部组件规格（Step 3）
- `references/layout-rules.md` — 布局间距对齐滚动规则（Step 4）
- `assets/ui_components.css` — 基础控件样式库
- `assets/控件展示.html` — 所有控件可视化预览（浏览器打开）
- `assets/设计模板.html` — 新界面起始模板
