---
layout: post
title: "Git 高级操作：那些教科书上不会教你的技巧 🔧"
date: 2026-02-03 16:00:00 +0800
categories:
  - 代码技巧
tags:
  - Git
  - 版本控制
  - 高级技巧
  - 程序员
  - 命令行
  - 效率工具
excerpt: "除了add、commit、push，Git还有很多强大但少为人知的技巧。本文分享20+高级操作，让你的Git技能从入门到精通，效率提升10倍。"
header:
  overlay_image: https://images.unsplash.com/photo-1556075798-4825dfaaf498?w=1920
  overlay_filter: 0.6
  teaser: https://images.unsplash.com/photo-1556075798-4825dfaaf498?w=500
toc: true
toc_sticky: true
---

# Git 高级操作：那些教科书上不会教你的技巧

## 前言 🚀

用Git几年了？你可能觉得已经掌握了Git。但我敢打赌，这篇文章里至少有10个技巧是你**不知道的**。

作为每天和Git打交道的开发者，我整理了这些年被低估但超级实用的Git操作。准备好了吗？

## 一、撤销操作 🔄

### 1.1 撤销最后一次提交（保留修改）

```bash
# 撤销最后一次提交，但保留修改在暂存区
git reset --soft HEAD~1

# 撤销最后一次提交，工作目录的修改也保留
git reset HEAD~1

# 撤销最后一次提交，完全丢弃修改（危险！）
git reset --hard HEAD~1
```

**使用场景**：提交后发现有错误，需要修改

### 1.2 修改最后一次提交

```bash
# 修改最后一次提交的消息
git commit --amend -m "新的提交消息"

# 修改最后一次提交，并添加忘记的文件
git add forgotten_file.txt
git commit --amend --no-edit  # 不修改消息，只添加文件
```

**注意**：只对最后一次提交有效，已经推送的提交不要修改！

### 1.3 撤销已推送的提交

```bash
# 创建新的提交来"撤销"之前的提交
git revert <commit-hash>

# 撤销多个连续提交
git revert --no-commit HEAD~3..
git revert -n <commit-hash-1> <commit-hash-2>
git commit -m "Revert commits"
```

**优点**：保留了历史记录，协作开发中安全

### 1.4 撤销本地修改（未暂存）

```bash
# 撤销单个文件
git checkout -- <file>

# 撤销所有未暂存的修改
git checkout -- .

# 更现代的写法（Git 2.23+）
git restore --staged <file>
git restore <file>
```

### 1.5 撤销暂存的文件

```bash
# 从暂存区移除，但保留工作目录修改
git reset HEAD <file>

# Git 2.23+ 新语法
git restore --staged <file>
```

### 1.6 恢复误删的文件

```bash
# 查看删除文件的历史
git log --diff-filter=D --summary | grep delete

# 恢复文件
git checkout <commit-hash>^ -- <file-path>
# 或者
git restore --source=<commit-hash> <file-path>
```

### 1.7 git restore 的强大用法

```bash
# 从特定提交恢复文件
git restore --source=HEAD~3 src/app.js

# 恢复所有修改为某个分支的状态
git restore --source=main -- .

# 恢复所有已删除的文件
git restore --staged .

# 恢复上一次的提交状态
git restore --source=HEAD~1 .
```

## 二、查看历史 📜

### 2.1 更智能的日志查看

```bash
# 简洁版日志
git log --oneline

# 美化版日志（推荐！）
git log --graph --oneline --decorate --all

# 自定义格式
git log --pretty=format:"%h - %an, %ar : %s"

# 图形化界面
git log --all --graph --oneline --simplify-by-decoration
```

### 2.2 搜索提交历史

```bash
# 搜索包含特定内容的提交
git log -S "关键字"

# 搜索特定文件的历史
git log -p --follow <file>

# 搜索特定作者
git log --author="用户名"

# 搜索提交消息
git log --grep="关键字"

# 搜索日期范围内的提交
git log --since="2024-01-01" --until="2024-12-31"
```

### 2.3 查看差异

