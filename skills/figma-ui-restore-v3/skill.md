---
name: figma-ui-restore-v3
version: "3.3"
based_on: figma-ui-restore-v2 v2.0
description: |
  将 HTML 代码或 UI 截图，使用 XYQ 自有 Figma 控件库还原为可编辑的 Figma 界面。
  当用户说"帮我还原这个界面"、"把这段 HTML 转成 Figma 稿"、"用控件库还原截图"、
  "生成 Figma 设计稿"、"把这个页面搬到 Figma"、"还原这个 UI" 等时，必须使用此 Skill。
  即使用户只是粘贴了一段 HTML 或上传了截图并说"帮我做成 Figma 稿"，也应触发此 Skill。
  用户说"重建控件目录"、"更新组件库索引"时，强制执行 Step 1 重建目录。

prerequisites: |
  ⚠️ **执行任何操作前，必须先读取 RULES.md 文件！**
  路径：.agents/skills/figma-ui-restore-v3/RULES.md
  包含 12 条从实际踩坑中提炼的硬性规则，违反任何一条都会导致还原结果错误。

changelog:
  - version: "3.3"
    date: "2026-04-17"
    changes:
      - "修复：Step 3 背景底图不可跳过——HTML 的 body.design-bg 必须在弹窗之前优先铺设，新增优先级0（mapping.json 直接映射设计底图组件）"
      - "新增注意事项：严格按 DOM 层级从外到内还原，禁止跳过外层容器（design-bg → design-container → ui-window）"
  - version: "3.2"
    date: "2026-04-16"
    changes:
      - "修复：Top10 单选按钮 variantMatch 状态值从 '0000（正常）' 改为 '0000'，与 Figma 实际值一致"
      - "修复：新增「深色」属性与底板关系说明——深色=yes 白字用于深色底板，深色=no 黑字用于浅色底板"
      - "新增 Step 4.5.7 flex:1 子元素等宽填充规则：当子元素都设 flex:1 时，每个子元素 resize 到等宽并紧密排列"
      - "修复：模型图片跨页处理——getNodeByIdAsync 在目标页无法获取控件库页节点时，改用 figma_search_components 搜索库组件"
      - "重要：弹窗底板禁止 detachInstance()——改用「画框叠加法」，弹窗保持完整组件实例，内容元素放入叠加的透明 FRAME 中"
      - "更新 css-figma-mapping.json 至 v1.1.0：补充 componentKey 字段，修复 area-bg-dark variantMatch"
      - "改进 Step 3 背景底图：默认 clone 用户选中的控件作为背景，无选中时才走 catalog 兜底"
  - version: "3.1"
    date: "2026-04-15"
    changes:
      - "新增 Step 2 布局数值提取：解析 CSS 时必须同步提取 flex/gap/padding 并输出到元素清单"
      - "新增 Step 4.5 精确布局计算规则：禁止目测坐标，所有 x/y/w/h 必须从 CSS 数值推导"
      - "新增 mkLocal 防错函数：本地未发布组件用 getNodeByIdAsync + createInstance"
      - "修复：单选按钮状态字段为 '0000' 而非 '正常'，更新 Top10 表格"
      - "修复：NPC头像_绿(286:3314) 为本地组件，importComponentByKeyAsync 会失败"
  - version: "3.0"
    date: "2026-04-15"
    changes:
      - "新增 CSS class → Figma 变体映射表（assets/css-figma-mapping.json），组件选型从猜测改为查表"
      - "新增 SKILL.md 内嵌 Top10 速查表，10个高频 class 零 IO 直接命中"
      - "新增 catalog/key-cache.json 持久化缓存 componentKey，跨会话复用"
      - "新增五级匹配漏斗：Top10 → mapping.json → catalog → figma_search → 策略C"
      - "新增防错函数头（st/ac/snap/mkInst），每次 figma_execute 开头注入"
      - "新增 cache miss 交互：Level 3/4 发现的组件询问用户是否持久化"
      - "Step 2.6 新增映射表预读步骤"
  - version: "2.0"
    date: "2026-04-09"
    changes:
      - "新增变体本地预览图机制（previews/目录），选型前视觉对比，消除试错"
      - "Step 2.5 新增字体预热步骤，减少 loadFontAsync 超时"
      - "Step 4 新增分区块截图检查点"
  - version: "1.0"
    date: "2026-04-03"
    changes:
      - "初始版本"
---

# Figma UI 还原 Skill v3

## 概述

将 HTML 代码或 UI 截图，通过调用 XYQ 正式控件库，在 Figma 中自动还原为可编辑界面。

