### 1. 核心概念与学术术语体系
* **大模型的本质**：模型并不是直接输出一整段固定的文本，而是基于输入的上下文，去预测并输出下一个 Token（词元）的概率分布。
* **核心学术术语**：
  * **Token（词元）**：大模型处理文本的基本单位。
  * **上下文窗口**：大模型单次交互中能够接收并处理的最大 Token 限制。
  * **Prompt（提示词）**：引导大模型输出的文本指令。
  * **System Prompt（系统提示词）**：定义 Agent 的系统设定，包括角色设定、人格属性、专业领域视角、权限限制与安全防御边界等。

### 2. 参数设置与生成控制

#### 控制结果的确定性与创造性
* **Temperature（温度）**：
  * *机制*：调整全局概率分布的平滑度。
  * *表现*：数值越小，输出结果越确定，偏向选择概率最高的词元；数值越大，输出越随机，概率低的词元也有机会被选中。
* **Top_p（核采样）**：
  * *机制*：仅在累积概率达到 $p$ 的高概率词元集合中进行采样，直接截断长尾词。
  * *表现*：用于精细控制生成多样性，常与 Temperature 二选一进行调节。

#### 控制结果的长度与结构（工程控本）
* **Max Length（最大长度）**：
  * *机制*：强制限制生成的最大 Token 数。
  * *表现*：直接卡死成本上限，防止大模型发生幻觉或由于指令不清生成过长内容。
* **Stop Sequences（停止序列）**：
  * *机制*：在 API 层面指定一串特殊字符。
  * *表现*：一旦模型生成的 Token 匹配到该字符，立即强行终止生成，用于精准控制结构化输出。

#### 减少重复与鼓励多样化
* **Frequency Penalty（频率惩罚）**：
  * *机制*：根据词元在已有文本中出现的绝对频次（次数）施加惩罚。
  * *表现*：专门针对下一个生成词元，减少单词的机械、循环重复。
* **Presence Penalty（存在惩罚）**：
  * *机制*：只要该词元在已有文本中出现过即施加惩罚，不累加计算出现次数。
  * *表现*：鼓励文本在题材和词汇上产生多样化，防止模型高频重复同一主题或用词。

### 3. 全球主流大模型对比分析

### 闭源前沿模型（综合能力最强）

这些模型在推理、编码、Agent、多模态等多数基准中领先，上下文窗口普遍达到 **1M tokens** 左右。

|排名参考|模型|厂商|主要擅长领域|备注|
|---|---|---|---|---|
|常居前几|**Claude Opus 5.5**|Anthropic|多步推理、Agentic 任务、复杂编码、知识工作、长上下文理解|多个榜单（BenchLM、Artificial Analysis 等）常列第一或并列第一；性价比较高（约 $4/$20 per M tokens）|
|前列|**Claude Fable 5.1 / Fable 5**|Anthropic|编码、Agent、知识工作、高可靠性工作流|部分综合/编码榜单领先；价格较高|
|前列|**Claude Sonnet 5.5**|Anthropic|高吞吐量生产编码、复杂分析、平衡性能|速度快、成本低于 Opus，适合大规模使用|
|前列|**GPT-6 Astra / GPT-6.1 Sol**|OpenAI|软件工程、计算机使用、数学/科学推理、Agent|Astra 偏重工程与工具使用；Sol 系列性价比高、速度快|
|前列|**Gemini 4 Argon**|Google|科学推理、多模态、Agent、部分 Arena 用户偏好|在 GPQA 等科学基准表现突出，价格有竞争力|
|新晋前列|**Muse Wonder / Muse Wonder Full**|Meta|推理、文本创作、代码、大上下文企业工作流|2026年10月发布，首次正面挑战前沿闭源模型|

**其他值得关注的闭源模型**：

- **Grok 系列**（xAI）：在部分速度与推理场景有竞争力。
- OpenAI 的更轻量版本（如 GPT-6 Luna/Terra 等）适合日常与高性价比需求。

### 开放权重/开源领域顶尖模型

中国厂商在此领域整体领先（Artificial Analysis 等开源指数前几名多为中国模型）。

- **MiMo-V2.6-Pro**（小米）：当前最强开源之一，综合与 Agent 能力突出。
- **GLM-5.3 / GLM-5 系列**（智谱 Z.ai）：智能指数高，速度快，性价比优秀。
- **DeepSeek V4 系列**（尤其 V4.1 Flash / Pro）：Agentic 编码、推理表现强，价格极低。
- **Kimi K3**（月之暗面 Moonshot）：编码与长上下文好。
- **Qwen3.8 Max** 等（阿里）：多语言、综合能力均衡。
- Meta 的 Muse Spark 系列、Google Gemma 4 等也有一定存在感，但整体落后于中国头部开源模型。

### 按任务大致擅长方向（基于当前主流基准与社区反馈）

- **复杂推理 / 多步 Agent / 知识工作**：Claude Opus 5.5、Claude Fable 系列、GPT-6 Astra。
- **编程 / 软件工程 / Codex 类任务**：Claude 系列（尤其 Fable/Opus）、GPT-6 Astra/Sol、DeepSeek V4。
- **数学 / 科学推理**：GPT-6 Astra、Gemini 4 Argon、Claude 高阶版本。
- **多模态（图像/视频理解与生成）**：Gemini 系列、部分 Claude 与 GPT 高阶版本。
- **速度 + 性价比 / 大规模部署**：Claude Haiku 5.5、Gemini Flash 系列、GLM Flash、DeepSeek Flash、GPT Luna 等轻量版。
- **长上下文（真正有效利用）**：Claude 与 GPT 高阶版本、部分 Gemini。
- **本地/私有部署**：优先选开源强者（MiMo、GLM、DeepSeek、Qwen）。

### 简要总结与选择建议

- **追求绝对最强综合能力**：优先 Claude Opus 5.5 或 Fable 5.1，其次 GPT-6 Astra / Gemini 4 Argon。
- **追求性价比与速度**：Claude Sonnet 5.5、GPT-6.1 Sol、Gemini Flash、开源的 DeepSeek/GLM Flash。
- **开源/可私有化**：小米 MiMo、智谱 GLM、DeepSeek、月之暗面 Kimi、阿里 Qwen。
- 排名变化很快（几乎每周都有新版本），建议结合具体任务在 LMArena、Artificial Analysis、BenchLM 等实时榜单验证，并做小规模实测。价格、速度和上下文有效性也很关键。

数据来源主要参考 2026年10月中旬的 Artificial Analysis、BenchLM、LMArena、LM Market Cap 等公开排行榜与厂商发布信息。
