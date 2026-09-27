chunk = 文档块 / 文本块，指从原始文档中切出来的一小段内容。

chunking = 分块 / 切块，把一个长文档拆分成多个 chunk 的过程。

chunk size = 分块大小，规定每个 chunk 大约包含多少字符、token 等内容。

overlap = 重叠部分，相邻两个 chunk 之间故意保留的重复内容，用来减少上下文被切断的问题。

chunking strategy = 分块策略，决定应该采用什么方式把文档切成 chunk，例如固定长度、按段落、递归切分、语义切分等。

fixed-size chunking = 固定长度分块，按照固定的字符数或 token 数把文档切开。

sentence-based chunking = 按句子分块，以完整句子作为主要切分单位。

paragraph-based chunking = 按段落分块，以段落作为主要切分单位。

recursive chunking = 递归分块，先按照较大结构切分，如果某部分仍然太大，再继续按照更小的结构切分，例如章节 → 段落 → 句子。

semantic chunking = 语义分块，根据文本的语义和主题是否相关来决定切分位置，而不是单纯按照长度切分。

boundary = 分块边界，两个 chunk 之间切开的具体位置。

boundary quality = 边界质量，判断 chunk 是否在合理的位置被切开，例如避免把一个完整概念或一句话从中间切断。

corpus = 语料库 / 文档集合，一组用于分析、训练、检索或测试的文档。

corpus analysis = 语料分析，对整个文档集合的类型、长度、结构、内容特征等进行分析。

corpus-driven = 由实际语料驱动，意思是根据真实文档的特点决定系统参数，而不是凭经验直接设定。

document structure = 文档结构，指文档中的标题、章节、段落、列表、表格、代码等组织方式。

metadata = 元数据，描述文档或 chunk 的附加信息，例如文件名、章节名、页码、作者、日期、来源等。

parent chunk = 父块，较大的上层文本块，通常用于保留更完整的上下文。

child chunk = 子块，从父块中进一步切出来的较小文本块，通常用于更精确地检索。

parent-child chunking = 父子分块，把文档组织成“大块父文本 + 小块子文本”的层级结构。

separator = 分隔符，用来告诉分块程序应该优先在哪里切开，例如换行符、空行、标题符号等。

---

### RAG 核心术语

RAG，Retrieval-Augmented Generation = 检索增强生成，在生成答案之前先从外部知识库中检索相关信息，再让大模型根据检索结果生成答案。

retrieval = 检索 / 找信息，从知识库中找到与用户问题相关的内容。

retriever = 检索器，负责从知识库中寻找与用户查询相关的 chunk。

query = 查询 / 用户问题，用户希望系统寻找答案时输入的内容。

embedding = 嵌入 / 向量表示，把文本转换成机器可以进行相似度计算的向量。

vector = 向量，用一串数字表示文本、图片等对象的特征。

vector database，Vector DB = 向量数据库，专门存储和检索向量数据的数据库。

similarity = 相似度，用来衡量两个文本或向量在意义上有多接近。

cosine similarity = 余弦相似度，通过向量夹角计算两个向量的相似程度，是向量检索中常见的相似度指标。

dense retrieval = 稠密检索，把文本转换成稠密向量，再通过向量相似度进行检索。

sparse retrieval = 稀疏检索，使用关键词等稀疏特征进行检索，例如 BM25。

hybrid retrieval = 混合检索，把稠密向量检索和关键词检索结合起来。

BM25 = 一种经典的关键词检索算法，根据查询词在文档中的出现情况计算相关性。

reranking = 重排序，对第一次检索得到的候选结果再次进行排序，把更相关的内容排到前面。

reranker = 重排序模型，负责重新计算候选文档与用户查询之间的相关性。

generation = 生成，根据检索到的内容生成最终回答的过程。

---

### Evaluation / 评估术语

evaluation = 评估，测试 RAG 系统或检索系统的效果，而不是凭感觉判断好不好。

accuracy = 准确性 / 正确程度，广义上表示结果有多准确；在具体评估中需要进一步明确使用什么指标。

precision = 精确率，在找出来的结果中，有多少是真正相关的。

