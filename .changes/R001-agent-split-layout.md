# 需求变更 R001：智能体左右分栏布局

## 1. 背景

### 现状
当前金光 Demo 的两个业务智能体（智能开票专员 / 风险预警官）的驾驶舱是 **单列布局**：

```
顶部 bar（标题 + 状态）
↓
状态卡（深墨渐变 + 大数字）
↓
4 KPI 卡
↓
事件流
↓
AI 智能建议
↓
底部聊天区
```

### 问题
1. **信息密度过高**：状态卡 + KPI + 事件流 + 建议 + 聊天区全部纵向堆叠，需要滚动很长才能看完
2. **聊天与内容冲突**：用户要看 chat 历史时，被动事件/KPI 等"驾驶舱内容"被推到下面
3. **不能同时操作**：用户想跟 AI 聊"查苏州金鹏 ¥96,000 的详情"，但 BI 报表也在同一个滚动流里，互相干扰

### 用户原话
> "我希望用户点击了一个智能体，内容区要分成左右两块，左边是聊天窗口，可以跟智能体聊天，右边是页签看板区，可以展示BI报表或是打开某个网页。"

## 2. 目标

### 改完之后
```
用户点击 [⚡ 智能开票专员]
        ↓
内容区变成 [左 50% | 右 50%] 分栏
        ↓              ↓
    聊天窗口         页签看板区
    (含聊天历史)    (默认 BI 报表 tab)
                    (可切换：报表 / 资料库 / 网页)
```

### 核心体验
- **左区**（聊天）：专注跟 AI 对话，能看到完整对话历史 + 实时输入
- **右区**（看板）：聚焦数据和文档，多 tab 切换，能展示 BI 报表 + 嵌入网页
- **横向看 / 不用滚动**：状态卡 / KPI / 事件流都在右区一次性看到

## 3. 验收标准

### 视觉层面
- [ ] 内容区分成左右两块，宽度比 50:50（或可配置）
- [ ] 左区有完整聊天窗口：顶部标题 + 历史气泡 + 底部输入框
- [ ] 右区顶部有 tab 栏（至少 3 个 tab）
- [ ] tab 栏下方显示对应内容
- [ ] 右区内容变化时，左区聊天区不受影响（不滚动 / 不消失）

### 功能层面
- [ ] 点击左侧菜单的 [⚡ 智能开票专员]，内容区变成左右分栏
- [ ] 点击左侧菜单的 [🛡 风险预警官]，也是左右分栏
- [ ] 右区 tab 切换时，左区聊天不受影响
- [ ] 左区聊天能发送消息（即使没接 LLM，UI 上也能看到气泡出现）
- [ ] 默认第一个 tab 是 BI 报表（事件流 + KPI + AI 建议）
- [ ] 至少有一个 tab 能展示网页（iframe 内嵌）

### 交互层面
- [ ] 切换两个智能体时，聊天历史可独立保存（agent-billing 的对话和 agent-risk 的对话分开）
- [ ] tab 切换是动画的（不要闪屏）
- [ ] 分隔线可以拖动（可选，默认 50:50 即可）

### 不破坏
- [ ] 其它 panel（new-task / history-* / data-board / agent-cockpit / tool-mgr）布局不变
- [ ] sidebar 不变
- [ ] 工作台模式（工作台子菜单下的视图）布局不变
- [ ] 没有新的 JS error（classList null 那个要保持不再出现）

## 4. 涉及范围

### 需要改的文件
- `demos/jinguang.html` —— 主要修改

### 需要改的 panel（2 个）
- `agent-billing`（智能开票专员）—— 改为左右分栏
- `agent-risk`（风险预警官）—— 改为左右分栏

### 不需要改的 panel
- `agent-cockpit`（智能体驾驶舱）—— 保持 6 区不变
- `new-task`（新任务）—— 已有中央欢迎 + 输入区
- `history-1 ~ 5`（历史对话）—— 已经是聊天窗口
- `data-board`（数据看板）—— 保持 4 区 5 卡
- `tool-mgr`（智能体管理）—— 保持 3 区
- `skill` / `tool-cap` / `tool-kb` / `schedule-1` —— 保持不变

### 不需要改的 sidebar 项
- 所有 sidebar 项
- 包括模式切换、业务智能体列表、智能体管理、用户区

## 5. 技术约束

### 来自 HANDOFF.md（必须遵守）

