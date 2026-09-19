# 需求变更 R006：金光 AI Demo v6（AI Native 重设计）

> 灵感来源：Harvey（法律 AI Native 标杆）的 **Memory + Command Center** 设计哲学
> 用户选择：方向 B（Memory 优先 + 命令面板）

## 1. 背景

### 现状（v5 · R001-R005 之后）
金光 Demo 有 17 个 panel、5 个独立区，用户从 sidebar 菜单跳转查看。**这是"AI 是工具"的传统 SaaS 思维**。

### 用户原话
> "如果我把金光的这个demo，以AI native思维来重新设计，它应该是怎么样的？"

### AI Native 的核心特征（参考 Harvey）
1. **AI 是主语**：不是"用户选什么功能"，而是"AI 在做什么"
2. **Memory（记忆）**：AI 记住用户之前的决定 / 偏好，跨会话保持
3. **主动建议**：AI 主动发现 + 主动呈现决策，不等用户找

## 2. 目标

### 改完之后

金光 Demo 从"**多 panel 切换**"变成"**一个持续的 AI 工作台**"：

```
┌─────────────────────────────────────────────────────────┐
│ 🪖 金光 AI                                              │
│ [Memory: 7 决策已记]  [⚙ 配置]  [🔄 刷新]               │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ╭──── 中央命令栏 ─────────────────────────────────╮   │
│  │                                                  │   │
│  │ 💬 跟金光 AI 说...                [↑ 发送]    │   │
│  │                                                  │   │
│  │ 提示词快捷按钮：                                 │   │
│  │ [今天有什么需要决策？] [扫描风险预警]           │   │
│  │ [补全所有缺失的主数据] [生成月度合规报告]      │   │
│  │                                                  │   │
│  ╰──────────────────────────────────────────────────╯   │
│                                                         │
│  ╭─ AI 在执行 ────────────────────────────────────╮   │
│  │                                                 │   │
│  │ ┌─⚠ 风险预警官 (扫描中 67%) ─┐               │   │
│  │ │ 234/350 家 · 发现 5 家高风险              │   │
│  │ │ ▓▓▓▓▓▓▓░░░░░░░ 67%                         │   │
│  │ │ [查看报告]                                  │   │
│  │ └─────────────────────────────────────────────┘   │
│  │ ┌─⚡ 开票专员 (运行中 89%) ──────┐               │   │
│  │ │ 287/320 张 · 拦截 3 张异常                   │   │
│  │ │ ▓▓▓▓▓▓▓▓▓▓▓░░░ 89%                         │   │
│  │ │ [查看清单]                                  │   │
│  │ └─────────────────────────────────────────────┘   │
│  │ ┌─🔧 补全专员 (待你拍板 0%) ──────┐               │   │
│  │ │ 5 家待确认                                  │   │
│  │ │ ░░░░░░░░░░░░░░ 0%                            │   │
│  │ │ [前往处理]                                  │   │
│  │ └─────────────────────────────────────────────┘   │
│  ╰──────────────────────────────────────────────────╯   │
│                                                         │
│  ╭─ Memory · 最近决策（你之前拍板的）────╮           │
│  │                                                 │   │
│  │ ✓ 09:15 [你] 同意「批量补全 5 家客户」         │   │
│  │ ✓ 11:45 [你] 同意「停止开票」苏州金鹏          │   │
│  │ ✓ 13:18 [你] 同意「调整账期」浙江光华          │   │
│  │ ⚠ 14:21 [AI] 建议「关闭 3 家高风险客户账期」│   │
│  │ ❌ 14:25 [你] 拒绝「立即停止苏州金鹏」         │   │
│  │                                                 │   │
│  │ [查看全部 7 条决策 →]                            │   │
│  ╰─────────────────────────────────────────────────╯   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

## 3. 验收标准

### 视觉层面
- [ ] 顶部标题"金光 AI"（不再是"金光 AI 财"等）+ 3 个右上角工具（Memory / 配置 / 刷新）
- [ ] 中央命令栏：textarea + 发送按钮 + 4 个提示词快捷按钮
- [ ] AI 在执行：3 个智能体卡片（带进度条 + 状态描述 + 操作按钮）
- [ ] Memory 区：列出最近 5-7 条历史决策（含时间 / 用户/AI / 决定）
- [ ] 整体一个 panel 完成（不再跳来跳去）
- [ ] 不出现 ¥XXX 资金字样（保持 R002 约束）

### 功能层面
- [ ] 在命令栏输入文字 + 点发送 → 触发 mock AI 回复
- [ ] 点 4 个提示词快捷按钮 → 自动填入命令栏 + 触发对应回复
- [ ] AI 在执行的 3 个智能体进度条会"动"（mock 进度更新）
- [ ] 点 [查看报告] [查看清单] [前往处理] → 跳到对应智能体 workspace
- [ ] Memory 列表显示最近 5-7 条决策
- [ ] 点 [查看全部] → 弹模态框显示完整决策历史

### AI Native 核心（最关键）
- [ ] **AI 是主语**：页面第一眼看到的是"AI 在说话 / 在做事"，不是"用户在操作"
- [ ] **Memory**：用户之前的决定被记住（即使是 mock 数据）
- [ ] **主动建议**：决策类内容不需要用户主动找，AI 在 Memory 区主动呈现

### 不破坏
- [ ] R001（左右分栏 / agent-billing / agent-risk）仍然能跳到
- [ ] R002 / R003 / R004 / R005 已实现的内容不丢（数据看板、定时简报、内嵌网页等）

## 4. 涉及范围

### 需要改的文件
- `demos/jinguang.html` —— 主要修改
- `HANDOFF.md` —— 更新 demo 整体定位说明

### 需要改的位置（核心改动）
1. **新增一个 panel：`agent-cockpit`** （重做现有内容）
   - 现有 cockpit（R003 做的"3 列运行态"）被替换成"AI Native 工作台"
2. **不删除其它 16 个 panel**
   - 但**默认入口从 sidebar 改为 agent-cockpit**（智能体驾驶舱 = AI 工作台）
   - 其它 panel 通过 cockpit 内的链接进入（如"前往处理"按钮）

### sidebar 简化
- 工作台模式：保留新任务 / 技能 / 数据看板 / 内嵌网页（4 个）
- 智能体模式：智能体驾驶舱（**默认入口，AI 工作台**）+ 业务智能体（2 个）+ 智能体管理

### 不需要改的 panel（16 个）
agent-billing / agent-risk / data-board / new-task / skill / history-1~5 / tool-mgr / tool-cap / tool-kb / schedule-1 / embed-web

## 5. 技术约束

### 来自 HANDOFF.md（必须遵守）
1. **单滚动容器**：每个 panel 自己 `overflow-y: auto`
2. **不要破坏 `.views-container { display: contents }`**
3. **不要破坏 sidebar `.sidebar-user { margin-top: auto }`**
4. **不要嵌套 panel**

### 来自 R001（必须遵守）
- 复用 CSS 变量
- 不引入新依赖

### 来自 R002（必须遵守）
- **不允许出现 ¥XXX 资金字样**

### 来自 R003-R005（必须遵守）
- 决策类内容只在数据看板/AI 工作台出现，不重复
- 3 个智能体的内容不重复

### 新增约束（按 R006）
- **AI 是主语**：页面布局要让 AI 主动呈现内容占主导（不是用户菜单）
- **Memory 概念**：即使 mock，也要有"AI 记住用户决策"的视觉表达
- **进度条要"动"**：3 个智能体的进度条定时变化（不是写死的静态值）

## 6. 实现方案

### 6.1 总体布局（agent-cockpit 新版本）

```html
<div class="view-panel" data-view-panel="agent-cockpit">
  <div style="padding: 24px; background: var(--canvas); height: calc(100vh - 60px); overflow-y: auto;">

    <!-- 顶部标题 + 工具按钮 -->
    <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 16px;">
      <div style="display: flex; align-items: center; gap: 12px;">
        <div style="width: 36px; height: 36px; border-radius: 18px; background: var(--ink-1); color: var(--brass); display: flex; align-items: center; justify-content: center; font-size: 16px; font-weight: 700;">AI</div>
        <div>
          <div style="font-size: 16px; font-weight: 600; color: var(--ink-1);">金光 AI</div>
          <div style="font-size: 11px; color: var(--ink-3);">2026-09-17 · 14:32 · 实时</div>
        </div>
      </div>
      <div style="display: flex; gap: 8px; font-size: 12px;">
        <button class="btn-ghost">🧠 Memory: 7 决策已记</button>
        <button class="btn-ghost">⚙ 配置</button>
        <button class="btn-ghost">🔄 刷新</button>
      </div>
    </div>

    <!-- 区 1：中央命令栏 -->
    <div class="dash-card" style="border: 2px solid var(--brass); margin-bottom: 16px;">
      <div style="display: flex; gap: 12px; padding: 16px;">
        <div style="flex: 1;">
          <div style="font-size: 13px; font-weight: 600; color: var(--ink-1); margin-bottom: 8px;">💬 跟金光 AI 说点什么...</div>
          <div style="display: flex; gap: 8px; align-items: flex-end; background: var(--canvas); border: 1px solid var(--line); border-radius: var(--r); padding: 8px 12px;">
            <textarea placeholder="例如：扫描所有客户的风险预警、补全缺失的主数据..." rows="2" style="flex: 1; ..."></textarea>
            <button style="height: 32px; padding: 0 14px;">↑ 发送</button>
          </div>
        </div>
      </div>
      <div style="padding: 0 16px 16px; display: flex; gap: 8px; flex-wrap: wrap;">
        <button class="btn-ghost" style="font-size: 11px;">📋 今天有什么需要决策？</button>
        <button class="btn-ghost" style="font-size: 11px;">🔍 扫描风险预警</button>
        <button class="btn-ghost" style="font-size: 11px;">📝 补全所有缺失的主数据</button>
        <button class="btn-ghost" style="font-size: 11px;">📊 生成月度合规报告</button>
      </div>
    </div>

    <!-- 区 2：AI 在执行 -->
    <div class="dash-card" style="margin-bottom: 16px;">
      <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 14px;">
        <div style="font-size: 14px; font-weight: 600;">⚙️ AI 在执行 · 3 个智能体</div>
        <button class="btn-ghost">查看全部 →</button>
      </div>

      <!-- 3 个智能体卡片 -->
      <div style="display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 12px;">

        <!-- 风险预警官 -->
        <div style="background: var(--canvas); border: 1px solid var(--line); border-left: 3px solid var(--danger); border-radius: var(--r); padding: 14px;">
          <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 8px;">
            <div style="font-size: 12px; font-weight: 600;">⚠️ 风险预警官</div>
            <span style="font-size: 10px; background: rgba(185, 52, 44, 0.1); color: var(--danger); padding: 2px 6px; border-radius: 8px;">运行中</span>
          </div>
          <div style="font-size: 11px; color: var(--ink-2); margin-bottom: 8px;">集团风险扫描</div>
          <div style="font-size: 11px; color: var(--ink-3);">234 / 350 家 · 进度 67%</div>
          <div style="height: 4px; background: var(--line); border-radius: 2px; margin: 6px 0;">
            <div style="height: 100%; width: 67%; background: var(--danger); border-radius: 2px;"></div>
          </div>
          <div style="display: flex; justify-content: space-between; font-size: 10px; color: var(--ink-3); margin-bottom: 10px;">
            <span>ETA 16:00</span>
            <span>5 家高风险</span>
          </div>
          <button class="btn-primary" style="width: 100%; height: 28px; font-size: 11px;">查看报告 →</button>
        </div>

        <!-- 智能开票专员 -->
        <div style="background: var(--canvas); border: 1px solid var(--line); border-left: 3px solid var(--success); border-radius: var(--r); padding: 14px;">
          <!-- 类似结构 -->
        </div>

        <!-- 客户补全专员 -->
        <div style="background: var(--canvas); border: 1px solid var(--line); border-left: 3px solid var(--brass); border-radius: var(--r); padding: 14px;">
          <!-- 类似结构 -->
        </div>
      </div>
    </div>

    <!-- 区 3：Memory · 最近决策 -->
    <div class="dash-card">
      <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 14px;">
        <div>
          <div style="font-size: 14px; font-weight: 600;">🧠 Memory · 最近决策</div>
          <div style="font-size: 11px; color: var(--ink-3); margin-top: 2px;">AI 记住你之前的决定 · 跨会话保持</div>
        </div>
        <button class="btn-ghost">查看全部 7 条 →</button>
      </div>

      <!-- Memory 条目 -->
      <div class="memory-item">✓ 09:15 [你] 同意「批量补全 5 家客户」</div>
      <div class="memory-item">✓ 11:45 [你] 同意「停止开票」苏州金鹏</div>
      <div class="memory-item">✓ 13:18 [你] 同意「调整账期」浙江光华</div>
      <div class="memory-item">⚠ 14:21 [AI] 建议「关闭 3 家高风险客户账期」</div>
      <div class="memory-item">❌ 14:25 [你] 拒绝「立即停止苏州金鹏」</div>
    </div>

  </div>