**控件库文件 Key**：`mMtG1gmIiynergLFT5a0vu`
**目标 Page**：`【XYQ-PC】控件库`（node-id: `274:1861`）
**目录文件位置**：`.agents/skills/figma-ui-restore-v3/catalog/`
**映射表位置**：`.agents/skills/figma-ui-restore-v3/assets/css-figma-mapping.json`
**Key 缓存位置**：`.agents/skills/figma-ui-restore-v3/catalog/key-cache.json`

---

## 核心原则：三级降级策略

还原每个 UI 元素时，**始终按以下优先级处理**：

| 优先级 | 策略 | 触发条件 | 操作 |
|-------|------|---------|------|
| 🥇 第一 | **策略A** | 置信度 > 80分 | 直接实例化，保持组件关联 |
| 🥈 第二 | **策略B** | 置信度 50~80分 | 实例化 → `.detachInstance()` 解绑 → 调整细节 |
| 🥉 最后 | **策略C** | 置信度 < 50分 | 用基础图形从零搭建近似元素 |

**绝对不允许**：跳过策略A和B，直接用基础图形新建。

> ⚠️ **【v3.2】弹窗底板禁止 detach**：
> 弹窗组件（`.ui-window` / `弹窗`）是完整组件（含标题栏/关闭/问号），**永远使用策略A，禁止 detachInstance()**。
> 内容元素不要 appendChild 到弹窗实例内部，而是创建一个透明画框（FRAME）叠在弹窗上方，所有内容放入画框中。
> 详见 Step 4 ⑦。

---

## 🔥 Top10 速查表（直接用，零文件 IO）

> **v3 新增**：这 10 个是出现频率最高的 class，AI 读 SKILL.md 时已在上下文中，**无需读任何文件**。

| CSS class | Figma 组件 | componentSetNodeId | variantMatch | 视觉说明 |
|---|---|---|---|---|
| `.ui-btn.ui-btn-primary` | btu_主次按钮 | `322:36629` | `{红:"yes", 状态:"正常"}` | 🔴 红色主按钮，确定/参与/开启 |
| `.ui-btn`（无 primary）| btu_主次按钮 | `322:36629` | `{红:"no", 状态:"正常"}` | 🔵 蓝色次级按钮，取消/返回 |
| `.ui-btn-icon` | 方块按钮 | `341:40322` | `{btn_红蓝按钮:{红:"no"}}` | 图标方块按钮（蓝），key: `f095fa082774b55d76c00255ccbbe111653730bd` |
| `.ui-window` | 弹窗 | `1441:64893` | `{}` | 弹窗底板，自带标题栏/关闭/问号 |
| `.ui-area-bg` | 内衬底板 | `272:6255` | `{级别:"一级无底纹"}` | 🟡 **浅色**背景，内容区/卡片标配 |
| `.ui-area-bg-dark` | 内衬底板 | `272:6255` | `{级别:"二级", 四角花纹:"false"}` | 🌑 **深色**背景，深色区域用 |
| `.ui-input` | 亮底输入框 | `902:41571` | `{}` | 输入框（浅色背景用）|
| `.ui-tabs-secondary` | btn_二级页签蓝 | `232:4858` | `{状态:"初始", 图标:"无"}` | 二级水平页签 |
| `.ui-tabs-tertiary` | btn_三级页签蓝 | `258:931` | `{状态:"初始"}` | 三级页签 |
| `.ui-radio-group` | 单选按钮 | `433:32010` | `{深色:"no", 状态:"0000", 已选:"no"}` | 单选框（浅色背景用 深色=no）|

> ⚠️ **亮/暗底板判断规则**：
> - `.ui-area-bg` → **永远**用 `一级无底纹`（浅色背景，弹窗内容区/卡片标配）
> - `.ui-area-bg-dark` → **永远**用 `二级`（深色背景）
> - 不确定时看 HTML 注释，含"浅色衬底"→浅色，含"深色"→深色
> - ⛔ 禁止用 `四级浅色`（视觉上仍偏深，经验证不符合预期）
>
> ⚠️ **「深色」属性含义（适用于单选按钮、复选框等含文字的交互控件）**：
> - `深色=yes` → **白色文字**，用于**深色底板**（二级内衬底板、弹窗外暗色区域）
> - `深色=no` → **黑色/深色文字**，用于**浅色底板**（一级无底纹内衬底板）
> - 判断依据：看该控件**所在父容器**是 `.ui-area-bg`（浅色→no）还是 `.ui-area-bg-dark`（深色→yes）
> - ⛔ 不要靠控件自身的「深色」外观判断，而是看**它放在什么底板上**

