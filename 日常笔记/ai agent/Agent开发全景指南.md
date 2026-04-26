# Agent开发全景指南

理解 Agent，最关键的入口不是看一堆概念定义，而是看清它和普通 LLM 调用到底差在哪。

LLM 是"问答型"，Agent 是"执行型"。LLM 收到问题后生成内容就结束，而 Agent 收到目标后会拆任务、调工具、读反馈、改状态，直到把事情做完。这条闭环——Goal → Plan → Act → Observe → Update——才是 Agent 的本质。

如果把 Agent 压缩到最小结构，核心只有四个部分：Plan、Tool、Memory、Action。这四层不是概念包装，而是真正决定一个 Agent 能不能落地的东西。

---

## 一、定义

Agent 不是单次问答模型，而是面向目标执行的智能软件单元。

它的最小闭环可以写成：Goal → Plan → Act → Observe → Update。这个闭环的关键在于"闭环"二字——Agent 不止是生成内容，而是要基于反馈不断调整，直到目标达成。

在工程实现上，Agent 的主体构成更适合压缩为四个部分：Plan（任务分解、步骤选择、重规划）、Tool（外部能力接口）、Memory（状态、上下文与历史）、Action（面向环境的实际执行）。其中，模型负责驱动这四个部分协同工作，而不是单独作为主体结构中的一层。

所以，Agent 更准确的定义不是"会聊天的模型"，而是"由 Plan / Tool / Memory / Action 组织起来的目标执行系统"。

> 相关阅读：[[从OpenClaw认识Agent]]、[[GSD (Get Shit Done) 完全使用指南]]

---

## 二、执行主线

Agent 是以目标为导向的，理解它最好的方式是从执行主线切入。

执行过程可以拆成五个环节：Goal 定义目标和边界、Plan 拆任务排顺序、Act 调用能力执行动作、Observe 读取工具结果和环境反馈、Update 刷新状态决定下一步。这五个环节对应的构成要素分别是 Plan 的输入、Plan 本身、Tool + Action、Memory 的更新来源，以及 Memory。

从这个主线可以看出，Agent 的本质不是生成，而是基于反馈的闭环控制。每一次 Observe 和 Update，都是在让系统从"乱"回到"有序"。

| 环节 | 作用 | 对应构成要素 |
| --- | --- | --- |
| Goal | 定义目标、边界、完成条件 | Plan 的输入 |
| Plan | 拆任务、排顺序、决定策略 | Plan |
| Act | 调用能力并执行动作 | Tool + Action |
| Observe | 读取工具结果与环境反馈 | Memory 的更新来源 |
| Update | 刷新状态并决定下一步 | Memory |

---

## 三、核心构成

核心构成的四要素各有明确职责。

Plan 负责生成步骤并调整策略，解决的问题是"下一步做什么"。Tool 提供外部能力，解决"能调什么能力"。Memory 保存上下文、状态与历史，解决"已经做过什么"。Action 将决策落实为环境动作，解决"实际执行什么"。

四者的关系可以写成一句话：Agent 基于 Memory 对任务进行 Plan，借助 Tool 产生 Action，再将结果回写到 Memory。

向外补充一层，还有 Goal（定义任务边界）、Skill（封装高频任务模板）、Policy（定义权限与约束）。这些是外围支撑，不是核心骨架，但对落地同样关键。

| 要素 | 作用 | 对应问题 |
| --- | --- | --- |
| Plan | 生成步骤并调整策略 | 下一步做什么 |
| Tool | 提供外部能力 | 能调什么能力 |
| Memory | 保存上下文、状态与历史 | 已经做过什么 |
| Action | 将决策落实为环境动作 | 实际执行什么 |

