# HANDOFF.md — 金光 Demo 交接文档

> 本文档给**接手这个金光 Demo 的 Agent** 准备。请完整阅读后再动手。

---

## 🎯 一、项目背景

### 这是什么
这是为**金光集团（中国）会计 AI 创新增效竞赛**做的 Demo，主题是"基础财会优化"。

参赛时间线（来自 2026-09-02 会议纪要）：

| 阶段 | 时间 | 目标 |
|:--|:--|:--|
| 初版 Demo | 2026-09-14 | 完成 PPT + Demo 初版 |
| 定稿 | 2026-09-20 | 评审通过，准备参赛 |
| 现场展示 | 2026-09-21 ~ 09-30 | 集团路演 + 同事投票 + 评委评分 |

### 核心场景（来自会议纪要）
3 个金光真实痛点：
1. **客户信息补全** —— 主数据缺失时自动从工商接口拉取
2. **自动开票** —— 销项发票自动化、异常时拦截
3. **公司风险监控** —— 客户工商/税务/经营异常时主动预警

### 技术定位
- **形式**：浏览器 HTML 单页 Demo（不要做成需要后端的服务）
- **数据**：mockData 写死（不需要真实数据，但要**看起来真实**）
- **目标观众**：金光财务经理 + 评委 + 现场投票的同事

---

## 📂 二、代码结构

### 仓库地址
- GitHub: https://github.com/Rickywang223/jinguang-demo
- 本地: `/tmp/demo-gallery/`

### 目录布局

```
demo-gallery/
├── index.html              # Demo Gallery 中转页（"口金卡"中性风格）
├── demos/
│   └── jinguang.html       # ★ 金光 demo 主文件（137.4 KB）
├── shared/
│   └── style.css           # 共享样式（中性灰 + 烫金 #8c6a3a）
├── agent-cockpit-design.md # 智能体驾驶舱设计文档
├── agent-mgr-design.md     # 智能体管理设计文档
├── data-board-design-v2.md # 数据看板设计文档（v2 是当前实现版）
├── data-board-design.md    # 数据看板 v1（已废弃，仅参考）
└── view-design.md          # 视图设计文档
```

### 技术栈
- 纯 HTML + CSS + 原生 JavaScript（无框架）
- 无构建步骤（直接打开 HTML 即可）
- jQuery-free（用 vanilla JS）
- 服务可用 `python3 -m http.server 7777`

---

## 🎨 三、视觉规范（必须保持）

### 设计参考
- **税务合伙人 v2 智能体驾驶舱**：参考了 `vue-component-development` skill 的 `references/agent-cockpit-v2.md`
- **整体风格**：轻量 AI 工作台（亮色 sidebar + 白卡片 + 低饱和状态色 + 留白充足）

### 配色（CSS 变量）

```css
:root {
  --paper: #ffffff;      /* 卡片底色 */
  --canvas: #f7f8fa;     /* 主区域底色 */
  --line: #f0f0f0;       /* 边框 */
  --line-2: #e5e7eb;     /* 次要边框 */
  --ink-1: #1a1f2e;      /* 主文字 */
  --ink-2: #4a5266;      /* 次要文字 */
  --ink-3: #8c92a3;      /* 三级文字（灰色）*/
  --brass: #8c6a3a;      /* 烫金（品牌色）*/
  --danger: #b9342c;     /* 警示红 */
  --success: #2e9e5b;    /* 成功绿 */
  --r: 8px;               /* 默认圆角 */
  --r-lg: 12px;           /* 大圆角 */
}
```

**口金卡**中性风格（用于 Gallery 中转页）：`#1a1f2e` + `#8c6a3a`，**不偏向任何客户品牌色**。

### 圆角与间距
- 卡片圆角：`var(--r-lg)` = 12px
- 元素圆角：`var(--r)` = 8px
- 卡片间距：`12-16px`
- 内容内边距：`16-24px`

### 字号层级

| 用途 | 字号 |
|:--|:--|
| 大数字（KPI 数字）| 28px font-weight 700 |
| 中等数字（卡片 KPI）| 20px font-weight 700 |
| 一级标题（panel 标题）| 14-16px font-weight 600 |
| 正文 | 12-13px |
| 小字（说明文字）| 10-11px |

---

## 🏗 四、布局架构

### 总体结构