recall = 召回率，在所有真正相关的结果中，系统找回来了多少。

Precision@K = 前 K 个结果的精确率，只计算排名前 K 的结果中有多少是相关的。

Recall@K = 前 K 个结果的召回率，只看前 K 个结果能够找回多少真正相关的信息。

MRR，Mean Reciprocal Rank = 平均倒数排名，衡量第一个正确结果出现得有多靠前。

NDCG，Normalized Discounted Cumulative Gain = 归一化折损累计增益，衡量检索结果的相关性以及排序位置是否合理，越相关的结果越应该排在前面。

ground truth = 真实答案 / 标准答案，人工或其他可靠方式确定的正确结果，用来和系统实际检索结果进行比较。

retrieval evaluation = 检索评估，通过 Precision、Recall、MRR、NDCG 等指标判断检索系统效果。

sample chunks = 切块样例，从实际切块结果中抽取一些 chunk，用来人工检查切分是否合理。

---

### Skill / Python / 工程术语

strategy = 策略 / 方法，解决某类问题时采用的具体方法。

strategy_results = 策略结果，各种策略实际测试后得到的结果。

recommendation = 推荐 / 建议，根据分析和测试结果给出的推荐方案。

config，configuration = 配置，用具体参数规定系统应该如何运行。

requirements = 需求 / 要求，描述系统需要满足的功能、性能、成本等条件。

pipeline = 流程 / 流水线，把多个处理步骤按照先后顺序连接起来，例如 Parsing → Chunking → Embedding → Retrieval。

parser = 解析器，把 PDF、Word、HTML 等原始文件转换成程序能够理解的结构化内容。

`.py` = Python 源代码文件的文件扩展名，例如 `chunking_optimizer.py` 就是一个 Python 程序。

`python3` = 使用 Python 3 运行 Python 程序的命令。

JSON = 一种结构化数据格式，用键值对等结构保存程序需要读取和交换的数据。

`-o` / `--output` = 输出参数，用来指定程序把结果保存到哪里。

`--extensions` = 文件扩展名参数，用来指定程序处理哪些类型的文件，例如 `.pdf`、`.md`、`.txt`。

config file = 配置文件，把系统运行时需要的参数单独保存起来。

optimizer = 优化器，根据测试结果寻找更合适的参数或方案。

chunking optimizer = 分块优化器，通过测试不同分块方式和参数来寻找更合适的 Chunking Strategy。
Document Loader=搬运工 / 接口适配器
OCR=看着图片认字
Vision-Language Model = 视觉语言模型







## 一、 RAG 的核心定义与解决痛点

### 1. 什么是 RAG？
* **全称**：Retrieval-Augmented Generation（检索增强生成）。
* **核心逻辑**：在将问题提交给大语言模型（LLM）之前，先从私有知识库或外部数据源中检索出高相关性的文本片段，将这些片段作为上下文信息（Context）补充到 Prompt 中，引导大模型基于事实依据生成准确回答。
* **典型应用场景**：企业私有知识库问答、智能客服系统、专业领域文档检索助手。

### 2. 为什么需要 RAG？（直连大模型的三大瓶颈）
如果直接将几百/上千页的文档与用户提问打包发送给大模型，会遇到以下突出问题：
* **上下文窗口限制（Context Window Limit）**：模型输入 token 存在上限，超长输入易导致信息遗忘（“中间遗忘”现象）或推理能力下降。
* **推理成本高昂（High Cost）**：大模型 API 计费直接与输入 Token 数挂钩，每次提问都附带全量文档成本不可接受。
* **推理延迟变大（Slow Speed）**：输入 Token 暴增会显著延长模型的首字响应时间（TTFT）和生成延迟。

RAG 通过“先精准检索少量核心片段，再提交生成”的方式，实现了低成本、低延迟与高准确率的统一。

## 二、 经典 RAG 两大阶段与五大核心环节

RAG 的工作流分为提问前的知识库构建阶段与提问后的实时响应阶段。

