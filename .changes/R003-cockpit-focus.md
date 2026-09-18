# 需求变更 R003：智能体驾驶舱 v3（X+Y 合并）+ 数据看板扩展

## 1. 背景

### 现状（v2 · R002）
R002 把驾驶舱改成"决策优先"3 块布局：
1. 顶部"待我决策"（4 单大卡）
2. 中部左列：智能体团队运行态 + 快捷调度
3. 中部右列：今日事件流 + 异常预警 + AI 智能洞察

### 问题
**用户反馈**：驾驶舱内容太多，应该聚焦"AI 在做什么"，决策类内容应该挪到数据看板

具体问题：
1. **驾驶舱混了两类内容**：状态类（AI 在做什么）+ 决策类（要我做什么）
2. **决策类内容位置不对**：决策是给财务"做事情"看的，应该在工作流前面（数据看板）
3. **驾驶舱定位模糊**：既不像"驾驶台"（快速看状态），也不像"工作台"（做事）

### 用户原话
> "我现在只需要把智能体的运行状态等相关内容展示出来即可，正在运行哪些任务，其余功能需要用户决策的，比如事件流、异常预警、待我决策的部分，可以放到工作台前面的数据看板中"

## 2. 目标

### 改完之后

**智能体驾驶舱 = 纯运行态监控**（一屏可见 3 智能体对比）

```
┌──────────────────────────────────────────────────────────┐
│ 🪖 智能体驾驶舱                              ⚙ 配置中心 │
├──────────────────────────────────────────────────────────┤
│ 📊 团队总览 · 3 智能体在线 · 0 异常 · 5 任务运行中   │
├──────────────────────────────────────────────────────────┤
│ ┌────────────┬────────────┬────────────┐               │
│ │ ⚠ 风险预警官│ ⚡ 开票专员 │ 🔧 补全专员│               │
│ │ 67%       │ 89%        │ 0%        │               │
│ │ ETA 16:00 │ ETA 18:30  │ 待你拍板    │               │
│ │ ✓ 5 家预警 │ ✓ 287 张发票│ ✓ 18 项数据│               │
│ │ ⏸ 116 家剩余│ ⏸ 33 张剩余│ ⏸ 5 家待确认│               │
│ ├────────────┼────────────┼────────────┤               │
│ │ [⚠ 进入]  │ [⚡ 进入]  │ [🔧 进入]  │               │
│ └────────────┴────────────┴────────────┘               │
├──────────────────────────────────────────────────────────┤
│ 📅 今日完成 · 287 张发票 · 5 家风险预警 · 18 项主数据   │
└──────────────────────────────────────────────────────────┘
```

**数据看板 = 决策 + 业务全景**

```
┌──────────────────────────────────────────────────────────┐
│ 区 1：顶部总览（4 KPI 数字滚动）        ← 保留 R001  │
├──────────────────────────────────────────────────────────┤
│ 区 2：🚨 待我决策（4 张大卡）           ← 新增（从 R002 │
│                                              驾驶舱搬来）│
├──────────────────────────────────────────────────────────┤
│ 区 3：🚨 异常预警                      ← 新增（从 R002  │
│                                              驾驶舱搬来）│
├──────────────────────────────────────────────────────────┤
│ 区 4：📊 今日事件流                     ← 新增（从 R002  │
│                                              驾驶舱搬来）│
├──────────────────────────────────────────────────────────┤
│ 区 5：3 个金光场景（保留现有）         ← 保留 R001 │
└──────────────────────────────────────────────────────────┘
```

## 3. 验收标准

### 视觉层面（驾驶舱）
- [ ] 3 列网格布局（不是单列详情）
- [ ] 每个智能体卡片包含：图标 + 名字 + 任务名 + 进度 + ETA + 已完成 + 待执行 + 操作按钮
- [ ] 进度条颜色根据状态变化（绿/黄/红）
- [ ] 整体在 1440x900 视口内**不需要滚动**
- [ ] 不出现 ¥XXX 资金数字（保持 R002 约束）
- [ ] 不出现决策类内容（待我决策 / 异常预警 / 事件流都不在驾驶舱）

### 视觉层面（数据看板）
- [ ] 数据看板原有 4 个区都还在（顶部总览 / 客户补全 / 自动开票 / 公司风险监控 / 定时简报）
- [ ] 新增"待我决策"区在顶部总览下方
- [ ] 新增"异常预警"区在待我决策下方
- [ ] 新增"今日事件流"区在异常预警下方
- [ ] 数据看板单屏内能看完（或最多 1 次微滚）