```
.demo-layout (grid 280px 1fr)
├── .demo-sidebar (280px)
│   ├── .sidebar-brand           品牌
│   ├── .sidebar-mode            [工作台] [智能体]
│   ├── .sidebar-section         # 工作台快捷入口 或 业务智能体列表
│   ├── .sidebar-subsection      # 历史对话 / 定时任务 二级 tab
│   ├── .sidebar-tool-list       # 智能体管理（底部固定）
│   └── .sidebar-user            # 用户区（最底部，margin-top:auto）
└── .demo-main
    ├── .main-toolbar            顶部条：标题 + 时间
    └── .main-content
        └── .views-container     所有 view-panel 的容器（display:contents）
```

### 关键 CSS 模式

**单滚动容器架构**（已调试好）：

```css
.view-panel {
  display: none;
}
.view-panel.active {
  display: block;
  height: 100%;
}
.views-container {
  display: contents;  /* 关键：让 panel 直接挂在 main-content 下 */
}
.main-content {
  flex: 1;
  overflow-y: auto;
  padding: 20px 24px;
}
```

**聊天窗口模式**（agent-billing / agent-risk / new-task / history-*）：

```css
display: flex;
flex-direction: column;
height: calc(100vh - 60px);  /* 填满视口 */
> 中间滚动区 { flex: 1; overflow-y: auto; }
> 底部固定区 { flex-shrink: 0; }  /* 聊天输入框 */
```

### Sidebar 布局要点

```css
.demo-sidebar {
  display: flex;
  flex-direction: column;
  height: 100vh;
}
.sidebar-user {
  margin-top: auto;  /* 关键：用户区始终贴底 */
}
.sidebar-tool-list {
  margin-top: auto;  /* agent 模式下工具区贴到用户区上方 */
}
```

---

## 📋 五、所有 Panel 清单（共 16 个）

### 工作台模式（8 个）

| Panel id | 用途 | 文件位置 |
|:--|:--|:--|
| `new-task` | 云升风格新任务（中央欢迎 + 底部输入）| 中部 |
| `data-board` | 数据看板（4 区 5 卡，含 14 天 SVG 折线图）| 中部 |
| `skill` | 技能页（占位）| 中部 |
| `history-1` | 上海永胜·主数据确认（5 轮完整聊天）| 中部 |
| `history-2` | 浙江光华·风险处置 | 中部 |
| `history-3` | 苏州金鹏·停止开票 | 中部 |
| `history-4` | 5 家主数据批量补全 | 中部 |
| `history-5` | 月初认证率盘点 | 中部 |
| `schedule-1` | 每日风险扫描（任务详情页）| 中部 |

### 智能体模式（6 个）

| Panel id | 用途 | 关键特性 |
|:--|:--|:--|
| `agent-cockpit` | 智能体驾驶舱（6 区）| 团队状态 + 工作卡 + 待我确认 + 正在执行 + 工具状态 + 快捷调度 |
| `agent-billing` | 智能开票专员驾驶舱 | 顶部状态卡 + 4 KPI + 事件流 + 智能建议 + **底部聊天区** |
| `agent-risk` | 风险预警官驾驶舱 | 同上结构（**含聊天区**）|
| `tool-mgr` | 智能体管理（3 区）| 总览 + 智能体清单 + 系统能力配置 |
| `tool-cap` | 能力中心（占位）| 不显示在 sidebar |
| `tool-kb` | 知识库（占位）| 不显示在 sidebar |

---

## 🔧 六、JS 关键逻辑

### 视图切换

```js
function switchView(viewId) {
  document.querySelectorAll('.view-panel, .agent-panel').forEach(p => 
    p.classList && p.classList.remove('active'));
  const target = document.querySelector(`[data-view-panel="${viewId}"]`);
  if (target && target.classList) target.classList.add('active');
  document.getElementById('main-title').textContent = titles[viewId] || '金光 AI 财工作台';
}

// 智能体点击 + 模式切换
document.querySelectorAll('[data-view]').forEach(el => {
  el.addEventListener('click', () => {
    const viewId = el.dataset.view;
    if (el.classList && el.classList.contains('agent-item')) {
      document.querySelectorAll('.agent-item').forEach(i => 
        i.classList && i.classList.remove('active'));
      el.classList.add('active');
    }
    switchView(viewId);
  });
});
```

### 模式切换（工作台/智能体）