</div>
```

### 6.2 中央命令栏交互（mock AI 回复）

```js
const AI_REPLIES = {
  '今天有什么需要决策？': '今天有 3 件需要你拍板：\n1. 苏州金鹏 · 税务非正常户（紧急）\n2. 浙江光华 · 法人变更（中风险）\n3. 上海永胜 · 4 项主数据缺失（低风险）\n\n我已经准备好建议，请查看下方 Memory 区。',
  '扫描风险预警': '✅ 已完成新一轮扫描\n• 发现 5 家高风险客户\n• 触发 3 家预警（详见 Memory）\n• 风险评分模型 v3.2.1 已更新',
  '补全所有缺失的主数据': '✅ 批量补全完成\n• 5 家客户 / 18 项主数据\n• 自动调用工商接口\n• 等待财务一键确认（见补全专员）',
  '生成月度合规报告': '📊 月度合规报告已生成\n• 涵盖 234 家客户\n• 风险评级 A+ / B+ / C 三档\n• 导出 PDF（在配置中心）'
};

// 用户输入 + 点发送
document.querySelector('.command-send').addEventListener('click', () => {
  const text = document.querySelector('.command-input').value.trim();
  if (!text) return;

  // 1. 显示用户消息
  appendChatMessage('user', text);

  // 2. 500ms 后模拟 AI 回复
  setTimeout(() => {
    const reply = AI_REPLIES[text] || `已收到："${text}"\n我会持续执行，请查看下方 AI 在执行区。`;
    appendChatMessage('ai', reply);
  }, 500);

  // 3. 清空输入
  document.querySelector('.command-input').value = '';
});

