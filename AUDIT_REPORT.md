# 星郡官网 v2 设计审计报告

> 审计时间：2026.09.06 12:18
> 审计对象：`C:\Users\q1747\Desktop\代号：星郡\stellaris官网_v2.html`（1651 行 / 92KB / 单文件多视图应用）
> 审计方法：全文件阅读 + 桌面 1440×900 + 移动 390×844 双视口截图，9 + 8 = 17 张证据截图于 `_preview/aud_*.png`
> 审计框架：反 AI Tells / 移动可用性 / 视觉气质 / 功能完整性 四维

---

## 1. 反 AI Tells（最影响气质 · 6 处）

### 🔴 P0 - 主页模块卡用了 emoji 字符当图标（已修过 flow，但 habitat/ark 两块漏了）

- **位置**：`#habitat-mod`（4 个 moditem）+ `#stellaris-areas`（4 个 moditem）
- **代码行**：
  - 868-871：`▦` / `♻` / `⚡` / `⚙`（生态舱 4 模块）
  - 888-891：`⚡` / `📋` / `📡` / `🗄`（主舰 5 区前 4 个）
- **证据**：`aud_habitat.png`、`aud_ark.png` 中可见带色块的 emoji 字符
- **问题**：flow-chain 已经从 emoji 改为 SVG（这是之前修过的），但 moditem 这两个区**漏掉了同一个修复**——出现了不一致
- **建议**：所有 moditem 改用 SVG line icons（与 flow 风格统一），或统一改回几何 Unicode 符号（但 ⚡📋📡🗄 是真 emoji 一定要换）

### 🔴 P0 - Wiki 速览卡用 Unicode 几何符号当图标

- **位置**：`#wiki-mount` 的 4 张 wq-card（三大空间 / 四条规则 / 玩家身份 / 核心危机）
- **代码行**：1286-1289：`☷` / `⚖` / `◎` / `✧`
- **证据**：`aud_wiki.png` 中可见 4 张卡的图标为彩色字符
- **问题**：这些 Unicode 字符在不同系统字体下渲染不一致（Windows 显示像 emoji，macOS 可能显示成方块）
- **建议**：改用 SVG 图标或与 manifesto 类似的衬线符号（或者干脆用纯背景色块 + 文字，更编辑感）

### 🟡 P1 - Wiki 数据里的 emoji 残留

- **位置**：`WIKI` JS 数据里 list3 类型的 `ic` 字段
- **代码行**：1121-1123：`🛠` / `📦` / `🌀`（开拓者 3 目标卡）
- **问题**：这些 emoji 在 Wiki 渲染时直接进 `<div class="l3-ic">`，与图标 SVG 风格不一致
- **建议**：改成 SVG 或纯文字编号

### 🟡 P1 - 时间线 dots 颜色无意义地交替

- **位置**：`.tl-item:nth-child(even)::before{border-color:var(--c-rift)}`
- **代码行**：487
- **证据**：`aud_road.png` 中 5 个圆点交替青蓝/紫
- **问题**：奇偶交替没有叙事意义（既不是阶段类型区分，也不是风险等级）
- **建议**：用统一的青蓝或干脆按阶段递进（MVP 蓝 → 04 橙 → 05 红），与 .phase 颜色一致

### 🟡 P1 - 6 个色板 swatch 视觉单调

- **位置**：`.swatches` 6 个 .swatch 卡片
- **代码行**：928-935
- **证据**：`aud_visual.png` 中 6 个纯色块并排，每张只有颜色不同
- **问题**：虽然不像"三等卡"那样严重（颜色已不同），但视觉上**完全一致的几何形状 + 同样的字号**显得模板化
- **建议**：每张 swatch 顶部加一个**该色彩对应的 emoji 化图标**（如 STELLARIS 是抽象几何、生态舱是叶、裂隙是闪电、遗物是文物碎片），形成"色彩即身份"的视觉锚点

### 🟢 P2 - Hero 标题用了 CSS 渐变色