```js
document.querySelectorAll('.mode-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    const mode = btn.dataset.mode;
    // 切换 [data-mode-content] 元素的显示
    document.querySelectorAll('[data-mode-content]').forEach(el => {
      el.style.display = (el.dataset.modeContent === mode) ? '' : 'none';
    });
    document.querySelectorAll('[data-mode-section]').forEach(el => {
      el.style.display = (el.dataset.modeSection === mode) ? '' : 'none';
    });
  });
});
```

### 子 tab 切换（历史对话 / 定时任务）

```js
document.querySelectorAll('.sub-tab').forEach(tab => {
  tab.addEventListener('click', () => {
    const target = tab.dataset.subtab;
    document.querySelectorAll('.sub-tab').forEach(t => t.classList.remove('active'));
    tab.classList.add('active');
    document.querySelectorAll('.sub-tab-panel').forEach(p => {
      if (p.dataset.subpanel === target) {
        p.style.display = '';
        p.classList.add('active');
      } else {
        p.style.display = 'none';
        p.classList.remove('active');
      }
    });
  });
});
```

### AI 依据 Modal（点击事件卡片触发）

```js
function openModal(id) {
  const data = EVIDENCE[id];
  // ... 渲染 modal
  document.getElementById('modal-mask').classList.add('open');
}
```

**EVIDENCE 对象**（8 条固定数据）：
- `event-1` 苏州金鹏·税务非正常户
- `event-2` 浙江光华·法人变更
- `event-3` 上海永胜·主数据缺项
- `event-4` 经营预警·增长不收钱
- `event-5` 上海林达纸业·经营异常
- `event-7` 主数据批量补全
- `event-safe` 其余 229 家低风险

---

## 🎯 七、设计原则（绝对不要破坏）

### 1. 单滚动容器
**绝对只有一个滚动元素**。每个 panel 自己有 `overflow-y: auto` + 高度固定，**不要**在外层加 `overflow: hidden`，否则 panel 内容会被裁剪。

### 2. 视图容器透明
`.views-container { display: contents }` —— 容器"消失"，panel 直接挂 main-content 下。**不要**改成 `display: block/flex`，否则 panel 嵌套会出问题。

### 3. Sidebar 用户区贴底
**永远让 `.sidebar-user { margin-top: auto }`**。删掉它用户区就会跑到中间。

### 4. Panel 嵌套 = Bug
**永远不要**在某个 panel 内嵌套另一个 panel（除非用 `<iframe>`）。空 `<div class="view-panel">` 会导致所有后续 panel 被困在里面（h=0）。

### 5. Sidebar 元素都标 `data-mode-content` 或 `data-mode-section`
这两个 data 属性决定元素在工作台 / 智能体模式下的可见性。**新增 sidebar 项必须加这两个属性之一**。

---

## 🆕 七·五、R001 实现说明：智能体左右分栏布局（2026-09-18）

> 需求文档：`.changes/R001-agent-split-layout.md`

### 改了什么

`agent-billing`（智能开票专员）和 `agent-risk`（风险预警官）从**单列堆叠**改为**左右分栏**：

```
┌────────────────────────┬────────────────────────┐
│ 左区：聊天窗口 (50%)    │ 右区：页签看板 (50%)    │
│  · .chat-header        │  · .dashboard-tabs     │
│  · .chat-messages      │    📊 BI 报表（默认）   │
│  · .chat-input-bar     │    📚 资料库           │
│                        │    🌐 网页（iframe）    │
└────────────────────────┴────────────────────────┘
              中间：.agent-split-resizer 分隔线
```

原「状态卡 + 4 KPI + 事件流 + AI 建议」整体移入右区 **BI 报表** tab，未做内容改动。

### 新增 CSS class

| class | 作用 |
|:--|:--|
| `.agent-split-layout` | 分栏容器，`height: calc(100vh - 60px)`，`display: flex` |
| `.agent-split-left` / `.agent-split-right` | 左右两栏，各 `flex: 1`（即 50:50） |
| `.agent-split-resizer` | 中间分隔线（4px） |
| `.chat-header` / `.chat-messages` / `.chat-input-bar` / `.chat-input-row` | 聊天区 |
| `.chat-msg` / `.chat-msg-ai` / `.chat-msg-user` | 聊天气泡 |
| `.dashboard-tabs` / `.tab-active` | 右区页签栏与选中态 |
| `.dashboard-content` / `.dashboard-pane` / `.tab-pane-active` | 页签内容与显隐 |
| `.doc-list` / `.doc-item` | 「资料库」tab 列表 |
| `.web-frame` / `.web-frame-bar` | 「网页」tab 的 iframe |

