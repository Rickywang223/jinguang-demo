# 需求变更 R004：数据看板 v2（仪表盘风格重新设计）

## 1. 背景

### 现状（R003 后）
R003 在数据看板增加了 3 个新区（待我决策 / 异常预警 / 今日事件流），加上原有的顶部总览 + 3 金光场景，总共 5 个区块。

### 问题（用户反馈："布局很乱"）

1. **3 金光场景太重**：每张卡里嵌套 KPI（4 个）+ 列表 + 操作按钮，"信息爆炸"
2. **区 3 双列内容不平衡**：异常预警 3 条 vs AI 洞察 3 条，列宽浪费
3. **区 4 事件流单条显示**：在 1092px 宽右区里只占一小条，视觉空洞
4. **缺少视觉层级**：5 个区用同样的卡片样式，主次不分明
5. **右侧空间浪费**：区 1 是 4 列 KPI、区 3 是双列，但有些区只用了一半宽度
6. **定时简报在最底部**：用 3 个小条横排，看起来像没设计完

### 用户原话
> "你再重新设计一下数据看板，我感觉布局很乱"

## 2. 目标

### 方案 C（仪表盘风格）：保留所有信息，只调布局

**核心原则**：
- ✅ **保留全部现有信息**（客户补全 / 开票 / 风险监控的所有细节）
- ✅ **决策区最显眼**（垂直大卡，每张占整行）
- ✅ **异常 + 事件流合并**到一列（避免双列浪费）
- ✅ **AI 洞察单独一列**（避免合并到异常里）
- ✅ **3 金光场景保持完整**（评委需要看细节）

### 改完之后

```
┌─────────────────────────────────────────────────────────┐
│ 今日 AI 工作报告（4 KPI 顶部）                     │ ← 全宽，紧凑
├─────────────────────────────────────────────────────────┤
│ 🚨 待我决策（4 张大卡，垂直列表）              │ ← 全宽，最显眼
├─────────────────────────────────────────────────────────┤
│ ┌─预警 + 事件（双列 1:1）─┐  ┌──AI 洞察（单列）─┐│ ← 双区双列
│ │🚨 异常预警（3 条）         │  │ 🔍 洞察1           ││
│ │📊 今日事件流（4 条）      │  │ 📈 洞察2           ││
│ │                           │  │ ⚡ 洞察3           ││
│ └──────────────────────────┘  └───────────────────┘│
├─────────────────────────────────────────────────────────┤
│ 业务全景：3 张完整大卡（垂直堆叠）             │ ← 全宽
├─────────────────────────────────────────────────────────┤
│ ⏰ 今日定时简报（一行 3 项）                          │ ← 全宽，最底部
└─────────────────────────────────────────────────────────┘
```

### 核心体验

| 区 | 内容 | 视觉权重 | 备注 |
|:--|:--|:--|:--|
| 顶部 | 4 KPI | ⭐⭐⭐ | 数字滚动，紧凑 |
| 待我决策 | 4 张大卡 | ⭐⭐⭐⭐⭐ | 最显眼，每张占整行 |
| 双列左 | 异常 + 事件流 | ⭐⭐⭐⭐ | 合并到一列，节省空间 |
| 双列右 | AI 洞察 | ⭐⭐⭐ | 3 条横向/纵向 |
| 业务全景 | 3 张完整大卡 | ⭐⭐⭐⭐ | 保留所有细节 |
| 定时简报 | 3 项一行 | ⭐⭐ | 最底部，最次要 |

## 3. 验收标准

### 视觉层面
- [ ] 顶部 4 KPI 紧凑横向排列
- [ ] 待我决策区视觉权重最高（4 张大卡垂直占整行）
- [ ] 异常 + 事件流合并到左列（双列布局，1:1）
- [ ] AI 洞察单独右列（3 条）
- [ ] 3 金光场景保持完整（垂直堆叠，每张含 KPI + 列表 + 操作按钮）
- [ ] 定时简报在最底部（一行 3 项）

### 功能层面
- [ ] 顶部 4 KPI 数字滚动
- [ ] 待我决策 4 张卡有 [✓同意] [✗拒绝] [详情] 按钮
- [ ] 异常预警显示 3 条（不同优先级）
- [ ] 今日事件流显示 4 条（带时间 + 状态）
- [ ] AI 洞察显示 3 条
- [ ] 3 金光场景保留所有现有内容
- [ ] 定时简报 3 项可点击

