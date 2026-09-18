# 需求变更 R002：智能体驾驶舱 v2（决策优先）

## 1. 背景

### 现状（v1 · 6 区堆叠）
当前 `agent-cockpit`（智能体驾驶舱）面板有 6 个区垂直堆叠：

1. 智能体团队状态（3 智能体在线）
2. 待我确认（4 单）
3. 智能体工作卡片（3 张）
4. 正在执行（3 任务进度条）
5. 能力与工具状态（5 工具）
6. 快捷调度（9 个按钮）

### 问题
1. **6 个区都在抢"最重要的位置"**——没有主次
2. **重复信息**：团队状态 + 智能体工作卡 + 正在执行 全是"AI 在干什么"
3. **待我确认（决策）没有视觉权重**——和其他区差不多大，但决策才是 AI 财税产品的核心价值
4. **视口高度 900px 看不全**——需要滚动 3 次（现场展示效果差）
5. **没有事件流和预警**——只看到状态，看不到"出了什么事"

### 用户原话
> "再帮我重新设计一下智能体驾驶舱的内容，现在的好丑"

### 用户后续约束
- "采用方案 A（决策优先）"
- "3，不要提资金的事情"（中间区放事件/预警/AI 智能/提示，不放资金数字）

## 2. 目标

### 改完之后

智能体驾驶舱变成 **3 块布局**：

```
┌──────────────────────────────────────────────────────────┐
│ 🎯 待我决策 · 4 单                       [全部同意 →]  │  ← 最突出
├──────────────────────────────────────────────────────────┤
│ ┌──────────────────────┬─────────────────────────────┐ │
│ │ ⚡ 智能体团队运行态   │ 📊 今日事件流 + 🚨 预警   │ │
│ ├──────────────────────┼─────────────────────────────┤ │
│ │ 🚀 快捷调度（6 入口）  │                             │ │
│ └──────────────────────┴─────────────────────────────┘ │
└──────────────────────────────────────────────────────────┘
```

### 核心体验

| 区 | 内容 | 优先级 |
|:--|:--|:--|
| 顶部大卡（决策）| 4 张待决策项，**最高视觉权重** | ⭐⭐⭐⭐⭐ |
| 中部左列 | 智能体团队运行态 + 快捷调度 | ⭐⭐⭐ |
| 中部右列 | 今日事件流 + 异常预警 + AI 智能 | ⭐⭐⭐⭐ |

### 关键原则
- **决策 = 最高优先级**（现场评委第一个注意的）
- **不出现资金数字**（按用户要求）
- **3 块布局，单屏能看完**（不需要滚动或最多滚 1 次）

## 3. 验收标准

### 视觉层面
- [ ] 顶部"待我决策"区视觉权重最高（最大、最显眼）
- [ ] 中部左右分栏（50:50 或自适应）
- [ ] 不出现 ¥XXX 万元 等资金相关数字
- [ ] 决策项卡片用红/黄/绿色块标识优先级
- [ ] 整体在 1440x900 视口内**不需要滚动**（或最多 1 次微滚）

### 功能层面
- [ ] 点击 [全部同意] 一键处理所有待我决策
- [ ] 每个决策项有 [✓ 同意] [✗ 拒绝] [详情] 按钮
- [ ] 智能体团队运行态显示：智能体名 + 当前任务 + 进度条 + ETA
- [ ] 今日事件流显示：时间 + 公司 + 状态（✓/⏸/⚠）
- [ ] 异常预警显示：紧急事件列表（带类型图标 + 公司）
- [ ] AI 智能显示：洞察列表（关键发现）
- [ ] 快捷调度 6 个入口按钮：⚡扫描 / 🌅晨报 / 📋报告 / 🔍盘点 / 📊合规 / 📤补全

### 交互层面
- [ ] 点击决策项的 [详情] 弹出模态框（沿用 R001 之前的 EVIDENCE modal）
- [ ] 点击快捷调度按钮触发 mock toast 提示
- [ ] 进度条颜色根据状态变化（绿正常/黄警告/红异常）

### 不破坏
- [ ] 其它 panel（agent-billing / agent-risk / data-board / new-task / history-* / tool-mgr）布局不变
- [ ] R001 的左右分栏不受影响（仅 agent-cockpit 改）
- [ ] sidebar 不变
- [ ] 没有新的 JS error
- [ ] R001 验收过的 16 个 panel 仍然能正常显示

## 4. 涉及范围

### 需要改的文件
- `demos/jinguang.html` —— 主要修改
- `HANDOFF.md` —— 更新文档说明新结构

### 需要改的 panel（1 个）
- `agent-cockpit`（智能体驾驶舱）—— 完全重构

### 不需要改的 panel
- 所有其它 15 个 panel（agent-billing / agent-risk / data-board / new-task / history-1~5 / skill / tool-mgr / tool-cap / tool-kb / schedule-1）

