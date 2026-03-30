---
name: xyq-ui-design-system
description: >
  Apply the XYQ畅玩服 Fantasy/RPG retro-modern UI design system when creating,
  reviewing, or refactoring game interface HTML components. Enforces strict
  color locks, typography scale, component dimensions, and layout rules.
  Use when designing any XYQ畅玩服 UI element: windows, buttons, tabs, input
  fields, lists, panels, grid selectors, or full screen layouts.
  Triggers on: 西游Q传界面, 畅玩服UI设计, 游戏界面设计, 按钮组件, 窗口面板, 颜色规范,
  排版规范, 组件规范, RPG game UI, retro game interface, fantasy UI,
  HTML game design, color spec, design system compliance.
allowed-tools: Bash Read Write Edit Glob Grep
metadata:
  tags: ui-design, game-ui, xyq, fantasy-rpg, design-system, html, retro-modern
  version: "3.0"
  synced-with: ui_components.css
  skeleton-templates: skeleton-templates.md
---

# XYQ畅玩服 UI Design System

> Style: Fantasy/RPG Retro-Modern | Output: HTML only | Max window: 800×600px | v3.0

## When to use this skill

- Creating new game UI components: windows, buttons, tabs, input fields, lists, avatars
- Reviewing or auditing existing HTML/CSS for design system compliance
- Resolving design decisions on colors, typography, spacing, or layout constraints
- Generating complete HTML interface exports for XYQ畅玩服
- Ensuring visual consistency when extending or combining existing panels
- Converting or refactoring non-compliant components to match the design spec

## 工作流概览 (v3.0)

```
Step 0  需求分析 + 功能分组识别
   ↓
Step 1  Phase 1：大骨架预览（视觉化确认）
   ↓
Step 2  Phase 2：分区细节预览（完整组件级确认）
   ↓
Step 3  颜色锁 + 字号校验
   ↓
Step 4  组件规范应用
   ↓
Step 5  生成最终 HTML
   ↓
Step 6  新组件发现（可选）
   ↓
Step 7  骨架模板自动发现（AI推导方案时触发）
```

## Instructions

### Step 0: 需求分析与功能分组识别

在开始任何设计工作前，**必须**完成以下分析：

1. **分析界面内容** — 根据用户需求，识别界面中的所有功能模块和内容区域

2. **总结功能分组** — 将内容整理为清晰的分组列表：
   - 分组1：[名称]（包含：A、B、C）
   - 分组2：[名称]（包含：D、E）
   - 底部操作栏（包含：确认、取消）

3. **记录用户草图** — 若用户在需求中提供了布局参考或草图描述，标记为「用户草图」，将在 Step 1 中渲染为「方案0」

4. **直接进入 Step 1** — **不再输出文字分组表格和 ASCII 草图**，直接用 HTML 骨架预览代替

---

### Step 1: Phase 1 — 大骨架预览 (视觉化布局确认)

> 目标：让用户在「真实渲染」的骨架上确认布局结构，而非文字符号。
> 骨架颗粒度：真实 XYQ 颜色 + 功能区色块 + 区域标签，**内容用占位块代替**。

#### 1.1 匹配骨架模板库

读取 `skeleton-templates.md`，按以下优先级决定输出方案：

| 匹配程度 | 操作 |
|---------|------|
| **完全匹配** — 分组类型和数量吻合 | 渲染该模板，填入用户分组名 |
| **部分匹配** — 有相近模板 | 渲染最近模板 + 可选输出 1 个 AI 变体（不强求） |
| **无匹配** — 无相近模板 | 只输出 AI 推导方案（1-2 个，不强求多个） |

**模板匹配信号速查**：

| 用户界面特征 | 优先匹配 |
|------------|---------|
| 有列表 + 有详情，无多分类 | SKL-001 左右分栏 |
| 有列表 + 有 ≥3 个子分类 | SKL-002 带页签左右分栏 |
| 内容单一 / 确认弹窗 / 奖励领取 | SKL-003 上下分区 |
| 三个并列功能区 | SKL-004 三栏布局 |

#### 1.2 方案编号规则

- 若用户有提供草图/参考描述 → **方案0（您的草图）** 排第一，用草图结构渲染骨架
- 模板方案 → **方案A、方案B**（按匹配度排序）
- AI 推导方案 → 排在最后，标注「AI 推导」

#### 1.3 生成骨架预览 HTML（含 Bridge 单选交互）

将所有方案输出为**一个单独的 HTML 文件**，方案横向排列便于对比。

骨架 HTML 生成规则：
- ✅ 使用真实 XYQ 颜色（`#11151c` 背景 + 扫描线、蓝色标题栏渐变、`#B1B9E0` 功能区）
- ✅ 功能区用带标签的色块表示（如「任务列表区」文字 + `#B1B9E0` 背景块）
- ✅ 内容用占位色块（`#CAD4F7` 色块，不放真实控件）
- ✅ 比例和尺寸与最终 HTML 一致（窗口宽高、标题栏高度 24px 等）
- ✅ **每个方案 wrapper 加 Bridge 选择属性**（见下方结构）
- ❌ 不放真实文案、图标、按钮内容

> ⚠️ **严禁自定义选择逻辑** — 禁止自行实现任何替代函数（`selectScheme`、`copyResult`、`submitChoice` 等）。
> ⚠️ **严禁自定义底部栏** — 禁止写 `.confirm-bar`、`.btn-confirm` 等 CSS 及对应 HTML，bridge-client.js 已内置。
> ⚠️ **严禁修改类名** — 高亮类名必须是 `vsb-selected`（bridge-client.js 添加），不要另加 `.selected`、`.active` 等。

**Phase 1 骨架 HTML 生成方式（必须严格按此操作，不得从零生成）：**

**第一步：读取基础模板**
```
读取文件：skill研发/skills/xyq-ui-design-system/templates/phase1_base.html
```
> 此文件已包含全部 CSS、bridge 接入脚本、vsb-selected 类名。禁止修改文件中除 `[PAGE_TITLE]` 和 `<!-- INSERT SCHEMES START/END -->` 之间的任何内容。

**第二步：只填两处内容，其余照搬**

填写位置 ①：把 `[PAGE_TITLE]` 替换为界面名称（如"梦境成果界面"）

填写位置 ②：在 `<!-- INSERT SCHEMES START -->` 和 `<!-- INSERT SCHEMES END -->` 之间插入方案，格式：
```html
  <div class="scheme-wrapper"
       data-group="骨架方案"
       data-value="方案0·用户草图"
       onclick="vsBridge.select(this)">
    <div class="scheme-label">方案0 · 您的草图</div>
    <div class="scheme-tag">忠实还原草图布局</div>
    [此处填骨架HTML内容]
  </div>

  <div class="scheme-wrapper"
       data-group="骨架方案"
       data-value="方案A·[模板名称]"
       onclick="vsBridge.select(this)">
    <div class="scheme-label">方案A · [模板名称]</div>
    <div class="scheme-tag">来源：[SKL-xxx]</div>
    [此处填骨架HTML内容]
  </div>
```

