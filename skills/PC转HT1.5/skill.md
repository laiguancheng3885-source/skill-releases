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
- 用户要求做新的互通端 UI 弹窗，但没有提供截图（从 Step 0 开始功能理解）

---

## 🗂️ 核心资源索引（使用前必查）

> ⛔ **强制规则**：遇到任何组件/颜色/尺寸问题，必须先查此索引，再参照规范输出。**禁止凭记忆直接写组件 HTML。**

| 需要的内容 | 查阅位置 |
|---|---|
| 所有 `ui-*` Class 实际样式 | `styles.css` |
| **布局类型（13 种预设 + 选型速查）** | **`ht-layout.css`** |
| 组件清单与限制规则 | `互通组件库框架清单.md` |
| **SVG 图标使用规范（Feather → 自绘 → 统一风格）** | **本文件 → §「图标使用规范」章节** |
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
| **已内置属性速查（禁止重复 inline）** | **`组件速查.md` → 五、已内置属性速查** |

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

- ⛔ 禁止修改 `ui-dialog-v1` 的 752×500px 固定尺寸；该弹窗在移动端**按需选用**，当 v2 预设最大尺寸（656×476 / 730×398）仍无法容纳内容时优先升级到 v1，禁止靠拉伸 v2 弹窗解决
- ⛔ 禁止删除 `ui-btn-primary` / `ui-tab-btn-primary` 的两侧装饰纹伪元素
- ⛔ 禁止硬编码颜色值，必须通过 CSS 变量（`var(--ui-*)`）引用
- ⛔ 禁止使用图片替代矢量图标（叉号/问号/对号等必须用内联 SVG 或 CSS 伪元素）
- ⛔ `ui-dialog-inner-panel-v2` 的左/右/上/下边距（`6px 23px 18px 22px`）已固化于 CSS，**HTML 无需再内联覆盖 `margin-bottom`**
- ⛔ 禁止使用 `ui-` 以外的自定义 Class 前缀
- ⛔ 禁止用自定义 `background`（如 `rgba(0,0,0,0.35)`）替代 `ui-area-bg` 组件；任何需要衬底的区域必须使用 `ui-area-bg` class，确保左右/多区域颜色一致
- ⛔ 禁止从零手写 HTML 骨架，**必须从 `template.html` 复制**；以下三项固定值手写必错：
  1. `<meta name="viewport" content="width=960, initial-scale=1.0">` — 禁止写成 `width=device-width`
  2. `.game-canvas` 背景必须是 `url('../resource/HTBG.jpg') 0 0 / 960px 540px no-repeat`，画布固定 **960×540px**，禁止 `cover` / `contain` / 自定义渐变背景替代
  3. 关闭按钮叉号固定参数：`stroke="#29408D" stroke-width="5.5" stroke-linecap="round"`，禁止更改颜色或笔触宽度

---

## Instructions（执行步骤）

> **任务类型判断（开始前先确认）**：
> - **提供了 PC 截图 或 HTML 文件**：从 Step 0 开始，走完整流程
> - **只做局部调整 / 新增单个组件**：直接跳到 Step 4

---

### Step 0：功能理解（生成前必做）

> **目的**：先理解业务功能和交互逻辑，再动手。防止"结构对、功能错"。

**输入判断**：

| 输入类型 | 理解方式 |
|---|---|
| PC 截图 | 从截图推断功能 |
| HTML 文件 | 读取代码 + 截图（如有） |
| 无截图 / 无 HTML | 直接向用户询问功能需求 |

**输出物 — 功能理解摘要**：

1. **界面定位**（一句话说清干什么的）
2. **功能模块拆解**（每个区域的业务含义）
3. **交互逻辑**（关键操作 → 预期结果）

输出摘要后：
- **有疑点** → 仅列出关键疑问，等用户解答后继续
- **无疑点** → 直接进入 Step 1，不必等确认

> ⛔ 禁止列模板化的"无疑点"声明、禁止为了问而问。只在真正影响实现的地方发问。

---

### Step 1：截图解析 & 骨架确认（提供截图时必做）

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
☐ ui-vtab-btn（纵向页签）⚠️ 定位锁定，固定高度 110px
☐ ui-avatar-56（头像框）⚠️ 尺寸锁定
☐ ui-item（道具框）⚠️ 尺寸锁定 56×56px
☐ ui-list-card（列表卡片）
☐ ui-btn-primary / ui-btn-secondary-sm（按钮）
☐ ui-input-box（输入框，唯一规格 30px）
☐ ui-num-display（数字展示框，仅展示，加减组合中间也用它）
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
   > "以上是移动端骨架方案，请确认或告知需要调整的部分。确认后我将继续执行 Step 2。"

---

### Step 2：选定弹窗尺寸

**决策链**：

1. 估算内容总高度（含两区/底部按钮栏）
2. ⛔ **滚动优先原则**：若内容总高超过最大 v2 预设高度（498px），**禁止直接自定义更大弹窗**。必须先将可滚动区域（列表/网格/属性区）用 `ui-flex-region` 包裹，使其弹性吸收并滚动，重新计算「固定区高度」后再选预设
3. 从下列 v2 预设挑最贴合的；若固定区高度仍装不下 → 升级到一级弹窗 `ui-dialog-v1`（752×500px，最大）
4. 仅当 v1 仍装不下（极端情况：固定区本身超过 v1 可用高）→ 在 `<style>` 块内声明新的 v2 自定义 Class（禁止拉伸已有预设）

> **核心逻辑**：弹窗高度由「固定区」决定，不由「全部内容」决定。可滚动内容不参与弹窗选型——它们在 `ui-flex-region` 内滚动。

**二级弹窗预设（首选，按需要从小到大选）**：

| Class | 尺寸 | 适用场景 |
|---|---|---|
| `ui-dialog-v2-446x276` | 446×276px | 轻量确认/提示弹窗 |
| `ui-dialog-v2-378x360` | 378×360px | 方形布局 |
| `ui-dialog-v2-580x368` | 580×368px | 横向为主，属性展示 |
| `ui-dialog-v2-730x398` | 730×398px | 宽屏列表展示 |
| `ui-dialog-v2-520x438` | 520×438px | 中等内容，单列或双栏 |
| `ui-dialog-v2-322x498` | 322×498px | 高窄型，纵向滚动列表 |
| `ui-dialog-v2-362x488` | 362×488px | 高窄，含纵向页签 |
| `ui-dialog-v2-656x476` | 656×476px | 内容较多，有列表+属性双栏 |