### 1. 提问前：数据准备阶段
* **分片（Chunking）**：将海量长文档拆分为独立、语义完整的文本块。策略包括按固定字符数、按段落、按章节或按语义切分。
* **索引（Indexing）**：利用 Embedding 模型将文本片段转换为高维语义向量，并将“原始文本”与“对应向量”同步保存至向量数据库（如 Chroma DB）。
  * **Embedding 模型**：将语义相近的文本映射到高维空间中邻近的向量（如 768 维）。
  * **向量数据库**：同时存储向量与原始文本，向量用于极速相似度比对，原始文本才是最终提交给大模型的真实输入。

### 2. 提问后：回答生成阶段
* **召回（Recall / Retrieval）**：将用户输入的问题转化为向量，在向量数据库中进行相似度计算，提取 Top-K（如 Top 5/10）相似片段。常用比对方法包括余弦相似度（Cosine Similarity）、欧氏距离（Euclidean Distance）与点积（Dot Product）。
* **重排（Reranking）**：使用高精度 Cross-Encoder（交叉编码器）模型对召回的候选片段进行二元联合打分与倒序精筛，提炼出相关度最高且噪音最低的 Top-N（如 Top 3）片段。
  * **“召回 + 重排”双阶段必要性**：向量召回（粗筛）计算极快但精度略低；Cross-Encoder 重排（精筛）精度极高但计算量大。双阶段协同兼顾了速度与精准度。
* **生成（Generation）**：将用户问题与重排提炼出的上下文片段拼接构造 Prompt，送入生成大模型（如 Gemini 2.5 Flash），输出基于事实的最终回答。

## 三、 RAG 架构的三大演进阶段

RAG 技术架构经历了从简单线性流水线到高度解耦模块化的演化：

### 1. 朴素 RAG（Naive RAG）
采用简单的“索引 -> 检索 -> 生成”单向线性流水线。缺点是容易遇到检索不精准、上下文冗余或检索结果无法回答问题等瓶颈。

### 2. 高级 RAG（Advanced RAG）
在朴素 RAG 基础上引入了检索前与检索后处理机制：
* **检索前处理**：包含查询重写（Query Rewrite）、假设性文档嵌入（HyDE）等，优化用户意图表达。
* **检索后处理**：引入重排序（Rerank）与上下文重过滤（Filter），精炼召回内容。

### 3. 模块化 RAG（Modular RAG）
解耦传统固定流向，拆解为高度独立、自由组合的六大功能模块以及底层的控制调度系统。

## 四、 模块化 RAG 核心模块与控制编排

### 1. 索引模块（Indexing）
* **切分优化**：采用“小块检索大块输出”（Small-to-Big），匹配时使用句子级小切片保证精度，输出时提取对应的完整段落或上下文。
* **结构化组织**：构建层级索引结构（树状层级）或引入知识图谱（Knowledge Graph），提取实体与三元组关系。

### 2. 检索前模块（Pre-Retrieval）
* **查询转换**：HyDE（通过生成假设回答进行检索）、反向 HyDE。
* **查询扩展**：子查询拆解（Sub-Query）、验证链（Verification Chain）。
* **查询构建**：Text-to-Cypher / Text-to-SQL，将自然语言转化为数据库专业查询。

### 3. 检索模块（Retrieval）
* **检索器微调**：利用 LM-Supervised 正负样本对比学习，对检索模型进行针对性微调。
* **混合检索（Hybrid Retrieval）**：将基于 Embedding 的稠密向量检索与基于关键词（BM25/TF-IDF）的稀疏检索结合，兼顾语义泛化与精确匹配。

### 4. 检索后模块（Post-Retrieval）
* **多类型重排**：使用 Cross-Encoder 对文本块、代码、表格等多源异构数据联合打分。
* **上下文压缩**：使用 LLM Lingua 等工具进行 Token 级长文本压缩，或利用 LLM 智能挑选最核心内容。

### 5. 生成与验证模块（Generation & Verification）
* **生成器微调**：对大模型进行专有上下文遵循能力微调。
* **结果质检与闭环**：包含幻觉检测（Hallucination Detection）、回答完整度评估、审查模型及隐私数据过滤。检测通过则输出答案，有瑕疵则触发二次检索（Do RAG Again），无条件回答则拒绝回答。