---

## 🛡️ 防错函数头（每次 figma_execute 必须包含）

> **v3 新增**：每一个 `figma_execute` 调用，**必须将以下函数定义放在代码开头**。
> 规则已下沉为函数，只需调用函数名，不需要记操作顺序。

```javascript
// ═══ 防错函数头 v3 — 每次 figma_execute 必须复制此块到开头 ═══
const st = async (node, text) => {
  // 安全改文字：自动 loadFont，避免崩溃
  if (!node || node.type !== 'TEXT') return;
  try { await figma.loadFontAsync(node.fontName); } catch(e) {}
  node.characters = String(text);
};

const ac = (parent, child) => {
  // 安全添加子节点：先 append，再由调用方 resize
  if (!parent || !child) return;
  parent.appendChild(child);
};

const snap = async (nodeId) => {
  // 安全截图：自动跳过 SECTION，找第一个 FRAME
  let node = figma.getNodeById(nodeId);
  if (!node) return null;
  if (node.type === 'SECTION') node = node.children[0];
  return await node.exportAsync({ format: 'PNG', constraint: { type: 'SCALE', value: 1 } });
};

const mkInst = async (componentKey) => {
  // 安全实例化：从库导入组件并创建实例（适用于已发布的库组件）
  try {
    const comp = await figma.importComponentByKeyAsync(componentKey);
    return comp.createInstance();
  } catch(e) {
    console.error('mkInst failed:', componentKey, e.message);
    return null;
  }
};

const mkLocal = async (componentSetNodeId, variantMatch) => {
  // 安全实例化本地未发布组件：通过 nodeId 查找变体并 createInstance
  // 适用于 mkInst 失败的组件（如 NPC头像_绿 286:3314）
  try {
    const set = await figma.getNodeByIdAsync(componentSetNodeId);
    if (!set || set.type !== 'COMPONENT_SET') return null;
    const variant = set.children.find(c => {
      const p = c.variantProperties || {};
      return Object.entries(variantMatch).every(([k, v]) => p[k] === v);
    });
    return variant ? variant.createInstance() : set.children[0].createInstance();
  } catch(e) {
    console.error('mkLocal failed:', componentSetNodeId, e.message);
    return null;
  }
};
// ═══ 防错函数头结束 ═══
```

---

## 执行流程

### Step 0：检查目录

读取 `catalog/index.json` 是否存在：
- ✅ **存在** → 直接跳到 Step 2
- ❌ **不存在** 或 用户说「重建目录」→ 执行 Step 1

---

### Step 1：构建组件目录（首次 / 按需重建）

> ⚠️ **分批处理，每批处理 3 个 Section，处理完一批立即保存文件，再处理下一批。**

#### 1.1 已知 Section 列表

**第一批：**
| 分类 | Section Node ID | 输出文件 |
|-----|----------------|---------|
| button | `587:2217` | `catalog/button.json` |
| input | `900:49615` + `1498:59292` | `catalog/input.json` |
| toggle | `424:32563` | `catalog/toggle.json` |

**第二批：**
| 分类 | Section Node ID | 输出文件 |
|-----|----------------|---------|
| checkbox | `433:32007` | `catalog/checkbox.json` |
| tab | `587:2218` + `927:42172` | `catalog/tab.json` |
| dialog | `581:30523` | `catalog/dialog.json` |

**第三批：**
| 分类 | Section Node ID | 输出文件 |
|-----|----------------|---------|
| bubble | `517:32016` + `517:32259` | `catalog/bubble.json` |
| tag | `580:29135` + `503:31681` + `546:2818` | `catalog/tag.json` |
| title | `546:2731` + `1498:59270` | `catalog/title.json` |

**第四批：**
| 分类 | Section Node ID | 输出文件 |
|-----|----------------|---------|
| frame | `581:30526` + `424:32519` | `catalog/frame.json` |
| item | `441:761` + `441:1218` | `catalog/item.json` |
| data | `424:32617` + `547:32789` + `15218:65213` | `catalog/data.json` |

**第五批：**
| 分类 | Section Node ID | 输出文件 |
|-----|----------------|---------|
| empty | `547:33198` + `15203:65126` | `catalog/empty.json` |
| background | `581:30526` | `catalog/background.json` |
| page-template | `665:36218` + `7170:46800` | `catalog/page-template.json` |

---

### Step 2：解析输入，生成元素清单 + 布局数值表（v3.1 增强）