- **位置**：`.hero-title .grad` 类 + 实际 hero h1 没有 `.grad` 子元素
- **代码行**：219
- **问题**：定义了一个三色线性渐变（青→绿→紫），但 hero 标题里没有使用这个 class。是死代码 + 备用字体方案。建议删除或保留作为 alt 标题样式

---

## 2. 移动可用性（≤ 760px · 4 处 🔴）

### 🔴 P0 - 顶栏 nav 在 390px 严重溢出

- **位置**：`#topbar` 在 ≤ 980px 没有折叠处理
- **证据**：`aud_mobile_top.png` 中 "CODEX" 被裁切、右侧 `◈ ♪` 按钮完全消失
- **代码行**：100-108（顶栏 CSS 无移动端处理）
- **建议**：
  1. ≤ 760px 把 `.nav-btn .cn` 隐藏，3 个按钮简写为 HOME / VIEW / CODEX
  2. ≤ 540px 整个 nav-btn 隐藏，加一个汉堡按钮，点击展开下拉
  3. 顶栏 padding 从 14px 40px 改 10px 14px

### 🔴 P0 - Wiki 侧栏在 mobile 完全消失但无替代

- **位置**：`.w-side .w-nav{display:none}` 在 ≤ 760px
- **代码行**：657
- **证据**：`aud_mobile_wiki.png` 中看不到任何章节导航
- **问题**：用户进入 Wiki 后**没有任何办法跳转章节**，只能 scroll-to-top 找下一章
- **建议**：
  1. 把侧栏改为顶部一个 chapter 切换 chips（横向 scroll）
  2. 或加一个浮动 FAB 按钮，点开章节抽屉
  3. 或顶部加一个下拉选择器

### 🟡 P1 - mobile_flow 的节点卡 padding 不够紧凑

- **位置**：`.flow-node{padding:22px 10px}`
- **证据**：`aud_mobile_flow.png` 单列垂直排列，但每张节点卡高度较大（包含 SVG icon + 编号 + 标题），7 个节点就要 7 屏高度
- **建议**：mobile 下改成紧凑行布局（icon 在左、编号+标题在右），一屏可看 2-3 个节点

### 🟡 P1 - archive-strip 在 mobile 按钮竖排但左文会过长

- **位置**：`.strip-row{grid-template-columns:1.4fr .6fr}` 在 ≤ 1100px 已变为 1 列
- **证据**：`aud_mobile_archive.png` 显示布局变成上下两段，h3 大字"完整世界观..."单列铺满
- **建议**：mobile 下 h3 字号从 clamp(24,3.4vw,40) 进一步收窄到 24-28px

---

## 3. 视觉气质（3 处 · 影响高端感）

### 🟡 P1 - manifesto 正文颜色偏灰，与引述句反差不够

- **位置**：`.manifesto-cn{color:rgba(220,231,232,.78)}`
- **代码行**：294
- **问题**：正文用 78% 透明度的 cool-white，整体偏灰、引述句的 100% 白字 vs 正文 78% 灰字，反差够大但**正文本身灰到一定程度影响阅读**
- **建议**：正文提到 88-92%，保留层次但不牺牲可读性

### 🟢 P2 - 顶栏 HUD 风格与 manifesto 的编辑感不统一

- **位置**：`#topbar` 全局样式
- **代码行**：99-113
- **问题**：顶栏是 HUD/控制台风格（space-between + 居中绝对定位的 brand），而 manifesto 是杂志编辑感（衬线引述 + 竖排铭牌）。两套风格虽然都"科技"，但**精修不同**
- **建议**：顶栏 brand 区可以把 `STELLARIS // 星郡` 改成 logo + 衬线 wordmark 组合，或者加入与 manifesto 呼应的金条/竖线元素

### 🟢 P2 - Codex 的占位大字符（`.cx-picph`）opacity 0.07 太低

- **位置**：`.cx-picph{opacity:.07}`
- **代码行**：629
- **证据**：`aud_codex.png` 中 wide 卡（无图）的"星"字几乎看不见
- **建议**：提到 0.1-0.12，让占位字成为"低调背景元素"而非"看不见"

---

## 4. 功能完整性（5 处 · 死代码 / 占位 / 假交互）