**一级弹窗（v2 装不下时使用）**：

| Class | 尺寸 | 适用场景 |
|---|---|---|
| `ui-dialog-v1` | 752×500px（固定） | v2 最大预设仍装不下；标题栏用 `ui-dialog-header` + `ui-dialog-title-bg`；关闭按钮用 `ui-dialog-close-btn-v1`（异形）；纵向页签同 v2 |

> 若 v1 仍不够，**必须在 HTML 的 `<style>` 块内声明新 v2 Class**，不可直接拉伸已有预设：
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

### Step 3：验证颜色与字体

**颜色管理规则**（不可硬编码）：

| 用途 | CSS 变量 | 参考色值 |
|---|---|---|
| 弹窗背景 | `var(--ui-dialog-bg)` | `#c1d1fe` |
| 弹窗边框 | `var(--ui-dialog-border)` | `#9baee4` |
| 内底板背景 | `var(--ui-dialog-inner-bg)` | `#a5b6e7` |
| 输入框背景 | `var(--ui-input-bg)` | `#c1c6dd` |

**字色 Class 用法（CSS 已固化作用域，无需判断背景色）**：

> ✅ **推荐用法**：用语义中性 class，**当前所在容器自动选对字色族**。

```
ui-text-title      标题
ui-text-body       正文（最常用）
ui-text-secondary  次要说明
ui-text-hint-dark  深一点的提示
ui-text-hint       弱提示
ui-text-gray       置灰
ui-text-reminder   提醒/公告（绿）
ui-text-emphasis   强调（蓝/黄）
ui-text-special    特殊高亮（红）
```

**作用域规则（CSS 自动处理，仅作了解）**：

| 容器 | 字色族 |
|---|---|
| `.ui-dialog-v2` / `.ui-dialog-inner-panel-v2` / `.ui-area-bg` / `.ui-list-card` | light-* 族（深色字） |
| `.ui-dialog-header-v2` / `.ui-list-wrap` / `.ui-list-header` | dark-* 族（亮色字） |

> 例：`<span class="ui-text-reminder">通知</span>` 放在 `ui-dialog-inner-panel-v2`（浅底）→ 自动取绿 `#008100`；同样代码放在 `ui-dialog-header-v2`（深底）→ 自动取荧光绿 `#6DF33A`。

**通用禁令**：
- ⛔ 禁止用内联 `style="color:..."` 或 `style="color:var(--ui-text-*)"` 代替字色 class（无 `!important` 会被父级覆盖）
- ⛔ 字色 class 禁止跨层嵌套（同一行多色用平级兄弟 `<span>`，详见 Best Practice #20）

**遗留兼容**：旧 class `ui-text-light-*` / `ui-text-dark-*` 仍可用，但**不推荐**新代码使用——容易写错族（如 `light-` 用在深底上）。已生成的 output 文件无需改动。

**字体规则**（强制）：
- 字体族：`'DFPYuanW7-GB', sans-serif`（华康圆体W7，通过 @font-face 从 resource/华康圆体W7.TTC 加载）
- 可用字号：`22px / 20px / 18px / 16px / 14px`（14px 仅在极限空间使用）
- 多行文本行高：固定值（`24px / 22px / 20px`），**不可改为百分比**

---

### Step 4：应用组件规格

> ⛔ **每个组件使用前必须完成双核对**：
> 1. 查 `互通组件库框架清单.md` 对应章节 → 确认规格和限制规则
> 2. 查 `styles.css` → 确认实际 Class 名和样式值

> 📋 **组件规格与 Class 速查** → 见 `组件速查.md`（规格表 + 完整 Class 速查表二合一）

---

### Step 4.1：按钮尺寸选型决策链

> 选错按钮等级是高频错误。任何按钮在落笔前**必须走完此决策链**，禁止凭直觉。

**决策树**：

```
该按钮是不是「弹窗的最终主操作」（确认 / 提交 / 报名 / 购买）？
├─ 是 → ui-btn-primary（150×40，红/蓝/灰，带两侧装饰纹）  ※每弹窗 ≤ 1 个
└─ 否 → 是不是「弹窗底部固定的次操作」（取消 / 关闭 / 切换模式）？
    ├─ 是 → ui-btn-secondary-lg（150×40，蓝，无装饰纹）
    └─ 否 → 是不是「重复出现在列表/卡片每行内」、或「内容区辅助操作」？
        ├─ 是 → ui-btn-secondary-sm（100×30）        ← 高频列表内按钮首选
        └─ 否 → 是不是「单一图标功能键」（搜索/编辑/分享/加减）？
            └─ 是 → ui-btn-square-sm-{38|32|28}（必含 SVG 图标）
```

**场景速查表**：

| 场景 | 选型 | 反例（禁用） |
|---|---|---|
| 弹窗底部「确认报名」「立即购买」 | `ui-btn-primary ui-btn-red` | 不要在 `ui-list-card` 内用 |
| 弹窗底部「取消」「关闭」 | `ui-btn-secondary-lg ui-btn-blue` | 不要套装饰纹 primary |
| **关卡卡片/排行榜每行右侧「挑战」「领取」「查看」** | **`ui-btn-secondary-sm`**（100×30） | ⛔ 用 `ui-btn-primary` 会撑爆卡片 + 视觉过强抢主操作 |
| 列表行末追加「补充」「强化」「升级」 | `ui-btn-secondary-sm`（窄列可缩到 54px 宽） | ⛔ 用 primary |
| 标题栏右侧「奖励预览」「规则说明」 | `ui-btn-secondary-sm` | — |
| 数量加减、模型预览旁的搜索/编辑/分享 | `ui-btn-square-sm-38` + 内联 SVG | ⛔ 用文字字符 `+/−/×` 替代 |
| **弹窗底部两个并列按钮（确认+分享等）** | **统一 `ui-btn-secondary-lg`，红蓝色区分** | ⛔ 一个 primary 一个 secondary-lg（层级/宽度/装饰纹不对等） |
| **内容区多个并列操作（清空+补齐+炼化等）** | **统一 `ui-btn-secondary-sm`，红蓝色区分主次** | ⛔ 混用 primary 和 secondary-sm（同行层级不一致） |

**判别口诀**：