### 不需要改的 sidebar
- sidebar 布局完全不变

## 5. 技术约束

### 来自 HANDOFF.md（必须遵守）

1. **单滚动容器**：每个 panel 自己 `overflow-y: auto`，**不要**在外层加 `overflow: hidden`
2. **不要破坏 `.views-container { display: contents }`**
3. **sidebar 用户区贴底**：`.sidebar-user { margin-top: auto }` 不能动
4. **不要嵌套 panel**
5. **新增 sidebar 项要标 `data-mode-content` 或 `data-mode-section`**（这次不是 sidebar 项，不适用）

### 来自 R001（必须遵守）

1. **复用现有 CSS 变量**（不引入新颜色）
2. **不引入新依赖**
3. **代码风格与现有 panel 一致**

### 新增约束（按 R002）

- **不出现资金数字**：整个 agent-cockpit 面板不允许出现 `¥` 符号
- **不允许出现** "亿元 / 万元 / 万元敞口 / 流失" 等任何资金表述
- **可用数字**：事件数 / 客户数 / 任务数 / 百分比（业务运营类）

## 6. 实现方案

### 6.1 总体布局

```html
<div class="view-panel" data-view-panel="agent-cockpit">
  <div style="padding: 24px; background: var(--canvas); height: calc(100vh - 60px); overflow-y: auto;">

    <!-- 区 1：待我决策（最突出） -->
    <div class="dash-card" style="border-left: 4px solid var(--danger); margin-bottom: 16px;">
      <div class="card-header">
        <div>
          <div style="font-size: 16px; font-weight: 600;">🎯 待我决策</div>
          <div style="font-size: 11px; color: var(--ink-3);">AI 等待你拍板的工作 · 按优先级排序</div>
        </div>
        <button>全部同意 →</button>
      </div>
      <!-- 4 张决策卡 -->
      <div class="decision-card">🚨 苏州金鹏 · 税务非正常户</div>
      <div class="decision-card">⚠ 浙江光华 · 法人变更</div>
      <div class="decision-card">⏸ 苏州金鹏 ¥18,500 ... 等等！这里不能放金额！</div>
      <div class="decision-card">✅ 上海永胜 · 4 项主数据</div>
    </div>

    <!-- 区 2：左列（团队运行态 + 快捷调度） -->
    <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 16px;">
      
      <!-- 左：智能体团队 -->
      <div class="dash-card">
        <div class="card-header">⚡ 智能体团队运行态</div>
        <!-- 3 个智能体进度条 -->
        <div>⚠ 风险预警官 · 扫描中 · 67% · ETA 16:00</div>
        <div>⚡ 开票专员 · 开票中 · 89% · ETA 18:30</div>
        <div>🔧 补全专员 · 待确认 · 0/5</div>
      </div>

      <!-- 右：今日事件 + 异常预警 + AI 智能 -->
      <div class="dash-card">
        <div class="card-header">📊 今日事件流 + 🚨 预警</div>
        <!-- 事件流 -->
        <div>14:32 上海永胜 ✓</div>
        <div>11:45 苏州金鹏 ⏸</div>
        <div>10:21 5 家批量 ✓</div>
        <div>09:15 月初盘点 ✓</div>
        
        <!-- 异常预警 -->
        <div>🚨 异常预警</div>
        <div>• 1 家停业（紧急）</div>
        <div>• 2 家法人变更</div>
        
        <!-- AI 智能 -->
        <div>🤖 AI 智能洞察</div>
        <div>• 增长不收钱（回款率 -2.3pp）</div>
        <div>• 3 家新增高风险客户</div>
      </div>

      <!-- 下方：快捷调度（占满整行） -->
      <div class="dash-card" style="grid-column: 1 / -1;">
        <div class="card-header">🚀 快捷调度</div>
        <div class="quick-actions">
          <button>⚡ 一键风险扫描</button>
          <button>🌅 智能体晨报</button>
          <button>📋 集团风控报告</button>
          <button>🔍 全量客户盘点</button>
          <button>📊 月度合规报告</button>
          <button>📤 批量补全</button>
        </div>
      </div>
    </div>
  </div>
</div>
```

### 6.2 CSS 新增