```bash
# 工作区 vs 暂存区
git diff

# 暂存区 vs 最新提交
git diff --cached

# 工作区 vs 最新提交
git diff HEAD

# 查看两次提交的差异
git diff <commit1> <commit2>

# 查看单个文件的差异
git diff <file>

# 单词级别差异（更易读）
git diff --word-diff
```

### 2.4 查看文件历史

```bash
# 文件的完整修改历史
git log --follow -p <file>

# 谁最后修改了这行代码
git blame <file>

# 谁最后修改了这行（只显示email）
git blame -e <file>

# 忽略空白字符的blame
git blame -w <file>

# 从某个commit开始blame
git blame <commit> -- <file>
```

### 2.5 搜索内容

```bash
# 在所有版本中搜索
git grep "关键字"

# 在特定版本中搜索
git grep "关键字" <commit>

# 搜索正则表达式
git grep -E "regex"

# 统计匹配次数
git grep -c "关键字"

# 显示行号
git grep -n "关键字"
```

## 三、分支操作 🌳

### 3.1 高效的分支管理

```bash
# 列出所有分支
git branch -a

# 列出已合并到当前分支的分支
git branch --merged

# 列出未合并到当前分支的分支
git branch --no-merged

# 删除已合并的分支（安全删除）
git branch --merged | grep -v "\*" | xargs -n 1 git branch -d

# 强制删除分支
git branch -D <branch-name>
```

### 3.2 重命名分支

```bash
# 重命名当前分支
git branch -m new-branch-name

# 重命名其他分支
git branch -m old-branch-name new-branch-name

# 远程分支重命名（需要两步）
git branch -m old new
git push origin :old new
git push origin new
```

### 3.3 比较分支

```bash
# 查看两个分支的差异概览
git diff --stat main..feature

# 查看分支A有但B没有的提交
git log main..feature

# 查看分支B有但A没有的提交
git log feature..main

# 找出分支间的差异文件
git diff --name-only main..feature
```

### 3.4 批量操作

```bash
# 删除本地已合并到main的所有分支（保留main和当前分支）
git checkout main && git branch --merged | grep -v '\*' | xargs -n 1 git branch -d

# 清理远程分支（删除远程已不存在的本地分支）
git fetch --prune
```

## 四、合并操作 🤝

### 4.1 高级合并技巧

```bash
# 合并单个文件/目录
git checkout <branch> -- <path>

# 交互式合并（选择性地合并）
git merge -X theirs feature    # 有冲突时自动采用他们的
git merge -X ours feature      # 有冲突时自动采用我们的

# 合并但保持分支可追溯
git merge --no-ff feature      # 即使可以fast-forward也创建合并提交

# 压缩合并（将多个提交合并成一个）
git merge --squash feature
git commit -m "Merge feature"
```

### 4.2 解决合并冲突

```bash
# 查看冲突文件列表
git diff --name-only --diff-filter=U

# 查看冲突详情
git diff

# 使用合并工具
git mergetool

# 放弃合并
git merge --abort

# 解决冲突后标记为已解决
git add <resolved-file>
git commit  # 不加-m会打开编辑器

# 保留某一方所有更改
git checkout --ours <file>    # 保留当前分支
git checkout --theirs <file>  # 保留被合并的分支
```

### 4.3 git mergetool 配置

```bash
# 使用 vimdiff
git mergetool -t vimdiff

# 配置默认合并工具
git config --global merge.tool vimdiff

# 配置 Beyond Compare
git config --global merge.tool bc3
git config --global mergetool.bc3.cmd "\"bcomp\" \"$LOCAL\" \"$REMOTE\" \"$BASE\" \"$MERGED\""
git config --global mergetool.bc3.trustExitCode false
```

## 五、暂存操作 📦

### 5.1 暂存的进阶用法

```bash
# 暂存当前工作（不提交）
git stash

# 暂存并添加消息
git stash save "工作信息"

# 暂存未跟踪文件
git stash -u
git stash --include-untracked

# 暂存特定文件
git stash push <file>

# 列出所有暂存
git stash list

# 查看暂存内容
git stash show
git stash show -p  # 详细查看

# 应用最近一次暂存
git stash pop

# 应用特定的暂存
git stash apply stash@{0}

# 删除暂存
git stash drop stash@{0}

# 应用并删除最近的暂存
git stash pop
```

