# 需求变更 R005：工作台内嵌网页面板

## 1. 背景

### 现状
工作台模式下，数据看板（`data-board`）是顶部最重要的面板，位于 sidebar 工作区快捷入口列表中。

### 需求
在工作台、数据看板**下方**新增一个面板：
- **可点击触发**（从 sidebar 工作区快捷入口点击进入）
- **右侧内嵌**指定网页：`http://202.102.43.13:8080/aims/yib0000.page`
- 保留用户使用网页的所有功能（点击 / 输入 / 滚动）

### 用户原话
> "在工作台，数据看板下方，做一个可以点击，然后右边内嵌这个网页的，http://202.102.43.13:8080/aims/yib0000.page"

## 2. 目标

### 改完之后

**Sidebar 工作区快捷入口**新增一项：
```
＋ 新任务     ⌘N
🧩 技能
📊 数据看板     ← 已有
🌐 内嵌网页     ← 新增（点击进入）
```

**新面板视图**（点击"内嵌网页"后展示）：
```
┌─────────────────────────────────────────────────────────┐
│ 📊 工作台 / 内嵌网页                  🔗 在新窗口打开 ↗ │
│ ─────────────────────────────────────────────────────── │
│ http://202.102.43.13:8080/aims/yib0000.page              │
│                                                          │
│ ┌────────────────────────────────────────────────────┐│
│ │                                                    ││
│ │   [iframe 加载的外部网页]                       ││
│ │                                                    ││
│ │   - 用户可在 iframe 内操作                        ││
│ │   - 滚动 / 点击 / 提交表单                         ││
│ │                                                    ││
│ └────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────┘
```

## 3. 验收标准

### 视觉层面
- [ ] Sidebar 工作区快捷入口新增"🌐 内嵌网页"项
- [ ] 点击后右侧主区显示内嵌面板
- [ ] 面板顶部有标题栏（"内嵌网页"）+ URL 显示 + "在新窗口打开"按钮
- [ ] iframe 占满主区域（高度撑满视口）
- [ ] iframe 内可正常交互（点击 / 输入 / 滚动）

### 功能层面
- [ ] Sidebar 入口点击 → 切换到新面板
- [ ] iframe 加载 `http://202.102.43.13:8080/aims/yib0000.page`
- [ ] iframe 加载失败时显示 fallback UI（错误提示）
- [ ] 点击 "在新窗口打开 ↗" → 在新浏览器标签页打开 URL
- [ ] iframe 内操作不污染父页面（事件隔离正常）

### 不破坏
- [ ] 数据看板（data-board）布局不变
- [ ] 工作台其他快捷入口（新任务 / 技能）布局不变
- [ ] R001 / R002 / R003 / R004 实现的其它 panel 不受影响
- [ ] 没有 JS error

## 4. 涉及范围

### 需要改的文件
- `demos/jinguang.html` —— 主要修改

### 需要改的位置（2 处）
1. **Sidebar 工作区快捷入口**（`.workspace-shortcuts` 区域）
   - 新增一项：`🌐 内嵌网页`，`data-view="embed-web"`
2. **views-container**（在 data-board 之后）
   - 新增 view-panel：`data-view-panel="embed-web"`

### 不需要改的 panel（15 个）
所有其它 panel 不动

### 不需要改的 sidebar
- 工作区快捷入口只增加一项，其它不动
- 智能体模式 sidebar 不动

## 5. 技术约束

### 来自 HANDOFF.md（必须遵守）
1. **单滚动容器**：每个 panel 自己 `overflow-y: auto`
2. **不要破坏 `.views-container { display: contents }`**
3. **不要破坏 sidebar `.sidebar-user { margin-top: auto }`**
4. **不要嵌套 panel**
5. **新增 sidebar 项要标 `data-mode-content` 或 `data-mode-section`**（这次新增 sidebar 项适用）

### 来自 R001（必须遵守）
- 复用 CSS 变量
- 不引入新依赖

### 通用约束
- 保持与现有 panel 风格一致（顶部 bar + 内容区）

### 新增约束（按 R005）
- **iframe URL 硬编码**到 HTML（`http://202.102.43.13:8080/aims/yib0000.page`）
- **iframe 高度填满视口**（让用户看不到外面是 demo）
- **安全考虑**：iframe 默认 sandbox 可能限制功能，需测试实际功能
  - 建议：`sandbox="allow-same-origin allow-scripts allow-forms allow-popups"`
- **fallback UI**：URL 访问失败时显示友好提示

## 6. 实现方案

### 6.1 Sidebar 新增入口