- **"主操作"只有一个**：弹窗最重要的那一个动作 → primary；其余降级
- **"并列按钮必同级"**：**同一行内的所有并列按钮**（无论是底部操作栏、内容区操作行、还是卡片内按钮组）必须使用**同一组件层级和宽度**，仅通过 `ui-btn-red` / `ui-btn-blue` 颜色区分语义；⛔ 禁止同行内混用不同层级（如一个 primary 搭配 secondary-sm，视觉不对等 + 宽度/装饰纹不一致）。示例：「清空」+「一键补齐」+「炼化」三并排 → 统一 `ui-btn-secondary-sm`，炼化用 `ui-btn-red` 区分
- **"重复出现的"必降级**：N 张关卡卡片 = N 个挑战按钮 = 重复出现 = 不能用 primary
- **"卡片/格子内"必降级**：被 `ui-list-card` / `ui-group-card` 包裹的按钮一律 ≤ secondary-sm
- **图标按钮**：宁可降级到方形按钮 + SVG，不要用文字+小框假装

**反例修正**：

```html
<!-- ⛔ 错：5 张关卡卡片每张都塞 primary，视觉过载 + 撑爆卡片 -->
<div class="ui-list-card">
  ...
  <div class="ui-btn ui-btn-primary ui-btn-red" style="width:64px; height:30px;">挑战</div>
</div>

<!-- ✅ 对：列表内重复出现 → secondary-sm -->
<div class="ui-list-card">
  ...
  <div class="ui-btn ui-btn-secondary-sm ui-btn-red">挑战</div>
</div>
```

> ⛔ 禁止用内联 `width/height` 把 `ui-btn-primary` 压成小尺寸来"将就"——失去装饰纹比例 + 文字溢出风险，应直接换组件。

**宽度自定义规则（仅 secondary）**：

- `ui-btn-secondary-sm`（默认 100px）和 `ui-btn-secondary-lg`（默认 150px）的 **width 允许覆盖**（CSS 已固化 `box-sizing:border-box`，width 即总宽，padding 不外溢）
- 卡片/列表内按钮自适应：`<div class="ui-btn ui-btn-secondary-sm ui-btn-red" style="width:100%;">挑战</div>`
- `ui-btn-primary` 与 `ui-btn-square-sm-*` 的 width **禁止覆盖**（前者破坏装饰纹比例，后者破坏正方形）
- 高度永远禁止覆盖（CSS 全部 `!important`）

**已完成/已领取状态 → 禁止用按钮，必须用标签**：

> 「已领取」「已完成」「已结束」等**终态**表示操作已不可逆转，不应给用户可点击的暗示。

| 状态 | 选型 | 元素 |
|---|---|---|
| 可操作（领取/挑战/购买） | `ui-btn-secondary-sm ui-btn-red/blue` | `<div class="ui-btn ...">` |
| 不可操作（条件未满足） | `ui-btn-secondary-sm ui-btn-gray` | `<div class="ui-btn ...">` |
| **终态（已领取/已完成/已结束）** | **`ui-tag-done`**（或 `ui-tag ui-tag-green`） | **`<span class="ui-tag-done">已领取</span>`** |

- `ui-tag-done` 与 `ui-btn-secondary-sm` 同尺寸（100×30），可 inline 改 width
- 描边风格：透明背景 + 2px 彩色边框 + 同色文字 + 圆角 6px，无填充/阴影
- CSS 已内置 `pointer-events:none` + `cursor:default`，不可交互
- 多色标签用 `ui-tag` 基类 + 修饰符：`ui-tag-green`（绿/已完成）、`ui-tag-red`（红/已过期）、`ui-tag-gray`（灰/未开放）、`ui-tag-blue`（蓝/进行中）
- ⛔ 禁止用 `ui-btn-gray` 表示已完成（灰色按钮语义是"条件未满足，将来可操作"，不是终态）

---

### Step 4.2：输入框 vs 数字展示框选型决策链

> 高频错误：误用 `ui-input-box-light`（已废弃）作为"亮底输入框"，把单纯展示数字也包成 `<input>`。
> 正确认知：**输入框只有一种规格**；纯展示数字必须用独立组件 `ui-num-display`。

**决策链**（落笔前必走）：

```
该位置用户能否键入文字/数字？
├─ 能（搜索框、兑换数量、备注） → ui-input-box（唯一规格，30px，<input>）
│
└─ 不能，仅展示数值 → ui-num-display（26px，<span>，常规字色 #292F60）
   │
   └─ 子情形：加减组合 [−][?][+]
       └─ 中间永远是 ui-num-display（不再用 ui-input-box）
          → 通过 JS 修改 textContent 而不是 .value
```

| 场景 | 控件 | 元素 | 是否可输入 |
|------|------|------|-----------|
| 搜索昵称/ID | `ui-input-box` | `<input>` | ✅ |
| 自由输入兑换数量 | `ui-input-box` | `<input>` | ✅ |
| 当前积分/金额展示 | `ui-num-display` | `<span>` | ❌ |
| 属性数值（伤害/防御） | `ui-num-display` | `<span>` | ❌ |
| 加减组合中间数字 | `ui-num-display` | `<span>` | ❌（点 +/− 修改） |

**禁止行为**：
- ❌ 用 `<input readonly>` 作纯展示（语义错误，焦点环、tab-stop 都是副作用）
- ❌ 引用 `ui-input-box-light`（已从 CSS 移除，类名失效）
- ❌ 加减组合中间放 `ui-input-box`（违反"中间用数字框"规则）
- ❌ 给 `ui-num-display` 做亮/暗底两个版本（不区分，统一暗蓝底）

**JS 适配**（加减组合）：
```js
// ❌ 错（旧 ui-input-box 写法）
input.value = qty;
// ✅ 对（ui-num-display 写法）
displayEl.textContent = qty;
```

---

### Step 4.3：道具网格展示选型决策链

> 图鉴、背包、仓库、收藏册等界面高频出现「N×M 道具网格」，选错容器是常见问题。

**决策链**（落笔前必走）：

```
该网格中的每个道具格子，是否需要在图标下方显示名称/数量/品质等文字？
├─ 需要 → ✅ 用封装组件 ui-item-grid + ui-item-card（首选）
│   ├─ 选列数修饰符：ui-item-grid--cols-3 / --cols-4 / --cols-5 / --cols-7
│   ├─ 每个卡片：ui-item-card > ui-item + ui-item-card__name
│   ├─ 需要选中 → ui-item-card 加 selected class + 内嵌 ui-selection-box
│   └─ 滚动/衬底/防缩 已全部内置，⛔ 禁止再手写 flex-direction/overflow/flex-shrink
│
└─ 不需要（纯图标网格，如打造记录、背包格子） → 直接用 ui-item 裸框
    ├─ 需要角标数量 → ui-item 内加 ui-badge-number
    ├─ 需要红点提示 → ui-item 内加 ui-badge-dot
    └─ 需要选中 → ui-selection-box 放在 ui-item 内
```

