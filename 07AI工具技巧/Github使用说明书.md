## 1. Git 底层架构

三级状态区域
Git 的精妙设计在于引入了**暂存区（Stage/Index）**，形成了三级架构：
* **工作区**：本地电脑上能直接看到和编辑的工程文件目录。
* **暂存区（Stage / Index）**：`.git` 目录下的缓冲区，允许开发者分批次暂存修改，确保每次提交（Commit）的逻辑单一纯粹。
* **版本库（Repository）**：工作区内的隐藏目录 `.git`，存储了所有的对象索引、历史快照以及分支指针（如 `master`、`dev`）和当前工作指针 `HEAD`。

### 3.3 四大核心命令与生命周期
代码状态流转过程：
* **工作区** -> `git add` -> **暂存区** -> `git commit` -> **本地版本库** -> `git push` -> **远程仓库**

日常协作循环：
```bash
git clone <url>                    # 克隆远程仓库到本地
# 编写与修改代码...
git add .                          # 把修改加入暂存区
git commit -m "完成指定功能"         # 生成一次本地快照
git push                           # 上传至远程仓库
```
建议小步快跑，完成一个小功能就 commit 一次，养成良好的版本控制习惯。

## 2. 环境配置与仓库初始化

### 2.1 全局身份配置
安装 Git 后必须配置全局提交者身份，信息会硬编码到每一次 Commit 记录中：
```bash
# 配置全局用户名与邮箱
git config --global user.name "Your Name"
git config --global user.email "email@example.com"

# 查看全局配置列表
git config --global --list
```
配置文件存储位置：全局配置位于 `~/.gitconfig`，特定仓库配置位于 `.git/config`。

### 2.2 版本库初始化与克隆
```bash
# 在当前目录下初始化一个新的 Git 仓库
git init

# 从远程仓库克隆项目到本地
git clone <repository_url>
git clone git@github.com:username/repo.git
```
`git init` 生成的 `.git` 目录是仓库的核心机密目录，切勿手动修改或删除其内部文件。

## 3. 工作区、暂存区与提交管理

### 3.1 状态检查与修改对比
```bash
# 查看工作区与暂存区的状态
git status

# 查看工作区文件与最新 HEAD 版本的差异
git diff HEAD -- <file>
```

### 3.2 暂存与提交
```bash
# 将指定文件添加到暂存区
git add <file>

# 将当前目录下的所有修改添加到暂存区
git add .

# 提交暂存区的内容到当前分支
git commit -m "提交说明"

# 强制添加被 .gitignore 忽略的文件
git add -f <file>
```
需要注意：Git 追踪的是“修改”而非“文件”，修改后如果不执行 `git add`，直接执行 `git commit` 不会提交该修改。

## 4. 撤销修改、版本回退与文件恢复

### 4.1 撤销修改
```bash
# 场景 1：丢弃工作区的修改（尚未 add 到暂存区）
git checkout -- <file>
git restore <file>

# 场景 2：已 add 到暂存区，重新放回工作区
git reset HEAD <file>
# 随后可再执行 git checkout -- <file> 丢弃工作区修改
```

### 4.2 版本回退
`HEAD` 表示当前版本，`HEAD^` 表示上一版本，`HEAD^^` 表示上上版本，`HEAD~100` 表示前 100 个版本。
```bash
# 查看提交日志
git log
git log --pretty=oneline
git log --graph --pretty=oneline --abbrev-commit

# 查看所有命令历史记录（可寻找过去的 Commit ID）
git reflog

# 强制回退到上一版本
git reset --hard HEAD^

# 根据 Commit ID 跳转到指定版本
git reset --hard <commit_id>
```

### 4.3 文件删除与恢复
```bash
# 从工作区和版本库中同步删除文件
rm <file>
git rm <file>
git commit -m "remove <file>"

# 误删工作区文件后从版本库恢复
git checkout -- <file>
```

## 5. 分支管理策略与冲突解决

### 5.1 分支本质与基础命令
Git 分支本质上是一个指向具体 Commit 的可变指针，`HEAD` 指向当前工作的分支指针。
```bash
# 查看所有分支（*号代表当前分支）
git branch

# 创建新分支
git branch <branch_name>

# 切换分支
git checkout <branch_name>
git switch <branch_name>

# 创建并切换分支
git checkout -b <branch_name>
git switch -c <branch_name>

# 合并指定分支到当前分支
git merge <branch_name>

# 删除已合并的分支
git branch -d <branch_name>
```

### 5.2 禁用快进合并（--no-ff）与团队规范
默认 Fast-forward 合并只移动指针不生成 Commit，删除分支后会丢失历史信息。生产环境推荐禁用快进模式：
```bash
git merge --no-ff -m "merge with no-ff" dev
```
团队分支规范：
* `master` 分支：保持绝对稳定，仅用于发布正式版本。
* `dev` 分支：日常开发分支，成员开发完毕后合并至 `dev`。

