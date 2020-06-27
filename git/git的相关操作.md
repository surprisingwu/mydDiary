# git的相关操作
## `GIT`常用的指令

*  新建一个本地分支: `git branch branchName`
* 提交本地分支作为远程master的分支: `git push origin branchName:branchName|| git push origin branchName`
* 提交到远程某一个分支:`git push origin originName`
* `删除远程分支:`git push origin :originName || git push origin --delete originName`
* 删除本地的分支: `git branch -d branchName`
* 切换分支,并更新工作区:`git checkout branchName`
* 切换到某一个新分支: `git checkout -b branchName`
* 切换到上一个分支: `git checkout -`
* 新建一个分支,与制定的远程分支建立追踪关系: `git branch --track [branch] [remoteBranch]`
* 合并制定分支到当前分支: `git merge [branch]`
* 恢复暂存区的指定文件到工作区: `git checkout [file]`
* 恢复暂存区所有的文件到工作区: `git checkout .`
* 拉取与远程分支到本地 `git checkout -b localBranchName origin/fetchBranchName`
* `git reset --mixed`:  撤销所有`git add XX`的文件
* `git pull origin master --allow-unrelated-histories`: 本地和远程合并后, 拉取代码时,需要加后面的参数


* 添加文件到暂存区: `git add fileName || 提交所有变动的文件 git add .`
*  删除工作区的文件,并且将这次的删除放入暂存区: ` git rm file1 [file2] ...`
* 停止追踪制定文件,但是该文件会保留在工作区: `git rm --cached [file]`
* 提交暂存区到仓库区: `git commit -m [message]`
* 提交暂存区制定的文件到仓库区: `git commit [file1] [file2]..`
* 提交时显示所有的diff信息: `git commit -v`
* 显示当前分支最近几次的提交: `git reflog`
* 分支合并: `git merge --squash branchName`
* 列出本地分支: `git branch`
* 列出所有远程分支:`git branch -r`
* 列出所有本地分支和远程分支: `git branch -a`

* show all modify files `git status`
* show current version history `git log`
* show commit history and commit modify files `git log --stat`

* 显示暂存区和工作区的差异: `git diff id`
* 显示暂存区和上一个commit的差异: `git diff --cached [file]`
* 显示工作区与当前分支最新commit之间的差异: `git diff HEAD`

* 显示某次提交的元数据和内容变化: `git show [commit]`
* 显示某次提交发生变化的文件: `git show --name-only [commit]`
* 显示某次提交时,某个文件的内容: `git show [commit]:[filename]`

* 下载远程仓库的所有的变动: `git fetch [remote]`
* 显示所有远程仓库: `git remote -v`
* 显示某个远程仓库的信息: `git remote show [remote]`
* 取回远程仓库的变化并与本地分支合并: `git pull [remote] [branch]`
* `git checkout -b  localBranch origin/originBranch`
* 上传本地分支到远程: `git push origin master || git push origin [originName]`
* `Git`远程覆盖本地: `git fetch --all || git reset --hard origin/dev || git pull`
* 暂时将未替替提交的变化移除,稍后再移入: `git stash`
* 从git栈中读取最近一次保存的内容: `git stash pop`
* 回退到上一个版本: `git reset --hard HEAD^`
* 会退到n次提交前: `git reset --hard HEAD~n`
* 回退到制定的提交: `git reset --hard commit_id`


## 一般提交的操作
* `git add .`
* `git commit -m '[message]`
* `git checkout master`
* `git pull --rebase` 
* `git chekout develop`
* `git rebase master` 这个可能会有冲突,解决玩冲突,`git add .    git rebase --continue`
* `git checkout master`
* `git rebase develop`
* `git push`

#### `gitnore的大致规则`
* 匹配模式前`/`代表项目根目录
* 匹配模式最后加`/`代表是目录
* 匹配模式前加`!`代表取反
* `*`代表任意个字符
* `?`匹配任意一个字符
* `**`匹配多级目录

#### `git`撤销本地`commit`

```javascript
git reset  id    // 不会将代码恢复上一个版本
git reset –hard id    // 代码也恢复到id对应的版本
```

#### 合并本地多次的提交
```javascript

git rebase -i HEAD~2 // 合并俩次的提交
// 进入面板  
// p  其他提交合并到这个提交
// s  需要合并的提交

* 选择pick操作，git会应用这个补丁，以同样的提交信息（commit message）保存提交
* 选择reword操作，git会应用这个补丁，但需要重新编辑提交信息
* 选择edit操作，git会应用这个补丁，但会因为amending而终止
* 选择squash操作，git会应用这个补丁，但会与之前的提交合并
* 选择fixup操作，git会应用这个补丁，但会丢掉提交日志
* 选择exec操作，git会在shell中运行这个命令


// 会弹出操作的面板
```

#### 关联远程仓库时, `git pull origin master` 报错`fatal: refusing to merge unrelated histories
`

```javascript
// 可以通过 
git pull origin master --allow-unrelated-histories

git push origin master
```

#### 发版打分支

```js

git tag -a v1.0.1 -m '修复线上bug'

 git push origin v0.1.2 # 将v0.1.2 Tag提交到git服务器
git push origin –-tags # 将本地所有Tag一次性提交到git服务器

```

##### `cherry-pick`的使用

```js
git cherry-pick <commit id>  // 当执行完cherry-pick以后, 将会生成一个新的提交

// 其它相关指令
git cherry-pick --continue
git cherry-pick —quit
git cherry-pick --abort
````

#### `git`查看某一个文件的修改历史

```js
 git log -p -number filename // 查看某一个文件的详细修改记录,  -number最近几次的修改
git log filename // 查看某个文件的提交记录
Git log —pretty=oneline 文件名 // 然后使用下面的命令可列出文件的所有改动历史，
 
```

#### git commit  语义化
```js
refactor: 重构
feature: 新功能
fix: 解决bug
doc: 新增文档
lint: 新增lint配置
```


#mydiary/git#