---
name: hutong-ui-pc-to-mobile
description: >
  基于互通组件库新版（styles.css）将PC端游戏界面截图转化为移动端HTML界面设计。
  当用户提供PC端界面截图时，自动识别界面结构、布局区域与功能模块，
  并使用互通组件库中的 ui-* Class 规范生成完整可运行的移动端 HTML 文件。
  触发关键词：PC转移动端、移动端界面、互通端UI、移动端弹窗、手游界面、
  pc to mobile、mobile ui、做移动端界面、根据截图生成、互通组件库、ui- 组件。
allowed-tools: Bash Read Write Edit Glob Grep
metadata:
  tags: ui-design, mobile-ui, pc-to-mobile, hutong, game-ui, html, css-components
  version: "1.0"
---

# 互通组件库新版 · PC → 移动端 UI 转化 Skill

> 设计风格：游戏互通端 · 蓝紫调 Fantasy UI  
> 输出格式：完整可运行 HTML 文件  
> CSS 来源：`styles.css`（互通组件库新版）  
> 移动端最大宽度：**750px**（以弹窗为核心内容单元）

---

## 当需要使用本 Skill 时

- 用户提供 PC 端游戏界面截图，要求生成对应的移动端 HTML
- 用户要求将某个功能弹窗「适配移动端」
- 用户要求「参考 PC 截图，用互通组件库做移动端界面」
- 用户要求做新的互通端 UI 弹窗，但没有提供截图（从 Step 0 开始骨架确认）

---

## 🗂️ 核心资源索引（使用前必查）

> ⛔ **强制规则**：遇到任何组件/颜色/尺寸问题，必须先查此索引，再参照规范输出。**禁止凭记忆直接写组件 HTML。**

| 需要的内容 | 查阅位置 |
|---|---|
| 所有 `ui-*` Class 实际样式 | `styles.css` |
| 组件清单与限制规则 | `互通组件库框架清单.md` |
| 按钮（通用/小方形） | 框架清单 → 一、按钮 |
| 横向页签 / 纵向页签 | 框架清单 → 二、页签 |
| 文字颜色规则 | 框架清单 → 三、字色规则 |
| 字体规范 | 框架清单 → 四、字体规范 |
| 单选框 / 复选框 / 输入框 | 框架清单 → 五、表单元素 |
| 区域衬底 | 框架清单 → 六、区域衬底 |
| 道具框 / 头像框 / 模型背景 | 框架清单 → 七、道具与头像 |
| 数字角标 / 选中框 | 框架清单 → 八、角标与选中框 |
| 列表卡片 | 框架清单 → 九、列表卡片 |
| 一级弹窗 / 二级弹窗 / 纵向页签 | 框架清单 → 十、弹窗 |

---

## PC → 移动端 转化原则

### 核心转化逻辑

| PC 端特征 | 移动端适配策略 |
|---|---|
| 固定像素布局（如 752×498px） | 使用 `ui-dialog-v2` 预设尺寸中最接近的，或以 `width: 100%; max-width: 750px` 自适应 |
| 多列并排布局 | 优先保留双列，极窄时改为单列堆叠 |
| 横向页签 | 保留横向页签，尺寸缩小至三级（`ui-tab-btn-tertiary`）|
| 纵向页签（右侧外挂） | 保留 `ui-vtab-btn`，与弹窗的 overlap 关系不变（`right: -46px`）|
| 多个小按钮并排 | 改用 `ui-btn-secondary-sm` + `ui-btn-square-sm-*` 组合 |
| 文字较密 | 优先用 `ui-text-single-18` / `ui-text-multi-18`，极限情况用 `ui-text-single-16` |
| 大段属性数据表格 | 改为两列 flex 网格，每项 `ui-list-card` 包裹 |
| 滚动列表 | 加 `overflow-y: auto; max-height: Xpx` 并隐藏滚动条 |

### 禁止行为（移植时同样适用）

