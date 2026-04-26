# 从 OpenClaw 认识 Agent

更新时间：2026-04-07

如果只是抽象地去讲 Agent，最后很容易变成一堆概念。但如果换一个入口，从一个具体系统去看，很多东西会立刻落地。OpenClaw 就是一个很好的入口。

我更倾向于把它看成：它不是一个单纯的聊天产品，也不是一个只会调模型的壳子，而是一个把会话、工具、记忆、多 Agent、安全边界和多端接入组织到一起的 Agent 系统。如果想借一个项目去真正理解 Agent，这个项目是值得拆开的。

> 相关阅读：[[Agent开发全景指南]]、[[GSD (Get Shit Done) 完全使用指南]]、[[gemini cli注意事项]]

---

## 一、先定义：OpenClaw 是什么

如果只用一句话来定义：OpenClaw 是一个面向 Agent 的自托管网关与运行系统。这个定义比"AI 助手"更准确。

原因很简单。它的重点不是把一个大模型接出来，而是把下面这些东西统一组织起来：消息入口、会话路由、Agent 工作区、工具调用、记忆系统、插件扩展、多 Agent 隔离、安全控制。所以 OpenClaw 的核心不是"聊天"，而是"把 Agent 跑起来"。

从官方首页的描述看，它本身是一个 self-hosted gateway，可以连接 Telegram、Slack、Discord、WhatsApp、WebChat 等多种消息入口，并把这些入口统一接到 Agent 上。官方也明确把 Gateway 定义成会话、路由和通道连接的单一事实源。截至 2026-04-07，GitHub 官方仓库 openclaw/openclaw 的 stars 大约是 350.7k，这已经说明它不是一个边缘项目。

---

## 二、为什么用 OpenClaw 来学 Agent

原因不在于它火，而在于它把 Agent 的关键层几乎都摆在台面上了。

如果只看一条主线，可以记成：消息进入 Gateway → 路由到某个 Agent → Agent 读取会话与记忆 → 调用工具/技能 → 执行动作 → 写回状态。这条链条里，Agent 的很多基础概念都会自然出现：Session、Tool、Skill、Memory、Action、Policy、Multi-Agent、Sandbox。也就是说，OpenClaw 的价值不只是"能用"，而是"能拿来拆解 Agent"。

---

## 三、从 OpenClaw 看 Agent 的主体构成

前面在 Agent 笔记里，我把主体构成压成了四个部分：Plan、Tool、Memory、Action。OpenClaw 虽然是一个完整系统，但还是可以用这四层去看它。

### Plan

Plan 在 OpenClaw 里不是一个单独的模块名，但它一直存在。只要 Agent 要决定"下一步做什么"，本质上就在做规划。例如：是先搜索还是先读文件；是直接回复还是先调用浏览器；是自己做还是交给子 Agent。所以 Plan 不一定非得长成"Planner 模块"，它也可能体现在运行时决策里。

### Tool

这部分是 OpenClaw 最容易看懂的。官方文档直接说得很明确：Agent 所有超出文本生成的能力，都是通过工具完成的。内置工具包括：exec、browser、web_search、web_fetch、read / write / edit、apply_patch。这件事非常重要，因为它刚好说明了 Agent 和 LLM 的边界：LLM 负责生成，Tool 负责接外部世界。如果没有 Tool，系统更多还是"会回答"。有了 Tool，系统才开始变成"能做事"。

### Memory

OpenClaw 的记忆系统非常适合拿来理解 Agent 里的 Memory 到底是什么。它不是那种空泛的"长期记忆"概念，而是非常具体的文件和检索结构。官方文档里，默认记忆至少包括三类文件：MEMORY.md（长期记忆）、memory/YYYY-MM-DD.md（日记式短期记录）、DREAMS.md（实验性的记忆整理结果）。更关键的是，它不是只存，还配了检索机制。官方文档提到记忆检索支持 keyword、vector similarity 和 hybrid search。这就说明，Memory 在 Agent 里本质上不是"存档"，而是"可被后续决策再次调用的状态系统"。

### Action

Action 在 OpenClaw 里也很直观。前面 Plan 决定了做什么，Tool 提供了能力，最后真正落地的那一下就是 Action。例如：执行一个 shell 命令；打开浏览器并点击页面；写入一个文件；发送一条消息；把某个事实写入记忆。所以 Action 不是额外概念，它就是决策真正作用到环境上的那一步。