### 交互层面
- [ ] 点击决策卡 [详情] 弹出模态框
- [ ] 点击 [全部同意] 一键处理
- [ ] 点击 3 金光场景的 [打开智能体] 跳转
- [ ] 点击定时简报 [查看全部] 跳转

### 不破坏
- [ ] 顶部 4 KPI 内容不变（287 张 / 4.2 小时 / 5 家 / 4 单）
- [ ] 3 金光场景所有嵌套 KPI / 列表 / 折线图 / 操作按钮都保留
- [ ] R001（左右分栏）/ R002 / R003 不受影响
- [ ] 没有 JS error

## 4. 涉及范围

### 需要改的文件
- `demos/jinguang.html` —— 主要修改
- `HANDOFF.md` —— 更新数据看板结构说明

### 需要改的 panel（1 个）
- `data-board` —— 完全重构布局

### 不需要改的 panel（15 个）
所有其它 panel 不动

### 不需要改的 sidebar
- sidebar 布局完全不变

## 5. 技术约束

### 来自 HANDOFF.md（必须遵守）
1. **单滚动容器**：每个 panel 自己 `overflow-y: auto`
2. **不要破坏 `.views-container { display: contents }`**
3. **不要破坏 sidebar `.sidebar-user { margin-top: auto }`**
4. **不要嵌套 panel**
5. **新增 sidebar 项要标 `data-mode-content` 或 `data-mode-section`**（这次不适用）

### 来自 R001（必须遵守）
- 复用 CSS 变量
- 不引入新依赖

### 来自 R002（必须遵守）
- **数据看板允许资金字样 ¥**（业务 KPI 需要）
- **决策类内容只在数据看板出现一次**

### 新增约束（按 R004）
- **保留所有信息**：不要"精简"3 金光场景的内部内容
- **只调布局，不删内容**

## 6. 实现方案

### 6.1 总体布局（6 个区）

```html
<div class="view-panel" data-view-panel="data-board">
  <div style="padding: 24px; background: var(--canvas); min-height: calc(100vh - 60px);">

    <!-- 区 1：顶部 4 KPI -->
    <div class="dash-kpi-strip" style="margin-bottom: 16px;">
      <!-- 4 KPI 紧凑一行 -->
    </div>

    <!-- 区 2：待我决策（最重要） -->
    <div class="board-zone weight-high" style="margin-bottom: 16px;">
      <div class="board-zone-title">🚨 待我决策 · 4 单</div>
      <!-- 4 张大卡，每张占整行 -->
    </div>

    <!-- 区 3 + 4：双列 -->
    <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 16px; margin-bottom: 16px;">

      <!-- 左列：异常预警 + 今日事件流 -->
      <div class="board-zone">
        <div class="board-zone-title">🚨 异常预警</div>
        <!-- 3 条预警 -->
        <div class="board-zone-title">📊 今日事件流</div>
        <!-- 4 条事件 -->
      </div>

      <!-- 右列：AI 智能洞察 -->
      <div class="board-zone">
        <div class="board-zone-title">🤖 AI 智能洞察</div>
        <!-- 3 条洞察 -->
      </div>
    </div>

    <!-- 区 5：业务全景（3 金光场景） -->
    <div style="margin-bottom: 16px;">
      <div class="board-zone-title">业务全景</div>
      <!-- 3 张完整大卡，垂直堆叠 -->
      <div class="dash-card">🔧 客户信息补全 ... 完整内容 ...</div>
      <div class="dash-card">⚡ 自动开票 ... 完整内容 ...</div>
      <div class="dash-card">🛡 公司风险监控 ... 完整内容 ...</div>
    </div>

    <!-- 区 6：定时简报（最底部） -->
    <div class="dash-card">
      <div class="board-zone-title">⏰ 今日定时简报</div>
      <!-- 3 项一行 -->
    </div>

  </div>
</div>
```

### 6.2 各区详细

**区 1：顶部 4 KPI（保留 R001 / R003 设计）**

