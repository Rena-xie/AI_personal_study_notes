### 一、三者是什么关系（分层，不是三选一）

|工具|解决的问题|层级|
|---|---|---|
|**LangChain**|调用大模型的方式怎么标准化|最底层积木|
|**LangGraph**|复杂流程怎么编排（分支/循环/人工介入）|建在 LangChain 之上|
|**LangSmith**|每一步到底执行了什么，怎么看清|贯穿全程的观测层，不参与业务逻辑|

### 二、LangChain

- 核心作用：把"调用大模型"从手写胶水代码变成搭积木，封装了 **6 个标准组件**：
    1. **LLM 统一接口**——不同厂商模型调用方式不同，封装后换模型基本只改一行
    2. **Prompt Template**——提示词做成带占位符的模板，避免手动拼字符串
    3. **Chain（链）**——把"提示词→模型→解析输出"串成一条可复用的流水线
    4. **Memory（记忆）**——自动管理多轮对话上下文（该存、该裁剪、超长压缩摘要）
    5. **Tools / Agent**——把业务函数注册成工具，让模型自己判断调用哪个、要不要再调一次
    6. **Vector Store（向量存储）**——文档切片转向量存入，检索后喂给模型，是 RAG 的基础
- **LCEL（LangChain Expression Language）**：用 `|` 把模板和模型拼接，左边输出自动变右边输入，类似 Linux 管道；调用用 `invoke()`。返回结果除文本外还包含 token 用量、使用的模型等信息。
- **Chain 与 Agent 的本质区别**：
    - Chain：先后顺序写代码时就定死，模型只负责填内容
    - Agent：是否多走一步、调哪个工具、调完要不要再调，决定权交给模型自己判断
- **LangChain 内部推荐学习顺序**：LCEL 基础链 → 加 Memory 做多轮对话 → 接检索器做 RAG → 接 Agent 做工具调用。

LangGraph负责：

- 节点
- 状态
- 路由
- 循环
- 工具调用

### 三、LangGraph

- 官方定义：**低层级的智能体编排框架**——不给你一个只能填空的现成 Agent 模板，流程怎么摆放由你自己决定。
- 解决的问题：Chain 是单向直线，走到头就结束，无法根据中间结果分叉或回退；LangGraph 把流程画成图，支持分叉、循环、中途暂停等人审批。
- **4 个核心概念**：
    1. **State（状态）**：用 TypedDict 定义，是所有节点共享读写的数据
    2. **Node（节点）**：普通 Python 函数，接收当前 state，返回要更新的字段
    3. **Edge（边）**：普通边直接走下一个节点；**条件边**根据 state 内容决定走向，分叉和循环都靠它
    4. **Graph**：用 `StateGraph` 把节点和边编排起来，指定入口，`compile()` 后得到可运行对象，调用方式仍是 `invoke()`
- **典型应用场景**（判断要不要上 LangGraph 的标准：流程是否需要用中间结果决定下一步）：
    1. 多业务线路由：先判断用户意图，再走对应分支，判断错可退回重问
    2. 多智能体协作：多个 agent 共享同一份 state，完成后交接给下一个
    3. 自适应 RAG：检索结果先打分，不相关则改写问题重新检索
    4. 类似 Tree of Thought 的搜索式推理：同一问题展开多条解法，逐步打分剪枝保留最优解

### 四、LangSmith

- 定位：LangChain 官方的**可观测性平台**，解决 Agent 执行过程是"黑盒"看不清的问题。
- **3 个递进功能**（先看得见 → 再比得出 → 最后能优化）：
    1. **Tracing（追踪）**：记录每一步调用的工具、传入/传出参数、耗时、token 消耗，网页上可逐层展开回放
    2. **Evaluation（评估）**：用带标准答案的测试集，对比不同版本 Prompt 的正确率与延迟
    3. **Prompt 优化**：基于评估分数迭代提示词，靠数据而不是感觉判断好坏
- **接入成本很低**：只需配置 3 个环境变量（开启 tracing 开关、填写 API Key、指定项目名称），之后所有调用细节自动记录，业务代码不用改动。
- 如果没用 LangChain，也可以单独用 `@traceable` 装饰器对某个函数做追踪。
- 提供 Python 和 TypeScript SDK，不绑定任何特定框架。