**⛔ 禁止的旧写法**（已归档为反模式）：

| 反模式 | 问题 | 正确替代 |
|---|---|---|
| `ui-flex-region` + `display:grid` 放同一元素 | grid 行高被压缩至 0 | 用 `ui-item-grid`（已内置分层） |
| `.item-cell.ui-list-card { flex-direction:column }` 覆盖 | 每次 ad-hoc 覆盖，参数不一致 | 用 `ui-item-card`（原生竖排） |
| `ui-item` 在 flex-column 中未加 `flex-shrink:0` | 道具框 56×56 被压扁 | `ui-item-card > .ui-item` 已内置 |
| grid 列数/间距/padding 每次手写 inline | 不可复用，易出错 | 用 `ui-item-grid--cols-N` 修饰符 |
| 滚动样式每次手写 5-7 行 | 重复代码 | `ui-item-grid` 已内置滚动 |

**标准用法（零自定义 CSS）**：

```html
<!-- ✅ 正确：封装组件，含衬底 + 4列网格 + 滚动 + 防缩 -->
<div class="ui-item-grid ui-item-grid--cols-4">
  <!-- 选中卡片 -->
  <div class="ui-item-card selected">
    <div class="ui-item">
      <div class="ui-item-inner" style="background: url('resource/道具图标.png') center/contain no-repeat;"></div>
    </div>
    <span class="ui-item-card__name ui-text-body">艾草香囊</span>
    <div class="ui-selection-box"></div>
  </div>
  <!-- 普通卡片 -->
  <div class="ui-item-card">
    <div class="ui-item">
      <div class="ui-item-inner" style="background: url('resource/道具图标.png') center/contain no-repeat;"></div>
    </div>
    <span class="ui-item-card__name ui-text-body">艾草香囊</span>
  </div>
  <!-- 未收集（灰化） -->
  <div class="ui-item-card">
    <div class="ui-item">
      <div class="ui-item-inner" style="background: url('resource/道具图标.png') center/contain no-repeat; opacity:0.4;"></div>
    </div>
    <span class="ui-item-card__name ui-text-gray">艾草香囊</span>
  </div>
</div>
```

---

### Step 5：移动端布局规则

> ⚠️ **布局类优先**：`ht-layout.css` 提供 13 种预设布局（双栏/三段式/列表/排行榜等），**必须优先选用**，禁止手拼 `display:flex; flex-direction:column/row` 等基础布局。选型速查：

| PC 端特征 | 推荐布局类 |
|---|---|
| 左窄列表 + 右详情 | `ht-layout-two-col-1` |
| 双列各自含标题栏 | `ht-layout-two-col-2` |
| 双列等分 | `ht-layout-two-col-3` |
| 标准三段式（头 + 内容 + 尾） | `ht-layout-header-main-footer` |
| 三段式（Header 为 Tab 栏） | `ht-layout-header-main-footer-1` |
| 成员/道具 + 三列网格 | `ht-layout-team` |
| 顶部操作栏 + 内容 | `ht-layout-top-action` |
| 内容 + 底部栏 | `ht-layout-top-bottom` |
| 底部操作栏 + 三列内容 | `ht-layout-bottom-action` |
| 横向多分区 | `ht-layout-mid-section` |
| 弹窗内列表（带筛选行） | `ht-layout-list-dialog` |
| 排行榜（含我的排名固定底栏） | `ht-layout-list-with-rank` |
| PC 侧边导航 → 折叠/抽屉 | `ht-layout-sider` |

> 仅当 13 种布局均不适用时，才可手写布局 flex，并在 HTML 注释中说明原因。

### Step 5.0：宽度预算公式（双栏/网格布局必做）

> ⛔ **强制检查**：任何涉及双栏并排或网格道具排列的界面，在落笔写 HTML 前**必须完成以下数值验算**，禁止凭直觉估算宽度。

**公式**：

```
① 可用宽 = 弹窗宽 − 2 × border(5px) − margin-left(22px) − margin-right(23px)

   V1(752px): 可用宽 = 752 − 10 − 22 − 23 = 697px
   V2 预设：用该预设 width 代入同公式

② 双栏场景：左栏宽 + gap + 右栏宽 = 可用宽
   → 必须声明两栏宽度并验算：左+gap+右 ≤ 可用宽

③ 网格场景：列数 × 单元宽 + (列数−1) × gap + 侧栏/标签宽 ≤ 可用宽
   → ui-item(56px) + 卡片 padding(左2+右2) = 60px/单元
   → 7列示例：7×60 + 6×2(gap) + 28(标签) = 460px → 左栏 460px

④ 高度同理：可用高 = 弹窗高 − 标题栏 − margin-top − margin-bottom
   V1(500px): 可用高 = 500 − 36 − 6 − 66 = 392px（有操作栏）
   V1(500px): 可用高 = 500 − 36 − 6 − 18 = 440px（无操作栏，用 --no-action）
```

**输出要求**：在 Step 1 骨架确认时，必须输出以下验算结果：

```
宽度预算：可用宽 = XXXpx
  左栏 = XXXpx（含：标签Xpx + N列×Xpx + gap）
  右栏 = XXXpx
  验证：左 + gap + 右 = XXX ≤ 可用宽 ✅/⛔

高度预算：可用高 = XXXpx
  内容总高 = XXXpx
  验证：内容总高 ≤ 可用高 ✅/⛔
```

**核心原则**：

- 最小间距 `4px`，标准间距 `6–8px`，弹窗内边距遵循 `ui-dialog-inner-panel-v2` 的 `6px 23px 18px 22px`（CSS 默认，不必内联）
- **横向页签**：放入 `<div data-tab-group>` 容器内，CSS 已默认让所有页签 `flex:1 1 0` 自动等分，无需 JS 等宽，无需估算总宽
- 列表区域超过 4 项时添加 `overflow-y: auto` + `max-height` 并隐藏滚动条（或直接用 `.ui-flex-region` 工具类）：