### 5.2 暂存的实用场景

```bash
# 场景1：临时切换分支处理紧急bug
git stash
git checkout bugfix-branch
# ... 处理bug ...
git checkout original-branch
git stash pop

# 场景2：保存工作进度
git stash push -m "今天的进度"
git stash list
# 查看：stash@{0}: On main: 今天的进度

# 场景3：清理工作区（但保留进度）
git stash -u
```

### 5.3 工作区管理

```bash
# 将工作区保存为分支
git stash branch new-branch-name

# 从暂存区恢复文件
git stash show stash@{0} -p | git apply -R
```

## 六、交互式操作 🎯

### 6.1 交互式暂存

```bash
# 交互式添加文件
git add -i

# 交互式暂存单个文件的某些行
git add -p <file>
# 选项：
# y - 暂存这一块
# n - 不暂存
# q - 退出
# a - 暂存这块和之后所有
# d - 不暂存这块和之后所有
# g - 跳转到下一个块
# / - 用正则搜索
# e - 手动编辑
# ? - 帮助
```

### 6.2 交互式 rebase

```bash
# 开始交互式 rebase
git rebase -i HEAD~5

# rebase 交互选项：
# pick - 保留提交
# reword - 修改提交消息
# edit - 暂停以修改提交
# squash - 与前一个提交合并
# fixup - 与前一个提交合并，丢弃提交消息
# drop - 删除提交
# reorder - 调整顺序
```

### 6.3 实用 rebase 技巧

```bash
# 合并最近的3个提交
git rebase -i HEAD~3
# 把后两个改成 squash

# 修改历史提交（包含多个步骤）
git rebase -i HEAD~3
# 把要修改的改成 edit
# 然后执行：
git commit --amend
git rebase --continue

# 拆分提交
git rebase -i HEAD~3
# 把要拆分的改成 edit
# 然后：
git reset HEAD~
# 分多次add和commit
git rebase --continue
```

### 6.4 安全地修改历史

```bash
# 修改最后一次提交
git commit --amend

# 修改最后一次提交，但保留原时间
GIT_COMMITTER_DATE="2024-01-01 12:00:00" git commit --amend --date="2024-01-01 12:00:00"

# 批量修改多个提交的消息
git filter-branch -f --msg-filter '
  if echo "$GIT_COMMIT_MSG" | grep -q "OLD-TEXT"; then
    echo "$GIT_COMMIT_MSG" | sed "s/OLD-TEXT/NEW-TEXT/g"
  else
    echo "$GIT_COMMIT_MSG"
  fi
' HEAD~10..HEAD

# 清理空白字符
git filter-branch -f --tree-filter '
  find . -name "*.py" -exec sed -i "s/[[:space:]]*$//" {}
' HEAD~50..HEAD
```

## 七、标签操作 🏷️

### 7.1 标签管理

```bash
# 创建轻量标签
git tag v1.0

# 创建附注标签
git tag -a v1.0 -m "Version 1.0 release"

# 创建带签名的标签
git tag -s v1.0 -m "Signed version 1.0"

# 查看标签列表
git tag -l
git tag -l "v1.*"

# 删除标签
git tag -d v1.0
git push origin :v1.0  # 删除远程标签

# 查看标签详情
git show v1.0

# 推送标签
git push origin v1.0
git push origin --tags  # 推送所有标签

# 用标签检出
git checkout v1.0
```

### 7.2 签名的 GPG 标签

```bash
# 配置 GPG
git config --global user.signingkey <gpg-key-id>

# 签名标签
git tag -s v1.0 -m "My signed tag"

# 验证标签
git tag -v v1.0
```

## 八、子模块操作 📦

### 8.1 子模块基础

```bash
# 添加子模块
git submodule add <repo-url> <path>

# 克隆包含子模块的仓库
git clone --recursive <repo-url>

# 初始化子模块
git submodule update --init

# 更新子模块
git submodule update --remote

# 查看子模块状态
git submodule status
```

### 8.2 子模块进阶

```bash
# 更新所有子模块
git submodule foreach git pull origin main

# 子模块的常用操作
cd <submodule-path>
git checkout main
git pull

# 删除子模块
git submodule deinit <path>
git rm <path>
rm -rf .git/modules/<path>
```

