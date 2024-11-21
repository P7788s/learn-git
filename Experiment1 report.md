# 实验一 Git和Markdown基础

班级： 22物联2

学号： B20220305232

姓名： 师钰茹

Github地址：<https://github.com/P7788s/learn-git>

---

## 实验目的

1. Git基础，使用Git进行版本控制
2. Markdown基础，使用Markdown进行文档编辑
3. vscode的基本使用

## 实验环境

1. Git
2. VSCode
3. VSCode插件

## 实验过程记录

### git config --global user.name/email "name/email"
- 动机: 配置自己的git用户，用于身份标识，一次设置全局配置避免每次项目提交的重复配置。

- 使用的git命令：
  ```shell
  git config --global user.name/email "yourname/email"
  ```
  
- git命令的解释：git config是git用于配置设置的命令。--global表示配置将应用于所有的git仓库，不仅是当前仓库。user.name和user.email是设置用户的名称和邮箱。

### git commit
- 动机: 将你的更改保存到版本历史中。确保所有的修改都有记录，并且可以追踪和回溯。类似于将当前的代码状态保存为一个快照。

- 使用的git命令：

  ```shell
  git commit
  ```

- git命令的解释：commit是一个子命令，表示将更改提交到版本历史。-m "message"：查看描述这次提交的内容和目的。-a：自动将所有已跟踪的文件的更改（即已经添加到 Git 的文件）加入到这次提交中，而无需手动使用 git add。例如：git commit -a -m "Update files"。
- --amend：这个选项允许你修改上一次提交的信息或将新更改添加到上一次提交中，而不是创建一个新的提交。例如：git commit --amend -m "Update last commit message"

### git branch
- 动机: 使用分支可以将不同的功能、特性或修复的开发过程分离开来，避免相互干扰。这有助于团队成员并行开发，提高工作效率。
在主分支（通常是 main 或 master）上保持代码的稳定性，确保在进行重大更改时不会影响到已发布或正在运行的代码。
使用分支可以让不同开发者在各自的分支上工作，然后通过合并（merge）将他们的改动合并到主分支。这简化了协作流程，并减少了冲突的发生。

- 使用的git命令：

  ```shell
  git branch
  ```

- git命令的解释：git branch <branch-name> 命令可以创建一个新的分支。以便随后在其上进行开发。
git branch -d <branch-name> 命令可以删除一个已合并到当前分支的分支。如果分支尚未合并，使用 -D 选项可以强制删除。
git branch -m <old-branch-name> <new-branch-name> 命令可以重命名现有的分支。
git branch -r 可以查看所有的远程分支。
git branch -a 列出所有本地和远程分支。

### git checkout

- 动机: 切换分支，恢复文件到最后一次提交时的状态，-b 选项，git checkout 可以在切换到新分支的同时创建它。

- 使用的git命令：

  ```shell
  git checkout
  ```

- git命令的解释：git checkout <branch-name>：切换到指定的分支。
git checkout -b <new-branch-name>：创建一个新的分支，然后立即切换到这个新分支上。
git checkout <file-name>：将指定文件恢复到最后一次提交的状态。
git checkout <commit-hash>：使用特定的提交哈希值查看该提交的状态。这对于回顾历史更改或调试特定问题非常有帮助。需要注意的是，使用这个命令后，处于“分离头指针”状态，
意味着你不在任何分支上，任何提交都不会被记录在当前分支中，除非你创建一个新分支。

### git merge

- 动机: 将两个分支合并到一起，新建一个分支，在上面开发新功能，开发完成后再合并回主线。
在合并过程中，如果两个分支对同一部分代码进行了不同的修改，Git 会提示开发者解决冲突。
允许多个开发者并行工作，每个人可以在自己的分支上进行开发，最后将所有更改合并到主分支中，确保项目的完整性.
开发者可以在独立的分支上开发新功能或修复bug，完成后使用 git merge 将这些更改合并到主分支中.