### 5.3 解决合并冲突
当两分支修改同一文件同一位置时合并会触发冲突。
1. 打开文件查看 `<<<<<<< HEAD`、`=======`、`>>>>>>>` 冲突标记。
2. 手动编辑保留正确代码并删除标记。
3. 执行 `git add <file>` 与 `git commit -m "fix conflict"` 完成提交。

## 6. 高级分支技巧：Stash, Cherry-pick 与 Rebase

### 6.1 工作现场储藏（git stash）
```bash
git stash                      # 储藏当前未提交的工作现场
git stash list                 # 查看储藏列表
git stash pop                  # 恢复现场同时删除 stash
git stash apply stash@{0}      # 恢复指定现场但不删除 stash
git stash drop                 # 删除指定 stash 内容
```

### 6.2 特定提交复制（git cherry-pick）
复制其他分支的指定 Commit 到当前分支（常用于跨分支同步 Bug 修复）：
```bash
git cherry-pick <commit_id>
```

### 6.3 Feature 分支强行销毁
若新功能分支未合并且需强行废弃，必须使用大写 `-D` 参数：
```bash
git branch -D <feature_branch_name>
```

### 6.4 提交历史整理（Rebase 变基）
```bash
git rebase
```
* 原理：把本地未推送的分叉提交“挪动”到远程最新提交之后，整理成一条直线历史。
* **禁令**：绝对禁止对已经推送到远程公共仓库的 Commit 执行 rebase 操作！

## 7. 远程仓库协作与多源配置

### 7.1 SSH 密钥配置与绑定
```bash
ssh-keygen -t rsa -C "youremail@example.com"
```
生成后，将 `~/.ssh/id_rsa.pub` 内容贴入 GitHub/Gitee 的 SSH Keys 配置中。

### 7.2 关联与推送
```bash
git remote add origin git@github.com:username/repo.git    # 关联远程仓库
git remote -v                                             # 查看远程仓库信息
git remote rm origin                                      # 解除关联
git push -u origin master                                 # 首次推送并建立分支关联
git push origin <branch_name>                             # 日常推送
git pull                                                  # 拉取并合并远程更新
git branch --set-upstream-to=origin/<branch> <branch>     # 建立本地与远程分支关联
```

### 7.3 多远程源管理
```bash
git remote add github git@github.com:username/repo.git
git remote add gitee git@gitee.com:username/repo.git
git push github master
git push gitee master
```

### 7.4 Fork & Pull Request 协作
1. Fork 开源项目到自己账号。
2. 克隆自己账号下的仓库到本地：`git clone git@github.com:my_username/repo.git`。
3. 本地修改提交并推送至自己远程仓库。
4. 在平台上发起 Pull Request 请求官方仓库合并。

## 8. 标签（Tag）管理

标签是针对特定 Commit 的只读快照/不可变指针，通常用于发布里程碑（如 v1.0）。

### 8.1 本地标签操作
```bash
git tag                                                 # 查看所有标签
git tag v1.0                                            # 针对当前 HEAD 打标签
git tag v0.9 <commit_id>                                # 针对指定 Commit 打标签
git tag -a v0.1 -m "version 0.1 released" <commit_id>   # 创建带附注说明标签
git show <tagname>                                      # 查看标签详情
git tag -d <tagname>                                    # 删除本地标签
```

### 8.2 远程标签操作
```bash
git push origin <tagname>                               # 推送指定本地标签
git push origin --tags                                  # 一次性推送所有未推送标签
git tag -d <tagname>                                    # 步骤 1：删除本地标签
git push origin :refs/tags/<tagname>                    # 步骤 2：推送到远程删除
```

## 9. 效率提升、配置别名与 .gitignore

### 9.1 命令别名配置（Alias）
```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.ci commit
git config --global alias.br branch
git config --global alias.unstage 'reset HEAD'
git config --global alias.last 'log -1'

# 彩色格式化分支图别名 (git lg)
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

### 9.2 忽略特殊文件（.gitignore）
在仓库根目录创建 `.gitignore` 文件：
```gitignore
# 忽略系统垃圾文件
Thumbs.db
Desktop.ini
.DS_Store

# 忽略编译中间产物
*.class
*.py[cod]
dist/
build/

# 忽略敏感配置
db.ini

# 排除例外
!.gitignore
!App.class
```
* 检查排查文件规则：`git check-ignore -v <file>`
* 强制添加忽略文件：`git add -f <file>`

### 9.3 图形化工具与自建服务器
* **图形化工具**：推荐使用 SourceTree，针对复杂分支演进与暂存区区块选择提交（Hunk Stage）提供可视化支持。
* **私有 Git 服务器搭建流程**：
  1. Ubuntu 系统安装：`sudo apt install git`
  2. 创建专用用户：`sudo adduser git`
  3. 收集开发者公钥写入 `/home/git/.ssh/authorized_keys`
  4. 初始化裸仓库：`sudo git init --bare sample.git`
  5. 改变所有权：`sudo chown -R git:git sample.git`（必须配置权限）
  6. 安全加固：编辑 `/etc/passwd` 将 `git` 用户 shell 改为 `/usr/bin/git-shell` 阻止直接登录系统。