// 提示词快捷按钮
document.querySelectorAll('.quick-prompt').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelector('.command-input').value = btn.dataset.prompt;
    document.querySelector('.command-send').click();
  });
});
```

### 6.3 AI 进度条"动起来"

```js
// 定时更新智能体进度（mock 进度增长）
const agentProgress = {
  'agent-risk': 67,
  'agent-billing': 89,
  'agent-complete': 0
};

setInterval(() => {
  Object.keys(agentProgress).forEach(id => {
    // 增长进度（直到 100）
    if (agentProgress[id] < 100) {
      agentProgress[id] += Math.random() * 2;
      if (agentProgress[id] > 100) agentProgress[id] = 100;
      updateProgressBar(id, agentProgress[id]);
    }
  });
}, 2000);  // 每 2 秒更新一次
```

### 6.4 Memory 列表（mock 数据）

```js
const MEMORY_DATA = [
  { time: '14:25', actor: '你', action: '拒绝', target: '立即停止苏州金鹏开票' },
  { time: '14:21', actor: 'AI', action: '建议', target: '关闭 3 家高风险客户账期' },
  { time: '13:18', actor: '你', action: '同意', target: '调整账期 浙江光华' },
  { time: '11:45', actor: '你', action: '同意', target: '停止开票 苏州金鹏' },
  { time: '09:15', actor: '你', action: '同意', target: '批量补全 5 家客户主数据' },
  { time: '昨日 17:30', actor: 'AI', action: '自动', target: '月初认证率盘点完成' },
  { time: '昨日 14:30', actor: '你', action: '同意', target: '5 家主数据批量补全' }
];
```

### 6.5 CSS 新增

```css
/* Memory 条目 */
.memory-item {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px 12px;
  background: var(--canvas);
  border-radius: var(--r);
  font-size: 12px;
  margin-bottom: 6px;
}
.memory-item:hover {
  background: var(--line-2);
}