```css
overflow-y: auto;
scrollbar-width: none;       /* Firefox */
-ms-overflow-style: none;    /* IE/Edge */
/* ::-webkit-scrollbar { display: none; } */
```

- **底部操作栏布局**：使用 `ui-action-bar` 组件作为内底板的最后一个子项，**禁止使用 `position: absolute`**（绝对定位会与弹性区内容重叠）。⛔ **内底板禁止加 `overflow:hidden`**（会裁切 `ui-tab-btn-primary` 等组件的装饰纹伪元素）；`overflow-x:hidden` 已内置于 `.ui-flex-region`，只加在弹性滚动子项上：
  ```html
  <!-- ui-dialog-inner-panel-v2 默认即 flex column + gap:8px + min-height:0 + margin-bottom:18px -->
  <div class="ui-dialog-inner-panel-v2">
    <!-- 固定区：banner、提示栏等（直接子项默认 flex-shrink:0） -->
    <div>...</div>
    <!-- 弹性区：列表/格子，工具类已含 flex:1 + min-height:0 + overflow -->
    <div class="ui-flex-region">...</div>
    <!-- 操作栏：ui-action-bar 已内置 flex-shrink:0 + 居中 + gap:12px -->
    <div class="ui-action-bar">
      <div class="ui-btn ui-btn-secondary-lg ui-btn-blue">取消</div>
      <div class="ui-btn ui-btn-primary ui-btn-red">确认</div>
    </div>
  </div>
  ```
- **信息组间距（`content-area` gap）**：`gap` 推荐值为 `8px`；若视觉需要呼吸感可设 `12px`，**禁止超过 16px**，避免内容总高超出可用区高度导致溢出
- **flex 行内按钮防文字纵向溢出**：属性行末尾追加功能性按钮（如「补充」「强化」）时，加 `ui-no-shrink` class（已内置 `white-space:nowrap; flex-shrink:0`），防止被压缩导致文字换行变纵向：
  ```html
  <div class="ui-btn ui-btn-secondary-sm ui-btn-blue ui-no-shrink"
       style="width:54px; padding:0;">补充</div>
  ```
- **输入框数值对齐方向**：使用 `.ui-stepper` 时默认居中；消耗/拥有类大数字加 `.ui-stepper--align-left` 切换为左对齐。⛔ 禁止一律右对齐。
- `ui-list-card` 宽度由父容器决定，高度由内容撑开，**不可固定高度**
- 图片资源路径统一使用 `resource/xxx.png`（相对路径）

**PC → 移动端 控件对照**：

| PC 常见控件 | 对应移动端 Class |
|---|---|
| 属性值行（标签+数值） | `ui-list-card` 内 flex 排列 + `ui-text-body` / `ui-text-emphasis`（语义中性 class） |
| 列表项（人物+文字） | `ui-list-card` 内 `ui-avatar` + 右侧 flex 文字块 |
| 数量输入 | `<span class="ui-stepper">` 复合组件，自动包含加减按钮 + 输入框（详见组件速查） |
| 操作按钮行 | 弹窗底部 `ui-btn ui-btn-primary ui-btn-red` 居中 |
| 道具格子 | `ui-item` + `ui-item-inner` + 可选 `ui-badge-number` |

---

### Step 5.5：双列布局高度对齐规范

使用**左侧装饰列（模型/头像/道具槽）+ 右侧信息列**的双栏布局时，必须使两列总高一致。

**对齐公式**（以右列为基准）：

```
模型框高度 = 右列总高 − 左列其他固定元素 − gap × n
```

> 详细公式推导与示例：见 `SKILL-EDGE.md` § 3。
> ⛔ 禁止目测估算。

---

### Step 5.6：属性标签对齐规范

当信息列表中存在**字数不等的属性名**（如「伤害」2字 vs「法术伤害」4字）时，使用预设 `.ui-attr-label-{2|3|4|5}ch` class，自动 CSS justify 等宽，无需自定义样式：

```html
<span class="ui-attr-label ui-attr-label-4ch">伤  害</span>
<span class="ui-attr-label ui-attr-label-4ch">法术伤害</span>
```

> ✅ 自动效果：N 字标签宽度统一为指定值，左右端对齐，右侧输入框整列对齐
> ⛔ 禁止 `&emsp;` / `&nbsp;` 手动垫空格

---

### Step 5.7：弹性区域填充规范

> **目的**：防止固定高度弹窗内部出现大块空白。

**机制**：`ui-dialog-inner-panel-v2` 已是 flex column 容器（CSS 默认）；弹性区只需加 `.ui-flex-region` 工具类，自动 `flex:1; min-height:0; overflow-y:auto; overflow-x:hidden` 并隐藏滚动条。

**触发条件**：弹窗使用 `ui-dialog-v2-XXXxXXX` 固定尺寸，且内容元素的高度之和 < 内底板可用高度。

**模板**：

```html
<div class="ui-dialog-inner-panel-v2">
  <!-- 固定区：Banner、提示栏 -->
  <div style="height:82px;">...</div>

  <!-- 弹性区：列表/格子/属性卡片 -->
  <div class="ui-flex-region">...</div>

  <!-- 操作栏：ui-action-bar 已内置 flex-shrink:0 + 居中 + gap -->
  <div class="ui-action-bar">...</div>
</div>
```

**弹性区类型**：列表 → 列表滚动容器；格子 → grid 容器；双栏 → 左右两列同时设 `flex:1`；纯文本 → 文本区。

**弹性区选型决策**（关键 — 决定"余白如何被吸收"）：

| 内部子项希望的表现 | 弹性区配置 | 余白落点 |
|---|---|---|
| 子项自然撑开（列表/格子可滚动） | `.ui-flex-region`（默认） | 滚动条吸收 |
| 子项**不希望被拉伸**，余白集中于"标题↔内容"之间 | `.ui-flex-region` + `display:flex; flex-direction:column; justify-content:space-between` | 顶部贴顶 / 底部贴底 / 中间 gap 吸收 |
| 子项**不希望被拉伸**，余白均匀分布上下 | `.ui-flex-region` + `display:flex; flex-direction:column; justify-content:center` | 上下对称 |
| 单一卡片/格子，整体居中 | `.ui-flex-region` + `display:flex; align-items:center; justify-content:center` | 四周环绕 |
| 仅希望"插入空隙"，两区都不变形 | 两固定区之间插 `<div style="flex:1; min-height:0;"></div>` | 占位 div 吸收 |