### 功能层面（驾驶舱）
- [ ] 顶部"团队总览"显示：3 在线、0 异常、5 任务运行中
- [ ] 每个智能体显示真实进度（从 mock data 来）
- [ ] 每个智能体显示 ETA
- [ ] 每个智能体显示已完成项 + 待执行项
- [ ] 点击 [⚠ 进入风险预警官] → 切换到 agent-risk panel（R001 已有）
- [ ] 点击 [⚡ 进入开票专员] → 切换到 agent-billing panel
- [ ] 点击 [🔧 进入补全专员] → 切换到对应 panel（或跳到 tool-mgr）

### 功能层面（数据看板）
- [ ] 待我决策区显示 4 张决策大卡
- [ ] 异常预警区显示 3+ 条预警
- [ ] 今日事件流显示 4+ 条事件
- [ ] 各区视觉权重递减（顶部总览 > 决策 > 预警 > 事件流 > 业务场景）

### 交互层面
- [ ] 驾驶舱 + 数据看板之间的内容**互不重复**（同一信息不在两处出现）
- [ ] 驾驶舱的"进入 X"按钮工作
- [ ] 数据看板新区的决策按钮工作（沿用 R001 的 EVIDENCE modal）

### 不破坏
- [ ] R001 的左右分栏（agent-billing / agent-risk）仍然正常
- [ ] 其它 13 个 panel 正常
- [ ] sidebar 不变
- [ ] 没有 JS error

## 4. 涉及范围

### 需要改的文件
- `demos/jinguang.html` —— 主要修改（2 个 panel：agent-cockpit + data-board）
- `HANDOFF.md` —— 更新驾驶舱结构说明

### 需要改的 panel（2 个）

| panel | 改动 |
|:--|:--|
| `agent-cockpit` | 完全重构（去掉决策类内容，加运行态 3 列） |
| `data-board` | 增加 3 个新区（待我决策 / 异常预警 / 今日事件流） |

### 不需要改的 panel（14 个）
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
- 整个 agent-cockpit **不允许出现 ¥XXX 资金字样**
- 但 **data-board 数据看板允许出现数字**（这是业务 KPI 看板，数字是核心）

### 新增约束（按 R003）
- **内容去重**：决策 / 异常 / 事件流只在数据看板出现一次，不在驾驶舱再列
- **驾驶舱聚焦运行态**：只展示"AI 在做什么"，不展示"AI 发现了什么需要我做什么"

## 6. 实现方案

### 6.1 驾驶舱 agent-cockpit

```html
<div class="view-panel" data-view-panel="agent-cockpit">
  <div style="padding: 24px; background: var(--canvas); height: calc(100vh - 60px); overflow-y: auto;">

    <!-- 顶部总览条 -->
    <div style="display: flex; justify-content: space-between; align-items: center; padding: 12px 16px; background: var(--paper); border: 1px solid var(--line); border-radius: var(--r-lg); margin-bottom: 16px;">
      <div style="font-size: 14px; font-weight: 600; color: var(--ink-1);">📊 团队总览</div>
      <div style="display: flex; gap: 24px; font-size: 12px;">
        <span>3 在线</span>
        <span>0 异常</span>
        <span>5 任务运行中</span>
      </div>
    </div>

    <!-- 3 列网格：智能体卡片 -->
    <div style="display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 16px; margin-bottom: 16px;">

      <!-- 卡片 1：风险预警官 -->
      <div class="dash-card cockpit-agent-card">
        <div class="card-header">⚠️ 风险预警官</div>
        <div style="font-size: 11px; color: var(--ink-3); margin-bottom: 8px;">集团风险扫描</div>

        <!-- 当前任务 -->
        <div style="font-size: 11px; color: var(--ink-3); margin-bottom: 4px;">当前任务</div>
        <div style="font-size: 13px; color: var(--ink-1); margin-bottom: 6px;">
          扫描 234 / 350 家 · 进度 67%
        </div>
        <div style="height: 6px; background: var(--line); border-radius: 3px; overflow: hidden; margin-bottom: 6px;">
          <div style="height: 100%; width: 67%; background: var(--brass); border-radius: 3px;"></div>
        </div>
        <div style="font-size: 11px; color: var(--ink-3); margin-bottom: 12px;">ETA 16:00</div>

        <!-- 已完成 -->
        <div style="font-size: 11px; color: var(--ink-3); margin-bottom: 4px;">已完成</div>
        <div style="font-size: 12px; margin-bottom: 8px;">
          <div>✓ 触发 5 家高风险预警</div>
          <div>✓ 生成风险周报（草稿）</div>
        </div>

        <!-- 待执行 -->
        <div style="font-size: 11px; color: var(--ink-3); margin-bottom: 4px;">待执行</div>
        <div style="font-size: 12px; margin-bottom: 14px;">
          <div>⏸ 116 家剩余</div>
        </div>

        <!-- 操作按钮 -->
        <button style="width: 100%; height: 32px; ...">⚠️ 进入风险预警官 →</button>
      </div>

      <!-- 卡片 2：智能开票专员 -->
      <div class="dash-card cockpit-agent-card">
        <!-- 类似结构 -->
      </div>

      <!-- 卡片 3：客户补全专员 -->
      <div class="dash-card cockpit-agent-card">
        <!-- 类似结构 -->
      </div>
    </div>

    <!-- 底部统计条 -->
    <div style="display: flex; gap: 16px; padding: 12px 16px; background: var(--paper); border: 1px solid var(--line); border-radius: var(--r); font-size: 12px;">
      <span>📅 今日完成 · 287 张发票</span>
      <span>· 5 家风险预警</span>
      <span>· 18 项主数据</span>
    </div>

  </div>
</div>
```