```html
<!-- 现有 3 项 -->
<div class="workspace-shortcuts" data-mode-content="work">
  <div class="shortcut-item" data-view="new-task"><span class="shortcut-icon">＋</span><span class="shortcut-text">新任务</span><span class="shortcut-kbd">⌘N</span></div>
  <div class="shortcut-item" data-view="skill"><span class="shortcut-icon">🧩</span><span class="shortcut-text">技能</span></div>
  <div class="shortcut-item" data-view="data-board"><span class="shortcut-icon">📊</span><span class="shortcut-text">数据看板</span></div>
  
  <!-- 新增：第 4 项 -->
  <div class="shortcut-item" data-view="embed-web"><span class="shortcut-icon">🌐</span><span class="shortcut-text">内嵌网页</span></div>
</div>
```

### 6.2 新增 view-panel

```html
<!-- ===== 视图：内嵌网页 ===== -->
<div class="view-panel" data-view-panel="embed-web">
  <div style="display: flex; flex-direction: column; height: calc(100vh - 60px);">

    <!-- 顶部 bar -->
    <div style="background: var(--paper); border-bottom: 1px solid var(--line); padding: 14px 24px; display: flex; justify-content: space-between; align-items: center; flex-shrink: 0;">
      <div>
        <div style="font-size: 14px; font-weight: 600; color: var(--ink-1);">🌐 内嵌网页</div>
        <div style="font-size: 11px; color: var(--ink-3); margin-top: 2px;">http://202.102.43.13:8080/aims/yib0000.page</div>
      </div>
      <div style="display: flex; gap: 8px;">
        <button class="btn-ghost" onclick="document.getElementById('embed-iframe').src = document.getElementById('embed-iframe').src;">↻ 刷新</button>
        <a href="http://202.102.43.13:8080/aims/yib0000.page" target="_blank" class="btn-primary">在新窗口打开 ↗</a>
      </div>
    </div>

    <!-- iframe 容器（填满视口） -->
    <div style="flex: 1; overflow: hidden; background: var(--canvas); padding: 16px;">
      <iframe
        id="embed-iframe"
        src="http://202.102.43.13:8080/aims/yib0000.page"
        style="width: 100%; height: 100%; border: 1px solid var(--line); border-radius: var(--r); background: var(--paper);"
        sandbox="allow-same-origin allow-scripts allow-forms allow-popups allow-downloads"
        referrerpolicy="strict-origin-when-cross-origin"
        loading="lazy"
        onload="document.getElementById('embed-error').style.display='none';"
        onerror="document.getElementById('embed-error').style.display='flex';"
      ></iframe>

      <!-- 加载失败 fallback UI -->
      <div id="embed-error" style="display: none; position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); background: var(--paper); padding: 40px; border-radius: var(--r-lg); box-shadow: 0 8px 32px rgba(0,0,0,0.1); text-align: center;">
        <div style="font-size: 48px; margin-bottom: 16px;">⚠️</div>
        <div style="font-size: 16px; font-weight: 600; color: var(--ink-1); margin-bottom: 8px;">无法加载内嵌网页</div>
        <div style="font-size: 12px; color: var(--ink-3); margin-bottom: 16px;">URL: http://202.102.43.13:8080/aims/yib0000.page</div>
        <div style="font-size: 12px; color: var(--ink-2); margin-bottom: 16px;">
          可能原因：<br>
          • 网络无法访问该 IP<br>
          • 目标网站禁止 iframe 嵌入（X-Frame-Options）<br>
          • 目标服务未启动
        </div>
        <a href="http://202.102.43.13:8080/aims/yib0000.page" target="_blank" class="btn-primary">在新窗口打开 →</a>
      </div>
    </div>

  </div>
</div>
```

### 6.3 关键实现细节

**1. iframe 高度填满视口**

```css
.view-panel[data-view-panel="embed-web"] {
  display: flex;
  flex-direction: column;
}
.view-panel[data-view-panel="embed-web"].active {
  display: flex;
}
.view-panel[data-view-panel="embed-web"] > div {
  flex: 1;
  display: flex;
  flex-direction: column;
  height: calc(100vh - 60px);  /* 减去 toolbar 高度 */
}
.view-panel[data-view-panel="embed-web"] iframe {
  flex: 1;
  width: 100%;
  border: 1px solid var(--line);
  border-radius: var(--r);
}
```

**2. Sandbox 配置**

```html
sandbox="allow-same-origin allow-scripts allow-forms allow-popups allow-downloads"
```

说明：
- `allow-same-origin`：允许同源请求
- `allow-scripts`：允许 JS 执行
- `allow-forms`：允许表单提交
- `allow-popups`：允许弹出窗口
- `allow-downloads`：允许下载

