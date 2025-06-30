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

## 2. BGE-VL-SCREENSHOT https://huggingface.co/BAAI/BGE-VL-Screenshot
在这项工作中，我们正式定义了一种新兴的信息检索范式，称为可视化信息检索（VisIR），其中多模态信息（如文本、图像、表格和图表）通过一个统一的视觉格式——截图——联合表示，用于各种检索应用。
<img src="https://github.com/user-attachments/assets/4204f114-bbc3-4e8b-b068-92f3d3172f5c" width="500" />

## 3. LLamaFactory https://github.com/hiyouga/LLaMA-Factory?tab=readme-ov-file#supported-models
> 零代码训练模型，有点意思：https://aws.amazon.com/cn/blogs/china/building-llm-model-hub-based-on-llamafactory-and-easyr1/
<img src="https://github.com/user-attachments/assets/9fc470a7-bd6b-4a28-bbc6-f068ae74d921" width="300">
<img src="https://github.com/user-attachments/assets/41c92eb9-65e7-4024-89b9-888d7c029bc8" width="300">
<img src="https://github.com/user-attachments/assets/1883ecbb-edfc-4801-b151-4d4df0674071" width="300">
<img src="https://github.com/user-attachments/assets/a42a6d93-9759-48c1-96e7-4cefd2914f71" width="300">

## 4.SwanLab 类似wandb https://docs.swanlab.cn/guide_cloud/general/what-is-swanlab.html

## 5. Dify
**Five Key Elements of Agentic Al**

<img src="https://github.com/user-attachments/assets/7fccd608-954d-44df-873e-28207cdc216d" width="300">
<img src="https://github.com/user-attachments/assets/08ad19b9-9348-4a70-9e4d-b2184632ca77" width="300">

- [ ] Model Providers（以plugin的形式）
- [x] Workflow
- [ ] RAG (Pipeline)
- [ ] Tools & Marketplace
- [x] Tracing

## 6. 无限光年
<img src="https://github.com/user-attachments/assets/7d0b5ba6-a774-4e65-8d61-d40150518ec9" width="300">
<img src="https://github.com/user-attachments/assets/ecf7b148-9a6b-4bf8-bba4-978a8f31246f" width="300">
<img src="https://github.com/user-attachments/assets/98a1a54f-b30e-48da-a6b3-dc0cd70150be" width="300">
<img src="https://github.com/user-attachments/assets/c5292dfb-fb53-4173-8129-e59701db9887" width="300">

几个重点总结一下：
1. 高质量训练数据= 流程化query（主题提取+关键词提取：生成种子问题-问题复杂化-问题压缩） + 上下文query(大模型标注链路）
2. 怎么把大模型改造成向量模型： 选择base模型+剥离lm head只使用encoder; 使用双向注意力机制+ 输入末尾添加[eos] + mean pooling

## 7. 老黄 AI编程白皮书：https://superhuang.feishu.cn/wiki/CBBPwvgEuicVhFkx0s7cPmhpn4e
> MCP很好，很火，但是门槛比较高，未来可期

## 8.jina 

## 9. indexttsv2.0 bilibili  (for fun)