**HTML 输入：**
- 解析 DOM 树，提取标签名、class、id、文案、层级关系
- ⭐ **必须同步提取以下 CSS 布局数值**（v3.1 新增）：
  - `display`（flex / block / inline-flex）
  - `flex-direction`（row / column）
  - `justify-content`（flex-start / center / space-between / space-evenly）
  - `align-items`（flex-start / center / stretch）
  - `gap`（元素间距，单位 px）
  - `padding`（四方向，单位 px）
  - `margin`（特别关注 `margin-top: auto` 等贴底/贴右用法）
  - `width` / `height`（固定值 px、百分比、auto）
  - `flex: 1` 等弹性分配标记
  - `border-bottom` / `border-top`（分隔线）
- 从外到内按层级输出标准化元素清单

**图片输入：**
- 视觉识别界面中的 UI 元素类型
- OCR 提取所有可见文字
- 估算各元素位置坐标和相对尺寸

**⭐ 元素清单格式要求（v3.1）：**

每个容器元素必须包含布局摘要，示例：

```
#1 ui-window（弹窗）
   layout: flex-col | padding: 12px | gap: 4px
   size: 700 × 动态

   #1.1 popo-info（上区容器）
        layout: flex-row | padding: 14px | gap: 12px
        → 左列: flex: 1（自适应）
        → 分割线: width: 1px
        → 右列: width: 144px, flex-col, align: center

        #1.1.1 header-row
               layout: flex-row | gap: 8px | align: center
               → 标签 "选择出战泡泡": width: 7em
               → 页签容器: flex: 1, justify: space-between
               border-bottom: 1px rgba(139,150,182,0.35)

        #1.1.2 skill-row
               layout: flex-row | gap: 8px
               → 标签 "当前技能": width: 7em
               → 图标容器: flex: 1, justify: space-between
```

**输出标准化元素清单后，向用户展示并确认，有误可纠正后继续。**

---

### Step 2.5：字体预热

> 在实例化任何组件之前执行，避免后续 loadFontAsync 超时。

```
1. 执行一次 figma_execute: await figma.loadFontAsync({ family: "DFPYuanW7-GB", style: "Regular" })
2. 如超时 → figma_reconnect → 重试一次
3. 预热成功后继续 Step 2.6
```

---

### Step 2.6：映射表预读（v3 新增）

> 在实例化开始之前，将扩展缓存加载到内存，减少后续 IO。

```
1. 读取 catalog/key-cache.json → 建立内存对象 keyCache
   key-cache 存放的是 mapping.json 以外发现的新 CSS class 映射
   两者内容不重合，合在一起构成完整的 CSS class 映射体系
2. 如文件不存在或为空 → keyCache = {}，继续
3. assets/css-figma-mapping.json 按需读取（命中 Top10 且无冲突时可跳过）
```

---

### Step 3：铺设背景底图（v3.3 改进）

> ⛔ **此步骤不可跳过！** HTML 的 `<body class="design-bg">` 对应 mapping.json 中的 `设计底图` 组件（HIGH confidence），
> 必须在弹窗之前先铺设。即使用户的关注点是弹窗内容，背景底图仍是完整还原的必要组成部分。
> **执行顺序：Step 3 背景 → Step 4 弹窗 → Step 4 内容**，绝不允许颠倒或跳过。

```
优先级0 ── mapping.json 映射（v3.3 新增）
  → HTML 的 body 或最外层有 design-bg class 时
  → 直接查 mapping.json cssId="design-bg" → 得到 componentKey
  → mkInst(componentKey) 实例化设计底图，置于 Section 最底层 insertChild(0, bg)
  → 弹窗居中放置于底图上：dialogX = (bgW - dialogW) / 2

优先级1 ── 用户选中的控件（默认）
  → 调用 figma_get_selection 获取当前选中节点
  → 如果有选中节点 → clone 该节点作为背景底图
    figma_execute:
      const sel = figma.currentPage.selection[0];
      const bg = sel.clone();
      bg.x = 0; bg.y = 0;
      section.insertChild(0, bg);  // 确保图层最底部
  → 如果无选中节点 → 进入优先级2

优先级2 ── catalog 候选（兜底）
  → 读 catalog/background.json → 拿候选组件列表
  → 默认选「普通场景」版本
  → 用户明确说「战斗场景」时才选另一个
  → 实例化，置于 (0, 0)，通过 figma_execute 确保图层最底部
    parent.insertChild(0, bgNode)
```

---

### Step 4：递归匹配 & 实例化（v3 核心改造）

按「**从外到内、从上到下**」遍历元素清单，每个元素执行五级匹配漏斗：