### 6. 控制与调度编排（Orchestration）
* **智能路由（Routing）**：分析 Query 意图，区分仅依赖检索文本的硬提示管线（Hard Prompt Pipeline）与允许结合模型内生知识的软提示管线（Soft Prompt Pipeline）。
* **循环调度（Scheduling）**：建立包含“是否需要检索”、“答案评估与重新检索”的分支闭环控制。
* **知识引导（Knowledge Guide）**：基于知识图谱规划推理路径（Reasoning Path），引导检索与生成交替推进。

### 1. 环境搭建与依赖安装
项目使用 `uv` 进行高效依赖与虚拟环境管理，推荐启动 Jupyter Notebook 进行调试：

```bash
# 初始化项目
mkdir rag && cd rag
uv init .
rm main.py

# 安装核心依赖库
uv add sentence-transformers chromadb google-genai python-dotenv

# 启动 Jupyter Notebook
uv run jupyter notebook
```

**核心依赖库作用**：
* `sentence-transformers`：加载 Embedding 模型与 Cross-Encoder 重排模型。
* `chromadb`：轻量级向量数据库，存储向量与文本片段并执行相似度比对。
* `google-genai`：官方 SDK，用于调用 Gemini 2.5 Flash 生成最终回答。
* `python-dotenv`：读取 `.env` 配置文件中的环境变量（如 `GEMINI_API_KEY`）。


Knowledge Pipeline：

> 把“原始知识”加工成“AI 可以检索和使用的知识”的流水线。

也就是说：

人看到的是：

```
LangGraph 官方网页
```

但是 Agent 需要的是：

```
可以被搜索
可以被召回
可以被引用
可以被评价
```

中间需要一套加工流程。

---

## 一个完整 Knowledge Pipeline

大概是这样：

```
                Knowledge Pipeline


官方文档
   |
   | ① Document Collection
   ↓

原始文档
   |
   | ② Cleaning
   ↓

干净文本
   |
   | ③ Chunking
   ↓

文本块 chunks
   |
   | ④ Embedding
   ↓

向量 embeddings
   |
   | ⑤ Storage
   ↓

Vector Database
   |
   | ⑥ Retrieval
   ↓

相关知识
   |
   | ⑦ Generation
   ↓

Agent回答
```

# 用Python实现RAG系统 —— 学习笔记

## 一、RAG整体流程

分为两大部分：

**1. 用户提问前（数据准备）**

- 分片（Chunking）
- 索引（Indexing）

**2. 用户提问后（问答）**

- 召回（Retrieve）
- 重排（Rerank）
- 生成（Generate）

流程串联：文档 → 分片 → 向量化 → 存入向量数据库（准备完毕）；用户提问 → 问题向量化 → 向量数据库召回top10相关片段 → cross-encoder重排取top3 → 连同问题一起交给大模型生成答案。

---

## 二、环境准备

**所需工具：**

- `uv`：Python包管理器，管理依赖
- Jupyter Notebook：交互式代码执行环境，便于逐段调试

**初始化项目：**

```
uv init rag
```

执行后目录会生成三个文件：

- `main.py`（默认生成，用不到，因为用notebook写代码，可删除）
- `pyproject.toml`（存储项目元信息和依赖列表）
- `README.md`

**安装依赖及各自用途：**

|依赖|用途|
|---|---|
|sentence-transformers|加载embedding模型和cross-encoder模型|
|chromadb|向量数据库|
|google-genai|调用Gemini模型生成答案（用的是gemini-2.5-flash）|
|python-dotenv|把`.env`中的API Key读取为环境变量|

**启动Jupyter Notebook：** 不能用普通方式打开（否则读不到uv装的依赖），要用：

```
uv run jupyter notebook
```

**准备测试文档：** 找一篇模型训练数据中大概率没见过的文章放入项目目录，作为知识库源文件（用于验证RAG确实是基于文档内容回答，而非模型自身知识）。

---

## 三、分片 Chunking

- 定义函数 `split_into_chunks(doc_path) -> List[str]`
- 内部逻辑：读取文件内容 → 按行切分成多个chunk，返回chunk列表

---

## 四、索引 Indexing

### 1. 生成向量（Embedding）