- ⛔ 禁止修改 `ui-dialog-v1` 的 752×500px 固定尺寸（移动端改用 `ui-dialog-v2` 系列）
- ⛔ 禁止删除 `ui-btn-primary` / `ui-tab-btn-primary` 的两侧装饰纹伪元素
- ⛔ 禁止硬编码颜色值，必须通过 CSS 变量（`var(--ui-*)`）引用
- ⛔ 禁止使用图片替代矢量图标（叉号/问号/对号等必须用内联 SVG 或 CSS 伪元素）
- ⛔ `ui-dialog-inner-panel-v2` 的左/右/上边距（`6px 23px ? 22px`）不可修改；**`margin-bottom` 须在内联样式中覆盖为 `18px`**（styles.css 默认 66px 会产生底部空白，必须覆盖）
- ⛔ 禁止使用 `ui-` 以外的自定义 Class 前缀

---

## Instructions（执行步骤）

> **任务类型判断（开始前先确认）**：
> - **提供了 PC 截图**：从 Step 0 开始，走完整流程
> - **只做局部调整 / 新增单个组件**：直接跳到 Step 3

---

### Step 0：截图解析 & 骨架确认（提供截图时必做）

1. **识别 PC 截图中的功能模块**，列出：
   - 弹窗类型（一级/二级）和大致尺寸
   - 标题栏内容（标题文字 + 是否有问号按钮 + 关闭按钮）
   - 内容区域分区（列表区 / 属性区 / 按钮区等）
   - 使用的控件类型（页签 / 输入框 / 道具框 / 头像 / 按钮等）

2. **声明本次组件清单**（标注尺寸锁定控件）：

```
本次用到的组件：
☐ ui-dialog-v2-XXXxXXX（弹窗骨架）
☐ ui-dialog-close-btn（关闭按钮）
☐ ui-tab-btn（横向页签）
☐ ui-vtab-btn（纵向页签）⚠️ 定位锁定
☐ ui-avatar-56（头像框）⚠️ 尺寸锁定
☐ ui-item（道具框）⚠️ 尺寸锁定 56×56px
☐ ui-list-card（列表卡片）
☐ ui-btn-primary / ui-btn-secondary-sm（按钮）
☐ ui-input-box（输入框）
☐ ui-area-bg（区域衬底）
```

3. **绘制移动端骨架结构图**（ASCII，标注组件 Class）：

```
┌─────────────────────────────────────────────────┐  ← ui-dialog-v2-XXXxXXX
│  标题栏 [ui-dialog-header-v2]：[标题]      [?][X] │
├───────────────────────────┬─────────────────────┤
│  [ui-tab-btn 横向页签]     │                     │
├───────────────────────────┤  [右侧属性区]        │
│  [ui-list-card 列表区]     │  ui-area-bg         │
│  ui-avatar + 文字          │                     │
│  ...                       ├─────────────────────┤
│                            │  [ui-btn 按钮区]     │
└───────────────────────────┴─────────────────────┘
                                  └─ [ui-vtab-btn 纵向页签, right:-46px]
```

4. ⛔ **硬停止** — 输出骨架图和组件清单后，**必须立即停止**，等待用户确认：
   > "以上是移动端骨架方案，请确认或告知需要调整的部分。确认后我将继续执行 Step 1。"

---

### Step 1：选定弹窗尺寸

根据内容量从以下预设中选择（二级弹窗）：

| Class | 尺寸 | 适用场景 |
|---|---|---|
| `ui-dialog-v2-656x476` | 656×476px | 内容较多，有列表+属性双栏 |
| `ui-dialog-v2-520x438` | 520×438px | 中等内容，单列或双栏 |
| `ui-dialog-v2-580x368` | 580×368px | 横向为主，属性展示 |
| `ui-dialog-v2-446x276` | 446×276px | 轻量确认/提示弹窗 |
| `ui-dialog-v2-730x398` | 730×398px | 宽屏列表展示 |
| `ui-dialog-v2-322x498` | 322×498px | 高窄型，纵向滚动列表 |
| `ui-dialog-v2-378x360` | 378×360px | 方形布局 |
| `ui-dialog-v2-362x488` | 362×488px | 高窄，含纵向页签 |