> 相关阅读：[[Agent开发全景指南#三、核心构成]]

---

## 四、OpenClaw 里最值得学的几个 Agent 概念

### Session

很多人学 Agent 时，容易先去看 Prompt。但从工程上说，Session 往往比 Prompt 更关键。因为 Session 决定的是：这段对话属于谁；历史上下文怎么延续；多个用户会不会串上下文；多个渠道会不会混会话。OpenClaw 把这件事设计得很明确。官方文档里，Gateway 负责会话和路由；单 Agent 模式下会有主会话，多 Agent 模式下每个 Agent 都有自己的 session store。这件事背后的知识点其实就是：Agent 不是一次调用，而是带状态的持续系统。

### Skill

OpenClaw 也是一个很适合理解 Skill 的项目。它官方把 Tool、Skill、Plugin 明确拆成三层：Tool 是 Agent 调用的函数能力；Skill 是教 Agent 何时、如何使用工具；Plugin 是把工具、技能、通道、模型能力打包起来。这个分层非常有代表性。因为在真实 Agent 系统里：Tool 解决"能不能做"；Skill 解决"怎么稳定地做"；Plugin 解决"怎么扩展这个系统"。OpenClaw 里的 Skill 是 SKILL.md 形式，这一点也很值得注意。它说明 Skill 不一定是代码模块，也可以是"带元数据的任务能力说明书"。

### Policy

Agent 学习里最容易被忽视的一层，是 Policy。也就是：什么工具能用；什么用户能触发；什么情况下必须提权；什么情况下必须沙箱。OpenClaw 在这一层做得很重。它有：tools allow / deny、tool profile、per-agent 工具限制、sandbox 模式、elevated 执行、pairing / allowlist。所以你从 OpenClaw 能学到一个很关键的判断：Agent 系统不是先有智能，再补权限；而是先划边界，再让智能在边界内运行。

### Multi-Agent

OpenClaw 的多 Agent 不是"多开几个提示词"那么简单。官方文档明确说，一个 agent 是一个完整作用域的 brain，它有自己的：workspace、agentDir、sessions、auth profiles。这个设计非常适合用来理解多 Agent 的真正含义：多 Agent 不是多角色提示词，而是多套隔离的工作上下文和运行状态。这也是为什么我一直觉得，多 Agent 的关键不在"角色设计"，而在"隔离设计"。

### Sandbox

如果只从 OpenClaw 学一件和安全相关的事，那就是 Sandbox。官方文档写得很直接：如果开启 sandbox，工具执行可以进入隔离环境；如果关闭，就直接跑在宿主机上。它支持的模式包括：off、non-main、all。还支持不同 backend，例如：docker、ssh、openshell。这件事告诉我们，Agent 的"能执行"永远要和"执行在哪里"一起看。否则系统做得越强，风险越大。

---

## 五、OpenClaw 最有代表性的技术结构

如果只保留最值得认识的几个结构，我觉得有五个。

### Gateway-first

OpenClaw 最核心的结构不是 model-first，而是 gateway-first。也就是说，系统先统一消息入口、会话、通道、路由，再让 Agent 在这个统一入口后面工作。这和很多只从 API 调模型开始的项目不一样。它更像一个真正的 Agent 基础设施。

### Tool / Skill / Plugin 三层结构

这是 OpenClaw 很值得学的一点。它没有把所有扩展都混成一个概念，而是明确拆成三层：Tool（函数能力）、Skill（任务指导）、Plugin（扩展打包）。这套分层一旦看懂，很多 Agent 系统里的抽象就会顺很多。

### Memory 文件化 + 检索化

OpenClaw 的记忆不是黑箱，而是显式可见的。这一点很重要。因为很多时候你以为 Agent 的 Memory 很玄，其实真正落地时，往往就是：文件、数据库、索引、检索工具。OpenClaw 把这件事做得很工程化。

### Multi-Agent 隔离

OpenClaw 的多 Agent 不共享工作区，不共享 session store，也不默认共享凭证。这说明它对多 Agent 的理解不是"让几个 Agent 聊起来"，而是"把多个 Agent 作为多个独立运行单元来管理"。

### 节点与远程执行

OpenClaw 还有 node 这层。官方文档里，Gateway 可以收消息、跑模型、做路由，而命令执行可以交给远程 node host。这背后的知识点其实是：Agent 的执行环境和推理环境，不一定在同一台机器上。这对理解分布式 Agent、远程执行、安全隔离都很有帮助。

### 嵌入式 Agent Runtime

还有一个很值得学的技术点：OpenClaw 不是完全从零写一套 Agent 内核，而是把 pi 的 agent runtime 嵌进了自己的 Gateway 架构里。官方技术文档明确写到，它通过 pi-coding-agent 的 createAgentSession() 直接实例化 AgentSession，而不是把 pi 当成一个外部子进程去调用。

这个设计背后有一个很重要的工程判断：现代 Agent 系统不一定自己重写 agent loop，更常见的做法是把现成 runtime 嵌入到更大的产品架构里。

OpenClaw 在这一层做的事情包括：接管 session 生命周期；注入 OpenClaw 自己的 tools；拼接自己的 system prompt；管理 session persistence；接入 sandbox、auth profile 和 model failover。

所以从技术实现上看，OpenClaw 其实给了一个很清楚的案例：pi 负责更底层的 agent loop；OpenClaw 负责 gateway、channel、session、tool policy、memory、multi-agent 和安全边界。这个分工非常值得记住。因为它说明一个完整 Agent 产品，并不一定等于一个单体框架。

---

## 六、通过 OpenClaw，应该怎样理解 Agent

如果只借 OpenClaw 学三个判断，我觉得最重要的是下面三个。

### Agent 不是 Prompt 套壳

从 OpenClaw 可以很明显看出来，Agent 不是"一个大模型 + 一个 system prompt"。真正的 Agent 系统至少还包括：session、memory、tools、skill、policy、runtime。所以 Prompt 很重要，但 Prompt 不是主体。

### Agent 的核心不只是推理，而是运行

OpenClaw 让人很容易看清一点：真正决定 Agent 能不能落地的，不只是模型会不会想，而是系统能不能：路由、保持上下文、控制权限、管理会话、执行工具、隔离风险。所以现在大家说 Agent Runtime 越来越重要，这不是一个概念包装，而是工程现实。

### Agent 的真正难点在边界

Agent 的上限当然和模型有关。但 Agent 的稳定性，更取决于边界设计。例如：哪些工具能用；哪些消息能触发；哪些会话应该隔离；哪些执行必须沙箱；哪些记忆可以跨会话保留。OpenClaw 在这些地方给出的不是抽象原则，而是具体机制。这也是它最适合拿来学习 Agent 的地方。

---

## 七、一条适合用 OpenClaw 学 Agent 的路径

如果真要把 OpenClaw 当成学习入口，建议按下面这条线走。

### 第一步：先看 Gateway 和 Session

先不要急着研究模型。先看：消息怎么进来；会话怎么建；路由怎么做；为什么 Agent 需要持续状态。

### 第二步：再看 Tool 和 Skill

这是从"会话系统"进入"Agent 系统"的关键一步。重点理解：Tool 是能力接口；Skill 是能力模板；Plugin 是扩展机制。

### 第三步：再看 Memory

这里最关键的不是记住文件名，而是理解：Memory 是状态系统，不是装饰功能。

### 第四步：最后看 Multi-Agent 和 Sandbox

到这一步，才真正进入工程层。因为这时候你要开始理解：为什么多 Agent 要隔离；为什么执行环境要控制；为什么权限设计比"多会一点"更重要。

---

## 八、结尾

如果只给 OpenClaw 下一个判断，我会这么说：它不是一个让你"体验 AI 对话"的项目，而是一个让你真正看见 Agent 系统长什么样的项目。

从这个项目里，至少能学到四件事：Agent 的主体不是提示词，而是运行系统；Tool、Skill、Memory、Policy 这些层必须拆开看；多 Agent 的关键不是角色，而是隔离；一个能落地的 Agent 系统，必须把安全边界当成一等公民。

所以，如果后面要继续学 Agent，OpenClaw 很适合作为一个技术入口。它不是把概念讲得最轻松的项目，但它确实是把概念做得比较完整的项目。

> 相关阅读：[[Agent开发全景指南]]、[[GSD (Get Shit Done) 完全使用指南]]、[[远程 Code CLI 工具指南]]

---

## 九、参考资料

- OpenClaw 官网文档：https://docs.openclaw.ai/
- OpenClaw GitHub：https://github.com/openclaw/openclaw
- Multi-Agent Routing：https://docs.openclaw.ai/concepts/multi-agent
- Memory Overview：https://docs.openclaw.ai/concepts/memory
- Skills：https://docs.openclaw.ai/tools/skills
- Tools and Plugins：https://docs.openclaw.ai/tools/index
- Sandboxing：https://docs.openclaw.ai/gateway/sandboxing
- Nodes：https://docs.openclaw.ai/nodes