### 五、需要记住的关键结论

- **框架越"高级"不代表系统越好**：多引入一层抽象也多一处可能出错的地方，且更难排查；效果好坏只有实际测评能验证，不能凭感觉信框架。
- **三者是叠加关系而非替代关系**：先用 LangChain 把"调用"标准化；流程复杂到需要分叉/循环/人工审核时再上 LangGraph；LangSmith 建议**从第一天就接入**，而不是等出问题才接。



你可以先把 LangGraph 想成：

> **“用流程图来编排 Agent 执行过程的框架。”**

官方现在把 LangGraph 定位为比较底层的 agent orchestration/runtime，核心就是让你能够控制 Agent 的**状态、执行步骤、步骤之间的流转，以及持久化**。

最简单可以想象成：

```
用户问题
   ↓
[理解问题]
   ↓
[决定是否调用工具]
   ↓
[调用工具]
   ↓
[生成答案]
   ↓
结束
```

LangGraph 就是把这样的流程变成一个真正可以执行、保存状态、恢复的程序。

---

# 二、State：Agent 的“工作记忆”

虽然你这次主要问四个东西，但必须先理解 **State**。

State 就是：

> **Agent 在整个执行过程中需要保存的信息。**

例如我们现在这个 Agent，可以有：

```
state = {
    "messages": [...]
}
```

其中：

```
messages
   ↓
用户：123*456是多少？
AI：我要计算
Tool：56088
AI：答案是56088
```

所以 State 可以理解成：

> **“这个 Agent 当前知道什么、已经做了什么。”**

---

# 三、StateGraph：整个流程图

`StateGraph` 就是：

> **定义“Agent 有哪些状态，以及这些状态如何流转”的图。**

官方最简单的例子就是：

```
from langgraph.graph import StateGraph, MessagesState, START, END

def mock_llm(state: MessagesState):
    return {
        "messages": [
            {"role": "ai", "content": "hello world"}
        ]
    }

graph = StateGraph(MessagesState)

graph.add_node(mock_llm)

graph.add_edge(START, "mock_llm")
graph.add_edge("mock_llm", END)

graph = graph.compile()
```

它实际上就是：

```
START
  ↓
mock_llm
  ↓
 END
```

官方文档也是按照这个思路介绍 `StateGraph → add_node → add_edge → compile` 的。

---

# 四、Node：一个“执行步骤”

Node 最简单理解就是：

> **图里的一个工作单元。**

例如：

```
                ┌──────────────┐
                │  LLM Node    │
                └──────────────┘
                       ↓
                ┌──────────────┐
                │ Tool Node    │
                └──────────────┘
                       ↓
                ┌──────────────┐
                │ Answer Node  │
                └──────────────┘
```

一个 Node 本质上就是一个函数。

例如：

```
def calculator_node(state):
    ...
```

或者：

```
def llm_node(state):
    ...
```

或者：

```
def retrieve_knowledge(state):
    ...
```

所以以后你的 AI Learning Assistant 很可能出现这样的 Node：

```
用户问题
   ↓
Query Rewrite Node
   ↓
Knowledge Retrieval Node
   ↓
LLM Node
   ↓
Answer
```

甚至：

```
                ┌→ Web Search Node ──┐
用户 → Router ──┤                    ├→ LLM → Answer
                └→ RAG Node ─────────┘
```

这时候你就会真正感受到 LangGraph 的价值。

---

# 五、Edge：决定“下一步去哪”

如果 Node 是：

> **做什么**

那么 Edge 就是：

> **做完以后去哪**

比如：

```
START
  │
  ↓
Router
  │
  ├────→ Calculator
  │
  └────→ RAG
```

这里：

```
Router → Calculator
```

就是一条 Edge。

---

# 六、为什么需要“条件 Edge”

这是 Agent 和普通流程图非常重要的区别。

比如：

```
用户问题
   ↓
LLM
   ↓
需要工具吗？
   │
   ├── 否 → 最终回答
   │
   └── 是 → 工具
               ↓
             LLM
               ↓
            最终回答
```

这里的路线不是固定的。