> 若以上尺寸均不满足，**必须在 HTML 的 `<style>` 块内声明新 Class**，不可直接拉伸已有弹窗：
> ```css
> /* 示例：自定义 500×400px 弹窗 */
> .ui-dialog-v2-500x400 {
>   width: 500px;
>   height: 400px;
> }
> ```
> 配合 `ui-dialog-v2` 使用：`<div class="ui-dialog-v2 ui-dialog-v2-500x400">`  
> ⛔ 禁止在已有预设 Class 上覆写 width/height

---

### Step 2：验证颜色与字体

**颜色管理规则**（不可硬编码）：

| 用途 | CSS 变量 | 参考色值 |
|---|---|---|
| 弹窗背景 | `var(--ui-dialog-bg)` | `#c1d1fe` |
| 弹窗边框 | `var(--ui-dialog-border)` | `#9baee4` |
| 内底板背景 | `var(--ui-dialog-inner-bg)` | `#a5b6e7` |
| 输入框背景 | `var(--ui-input-bg)` | `#c1c6dd` |
| 亮底正文色 | `var(--ui-text-light-body)` | `#292F60` |
| 暗底标题色 | `var(--ui-text-dark-title)` | `#FFFFFF` |

**字体规则**（强制）：
- 字体族：`'DFPYuanW7-GB', sans-serif`（华康圆体W7，通过 @font-face 从 resource/华康圆体W7.TTC 加载）
- 可用字号：`22px / 20px / 18px / 16px / 14px`（14px 仅在极限空间使用）
- 多行文本行高：固定值（`24px / 22px / 20px`），**不可改为百分比**

---

### Step 3：应用组件规格

> ⛔ **每个组件使用前必须完成双核对**：
> 1. 查 `互通组件库框架清单.md` 对应章节 → 确认规格和限制规则
> 2. 查 `styles.css` → 确认实际 Class 名和样式值

> 📋 **组件规格与 Class 速查** → 见 `组件速查.md`（规格表 + 完整 Class 速查表二合一）

---

### Step 4：移动端布局规则

**核心原则**：

- 最小间距 `4px`，标准间距 `6–8px`，弹窗内边距遵循 `ui-dialog-inner-panel-v2` 的 `6px 23px 66px 22px`
- 优先使用 **双栏 flex 布局**（左侧列表 + 右侧属性/操作），避免单列堆砌
- 列表区域超过 4 项时添加 `overflow-y: auto` + `max-height` 并隐藏滚动条：

```css
overflow-y: auto;
scrollbar-width: none;       /* Firefox */
-ms-overflow-style: none;    /* IE/Edge */
/* ::-webkit-scrollbar { display: none; } */
```

- **底部操作栏布局**：操作栏（按钮 + 说明文字）作为**普通 flex 子项**排在内容区末尾，**禁止使用 `position: absolute`**（绝对定位会与弹性区内容重叠）。内底板须同时加 `overflow: hidden` 防止子项超出：
  ```html
  <div class="ui-dialog-inner-panel-v2"
       style="margin-bottom:18px; display:flex; flex-direction:column; gap:8px; overflow:hidden;">
    <!-- 固定区：banner、提示栏等 flex-shrink:0 -->
    <!-- 弹性区：列表/格子 flex:1; min-height:0 -->
    <!-- 操作栏：flex-shrink:0; height:48px -->
  </div>
  ```
- **信息组间距（`content-area` gap）**：`gap` 推荐值为 `8px`；若视觉需要呼吸感可设 `12px`，**禁止超过 16px**，避免内容总高超出可用区高度导致溢出
- **flex 行内按钮防文字纵向溢出**：属性行末尾追加功能性按钮（如「补充」「强化」）时，必须加 `white-space: nowrap; flex-shrink: 0`，防止按钮在 flex 容器中被压缩导致文字换行变纵向：
  ```html
  <div class="ui-btn ui-btn-secondary-sm ui-btn-blue"
       style="white-space:nowrap; flex-shrink:0; width:54px; padding:0;">补充</div>
  ```