> ⚠️ **常见踩坑**：直接给装着卡片的容器加 `.ui-flex-region` 而内部 `flex-direction:column; justify-content:flex-start`（默认）→ 子项仍贴顶，余白全留底部 → **没解决问题**。必须配合 `space-between` / `center` 才能让余白真正消化。

**可用高度计算**：`弹窗高 − 标题栏(34px) − 上边距(6px) − 下边距(18px)`；8 个预设的速查表见 `SKILL-EDGE.md` § 10。

> ✅ 弹窗内容区从顶部延伸至操作栏，无空白无重叠
> ⛔ 禁止 `position:absolute` 定位操作栏；禁止 `height:100%` 包裹主内容

---

### Step 5.8：输出前 Checkpoint（核心 10 项）

> 大量历史踩坑已下沉到 CSS 默认行为，本节只保留**仍需 LLM 主动判断**的项；详细疑难排查见 `SKILL-EDGE.md`。

- [ ] **功能逻辑理解已完成**：Step 0 的功能理解摘要已输出（界面定位、模块拆解、交互逻辑），关键疑点（如有）已获用户解答
- [ ] 弹窗优先用 `ui-dialog-v2` 预设尺寸 class；当 v2 最大预设（656×476 / 730×398）仍装不下时，改用 `ui-dialog-v1`（752×500px，标题栏与关闭按钮使用 v1 专用类）
- [ ] ⚠️ 关闭按钮 `ui-dialog-close-btn` **必须**是 `ui-dialog-wrapper` 的**直接子元素**，放在 `ui-dialog-v2` **闭合标签之后**；⛔ **严禁放在 `ui-dialog-v2` 内部**（虽然 `ui-dialog-v2` 已改为 `overflow:visible`，但放入内部会使 `position:absolute; top:-4px; right:-4px` 相对于 header 而非 wrapper，导致定位偏移）；⛔ **严禁使用自定义 wrapper class**（必须使用 `ui-dialog-wrapper`，CSS 内置了关闭按钮的定位基准）
- [ ] 弹性区已用 `.ui-flex-region`；操作栏作为最后 flex 子项，**非绝对定位**
- [ ] 列表容器选对：表格→`ui-list-wrap`（透明子卡 + header）；卡片选择→`ui-list-stack`（保留卡片视觉 + 金色 `ui-selection-box` 选中态）
- [ ] 强相关展示元素已成组：列表行→`ui-list-card`；独立信息块→`ui-group-card`；段落/区块→`ui-area-bg`；⛔ 禁止裸放头像+名称+属性
- [ ] 汇总栏/操作栏**未放入** `ui-list-wrap` 内部
- [ ] 字色用语义中性 class（`ui-text-body` / `-reminder` 等），**未内联** `style="color:..."`
- [ ] 列宽优先使用 `ui-col-*` token，header 和 card 用同名 token
- [ ] 横向页签放在 `[data-tab-group]` 内，**未** inline `flex` 或 `width`
- [ ] 数量加减用 `<span class="ui-stepper">` 复合组件
- [ ] 双列布局（含模型框）已按公式计算左右列高度对齐
- [ ] 引用 `resource/` 图片前已 `list_files` 确认实际文件名
- [ ] 每个 `ui-*` class 在 `styles.css` 中实际存在；未硬编码颜色
- [ ] **无冗余 inline style**：对照 `组件速查.md` → 五、已内置属性速查，确认未重复写组件已内置的 `display:flex / gap / flex-shrink:0 / margin-bottom:18px / width:100%; min-width:0` 等；操作栏已用 `ui-action-bar`
- [ ] **底部留白自查**：心算「弹窗高 − 标题栏(34) − 上下边距(24) − ∑(子项高 + gap)」≤ **24px**；若 > 24px 必须三选一处置：
  1. 给某子区域加 `.ui-flex-region` 吸收剩余高度（首选；卡片不希望被拉伸时配合 `justify-content:space-between` 或 `align-items:center`）
  2. 在 HTML `<style>` 块声明更小尺寸的 `.ui-dialog-v2-WxH` 自定义 Class
  3. 在两个固定区之间插入 `<div style="flex:1; min-height:0;"></div>` 弹性占位
  ⛔ 禁止留下 > 24px 的肉眼可见空白带

未全部确认，**不得输出 HTML**。

---

### Step 6：输出为 HTML

**输出路径**：`F:\AI Agent\skill\PC转HT-1.2\output\界面名称.html`

**自动打开规则**：
- ✅ **首次生成** HTML 文件后，立即执行以下命令在浏览器中打开预览：
  ```
  start "" "F:\AI Agent\skill\PC转HT-1.2\output\界面名称.html"
  ```
- ⛔ **后续修改**（对同一文件的调整/迭代）**不重复执行打开命令**，避免每次修改都弹出新标签页。浏览器刷新页面即可看到最新效果。

**起点模板**：**必须复制 `template.html` 作为起点**，修改标题和弹窗尺寸 Class 即可。⛔ 禁止从零手写 HTML 骨架。

> **`template.html` 包含以下固定内容，手写必错，必须直接复制：**
> - `<meta name="viewport" content="width=960, initial-scale=1.0">` — ⛔ 禁止写成 `width=device-width`
> - `body { background: #000000; }` — 画布外纯黑
> - `.game-canvas { width:960px; height:540px; background: url('../resource/HTBG.jpg') 0 0 / 960px 540px no-repeat; }` — 固定尺寸，禁止 cover/contain
> - `<div class="ui-dialog-wrapper">` — 弹窗+关闭按钮共同的定位父容器
> - 梯形 SVG 路径（固定值，禁止手写）：`d="M 0 0 L 232 0 L 226.184 16.868 C 223.680 24.128 216.846 29 209.167 29 L 22.833 29 C 15.154 29 8.320 24.128 5.816 16.868 L 0 0 Z"`
> - 关闭叉号（固定值）：`stroke="#29408D" stroke-width="5.5" stroke-linecap="round"`
> - 关闭按钮位置：`ui-dialog-wrapper` 的**直接子元素**，在 `ui-dialog-v2` 闭合标签**之后**