### 新增 JS

| 名称 | 作用 |
|:--|:--|
| `AGENT_CHAT` | 每个智能体**独立**的聊天历史（内存，切换不丢） |
| `CHAT_REPLIES` | 模拟回复池 |
| `MOCK_WEB` | 「网页」tab 的示例页面（内嵌 HTML 字符串，**无外部依赖**） |
| `renderChat(agentId)` | 渲染某智能体的聊天记录 |
| `sendChatMessage(agentId, text)` | 发送消息 → 500ms 后追加模拟回复 |
| `switchDashTab(agentId, tab)` | 右区页签切换（「网页」tab 首次激活才注入 iframe，按需渲染） |

绑定方式：`[data-chat-send]` / `[data-chat-input]` / `[data-tabs]` / `[data-web-frame]` 属性选择器，支持文本框 Enter 发送（Shift+Enter 换行）。

### ⚠️ 实现注意（后续改动不要破坏）

1. `.chat-messages` 和 `.dashboard-content` 必须保留 `min-height: 0`，否则在 flex 容器内**无法正确滚动**
2. 滚动由**内层**（`.chat-messages` / `.dashboard-content`）承担，**没有**给分栏外层加 `overflow: hidden`
3. 右区切 tab 只切换 `.dashboard-pane` 的 `tab-pane-active`，**左区聊天完全不受影响**
4. 聊天历史按 `agentId` 分开存储；新增智能体只需在 `AGENT_CHAT` / `CHAT_REPLIES` / `MOCK_WEB` 里加一份

---

## 🆕 七·六、R002 实现说明：智能体驾驶舱 v2（决策优先）（2026-09-18）

> 需求文档：`.changes/R002-cockpit-redesign.md`

### 改了什么

`agent-cockpit` 从**6 区纵向堆叠**（274 行）重构为 **3 块布局**（49 行）：

```
┌──────────────────────────────────────────────────────┐
│ 🎯 待我决策 · N 单                    [全部同意 →]    │  ← 最高视觉权重
│   [高] 🚨 苏州金鹏 · 税务非正常户   [✓同意][✗拒绝][详情] │
│   [中] ⚠ 浙江光华 · 法人变更        [✓同意][✗拒绝][详情] │
│   [中] ⚠ 上海林达 · 经营异常        ...                │
│   [低] ✅ 上海永胜 · 4 项主数据缺失  ...                │
├───────────────────────────┬──────────────────────────┤
│ ⚡ 智能体团队运行态        │ 📊 今日事件流             │
│   进度条 + % + ETA        │ 🚨 异常预警               │
│ 🚀 快捷调度（6 入口）      │ 🤖 AI 智能洞察            │
└───────────────────────────┴──────────────────────────┘
```

### 🚫 硬约束：本面板禁止资金数字

整个 `agent-cockpit` **不允许出现 `¥` / 万元 / 亿元 / 敞口 / 挽回损失** 等表述。
只使用业务运营类数字（事件数 / 客户数 / 进度 % / 效率 +12%）。

> 原因见 R002 第 12 节：评委是 AI 财会业务专家，讲业务问题比讲金额更显真实；
> 金额应留给用户在「详情」里看，不该出现在驾驶舱列表。

### 新增 CSS（均限定或专用于 cockpit）

| class | 作用 |
|:--|:--|
| `[data-view-panel="agent-cockpit"] .dash-card` / `.card-header` | **限定作用域**，因其它 panel 也用这两个 class 名（靠 inline style），不能全局定义 |
| `.cockpit-wrap` | 面板容器（padding + `height: calc(100vh - 60px)` + `overflow-y: auto`） |
| `.cockpit-decision-zone` | 决策区（红色左边框 4px + 阴影 = 最高权重） |
| `.decision-card` / `.priority-high\|mid\|low` / `.decision-tag` | 决策卡与优先级色块（红/金/绿） |
| `.btn-decision` / `.btn-approve` / `.btn-reject` / `.btn-detail` | 决策按钮 |
| `.cockpit-grid` / `.cockpit-col` | 中部左右分栏 |
| `.agent-progress-*` | 运行态进度条 |
| `.event-item` / `.event-status.done\|paused\|warn` | 事件流 |
| `.mini-list` / `.mini-list-item.urgent\|warning` | 预警与洞察列表 |
| `.quick-actions` | 快捷调度 3×2 网格 |
| `#toast` / `#toast.show` | 全局轻提示（新增，R002 引入） |