1. **单滚动容器**：每个 panel 自己 `overflow-y: auto`，**不要**在外层加 `overflow: hidden`
2. **不要破坏 `.views-container { display: contents }`**：否则 panel 嵌套会出问题
3. **sidebar 用户区贴底**：`.sidebar-user { margin-top: auto }` 不能动
4. **不要嵌套 panel**：不要在另一个 panel 里放 panel
5. **新增 sidebar 项要标 `data-mode-content` 或 `data-mode-section`**：这次新增右区 tab，**不是 sidebar 项**，所以不需要

### 通用约束

- **样式复用**：使用现有 CSS 变量（`--paper / --canvas / --line / --ink-* / --brass / --danger / --success`）
- **不引入新依赖**：保持纯 HTML + 原生 JS
- **代码风格**：与现有 panel 一致（顶部 bar + 状态区 + 滚动区 + 底部固定区）

## 6. 实现方案（建议）

### 6.1 总体结构（每个智能体 panel）

```html
<div class="view-panel" data-view-panel="agent-billing">
  <div class="agent-split-layout">  <!-- 新加的容器，左右分栏 -->
    
    <!-- 左区：聊天窗口（占 50%） -->
    <div class="agent-split-left">
      <div class="chat-header">⚡ 智能开票专员 · 王二</div>
      <div class="chat-messages">  <!-- 可滚动 -->
        <!-- 历史对话气泡 -->
        <div class="chat-msg chat-msg-ai">...</div>
        <div class="chat-msg chat-msg-user">...</div>
      </div>
      <div class="chat-input-bar">
        <textarea placeholder="..." rows="1"></textarea>
        <button>↑ 发送</button>
      </div>
    </div>
    
    <!-- 中间分隔线（可选） -->
    <div class="agent-split-resizer"></div>
    
    <!-- 右区：页签看板（占 50%） -->
    <div class="agent-split-right">
      <div class="dashboard-tabs">
        <button class="tab-active">📊 BI 报表</button>
        <button>📚 资料库</button>
        <button>🌐 网页</button>
      </div>
      <div class="dashboard-content">
        <!-- 当前 tab 内容 -->
      </div>
    </div>
    
  </div>
</div>
```

### 6.2 三个 tab 内容

**Tab 1（默认）：📊 BI 报表**

把原来 panel 里的内容搬过来：
- 状态卡（深墨渐变 + 大数字进度条）
- 4 KPI 卡（用 grid 4 列）
- 事件流（3-5 条）
- AI 智能建议（1-2 条）

布局：垂直滚动，**顶部固定 tab 栏**。

**Tab 2：📚 资料库**

- 列出该智能体的相关文档
- 例如：智能开票专员 → [发票模板库 / 客户主数据 / 风险案例库 / 法规库]
- 每条可点击进入详情

**Tab 3：🌐 网页**

- iframe 内嵌一个示例页面
- 例如：可以放一个 mock 的"票易通开票后台"或"风险监控平台"
- 顶部有 "在新窗口打开 ↗" 按钮

### 6.3 聊天区实现建议

**聊天历史**（每个智能体独立）：
- 顶部条：智能体名字 + 状态
- 中间：历史对话气泡（AI 在左，人在右）
- 底部：textarea + 发送按钮

**发送逻辑**（最低可用版本）：
```js
function sendChatMessage(agentId, text) {
  // 1. 添加用户消息气泡
  // 2. 500ms 后模拟 AI 回复（用预置回复池或随机）
  // 3. 滚动到底部
}

// 用一个简单的 mock 回复池
const CHAT_REPLIES = {
  'agent-billing': [
    '好的，我帮您查询...',
    '正在从票易通拉取发票...',
    '已开 13 张发票，¥XXX',
  ],
  'agent-risk': [
    '正在扫描...',
    '发现 3 家高风险客户',
    '已生成风险报告',
  ],
};
```

### 6.4 CSS 新增

