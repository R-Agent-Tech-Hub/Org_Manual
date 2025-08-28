# 📘 R-Agent-Tech-Hub Mannual

本仓库旨在记录 **R·Agent** 项目在 V1–V5 各阶段的目标、技术路径，以及可参考的开源项目。  
通过逐步引入 OCR、Prompt 链、多 Agent Pipeline、RAG、RLHF 等技术，构建面向医疗器械注册的智能 RA 助手。  

---

## 🚀 技术演进路线与对应项目

### **V1 — 框架生成 & 单模块核查**
- **目标**：验证可行性，能从法规/案例中生成文档框架，完成单模块一致性核查  
- **技术路径**：
  - API 调用（GPT/Gemini 等）
  - Prompt 链 & 规则匹配  
  - OCR 支持（提取扫描件/图片文字）
  - 多 Prompt 分链路管理  
- **相关项目**：
  - [RapidOCR](https://github.com/RapidAI/RapidOCR) — 高效 OCR 工具  
  - [amazon-bedrock-serverless-prompt-chaining](https://github.com/aws-samples/amazon-bedrock-serverless-prompt-chaining) — Prompt 分链示例 (V1-V2)  

---

### **V2 — 全注册包扩展 & 轻量 RAG**
- **目标**：支持完整注册包核查，提高信息检索与生成的准确性  
- **技术路径**：
  - 文本清洗、归一化、切块向量化  
  - 稀疏检索 (BM25) + 向量检索 (FAISS/Chroma/Pinecone) 混合  
  - ReRank (Cross-Encoder / Cohere / BGE)  
  - RAGTruth / 自动评估  
- **相关项目**：
  - [RAGFlow](https://github.com/infiniflow/ragflow) — RAGFlow is an open-source RAG (Retrieval-Augmented Generation) engine based on deep document understanding.
  - [RAGTruth](https://github.com/ParticleMedia/RAGTruth) — 词级事实标注语料库  
  - [ARES](https://github.com/stanford-futuredata/ARES) — RAG 自动评估框架，作者：Jon Saad-Falcon, Omar Khattab, Christopher Potts, Matei Zaharia  

---

### **V3 — Planner + 小模型**
- **目标**：实现任务拆解与模块化审查，支持规划与局部小模型优化  
- **技术路径**：
  - Planner (任务图 / LangGraph)  
  - 针对子任务的 LoRA / PEFT 微调  
  - 示例驱动 + 数据对优化  
- **相关项目**：
  - [LangGraph](docs/workflows/langgraph-workflow-guide.md) — 有状态 Agent 编排框架 (快速落地工作流指南)
  - [Open Deep Research](https://github.com/langchain-ai/open_deep_research) — 基于 LangGraph 的多 Agent 深度研究框架
  - [DeepAgents](https://github.com/langchain-ai/deepagents) — LangChain-ai 的 LLM 驱动自主 Agent 框架
  - [MetaGPT](https://github.com/FoundationAgents/MetaGPT) — 团队式多 Agent 协作框架 (R-Agent V4 参考)
  - [Awesome Foundation Agents](https://github.com/luyu-co/awesome-foundation-agents) — 基础 Agent 资源合集
  - [LLM Agent Papers](https://github.com/yaodongC/LLMAgentPapers) — LLM Agent 必读论文  

---

### **V4 — 多 Agent Pipeline**
- **目标**：形成可用的 RA 审查助手，支持跨文档一致性分析、报告生成  
- **技术路径**：
  - 多 Agent 协作：Retriever / Extractor / Checker / Composer  
  - Auditor → 人机反馈闭环  
  - 子 Agent、消息流转机制  
- **相关项目**：
  - [deepagents](https://github.com/langchain-ai/deepagents) — 深度 Agent 框架（子代理/虚拟文件系统）  
  - [MetaGPT](https://github.com/FoundationAgents/MetaGPT) — 多 Agent 虚拟团队（PM、Dev、Tester 协作）  
  - [open_deep_research](https://github.com/langchain-ai/open_deep_research) — 多 Agent 深度研究框架（Pipeline + 自学习雏形）  

---

### **V5 — RLHF + 自学习**
- **目标**：模拟专业审查员，具备自学习与持续优化能力  
- **技术路径**：
  - RLHF (强化学习+人类反馈)  
  - Learner + Auditor 迭代优化  
  - 行业知识图谱与奖励模型  
- **相关项目**：
  - [awesome-RLHF](https://github.com/opendilab/awesome-RLHF) — RLHF 资源集合  
  - [awesome-foundation-agents](https://github.com/luyu-co/awesome-foundation-agents) — 多 Agent 相关论文与资源合集  
  - [LLMAgentPapers](https://github.com/yaodongC/LLMAgentPapers) — LLM Agent 必读论文合集  

---

## 📚 研究论文与资源参考
- **RAG 自动评估机制**：ARES, RAGTruth  
- **多 Agent 系统设计**：MetaGPT, deepagents, open_deep_research  
- **RLHF 与自学习**：awesome-RLHF, awesome-foundation-agents, LLMagentPapers  

---

## 📌 总结

- **V1 → OCR + Prompt 链**  
- **V2 → RAG + RAGTruth + 自动评估 (ARES)**  
- **V3 → LangGraph (Planner)**  
- **V4 → deepagents / MetaGPT / open_deep_research (多 Agent Pipeline)**  
- **V5 → awesome-RLHF / awesome-foundation-agents / LLMAgentPapers (RLHF + 自学习)**  

本仓库将逐步实现并对接这些开源项目，作为 R·Agent 技术演进的路线图与实验平台。  

## 🛠️ 子项目

### [VB Assistant](https://jianan-huang0609.github.io/VB_Assistant/)
- **定位**：一个轻量化的辅助工具集合，整合了 **PoC-Tech 技术路径** 和 **VB-ToDo 待办清单** 模块。  
- **功能特点**：
  - Sidebar 导航（支持展开/收起）  
  - 融合多页面（PoC-Tech、VB-ToDo），统一入口  
  - 适合作为 **个人项目管理 + 技术路径演示** 的展示平台  
- **未来扩展**：
  - 加入更多实用小工具  
  - 与 R·Agent 主仓库联动，展示 V1–V5 技术演进的 Demo  