```
① 提取元素的 CSS class 列表

② 六级匹配漏斗（命中即止步）：

   Level 1 ── Top10 速查表（本 SKILL.md 上方表格，零 IO）
     → 精确匹配 cssClass → 得到 componentSetNodeId + variantMatch
     → 同时检查 mapping.json 是否有相同 cssId：
         ├ mapping.json 数据一致 或 无该 cssId → 直接用 Top10，进入 ③
         └ mapping.json 数据不同 → 用 mapping.json 数据，询问用户「是否更新 Top10？」，进入 ③

   Level 2 ── assets/css-figma-mapping.json（官方映射表，读文件一次）
     → 在 mappings[] 中找 cssClass 匹配项
     → confidence=NO_MATCH → 直接跳策略C
     → confidence=HIGH → ⛔ 立即止步，禁止继续查 Level 3/4/5
                          直接用 mapping.json 的 componentSetNodeId + variantMatch 进入 ③

   Level 3 ── catalog/key-cache.json（扩展映射表，Step 2.6 已加载）
     → 查 keyCache 对象，找 cssId 匹配项
     → 命中 → 直接得到 componentKey，进入 ④（跳过 ③ 的运行时查找）
     → 未命中 → 继续 Level 4

   Level 4 ── catalog/xxx.json（按元素类型读对应分类文件）
     → match_keywords 模糊匹配 → visual_note 视觉确认
     → 置信度 MEDIUM → 进入 ③（可能策略B）

   Level 5 ── figma_search_components（API 调用）
     figma_search_components(libraryFileKey: "mMtG1gmIiynergLFT5a0vu", query: "关键词")
     → 选最匹配结果 → 置信度 LOW → 进入 ③（策略B）
     → 完成实例化后询问用户：「是否将此组件的 CSS class 映射写入 key-cache？」

   Level 6 ── 策略C
     → figma_create_child 基础图形
     → 标注 🔴「新增-请替换」
     → 记录到还原报告

   【特殊：模型图片处理】
     HTML 含 img[src*='模型'] 或 img[src*='泡泡'] 或 `.ui-model-pet` 等模型图片时：
     → 不走漏斗，按以下优先级逐级尝试：

       优先级1 ── 同页 clone（当前页就是控件库页面时）
         figma_execute:
           const modelFrame = await figma.getNodeByIdAsync('298:19515');
           const modelInst = modelFrame.children.find(c => c.name === '单体模型' || c.type === 'INSTANCE');
           const cloned = modelInst.clone();
           cloned.resize(64, 64);
           parent.appendChild(cloned);

       优先级2 ── 跨页 clone（当前页≠控件库页面时，getNodeByIdAsync 返回 null）
         ⚠️ getNodeByIdAsync 无法跨页获取节点！
         改用 figma_search_components 在库中搜索：
           figma_search_components(libraryFileKey: "mMtG1gmIiynergLFT5a0vu", query: "模型")
         → 找到匹配结果 → 用 figma_instantiate_component 实例化
         → 未找到 → 进入优先级3

       优先级3 ── 占位框 + 标注
         → 创建一个与 HTML 中模型同尺寸的占位矩形（圆角，半透明灰色填充）
         → 在占位框上方添加文字标注「🔴 模型占位-请替换」
         → 报告里标注「模型图-需人工替换为对应角色」

       ⛔ 禁止：跳过优先级1和2，直接用占位框

③ 解析 componentKey：
   a. 先查 keyCache（Step 2.6 已加载）
      → cache hit：直接用 componentKey
      → cache miss：
        figma_execute: const set = figma.getNodeById(componentSetNodeId)
                       const v = set.children.find(c => Object.entries(variantMatch)
                                 .every(([k,val]) => c.variantProperties[k] === val))
                       return v.key
        → 得到 componentKey

④ 实例化（防错函数头 + mkInst）：
   const inst = await mkInst(componentKey)
   → Level 3/4 命中时，询问用户是否写入 key-cache：
     「找到组件 [名称]（来源: Level X），是否保存到 key-cache 下次直接用？」
     用户确认 → 追加写入 catalog/key-cache.json

⑤ 记录目标尺寸（w, h），setProperties 切换变体后立即在同一 figma_execute 调用中 resize

⑥ 注入文案：
   → 优先用 figma_set_instance_properties（TEXT属性）
   → 找不到属性时：figma_execute + 防错函数 st(node, text)

⑦ 容器类元素（dialog/frame）— 画框叠加法（v3.2 改进）：
   ⛔ 禁止对弹窗组件执行 detachInstance() 或 appendChild()！
   弹窗底板保持完整组件实例不变，内容元素放在独立画框（FRAME）中。

   步骤：
   a. 实例化弹窗底板 → resize 到目标尺寸 → 放置到 Section 中
   b. 创建一个同尺寸的透明 FRAME（命名「内容层」），坐标与弹窗完全重合
      → fills = [] （无填充，透明）
      → 该 FRAME 的图层在弹窗之上（后 appendChild 的自然在上层）
   c. 递归处理弹窗的所有 children 元素 → 全部放入「内容层」FRAME
      → 内容元素的坐标按弹窗内部区域计算（跳过标题栏高度）
   d. 对于非弹窗的普通容器（内衬底板等）也同理：
      实例化底板 → 创建透明内容画框叠加 → children 放入画框

   示例代码：
   ```javascript
   // 弹窗底板（保持组件实例）
   const dialog = await mkInst(windowKey);
   dialog.resize(700, 500);
   dialog.x = sectionX; dialog.y = sectionY;
   section.appendChild(dialog);

   // 内容层画框（透明，叠在弹窗上方）
   const contentFrame = figma.createFrame();
   contentFrame.name = '内容层';
   contentFrame.resize(700, 500);
   contentFrame.x = sectionX; contentFrame.y = sectionY;
   contentFrame.fills = []; // 透明
   section.appendChild(contentFrame);

   // 后续所有内容元素放入 contentFrame
   contentFrame.appendChild(childElement);
   ```
```

