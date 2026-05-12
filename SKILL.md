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
| 属性展示（标签+只读框） | `## Stat Display` |
| 单选框/复选框 | `## Radio & Checkbox` |
| 翻页控件 | `## Pagination` |
| 数量选择器（加减按钮） | `## Quantity Selector` |
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
| 通用设计起点模板 | `assets/设计模板.html` | 无匹配模板时使用的通用骨架 |
| 查看控件实际外观 | `assets/控件展示.html` | 浏览器打开查阅视觉效果 |
| 路径约定 | **见下方 `## Assets` 章节** | ⛔ 输出任何 HTML 前必读，路径写错会导致资源全部失效 |

### 📋 模板注册表（任务类型判断时必查）

> AI 接到新任务后，**必须先扫描此表**，将用户需求的关键词和界面描述与下表匹配。命中后加载对应的规范文件和参考 HTML。

| 模板 ID | 名称 | 命中关键词 | 结构特征 | 规范文件 | 参考 HTML |
|---------|------|-----------|---------|---------|----------|
| `act-bp` | 活动BP界面 | 活动BP、BP奖励、赛季通行证、战令、庆典、等级奖励、赛季奖励 | Banner + 进度条 + 横向卡片列表 + 领取按钮 | `assets/templates/活动BP/活动BP界面规范.md` | `assets/templates/活动BP/新春庆典2.html`（新春红主题）<br>`assets/templates/活动BP/暑假活动BP.html`（海洋蓝主题） |
| `fest-main` | 节日活动主界面 | 节日活动、活动主界面、玩法入口、活动大厅、活动总览 | 全屏背景图 + 绝对定位按钮组 + 多玩法入口 | `assets/templates/节日活动主界面/节日活动主界面规范.md` | `assets/templates/节日活动主界面/index.html` |
| `shop-buy` | 购买界面 | 购买、购买界面、商店购买、商城购买、买道具 | 5×4背包格子 + 外挂页签切换货币 + 单价/数量/总额输入框 | `assets/templates/购买界面/购买界面规范.md` | `assets/templates/购买界面/购买界面.html` |
| `reward-popup` | 奖励弹窗 | 奖励说明、规则说明、活动规则、玩法说明、说明弹窗 | 小弹窗 + 纯文字说明 + 四角装饰 + 多段落列表 | `assets/templates/奖励弹窗/奖励弹窗规范.md` | `assets/templates/奖励弹窗/奖励弹窗.html` |
| `reward-tier-claim` | 礼包领取 | 礼包领取、档位奖励、分段奖励、阶段奖励、任务礼包、勤劳致富 | 档位列表 + 条件胶囊标签 + 按钮三态（领取/已领取/暂未解锁） | `assets/templates/礼包领取/礼包领取规范.md` | `assets/templates/礼包领取/勤劳致富礼包领取界面.html` |
| *(通用)* | 通用弹窗/面板 | *(以上均未命中时)* | 标准 ui-window 窗口 | — | `assets/设计模板.html` |

---

## Instructions

> **任务类型判断（开始前必做）**：
>
> **① 模板命中**：扫描用户需求，对照上方「📋 模板注册表」匹配关键词和结构特征：
> - **唯一命中** → 读取该模板的 `规范.md`（规则手册）+ `参考 HTML`（结构基础）
> - **多个模板均可能命中**（关键词交叉或描述模糊）→ 必须向用户确认："检测到需求可能匹配以下模板：[列出候选]，请确认使用哪个？"
> - **未命中** → 使用通用模板 `assets/设计模板.html`
>
> **② 任务规模**：
> - **新界面 / 大改版**：从 Step 0 开始，走完整流程
> - **局部修改 / 加一个组件**：直接跳到 Step 3，无需 Step 0 确认
>
> **③ 源文件处理**（⛔ 核心原则：**output/ 是唯一工作区，所有修改只发生在 output/ 里，原始文件不动**）：
> - **无源文件**：
>   - 命中模板 → 复制模板参考 HTML 到 `output/`，基于副本新建
>   - 未命中 → 复制通用模板 `assets/设计模板.html` 到 `output/`
> - **有源文件**：
>   - 已在 `output/` → 直接在原文件上修改
>   - 不在 `output/`（模板文件 / 外部文件）→ 先复制到 `output/`，再修改副本
>   - 命中模板时，同时加载规范.md 作为规则约束
>
> 以上三步在每次任务开始前**静默完成**（无需向用户输出判断过程），直接进入对应 Step。