```css
.agent-split-layout {
  display: flex;
  height: calc(100vh - 60px);
  background: var(--canvas);
}
.agent-split-left, .agent-split-right {
  flex: 1;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
}
.agent-split-resizer {
  width: 4px;
  background: var(--line);
  cursor: col-resize;
}
.chat-messages {
  flex: 1;
  overflow-y: auto;
  padding: 20px 24px;
}
.chat-msg {
  max-width: 80%;
  margin-bottom: 12px;
  padding: 10px 14px;
  border-radius: var(--r);
  font-size: 13px;
  line-height: 1.6;
}
.chat-msg-ai {
  background: var(--paper);
  border: 1px solid var(--line-2);
  margin-right: auto;
}
.chat-msg-user {
  background: var(--ink-1);
  color: var(--paper);
  margin-left: auto;
}
.dashboard-tabs {
  display: flex;
  border-bottom: 1px solid var(--line);
  background: var(--paper);
  padding: 0 24px;
}
.dashboard-tabs button {
  padding: 12px 16px;
  border: none;
  background: transparent;
  border-bottom: 2px solid transparent;
  cursor: pointer;
  font-size: 13px;
}
.dashboard-tabs button.tab-active {
  border-bottom-color: var(--brass);
  color: var(--brass);
  font-weight: 600;
}
```

## 7. 测试要点

### 单元测试
- [ ] 切换到 agent-billing：分栏显示，左 50% / 右 50%
- [ ] 切换到 agent-risk：分栏显示，内容是风险预警官的（不是开票专员）
- [ ] 点右区 "BI 报表" tab：显示原内容（状态卡 + KPI + 事件流）
- [ ] 点右区 "资料库" tab：显示文档列表
- [ ] 点右区 "网页" tab：iframe 加载示例页面
- [ ] 左区输入消息 + 点发送：气泡出现

### 回归测试
- [ ] 所有其它 panel 仍然正常显示（用 test_all.py）
- [ ] 没有 JS console error
- [ ] sidebar user 区仍在最底部
- [ ] div 平衡（`grep -c '<div' == grep -c '</div>'`）

## 8. 风险

### 中风险
1. **宽度比**：50:50 可能不适合所有内容（如 BI 报表数据多，聊天可能也想多占）
   - 缓解：默认 50:50，但 CSS 用 `flex: 1` 可调整
2. **iframe 嵌入**：某些网站禁止 iframe 嵌入
   - 缓解：用一个 mock HTML 作为示例（不需要真实服务）
3. **聊天历史切换智能体会丢**：用户期望每次切换保留历史
   - 缓解：聊天历史用 `data-agent-id` 区分，每个智能体独立存储（可放内存或 localStorage）

### 低风险
4. tab 切换性能：3 个 tab 全部预渲染会有初始加载时间
   - 缓解：按需渲染（首次激活时才创建）
5. 移动端体验：50:50 在小屏幕上体验差
   - 缓解：媒体查询改成纵向堆叠（@media max-width: 768px）

## 9. 验收 checklist

接手 Agent 完成后，按此清单自验：

```
视觉层面
[ ] 内容区分左右两块（50:50）
[ ] 左区有聊天窗口（顶部/历史/底部输入）
[ ] 右区有 tab 栏 + 内容
[ ] 分隔线清晰（不要糊在一起）

功能层面
[ ] 点 ⚡ 智能开票专员 → 左右分栏显示
[ ] 点 🛡 风险预警官 → 左右分栏显示
[ ] 右区 3 个 tab 都能切换
[ ] 左区能发送消息（看到气泡）
[ ] 默认第一个 tab 是 BI 报表

回归层面
[ ] 其它 14 个 panel 正常
[ ] 无 JS error
[ ] sidebar 布局不变
[ ] div 平衡
[ ] HANDOFF.md 已更新（加 R001 实现说明）
```

## 10. 相关文件参考

- 📄 `HANDOFF.md` —— 项目总览 + 设计原则
- 📄 `data-board-design-v2.md` —— BI 报表设计参考（就是 R001 右区 tab 1 的内容）
- 📄 `agent-cockpit-design.md` —— 智能体驾驶舱设计参考（看 6 区结构）
- 🎬 `v43-agent-billing.png` / `v43-agent-risk.png` —— **改之前**的截图，做对比参考

## 11. 建议的实现顺序

1. 先改 CSS（`.agent-split-layout` 等新 class）
2. 改 HTML 结构（左区 chat + 右区 tab + tab 内容）
3. 实现 tab 切换 JS
4. 实现聊天发送 mock 逻辑
5. 测试两个智能体都正常
6. 跑回归测试

**预计工作量**：1-2 小时（如果已有 CSS 基础）。

---

**等待接手 Agent 完成后，再让我跑一遍验收。**