#### 元素类型 → 目录分类映射（Level 3 用）

| 元素类型特征 | 目录分类 |
|------------|---------|
| `<button>` `.btn` `.button` | button |
| `<input>` `<select>` `.search` | input |
| `.switch` `.toggle` | toggle |
| `<input type="radio/checkbox">` | checkbox |
| `.tab` `.pagination` | tab |
| `.modal` `.dialog` `.popup` `.ui-window` | dialog |
| `.tooltip` `.bubble` | bubble |
| `.tag` `.badge` `.dot` | tag |
| `<h1~h3>` `.title` | title |
| `.inner-bg` `.panel` `.ui-area-bg` | frame |
| `.item-slot` `.avatar` `.icon` | item |
| `.progress` `.list` `<table>` | data |
| `.empty` `.locked` | empty |
| `<body>` 整页背景 | background |
| 整页模板框架 | page-template |

#### 分区块检查点

每完成一个独立区块立即截图对比（用 `snap(nodeId)` 函数）：

| 检查点 | 完成后截图对比 |
|--------|-------------|
| ① 弹窗底板 + 标题栏 | 确认窗口尺寸、标题文字 |
| ② 信息区（底板+文字+进度条）| 确认颜色、间距 |
| ③ 属性区（底板+文字网格）| 确认对齐、颜色对比度 |
| ④ 底部按钮 | 最终全图对比 |

---

### Step 4.5：精确布局计算（v3.1 新增）

> ⛔ **禁止目测/估算坐标**。所有元素的 x, y, width, height 必须从 Step 2 提取的 CSS 数值推导。
> 这是 v3c 之前还原布局不准的根因：AI 用「大概在这个位置」而非精确计算。

#### 4.5.1 坐标推导公式

**基础公式**：每个元素的 Figma 坐标 = 父容器起点 + padding + 前置兄弟元素总宽/高 + gap 累加

```
对于 flex-row 容器，子元素 i 的 x 坐标：
  x[i] = parent.x + padding_left + Σ(width[0..i-1]) + gap × i

对于 flex-column 容器，子元素 i 的 y 坐标：
  y[i] = parent.y + padding_top + Σ(height[0..i-1]) + gap × i
```

#### 4.5.2 flex: 1 自适应宽度计算

当子元素标记了 `flex: 1` 时，先计算固定宽度元素占用的空间：

```
容器可用宽度 = container_width - padding_left - padding_right
固定占用 = Σ(固定宽度子元素的 width) + gap × (子元素数 - 1)
flex:1 元素宽度 = (容器可用宽度 - 固定占用) / flex:1 元素个数
```

**示例**：容器 676px，padding 14px，gap 12px，右列固定 144px，分割线 1px
```
可用 = 676 - 14 - 14 = 648
固定 = 144 + 1 + 12 × 2 = 169
左列(flex:1) = 648 - 169 = 479px
```

#### 4.5.3 justify-content: space-between 分布