### 新增 JS

| 名称 | 作用 |
|:--|:--|
| `DECISIONS` / `AGENTS_RUNNING` / `TODAY_EVENTS` / `ALERTS` / `INSIGHTS` / `QUICK_ACTIONS` | 驾驶舱数据源 |
| `renderDecisions()` | 渲染决策卡（含优先级 tag 与三个按钮） |
| `renderAgentsRunning()` / `renderTodayEvents()` / `renderMiniList()` / `renderQuickActions()` | 各区块渲染 |
| `bindCockpit()` | 事件委托：**点卡片 → 复用 `openModal()` 打开依据**；单卡同意/拒绝；一键全部同意；快捷调度 toast |
| `showToast(msg)` | 轻提示（新引入；之前项目里没有） |

### 单屏验证（1440×900）

实测：`.cockpit-wrap` 的 `scrollHeight === clientHeight`（**overflow = 0**），
决策区 300px + 中部 407px 恰好填满，**无需滚动**。

### ⚠️ 实现注意

1. 决策卡「详情」与点卡片本体都走 `openModal(eventId)`，复用既有 `EVIDENCE` 数据
2. `[data-view-panel="agent-cockpit"]` 前缀的 CSS 是**故意加的作用域**——去掉会影响 `tool-mgr` 等同样使用 `.dash-card` 的 panel
3. 决策数据里 `eventId` 需对应 `EVIDENCE` 的 key（当前用 event-1/2/5/3）

---

## 🆕 七·七、R003 实现说明：驾驶舱聚焦运行态 + 数据看板扩展（2026-09-18）

> 需求文档：`.changes/R003-cockpit-focus.md`

### 核心变化：信息分类归位

| 内容类型 | 改动前（R002） | 改动后（R003） |
|:--|:--|:--|
| 运行态（AI 在做什么） | 驾驶舱中部左列 | **驾驶舱**（3 列网格，唯一归属） |
| 待我决策 / 异常预警 / 事件流 | 驾驶舱 + 无 | **数据看板**（唯一归属，驾驶舱不再出现） |

> **去重原则**：同一信息只在**一处**出现。驾驶舱禁止出现「待我决策 / 异常预警 / 事件流」。

### agent-cockpit（v3 · 纯运行态）

```
┌──────────────────────────────────────────────────────┐
│ 📊 团队总览              3 在线 · 0 异常 · 5 任务运行中 │
├───────────────┬───────────────┬──────────────────────┤
│ ⚠️ 风险预警官  │ ⚡ 智能开票专员 │ 🔧 客户补全专员       │
│ 当前任务/进度条│ ...           │ ...                  │
│ ETA 16:00     │ ETA 18:30     │ 待你拍板              │
│ 已完成 ✓ ...   │ 已完成 ✓ ...   │ 已完成 ✓ ...          │
│ 待执行 ⏸ ...   │ 待执行 ⏸ ...   │ 待执行 ⏸ ...          │
│ [⚠️ 进入… →]   │ [⚡ 进入… →]   │ [🔧 进入… →]          │
├───────────────┴───────────────┴──────────────────────┤
│ 📅 今日完成 · 287 张发票 · 5 家风险预警 · 18 项主数据   │
└──────────────────────────────────────────────────────┘
```

- 数据源：`COCKPIT_AGENTS`，由 `renderCockpitAgents()` 渲染
- `[进入 X]` 按钮通过 `data-go` → `switchView()` 跳转到对应 panel
- **实测单屏**：1440×900 下 `.cockpit-wrap` 的 `scrollHeight === clientHeight`（overflow = 0）

### data-board（新增 3 个区，位于区 1「今日总览」之后）

| 区 | 内容 | 容器 id | 视觉权重 |
|:--|:--|:--|:--|
| 区 2 | 🚨 待我决策（4 张卡，优先级色块 + 同意/拒绝/详情） | `#board-decision-list` | `.weight-high`（红左边框+阴影） |
| 区 3 | 🚨 异常预警 + 🤖 AI 智能洞察（左右两列） | `#board-alerts` / `#board-insights` | `.weight-mid` |
| 区 4 | 📊 今日事件流 | `#board-events` | 默认 |