```css
/* 决策卡片（最高优先级） */
.decision-card {
  display: flex;
  align-items: center;
  padding: 14px 18px;
  background: var(--paper);
  border: 1px solid var(--line);
  border-left: 3px solid var(--danger);  /* 优先级色块 */
  border-radius: var(--r);
  margin-top: 10px;
  cursor: pointer;
  transition: all 0.2s;
}
.decision-card:hover {
  border-color: var(--brass);
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(140, 106, 58, 0.08);
}
.decision-card.priority-high { border-left-color: var(--danger); }
.decision-card.priority-mid { border-left-color: var(--brass); }
.decision-card.priority-low { border-left-color: var(--success); }

/* 进度条（智能体运行态） */
.agent-progress-row {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px 0;
}
.agent-progress-bar-mini {
  flex: 1;
  height: 4px;
  background: var(--line);
  border-radius: 2px;
  overflow: hidden;
}
.agent-progress-bar-fill {
  height: 100%;
  background: var(--brass);
  border-radius: 2px;
  transition: width 0.3s;
}

/* 事件流条目 */
.event-item {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 6px 0;
  font-size: 12px;
  border-bottom: 1px solid var(--line-2);
}
.event-item:last-child { border-bottom: none; }

/* 快捷调度按钮网格 */
.quick-actions {
  display: grid;
  grid-template-columns: repeat(6, 1fr);
  gap: 10px;
}
.quick-actions button {
  padding: 10px 8px;
  border: 1px solid var(--line);
  background: var(--paper);
  border-radius: var(--r);
  cursor: pointer;
  font-size: 11px;
  transition: all 0.2s;
}
.quick-actions button:hover {
  border-color: var(--brass);
  color: var(--brass);
}
```

### 6.3 数据结构（建议）

```js
// 决策数据（替代现有 CONFIRMATIONS）
const DECISIONS = [
  {
    id: 'dec-1',
    priority: 'high',  // high | mid | low
    agentId: 'agent-risk',
    agentIcon: '⚠',
    title: '苏州金鹏 · 税务非正常户',
    impact: '若继续开票，进项转出风险严重',  // 不提具体金额
    suggestion: '立即停止开票 + 启动法务催收',
    time: '11:45',
    eventId: 'event-1'  // 关联模态框证据
  },
  {
    id: 'dec-2',
    priority: 'mid',
    agentId: 'agent-risk',
    agentIcon: '⚠',
    title: '浙江光华 · 法人变更',
    impact: '账期策略需要重新评估',
    suggestion: '账期 90→30 天 + 暂缓发货',
    time: '13:18',
    eventId: 'event-2'
  },
  // ... 共 4 条
];

// 智能体运行态数据
const AGENTS_RUNNING = [
  {
    id: 'agent-risk',
    name: '风险预警官',
    icon: '⚠',
    task: '集团风险扫描',
    progress: 67,
    eta: '16:00',
    color: 'var(--brass)'
  },
  {
    id: 'agent-billing',
    name: '智能开票专员',
    icon: '⚡',
    task: '自动开票',
    progress: 89,
    eta: '18:30',
    color: 'var(--success)'
  },
  {
    id: 'agent-complete',
    name: '客户补全专员',
    icon: '🔧',
    task: '待确认',
    progress: 0,
    eta: '待你拍板',
    color: 'var(--line-2)'
  }
];

// 今日事件流
const TODAY_EVENTS = [
  { time: '14:32', company: '上海永胜', status: 'done', text: '主数据补全完成' },
  { time: '11:45', company: '苏州金鹏', status: 'paused', text: '触发风险拦截' },
  { time: '10:21', company: '5 家批量', status: 'done', text: '主数据批量补全' },
  { time: '09:15', company: '月初盘点', status: 'done', text: '认证率盘点完成' }
];

// 异常预警
const ALERTS = [
  { level: 'urgent', icon: '🚨', text: '1 家客户已停业（紧急）' },
  { level: 'warning', icon: '⚠', text: '2 家客户法人变更' },
  { level: 'info', icon: 'ℹ', text: '5 家客户工商地址变更（低风险）' }
];

// AI 智能洞察
const INSIGHTS = [
  { icon: '🔍', text: '增长不收钱（回款率 -2.3pp）' },
  { icon: '📈', text: '3 家新增高风险客户（建议重点关注）' },
  { icon: '⚡', text: '自动开票效率 +12%' }
];
```

### 6.4 JS 交互

```js
// 一键全部同意
document.querySelector('[data-action="approve-all"]').addEventListener('click', () => {
  DECISIONS.forEach(d => {
    showToast(`已决策：${d.title}`);
  });
  document.querySelectorAll('.decision-card').forEach(c => {
    c.style.opacity = '0.5';
    setTimeout(() => c.remove(), 500);
  });
});

// 单个决策同意/拒绝
document.querySelectorAll('.decision-card .btn-decision').forEach(btn => {
  btn.addEventListener('click', (e) => {
    e.stopPropagation();
    const decisionId = btn.closest('.decision-card').dataset.id;
    showToast(btn.classList.contains('btn-approve') ? '已同意' : '已拒绝');
    // 移除卡片
  });
});

// 决策详情（打开模态框）
document.querySelectorAll('.decision-card').forEach(card => {
  card.addEventListener('click', () => {
    const decisionId = card.dataset.id;
    const decision = DECISIONS.find(d => d.id === decisionId);
    if (decision.eventId && EVIDENCE[decision.eventId]) {
      openModal(decision.eventId);
    }
  });
});

// 快捷调度按钮
document.querySelectorAll('.quick-actions button').forEach(btn => {
  btn.addEventListener('click', () => {
    showToast(`已启动：${btn.innerText.trim()}`);
  });
});
```

