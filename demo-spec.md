# Super-Agent × 飞书 对话 Demo — 共享规格

三个并行方向（demo-a / demo-b / demo-c）共用本文件的内容事实与对话脚本，只允许在视觉与交互形态上分岔。**对话内容、机制事实、术语必须以本文件为准**，不得虚构产品行为。

被模拟的系统是本仓库 `dsh-superagent-prototype/`（DeepSeek Harness 官方 Super-Agent 原型的原样拷贝，行为依据见 `dsh-superagent-prototype/docs/` 与 `prompts/replies.md`）。

---

## 1. 被模拟系统的机制事实（demo 中出现的每个机制都必须能在下面找到）

| # | 机制 | Demo 中的呈现 |
|---|------|--------------|
| F1 | 入口只有两个 Source：GitHub（主）与飞书（可选）；Web 面板只供人审批/查看，不能派任务 | demo 场景发生在飞书；旁白/面板文案可点明"面板不能派任务" |
| F2 | 常驻的只有一个 **orchestrator session**（`orchestrator-<repo>`，router preset，小工具集，自己不干活）：监听 trigger → eval 判断（accept / stop / ignore）→ 建 Ticket → 派发 → 汇报 | 收到消息后先出现"评估"动作，再建 ticket |
| F3 | 每个 ticket 一个 **worker session**：`worker-<ticketId前12位>`，独立 worktree 目录、独立分支、独立 PR，配完整工具集（`dsh-superagent-worker` preset） | ticket 卡片展示 worker session 名与分支名 |
| F4 | 并发 slot：`maxWorkers` 默认 3（可调 1–16），并预留 1 个 slot 给人类消息（人不会被挤掉） | 第 4 个任务排队时展示"3/3 运行中，已排队" |
| F5 | Ack 机制：worker 接活前先在**原线程**回一条点名具体对象与预期结果的确认，如「收到：排查 iOS Safari 登录态丢失的根因并修复。」（不重复用户原话，不寒暄） | bot 的第一条回复即此格式 |
| F6 | IM 消息 100 词硬上限（中英文皆然）；飞书走 Markdown→富文本 transport | bot 消息全部短句，无长篇大论 |
| F7 | Attention：worker 被阻塞需要人决策时，ticket 进入 waitsForPerson，orchestrator 在原线程向发起人提问，必须带 ticket_id 和问题原文 | 出现一张"需要你确认"的卡片 + 两个明确选项 |
| F8 | 同一线程的新消息 fan-out 给订阅中的 ticket → 追问路由给**同一个 worker**，不开新 ticket | 用户在第 2 步追问后，回复仍归属 T-8f3a |
| F9 | 与现有 ticket 无关的新请求 → 新 ticket、新 worker session | 用户临时插一个无关小任务时出现 T-b21c |
| F10 | 结案 outcome 五种：completed / merged / closed / dropped / stopped；本剧情主 ticket 以 **merged** 结案 | 结案报告卡显示 merged |
| F11 | Retry 语义：ticket 中断后新 assignment 复用**同一个 session / 目录 / 分支 / PR** 接续 | 仅作旁白/面板注释，不入对话 |
| F12 | 汇报纪律：worker 对内报告，**orchestrator 负责面向人的转达**（提取事实重写，不逐条转发） | bot 的进展消息是转述口吻，不是日志粘贴 |

## 2. 对话脚本（三条 demo 完全一致，含文案）

场景：今天，飞书群「acme-web 值班」/ 与 Superagent 机器人的会话。人物：**Godw**（用户，说话口吻随意）与 **Superagent**（bot，遵守 F5/F6：短、点名对象、不寒暄）。时间线如下，消息序号在 demo 中对应呈现顺序（可合并呈现，不得改写文案实质）：