> 相关阅读：[[从OpenClaw认识Agent#三、从 OpenClaw 看 Agent 的主体构成]]

---

## 四、Skill 在 Agent 中的位置

Skill 是高于 Tool 的任务能力封装，通常由 Prompt + Tool + 约束 + 输出格式 + 局部流程组成。

Tool 和 Skill 的区别在于抽象层级：Tool 是低层，目标是完成单个动作；Skill 是中层，目标是完成一类任务。Tool 是单一接口或函数，Skill 则是 Prompt + Tool + 流程的组合。Tool 直接调用，Skill 作为能力模块装配。

举个例子：网页搜索、SQL 查询、HTTP 请求是 Tool；竞品调研、日志排障、代码审查是 Skill。

Skill 的意义在于把高频任务沉淀为标准执行模板，从而降低决策复杂度并提高复用性。

| 项 | Tool | Skill |
| --- | --- | --- |
| 抽象层级 | 低 | 中 |
| 目标 | 完成单个动作 | 完成一类任务 |
| 组成 | 单一接口/函数 | Prompt + Tool + 流程 |
| 复用方式 | 直接调用 | 作为能力模块装配 |

---

## 五、Agent 与相近概念的边界

Agent 和几个相近概念容易混淆，需要讲清边界。

LLM 的核心问题是生成内容，Agent 额外具备执行闭环。RAG 是检索后回答，Agent 可动态决定是否检索与是否重规划。Workflow 是固定流程自动化， Agent 可在执行中改变路径。

生产系统通常是 Workflow + Agent 的混合体。固定部分走 Workflow，开放部分交给 Agent。

| 对象 | 核心问题 | 与 Agent 的差异 |
| --- | --- | --- |
| LLM | 生成内容 | Agent 额外具备执行闭环 |
| RAG | 检索后回答 | Agent 可动态决定是否检索与是否重规划 |
| Workflow | 固定流程自动化 | Agent 可在执行中改变路径 |

---

## 六、当前 Agent 的主要特征

截至 2026 年，主流 Agent 系统呈现七个共性。

Tool-centric：价值中心从问答转向工具接入。Stateful：显式状态、长任务、可恢复执行。Structured：Schema、类型约束、结构化输出。Runtime-oriented：重点从 Prompt 转向 Runtime。Human-in-the-loop：审批、接管、打断机制常态化。Multi-agent capable：多 Agent 成为可选架构，而非默认架构。Protocolized：MCP 管工具接入，A2A 管 Agent 协作。

这七个特征说明一个问题：Agent 的重点已经从"模型会说什么"转向"系统能做什么"。

---

## 七、架构与工程应用

### 常见架构

最常用的四类架构各有适用场景。

单 Agent + Tool Loop 最简单、稳定、成本低，适合大多数业务场景。Planner-Executor 把规划与执行分离，适合复杂任务、报告、研发。Router-Specialist 先分流再处理，适合多领域入口、多专家系统。Manager-Worker 让管理者拆分任务，适合复杂协同、多子任务系统。

选择原则很简单：任务稳定优先 Workflow，任务开放优先单 Agent，任务天然可拆分再考虑多 Agent。

| 架构 | 特征 | 适用场景 |
| --- | --- | --- |
| 单 Agent + Tool Loop | 简单、稳定、成本低 | 大多数业务场景 |
| Planner-Executor | 规划与执行分离 | 复杂任务、报告、研发 |
| Router-Specialist | 先分流再处理 | 多领域入口、多专家系统 |
| Manager-Worker | 管理者拆分任务 | 复杂协同、多子任务系统 |

### 工程问题

工程落地的关键不在"能跑"，而在"能稳定运行"。

核心问题只有四个：上下文工程（什么信息在什么时刻进入决策面）、状态管理（如何恢复、追踪、审计）、工具权限（能力边界与风险边界如何定义）、评估观测（如何同时评估过程与结果）。这四个问题没解决好，Agent 跑得再快也会在某个时刻失控。

### 应用方向

当前最成熟的三类落地方向：Coding Agent（读写代码、测试、排障、补丁生成）、Browser Agent（网页导航、表单操作、Web 自动化）、Knowledge Agent（知识库、数据库、企业系统问答与执行）。

---

## 八、开源项目地图

以下排序基于 2026-04-07 查询到的 GitHub 官方仓库星标数，只保留头部项目。

前十名分别是：browser-use（86.3k，Browser Agent）、OpenHands（70.7k，Coding Agent）、MetaGPT（66.8k，Multi-Agent）、AutoGen（56.8k，Multi-Agent / Framework）、LlamaIndex（48.4k，Knowledge / Agentic RAG）、CrewAI（48.2k，Multi-Agent / Workflow）、LangGraph（28.6k，Agent Runtime）、Semantic Kernel（27.7k，Enterprise Agent Framework）、smolagents（26.5k，Lightweight Agent Framework）、Haystack（24.7k，Knowledge / RAG / Agent）。

如果只保留一页 PPT，推荐只讲前 6 个：browser-use、OpenHands、MetaGPT、AutoGen、LlamaIndex、CrewAI。

| 排名 | 项目 | GitHub Stars | 类型 |
| --- | --- | ---: | --- |
| 1 | browser-use | 86.3k | Browser Agent |
| 2 | OpenHands | 70.7k | Coding Agent |
| 3 | MetaGPT | 66.8k | Multi-Agent |
| 4 | AutoGen | 56.8k | Multi-Agent / Framework |
| 5 | LlamaIndex | 48.4k | Knowledge / Agentic RAG |
| 6 | CrewAI | 48.2k | Multi-Agent / Workflow |
| 7 | LangGraph | 28.6k | Agent Runtime |
| 8 | Semantic Kernel | 27.7k | Enterprise Agent Framework |
| 9 | smolagents | 26.5k | Lightweight Agent Framework |
| 10 | Haystack | 24.7k | Knowledge / RAG / Agent |

以下项目不放在主列表中，不是因为没价值，而是当前星标和社区势能明显弱一档：Google ADK（18.8k）、CAMEL（16.6k）、PydanticAI（16.2k）。

### 项目推荐

有些项目没有放进"按 stars 排序的 Agent 头部项目"，是因为它们的定位并不完全等于 Agent 本身。

LangChain 是 LLM 应用开发框架，覆盖 prompt、tool、RAG、chain、agent 等通用抽象。LangSmith 是 Agent / LLM 应用的观测、调试、评估平台。Dify 是面向应用搭建的 LLMOps / Agent 平台，偏低代码与产品化。n8n 是通用自动化工作流平台，可与 Agent 结合，但本体不是 Agent 框架。

其中最容易混淆的是 LangChain。LangChain 更适合定义为通用 LLM 应用框架、上层抽象层、生态入口，而不是最典型的"生产级 Agent Runtime"。

如果只看 Agent 体系内的分工，可以简化理解为：LangChain 是应用开发抽象层，LangGraph 是 Agent Runtime / 有状态执行层，LangSmith 是观测、调试、评估层。

| 项目 | 更准确的定位 |
| --- | --- |
| LangChain | LLM 应用开发框架，覆盖 prompt、tool、RAG、chain、agent 等通用抽象 |
| LangSmith | Agent / LLM 应用的观测、调试、评估平台 |
| Dify | 面向应用搭建的 LLMOps / Agent 平台，偏低代码与产品化 |
| n8n | 通用自动化工作流平台，可与 Agent 结合，但本体不是 Agent 框架 |

---

## 九、总结

Agent 的本质不是对话，而是闭环执行。主体构成可压缩为 Plan / Tool / Memory / Action。Tool 决定能力边界，Memory 决定连续性，Action 决定执行落地。当前重点已经从 Prompt 转向 Runtime、状态管理与安全治理。当前最成熟的落地形态是 Coding Agent、Browser Agent、Knowledge Agent。

> 相关阅读：[[从OpenClaw认识Agent]]、[[GSD (Get Shit Done) 完全使用指南]]、[[gemini cli注意事项]]

---

## 十、参考链接

- OpenAI Agents：
  - https://platform.openai.com/docs/guides/agents/best-practices
  - https://platform.openai.com/docs/guides/agent-builder
- Anthropic Agent 资料：
  - https://resources.anthropic.com/hubfs/Building%20Effective%20AI%20Agents-%20Architecture%20Patterns%20and%20Implementation%20Frameworks.pdf
- MCP：
  - https://modelcontextprotocol.io/docs/getting-started/intro
- LangGraph：
  - https://github.com/langchain-ai/langgraph
- CrewAI：
  - https://github.com/crewAIInc/crewAI
- AutoGen：
  - https://github.com/microsoft/autogen
- OpenHands：
  - https://github.com/OpenHands/OpenHands
- browser-use：
  - https://github.com/browser-use/browser-use
- PydanticAI：
  - https://github.com/pydantic/pydantic-ai
- Google ADK：
  - https://github.com/google/adk-python
- Semantic Kernel：
  - https://github.com/microsoft/semantic-kernel
- LlamaIndex：
  - https://developers.llamaindex.ai/python/framework/
- Haystack：
  - https://github.com/deepset-ai/haystack
- CAMEL：
  - https://github.com/camel-ai/camel
- MetaGPT：
  - https://github.com/geekan/MetaGPT