---

## Assets

> ⛔ **输出任何 HTML 前必读此章节。路径写错 = 资源全部 404，页面空白。**

### 目录结构

```
xyq-ui-design-system/
├── assets/
│   ├── ui_components.css          ← 全局样式
│   ├── resource/                  ← 通用共享资源（道具图标、字体、人物等）
│   └── templates/
│       ├── 活动BP/
│       │   ├── 新春庆典2.html     ← 参考模板（只读，不修改）
│       │   └── resource/          ← 活动BP专属资源
│       └── 节日活动主界面/
│           ├── index.html         ← 参考模板（只读，不修改）
│           └── images/            ← 节日活动专属图片
└── output/                        ← ⛔ 唯一工作区，所有输出文件放这里
    └── 界面名称.html
```

### 路径对照表（从 `output/*.html` 出发）

| 引用目标 | 正确路径 | ❌ 常见错误 |
|---------|---------|-----------|
| 全局样式表 | `../assets/ui_components.css` | `../../ui_components.css` |
| 通用道具图标 | `../assets/resource/道具图标.png` | `../../resource/道具图标.png` |
| 通用字体 | `../assets/resource/华康圆体W7.TTC` | `../../resource/华康圆体W7.TTC` |
| 活动BP专属资源 | `../assets/templates/活动BP/resource/xxx.png` | `./resource/xxx.png` |
| 节日活动图片 | `../assets/templates/节日活动主界面/images/xxx.png` | `../../节日活动主界面/images/xxx.png` |

> **规律**：`output/` 与 `assets/` 同级，一律用 `../assets/` 前缀访问所有资源。
> 模板文件内的 `../../` 是相对于 `templates/活动BP/` 的路径，**复制到 `output/` 后必须替换**。

### 复制模板到 output/ 时的路径替换规则

| 模板内原路径 | 替换为 |
|------------|--------|
| `../../ui_components.css` | `../assets/ui_components.css` |
| `../../resource/` | `../assets/resource/` |
| `./resource/` | `../assets/templates/活动BP/resource/` |
| `../../节日活动主界面/images/` | `../assets/templates/节日活动主界面/images/` |

---

### Step 0: 界面骨架确认（新界面设计时必做）

> 读取 `references/color-system.md`（本步骤一次加载，Step 1 & 2 直接复用，无需重复读取）
>
> ⚡ **模板跳过规则**：若已命中模板，且用户需求的布局与模板规范.md 中定义的结构层级**100% 一致**（无新增/删除/移动区块），则跳过本步骤，直接进入 Step 1。若有任何结构差异（如新增页签、调整分区），仍需画骨架确认。

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

> ⛔ **组件规格确认规则**：
> - **已在命中模板的规范.md 中列出的组件**（含 HTML 结构和 class 名）→ 直接按规范使用，无需双读
> - **规范.md 未列出的组件**（本次新增或通用组件）→ 必须完成双读：
>   1. 先查 **Master Index** → 找到 `references/components.md` 对应章节 → 读取规格
>   2. 再对照 **`assets/ui_components.css`** → 确认实际 class 名和样式
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

**起点**：由「任务类型判断 ③」决定 — 模板参考 HTML 或通用模板已被复制到 `output/`，在此基础上修改即可。

