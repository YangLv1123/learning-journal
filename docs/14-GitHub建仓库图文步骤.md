# 14 · GitHub 建仓库图文步骤（D1 用）

> 目标：建好两个**空**仓库 `learning-journal`（私有）和 `verimem`（先私有）。

## 0. 前提

- 已注册 GitHub 账号并**验证邮箱**（没验证的话 push 会被拒）。
- 能打开 github.com。国内偶尔打不开：换手机热点、或开代理再试。

## 1. 新建仓库

1. 登录后点右上角 **+** → **New repository**；
2. **Repository name** 填 `learning-journal`；
3. Description 随便写或留空；
4. 选 **Private**（私有）；
5. **下面三个勾全部不要勾**：Add a README file、Add .gitignore、Choose a license；
6. 点绿色 **Create repository**。

创建成功后会看到 "Quick setup — if you've done this kind of thing before" 页面，说明仓库是**空的**，正确。

## 2. 复制地址

页面上有 `https://github.com/<你的ID>/learning-journal.git`，复制它，下一步 clone 用。

## 3. 再建一个 verimem

重复第 1 步，名字换成 `verimem`，同样 **Private + 三个勾都不勾**。这个仓库 D28 之前不用管。

## 4. 常见坑

| 现象 | 原因 | 解决 |
|---|---|---|
| `git push` 报 `rejected ... fetch first` | 建仓库时勾了 README，远端已有提交 | `git pull --rebase origin main` 后再 push；或删掉仓库重建（空仓库） |
| push 提示 Authentication failed | 用了账号密码 | GitHub 不支持密码推送，走浏览器授权（见 08 第 18 条） |
| 页面提示 repository name already exists | 名字被占用 | 换个名字，比如 `learning-journal-2026`，后面命令同步改 |
| 打开 github.com 很慢/超时 | 网络问题 | 换网络（手机热点）或代理；clone 也可改用 SSH（08 第 7 条） |

## 5. 建完后的验证

在 PowerShell 里（换成你的地址）：

```powershell
git clone https://github.com/<你的ID>/learning-journal.git
cd learning-journal
git status
```

看到 `On branch main` 且 `nothing to commit` 就对了。