## 7. 测试要点

### 单元测试

- [ ] 切到 agent-cockpit：3 块布局正确显示
- [ ] 顶部决策区有 4 张卡（高/中/低 3 种优先级色块）
- [ ] 左列显示 3 智能体运行态 + 进度条
- [ ] 右列显示事件流 + 异常预警 + AI 智能
- [ ] 底部快捷调度显示 6 个按钮
- [ ] 整页不出现 ¥XXX 资金数字

### 回归测试

- [ ] agent-billing / agent-risk 的左右分栏（R001）不受影响
- [ ] 其它 14 个 panel 仍然正常
- [ ] 没有 JS console error
- [ ] div 平衡（707=707 或差 ≤ 1）
- [ ] sidebar 布局不变

## 8. 风险

### 中风险

1. **决策卡内容过密**：4 张卡每张要描述 AI 建议 + 影响 + 按钮，可能文字太多
   - 缓解：用一行布局，文字精简到一句话
2. **快捷调度按钮 6 个在 1440 视口**可能挤
   - 缓解：6 列 grid 等分；如果屏幕小，可以改成 3 列 × 2 行

### 低风险

3. **决策卡删除动画**：用户点同意后卡片消失，需要动画过渡
   - 缓解：CSS transition opacity + transform
4. **模态框和决策卡联动**：详情按钮需要重新查 EVIDENCE
   - 缓解：复用 R001 之前的 openModal 函数

## 9. 验收 Checklist

```
视觉层面
[ ] 顶部决策区视觉权重最高
[ ] 中部左右分栏（50:50）
[ ] 不出现 ¥XXX 资金数字
[ ] 决策卡有红/黄/绿色块标识
[ ] 单屏内能看完（或最多 1 次微滚）

功能层面
[ ] [全部同意] 一键处理
[ ] 每个决策项有 [✓][✗][详情]
[ ] 智能体运行态显示进度条 + ETA
[ ] 事件流显示 ✓/⏸/⚠ 状态
[ ] 异常预警按等级分组
[ ] AI 智能显示洞察列表
[ ] 快捷调度 6 个按钮

回归层面
[ ] R001 的左右分栏不受影响
[ ] 其它 15 个 panel 正常
[ ] 无 JS error
[ ] div 平衡
[ ] sidebar 布局不变
[ ] HANDOFF.md 已更新（加 R002 说明）
```

## 10. 相关文件参考

- 📄 `HANDOFF.md` —— 项目总览 + 设计原则
- 📄 `agent-cockpit-design.md` —— 旧设计文档（参考）
- 📄 `.changes/R001-agent-split-layout.md` —— R001 改了什么
- 🎬 `cockpit_now_1.png` / `cockpit_now_2.png` / `cockpit_now_3.png` —— **改之前**的 3 张截图（滚动后）

## 11. 建议的实现顺序

1. **先看 3 张 `cockpit_now_*.png` 截图**，理解"丑"在哪里
2. 删除旧的 `agent-cockpit` 6 区结构
3. 改 HTML：3 块布局（决策区 + 左右分栏 + 快捷调度）
4. 加 CSS（decision-card / progress-bar / quick-actions）
6. 加 JS 交互（一键同意 + 单卡同意/拒绝 + 详情弹窗）
7. 测试：单屏显示、按钮点击、详情弹窗
8. 跑回归：R001 还在，其它 panel 不动

**预计工作量**：1.5-2.5 小时（比 R001 复杂，因为是完全重构）

## 12. 约束强化

**🚫 严格禁止**：

```js
// ❌ 错误示例
"¥96万敞口"
"进项转出 96 万"
"若错过本月申报，预估多缴增值税 ¥45.6万"
"挽回损失 33.7万"
"成本 4 小时"
"累计开票 ¥4.36亿"
```

**✅ 正确示例**：

```js
// ✅ 业务运营类
"4 项主数据缺失"
"已停业 3 天"
"法人变更信号"
"回款率 -2.3pp"
"自动开票效率 +12%"
"扫描进度 67%"
```

**原因**：
- 评委是 AI 财会业务专家，**讲业务问题不讲数字**反而显得真实
- **AI 财税产品的核心是"发现问题 + 建议 + 决策"**，不是"算账"
- 财务决策的"金额"应该是用户在详情里看到的，不应该在驾驶舱列表里

---

**等待接手 Agent 完成后，再让我跑一遍验收。**