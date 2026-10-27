# 两个月冲刺：从零到「Agent 记忆算法实习生」

- 目标岗位：蚂蚁财富 AI Lab · Agent 记忆算法工程师（实习）
- 起点：自评"什么都不会"，手上有一篇 WWW 2027 **在审**论文（RIVP，AI 辅助完成）
- 时间：2026-10-26 起约 60 天（D60 = 2026-12-24，另留 12-25~12-31 作机动）
- 节奏：工作日晚上 2–3 小时（只做 ⭐ 主线）＋ 周末 6–8 小时（全量）

---

## 一、先说实话（预期管理）

1. **两个月不可能把机器学习"学完"。** 目标不是成为专家，而是三件事：
   ① 面试官问什么都能接得住；② 入职后前两周不掉队；③ GitHub 上有一个能打的项目。
2. **论文是最大资产，也是最大风险。** 它是"投稿在审"（简历写 *Under review*，绝不能写"已录用/已发表"）。因为写作用了 AI 辅助，你必须把论文"认领"成自己的：每一句公式、每一张表都能独立解释——这也是本计划 W1-W2 的任务。
3. **论文主题（微服务根因定位）和岗位主题（Agent 记忆/用户模拟）不完全一致，但不用慌。** 论文里三样东西——**过程记忆、角色绑定、可验证写入**——恰好是"可验证 Agent 记忆"的核心。项目就是把这个想法迁移到 LLM Agent 记忆上，构成一条漂亮的故事线：*RIVP → verimem*。

## 二、三条红线（一次都不许犯）

- 🚫 **公司内部代码/数据/文档，一律不进个人 GitHub。** 项目只用公开数据 + 自己生成的合成数据。金融场景、隐私敏感，出一次事就全完。
- 🚫 **论文状态如实表述。** 面试被问到 AI 辅助时，按 WWW 2027 政策如实说明自己的贡献边界；证明方式是"能逐句复现"（见 `04-论文答辩准备-RIVP.md`）。
- 🚫 **项目结果不造假。** 负结果、失败 case 如实写进 README/results——这是研究品味，是加分项，不是减分项。

## 三、每日节奏（每天雷打不动）

| 时间段 | 内容 | 时长 |
|---|---|---|
| 学 | 看指定材料，边看边写笔记（一个 md 文件随学随记） | 60–90 分钟 |
| 做 | 写代码/做实验（工作日 ⭐ 任务，周末全量） | 2–3h / 6–8h |
| 结 | 写今日日志 3 行（学到什么/卡在哪/明天做什么）＋ git commit ＋ 在 `02-60天计划.md` 里打勾 | 20–30 分钟 |

每周日：50 字周报 + 下周计划，发给我。

## 四、教练协议（怎么让我带你）

每天完成后，把三样东西发给我，我负责答疑、code review、把卡住的任务展开成保姆级步骤：

1. 今日 commit 链接（或截图）
2. 卡点（一句话描述 + 报错原文）
3. 明日打算

任何一天的任务你都可以直接说："把 Dxx 展开成保姆级步骤"，我会给到具体命令和代码。

## 五、假设（与你不符就告诉我，我改计划）

- 无 GPU 假设：LLM 走 OpenAI 兼容 API（DeepSeek / Qwen / GLM，预算 ¥50–200/月）。如果你有论文实验用的 4090D，本地跑 7B 模型也可以。
- Python 从零开始；会用浏览器、会装软件。
- 工作日 2–3h、周末 6–8h；实际 D1 = 2026-10-27（周二），之后按 D 编号顺延（计划文件里的日期仅作参考）。

## 六、文件导航

| 文件 | 用途 |
|---|---|
| `02-60天计划.md` | **每天做什么（核心，按天打勾）** |
| `03-项目设计-verimem.md` | GitHub 项目规格书（研究问题/架构/指标/里程碑） |
| `04-论文答辩准备-RIVP.md` | 论文 30+ 问自测、弱点清单、桥接岗位的话术 |
| `05-学习资源清单.md` | 课程 / 论文 / 代码链接（按周索引） |
| `06-每日日志模板.md` | 每天打卡格式（复制一份照着填） |
| `07-RIVP方法带读.md` | RIVP §3.2–3.4 逐节带读（D10–D11 配合用） |
| `08-常见报错速查.md` | D1–D6 环境报错对号入座表 |
| `09-RIVP符号术语自测卡.md` | 论文全部符号的自测卡（D7/D13/D52 用） |
| `10-论文精读模板.md` | W4 论文精读笔记模板（D23–D27 每篇复制一份） |
| `11-D3类与pytest带读.md` | D3 类与对象、pytest 带读（配 d3_counter 练习） |
| `12-W4论文带读.md` | MemGPT/Generative Agents/Reflexion/Mem0 四篇导读（D23–D25 用） |
| `13-交作业指南.md` | 代码放哪、git 怎么交、三个新手坑（D2 起每天用） |
| `14-GitHub建仓库图文步骤.md` | 建 learning-journal / verimem 两个空仓库的逐步说明 |
| `15-D2保姆级步骤.md` | D2 的完整操作指引（学习清单/提示/调试/提交） |
| `exercises/` | 各天练习的起步代码 + 测试（先红灯、后绿灯） |

## 七、今天就开始（D1 清单）

在 Windows PowerShell 里逐条执行：

```powershell
# 1) 装工具（已装则跳过）
winget install Python.Python.3.12 Git.Git Microsoft.VisualStudioCode

# 2) 验证
python --version    # 应为 3.12.x
git --version
code --version

# 3) git 基础配置（换成你自己的名字/邮箱）
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
git config --global init.defaultBranch main

# 4) 在 GitHub 网页上建两个空仓库：learning-journal（可设私有）、verimem（先私有，D50 公开）
#    不会建看《14-GitHub建仓库图文步骤.md》，重点：三个勾都不要勾，保持空仓库
#    然后克隆学习日志仓库：
git clone https://github.com/<你的ID>/learning-journal.git
cd learning-journal

# 5) 建虚拟环境并装依赖
# 注意：你机器上有 Python 3.12（推荐）和 3.14；torch 对 3.14 可能还没有 Windows 预编译包，
# 所以显式用 3.12 建 venv（路径不存在就换成你实际的 python）：
& "$env:LOCALAPPDATA\Programs\Python\Python312\python.exe" -m venv .venv
.venv\Scripts\Activate.ps1
python --version    # 应显示 3.12.x
python -m pip install -U pip
pip install numpy pandas matplotlib jupyter scikit-learn pytest
pip install torch --index-url https://download.pytorch.org/whl/cpu   # 无GPU用CPU版；有NVIDIA显卡则去 pytorch.org 装 CUDA 版

# 6) 把本目录的计划文件拷进 learning-journal/docs/，首次提交推送
# 7) 环境自检：全部 [OK] 再开始 D2（若提示"禁止运行脚本"，用下面的方式跑）
powershell -ExecutionPolicy Bypass -File .\check_env.ps1
```

然后打开 `02-60天计划.md`，从 D1 开始打勾。