### 6.2 数据看板 data-board（新增 3 个区）

```html
<div class="view-panel" data-view-panel="data-board">
  <div style="padding: 24px; background: var(--canvas); min-height: calc(100vh - 60px);">

    <!-- 区 1：顶部总览（保留 R001） -->
    <div class="dash-kpi-strip">...</div>

    <!-- 区 2：🚨 待我决策（新增） -->
    <div class="dash-card">
      <div class="card-header">🚨 待我决策 · 4 单</div>
      <!-- 4 张大卡（沿用 R002 设计） -->
      <div class="decision-card">🚨 苏州金鹏 · 税务非正常户</div>
      <div class="decision-card">⚠ 浙江光华 · 法人变更</div>
      <!-- ... -->
    </div>

    <!-- 区 3：🚨 异常预警（新增） -->
    <div class="dash-card">
      <div class="card-header">🚨 异常预警</div>
      <!-- 3+ 条预警 -->
    </div>

    <!-- 区 4：📊 今日事件流（新增） -->
    <div class="dash-card">
      <div class="card-header">📊 今日事件流</div>
      <!-- 4+ 条事件 -->
    </div>

    <!-- 区 5：3 个金光场景（保留 R001） -->
    <div class="dash-card">客户信息补全</div>
    <div class="dash-card">自动开票</div>
    <div class="dash-card">公司风险监控</div>

    <!-- 定时简报（保留 R001） -->
    <div>📅 今日定时简报...</div>

  </div>
</div>
```

### 6.3 CSS 新增

```css
/* 驾驶舱智能体卡片 */
.cockpit-agent-card {
  display: flex;
  flex-direction: column;
  border-left: 3px solid var(--brass);
  padding: 16px;
}

.cockpit-agent-card.agent-risk { border-left-color: var(--danger); }
.cockpit-agent-card.agent-billing { border-left-color: var(--success); }
.cockpit-agent-card.agent-complete { border-left-color: var(--ink-3); }

/* 数据看板的决策卡（沿用 R002） */
.dash-decision-card {
  padding: 14px 18px;
  border: 1px solid var(--line);
  border-left: 3px solid var(--danger);
  border-radius: var(--r);
  margin-bottom: 10px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.dash-decision-card.priority-high { border-left-color: var(--danger); }
.dash-decision-card.priority-mid { border-left-color: var(--brass); }
.dash-decision-card.priority-low { border-left-color: var(--success); }

/* 数据看板的事件/预警条目 */
.dashboard-event-item {
  display: flex;
  gap: 10px;
  padding: 8px 0;
  border-bottom: 1px solid var(--line-2);
  font-size: 13px;
}
```

### 6.4 JS 交互（沿用 R002 + 新增跳转）

```js
// 进入智能体按钮
document.querySelectorAll('.cockpit-agent-card button[data-go]').forEach(btn => {
  btn.addEventListener('click', () => {
    const targetView = btn.dataset.go; // 'agent-risk' / 'agent-billing'
    switchView(targetView);
  });
});

// 数据看板的决策卡（沿用 R002）
// 数据看板的事件流 + 预警（不需要交互，纯展示）
```