- **输入框数值对齐方向**：属性值（等级、单项数值）用 `text-align: center`；消耗/拥有类大数字用 `text-align: left`（与标签阅读方向一致）；**禁止一律右对齐**
- `ui-list-card` 宽度由父容器决定，高度由内容撑开，**不可固定高度**
- 图片资源路径统一使用 `resource/xxx.png`（相对路径）

**PC → 移动端 控件对照**：

| PC 常见控件 | 对应移动端 Class |
|---|---|
| 属性值行（标签+数值） | `ui-list-card` 内 flex 排列 + `ui-text-light-body` / `ui-text-light-emphasis` |
| 列表项（人物+文字） | `ui-list-card` 内 `ui-avatar` + 右侧 flex 文字块 |
| 数量输入 | `ui-btn-square-sm-32 ui-btn-square-red/blue` + `ui-input-box` + `ui-btn-square-sm-32 ui-btn-square-red/blue`（加减按钮必须同色） |
| 操作按钮行 | 弹窗底部 `ui-btn ui-btn-primary ui-btn-red` 居中 |
| 道具格子 | `ui-item` + `ui-item-inner` + 可选 `ui-badge-number` |

---

### Step 4.5：双列布局高度对齐规范

使用**左侧装饰列（模型/头像/道具槽）+ 右侧信息列**的双栏布局时，必须使两列总高一致，避免视觉错位。

**对齐公式**（以右列为基准）：

```
模型框高度 = 右列信息总高 − 名称标签高 − 道具槽高 − gap × n
```

**示例计算**：

```
右列总高 = 装备信息区(174px) + gap(8px) + 消耗信息区(88px) = 270px
左列固定元素 = 名称标签(28px) + gap(8px) + 道具槽(56px) + gap(8px) = 100px
模型框高度 = 270 − 100 = 170px  ✅ 两列对齐
```

> ✅ 若右列高度因内容变化而改变，同步重新计算模型框高度  
> ⛔ 禁止直接目测估算，必须通过计算确保精确对齐

---

### Step 4.6：属性标签对齐规范

当信息列表中存在**字数不等的属性名**（如「伤害」2字 vs「法术伤害」4字）时，必须使用 CSS 两端对齐方案，**禁止手动插入 `&emsp;` 空格**：

```css
/* 属性标签等宽对齐 —— 固定宽度 = 最长属性名字数 × 字号（px）*/
.attr-label {
  width: 72px;               /* 示例：4字 × 18px */
  flex-shrink: 0;
  white-space: nowrap;
  display: inline-block;
  text-align: justify;
  text-align-last: justify;  /* 单行文本同样两端对齐 */
}
```

> ✅ 效果：2字标签与4字标签自动左右端对齐，右侧输入框整列对齐  
> ⛔ 禁止用 `&emsp;`、`&nbsp;` 等 HTML 实体手动垫空格

---

### Step 4.7：弹性区域填充规范

> **目的**：防止固定高度弹窗内部出现大块空白（内容高度 < 内底板可用高度时必须处理）。

**触发条件**：弹窗使用 `ui-dialog-v2-XXXxXXX` 固定尺寸，且内容元素的高度之和 < 内底板可用高度。

---

#### ① 计算内底板可用高度

```
可用高度 = 弹窗总高 − 标题栏高(34px) − 上边距(6px) − 下边距(18px)

示例：ui-dialog-v2-656x476 → 476 − 34 − 6 − 18 = 418px
```

将固定高度元素（Banner、提示栏、按钮行等）+ 间距之和与可用高度比较：
- **总和 ≥ 可用高度**：内容自然填满，无需特殊处理
- **总和 < 可用高度**：必须指定弹性区吸收剩余空间

---

#### ② 识别「弹性区」

内容中**高度最灵活、最可扩展**的区域即为弹性区（每个弹窗只选一个主弹性区）：