- 引入 `SentenceTransformer`，加载一个embedding模型
- 定义函数 `embed_chunk(chunk) -> List[float]`：调用embedding模型，返回该片段对应的向量（本例中为768维）
- 循环对所有chunk调用该函数，得到全部片段的向量列表
- **注意**：首次运行会从HuggingFace下载模型（约400MB左右），需保持网络畅通，之后运行会很快

### 2. 存入向量数据库

- 使用 **ChromaDB**
- 创建客户端两种方式：
    - `chromadb.EphemeralClient()`：内存型客户端，数据不落盘，程序结束即清空
    - `chromadb.PersistentClient(path="...")`：持久化到磁盘指定文件/目录
- 创建一个 collection（类似传统数据库中的"表"）
- 写入函数：参数为 `chunks`（片段内容列表）和 `embeddings`（对应向量列表），长度需一致
    - 内部需生成 `ids`（如 "0" ~ "9" 的字符串列表，ChromaDB要求每条记录都有唯一ID）
    - 调用collection的写入方法，把chunks、embeddings、ids一起传入
- **踩坑提示**：执行时可能输出若干报错信息，通常是ChromaDB与其他依赖版本不完全兼容导致，不影响实际使用

---

## 五、召回 Retrieve

- 定义函数 `retrieve(query, top_k) -> List[str]`
- 参数：`query`（用户问题），`top_k`（召回数量）
- 内部逻辑：
    1. 调用 `embed_chunk` 把用户问题转为向量
    2. 用该向量在ChromaDB中查询，取最相似的top_k个片段
    3. 返回这些片段内容列表
- **观察点**：单纯基于向量相似度的召回排序不一定准确（正确答案所在片段可能排不到第一），这是重排环节要解决的问题

---

## 六、重排 Rerank

- 使用 **CrossEncoder**（来自 sentence-transformers）
- 定义函数 `rerank(query, retrieved_chunks, top_k) -> List[str]`
- 参数：用户问题、召回的片段列表、重排后保留的数量
- 内部逻辑：
    1. 加载一个CrossEncoder模型
    2. 构造 `(query, chunk)` 配对列表，每个召回片段都和query配一对
    3. 调用CrossEncoder的预测方法，对每一对打分，分数代表query与该片段的相关程度
    4. 将片段内容与对应分数打包，按分数降序排序
    5. 取排序后的前top_k个，只保留片段内容（丢弃分数）
- **注意**：同样首次运行需下载约400MB左右的cross-encoder模型
- **效果**：重排后正确答案对应片段的排名明显优于单纯embedding检索，说明cross-encoder对相关性的判断比向量相似度更准确

---

## 七、生成 Generate

- 使用 **Gemini 2.5 Flash**（免费模型）
    
- 前置准备：
    
    1. 注册获取Gemini API Key
    2. 在项目目录新建 `.env` 文件，写入：`GEMINI_API_KEY=你的key`
    3. 用 `python-dotenv` 的 `load_dotenv()` 把`.env`中的key加载为环境变量（SDK只从环境变量读取，不会自己去读`.env`文件，`.env`只是中转）
    4. 创建一个google genai client，后续用它调用模型
- 定义函数 `generate(query, chunks) -> str`
    
- 参数：用户问题、重排后得到的片段列表
    
- 内部逻辑：
    
    1. 构造prompt（把用户问题和相关片段拼接进去）
    2. 调用Gemini模型，传入prompt
    3. 返回模型生成的答案

---

## 八、关键要点总结

1. **两阶段划分**：准备阶段（分片+索引，用户提问前完成一次）和问答阶段（召回+重排+生成，每次提问都执行）
2. **召回 ≠ 最终结果**：向量检索召回的候选集合排序未必准确，重排（cross-encoder）是提升准确率的关键一步
3. **常见依赖**：sentence-transformers（embedding + cross-encoder）、chromadb（向量库）、google-genai（生成模型调用）、python-dotenv（环境变量管理）
4. **模型下载**：embedding模型和cross-encoder模型首次使用都需联网从HuggingFace下载（各约400MB）
5. **ChromaDB版本报错**：运行中出现的报错信息多为依赖版本不兼容，不影响功能，可忽略