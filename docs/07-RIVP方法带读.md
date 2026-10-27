# 07 · RIVP §3.2–3.4 方法带读（D10–D11 配合使用）

> 用法：D10 动手前先读第 1–2 节，D11 动手前读第 3 节；读完再打开 `exercises/d10_rivp_binding/`、`exercises/d11_rivp_verify/` 写代码。
> 每节末尾有"动手前自查"，答不出来就回正文找，别糊弄过去——面试官问的就是这些。

## 0. 一页总览

- **问题**：微服务持续演进，历史诊断步骤会指向已下线对象 / 不可达路径；候选确认又会重复使用排序证据（自我背书）。
- **两个挑战**：① 对象变化破坏历史流程 → 用"角色化查询计划 + 集合式绑定"解决；② 证据复用破坏验证 → 用"源不相交验证 + 三态"解决。
- **方法**：Scope（定候选集与记忆键）→ Inspect（O_I 排序）→ Verify（O_V 确认，三态）→ Report（排序+状态+溯源）。
- **写回规则**：只有训练期且状态 verified 的流程才进长期记忆；测试期冻结。

## 1. §3.2 类型对象图（Typed Object Graph）

**节点**：incidents（事件）、services、deployments、hosts、versions、signals。
**边**：includes / calls / runs_on / observes——每条边带方向、带时间有效性。

两个必须记住的设计：

1. **溯源（provenance）**：每条查询记录带 source index（模态、源文件、行号、时间戳、lineage_id、源对象），所以任何证据都能追溯到"哪个查询产生了它"。
2. **空结果 ≠ 否定**：缺失观测、空查询、查询错误、成功返回分别记录。空返回绝不当作 contradiction——这就是 §2.2 把观测分成 O_I / O_V 之后的直接后果。

**执行顺序**：先查图上"结构可达性"（能不能走到），通过的对象才真正去查观测数据。图上走不到 → 直接剪枝，不浪费查询。

## 2. §3.3 过程记忆与角色绑定

**角色签名 κ = (τ, η)**：τ 是对象类型，η 是它在有向关系里的角色。由"类型 + 关系方向 + 元数据"决定，**与对象名字无关**——这就是改名不影响记忆的原因。

- 六个查询角色：`incident.entry` / `service.caller` / `service.callee` / `deployment.runtime` / `host.node` / `config.version`
- 三个观测角色：`signal.trace` / `signal.metric` / `signal.log`
- 动作五元组 A = (阶段 q, 源角色 κ_src, 关系 ρ, 目标角色 κ_dst, 所需信号 σ)

**一条 role path 长这样**（论文里的例子）：

```
incident.entry ──includes──► service.caller      (Scope: trace)
service.caller ──calls──►    service.callee      (Inspect: latency)
service.callee ──runs_on──►  deployment.runtime  (Verify: metric)
```

**Eq.1 集合式匹配**：M_t(a_l | u) = { v ∈ V_t : κ_src ∈ κ_t(u), κ_dst ∈ κ_t(v), (u,v) ∈ E_t(ρ) }
即：源对象要满足源角色，目标对象要满足目标角色，且两者之间要有对应关系边。

**Eq.2 三种绑定结果**：

| 匹配数 | 结果 | 含义 |
|---|---|---|
| \|M\| = 1 | bind | 唯一匹配，直接用 |
| \|M\| > 1 | keep | 全部保留为并行分支（新旧版本并存，交给下游证据决定） |
| \|M\| = 0 | prune | 该分支不可达，只剪这条分支 |

三个"为什么"（面试高频）：

- 为什么 keep 全部而不是选一个？因为并行分支都可能是真因，选择权应留给证据（Inspect/Verify），而不是绑定这一步。
- 为什么 prune 只剪分支、不整条回退？局部不兼容 ≠ 全部失效；只有所有分支剪光（U_l = ∅）才回退默认流程。
- 为什么不爆炸？按对象身份去重 + 分支上界 = 候选集；论文实测中位数 3 个分支、最多 11 个。

**记忆键与冻结**：k = (entry role, 局部关系类型集)——不含实体名/版本/标签/下游全路径。K_mem=1 取质量最高的已验证 episode；记忆只提供"角色、关系、顺序"，**永远不能跳过 Scope/Inspect/Verify**。训练期写回，测试期冻结只审计。

## 3. §3.4 Scope–Inspect–Verify–Report

- **Scope**：解析当前事件入口 + 相邻关系 → 候选集、处理顺序、记忆键；绑定计划。建不出有效范围 → `failed`。
- **Inspect**：用 **O_I**（Trace 路径 + 延迟）对候选打分；scorer 冻结；O_V 记录、旧证据、测试标签一律不参与打分。
- **Verify**：用 **O_V**（Log / Metric / 部署状态）检查当前事实 H(c)。
  - support：存在"时间窗内、与候选关联"的确认记录；
  - contradict：存在明确反对候选的记录；
  - unknown：缺失、迟到、无关联——**空结果永远不是否定**；
  - 优先级：对同一事实，contradict > support > unknown。
- **lineage_id 的作用**：一条记录及其衍生特征只能进一个通道（Inspect 或 Verify 二选一），否则"确认"复用了"排序"的证据，等于没验证。
- **Eq.3/4 补充检查**：先检查打分最高的 c(0)；只有结果是 contradict 才允许检查下一个候选（R_verify = 1）；unknown 不触发补充检查。
- **Report**：support 提到最前，contradict 排到所有未矛盾候选之后，unknown 保留原位。状态：verified / uncertain / failed。
- **Eq.5 写回门禁**：write(m_t) = 1[z_t = verified]。只有训练期、被当前证据支持的流程才进长期记忆。

## 4. 论文概念 ↔ 练习代码映射

| 论文概念 | 练习代码（D10/D11） |
|---|---|
| 类型图 G_t | `Graph`（add_node / add_edge） |
| 角色签名 κ_t(v) | `derive_signatures` |
| Eq.1 可行目标 M_t(a\|u) | `match_targets` |
| Eq.2 bind / keep / prune | `bind_plan` 内部逻辑 |
| 三态验证 | `verify` |
| Eq.5 写回门禁 | `write_back` |

## 5. 动手前自查（5 问，写代码前先口头回答）

1. 角色签名为什么由"类型 + 关系方向"决定，而不是对象名字？
2. 多匹配为什么 keep 全部，而不是随机选一个？
3. 局部剪枝和整条回退的区别是什么？各自在什么条件下触发？
4. 空查询结果为什么是 unknown 而不是 contradict？
5. 为什么只有 verified 的训练流程才写回记忆？

## 6. 做完练习后的三个追问（写进当天日志）

1. 你的 `bind_plan` 为什么不会让分支爆炸？（复杂度上界在哪？）
2. 两个历史分支绑定到同一个当前对象时，你的去重按什么做？
3. "证据过期（valid=False）按 unknown 处理"对应论文哪一句？（§3.4.3：缺失/迟到证据是 unknown）
