## 1. 蚂蚁CGM https://github.com/codefuse-ai/CodeFuse-CGM

>TL;DR: 一个做专门针对coding的LLM，特别之处在于**代码图**和reranker得到的两个模态（图+文字）做了对齐，是一个**模型**；整体流程类似RAG框架。
>不是特别适合处理单个main函数的内容，感觉大材小用了，适合复杂任务。
> 要不试试

- 📜 **Paper**: [Code Graph Model (CGM): A Graph-Integrated Large Language Model for Repository-Level Software Engineering Tasks](https://arxiv.org/abs/2505.16901)
- 🤖 **Model**: [codefuse-ai/CodeFuse-CGM-72B](https://huggingface.co/codefuse-ai/CodeFuse-CGM-72B)
- 📊 **Data**: [codefuse-ai/CodeGraph](https://huggingface.co/datasets/codefuse-ai/CodeGraph)


### 🚀 项目背景

**CGM (Code Graph Model)** 是蚂蚁集团开发的一个**图增强的大语言模型**，专门针对**仓库级别的软件工程任务**，特别是代码修复和bug解决。

**核心成就：**
- 在SWE-Bench-Lite排行榜上达到**44.00%**的解决率（V1.2版本）
- 这是一个专门解决实际软件工程问题的AI系统

### 🏗️ 核心架构：R4链式框架

CGM采用类似RAG的链式结构，包含四个核心模块：

```
Issue输入 → Rewriter → Retriever → Reranker → Reader → 代码补丁输出
```

![image](https://github.com/user-attachments/assets/5de2a2d9-0ec1-4569-ab40-d86c93ed58a2)


#### 1. Rewriter（重写器）
- **作用**：分析issue描述，提取关键信息
- **两个子模块**：
  - **Extractor**：提取相关的文件、类、函数名
  - **Inferer**：生成搜索查询语句
- **输出**：结构化的代码实体和查询关键词

#### 2. Retriever（检索器）
- **作用**：基于代码图进行启发式子图检索
- **核心技术**：使用CodeFuse-CGE-Large模型生成embedding
- **流程**：
  ```
  定位锚点节点 → 扩展连接子图 → 序列化输出
  ```

#### 3. Reranker（重排器）
- **作用**：从检索到的子图中识别最可能需要修改的文件
- **两阶段设计**：
  - Stage 1：初步筛选相关文件
  - Stage 2：为文件打分（1-5分）评估修改可能性

#### 4. Reader（读取器）
- **作用**：基于图结构生成最终的代码补丁
- **特点**：可训练的图增强模型

### 📊 技术特点

#### 代码图表示
- 构建仓库级别的代码图来表示代码结构和上下文关系
- 支持多层次的代码实体：Repo → Package → File → Class → Function
- 包含多种关系：包含、导入、继承、调用等

#### 多框架支持
- 支持Accelerate、DeepSpeed、FSDP
- 支持LoRA、QLoRA和全参数训练
- 高效的大模型微调能力

### 🔄 完整使用流程

#### 步骤1：预处理
```bash
# 生成节点内容
python generate_code_content.py
# 生成代码embedding
python generate_code_embedding.py  
# 生成查询embedding
python generate_rewriter_embedding.py
```

#### 步骤2：Rewriter
```bash
# 生成prompt
python generate_rewriter_prompt.py
# 执行推理
python inference_rewriter.py
# 后处理
python rewriter_output_post_processing.py
```

#### 步骤3：Retriever
```bash
# 定位锚点节点
python locate_anchor_node.py
# 生成子图
python subgraph.py
# 序列化子图
python serialize_subgraph.py
```

#### 步骤4：Reranker
```bash
# 文件重排序
python reranker.py --stage_1_k 10 --stage_2_k 5
```

#### 步骤5：Reader
```bash
# 使用DeepSpeed训练/推理
bash launch/zero2.sh  # 或 zero3.sh
```

