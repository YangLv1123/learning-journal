# verimem —— 可验证的 LLM Agent 记忆系统（项目规格书）

> 一句话定位：把 RIVP 论文里"过程记忆 + 角色绑定 + 源不相交验证"三件套，从微服务根因定位迁移到 LLM Agent 记忆——**记忆写入必须经过独立证据验证；记忆按角色而非具体 ID 绑定；系统变化后记忆依然有效**。配套 LLM 用户模拟器做离线评测。
>
> 对应计划：D28 定稿设计 → D29–D35 骨架 → D36–D42 模拟器+评测 → D43–D49 打磨 → D50 开源。

---

## 1. 为什么做这个项目（面试开场白用）

岗位 JD 要的是：LLM/Agent Memory、用户模拟与数据飞轮、离线评测与误差归因、可复现原型。我的论文（RIVP，WWW 2027 在审）做的是微服务诊断里的"可验证过程记忆"，核心洞察是三条：

1. **记忆的价值不在"记了什么"，而在能不能被重新执行、被验证、失效时能被发现。**
2. **写入要有门禁**：候选记忆只有被"与排序证据不重叠"的独立证据支持，才能进长期记忆（否则就是自我背书）。
3. **实体要用角色绑定而非具体名字**：对象改名/换版本后，按角色的记忆仍然能重绑定到新对象。

这三条放到 LLM Agent 的用户记忆上，恰好都是开放问题：Agent 会不会把猜测当事实写进记忆？产品改名后旧记忆会不会永久失效？模拟器用户和真实用户差多少？所以 verimem 不是一个"换个名字的 RIVP"，而是把同一套原则在全新任务上的实现 + 评测。

## 2. 与岗位 JD 的对照表（写进 README 会很好看）

| JD 要求 | verimem 对应 |
|---|---|
| LLM / Agent Memory | 记忆模型（情景/语义/程序）+ 写入门 + 混合检索 + 遗忘/更新 |
| 用户模拟与数据飞轮 | persona 用户模拟器 + sim-to-real gap 分析 + 校准 |
| 离线评测、误差归因 | 评测 harness + 消融 + 失败 case 分类（负结果也报告） |
| 可复现原型 | 固定种子、配置化跑批、CI、pytest、一键复现 |
| 因果/反事实/校准 | 消融对照 + "知识更新前后"的反事实式评测 + 模拟器校准 |
| 用户同理心/隐私 | 全合成数据；记忆删除/更正接口（对应遗忘权）；记忆范围控制 |

## 3. 研究问题（RQ1–RQ4）

- **RQ1 写入门**：三态验证（support / contradict / unknown）+ 仅验证通过才写入长期记忆，能否提升记忆 precision 与跨会话回答一致性？
- **RQ2 角色绑定**：实体改名/换版本后，角色级记忆的存活率是否高于"按名字直写"的记忆？
- **RQ3 检索**：稠密 + recency + importance 混合检索、并过滤 contradicted 记忆，对事实 QA 准确率与 token 成本的影响？
- **RQ4 sim-to-real**：LLM 模拟用户与保留的真实/人工标注样本的一致性多高？系统性偏差在哪里、如何校准？

## 4. 架构

```
用户模拟器 ──► Agent (ReAct) ──► 记忆系统
   │                │              ├─ 写入门 verify: support / contradict / unknown
   │                │              ├─ 角色绑定: user/product/session → entity
   │                │              ├─ 存储: episodic / semantic / procedural + provenance
   │                │              └─ 检索: 混合加权 + 三态过滤
   └──► 评测 harness ──► 指标 (任务成功率/一致性/记忆P/R/陈旧率/token成本)
```

数据流：对话 → 抽取记忆候选（claim + 来源证据）→ 验证（用与来源不重叠的证据/用户确认）→ 写入（仅 support 进长期库，unknown 进短时缓冲，contradict 拒绝并更新旧记忆）→ 检索（过滤 contradicted）→ 生成回答 → 评测。

## 5. 记忆模型

- **Episodic**：会话事件（session, turn, action, result, source）
- **Semantic**：用户事实/偏好（subject, predicate, object, confidence, source, state）
- **Procedural**：任务流程（目标 → 步骤 → 工具 → 前置条件，类似 RIVP 的 role path）
- 每条记忆必带：`provenance`（证据来源）、`created_at`、`last_access`、`importance`、`verification_state`
- **遗忘/更新**：知识更新事件（产品下架、利率调整）使旧语义记忆变 contradicted → 不再被检索，并打上 stale 标签（"陈旧记忆率"指标由此而来）

## 6. 技术栈与预算

- Python 3.12；`openai`（兼容 API）、`faiss-cpu` 或 `chromadb`、`sqlite3`、`pytest`、`ruff`、GitHub Actions
- LLM：DeepSeek / Qwen / GLM 任一 OpenAI 兼容 API（预算 ¥50–200/月）；无 API 时用 ollama 本地小模型（qwen2.5:7b 4bit，16G 内存可跑）或纯规则 agent 先跑通评测
- 无 GPU 要求；有 GPU（如论文用的 4090D）则本地模型更顺

## 7. 评测基准（自建、全合成）

- 数据：20 个 persona × 10 个会话；每 persona 一份金标记忆集
- 任务四类：偏好延续、事实 QA、知识更新（产品下架/利率调整）、多轮服务
- 指标：任务成功率、跨会话回答一致性、记忆 precision/recall、陈旧记忆率、token 成本
- 基线：no-memory / full-context / rolling-summary /（Mem0 若 API 可用）
- 消融：−write gate / −role binding / −recency / −importance

## 8. 里程碑（与 60 天计划对应）

| 里程碑 | 计划日 | 内容 |
|---|---|---|
| M0 | D28 | 设计定稿 + 仓库建立 |
| M1 | D35 | 骨架 v0.1：记忆模型/存储/写入门/角色绑定/最小 agent demo |
| M2 | D42 | 数据 + 模拟器 + 评测 harness + 基线/消融首跑 |
| M3 | D49 | sim-to-real、失败分析、文档、博客草稿 |
| M4 | D56 | v1.0 公开 + README 故事线 + release |

## 9. 风险与兜底（写进 design.md）

- API 不可用 → 本地 ollama 或纯规则 agent 先跑通评测管线
- 时间不够 → 砍 Procedural 记忆和 Mem0 基线，保住 RQ1/RQ2
- 效果不显著 → 如实记录负结果并分析原因（面试加分项，不要美化）
- 合规 → 全部合成数据，README 显式声明；绝不使用公司任何内部数据

## 10. 仓库结构

```
verimem/
├─ README.md            # 故事线 + 快速开始 + 结果摘要 + 局限
├─ LICENSE
├─ pyproject.toml
├─ docs/
│  ├─ design.md         # 本文件的项目版（D28 自己写）
│  ├─ benchmark.md      # 基准定义（D36）
│  └─ results.md        # 结果汇总（D40 起持续更新）
├─ src/verimem/
│  ├─ memory/           # models.py / store.py / retrieve.py
│  ├─ verify.py         # 写入门（三态验证）
│  ├─ binding.py        # 角色绑定与 rebind
│  ├─ agent.py          # ReAct agent
│  ├─ simulator.py      # 用户模拟器（LLM + 规则双实现）
│  ├─ eval/             # 指标与评测 harness
│  └─ llm.py            # LLM 客户端（OpenAI 兼容，可 mock）
├─ tests/
├─ configs/*.yaml
├─ data/                # 只放样例，生成脚本可复现
├─ scripts/             # generate_data.py / run_eval.py / run_ablation.py / demo.py
└─ .github/workflows/ci.yml
```