原区 2~5（客户信息补全 / 自动开票 / 公司风险监控 / 定时简报）**未改动**。

### ⚠️ 已知偏差（待产品确认）

R003 验收要求「数据看板单屏内能看完（或最多 1 次微滚）」，**实测无法满足**：

| 版本 | 数据看板 overflow（1440×900） |
|:--|:--|
| R002（改动前） | 490px（**本已超屏**） |
| R003（改动后） | 1150px（新区贡献约 660px） |

新区已按紧凑设计（决策卡单行、预警/洞察并排两列），但**看板在 R003 之前就已经超出单屏**，
在不删减既有内容的前提下无法做到单屏。已如实记录，保留完整功能。

### 新增 CSS

`.cockpit-agent-card`（+ `.agent-risk/.agent-billing/.agent-complete` 左边框配色）、
`.cockpit-overview` / `.cockpit-footer` / `.cockpit-agent-*`、
`.board-zone`（+ `.weight-high/.weight-mid`）、`.board-two-col`、
`.dash-decision-card`（+ 优先级变体）、`.dashboard-event-item`。

### 顺带修复

R002 插入 JS 时留下了一个**重复的 `</script>` 闭合标签**（`</script></script>`），本次一并清理，
现全文档 `</script>` 唯一。

---

## 🆕 七·八、R004 实现说明：数据看板 v2（仪表盘风格）（2026-09-18）

> 需求文档：`.changes/R004-databoard-redesign.md`

### 原则：只调布局，不删内容

本轮的**唯一目标**是重新组织 `data-board` 的布局。**没有删除、精简或替换任何信息**
（3 金光场景的 KPI / 列表 / 折线图 / 按钮全部原样保留）。

### 6 个区（自上而下）

| 区 | 内容 | 视觉权重 |
|:--|:--|:--|
| 区 1 | 今日 AI 工作报告（4 KPI，数字滚动） | ⭐⭐⭐ |
| 区 2 | 🚨 待我决策（4 张**整行**大卡） | ⭐⭐⭐⭐⭐ `.weight-high` |
| 区 3+4 | **双列 1:1**：左=🚨 异常预警 + 📊 今日事件流；右=🤖 AI 智能洞察 | ⭐⭐⭐⭐ / ⭐⭐⭐ |
| 区 5 | **业务全景**：🔧 客户信息补全 / ⚡ 自动开票 / 🛡 公司风险监控（垂直堆叠，内容完整） | ⭐⭐⭐⭐ |
| 区 6 | ⏰ 今日定时简报（一行 3 项） | ⭐⭐ |

### 本轮改了什么

1. **区 3 + 区 4 合并为双列**：原先「异常预警+AI洞察」一个双列区、「今日事件流」独立一区；
   现改为**一个** `.board-two-col` 网格 —— 左列 = 异常预警 + 今日事件流（同卡片内上下排列），
   右列 = AI 智能洞察。消除了原先「事件流在宽区里只占一小条」的空洞感。
2. **决策卡改为整行三行式**：`标题（含优先级标签） / 影响 / AI 建议` 三行 + 右侧按钮组，
   与 R004 6.2 一致（原先「影响」与「AI 建议」挤在一行）。
3. **新增「业务全景」区标题**，统摄 3 张金光场景大卡。
4. 定时简报本就是一行 3 项，未改动。

### 新增/调整 CSS

```css
.dash-decision-card { align-items: flex-start; }                 /* 三行式改为顶对齐 */
.dash-decision-card .decision-card-title { display:flex; align-items:center; gap:7px; }
.decision-card-meta { ... line-height: 1.5; }
```

> 沿用 R003 已有的 `.board-zone` / `.board-zone-title` / `.board-two-col` / `.weight-high` 等，
> 未引入新颜色或新依赖。

### ⚠️ 关于页面高度

数据看板在 1440×900 下 `overflow ≈ 1240px`（R003 时为 1150px）。R004 第 8 节已预见
「3 金光场景保持完整可能滚动更多」，并明确以「保留全部信息」优先，故此处以滚动换取内容完整。

---

## ⚠️ 八、已知问题（建议优先解决）

### 高优先级