## 九、Git 配置优化 ⚙️

### 9.1 实用别名

```bash
# 基础别名
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.ci commit
git config --global alias.br branch

# 高级别名
git config --global alias.unstage 'restore --staged .'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual '!gitk'
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"

# 查看日志的漂亮格式
git config --global alias.tree 'log --graph --oneline --decorate --all'
```

### 9.2 Git 补全

```bash
# Bash 补全
curl -o ~/.git-completion.bash https://raw.githubusercontent.com/git/git/master/contrib/completion/git-completion.bash

# 在 ~/.bashrc 中添加
source ~/.git-completion.bash

# Zsh 补众
# 使用 oh-my-zsh 的 git 插件
```

### 9.3 Git 配置

```bash
# 查看所有配置
git config --list --show-origin

# 配置用户信息
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# 配置默认分支名
git config --global init.defaultBranch main

# 配置编辑器
git config --global core.editor vim

# 配置差异工具
git config --global merge.tool vimdiff

# 配置颜色
git config --global color.ui auto

# 配置代理
git config --global http.proxy http://proxy.example.com:8080

# 忽略文件权限变更
git config --global core.fileMode false

# 优化性能
git config --global core.precomposeUnicode true
```

### 9.4 Git 钩子

```bash
# 预提交钩子示例（检查代码风格）
#!/bin/bash
# .git/hooks/pre-commit
echo "Running pre-commit checks..."
# 运行 linter
exec < /dev/null
```

## 十、高级技巧 🚀

### 10.1 找回丢失的提交

```bash
# 查看所有操作日志（包括reset、rebase等）
git reflog

# 从 reflog 恢复提交
git checkout <commit-hash>
git branch recovery-branch <commit-hash>
```

### 10.2 Cherry-pick 高级用法

```bash
# Cherry-pick 单个提交
git cherry-pick <commit-hash>

# Cherry-pick 多个提交
git cherry-pick <commit-1> <commit-2>

# Cherry-pick 一系列提交
git cherry-pick <start-commit>^..<end-commit>

# Cherry-pick 并修改
git cherry-pick -n <commit>
# 修改代码
git add .
git commit -m "Custom message"

# Cherry-pick 保留原始时间
GIT_COMMITTER_DATE="$(git show -s --format=%ci <commit>)" git cherry-pick <commit>
```

### 10.3 bisect 二分查找

```bash
# 开始二分查找
git bisect start

# 标记当前版本为"坏"的
git bisect bad

# 标记已知"好"的版本
git bisect good v1.0

# Git 会自动checkout中间的版本
# 测试后标记
git bisect good  # 或 git bisect bad

# 重复直到找到问题提交
# 完成后
git bisect reset
```

### 10.4 打包和传输

```bash
# 创建 bundle（包含完整历史）
git bundle create repo.bundle --all

# 从 bundle 克隆
git clone repo.bundle repo

# 创建增量 bundle
git bundle create new-changes.bundle main~10..main

# 增量更新
git bundle verify new-changes.bundle
git pull new-changes.bundle main
```

### 10.5 git submodule 的替代方案：Git subtree

```bash
# 添加 subtree
git subtree add --prefix=libs/external-repo https://github.com/user/repo.git main --squash

# 更新 subtree
git subtree pull --prefix=libs/external-repo https://github.com/user/repo.git main --squash
```

### 10.6 工作树（Worktree）

```bash
# 创建新的工作树
git worktree add -b feature2 ../myproject-feature2 main

# 查看所有工作树
git worktree list

# 移除工作树
git worktree remove ../myproject-feature2

# 在不同目录同时工作
git worktree add /tmp/checkout-fix main~5
```

### 10.7 git clean 高级用法

```bash
# 清理未跟踪文件（危险！）
git clean -f     # 删除未跟踪文件
git clean -fd    # 删除未跟踪文件和目录
git clean -fdx   # 删除未跟踪文件和忽略的文件

# 预览删除内容（不实际删除）
git clean -n

# 只清理特定文件
git clean -f "*.tmp"
```

### 10.8 优化大型仓库