```html
<div class="dash-kpi-strip">
  <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 14px;">
    <div>
      <div style="font-size: 14px; font-weight: 600;">今日 AI 工作报告</div>
      <div style="font-size: 11px; color: var(--ink-3);">报告期 2026-09-17 22:00 · 实时</div>
    </div>
    <button>↓ 导出今日简报</button>
  </div>
  <div style="display: grid; grid-template-columns: 1fr 1px 1fr 1px 1fr 1px 1fr; padding-top: 12px; border-top: 1px solid var(--line);">
    <!-- 4 KPI 卡片（287 张 / 4.2 小时 / 5 家 / 4 单） -->
  </div>
</div>
```

**区 2：待我决策（垂直 4 张大卡）**

```html
<div class="board-zone weight-high">
  <div class="board-zone-title">
    🚨 待我决策 · 4 单
    <button>全部同意 →</button>
  </div>
  <div class="decision-card priority-high">
    <div style="flex: 1;">
      <div>🚨 [高] 苏州金鹏 · 税务非正常户</div>
      <div>影响：若继续开票，进项转出风险严重</div>
      <div>AI 建议：立即停止开票 + 启动法务催收</div>
    </div>
    <div style="display: flex; gap: 8px;">
      <button>✓ 同意</button>
      <button>✗ 拒绝</button>
      <button>详情</button>
    </div>
  </div>
  <!-- 4 张决策卡（同结构） -->
</div>
```

**区 3 + 4：双列 1:1**

```html
<div style="display: grid; grid-template-columns: 1fr 1fr; gap: 16px;">

  <!-- 左列：异常 + 事件流（合并到一列） -->
  <div class="board-zone">
    <div class="board-zone-title">🚨 异常预警</div>
    <div class="alert-item">🚨 1 家客户已停业（紧急）</div>
    <div class="alert-item">⚠ 2 家客户法人变更</div>
    <div class="alert-item">ℹ 5 家客户工商地址变更（低风险）</div>

    <div class="board-zone-title" style="margin-top: 16px;">📊 今日事件流</div>
    <div class="event-item">14:32 上海永胜 · 主数据补全完成 ✓</div>
    <div class="event-item">11:45 苏州金鹏 · 触发风险拦截 ⏸</div>
    <div class="event-item">10:21 5 家批量 · 主数据批量补全 ✓</div>
    <div class="event-item">09:15 月初盘点 · 认证率盘点完成 ✓</div>
  </div>

  <!-- 右列：AI 洞察 -->
  <div class="board-zone">
    <div class="board-zone-title">🤖 AI 智能洞察</div>
    <div class="insight-item">🔍 增长不收钱（回款率 -2.3pp）</div>
    <div class="insight-item">📈 3 家新增高风险客户，建议重点关注</div>
    <div class="insight-item">⚡ 自动开票效率 +12%</div>
  </div>
</div>
```

**区 5：业务全景（3 张完整大卡，垂直堆叠）**

```html
<div class="dash-card">🔧 客户信息补全
  <!-- 完整保留：智能体名 + 4 KPI + 缺失项分布 + 最近处理列表 + 操作按钮 -->
</div>

<div class="dash-card">⚡ 自动开票
  <!-- 完整保留：智能体名 + 4 KPI + 14 天折线图 + 今日待确认 + 操作按钮 -->
</div>

<div class="dash-card">🛡 公司风险监控
  <!-- 完整保留：智能体名 + 4 KPI + 本月触发事件 + 操作按钮 -->
</div>
```

**区 6：定时简报（一行 3 项）**

```html
<div class="dash-card">
  <div style="display: flex; gap: 16px; padding: 12px 16px; font-size: 12px;">
    <div>✓ 09:00 每日风险扫描</div>
    <div>◯ 17:00 周末应收提醒</div>
    <div>◯ 09:00 每日认证率盘点</div>
    <button style="margin-left: auto;">[查看全部]</button>
  </div>
</div>
```

### 6.3 CSS 调整（最小）

```css
/* 看板区视觉权重 */
.board-zone {
  background: var(--paper);
  border: 1px solid var(--line);
  border-radius: var(--r-lg);
  padding: 18px 22px;
  margin-bottom: 16px;
}
.board-zone.weight-high {
  border-left: 3px solid var(--danger);
}
.board-zone-title {
  font-size: 14px;
  font-weight: 600;
  color: var(--ink-1);
  margin-bottom: 12px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.decision-card {
  padding: 14px 18px;
  border: 1px solid var(--line);
  border-left: 3px solid var(--danger);
  border-radius: var(--r);
  margin-bottom: 10px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.decision-card.priority-high { border-left-color: var(--danger); }
.decision-card.priority-mid { border-left-color: var(--brass); }
.decision-card.priority-low { border-left-color: var(--success); }
.alert-item, .event-item, .insight-item {
  padding: 8px 0;
  border-bottom: 1px solid var(--line-2);
  font-size: 13px;
}
```