| 弹窗类型 | 弹性区选择 |
|---|---|
| 列表型 | 列表滚动容器 |
| 格子型（签到 / 背包 / 技能） | 格子 grid 容器 |
| 属性展示型 | 属性卡片区 |
| 双栏型 | 左右两列同时设 `flex: 1` |
| 纯文本 / 说明型 | 文本内容区 |

---

#### ③ 应用弹性填充（正确模式：内底板全链路 flex）

> ✅ 让 `ui-dialog-inner-panel-v2` 自身成为 flex 容器，所有内容（含操作栏）均为其直接 flex 子项。  
> ⛔ 不使用 `height:100%` + 子 div 包裹（会导致 footer 与弹性区重叠）。

```html
<!-- 内底板：flex 容器 -->
<div class="ui-dialog-inner-panel-v2"
     style="margin-bottom:18px; display:flex; flex-direction:column; gap:8px; overflow:hidden;">

  <!-- 固定区：Banner、提示栏等 -->
  <div style="flex-shrink:0; height:82px;">...</div>

  <!-- 弹性区：列表/格子/属性卡片；grid布局需加 grid-template-rows:repeat(N,1fr) -->
  <div style="flex:1; min-height:0; overflow-y:auto;">...</div>

  <!-- 操作栏：最后一个 flex 子项，禁止 position:absolute -->
  <div style="flex-shrink:0; height:48px;">...</div>

</div>
```

> `min-height:0` 必须写——flex 子项默认 `min-height:auto`，不写会阻止收缩导致溢出。

---

> ✅ 检验标准：弹窗内容区从顶部延伸至操作栏，无空白；操作栏与弹性区无重叠  
> ⛔ 禁止用 `position:absolute` 定位操作栏（会压在弹性区上）  
> ⛔ 禁止用固定 `height` 或 `height:100%` 包裹主内容（会导致百分比高度失效或重叠）

---

### Step 4.8：输出前 Checkpoint（必须逐项确认）

输出 HTML 前，逐项自检：

- [ ] 已确认弹窗使用 `ui-dialog-v2` 预设尺寸 Class，**未使用 `ui-dialog-v1`**
- [ ] `ui-dialog-inner-panel-v2` 已加内联样式 `margin-bottom:18px; display:flex; flex-direction:column; gap:8px; overflow:hidden`
- [ ] 弹性区已设 `flex:1; min-height:0`；固定子项已设 `flex-shrink:0`；操作栏为最后 flex 子项（**非绝对定位**），弹窗无空白无重叠（**参照 Step 4.7**）
- [ ] 自定义弹窗尺寸已在 `<style>` 块内声明新 Class，未直接覆写已有预设 Class
- [ ] flex 行内的功能性按钮（「补充」等）已加 `white-space:nowrap; flex-shrink:0`
- [ ] 双列布局已计算左右列高度，模型框高度经公式确认与右列对齐
- [ ] 消耗/拥有类大数字输入框已设 `text-align: left`；属性值输入框已设 `text-align: center`
- [ ] `content-area` 的 `gap` 在 `8px–12px` 范围内，未超过 `16px`
- [ ] 存在多字数属性名时，使用 CSS `justify` 方案对齐，未使用 `&emsp;` 手动空格
- [ ] 同一控件组的加减按钮已统一为相同颜色 token
- [ ] 已确认每个 `ui-*` Class 在 `styles.css` 中实际存在
- [ ] 未硬编码任何颜色值，全部通过 CSS 变量引用
- [ ] 关闭按钮（`ui-dialog-close-btn-x`）叉号使用 CSS 伪元素，**未使用图片/SVG**
- [ ] 问号按钮（`ui-dialog-help-btn`）默认**不添加**（无需求说明时）
- [ ] `ui-btn-primary` 和 `ui-tab-btn-primary` 的装饰纹伪元素**未被删除**
- [ ] 道具框/头像框尺寸**未被修改**
- [ ] 纵向页签定位 `right: -46px` **未被手动调整**
- [ ] 图片路径使用 `resource/xxx.png` 相对路径
- [ ] CSS 引用：`<link rel="stylesheet" href="styles.css">`
- [ ] 字体族：`'DFPYuanW7-GB', sans-serif`（全文一致，华康圆体W7）