1. ~~**JS 报错**：`Cannot read properties of null (reading 'classList')`~~ ✅ **已修复 2026-09-18**
   - 根因：页面不存在 `[data-panel]` 元素，`querySelector` 返回 null 后直接取 `.classList`
   - 已加判空；另修复了 `history-1` 闭合错位、`tool-mgr` 游离在 `<main>` 外两处结构问题
   - 遗留：`.agent-item` 处理器与 `[data-view]` 处理器功能重复，可择机删除（非必要）

2. ~~**聊天无交互**~~ ✅ **已修复 2026-09-18（R001）**
   - `agent-billing` / `agent-risk` 两个智能体的聊天区已可发送并收到模拟回复
   - 注意：**其它 panel** 底部的聊天框（`new-task` / `history-*`）仍是装饰性 textarea，未接入

3. **数据全写死**
   - 现象：287 张开票、5 家高风险等数字都是硬编码
   - 建议：抽出到顶部 `const MOCK_DATA = { ... }`

### 中优先级

4. **inline style 泛滥**：每个 panel 用 `style="..."`，难以维护
   - 建议：抽 CSS class（`.chat-area / .kpi-grid / .agent-status-card`）

5. **sidebar 三个固定菜单**：智能体管理 + 用户区 + 模式切换
   - 建议：不要随意添加固定项，会挤压中间内容

6. **数据看板数字不滚动**
   - 已有 `animateCounter` 函数（数字 0 → 真实值），但目前没触发
   - 修复：在 switchView 中调用

### 低优先级

7. 移动端 sidebar 不折叠
8. 历史对话 panel 长内容溢出
9. 模态框 (modal) 关闭后滚动锁

---

## 🛠 九、推荐的优化方向

### 短期（保持单文件）
1. 抽出顶部 mockData 常量
2. 把 inline style 抽 CSS class（可读性 +10x）
3. 修复 JS classList 报错
4. 让聊天可以发送（mock 流式回复）
5. 数据看板数字滚动生效

### 中期（拆文件）
```
demos/jinguang.html  →  demos/index.html
                     +  demos/css/style.css
                     +  demos/css/components.css
                     +  demos/js/app.js
                     +  demos/js/data.js (mockData)
                     +  demos/js/chat.js
                     +  demos/js/dashboard.js
```

### 长期（升级框架）
- Vue 3 + Vite + Pinia + TypeScript
- 接入真实 LLM API
- 响应式 + 移动端适配
- 路由系统 (vue-router)
- 单元测试（vitest）

---

## 🔍 十、调试技巧

### 快速定位 panel 问题

```js
// 在浏览器 console 中运行：
Array.from(document.querySelectorAll('[data-view-panel]')).forEach(p => {
  const r = p.getBoundingClientRect();
  console.log(p.dataset.viewPanel, {
    height: p.offsetHeight,
    visible: p.offsetParent !== null,
    active: p.classList.contains('active'),
    parent: p.parentElement.tagName,
    parentDisplay: window.getComputedStyle(p.parentElement).display
  });
});
```

### 检查 div 平衡

```bash
grep -c '<div' demos/jinguang.html   # 计数
grep -c '</div>' demos/jinguang.html
# 两个数字必须相等
```

### 启动本地预览

```bash
cd /tmp/demo-gallery
python3 -m http.server 7777
# 访问 http://localhost:7777/demos/jinguang.html
```

---

## 📞 十一、联系信息

- **当前负责 Agent**：Cofier (我)
- **接手时**：可继续用同样的工具集（terminal / patch / write_file / playwright）
- **用户在飞书**：金光的实际 demo 演示由用户本人进行

---

## ✅ 十二、接手 Checklist

接手时按这个顺序：

- [ ] 阅读本文件（5 分钟）
- [ ] 看 4 个截图：v43-agent-billing / v43-agent-risk / v41-databoard / v41-history1（5 分钟）
- [ ] 读 4 个设计文档（10 分钟）
- [ ] 在本地起服务，浏览所有 panel（10 分钟）
- [ ] 用上面"调试技巧"的 JS 在 console 跑一次（5 分钟）
- [ ] 找到你最想优先解决的问题（5 分钟）
- [ ] 动手改代码

总计：~45 分钟 ready。

---

**最后一点**：这是个**比赛 Demo**，目标观众是**财务人员 + 评委**，不是开发者。所以：

- **视觉效果 > 代码优雅**
- **"看起来真实" > 真实数据**
- **故事性 > 完整性**

每个 panel 都要让观众相信"AI 真的能干活"。