# Git 学习笔记

记录我在完成焦糖招新 Git 任务过程中学到的东西和踩过的坑。
环境：Windows 11 + PowerShell + Git for Windows 2.55.0（安装在 `D:\Ainstall\Git`）。

---

## 一、环境准备

安装后在 PowerShell 里验证：

```powershell
git --version
```

看到 `git version 2.55.0.windows.5` 就说明装好了。

## 二、把 Git 和 GitHub 连起来

我理解下来，"连接"其实是三件事：

| 要素 | 作用 | 配置命令 |
| --- | --- | --- |
| 身份 | 提交时记录"谁提交的" | `git config --global user.name` / `user.email` |
| 凭据 | 证明"我有权限推送" | HTTPS 用凭据助手（首次推送弹浏览器登录） |
| 远端 | 本地仓库对应哪个线上仓库 | `git remote add` / `git clone` |

```powershell
# 查看当前配置
git config --global --list

# 设置身份
git config --global user.name "FTY12390"
git config --global user.email "327526976+FTY12390@users.noreply.github.com"

# 网络需要代理时（我的代理客户端端口是 1089）
git config --global http.proxy  http://127.0.0.1:1089
git config --global https.proxy http://127.0.0.1:1089
```

关键点：**远端已经有内容时要用 `git clone`，不要用 `git init` + `push`**，
否则会因为两边历史不一致被拒绝（non-fast-forward）。

```powershell
git clone https://github.com/FTY12390/jotang-recruit-2026-git.git
cd jotang-recruit-2026-git
git remote -v     # 确认 origin 指向自己的仓库
git status        # 确认当前分支和工作区状态
```

## 三、踩坑记录

### 坑 1：`remote: Repository not found`

```
remote: Repository not found.
fatal: repository 'https://github.com/FTYI23390/jotang-recruit-2026-git/' not found
```

**原因**：地址里的用户名打错了（把 `FTY12390` 写成了 `FTYI23390`）。
GitHub 为了防止泄露私有仓库是否存在，对**不存在或没权限**的仓库统一返回
`Repository not found`，所以这个报错看着像权限问题，其实经常只是**地址写错**。

**排查方法**：拿浏览器地址栏里的 URL 逐字符对比，重点关注
用户名里的字母/数字（`1` 和 `I`、`0` 和 `O` 最容易混）。

### 坑 2：`Failed to connect to 127.0.0.1 port 1089`

**原因**：配置了 HTTP 代理，但代理客户端（火种）没启动，1089 端口没人监听。

**排查方法**：

```powershell
netstat -ano | findstr 1089
```

有 `LISTENING` 才是正常的。推送前先确认代理在跑。

### 坑 3：`fatal: not a git repository`

**原因**：在 `git clone` 失败后的空目录里直接跑 `git status` / `git remote -v`。
目录里没有 `.git` 就不是仓库，命令自然用不了。要先确认 clone 成功。

## 四、常用命令速查

```powershell
git status            # 看当前有哪些改动
git add .             # 把所有改动加入暂存区
git add 文件名         # 只暂存某个文件
git commit -m "说明"   # 提交到本地仓库
git push              # 推送到 GitHub
git pull --rebase     # 拉取远端更新
git log --oneline     # 看提交历史（简洁）
git diff              # 看还没暂存的具体改动
git remote -v         # 看远端地址
```

## 五、还没搞懂 / 待学习

- [ ] 分支（branch）怎么用，`merge` 和 `rebase` 的区别
- [ ] `.gitignore` 的写法
- [ ] 冲突（conflict）出现了怎么解决
- [ ] SSH key 方式和 HTTPS 方式的区别