> ⚠️ `onclick` 只能是 `vsBridge.select(this)`，`data-group` 只能是 `"骨架方案"`，这两个值与基础模板底部的 `vsBridge.init` 参数对应，改了会导致选择失效。

**输出前自查（2项即可，其余已由基础模板保证）：**
- [ ] `<!-- INSERT SCHEMES START/END -->` 之间每个 wrapper 的 `onclick` 都是 `vsBridge.select(this)`
- [ ] 没有在 `</body>` 之前插入任何额外 `<script>` 或 `<style>`

#### 1.4 自动打开预览 + 等待选择结果

HTML 文件生成后，**AI 必须立即用终端命令自动启动 Bridge 服务器并打开预览**，无需用户手动操作：

```
start "XYQ Bridge" cmd /c ""E:\AI开发\skill研发\skills\xyq-ui-design-system\open_preview.bat" "[生成的HTML文件绝对路径]""
```

> 例：`start "XYQ Bridge" cmd /c ""E:\AI开发\skill研发\skills\xyq-ui-design-system\open_preview.bat" "e:\AI开发\skill研发\skills\xyq-ui-design-system\test\trade_skeleton_phase1.html""`

**注意**：`start` + `cmd /c` 会在独立窗口中运行 bat，不阻塞 AI 对话，服务器在用户完成选择后自动退出。

然后提示：
> 📐 **布局骨架预览已自动打开，Bridge 服务已启动** → 点击选择方案 → 右下角「确认选择」亮起后点击 → Ctrl+V 粘贴结果到对话继续

**当 AI 在对话中收到 `[VBridge]` 开头的消息时，自动解析并进入 Step 2，无需用户额外说明。**

`[VBridge]` 解析规则：
```
[VBridge] page=布局骨架方案选择 | 骨架方案→方案0·用户草图
                                   ↑ 提取此值作为选定骨架
```

---

### Step 2: Phase 2 — 分区细节预览 (完整组件级确认)

> 目标：对选定骨架的每个主要分区，展示 2 个完整渲染的变体，让用户确认控件摆法。
> 细节颗粒度：**完整真实组件**（真实按钮尺寸、真实列表项、真实图标格子），使用示例假数据。

#### 2.1 确定需要细节确认的分区

- 只对**主要功能区**进行细节确认（通常 2-3 个区）
- 底部操作栏（纯按钮行）通常不需要细节确认，可直接沿用规范样式

#### 2.2 为每个分区生成 2 个变体

每个变体应体现**不同的控件排布方式**，例如：

| 分区类型 | 变体1 | 变体2 |
|---------|-------|-------|
| 列表区 | 纯文本列表行（无图标） | 带小图标的列表行 |
| 列表区 | 带页签切换的列表 | 无页签的单一列表 |
| 详情区 | 头像+属性纵向排列 | 头像居左+属性网格 |
| 详情区 | 大图标展示+描述文字 | 多属性统计行 |
| 进度区 | 进度条样式 | 数值比例样式 |
| 图标区 | 大图标格（52px，3列） | 小图标格（30px，5列） |

#### 2.3 生成分区细节预览 HTML（含 Bridge 多选逐项确认）