| # | 时间 | 发言方 | 内容 / 卡片 |
|---|------|--------|------------|
| 1 | 10:02 | Godw | `@Superagent 线上登录页在 iOS Safari 上会随机掉登录态，帮我查根因并修掉` |
| 2 | 10:02 | Superagent | （F2 eval → accept）`收到：排查 iOS Safari 登录态随机丢失的根因并修复。` |
| 3 | 10:03 | Superagent | 卡片 **Ticket T-8f3a 已创建**：worker 会话 `worker-8f3a1c9d2e4b` · 分支 `fix/ios-safari-session` · 状态 running · 运行中 1/3 |
| 4 | 10:11 | Superagent | （F12 转述）`已复现：SameSite=Lax 的 cookie 在跨站跳转后被丢弃。准备收紧 cookie 策略并补集成测试。` |
| 5 | 10:26 | Superagent | 卡片 **需要你确认**（F7，T-8f3a）：`收紧策略后，旧的站外分享链接会要求重新登录。接受这个影响吗？` 按钮：`接受影响` / `换个方案` |
| 6 | 10:31 | Godw | `接受影响`（点按钮或回复均可，demo 中呈现为用户操作） |
| 7 | 10:33 | Superagent | `已按你的确认继续。改动在 PR #341，CI 运行中。` 附 PR 链接样式（`acme-web#341 fix/ios-safari-session`） |
| 8 | 10:58 | Godw | （同线程追问，F8）`顺便查一下 Android WebView 是不是同一个问题` |
| 9 | 10:59 | Superagent | `收到：排查 Android WebView 是否同样受 cookie 策略影响。` 归属卡片小字：`T-8f3a · 同一 worker 接续` |
| 10 | 11:12 | Superagent | `Android WebView 默认无此问题；已补一条回归测试防复发。` |
| 11 | 11:20 | Godw | （新任务，F9）`另外帮我把 README 里的过期徽章换成新的` |
| 12 | 11:21 | Superagent | 卡片 **Ticket T-b21c 已创建**（queued）：`运行中 3/3，排队中 · 前面还有 1 个任务`（F4）+ ack `收到：更新 README 徽章。` |
| 13 | 11:47 | Superagent | 卡片 **T-8f3a 结案**（F10）：outcome `merged` · PR #341 已合并 · `共 3 个提交，含 2 条新测试。T-b21c 已开工。` |

补充设定：机器人头像用单色几何/字母标识（自绘 SVG），不得使用飞书或 DeepSeek 官方 logo；Godw 头像同理。"acme-web" 为虚构仓库名。

## 3. 三个方向的差异（各自的唯一分岔点）

- **demo-a「仿真飞书」** — Operate。以假乱真的**飞书桌面客户端**观感：左侧导航与会话列表、中间消息流、右侧（可收起）线程回复面板。设计 token 以真实飞书桌面端为参照（近似还原，非官方规范）：会话背景 `#F5F6F7`、他人气泡白底 `#FFFFFF`、自己气泡品牌蓝 `#3370FF` 白字、正文 14px、消息圆角 12px（邻时间轴一侧 4px）、分割线 `#E5E6EB`、品牌蓝 `#3370FF`。交互：①「播放对话」自动逐条推进 + 底部可点击的三项快捷操作（接受影响 / 换个方案 / 输入框发送追问）；②消息 #6、#8、#11 由用户点击/输入触发，其余自动播放；③票据卡片为飞书富文本卡风格（左侧色条 + 状态徽标）。可视化目标是"我在飞书里真的在跟它干活"。
- **demo-b「指挥中心」** — Operate×Dashboard。双栏：左栏是精简飞书对话流（同用 §2 脚本），右栏是**引擎舱**面板：orchestrator session 常驻块、worker session 卡（T-8f3a/T-b21c）随剧情起落、slot 仪表（3 格 + 1 human slot 常亮）、ticket 状态机轨迹（running→blocked→running→merged）。左栏每条消息出现时，右栏对应机制点亮并滚动注释（"此刻：fan-out 把追问路由给同一 worker"）。交互：播放/暂停、单步、点击任一机制节点高亮其所有消息。
- **demo-c「移动叙事」** — Experience。手机竖屏（390 宽）飞书聊天流为舞台，外层是 scroll-driven 叙事页：整页滚动驱动对话逐条落位，关键节点（eval、attention、fan-out、排队、merge）插入简短旁白注解（固定文案由各节点机制事实改写，字数克制）。交互：滚动即播放；手机框可点击"进入全屏对话"切换为纯聊天流模式，支持点击发送最后两条用户消息。

## 4. 交付约束（三条一致）

- 单文件 HTML，`file://` 直接打开，无外部依赖（字体系统栈、内联 CSS/JS、图表用 SVG/CSS 自绘）；`<html data-zcode-design>`，可调值放 `:root` CSS 变量。
- 遵守 design 插件 hard rules：无 emoji 图标（1.6–1.8px monoline SVG + currentColor）、无 AI 味默认样式、动画只动 transform/opacity、尊重 reduced motion、键盘焦点可见。
- 目录：各 worktree 下 `原型/`；文件名 `superagent-feishu-demo-{方向名}.html`（方向名：仿真飞书 / 指挥中心 / 移动叙事）。
- demo 数据是演示用途：页面角落保留一行小字说明"模拟数据 · 基于 dsh-superagent-prototype 行为"。