/* Memory 角色徽章 */
.memory-actor-你 {
  background: var(--brass);
  color: var(--paper);
}
.memory-actor-AI {
  background: var(--ink-2);
  color: var(--paper);
}
.memory-badge {
  font-size: 10px;
  padding: 1px 6px;
  border-radius: 6px;
  font-weight: 600;
}
```

## 7. 测试要点

### 单元测试
- [ ] 切到 agent-cockpit：3 个区都显示（命令栏 / AI 执行 / Memory）
- [ ] 命令栏输入文字 + 发送 → AI 回复气泡出现
- [ ] 4 个提示词按钮 → 触发对应 AI 回复
- [ ] 3 个智能体进度条动起来
- [ ] Memory 列表显示 5-7 条

### AI Native 核心验证
- [ ] 第一眼看到 AI 在做事（不是用户在操作菜单）
- [ ] Memory 概念清晰呈现
- [ ] 决策类内容 AI 主动呈现（在 Memory 区）

### 回归测试
- [ ] 其它 16 个 panel 正常
- [ ] R001-R005 不受影响
- [ ] 无 JS error

## 8. 风险

### 高风险
1. **进度条自动更新**可能让用户分心
   - 缓解：动画速度慢（每 2 秒更新一次）

### 中风险
2. **AI 回复 mock**可能显得假
   - 缓解：回复内容真实，符合业务场景

### 低风险
3. **Memory 数量** 7 条可能太多/太少
   - 缓解：默认 5-7 条，可点击"查看全部"

## 9. 验收 Checklist

```
视觉层面
[ ] 顶部"金光 AI" 标题
[ ] 中央命令栏（textarea + 发送 + 4 快捷按钮）
[ ] AI 在执行区（3 个智能体进度）
[ ] Memory 区（5-7 条历史决策）
[ ] 整体单页一屏可见

