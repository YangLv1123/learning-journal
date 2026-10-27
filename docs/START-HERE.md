# START HERE —— 只看这一页

> 其余 10 多份文档都是"词典"，**需要时再查**，不用通读。从今天起，每天只打开这一页。

## 今天（D1 + D2）只做 8 步

### 1. 确认 git
```powershell
git --version
```
应显示 `git version 2.56.0.windows.3`。不对就关窗口、从开始菜单重开。

### 2. 建 GitHub 空仓库
<https://github.com/new> → 名字 `learning-journal` → **Private** → **三个勾都不勾** → Create。
复制 `https://github.com/你的ID/learning-journal.git`。

### 3. 克隆
```powershell
cd $HOME
git clone <刚才复制的地址>
cd learning-journal
```
应出现 `warning: You appear to have cloned an empty repository.`（正常）。

### 4. 建 venv
```powershell
& "$env:LOCALAPPDATA\Programs\Python\Python312\python.exe" -m venv .venv
.venv\Scripts\Activate.ps1
python --version
```
应显示 `Python 3.12.10`，提示符前有 `(.venv)`。

**如果激活报"禁止运行脚本"**（第一次必遇到）：
```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
.venv\Scripts\Activate.ps1
```

### 5. 装依赖
```powershell
python -m pip install -U pip
pip install numpy pandas matplotlib jupyter scikit-learn pytest
pip install torch --index-url https://download.pytorch.org/whl/cpu
```
每条应以 `Successfully installed` 结尾。

### 6. 文档入库 + 首次提交
```powershell
mkdir docs
Copy-Item 'C:\Users\32720\Desktop\实习项目学习\*.md' docs\
Copy-Item 'C:\Users\32720\Desktop\实习项目学习\.gitignore' .
git add .
git commit -m "D01: 环境搭建 + 计划文档入库"
git push
```
应出现 `main -> main`。第一次 push 弹浏览器登录授权，正常。

### 7. 自检
```powershell
powershell -ExecutionPolicy Bypass -File C:\Users\32720\Desktop\实习项目学习\check_env.ps1
```
应全部 `[OK]`，最后一行 `ALL PASS - ready for D2`。→ **D1 完成**

### 8. D2：第一个练习
```powershell
mkdir code\d2_word_count
Copy-Item 'C:\Users\32720\Desktop\实习项目学习\exercises\d2_word_count\*' code\d2_word_count
cd code\d2_word_count
python -m pytest -v
```
应先看到 **4 failed**（正常）。
用 VS Code 打开这个文件夹，按 `15-D2保姆级步骤.md` 补全 `word_count.py`、`csv_io.py`，直到 **4 passed**。

```powershell
cd ..\..
git add .
git commit -m "D02: word_count + csv_io 全绿"
git push
```

---

## 每天的固定循环

| 阶段 | 做什么 | 时间 |
|---|---|---|
| 学 | 看当天任务指定的材料，边看边敲 | 60 分钟 |
| 做 | 跑当天练习包：先红灯，再补代码到绿灯 | 90–120 分钟 |
| 结 | 日志 3 行 + commit + push | 20 分钟 |

**每天只从 `02-60天计划.md` 找"今天第几天"，只做那一天的任务。**

## 卡住时查哪一份（其余不用看）

| 情况 | 看这份 |
|---|---|
| 任何报错 | `08-常见报错速查.md` |
| 不知道代码放哪/怎么提交 | `13-交作业指南.md` |
| 建仓库卡住 | `14-GitHub建仓库图文步骤.md` |
| 当天任务看不懂 | 直接把任务名发我，我展开 |
| 论文相关 | `07-` / `09-` / `04-` 三份 |

## 规则

1. **一次只做一步**，输出不对就停，把屏幕发我；
2. 不囤课、不通读文档、不同时开多个练习；
3. 每天必须有 1 个 commit——这是两个月的"进度条"。