**完整弹窗骨架**（复制后仅需改标题和弹窗尺寸 Class）：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=960, initial-scale=1.0">
  <title>界面名称</title>
  <link rel="stylesheet" href="../styles.css">
  <link rel="stylesheet" href="../ht-layout.css">
  <style>
    body {
      margin: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      background: #000000;
      font-family: 'DFPYuanW7-GB', sans-serif;
    }
    .game-canvas {
      position: relative;
      width: 960px;
      height: 540px;
      flex-shrink: 0;
      background: url('../resource/HTBG.jpg') 0 0 / 960px 540px no-repeat;
      background-color: #3d5080;
      display: flex;
      justify-content: center;
      align-items: center;
      overflow: visible;
    }
  </style>
</head>
<body>
  <div class="game-canvas">
    <div class="ui-dialog-wrapper">

      <div class="ui-dialog-v2 ui-dialog-v2-656x476"><!-- ← 改尺寸 Class -->

        <div class="ui-dialog-header-v2">
          <div class="ui-dialog-title-bg-v2">
            <svg class="ui-title-shape-svg-v2" viewBox="0 0 232 29" xmlns="http://www.w3.org/2000/svg">
              <path d="M 0 0 L 232 0 L 226.184 16.868 C 223.680 24.128 216.846 29 209.167 29 L 22.833 29 C 15.154 29 8.320 24.128 5.816 16.868 L 0 0 Z" fill="#A0B4E8"/>
            </svg>
            <span class="ui-dialog-title-text-v2">弹窗标题</span><!-- ← 改标题 -->
          </div>
          <!-- 问号按钮（需要时取消注释）：<div class="ui-dialog-help-btn"></div> -->
        </div>

        <div class="ui-dialog-inner-panel-v2">
          <!-- 内容区写这里 -->
        </div>

      </div><!-- /.ui-dialog-v2 -->

      <!-- ⚠️ 关闭按钮：必须是 ui-dialog-wrapper 的直接子元素，在 ui-dialog-v2 之后 -->
      <div class="ui-dialog-close-btn">
        <div class="ui-dialog-close-btn-x">
          <svg viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
            <line x1="3" y1="3" x2="17" y2="17" stroke="#29408D" stroke-width="5.5" stroke-linecap="round"/>
            <line x1="17" y1="3" x2="3" y2="17" stroke="#29408D" stroke-width="5.5" stroke-linecap="round"/>
          </svg>
        </div>
      </div>

    </div><!-- /.ui-dialog-wrapper -->
  </div><!-- /.game-canvas -->
</body>
</html>
```

---

### Step 7：输出后视觉审查（首次生成必做）

> HTML 输出并在浏览器打开后，**立即执行一次视觉审查**，不等用户反馈。

**审查流程**：

1. **留白检测**（最高优先级）：
   - 对照浏览器截图或心算：`弹窗高 − 标题栏(34) − 上下边距(24) − Σ(子项高 + gap)` = 剩余留白
   - 若剩余留白 **> 24px**（即底部或中部可见明显空白带）→ **必须修复**，不可交付

2. **留白修复决策链**（按优先级尝试）：
   ```
   留白 > 24px？
   ├─ 能换更小的 v2 预设弹窗？
   │   └─ 是 → 换小一号预设 Class，重新验证内容是否溢出
   ├─ 预设尺寸无合适的？
   │   └─ 在 <style> 中声明自定义 .ui-dialog-v2-WxH（H = 内容实际所需高度 + 标题栏 + 边距 + 余量 ≤ 24px）
   └─ 修改弹窗尺寸后，必须重新验证：
       ├─ 内容无溢出、无截断
       ├─ 双栏布局左右列高度仍对齐
       ├─ 底部按钮栏未被推出弹窗
       └─ 关闭按钮位置正常
   ```

3. **其他视觉复核**（留白修复完成后快速扫一遍）：
   - 左右双栏颜色一致（都用 `ui-area-bg` 或都不用）
   - 按钮层级/宽度对等（并列按钮同级同宽）
   - 文字未溢出容器
   - 图片加载正常
   - 关闭按钮可点击区域未被遮挡

4. **修复后不重复打开浏览器**，直接编辑文件 → 用户刷新即可

> ⛔ **禁止跳过本步**：即使 Step 5.8 Checkpoint 通过，实际渲染仍可能因 gap/padding 累积偏差导致留白。本步是最终防线。

---

### Step 8：新组件发现

完成设计任务后，若识别到 PC 截图中存在当前组件库未收录的 UI 模式，询问用户：
> "发现新 UI 模式：[模式描述]，是否需要将其作为新组件补充到 styles.css 和框架清单中？"

---

## 目录结构

```
skill/PC转HT-1.4/
├── SKILL.md                    ← 本文件（Skill 入口）
├── SKILL-EDGE.md               ← 疑难陷阱速查
├── styles.css                  ← 互通组件库核心样式
├── ht-layout.css               ← 布局类型预设（13 种）
├── 组件速查.md                  ← 组件规格速查 + CSS Class 速查 + 已内置属性速查
├── template.html               ← HTML 起点模板（直接复制使用）
├── resource/                   ← 图片资源（头像、道具、模型、字体等）
└── output/                     ← 成品 HTML 输出目录
    └── 界面名称.html