AI Native
[ ] AI 是主语（开场是 AI 在说话）
[ ] Memory 概念呈现
[ ] 决策 AI 主动呈现

功能
[ ] 命令栏发送 → AI 回复
[ ] 4 个提示词按钮
[ ] 进度条自动更新
[ ] 查看全部按钮工作
[ ] 跳转智能体 workspace

回归
[ ] 其它 16 个 panel 正常
[ ] 无 JS error
[ ] div 平衡
```

## 10. 相关文件参考

- 📄 `HANDOFF.md`
- 📄 `.changes/R006-mockup-ai-native.md` —— 方向讨论
- 🎬 Harvey.ai（参考）

## 11. 建议的实现顺序

1. 重构 agent-cockpit panel HTML
2. 实现 CSS 样式
3. 实现命令栏 mock AI 回复
4. 实现进度条自动更新
5. 实现 Memory 列表
6. 跑回归 + 截图对比

**预计工作量**：2-3 小时（涉及 mock 数据 + 交互）

## 12. 约束强化

**🚫 不要做**：

```js
// ❌ 不要"菜单化"AI
"选择智能体：⚠️ ⚡ 🔧"
"切换面板：数据看板 / 驾驶舱"
"功能列表..."

// ❌ 不要让用户"找"决策
"决策内容" → 在菜单某个二级页面
"AI 建议" → 需要点"查看"才显示
```

**✅ 必须做**：

```js
// ✅ AI 主动呈现
"今天有 3 件需要你拍板" → 开场白
"AI 正在执行" → 进度条
"AI 记得" → Memory 区
"AI 建议" → Memory 区主动呈现
```

---

**等待接手 Agent 完成后，再让我跑一遍验收。**