### 🔴 P0 - 音乐播放器播放按钮没有任何真实音频逻辑

- **位置**：`#plPlay` 点击事件只有 `textContent` 切换，没有 `<audio>` 实例
- **代码行**：1627
- **问题**：点播放只切换 ▶/⏸ 图标和旋转动画，**没有真实音频源**——是假交互
- **建议**：
  1. 既然设计意图是"先做 UI 占位"，那在 UI 上加明显的「音源待接入」标签（当前只在 .pl-sub 显示）
  2. 或接入一个 `<audio>` 实例，即使 src 为空也能给出明确的"无音源"反馈

### 🟡 P1 - Codex 卡片 `flip` 交互没有任何 .flip CSS

- **位置**：`bindCodexCards()` 切换 `c.classList.toggle('flip')`，但 `.cx-card.flip` 没有任何 CSS 定义
- **代码行**：1471-1476
- **证据**：卡片点击只会触发事件但**视觉无变化**
- **建议**：要么删掉 click handler，要么写完整的 3D 翻转样式（参考品味原则中"反 fake interaction"）

### 🟡 P1 - Wiki `esc()` 函数是 no-op

- **位置**：`function esc(s){return s;}` 
- **代码行**：1329
- **问题**：所有 wiki 数据里的 `<b>`、`<span>` 都是直接 innerHTML 注入，**没有 XSS 防护**。如果未来 Wiki 内容来自用户输入或文档导入，会被 XSS 攻击
- **建议**：用 `esc()` 实际做 HTML 转义（除非内容是受信任的内部数据）

### 🟢 P2 - Wiki hero 进度条只在 w-main 滚动时更新，但 showView 切换后未触发首次计算

- **位置**：`mountWiki` 里 scroll 监听只在 `scroll` 事件触发时更新
- **代码行**：1353-1364
- **问题**：从 home 跳到 wiki 时，进度条初始是 0%，要等用户滚一下才更新
- **建议**：mountWiki 后手动触发一次进度条计算（`setTimeout(()=>scroller.dispatchEvent(new Event('scroll')),100)`）

### 🟢 P2 - footer 链接 `#player` 和 `#fx` 等锚点空指针

- **位置**：footer 的 `<a href="#player">` 和 `<a href="#fx">`
- **代码行**：`#footPlayer` / `#footFx` 的 event handler 用了 preventDefault，但没有 `data-goto` 或其他标识
- **问题**：footer 里这些链接看起来"坏了"，实际是被 JS 接管了——但用户右键"在新标签打开"会跳到空锚点
- **建议**：把 footer 这些链接改成 `<button>` 或给 href 留一个真实 anchor id（如 `#open-player`），并在 showView / 播放器逻辑里支持该 hash

---

## 优先级总览

| 优先级 | 数量 | 类别 |
|---|---|---|
| 🔴 P0 阻断 | 5 | 移动 nav 溢出 + Wiki 侧栏消失 + emoji 图标（2处）+ 假播放交互 |
| 🟡 P1 重要 | 7 | 时间线颜色、swatch 单调、正文颜色、Wiki nav 替代、emoji 残留、codex flip、esc |
| 🟢 P2 锦上添花 | 5 | Hero 渐变死代码、顶栏风格统一、占位字 opacity、进度条初始值、footer 锚点 |

## 修复顺序建议

1. **第一波（30 分钟）**：所有 emoji 字符 → SVG 图标（4 处 P0/P1 一起改，风格统一）
2. **第二波（30 分钟）**：移动端 nav 汉堡化 + Wiki 侧栏替代方案（两个 P0）
3. **第三波（30 分钟）**：timeline dots / swatch 图标 / 正文颜色微调（气质提升）
4. **第四波（15 分钟）**：清理死代码 + 修代码 quality（esc / flip / footer）

## 证据截图清单（`_preview/`）

- 桌面：`aud_habitat / ark / flow / rift / visual / road / archive / wiki / codex`
- 移动：`aud_mobile_top / flow / habitat / ark / rift / visual / archive / wiki / codex`

---

_此报告由 design-audit-page 工作流生成_
