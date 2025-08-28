# 🚀 LangGraph - 快速落地 Workflow 开发指南

> **V3 - Planner + 小模型** 技术路径实现指南  
> 本指南描述了如何从 **原型图 → workflow_spec.yaml → LangGraph 骨架 → Studio 可视化 → LangSmith 评测**，逐步迭代 R·Agent / RAifry 的 Agent Pipeline。

## 🎯 概述

本工作流指南对应 **V3 阶段目标**：实现任务拆解与模块化审查，支持规划与局部小模型优化。

### 🚀 核心价值
- **快速原型**：从画图到可运行代码，15分钟完成
- **可视化调试**：Studio 实时查看节点流转，支持 time-travel 调试
- **模块化设计**：支持子任务 LoRA/PEFT 微调，便于小模型优化
- **评测闭环**：LangSmith 自动记录，支持回归测试

### 🛠️ 技术栈
- **Planner**: LangGraph (任务图编排)
- **微调**: LoRA / PEFT (针对子任务)
- **优化**: 示例驱动 + 数据对优化

---

## 1. 理解与原型设计
- 用 **草图 / 白板 / Mermaid / Dify** 先画出流程（节点 + 条件边）。
- 节点示例：Loader → Splitter → Embed → Index / Query → Retrieve → Rerank → Generate → Audit → Report。
- 条件示例：如果检索结果分数过低 → 触发 Query Rewrite → 回到 Retrieve。

---

## 2. 转化为 workflow_spec.yaml
- 将原型图转写为规范化的 YAML，包含：
  - **State 定义**（传递哪些字段）。
  - **nodes**（每个节点的实现路径）。
  - **edges**（节点连接与条件边）。
  - **entry**（入口节点）。

例子：
```yaml
state:
  type: TypedDict
  name: RAGState
  fields:
    question: str
    passages: "List[Dict[str, Any]]"
    answer: str
    meta: "Dict[str, Any]"

nodes:
  - name: retrieve
    impl: "src/nodes/retrieve.py:retrieve"
  - name: rerank
    impl: "src/nodes/rerank.py:rerank"
  - name: generate
    impl: "src/nodes/generate.py:generate"
  - name: audit
    impl: "src/nodes/audit.py:audit"

edges:
  - from: retrieve
    conditional:
      expr: "max([p.get('score',0) for p in passages] or [0]) < 0.5"
      true: rewrite_query
      false: rerank
  - from: rerank
    to: generate
  - from: generate
    to: audit
  - from: audit
    to: END

entry: retrieve
```

---

## 3. 生成 LangGraph 骨架
在 Cursor 里输入指令：

> **指令**：  
> - 读取 `workflow_spec.yaml`，生成以下文件：  
>   - `src/state.py` → 定义 `RAGState`  
>   - `src/nodes/*.py` → 各节点函数 `(state:RAGState)->RAGState`  
>   - `src/graph.py` → StateGraph + 节点/边/条件边  
>   - `scripts/try_local.py` → 本地冒烟测试  
> - 确保可运行，运行命令为：  
>   ```bash
>   pip install -U langgraph langgraph-cli[inmem] langchain-openai
>   export OPENAI_API_KEY=sk-xxx
>   langgraph dev
>   ```

---

## 4. 节点落地
- 把 **V1 脚本逻辑**移入 `src/nodes/`，保持签名一致：  
  ```python
  def node_name(state: RAGState) -> RAGState:
      ...
      return {**state, "xxx": value}
  ```
- 在 `meta` 中记录调试信息（耗时、得分、prompt 版本等）。

---

## 5. 本地运行与可视化
- 在终端（Cursor 内置终端也行）运行：
  ```bash
  langgraph dev
  ```
- 打开终端输出的 **Studio 链接**，即可：
  - 查看流程图
  - 检查每个节点输入/输出
  - 用 time-travel 调试
  - 保存/热重载代码

---

## 6. 接入 LangSmith（观测 & 评测）
1. 在环境中导出：
   ```bash
   export LANGSMITH_TRACING=true
   export LANGSMITH_API_KEY=lsm_xxx
   ```
2. LangSmith 会自动记录每次 run → 在控制台看调用树、耗时、成本。
3. 在 LangSmith 创建数据集（输入/期望输出），用 `evaluate`/`run_on_dataset` 做离线评测。

---

## 7. V1 → V5 演进路径
- **V1：最小链路**  
  Retrieve → Rerank → Generate → Audit
- **V2：工具化 + 条件边**  
  加入工具节点（抓取/知识库切换）；低命中率 → 改写查询 → 回环。
- **V3：并发与子图**  
  多策略检索并行；一致性核查独立成子图。
- **V4：多 Agent 编排**  
  Planner/Router、Executor、Auditor、Reporter 分层。
- **V5：工程化闭环**  
  完整 LangGraph 大图 + LangSmith 回归评测 + 外围系统（n8n/CRM）编排。

---

## 8. 关键约定
- **State 统一**：  
  `RAGState = {question, passages: List[Doc], answer, meta}`，向后兼容。
- **节点签名统一**：  
  `(state: RAGState) -> RAGState`。
- **条件边策略**：  
  按命中率/得分/覆盖度决定走向；低质 → 改写查询；支持回环。
- **评测常开**：  
  开发期始终在 Studio + LangSmith 验证，避免“越改越差”。

---

## 🛠 用途
这套流程让你：
- **从画图到代码无缝切换**（Dify → YAML → Cursor → LangGraph）。  
- **本地快速起跑**（`langgraph dev` + Studio）。  
- **持续演进与评测**（LangSmith 数据集回归，保证质量/成本/时延可控）。

---

## 📚 相关资源
- [LangGraph 官方文档](https://langchain-ai.github.io/langgraph/)
- [LangSmith 评测平台](https://smith.langchain.com/)
- [R-Agent-Tech-Hub 项目主页](https://r-agent-tech-hub.github.io/Org_Manual/)