- 使用的git命令：

  ```shell
  git merge
  ```

- git命令的解释：git merge <branch-name>：在当前分支上执行该命令将指定的分支（例如 feature 分支）的更改合并进来。

### git rebase
- 动机:创建一个更清晰、线性的提交历史。与 git merge 不同，合并操作会产生一个新的合并提交，导致历史记录变得复杂
 ，而 rebase 可以将多个提交整合成一条直线，从而使历史更简洁明了。
 大型项目中，频繁的合并可能会导致许多合并提交，rebase 允许开发者在更新分支时避免这些合并提交，使得项目历史更整洁。

- 使用的git命令：

  ```shell
  git rebase
  ```

- git命令的解释：git rebase <upstream>：在当前分支上执行该命令会将其基础变更为指定的上游分支（例如 main ），当前分支的提交将被“移到”目标分支的最新提交之后.

### git reset

- 动机: 撤销之前的提交或更改。回到指定的提交。

- 使用的git命令：

  ```shell
  git reset
  ```

- git命令的解释：git reset --HEAD~1将当前分支的 HEAD 回退到上一个提交（HEAD~1），并保持所有的更改在暂存区中，方便立即修改并重新提交。
git reset把所有文件从暂存区移除，但保留工作目录中的更改。你意外地添加了一些文件到暂存区，而不想提交这些更改。

### git revert

- 动机: 为了撤销更改并分享给别人，需要使用 git revert。
当在公开分支（如 main 或 master）上工作时，使用 git revert 是一种更安全的方式来撤销更改，因为它不会改变提交历史，从而避免潜在的合并冲突。
在团队开发中，使用 git revert 允许开发者在不干扰他人工作的情况下安全地撤销不必要的更改，保持代码库的一致性

- 使用的git命令：

  ```shell
  git revert
  ```

- git命令的解释：git revert <commit>这里的 <commit> 是你希望撤销的具体提交的哈希值或引用（如 HEAD~1 表示上一个提交）。
撤销之前的提交或更改。回到指定的提交。
撤销一系列的提交，可以使用：git revert HEAD~3..HEAD 这将逆转从当前提交到前三个提交之间的所有更改，每个提交都会生成一个新的撤销提交。

### git cherry-pick

 - 动机:将一些提交复制到当前所在的位置（HEAD）下面。允许开发者选择特定的提交，并将这些提交的更改应用到当前分支上。
避免引入整个分支的所有历史记录，只需选择需要的提交。


- 使用的git命令：

  ```shell
  git cherry-pick<commit1> <commit2> <commit3>
  ```

- git命令的解释：假设在 feature 分支上开发，并且在 main 分支上完成了一个非常有用的提交（abc1234），可以使用以下命令将这个提交应用到 feature 分支上：
git cherry-pick abc1234
这会将 abc1234 提交的更改复制到 feature 分支上，创建一个新的提交。
git cherry-pick --abort 这将取消当前的 cherry-pick 操作，并恢复到操作开始前的状态。

## 实验总结
  通过本次实验我学会了，安装配置和使用git这个工具，配置git环境的时候会有些问题存在，遇到一个个问题时，要注意在克隆仓库时需要配置网站和本地的密钥.ssh
要注意.ssh文件的路径不能有中文。如果有中文要打开自己电脑上，用管理员身份打开终端power-shell进行配置ssh -T git@gitee.com命令。
  通过在learn git branching的学习，我学会了git基本命令的使用极其工作原理。该网站由简到难提供了git的学习思路，一步步接受了新的知识。
  在创建团队合作的仓库时，学会了如何在gitee网站上创建仓库并将仓库克隆到本地，并且学会了在本地创建分支之后上传到远程仓库。
使用pull命令将本地创建的分支和文件上传到远程仓库。
  并且学习了用vscode插件Markdown PDF，将markdown文件转换为pdf格式的文件。