所以 LangGraph 可以根据 State 决定下一步：

```
graph.add_conditional_edges(...)
```

例如：

```
                    ┌→ calculator
                    │
LLM → 判断 tool ────┤
                    │
                    └→ END
```

这就是 Agent 中非常重要的：

> **Routing / Conditional Routing**

你以后做 AI Learning Assistant，一定会大量遇到这个概念。

---

# 七、Checkpoint：给 Agent “存档”

这是四个概念里最容易被误解的一个。

Checkpoint 不是：

> “把代码保存下来”

而是：

> **把 Agent 某个时刻的 State 保存下来。**

比如你的 Agent 执行：

```
Step 1
用户：帮我学习 RAG
        ↓
Step 2
检索知识库
        ↓
Step 3
找到 5 个文档
        ↓
Step 4
LLM 开始回答
```

那么 Checkpoint 可以保存：

```
Checkpoint #1
    用户问题

Checkpoint #2
    用户问题
    检索结果

Checkpoint #3
    用户问题
    检索结果
    LLM中间状态
```

这样程序如果中途出问题，就有机会从保存的状态继续，而不是从头开始。LangGraph 当前文档把 checkpointer 定义为保存某个 thread 的 graph state checkpoints，并用于会话连续性、故障恢复、人机协作和状态回溯等场景。

---

# 八、为什么叫“Checkpoint”

你可以把它理解成玩游戏：

```
开始游戏
   ↓
打过第一关
   ↓
💾 存档
   ↓
第二关
   ↓
💾 存档
   ↓
第三关
```

程序崩了：

```
重新启动
   ↓
读取最近存档
   ↓
继续
```

Agent 也是类似。

---

# 九、Checkpoint 和 Memory 不完全是一回事

这个地方以后你会经常遇到，所以提前分清楚。

### Checkpoint

关注：

> **这一次 Agent 执行到哪了？当前状态是什么？**

例如：

```
thread_id = 001

用户：
“帮我解释RAG”

当前State：
messages = [...]
retrieved_docs = [...]
```

它属于**线程/运行过程级别**的状态保存。

### Store / 长期记忆

关注：

> **这个用户长期有哪些信息？**

例如：

```
user_001

喜欢：
Python
RAG
LangGraph

当前学习阶段：
AI应用开发基础
```

LangGraph 当前文档也明确区分了这两类持久化：checkpointer 用于 thread-scoped 的短期状态，store 用于跨 thread 的长期数据。

所以：

```
Checkpoint
    ↓
“这次对话做到哪里了？”

Long-term Store
    ↓
“这个用户长期记住什么？”
```

---

# 十、把四个东西连起来

现在你可以把整个 LangGraph 理解成：

```
                    State
                     │
                     ↓
            ┌─────────────────┐
            │   StateGraph    │
            │                 │
            │  ┌───────────┐  │
START ─────→│  │   Node A  │  │
            │  └─────┬─────┘  │
            │        │ Edge   │
            │        ↓        │
            │  ┌───────────┐  │
            │  │   Node B  │  │
            │  └─────┬─────┘  │
            │        │        │
            │        ↓        │
            │  ┌───────────┐  │
            │  │   Node C  │  │
            │  └───────────┘  │
            └─────────────────┘
                     │
                     ↓
                Checkpoint
                     │
                     ↓
                保存 State
```

一句话记忆：

|概念|你可以理解成|
|---|---|
|**State**|Agent 当前手里有什么信息|
|**StateGraph**|整个执行流程图|
|**Node**|一个具体工作步骤|
|**Edge**|从一个步骤走向另一个步骤的路线|
|**Conditional Edge**|根据情况决定走哪条路线|
|**Checkpoint**|给当前 State 存档|

在 LangGraph 里，大致对应：

|Dify|LangGraph|含义|
|---|---|---|
|画布|`StateGraph`|整个工作流|
|节点|Node|一个执行步骤|
|连线|Edge|节点之间的流转关系|
|条件分支|Conditional Edge|根据状态决定走哪条路|
|节点之间传递的数据|State|整个流程共享的运行状态|
|保存运行状态|Checkpoint|把某一时刻的 State 存下来|