如果后端没有返回 `X-Frame-Options: DENY`，应该能正常加载。如果有，需要后端调整或用其他方案。

**3. 加载失败检测**

```js
// 检测 iframe 加载（受同源限制，可能不准）
iframe.onload = () => {
  // 隐藏错误提示
};
iframe.onerror = () => {
  // 显示错误提示
};

// 或者用 setTimeout 检测（如果超时还没加载完，认为失败）
setTimeout(() => {
  try {
    if (!iframe.contentDocument) {
      // 跨域无法访问，认为加载失败
    }
  } catch(e) {
    // 跨域会抛异常，认为加载失败
  }
}, 5000);
```

**4. 刷新按钮**

```js
document.getElementById('embed-iframe').src = document.getElementById('embed-iframe').src;
```

## 7. 测试要点

### 单元测试
- [ ] Sidebar 工作区出现 "🌐 内嵌网页" 项
- [ ] 点击 → 切换到新面板
- [ ] iframe 加载目标 URL
- [ ] 顶部 bar 显示标题 + URL
- [ ] "在新窗口打开" 按钮工作
- [ ] "刷新" 按钮工作

### 网络测试（**关键**）
- [ ] 从能访问 202.102.43.13 IP 的网络访问 → iframe 正常加载
- [ ] 从不能访问的网络 → 显示 fallback UI
- [ ] 在新窗口打开能正常访问
- [ ] iframe 内操作（点击 / 输入 / 提交）不影响 demo

### 回归测试
- [ ] 工作台新任务 / 技能 / 数据看板 正常
- [ ] 智能体模式 sidebar 不变
- [ ] 其它 15 个 panel 不受影响
- [ ] 没有 JS error

## 8. 风险

### 高风险

1. **网络访问限制**：当前 demo 服务器可能访问不了 `202.102.43.13`
   - 缓解：fallback UI + "在新窗口打开" 按钮
   - 用户说："IP 访问不了，先准备 mock 页面"——接受此风险
2. **X-Frame-Options 拒绝**：目标网站可能禁止 iframe 嵌入
   - 缓解：fallback UI + 在新窗口打开

### 中风险

3. **sandbox 限制功能**：太严的 sandbox 会让网页功能不全
   - 缓解：用宽松 sandbox 配置
4. **iframe 高度问题**：动态内容可能撑不开
   - 缓解：固定 height: 100% + overflow: hidden

### 低风险

5. **样式不一致**：iframe 内容跟 demo 视觉风格不同
   - 缓解：iframe 有边框 + 圆角包裹

## 9. 验收 Checklist

```
视觉层面
[ ] Sidebar 新增 "🌐 内嵌网页" 入口
[ ] 面板顶部 bar 显示标题 + URL
[ ] "在新窗口打开" 按钮
[ ] "刷新" 按钮
[ ] iframe 撑满主区域

功能层面
[ ] Sidebar 点击切换到新面板
[ ] iframe 加载目标 URL（如果能访问）
[ ] fallback UI 显示（如果不能访问）
[ ] "在新窗口打开" 工作
[ ] "刷新" 工作

回归层面
[ ] 工作台其它快捷入口正常
[ ] 数据看板布局不变
[ ] 其它 15 个 panel 正常
[ ] 无 JS error
[ ] div 平衡
[ ] HANDOFF.md 已更新
```

## 10. 相关文件参考

- 📄 `HANDOFF.md`
- 📄 `.changes/R001-R004 各需求文档` —— 之前的修改历史

## 11. 建议的实现顺序

1. **新增 sidebar 项**：在 `workspace-shortcuts` 区域加一项
2. **新增 view-panel**：在 `views-container` 内（任意位置，建议在 data-board 之后）
3. **测试 iframe 加载**（从能访问 IP 的网络）
4. **验证 fallback UI**（从不能访问的网络）
5. **跑回归**

**预计工作量**：30 分钟 - 1 小时（结构简单）

## 12. 用户后续调试说明

### 如果网络能访问 202.102.43.13
- iframe 直接加载成功，无需修改

### 如果网络不能访问
- 显示 fallback UI（错误提示）
- 用户可以：
  1. 点击 "在新窗口打开" 查看
  2. 修改 `src` 为可访问的 URL
  3. 或部署 mock 页面替换

### 如果 X-Frame-Options 拒绝嵌入
- fallback UI 显示原因
- 用户可以用 Chrome 扩展解除限制，或让后端调整响应头

---

**等待接手 Agent 完成后，再让我跑一遍验收。**