若未命中任何模板且无源文件，也可使用以下最简骨架：

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
| 属性展示 | `.ui-stat-display`, `.ui-stat-label`, `.ui-stat-label-2/3/4`, `.ui-stat-label-tight/normal`, `.ui-stat-input`, `.ui-stat-btns`, `.ui-stat-tag-red/blue`, `.ui-stat-tag-2/3`, `.ui-stat-label-highlight` |
| 单选/复选 | `.ui-radio` / `.ui-checkbox` |
| 翻页控件 | `.ui-pagination`, `.ui-pagination-info` |
| 数量选择器 | `.ui-quantity-selector`, `.ui-quantity-value` |
| 区域衬底 | `.ui-area-bg`（浅色）/ `.ui-area-bg-dark`（深色） |
| 设计背景 | `.design-bg`（`<body>`）/ `.design-container`（包裹 UI） |
| 道具图标 | `.ui-item-icon`（52×52）, `.ui-item-level`（14px，圆形图标内为12px）, `.ui-item-icon-small`（30×30）, `.ui-item-icon-round`（30×30圆形） |
| 背包 | `.ui-inventory`, `.ui-inv-cell`（格内 `.ui-item-icon` 无需额外修饰类） |
| 列表项 | `.ui-list-item` |
| 数据表格 | `.ui-table`, `.ui-table-header`, `.ui-table-body`, `.ui-table-row`, `.ui-table-sort-icon` |
| 星级 | `.ui-stars`, `.ui-stars--1/2/3` |
| 玩家头像 | `.ui-avatar`（空状态: `.ui-avatar.empty`，带加号: `.ui-avatar.empty.show-plus`） |
| 召唤兽头像 | `.ui-pet-avatar`（标准52px）/ `.ui-pet-avatar-small`（小30px） |
| 人物/召唤兽模型 | `.ui-model-character` / `.ui-model-pet`（空div，图片由CSS背景自动加载） |
| 滚动区域 | `.ui-scroll-area`（自带overflow+滚动条）/ `.ui-scrollbar`（仅滚动条样式） |
| 奖励进度条 | `.ui-reward-progress`（详见 components.md Reward Progress Bar 章节） |
| 文字颜色工具类 | `.ui-text-light-bg-primary/secondary/success/link/error`（浅色背景）<br>`.ui-text-dark-bg-primary/secondary/success/warning/error`（深色背景） |

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
│   ├── 设计模板.html          ← 通用兜底模板（复制到 output/ 后使用）
│   ├── 控件展示.html          ← 所有控件可视化预览（浏览器打开查阅）
│   ├── 编辑器.html            ← 开发工具
│   ├── resource/              ← 全局共享资源
│   │   ├── 设计底图.jpg       ← 游戏背景底图（1024×768px）⚠️ 禁止替换
│   │   ├── 华康圆体W7.TTC     ← 主字体文件 ⚠️ 禁止替换
│   │   ├── 人物头像.png        ⚠️ 禁止替换
│   │   ├── 人物模型.png        ⚠️ 禁止替换
│   │   ├── 召唤兽头像.png      ⚠️ 禁止替换
│   │   ├── 召唤兽模型.png      ⚠️ 禁止替换
│   │   └── 道具图标.png        ⚠️ 禁止替换
│   └── templates/             ← 可命中模板集（新增模板在此添加子目录）
│       ├── 活动BP/            ← 模板：活动BP界面（多主题）
│       │   ├── 活动BP界面规范.md
│       │   ├── 新春庆典2.html  ← 新春红主题
│       │   └── 暑假活动BP.html ← 海洋蓝主题
│       ├── 节日活动主界面/     ← 模板：节日活动主界面
│       │   ├── 节日活动主界面规范.md
│       │   ├── index.html
│       │   └── images/
│       ├── 购买界面/           ← 模板：购买界面
│       │   ├── 购买界面规范.md
│       │   └── 购买界面.html
│       ├── 奖励弹窗/           ← 模板：奖励说明弹窗
│       │   ├── 奖励弹窗规范.md
│       │   └── 奖励弹窗.html
│       └── 礼包领取/           ← 模板：档位制礼包
│           ├── 礼包领取规范.md
│           └── 勤劳致富礼包领取界面.html
└── output/               ← 成品输出目录：所有修改只发生在此
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