```

**路径约定**（HTML 在 `output/`）：
- CSS 组件库：`../styles.css`
- CSS 布局类：`../ht-layout.css`
- 图片资源：`../resource/xxx.png`

---

## Best Practices

> 仅保留 AI 需**主动判断**的设计决策规则。CSS 已固化的实现细节不在此列——查 `组件速查.md` → 五、已内置属性速查。

### 设计决策类（每次生成必看）

1. **功能逻辑理解先行** — 任何界面生成前（截图/HTML输入），必须先输出功能理解摘要。无疑点可直接进 Step 1，有关键疑点才发问。⛔ 禁止跳过 Step 0 直接画骨架
2. **弹窗尺寸优先** — 先确定弹窗 Class，再布局内部内容；预设不满足时在 `<style>` 新建 `.ui-dialog-v2-WxH`，禁止覆写已有预设
3. **布局类优先** — 必须优先从 `ht-layout.css` 13 种预设布局中选用（见 Step 5 速查表），禁止手拼基础 flex 布局
4. **移动端以弹窗为核心** — 不做页面级布局，以弹窗为最大内容容器
5. **强相关展示元素必成组** — 同一语义实体（如头像+名称+属性、图标+计数+说明）必须用容器包裹形成视觉单元，禁止裸放在父级 flex 中：
    - 列表中的一行 → `ui-list-card`（在 `ui-list-wrap` / `ui-list-stack` 内）
    - 独立信息块（预览/属性条/小卡） → `ui-group-card`
    - 段落/区块级分组（带标题或多行） → `ui-area-bg`
    判别口诀：「相邻信息能否独立成段被理解」→ 否则必成组（详见 SKILL-EDGE § 7.4）
6. **列表语义二选一** — 表格用 `ui-list-wrap`（含 header + 子卡背景透明）；卡片选择用 `ui-list-stack`（每张卡保留渐变底+边框，选中态用金色 `ui-selection-box`）。⛔ 禁止把卡片选择列表套进 `ui-list-wrap`（详见 SKILL-EDGE § 7.3）
7. **双列高度对齐** — 模型框高度 = 右列总高 − 左侧其他固定元素之和（参照 Step 5.5）
8. **大段文本不应被分隔成单字 span** — 多行说明文字必须放在同一容器中，靠容器宽度自动换行

### 组件选型类（选组件时查）

9. **颜色变量强制** — 所有颜色通过 `var(--ui-*)` 引用，禁止硬编码色值
10. **尺寸锁定控件不可拉伸** — `ui-item`（56px）、`ui-avatar`（各预设尺寸）禁止改动
11. **页签状态语义固定** — `light`=未选中 / `dark`=选中 / `gray`=禁用，不可混用
12. **纵向页签通过 JS 切换 active** — 禁止在 HTML 中硬编码多个 active；上限 4 个
13. **内联 SVG 优先** — 所有矢量图标优先从 Feather Icons 取，找不到则手写内联 SVG。**禁止图片/文字字符替代。**
14. **方形按钮图标使用内联 SVG** — `ui-btn-square-sm-*` 内部必须使用内联 SVG，**禁止文字字符（+/−）替代**
15. **属性标签等宽对齐用 `.ui-attr-label-Nch`** — 禁 `&emsp;` 手动垫字
16. **加减组合用 `.ui-stepper`** — 大数字加 `.ui-stepper--align-left` 切左对齐
17. **操作栏用 `ui-action-bar`** — 禁止手写 flex 居中 inline；汇总栏/操作栏禁止放入 `ui-list-wrap`（详见 SKILL-EDGE § 2.3）
18. **行内按钮/标签防挤用 `ui-no-shrink`** — 禁止手写 `white-space:nowrap; flex-shrink:0`
19. **字色优先语义中性 class** — `ui-text-body` / `-reminder` 等，CSS 容器作用域自动选族
20. **字色 class 禁止跨层嵌套** — 多色文本用平级兄弟 `<span>`（详见 SKILL-EDGE § 4.2）
21. **列表表头/数据行用同名 `ui-col-*` token** — CSS 通过 `:has()` 让 header 自动 `gap:8px` 与 card 对齐
22. **固定列宽优先选 `ui-col-*` 语义 token** — 已内置「最长内容 + 8px 安全余量」
23. **小圆按钮使用 `ui-btn-round`** — 34px 圆形按钮，SVG 内 `<linearGradient>` id 须唯一
24. **答题选项用 `ui-option-btn`，禁止 `ui-list-card`**（详见 SKILL-EDGE § 7.1）
25. **问号按钮**：标题栏帮助用 `ui-dialog-help-btn`（25px）；内容区说明用 `ui-btn-round`（详见 SKILL-EDGE § 8）
26. **`ui-avatar-inner` 禁内嵌 `<img>`** — 头像由 CSS background 驱动（详见 SKILL-EDGE § 6.1）
27. **`.selected` 仅用于功能型列表** — 排行榜/纯展示禁用（详见 SKILL-EDGE § 7.2）
28. **排行榜"我"标签用 `ui-rank-me-tag`** — 绝对定位于 `ui-list-wrap` 父容器（详见 SKILL-EDGE § 2.5）
29. **列表与紧贴固定行用子容器 `gap:0` 包裹**（详见 SKILL-EDGE § 2.4）
30. **`ui-list-wrap` 的 `overflow:hidden` 不可覆写为 visible**（详见 SKILL-EDGE § 2.2）
31. **横向页签放入 `[data-tab-group]`** — CSS 已固化 `flex:1 1 0` 自动等分，无需 inline style 与 JS

### 输出规范类

32. **仅 HTML 输出** — 交付物为完整可运行 HTML，不输出独立 CSS
33. **背景底板固定规则** — `body` 背景纯黑 `#000000`；游戏底板固定 960×540px `.game-canvas`，引用 `resource/HTBG.jpg` 设为 `960px 540px no-repeat`；禁止 `cover` 铺满视口
34. **信息组间距** — `gap` 默认 `8px`，最大 `12px`，禁止超过 `16px`
35. **图片路径必须 `list_files` 后填** — 实际文件多为中文名，猜测必错（详见 SKILL-EDGE § 9）
36. **flex column 内自定义行容器加 `width:100%; min-width:0`** — `ui-list-card` 已内置（无需写），仅自定义 `<div>` 行需要加

---

## 图标使用规范（精简）

> 完整图标速查与自绘规范见 `SKILL-EDGE.md` § 5；本节仅列高频用法。

**两种图标来源**：
1. **固定 SVG 组件**（标题梯形、关闭叉号）→ 从 `index.html` / `template.html` / `SKILL-EDGE.md` § 5 直接**复制**，禁止手写或改参数（颜色 `#29408D`、`stroke-width:5.5` 等是固定值）
2. **业务图标**（加减、对勾、铃、奖杯等）→ 优先从 [Feather Icons](https://feathericons.com) 复制 path，统一覆盖 `stroke-width:3`、`stroke-linecap/linejoin:round`，`viewBox 0 0 24 24`

**模板**：

```html
<svg width="24" height="24" viewBox="0 0 24 24"
     fill="none" xmlns="http://www.w3.org/2000/svg"
     style="display:block; flex-shrink:0;">
  <path d="..." stroke="#3C4599" stroke-width="3"
        stroke-linecap="round" stroke-linejoin="round"/>
</svg>
```

**尺寸**：按钮内 16–18px / 行内说明 18–20px / 标题栏 20–24px / `ui-dialog-close-btn` 与 `ui-btn-round` 固定 20px。

**禁止**：
- ⛔ PNG/JPG 替代矢量图标
- ⛔ 文字字符（`+`/`−`/`×`/`?`）替代图标
- ⛔ `stroke-width` 低于 2.5
- ⛔ 改 `ui-dialog-close-btn` 叉号的 20px 固定尺寸