## 7. 测试要点

### 单元测试（驾驶舱）
- [ ] 切到 agent-cockpit：3 列网格布局
- [ ] 顶部总览显示 3 在线 / 0 异常 / 5 任务
- [ ] 3 个智能体卡片显示进度 + ETA
- [ ] 底部统计显示 287 / 5 / 18
- [ ] 不出现 ¥XXX
- [ ] 不出现决策类内容（待我决策 / 异常预警 / 事件流）
- [ ] 1440x900 视口不需要滚动

### 单元测试（数据看板）
- [ ] 切到 data-board：5 个区都显示
- [ ] 区 2 待我决策显示 4 张决策
- [ ] 区 3 异常预警显示 3+ 条
- [ ] 区 4 今日事件流显示 4+ 条
- [ ] 原有 3 个金光场景保留

### 回归测试
- [ ] agent-billing / agent-risk 仍可工作（[进入] 按钮能跳转）
- [ ] 其它 13 个 panel 正常
- [ ] 无 JS error
- [ ] div 平衡

## 8. 风险

### 中风险
1. **驾驶舱 3 列网格在 1440px 视口**每列约 450px，可能窄
   - 缓解：每列文字精简，已完成/待执行用一行显示
2. **数据看板加 3 个新区**后总长度会增加，可能要滚动
   - 缓解：新区紧凑设计，单屏内能看完 3 个区

### 低风险
3. **驾驶舱"进入"按钮**点击后切换，可能用户期望新窗口
   - 缓解：保持单页应用体验（沿用现有 switchView）
4. **决策内容去重**要确保驾驶舱不再列决策
   - 缓解：移除 R002 的 .cockpit-decision-zone 区

## 9. 验收 Checklist

```
驾驶舱
[ ] 3 列网格布局（一屏可见）
[ ] 每个卡片包含：进度 + ETA + 已完成 + 待执行
[ ] 顶部总览条
[ ] 底部统计条
[ ] 不出现 ¥XXX
[ ] 不出现决策类内容
[ ] [进入] 按钮工作
[ ] 单屏不滚动

数据看板
[ ] 5 个区都显示（顶部总览 / 待我决策 / 异常预警 / 今日事件流 / 3 场景）
[ ] 待我决策 4 张卡
[ ] 异常预警 3+ 条
[ ] 今日事件流 4+ 条
[ ] 原有 3 金光场景保留
[ ] 单屏或最多 1 次微滚

回归
[ ] agent-billing / agent-risk 正常（R001 不受影响）
[ ] 其它 13 个 panel 正常
[ ] 无 JS error
[ ] div 平衡
[ ] HANDOFF.md 已更新（加 R003 说明）
```

## 10. 相关文件参考

- 📄 `HANDOFF.md` —— 项目总览 + 设计原则
- 📄 `.changes/R001-agent-split-layout.md` —— 智能体左右分栏
- 📄 `.changes/R002-cockpit-redesign.md` —— 驾驶舱决策优先版
- 📄 `.changes/R003-mockup-z.md` —— Z 方案 mockup

## 11. 建议的实现顺序

1. **先改驾驶舱**：删除 R002 的决策区 + 重构为 3 列网格（信息去重是关键）
2. **再改数据看板**：在区 1 顶部总览之后插入 3 个新区
3. **最后改 HANDOFF.md**：记录新结构
4. 跑回归 + 截图对比

**预计工作量**：2-3 小时（涉及 2 个 panel + 信息去重逻辑）

## 12. 约束强化

**🚫 严格禁止**：

```js
// ❌ 驾驶舱内容
"¥96万敞口"    // 不能放金额
"待我决策"      // 不能放决策内容
"异常预警"      // 不能放预警内容
"事件流"        // 不能放事件内容

// ✅ 驾驶舱只放
"扫描进度 67%"  // 业务运营数字 OK
"ETA 16:00"    // 时间 OK
"已完成 5 项"   // 数量 OK
```

**✅ 数据看板允许**：

```js
"¥96万敞口"    // 数据看板允许金额（业务 KPI）
"待我决策"      // 数据看板的核心功能
"异常预警"      // 数据看板的核心功能
"事件流"        // 数据看板的核心功能
```

---

**等待接手 Agent 完成后，再让我跑一遍验收。**