```
totalChildren = N 个子元素
totalChildrenWidth = Σ(每个子元素的 width)
containerInnerWidth = container_width - padding_left - padding_right
spaceBetweenGap = (containerInnerWidth - totalChildrenWidth) / (N - 1)

子元素 i 的 x：
  x[0] = parent.x + padding_left
  x[i] = x[i-1] + width[i-1] + spaceBetweenGap
```

#### 4.5.4 margin-top: auto（贴底）

子元素设置了 `margin-top: auto` 时，该元素紧贴容器底部：
```
y = parent.y + parent.height - padding_bottom - element.height
```

#### 4.5.5 分隔线（border-bottom / border-top）

HTML 元素有 `border-bottom` 时，在该元素底部创建一条 1px 矩形：
```
const line = figma.createRectangle();
line.resize(parent_inner_width, 1);
line.x = parent.x + padding_left;
line.y = element.y + element.height;
line.fills = [{ type: 'SOLID', color: 解析 border 颜色, opacity: 解析 opacity }];
```

#### 4.5.7 flex:1 子元素等宽填充（v3.2 新增）

> ⛔ **当容器设置了 `justify-content: space-between`，但子元素同时设置了 `flex: 1` 时，`flex: 1` 优先级更高**。
> 此时子元素会等宽填充容器，**不适用 Step 4.5.3 的 space-between 公式**。

```
场景：父容器 justify-content: space-between; 子元素都有 flex: 1;

此时每个子元素的宽度 = 容器可用宽度 / 子元素个数
子元素紧密排列（无间距），或者仅由组件自身的内边距产生视觉间距。

示例：容器可用宽度 347px，5 个 tab 各自 flex: 1
  每个 tab 宽度 = 347 / 5 = 69.4px → resize(69, 26)
  tab[0].x = container.x
  tab[i].x = tab[i-1].x + tab[i-1].width  // 紧密排列，无额外 gap
```

**判断规则**：
- CSS 中容器用 `space-between` + 子元素用 `flex: 1` → **按等宽填充处理**
- CSS 中容器用 `space-between` + 子元素**无** `flex: 1`（固定宽度）→ 用 Step 4.5.3 公式
- CSS 中子元素 `flex: 1` + 容器用 `gap` → 等宽填充，但相邻元素之间有 `gap` 间距

---

#### 4.5.6 已知组件尺寸速查

> 实例化后的组件有默认尺寸，布局计算时使用这些值：

| 组件 | 默认宽 × 高 | 说明 |
|------|------------|------|
| btn_三级页签蓝 | 76 × 26 | 文字短时可能更窄 |
| 技能图标40*40 | 40 × 40 | 固定 |
| 单选按钮 | 62 × 24 | 含文字 |
| btu_主次按钮 | 可 resize | 按文字长度调整 |
| 方块按钮 | 26 × 26 | 固定 |
| NPC头像_绿(尺寸=56) | 56 × 56 | 固定 |
| 内衬底板 | 可 resize | 按容器计算 |

---

### Step 5：布局排列（v3.1 改进）

> 不再使用「凭感觉排列」，改为严格按 Step 4.5 公式计算坐标。

```
1. 从 Step 2 元素清单的布局摘要中，逐层计算每个元素的绝对坐标
2. 容器优先：先确定所有容器（弹窗 → 区域底板 → 行容器）的坐标和尺寸
3. 子元素按 flex 公式填入：
   - flex-row + space-between → Step 4.5.3
   - flex-row + gap → 逐个累加
   - flex: 1 → Step 4.5.2 计算宽度
   - margin-top: auto → Step 4.5.4 贴底
4. 最后处理分隔线（Step 4.5.5）
5. 所有组件放入一个命名 Section：「UI还原-[来源描述]」
```

---

### Step 6：截图验证 & 输出还原报告

```
snap(sectionFrameId) 截图当前还原结果
与原始输入对比，输出还原报告
```

**还原报告格式：**

```
## 界面还原报告

### 总览
- 总元素：XX 个
- ✅ 策略A（直接使用控件）：XX 个
- 🔶 策略B（解绑调整）：XX 个
- 🔴 策略C（新增元素）：XX 个

### 匹配来源统计
- Level 1（Top10）：XX 个
- Level 2（mapping.json）：XX 个
- Level 3（catalog）：XX 个
- Level 4（figma_search）：XX 个
- Level 5（策略C）：XX 个

### 🔶 策略B — 需人工确认
| 元素 | 使用的组件 | 解绑原因 | 建议操作 |

### 🔴 策略C — 需人工替换
| 元素 | 位置 | 原因 | 建议操作 |

### � 本次写入 key-cache 记录
| cssId | 组件名 | componentKey |
```

---

## 增量补图机制