```bash
# 清理未关联的对象
git gc --aggressive

# 清理过期引用
git reflog expire --expire=now --all && git gc --prune=now --aggressive

# 重建索引
git reset --hard

# 大文件处理（使用 Git LFS）
git lfs install
git lfs track "*.psd"
git add .gitattributes
```

## 十一、GitHub 协作技巧 🤝

### 11.1 PR 自动化

```bash
# 创建 PR 的命令行参数
gh pr create --title "My PR" --body "Description" --base main --head feature

# 快速 PR
git checkout -b feature
# ... work ...
git push -u origin feature
# 访问生成的 URL

# 同步 PR 分支
gh pr checkout 123
gh pr merge 123
```

### 11.2 GitHub CLI 技巧

```bash
# 查看 PR 状态
gh pr status

# 快速审阅 PR
gh pr view 123 --web  # 在浏览器打开

# 搜索 PR
gh pr search "bug" --state=open --sort=updated

# 处理 Issues
gh issue list
gh issue create --title "Bug" --body "Description"
gh issue close 123
```

### 11.3 Git Aliases 效率工具

```bash
# 在 ~/.gitconfig 中添加

[alias]
    # 快速查看
    lg = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
    tree = log --graph --oneline --decorate --all
    
    # 撤销
    unstage = restore --staged
    undo = reset --soft HEAD~1
    cleanbranch = "!git branch --merged | grep -v '\\*' | xargs -n 1 git branch -d"
    
    # 查看
    recent = log --oneline -10
    changes = diff --name-only HEAD~1
    staged = diff --cached --name-only
    
    # 搜索
    search = log -S
    who = blame -e
    
    # 合并
    ours = merge -s ours
    theirs = merge -X theirs
    
    # 高级
    hist = log --pretty=format:'%h %s %an' --graph
    snapshot = stash push -m "snapshot: $(date +%Y-%m-%d_%H:%M:%S)"
```

## 十二、常见问题与解决方案 ❓

### Q1: 如何撤销 git reset --hard？

```bash
# 使用 reflog 找回
git reflog
git checkout <commit-hash>
# 或者创建新分支
git branch recovery <commit-hash>
```

### Q2: 如何恢复已删除的分支？

```bash
git reflog
git checkout -b <branch-name> <commit-hash>
```

### Q3: 怎么查看某个文件的历史版本？

```bash
# 列出所有版本
git log -p --follow -- <file>

# 查看特定版本的内容
git show <commit>:path/to/file
```

### Q4: 合并冲突太多，想放弃？

```bash
git merge --abort
# 或
git rebase --abort
```

### Q5: 如何修改历史的作者信息？

```bash
git filter-branch -f --env-filter '
  if [ "$GIT_COMMITTER_NAME" = "Old Name" ]; then
    export GIT_COMMITTER_NAME="New Name"
    export GIT_COMMITTER_EMAIL="new@email.com"
    export GIT_AUTHOR_NAME="New Name"
    export GIT_AUTHOR_EMAIL="new@email.com"
  fi
' -- --all

# 更安全的方式使用 filter-repo
git filter-repo --name-callback 'return name.replace("Old", "New")' --email-callback 'return email.replace("old@", "new@")'
```

### Q6: Git 很慢怎么办？

```bash
# 检查大文件
git count-objects -vH

# 使用 shallow clone
git clone --depth=1

# 优化配置
git config --global core.preloadindex true
git config --global core.fsmonitor true

# 使用 git status --porcelain 提高脚本效率
```

## 结语 🎯

Git 是一个博大精深的工具，这篇文章只是冰山一角。但掌握这些技巧，绝对能让你在日常开发中**事半功倍**。

记住这些核心原则：

- **多练习**：理论知识不如实际操作
- **善用 help**：`git <command> -h` 是最好的文档
- **备份优先**：危险操作前先 `git stash` 或 `git branch`
- **了解原理**：知道为什么这么做，比知道怎么做更重要

**你的 Git 技巧是什么？欢迎在评论区分享！**

---

**下期预告**：如何从零开始搭建 CI/CD 流水线？

**记得关注旺旺，持续获取技术干货！🐕✨**

#Git #版本控制 #高级技巧 #程序员 #命令行 #效率工具 #技术分享