## 7. 测试要点

### 单元测试
- [ ] 切到 data-board：6 个区都显示
- [ ] 顶部 4 KPI 数字 + 滚动
- [ ] 待我决策 4 张卡（垂直排列）
- [ ] 双列布局：左列异常+事件流，右列 AI 洞察
- [ ] 3 金光场景：每张含 4 KPI + 列表 + 操作按钮（完整保留）
- [ ] 定时简报在最底部

### 回归测试
- [ ] 顶部 4 KPI 内容不变（287 / 4.2 / 5 / 4）
- [ ] 3 金光场景所有嵌套内容都保留
- [ ] agent-cockpit（R003 改的）正常
- [ ] agent-billing / agent-risk（R001）正常
- [ ] 其它 14 个 panel 正常
- [ ] 无 JS error
- [ ] div 平衡

## 8. 风险

### 中风险
1. **数据看板总长度增加**（3 金光场景保持完整可能滚动更多）
   - 缓解：区 3 + 4 用双列节省高度；定时简报紧凑

### 低风险
2. **决策卡垂直布局占垂直空间**
   - 缓解：每张卡紧凑（标题 + 建议 + 3 按钮一行）

## 9. 验收 Checklist

```
视觉层面
[ ] 顶部 4 KPI 紧凑横向
[ ] 待我决策 4 张大卡垂直
[ ] 双列 1:1（异常+事件流 / AI 洞察）
[ ] 3 金光场景保持完整（垂直堆叠）
[ ] 定时简报在最底部（一行 3 项）

功能层面
[ ] 顶部 4 KPI 内容不变
[ ] 待我决策按钮可点击
[ ] 异常 3 条 + 事件流 4 条
[ ] AI 洞察 3 条
[ ] 3 金光场景操作按钮工作
[ ] 定时简报 3 项可点击

回归层面
[ ] 其它 15 个 panel 正常
[ ] 无 JS error
[ ] div 平衡
[ ] HANDOFF.md 已更新
```

## 10. 相关文件参考

- 📄 `HANDOFF.md`
- 📄 `.changes/R003-cockpit-focus.md`
- 📄 `.changes/R004-mockup-databoard.md`

## 11. 建议的实现顺序

1. **不改内容**：从 R003 当前版本开始，只调整布局结构
2. **区 2 待我决策改为垂直**：每张卡单独一行
3. **区 3 + 4 改为双列 1:1**：异常+事件流合并到左列，AI 洞察到右列
4. **区 5 3 金光场景保持完整**：不变内容，只确保垂直堆叠
5. **区 6 定时简报紧凑**：一行 3 项
6. 跑回归测试

**预计工作量**：1.5-2 小时（比 R003 简单，因为不改内容）

## 12. 约束强化

**🚫 不要做**：

```js
// ❌ 不要"精简"3 金光场景
不要删除嵌套的 KPI
不要删除最近处理列表
不要删除 14 天折线图
不要删除操作按钮

// ❌ 不要换信息源
//   信息源必须是 R003 现状，不要换成假数据
```

**✅ 必须保留**：

- ✅ 顶部 4 KPI（287 张 / 4.2 小时 / 5 家 / 4 单）
- ✅ 待我决策 4 张卡（高/中/中/低 优先级）
- ✅ 异常预警 3 条（紧急/警告/低风险）
- ✅ 今日事件流 4 条（时间 + 公司 + 状态）
- ✅ AI 洞察 3 条
- ✅ 客户信息补全完整内容（智能体名 + 4 KPI + 缺失项分布 + 最近处理）
- ✅ 自动开票完整内容（4 KPI + 14 天折线图 + 待确认）
- ✅ 公司风险监控完整内容（4 KPI + 本月触发事件）
- ✅ 定时简报 3 项

---

**等待接手 Agent 完成后，再让我跑一遍验收。**