**触发条件（满足任一）：**
- 实例化了某变体，但 `variant_previews` 中无记录
- `local_image` 文件路径不存在

**执行动作：**
```
1. figma_get_component_image(nodeId=variantNodeId, scale=1)
2. 保存图片到 previews/{category}/{组件名}_{变体key}.png
3. 更新 catalog JSON 中对应条目的 variant_previews
```

---

## 注意事项

- Figma 未连接时，提示用户确保 Figma Desktop Bridge 插件已运行
- 始终在 Figma 现有页面操作，不要新建页面
- 所有还原结果放入命名 Section，不要散放在画布上
- 建目录时分批处理（每批 3 个 Section），处理完一批立即保存
- 匹配时每次只加载一个分类目录文件，不全量加载
- **【v3.1】布局必须精确计算**：⛔ 禁止凭感觉估算坐标，所有 x/y/w/h 必须从 CSS flex/gap/padding 数值推导（Step 4.5）
- **【v3.1】flex:1 → 做减法**：遇到 `flex: 1` 的元素，先算固定元素占用空间，再用总宽度减去得到自适应宽度
- **【v3.1】space-between → 做除法**：遇到 `justify-content: space-between`，用 (容器内宽 - 子元素总宽) / (N-1) 得到均匀间距
- **【v3.1】分隔线不要遗漏**：HTML 的 `border-bottom` / `border-top` 必须还原为 1px 矩形
- **【v3.1】根据 published 字段选择实例化方式**：mapping.json 每个条目已标注 `"published": true/false`
  - `published: true` → 用 `mkInst(componentKey)`（快路径）
  - `published: false` → 用 `mkLocal(componentSetNodeId, variantMatch)`（本地路径）
  - 无 published 字段 → 先试 mkInst，失败降级 mkLocal
- **【v3.1】已知未发布组件清单**（2026-04-17 探测）：
  - `item-icon-round`（Component 260，圆形道具图标 `15000:61638`）
  - `list-item`（每日活动 `14056:87313`）
  - NPC头像_绿(`286:3314`)、玩家头像_绿(`441:949`)（不在 mapping 中但常用）
- **【v3】映射表优先**：有 CSS class 时，Top10 → mapping.json 必须先于 catalog 查找
- **【v3】防错函数头必须注入**：每个 figma_execute 调用都要带函数头（含 mkLocal），不允许省略
- **【v3】新发现组件先询问再持久化**：Level 3/4 命中的组件，用户确认后才写 key-cache
- **【v3.2】单选按钮状态字段**：Figma 实际属性值为纯数字 `0000`/`0001`/`0002`/`0003`，⛔ 不含中文括号后缀（如 `0000（正常）` 会匹配失败导致 mkLocal 回退到 children[0]）
- **【v3.2】「深色」属性不是视觉描述，而是底板适配**：深色=yes→白字→放深色底板上；深色=no→黑字→放浅色底板上。判断方法：看控件所在父容器是 `.ui-area-bg`(浅色→no) 还是 `.ui-area-bg-dark`(深色→yes)
- **【v3.2】flex:1 优先于 space-between**：当子元素都有 `flex: 1` 时，忽略容器的 `space-between`，按等宽填充计算（Step 4.5.7）
- **【v3.2】跨页获取节点会返回 null**：`getNodeByIdAsync` 只能访问当前页面的节点。需要控件库页面的资源（如模型、背景底图）时，改用 `figma_search_components` + `importComponentByKeyAsync` 从已发布库导入
- **【v3.2】⛔ 弹窗底板禁止 detach**：弹窗组件保持完整实例不动，内容元素放入叠加的透明 FRAME（「内容层」），不要 appendChild 到弹窗实例内部（详见 Step 4 ⑦）
- **【v3.3】⛔ 严格按 DOM 层级从外到内还原，禁止跳过外层容器**：HTML 的 `<body class="design-bg">` → `.design-container` → `.ui-window` 必须逐层处理。背景底图（`design-bg`）是最外层，必须在 Step 3 优先铺设，弹窗居中放置其上。**绝对不允许**跳过背景直接从弹窗开始——即使弹窗是视觉焦点，背景底图仍是 mapping.json 中 `confidence: HIGH` 的有效组件映射（`设计底图 0042d258…`）
- **【v2】选型必须先看预览图，禁止在 Figma 里试错**
- **【v2】setProperties 后立即 resize，不允许分开两次调用**
- **【v2】每完成一个区块截图检查，不等全部完成再验证**
- **截图用 `snap(nodeId)` 函数，不要直接 exportAsync SECTION 节点（会返回白图）**