以上未全部确认，**不得输出 HTML**。

---

### Step 5：输出为 HTML

**输出路径**：`F:\AI Agent\skill\PC转HT-1.2\output\界面名称.html`

**自动打开规则**：
- ✅ **首次生成** HTML 文件后，立即执行以下命令在浏览器中打开预览：
  ```
  start "" "F:\AI Agent\skill\PC转HT-1.2\output\界面名称.html"
  ```
- ⛔ **后续修改**（对同一文件的调整/迭代）**不重复执行打开命令**，避免每次修改都弹出新标签页。浏览器刷新页面即可看到最新效果。

**起点模板**：复制 `template.html` 作为起点，修改标题和弹窗尺寸 Class 即可。

---

### Step 6：新组件发现

完成设计任务后，若识别到 PC 截图中存在当前组件库未收录的 UI 模式，询问用户：
> "发现新 UI 模式：[模式描述]，是否需要将其作为新组件补充到 styles.css 和框架清单中？"

---

## 目录结构

```
skill/PC转HT-1.2/
├── SKILL.md                    ← 本文件（Skill 入口）
├── styles.css                  ← 互通组件库核心样式（唯一 CSS 来源）
├── 互通组件库框架清单.md         ← 组件规格与限制规则
├── 组件速查.md                  ← 组件规格速查 + CSS Class 完整速查表
├── template.html               ← HTML 起点模板（直接复制使用）
├── 修改.md                     ← 版本修改记录
└── output/                     ← 成品 HTML 输出目录（F:\AI Agent\skill\PC转HT-1.2\output\）
    └── 界面名称.html
```

**路径约定**（HTML 在 `output/`）：
- CSS：`../styles.css`
- 图片资源：`../resource/xxx.png`

---

## Best Practices

1. **弹窗尺寸优先** — 先确定弹窗 Class，再布局内部内容；预设不满足时在 `<style>` 新建 `.ui-dialog-v2-WxH`，禁止覆写已有预设
2. **颜色变量强制** — 所有颜色通过 `var(--ui-*)` 引用，禁止硬编码色值
3. **尺寸锁定控件不可拉伸** — `ui-item`（56px）、`ui-avatar`（各预设尺寸）禁止改动
4. **页签状态语义固定** — `light`=未选中 / `dark`=选中 / `gray`=禁用，不可混用
5. **纵向页签通过 JS 切换 active** — 禁止在 HTML 中硬编码多个 active
6. **内联 SVG 优先** — 所有矢量图标（叉号/对号/加减等）使用内联 SVG
7. **仅 HTML 输出** — 交付物为完整可运行 HTML，不输出独立 CSS
8. **移动端以弹窗为核心** — 不做页面级布局，以弹窗为最大内容容器
9. **背景底板固定规则** — `body` 背景纯黑 `#000000`；游戏底板固定 960×540px `.game-canvas`，引用 `resource/HTBG.jpg` 设为 `960px 540px no-repeat`；禁止 `cover` 铺满视口
10. **属性标签等宽对齐** — 字数不等的属性名用 CSS `width + text-align:justify; text-align-last:justify`，禁止 `&emsp;` 手动垫字
11. **信息组间距** — `gap` 默认 `8px`，最大 `12px`，禁止超过 `16px`
12. **加减按钮同色** — 同一控件组加减按钮必须用相同颜色 token
13. **flex 行内按钮防纵向溢出** — 属性行末尾功能按钮必须加 `white-space:nowrap; flex-shrink:0`
14. **双列高度对齐** — 模型框高度 = 右列总高 − 左侧其他固定元素之和（参照 Step 4.5）
15. **输入框数值对齐方向** — 属性值用 `text-align:center`；消耗/拥有量大数字用 `text-align:left`