将所有分区的变体输出为**一个 HTML 文件**，分区分组排列，**必须集成 Bridge 多选模式**：

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>分区细节方案选择</title>
<style>
  body { background: #0f111a; padding: 32px 32px 120px;
         font-family: 'Microsoft YaHei', sans-serif; color: #e6e6e6; }
  .section-group  { margin-bottom: 48px; }
  .section-title  { color: #e8c361; font-size: 16px; font-weight: bold;
                    padding-bottom: 10px; border-bottom: 1px solid #333C6B;
                    margin-bottom: 16px; }
  .variants-row   { display: flex; gap: 32px; flex-wrap: wrap; align-items: flex-start; }
  .variant-wrapper{ display: flex; flex-direction: column; gap: 10px; }
  .variant-label  { color: #c0c8e0; font-size: 14px; font-weight: bold; }

  /* 逐项确认面板（由 bridge-client.js 的 vsb- 类控制显隐） */
  .confirm-area {
    display: none; margin-top: 16px;
    background: rgba(15,17,26,.85); border: 1px solid #333C6B;
    border-left: 3px solid #e8c361; border-radius: 6px;
    padding: 14px 16px; flex-direction: column; gap: 10px;
  }
  .confirm-area.vsb-active { display: flex; }
  .confirm-area textarea {
    width: 100%; min-height: 60px; background: #0f111a;
    border: 1px solid #333C6B; border-radius: 4px;
    color: #9aa8cc; font-size: 12px; padding: 8px 10px;
    font-family: inherit; resize: vertical; outline: none;
  }
  .confirm-area textarea:focus { border-color: #e8c361; }
  .confirm-area textarea::placeholder { color: #333C6B; }
  .confirm-area-btns { display: flex; gap: 8px; justify-content: flex-end; }
  .btn-section-confirm {
    padding: 7px 22px; background: linear-gradient(135deg,#e8c361,#c8941a);
    color: #1a1c30; border: none; border-radius: 4px;
    font-size: 13px; font-weight: bold; cursor: pointer;
  }
  .btn-section-reselect {
    padding: 7px 16px; background: transparent; color: #4e5a7a;
    border: 1px solid #333C6B; border-radius: 4px; font-size: 12px; cursor: pointer;
  }
  .locked-note {
    display: none; margin-top: 8px; font-size: 12px; color: #5a8a50;
    padding: 6px 10px; background: rgba(58,106,40,.15);
    border-radius: 4px; border: 1px solid rgba(58,106,40,.3);
  }
  [data-section-id].vsb-locked .locked-note { display: block; }
</style>
</head>
<body>

  <!-- ① [分区名称] 变体选择 -->
  <div class="section-group" data-section-id="[分区名A]">
    <div class="section-title">① [分区名A] — 选择控件排布方式</div>
    <div class="variants-row">

      <div class="variant-wrapper"
           data-group="[分区名A]"
           data-value="变体1-[描述]"
           onclick="vsBridge.select(this)">
        <div class="variant-label">变体1：[描述]</div>
        [分区HTML，包含真实按钮/列表项/图标等]
      </div>

      <div class="variant-wrapper"
           data-group="[分区名A]"
           data-value="变体2-[描述]"
           onclick="vsBridge.select(this)">
        <div class="variant-label">变体2：[描述]</div>
        [分区HTML]
      </div>

    </div>

    <!-- 逐项确认面板 -->
    <div class="confirm-area" id="vsb-confirm-[分区名A]">
      <div style="color:#e8c361;font-size:12px;font-weight:bold;">① [分区名A] — 确认选择</div>
      <div id="vsb-label-[分区名A]" style="color:#c0c8e0;font-size:12px;padding:4px 10px;background:rgba(46,60,107,.5);border-radius:4px;">已选：—</div>
      <textarea id="vsb-note-[分区名A]" placeholder="修改意见（可选）…"></textarea>
      <div class="confirm-area-btns">
        <button class="btn-section-reselect" onclick="vsBridge.reselect('[分区名A]')">← 重新选择</button>
        <button class="btn-section-confirm"  onclick="vsBridge.confirmSection('[分区名A]')">确认此项 →</button>
      </div>
    </div>
    <div class="locked-note" id="vsb-locked-note-[分区名A]"></div>
  </div>

  <!-- ② [分区名B] 变体选择（同结构，替换分区名） -->
  <!-- ... -->

  <!-- Bridge 多选模式，声明所有必选分组 -->
  <!-- ⚠️ 必须用相对路径，确保 file:// 直接打开也能加载 -->
  <script src="../bridge-client.js"></script>
  <script>
    document.addEventListener('DOMContentLoaded', () =>
      vsBridge.init({ mode: 'multi-step', required: ['[分区名A]', '[分区名B]'] })
    );
  </script>
</body>
</html>
```

#### 2.4 自动打开预览 + 等待选择结果

HTML 文件生成后，**AI 必须立即用终端命令自动打开预览**：

```
start "" "[生成的HTML文件路径]"
```

然后提示：
> 🎨 **分区细节预览已自动打开** → 逐项点击变体 → 填写意见（可选）→「确认此项」→ 全部完成后「提交全部」→ Ctrl+V 粘贴结果到对话继续

**当 AI 在对话中收到 `[VBridge]` 开头的消息时，自动解析所有分区选择并进入 Step 3，无需用户额外说明。**

`[VBridge]` 解析规则：
```
[VBridge] page=分区细节方案选择 | 分区A→变体1-xxx | 分区B→变体2-yyy | 备注·分区A→按钮加大
                                   ↑ 每个 key→value 对应一个分区的选择
                                   备注·xxx→yyy 对应该分区的修改意见
```

---

### Step 3: Enforce color locks (原 Step 1)

Two colors are **IMMUTABLE** — must never change regardless of theme or user request:

| Element | Value |
|---------|-------|
| Window Body Background | `#11151c` + scanline pattern |
| Window Header | `linear-gradient(to right, #233263, #2E4077 30%, #2E4077 70%, #233263)` |

Apply primary/secondary palette for all other interactive elements (see Color System).

### Step 4: Validate typography (原 Step 2)

- Allowed sizes: `14px` (body, buttons) and `16px` (window titles, inputs) — **no other sizes**
- **Mandatory warning**: if any text would be `<14px`, output:
  > ⚠️ Warning: Text size [X]px is below the minimum standard (14px). Please confirm.
- Font stack: `"DFYuanW7-B5", "华康圆体", "Yuane", "Microsoft YaHei", sans-serif`

### ⛔ 强制前置规则：所有受约束控件必须查 CSS 源文件后再写代码

> **此规则适用于 Step 5 及所有生成 HTML 的环节，无一例外。**

在写任何受设计系统约束的控件 HTML 之前，**必须先执行以下核查步骤**，不允许依赖记忆或名称推断：

#### 核查流程

```
需要使用某个受约束控件（按钮、图标、头像、列表项、页签、输入框等）
    ↓
① 在 ui_components.css 中搜索对应 class 名
    ↓
② 确认：class 名称、尺寸、颜色、HTML 结构（是 div 还是 img？有哪些子类？）
    ↓
③ 直接使用 CSS 中定义的 class，禁止在 HTML 内联或 <style> 中覆盖其尺寸/颜色
    ↓
④ 写代码
```

#### 受约束控件速查表（必须查源文件，不得凭记忆）

| 控件类型 | 对应 CSS class | 关键约束 |
|---------|---------------|---------|
| 标准道具图标 | `.ui-item-icon` + `.ui-item-icon--preset` | 52×52px，禁止修改尺寸 |
| 小型道具图标 | `.ui-item-icon-small` | 30×30px，禁止修改尺寸 |
| 玩家头像 | `.ui-avatar` + `.ui-avatar--character` | 52×52px，禁止修改尺寸 |
| 召唤兽头像 | `.ui-pet-avatar` + `.ui-pet-avatar--preset` | 52×52px，禁止修改尺寸 |
| 主按钮 | `.ui-btn` | height 24px，禁止修改高度/颜色 |
| 图标按钮 | `.ui-btn-icon` | 24×24px，禁止修改尺寸/颜色 |
| 二级页签 | `.ui-tab` | height 24px，禁止修改高度 |
| 输入框 | `.ui-input` | height 28px，禁止修改高度/颜色 |
| 区域衬底 | `.ui-area-bg` | 背景 #B1B9E0，禁止修改颜色 |
| 列表项 | `.ui-list-item` | 背景 #CAD4F7，禁止修改颜色 |

#### 违规示例（禁止）

```html
<!-- ❌ 错误：用名称推断选了 small 类，又自己覆盖尺寸 -->
<div class="ui-item-icon-small">
  <img src="道具图标.png" style="width:22px;height:22px;">
</div>

<!-- ❌ 错误：直接写 img 标签而非使用 --preset 背景类 -->
<div class="ui-item-icon">
  <img src="../resource/道具图标.png">
</div>
```

#### 正确示例（必须）

```html
<!-- ✅ 正确：查 CSS 确认是 52px，使用 --preset 背景类，不写任何尺寸覆盖 -->
<div class="ui-item-icon ui-item-icon--preset"></div>

<!-- ✅ 正确：需要小图标时查 CSS 确认是 30px，再决定用 small -->
<div class="ui-item-icon-small ui-item-icon--preset"></div>
```

> **根因记录（来自实际踩坑）**：  
> 曾因未查 CSS 直接凭名称推断使用 `ui-item-icon-small`（30px），并手写 `img { width: 22px }` 覆盖尺寸，
> 导致道具图标显示错误。整个迭代周期内因从未回头校验而持续沿用错误。  
> **教训：受约束控件必须查源文件，不允许例外。**

---

### Step 5: Apply component specs & layout rules (原 Step 3-4)

Reference the Component Specifications section for exact dimensions. Key rules:

- **Buttons**: height `24px`, `border-radius: 4px`, metal/glassy gradient, no fixed width (default `96px`), `padding: 0`, `white-space: nowrap`
- **Tabs (Secondary)**: height `24px`, width ~`88px`; active = `#CAD4F7` bg + dark text
- **Tabs (Tertiary)**: height `24px`, width `80px`, vertical layout
- **Inputs**: rounded rect `border-radius: 6px`, height `28px`, metallic gradient
- **Window structure**: header `24px` (color LOCKED) + body with scanline pattern

### Step 5-continued: Follow layout rules

- Minimum gap: `4px`; standard gap: `6px` or `8px`; section spacing: `8–12px`
- Use **Modular Split Layout** (horizontal layers) — avoid single tall columns
- Content panels must "float": padding from window body edges enforced
- Text labels with different character counts must use `justify-content: space-between`
  or full-width spaces (e.g., `"气　　血"` aligns with `"攻击资质"`)

### Step 6: Deliver as HTML (原 Step 5)

所有设计必须输出为 **HTML 文件**（含 `<style>` 块或 inline styles）。
最大窗口尺寸：**800px × 600px**。

> 此步骤在 Step 2 分区变体已确认后执行。直接将用户选定的骨架 + 各区变体组合，
> 套用 Step 3-5 的规范约束，生成最终完整 HTML 文件。

### Step 6-continued: New component discovery (原 Step 6)

完成设计任务后，识别本次创建的新 UI 模式。  
询问用户：*"是否要将「[组件名称]」添加到设计规范中？"*

---

### Step 7: 骨架模板自动发现 (新增)

> 仅在本次使用了 **AI 推导方案**（非直接来自模板库）时触发此步骤。

#### 7.1 触发前置条件（ALL 满足）

- [ ] 本次布局来自 AI 推导（不是直接渲染 skeleton-templates.md 中的模板）
- [ ] 用户明确选择了此方案（Step 1 选择了该 AI 方案）
- [ ] 最终 HTML 已成功生成（走完了完整流程）

#### 7.2 复用价值判断规则（满足 ≥ 2 条则建议加入）

| 规则 | 判断标准 |
|------|---------|
| **R1 结构差异度** | 与模板库中最相近模板相比，分区数量或排列方式有 ≥ 2 处不同 |
| **R2 场景通用性** | 该布局能对应一类常见界面类型（如「列表+进度」「角色展示+属性」） |
| **R3 组件多样性** | 布局内涉及 ≥ 3 种不同类型的功能区（列表区/详情区/进度区/图标区…） |
| **R4 用户调整少** | 用户在 Phase 2 细节确认时，改动 ≤ 1 处就接受了 |

#### 7.3 建议提示格式

```
💡 发现新布局方案，建议加入骨架模板库

本次「[布局描述]」来自 AI 推导，与现有模板相比有以下差异：
  · [差异描述1]
  · [差异描述2]

符合以下复用价值条件：
  ✅ R1 结构差异度：[具体说明]
  ✅ R2 场景通用性：适合「[界面类型]」类界面
  ❌ R4 用户调整少：您调整了 [调整内容]

建议模板名称：「[名称]」
是否将此方案加入骨架模板库？[是 / 否 / 修改后加入]
```

#### 7.4 加入模板库操作

用户确认「是」后，AI 执行：

1. 为新模板分配 ID（按序：SKL-005、SKL-006……）
2. 提取本次骨架 HTML，抽象为通用模板（将具体分组名替换回 `{{LABEL}}` 占位符）
3. 编写 YAML 元数据（id / 适用场景 / 典型界面 / 使用次数: 0）
4. 追加到 `skeleton-templates.md` 的「新模板追加区」
5. 回复：「✅ 已将「[名称]」作为 [SKL-00X] 加入模板库，下次相似界面可直接调用。」

---

## Color System (颜色系统)

### CSS Variables

```css
:root {
    /* 按钮颜色 */
    --ui-primary-btn-top: #8c2133;
    --ui-primary-btn-bot: #5c111d;
    --ui-sec-btn-top: #3a5594;
    --ui-sec-btn-bot: #273F74;
    
    /* 背景和边框 */
    --ui-bg-light-blue: #b5c0d9;
    --ui-border-blue: #8b96b6;
    
    /* 文字颜色 */
    --ui-text-dark: #1a1a1a;
    --ui-text-light: #e6e6e6;
    
    /* 强调色 */
    --ui-gold: #e8c361;
    --ui-green: #3a9e3e;
    
    /* 页签颜色 */
    --ui-tab-active-bg: #CAD4F7;
    --ui-tab-normal-bg: #333C6B;
    
    /* 背包/库存背景 */
    --ui-inv-bg: #B1B9E0;
}
```

### Primary Colors

- **Red (Primary Action/Highlight)**:
  - Gradient: `linear-gradient(to bottom, #8c2133, #5c111d)`
  - Border: `#111` | Shadow: `inset 0 1px 1px rgba(255,255,255,0.2), 0 1px 2px rgba(0,0,0,0.4)`
- **Blue (Secondary Action/Navigation)**:
  - Gradient: `linear-gradient(to bottom, #3a5594, #273F74)`
  - Border: `#111` | Shadow: same as red

### Backgrounds

| Element | Value | Lock |
|---------|-------|------|
| Panel Body | `#11151c` + `repeating-linear-gradient(0deg, #11151c, #11151c 1px, #1a212c 1px, #1a212c 2px)` scanlines | ⚠️ IMMUTABLE |
| Panel Header | `linear-gradient(to right, #233263, #2E4077 30%, #2E4077 70%, #233263)` | ⚠️ IMMUTABLE |
| Input/Display | `#B1B8C8`, border `#8b96b6` | — |
| Area Background | `#B1B9E0`, border `#8b96b6` | — |
| Tab Active | `#CAD4F7` | — |
| Tab Normal | `#333C6B` | — |

### Text Colors

| Context | Primary | Secondary | Success | Link | Error | Warning |
|---------|---------|-----------|---------|------|-------|---------|
| On Light Background | `#121421` | `#45476D` | `#0B800E` | `#0F11FF` | `#FC0017` | — |
| On Dark Background | `#FFFFFF` | `#C3BFD8` | `#70F248` | — | `#F61729` | `#FFFF2F` |

---

## Typography (排版)

- **Font**: `"DFYuanW7-B5", "华康圆体", "Yuane", "Microsoft YaHei", sans-serif`
- **Allowed sizes**: `14px` (default), `16px` (titles, inputs).
- **Warning trigger**: any size `<14px` requires user confirmation before use.

| Usage | Size | Weight |
|-------|------|--------|
| Window Title | 14px | Bold |
| Input Fields | 16px | Normal |
| Buttons / Tabs / Body | 14px | Normal |
| Window Controls | 16px | Bold |

### Text Consistency Rules (文本一致性规则)

1. **禁止文本换行** — 统计信息、标签等短文本必须添加 `white-space: nowrap` 防止意外换行
2. **同一文本段字号统一** — 同一段文本（如"次数: 0/20"）中所有字符必须使用相同字号，不允许数值部分与标签部分字号不一致
3. **内联数值样式** — 数值高亮仅允许修改颜色和字重，禁止修改字号：
   ```css
   .stat-value {
       color: #0B800E;      /* 允许：颜色高亮 */
       font-weight: bold;   /* 允许：加粗 */
       font-size: 14px;     /* 强制：与父级保持一致 */
   }
   ```

---

## Component Specifications (组件规范)

### Buttons (按钮)

**设计约束**: 只允许修改宽度和文案，其他属性禁止修改

| Property | Value |
|----------|-------|
| Height | `24px` (强制) |
| Default Width | `96px` (可调整) |
| Border Radius | `4px` |
| Border | `1px solid #111` |
| Font Size | `14px` |
| Text Color | `#e6e6e6` |
| Text Shadow | `1px 1px 1px rgba(0,0,0,0.8)` |
| Box Shadow | `inset 0 1px 1px rgba(255,255,255,0.2), 0 1px 2px rgba(0,0,0,0.4)` |

**Primary Button (红色)**:
```css
background: linear-gradient(to bottom, #8c2133, #5c111d);
```

**Secondary Button (蓝色)**:
```css
background: linear-gradient(to bottom, #3a5594, #273F74);
```

**Icon Button**:
- Size: `24px × 24px`
- Icon Size: `16px × 16px`

### Tabs (页签)

**设计约束**: 只允许修改宽度和文案，其他属性禁止修改

#### Secondary Tabs (二级页签)
| Property | Value |
|----------|-------|
| Height | `24px` (强制) |
| Default Width | `88px` (可调整) |
| Border Radius | `2px` |
| Gap | `2px` |
| Font Size | `14px` |

**Normal State**:
- Background: `#333C6B`
- Text Color: `#e6e6e6`
- Text Shadow: `1px 1px 1px rgba(0,0,0,0.8)`

**Active State**:
- Background: `#CAD4F7`
- Text Color: `#1a1a1a`
- No text shadow

#### Tertiary Tabs (三级页签)
| Property | Value |
|----------|-------|
| Height | `24px` (强制) |
| Default Width | `80px` (可调整) |
| Border Radius | `4px` |
| Layout | Vertical (flex-direction: column) |

### Input (输入框)

| Property | Value |
|----------|-------|
| Height | `28px` |
| Default Width | `160px` |
| Border Radius | `6px` |
| Background | `#B1B8C8` |
| Border | `1px solid #8b96b6` |
| Font Size | `16px` |
| Text Color | `#121423` |
| Box Shadow | `inset 0 1px 3px rgba(0,0,0,0.15)` |

### Windows / Panels (窗体)

**设计约束**: 只允许修改标题文案和尺寸，最大尺寸 800×600px

#### Window Header (标题栏) — 禁止修改样式
| Property | Value |
|----------|-------|
| Height | `24px` |
| Background | `linear-gradient(to right, #233263, #2E4077 30%, #2E4077 70%, #233263)` ⚠️ IMMUTABLE |
| Font Size | `14px` |
| Font Weight | Bold |
| Border Bottom | `1px solid #111` |

#### Window Body (内容区域) — 禁止修改背景
| Property | Value |
|----------|-------|
| Background Color | `#11151c` ⚠️ IMMUTABLE |
| Background Image | `repeating-linear-gradient(0deg, #11151c, #11151c 1px, #1a212c 1px, #1a212c 2px)` |
| Border | `2px solid #252f4a` |
| Border Radius | `6px` |
| Max Size | `800px × 600px` |

### Area Background (区域衬底)

**设计约束**: 禁止修改颜色与描边

| Property | Value |
|----------|-------|
| Background | `#B1B9E0` |
| Border | `1px solid #8b96b6` |
| Border Radius | `4px` |
| Box Shadow | `inset 0 0 8px rgba(255,255,255,0.4)` |

### Item Icon (道具图标)

**设计约束**: 禁止修改尺寸与颜色

| Property | Value |
|----------|-------|
| Size | `52px × 52px` |
| Background | `#B1B9E0` |
| Border | `1px solid #7a85a5` |
| Border Radius | `6px` |
| Box Shadow | `inset 0 0 6px rgba(255,255,255,0.5), 0 1px 2px rgba(0,0,0,0.2)` |

**Level Label (等级标签)**:
- Position: bottom-right
- Font Size: `14px`
- Font Weight: Bold
- Color: White with black stroke

### Small Item Icon (小型道具图标)

**设计约束**: 禁止修改尺寸与颜色

| Property | Value |
|----------|-------|
| Size | `30px × 30px` |
| Background | `#B1B9E0` |
| Border | `1px solid #7a85a5` |
| Border Radius | `6px` |
| Box Shadow | `inset 0 0 6px rgba(255,255,255,0.5), 0 1px 2px rgba(0,0,0,0.2)` |
| Emoji Size | `18px` |

### Player Avatar (玩家头像)

**设计约束**: 禁止修改尺寸与颜色

| Property | Value |
|----------|-------|
| Size | `52px × 52px` |
| Background | `linear-gradient(to bottom, #26759A, #9AB694)` |
| Border | `1px solid #E8F3F4` |
| Border Radius | `6px` |
| Box Shadow | `inset 0 0 6px rgba(255,255,255,0.5), 0 1px 2px rgba(0,0,0,0.2)` |

**空头像状态**:
- Background: `rgba(177, 185, 224, 0.4)`
- Border: `1px dashed #a0a8c0`
- 可选显示 `+` 号占位符

### Pet Avatar (召唤兽头像)

**设计约束**: 禁止修改尺寸与颜色，样式与道具图标类似

| Property | Value |
|----------|-------|
| Size | `52px × 52px` |
| Small Size | `30px × 30px` |
| Background | `#B1B9E0` |
| Border | `1px solid #7a85a5` |
| Border Radius | `6px` |
| Box Shadow | `inset 0 0 6px rgba(255,255,255,0.5), 0 1px 2px rgba(0,0,0,0.2)` |

---

## ⚠️ Preset Image Assets (预设图片资源约束)

以下预设图片资源为系统默认资源，**任何情况下禁止替换**：

| 资源文件 | 用途 | 对应样式类 |
|----------|------|------------|
| `resource/人物头像.png` | 玩家头像预设图片 | `.ui-avatar--character` |
| `resource/道具图标.png` | 道具图标预设图片 | `.ui-item-icon--preset` |
| `resource/召唤兽头像.png` | 召唤兽头像预设图片 | `.ui-pet-avatar--preset` |

### 设计规范

1. **禁止生成新图片** — 在设计界面时，对于头像和图标类组件，直接使用预设样式类即可，无需为这些组件生成新的图片资源
2. **使用默认资源** — 所有头像、道具图标、召唤兽头像统一使用系统预设图片
3. **样式类使用方法**：
   ```html
   <!-- 玩家头像 -->
   <div class="ui-avatar ui-avatar--character"></div>
   
   <!-- 道具图标 -->
   <div class="ui-item-icon ui-item-icon--preset">
       <span class="ui-item-level">5</span>
   </div>
   
   <!-- 召唤兽头像 -->
   <div class="ui-pet-avatar ui-pet-avatar--preset"></div>
   ```

4. **约束原因**：
   - 保持界面视觉一致性
   - 减少设计工作量
   - 避免资源冗余

### Inventory (背包格子)

**设计约束**: 禁止修改颜色与描边

| Property | Value |
|----------|-------|
| Grid | 5 columns × 4 rows |
| Cell Size | `52px × 52px` |
| Background | `#B1B9E0` |
| Border | `2px solid #8b96b6` |
| Border Radius | `6px` |
| Cell Divider | `1px solid #8b96b6` |

**Selected State**:
- Box Shadow: `inset 0 0 0 2px #e8c361, 0 0 4px #e8c361`

### List Item (列表项)

**设计约束**: 禁止修改颜色与描边

| Property | Value |
|----------|-------|
| Height | `64px` |
| Default Width | `320px` |
| Background | `#CAD4F7` |
| Border | `1px solid #a0a8c0` |
| Border Radius | `4px` |
| Padding | `6px 10px` |
| Gap | `10px` |

### Form Elements (表单元素)

#### Radio Button (单选框)
| Property | Value |
|----------|-------|
| Size | `16px × 16px` |
| Border Radius | `50%` |
| Border | `2px solid #8b96b6` |
| Background | `#d0d5e5` |
| Selected Dot | `8px × 8px`, color `#3b486e` |

#### Checkbox (复选框)
| Property | Value |
|----------|-------|
| Size | `16px × 16px` |
| Border Radius | `2px` |
| Border | `1px solid #3b486e` |
| Background | `#3b486e` |
| Checkmark | `✓`, white, 14px bold |

### Stars Rating (星级评分)

| Property | Value |
|----------|-------|
| Color | `#e8c361` |
| Font Size | `14px` |
| Letter Spacing | `-2px` |
| Max Stars | 3 |

---

## Layout & Spacing (布局与间距)

| Rule | Value |
|------|-------|
| Minimum gap | `4px` |
| Standard gap | `6px` or `8px` |
| Section spacing | `8–12px` |
| Tab gap | `2px` |
| List item gap | `10px` |
| List row height | `24–26px` |

### Modular Split Layout
Avoid single top-to-bottom columns when one side is much shorter. Use horizontal layers:
- **Top Layer**: [List] + [Viewport] — equal height
- **Middle Layer**: [Fixed Stats] + [Variable/Action Stats]
- **Footer Layer**: full-width or grouped action buttons

Result: the window stays a filled rectangle without large empty gaps.

### Section Grouping (功能分组)

设计时必须识别界面的功能分组，并通过视觉手段强调分组边界：

1. **分组识别** — 分析界面内容，识别不同功能区域（如：任务列表区、奖励进度区、操作按钮区）
2. **视觉强调** — 使用浅色区域衬底（`.ui-area-bg`）包裹主要功能分组，与深色窗口背景形成对比
3. **分组间距** — 不同功能分组之间使用 `8-12px` 间距分隔
4. **嵌套规则** — 分组内的子元素（如列表项、卡片）使用更浅或对比色背景

#### Color Depth Levels (颜色深浅层级)

分组的颜色层级遵循 **"主功能区用浅色包裹"** 原则，从深到浅共分为5级：

| 层级 | 颜色值 | 用途 | 典型元素 |
|------|--------|------|----------|
| **Level 0** | `#11151c` ⚠️ | 窗口底色（最深，不可变） | Window Body |
| **Level 1** | `#233263→#2E4077` ⚠️ | 标题栏渐变（不可变） | Window Header |
| **Level 2** | `#333C6B` | 深色控件/未选中态 | 未选中Tab、分组标题条 |
| **Level 3** | `#B1B9E0` | 功能区浅色衬底 | 主功能区域、列表容器、详情卡片 |
| **Level 4** | `#CAD4F7` | 选中态/高亮 | 选中Tab、列表项、交互焦点 |

**分组层级判断规则**：

| 分组类型 | 颜色层级 | 说明 |
|----------|----------|------|
| 标题栏 | Level 1 (固定) | 窗口标题，不可修改 |
| 一级/二级页签区 | Level 0 + Level 2/4 | 页签直接在窗口底色上，选中态用浅色 |
| **主功能区（列表、详情、表单）** | **Level 3 浅色衬底** | 核心内容区域必须用浅色包裹 |
| 嵌套子元素（列表项、卡片） | Level 4 或交替色 | 在浅色衬底内使用更浅/对比色 |
| 底部操作栏 | Level 0 + 按钮 | 操作按钮直接在窗口底色上 |
| 空状态区 | Level 3 浅色衬底 | 即使内容为空也需要浅色衬底占位 |

**核心原则**：
- **背景越深，层级越底层**
- **主功能区必须用浅色衬底包裹**，与深色窗口底色形成对比
- **交互元素越亮，重要性/可交互性越高**
- **选中态永远比未选中态更浅/更亮**

**示例结构**：
```html
<div class="ui-window-body">
    <div class="main-layout">
        <!-- 分组1：任务区域（浅色衬底） -->
        <div class="task-section ui-area-bg">
            <div class="left-nav">...</div>
            <div class="content-area">...</div>
        </div>
        
        <!-- 分组2：奖励进度区域 -->
        <div class="reward-section">
            ...
        </div>
    </div>
</div>
```

**CSS 示例**：
```css
.task-section {
    display: flex;
    gap: 8px;
    background: var(--ui-inv-bg);        /* #B1B9E0 */
    border: 1px solid var(--ui-border-blue);
    border-radius: 4px;
    box-shadow: inset 0 0 8px rgba(255,255,255,0.4);
    padding: 8px;
}
```

### Alignment Rules (对齐规则)

设计时必须遵循以下对齐原则，确保界面元素整齐、视觉平衡：

#### 1. 行内元素间距统一

同一行内的相关控件使用固定 `gap` 保持紧凑，避免使用 `justify-content: space-between` 导致元素分散。

```css
/* ✅ 正确：固定间距 */
.row-controls {
    display: flex;
    align-items: center;
    gap: 8px;
}

/* ❌ 错误：两端分散 */
.row-controls {
    display: flex;
    justify-content: space-between;  /* 会导致元素拉伸分散 */
}
```

#### 2. 不同尺寸元素的列对齐

当不同尺寸的元素需要垂直对齐时，使用 Grid 固定列宽，让小元素在列内居中。

**典型场景**：大道具框 (52px) 与小道具框 (30px) 垂直对齐

```css
/* 大道具行 */
.items-grid {
    display: grid;
    grid-template-columns: repeat(3, 52px);  /* 固定列宽 */
    gap: 6px;
}

/* 小道具行 - 使用相同列宽 */
.items-grid-small {
    display: grid;
    grid-template-columns: repeat(3, 52px);  /* 与上方同列宽 */
    gap: 6px;
}

/* 小元素在列内居中 */
.items-grid-small .ui-item-icon-small {
    justify-self: center;
}
```

#### 3. 分组衬底完整包裹

功能区域的浅色衬底必须完整包裹所有子元素，避免"漏底"导致视觉断裂。

**典型场景**：页签 + 列表应整体包裹在同一衬底内

```html
<!-- ❌ 错误：页签在衬底外 -->
<div class="section">
    <div class="tabs-row">...</div>           <!-- 在深色背景上 -->
    <div class="list-area ui-area-bg">...</div>  <!-- 在浅色衬底内 -->
</div>

<!-- ✅ 正确：整体包裹 -->
<div class="section ui-area-bg">
    <div class="tabs-row">...</div>           <!-- 在浅色衬底内 -->
    <div class="list-area">...</div>          <!-- 在浅色衬底内 -->
</div>
```

#### 4. 左右分栏视觉平衡

左右分栏布局时，两侧浅色区域的顶部/底部边界应尽量对齐，形成饱满的矩形视觉。

| 检查项 | 要求 |
|--------|------|
| 顶部对齐 | 左右两侧浅色衬底的顶边在同一水平线 |
| 底部平衡 | 避免一侧明显短于另一侧，必要时使用 `flex: 1` 撑满 |
| 间距一致 | 左右分栏与窗体边缘的间距相同 |

```css
.main-layout {
    display: flex;
    gap: 10px;
}

.left-panel {
    width: 260px;
    display: flex;
    flex-direction: column;
    gap: 10px;
}

.right-panel {
    flex: 1;  /* 自动填充剩余宽度 */
    display: flex;
    flex-direction: column;
}
```

#### 5. 多栏等高对齐

当界面使用多栏布局（如左中右三栏）时，必须确保各栏高度一致，避免出现黑色空隙：

```css
/* 主内容区强制等高 */
.main-content {
    display: flex;
    align-items: stretch;  /* 关键：强制子元素等高 */
    gap: 6px;
}

/* 每栏设为 flex 容器 */
.left-column,
.center-column,
.right-column {
    display: flex;
    flex-direction: column;
}

/* 需要填充高度的区域 */
.fill-section {
    flex: 1;
}
```

**典型问题**：左侧有两个分组（人物信息 + 法宝配置），右侧有一个长分组（召唤兽列表），导致左侧底部出现黑色空隙。

**解决方案**：将左侧某个分组设为 `flex: 1` 自动填充剩余高度。

#### 6. 弹性分组规则

当同一栏内有多个垂直分组，且需要与其他栏对齐高度时，应区分"松散区域"和"紧凑区域"：

| 分组类型 | 布局方式 | 适用场景 |
|----------|----------|----------|
| **松散区域** | `flex: 1` + `justify-content: space-between` | 内容较少但需要填充空间（如人物信息区） |
| **紧凑区域** | 不设置 `flex`，保持自然高度 | 内容固定且紧凑（如法宝配置区） |

**松散区域示例**（内容均匀分布，避免空白集中）：
```css
.character-section {
    flex: 1;
    display: flex;
    flex-direction: column;
    justify-content: space-between;  /* 上下内容均匀分布 */
}
```

**紧凑区域示例**（保持自然高度）：
```css
.treasure-section {
    /* 不设置 flex，保持内容高度 */
    padding: 8px;
}
```

**核心原则**：
- 优先让内容较少的区域扩展（设为松散区域）
- 内容固定的区域保持紧凑
- 使用 `justify-content: space-between` 让松散区域内的元素均匀分布，避免空白集中在中间

#### 对齐检查清单

设计完成后，使用以下清单检查对齐问题：

- [ ] 同一行控件是否使用统一 `gap`？
- [ ] 不同尺寸元素是否通过 Grid 列对齐？
- [ ] 功能分组衬底是否完整包裹所有子元素？
- [ ] 左右分栏的顶部边界是否对齐？
- [ ] 是否存在"漏底"的黑色空隙？

### Scrollable Area Rules (滚动区域规则)

滚动区域必须遵循单向滚动原则，确保用户体验一致且操作简单：

#### 1. 单向滚动原则

**每个滚动区域只能有一个方向支持滚动**，禁止同时出现横向和纵向滚动条。

| 规则 | 说明 |
|------|------|
| 纵向滚动 | `overflow-y: auto; overflow-x: hidden;` |
| 横向滚动 | `overflow-x: auto; overflow-y: hidden;` |
| 禁止双向 | 不允许 `overflow: auto` 或 `overflow: scroll` 导致双向滚动 |

#### 2. 宽度适配优先

当内容可能溢出时，优先调整容器宽度以完整显示内容，而非依赖横向滚动：

```css
/* ✅ 正确：扩展窗体宽度适配内容 */
.ui-window {
    width: 680px;  /* 根据内容调整 */
}

.scroll-list {
    overflow-y: auto;
    overflow-x: hidden;  /* 禁止横向滚动 */
}

/* ❌ 错误：允许双向滚动 */
.scroll-list {
    overflow: auto;  /* 可能产生双向滚动 */
}
```

#### 3. 滚动区域高度限制

纵向滚动区域必须设置明确的最大高度：

| 场景 | 推荐 max-height |
|------|-----------------|
| 列表区域 | `300px` ~ `400px` |
| 弹窗内列表 | 根据窗体高度计算，预留底部操作区 |

```css
.team-list {
    display: flex;
    flex-direction: column;
    gap: 6px;
    max-height: 400px;
    overflow-y: auto;
    overflow-x: hidden;
}
```

#### 4. 滚动条样式

滚动条应与整体设计风格一致：

```css
/* Webkit 滚动条样式 */
.scroll-area::-webkit-scrollbar {
    width: 6px;
}

.scroll-area::-webkit-scrollbar-track {
    background: rgba(0,0,0,0.2);
    border-radius: 3px;
}

.scroll-area::-webkit-scrollbar-thumb {
    background: #5a6a9a;
    border-radius: 3px;
}

.scroll-area::-webkit-scrollbar-thumb:hover {
    background: #6b7bb0;
}
```

#### 滚动区域检查清单

设计完成后，检查以下项目：

- [ ] 滚动区域是否只有单一方向可滚动？
- [ ] 是否设置了 `overflow-x: hidden` 或 `overflow-y: hidden`？
- [ ] 容器宽度是否足够显示所有横向内容？
- [ ] 纵向滚动区域是否设置了 `max-height`？
- [ ] 滚动条样式是否与整体设计一致？

---

## Examples

### Example 1: Primary Button

```html
<button class="ui-btn ui-btn-primary" style="width: 96px;">
  确认
</button>
```

Or inline style:
```html
<button style="width:96px; height:24px; padding:0; border-radius:4px;
  background:linear-gradient(to bottom, #8c2133, #5c111d);
  border:1px solid #111; color:#e6e6e6; font-size:14px;
  text-shadow:1px 1px 1px rgba(0,0,0,0.8);
  box-shadow:inset 0 1px 1px rgba(255,255,255,0.2), 0 1px 2px rgba(0,0,0,0.4);
  cursor:pointer;">
  确认
</button>
```

### Example 2: Active vs Inactive Tab

```html
<!-- Inactive -->
<div style="height:24px; width:88px; border-radius:2px;
  background:#333C6B; color:#e6e6e6; font-size:14px;
  border:1px solid #111; text-shadow:1px 1px 1px rgba(0,0,0,0.8);
  display:flex; align-items:center; justify-content:center;">
  道具
</div>
<!-- Active -->
<div style="height:24px; width:88px; border-radius:2px;
  background:#CAD4F7; color:#1a1a1a; font-size:14px;
  border:1px solid #111;
  display:flex; align-items:center; justify-content:center;">
  装备
</div>
```

### Example 3: Window Shell Structure

```html
<div style="width:420px; max-width:800px; max-height:600px;
  border:2px solid #252f4a; border-radius:6px; overflow:hidden;
  box-shadow:0 4px 12px rgba(0,0,0,0.5);">
  <!-- Header — IMMUTABLE color -->
  <div style="height:24px;
    background:linear-gradient(to right, #233263, #2E4077 30%, #2E4077 70%, #233263);
    display:flex; align-items:center; padding:0 10px;
    color:#e6e6e6; font-size:14px; font-weight:bold;
    border-bottom:1px solid #111; position:relative;
    box-shadow:inset 0 1px 1px rgba(255,255,255,0.1);">
    <span style="position:absolute; left:50%; transform:translateX(-50%);
      text-shadow:1px 1px 2px black; letter-spacing:1px;">背包</span>
    <span style="margin-left:auto; font-size:16px; font-weight:bold;
      text-shadow:1px 1px 1px black;">? ×</span>
  </div>
  <!-- Body — IMMUTABLE color -->
  <div style="min-height:220px; background-color:#11151c;
    background-image:repeating-linear-gradient(0deg, #11151c, #11151c 1px, #1a212c 1px, #1a212c 2px);">
    <!-- content -->
  </div>
</div>
```

### Example 4: Input Field

```html
<input class="ui-input" type="text" placeholder="请输入..." style="width:160px;">
```

Or inline style:
```html
<input style="width:160px; height:28px; border-radius:6px;
  background:#B1B8C8; border:1px solid #8b96b6;
  padding:0 12px; font-size:16px; color:#121423;
  box-shadow:inset 0 1px 3px rgba(0,0,0,0.15);
  outline:none;" type="text" placeholder="请输入...">
```

### Example 5: Item Icon with Level

```html
<div style="width:52px; height:52px; background:#B1B9E0;
  border:1px solid #7a85a5; border-radius:6px; position:relative;
  display:flex; align-items:center; justify-content:center;
  box-shadow:inset 0 0 6px rgba(255,255,255,0.5), 0 1px 2px rgba(0,0,0,0.2);">
  <span style="font-size:32px;">⚔️</span>
  <span style="position:absolute; bottom:1px; right:2px;
    font-size:14px; font-weight:bold; color:white;
    text-shadow:-1px -1px 0 #000, 1px -1px 0 #000, -1px 1px 0 #000, 1px 1px 0 #000;">
    99
  </span>
</div>
```

---

## Best Practices

1. **两阶段确认优先** — 生成最终 HTML 前，必须经过 Step 1 骨架确认 + Step 2 分区细节确认
2. **骨架模板优先** — Step 1 优先从 `skeleton-templates.md` 匹配，不强求 AI 额外推导
3. **Color lock first** — 在 Step 3 校验窗口标题栏 (`#233263 → #2E4077`) 和主体 (`#11151c`) 颜色不被修改
4. **Typography gate** — 拒绝任何 `<14px` 文字，只允许 `14px` 和 `16px`
5. **Component constraints** — 遵守每个组件的「禁止修改」规则
6. **Modular layout default** — Step 2 变体生成时，优先使用分层分栏布局，避免单列
7. **HTML only** — 所有交付物为完整 HTML 文件（含 `<style>` 块或 inline styles）
8. **Button sizing** — height 固定 `24px`，width 可调（默认 `96px`）
9. **Tab sizing** — height 固定 `24px`，width 可调（secondary: `88px`，tertiary: `80px`）
10. **New component discovery** — 每次完成任务后，询问新模式是否加入规范（Step 6）
11. **Skeleton auto-discovery** — AI 推导方案被采用后，运行 Step 7 判断是否加入模板库
12. **Size cap** — 单个窗口不超过 800×600px
13. **Use CSS classes** — 优先使用 `ui_components.css` 中的 `ui-*` 类名

## CSS Class Reference

| Component | Class Name(s) |
|-----------|---------------|
| Primary Button | `.ui-btn .ui-btn-primary` |
| Secondary Button | `.ui-btn .ui-btn-secondary` |
| Icon Button | `.ui-btn-icon` |
| Secondary Tab | `.ui-tab-secondary` |
| Tertiary Tab | `.ui-tab-tertiary` |
| Tab Container | `.ui-tabs-secondary`, `.ui-tabs-tertiary` |
| Window | `.ui-window` |
| Window Header | `.ui-window-header` |
| Window Title | `.ui-window-title` |
| Window Controls | `.ui-window-controls` |
| Window Body | `.ui-window-body` |
| Input | `.ui-input` |
| Radio | `.ui-radio` |
| Checkbox | `.ui-checkbox` |
| Area Background | `.ui-area-bg` |
| Item Icon | `.ui-item-icon` |
| Item Level | `.ui-item-level` |
| Inventory | `.ui-inventory` |
| Inventory Cell | `.ui-inv-cell` |
| List Item | `.ui-list-item` |
| Stars | `.ui-stars`, `.ui-stars--1`, `.ui-stars--2`, `.ui-stars--3` |
| Player Avatar | `.ui-avatar`, `.ui-avatar--character` |
| Pet Avatar | `.ui-pet-avatar`, `.ui-pet-avatar-small`, `.ui-pet-avatar--preset` |
| Small Item Icon | `.ui-item-icon-small` |
| Item Icon Preset | `.ui-item-icon--preset` |
| Character Model | `.ui-model-character` |
| Pet Model | `.ui-model-pet` |

### Character & Pet Models (人物与召唤兽模型)

**设计约束**: 禁止修改尺寸和图片源

#### Character Model (人物模型)
| Property | Value |
|----------|-------|
| Size | `64px × 80px` |
| Background Image | `resource/人物模型.png` ⚠️ 禁止替换 |
| Background Size | `contain` |

#### Pet Model (召唤兽模型)
| Property | Value |
|----------|-------|
| Size | `64px × 56px` |
| Background Image | `resource/召唤兽模型.png` ⚠️ 禁止替换 |
| Background Size | `contain` |

## References

- [ui_components.css](../../基础控件库/ui_components.css) - 基础控件样式库
- [Agent Skills Specification](https://agentskills.io/specification)
- [XYQ畅玩服 原始方案设计文档](../XYQ畅玩服-方案设计.md)
