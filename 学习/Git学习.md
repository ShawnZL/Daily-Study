# Git学习

## 初始git仓库

```
git init
```

.git 是 Git 的版本库

## 添加问价到Git仓库

```
git add <file>
git commit -m <message>
例子：git commit -m "first commit"
```

## 时光穿梭机

查看当前仓库当前状态，和查看文件之间的 difference。

```
git status
git diff <file>
```

## 版本回退

查看历史记录，显示从最近到最远的提交日志。

```
git log
```

commit ID  xxxxxxxxxxxxxxxxxxxxxxxxxx

Author: username \<useremail\> 

Date: xxxxxxxxxxx

​			commit 信息

```
git log --pretty=online //美观
```

**回退版本**

返回上一次提交版本上去，在Git 内部有个指向当前版本的 HEAD 指针，上一个版本就是 HEAD^，上上一个版本就是 HEAD^^，往上 100 个版本写成 HEAD~100。

```
git reset --hard HEAD^

cat readme.md // 查看信息
```

再回来回到当前版本，注意这里 ID 是 commit ID 前 n 位

```
git reset --hard ID
```

重返未来，使用 git reflog 查看命令历史。以便知道回到未来那个版本

```
git reflog
```

## 工作区和暂存区

工作区：就是文件夹里看到的目录

**版本库 Repository**

隐藏目录 .git 是版本库，其中最重要的就是stage (或者index)。Git自动创建的第一个分支就是master，以及指向 master 的一个指针 HEAD。

1. git add 就是将文件添加进入暂存区

2. git commit 提交更改就是将暂存区内容提交进入当前分支

## 管理修改

使用命令查看工作区和版本库里面最新版本的区别

```
git diff HEAD -- readme.md
```

## 撤销修改

**没有提交** git checkout -- file 丢弃工作区的修改

```
git checkout -- readme.md
```

情况1：readme.md 自修改后还没有被放到暂存区，这样就回退到和版本库一模一样的状态

情况2：readme.md 已经加入了暂存区，又修改，现在，撤销修改就回到添加到暂存区后的状态

这个命令就是让文件回到最近一次 git commit 或者 git add 时的状态

**git add 到暂存区** 但是自己还没有提交

使用命令 git reset HEAD \<file>

```
git reset HEAD readme.md
```

git reset 命令可以回退版本，也可以将暂存区修改回退到工作区。

现在，假设你不但改错了东西，还从暂存区提交到了版本库，怎么办呢？还记得[版本回退](https://www.liaoxuefeng.com/wiki/896043488029600/897013573512192)一节吗？可以回退到上一个版本。不过，这是有条件的，就是你还没有把自己的本地版本库推送到远程。还记得Git是分布式版本控制系统吗？我们后面会讲到远程版本库，一旦你把`stupid boss`提交推送到远程版本库，你就真的惨了……

## 删除文件

文件管理器删除无用文件，使用命令 $ rm file

```
rm readme.md
git status #会告诉什么文件被删除了
```

现在你有两个选择，一是确实要从版本库中删除该文件，那就用命令`git rm`删掉，并且`git commit`然后文件从版本库删除：

```
git rm test.txt
git commit -m "remove test.txt"
```

另一种情况是删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：

```
git checkout -- test.txt
```

`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

## 添加远程库

在本地 **文件夹** 下运行命令

```
git remote add origin git@github.com:ShawnZL/repo-name.git
```

远程库的名字为 origin，是git 默认叫法，同时记住第一次传.git文件

下一步就是把本地所有内容推送到远程库上。第一次推送master分支的所有内容。

```
git push -u origin master
```

`git push` 命令就是把当前分支 `master` 推送到远程。由于远程库是空的，我们第一次推送`master`分支时，加上了`-u`参数，Git不但会把本地的`master`分支内容推送的远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来，在以后的推送或者拉取时就可以简化命令。

从现在起，只要做了本地提交，就可以通过命令，把本地`master`分支的最新修改推送至GitHub，现在，你就拥有了真正的分布式版本库！

```
git push origin master
```

## 删除远程库

先使用`git remote -v` 查看远程库信息

```
git remote -v
```

然后根据名字删除，例如删除 learngit，此处的“删除”其实是解除了本地和远程的绑定关系，并不是物理上删除了远程库。

```
git remote rm learngit
```

## 从远程克隆

执行命令 `git clone` 外加SSH

```
git clone SSH
```

## 分支管理

现在有了分支，就不用怕了。你创建了一个属于你自己的分支，别人看不到，还继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作。

### 创建与合并分支

在版本回退里，每一次提交，Git都将它们串成一条时间线，这条时间线就是一个分支，目前只有一个主分支，就是master分支。HEAD 严格来说不是指向提交，而是指向 master，master才是指向提交，所以 HEAD 指向的就是当前分支。

当我们创建新分支，如 `dev`，Git新建了一个指针叫 `dev`，指向`master`相同的提交，再把 `HEAD` 指向 `dev`，表示当前分支在 `dev`上。

![img](https://static.liaoxuefeng.com/files/attachments/919022363210080/l)

不过，从现在开始，对工作区的修改和提交就是针对`dev`分支了，比如新提交一次后，`dev`指针往前移动一步，而`master`指针不变：

![git-br-dev-fd](https://www.liaoxuefeng.com/files/attachments/919022387118368/l)

假如我们在`dev`上的工作完成了，就可以把`dev`合并到`master`上。Git怎么合并呢？最简单的方法，就是直接把`master`指向`dev`的当前提交，就完成了合并：

![git-br-ff-merge](https://www.liaoxuefeng.com/files/attachments/919022412005504/0)

合并完分支后，甚至可以删除`dev`分支。删除`dev`分支就是把`dev`指针给删掉，删掉后，我们就剩下了一条`master`分支：

创建 `dev` 分支，然后切换到`dev`分支

```
git checkout -b dev #俩个一起完成

git branch branchname #创建新分支 branchname
git checkout branchname #切换现在分支到 branchnname

git branch  #查看当前分支
```

`git branch`命令会列出所有分支，当前分支前面会标一个`*`号。

`git merge` 命令用于合并指定分支到当前分支。合并后，再次查看，会发现与dev原文件相同。

现在，我们把`dev`分支的工作成果合并到`master`分支上

```
$ git merge dev
Updating 5bd7e5a..82c60e6
Fast-forward
 readme.md | 4 +++-
 1 file changed, 3 insertions(+), 1 deletion(-)
```

注意到上面的`Fast-forward`信息，Git告诉我们，这次合并是“快进模式”，也就是直接把`master`指向`dev`的当前提交，所以合并速度非常快。

当然，也不是每次合并都能`Fast-forward`，我们后面会讲其他方式的合并。

合并后删除dev分支，执行命令 `git branch -d 分支名`

```
$ git branch -d dev
Deleted branch dev (was 82c60e6).
```

我们注意到切换分支使用`git checkout <branch>`，而前面讲过的撤销修改则是`git checkout -- <file>`，同一个命令，有两种作用，确实有点令人迷惑。

实际上，切换分支这个动作，用`switch`更科学。因此，最新版本的Git提供了新的`git switch`命令来切换分支：

创建并切换到新的`dev`分支，和直接切换到已有的 `master` 分支:

```
git switch -c dev #创建并切换到新的dev分支
git switch master #切换到已有分支
```

## 解决冲突

当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。

使用 `git log --graph` 命令可以查看分支合并图

## 分支管理策略

通常，合并分支时，如果可能，Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息。如果要强制禁用`Fast forward`模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。

准备合并`dev`分支，请注意`--no-ff`参数，表示禁用`Fast forward`：

```
$ git merge --no-ff -m "merge with no-ff" dev
Merge made by the 'recursive' strategy.
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```

因为本次合并要创建一个新的commit，所以加上`-m`参数，把commit描述写进去。

![git-no-ff-mode](https://www.liaoxuefeng.com/files/attachments/919023225142304/0)

合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并。

## Bug分支

Git 提供了一个 stash 功能，将现场工作存储起来，等到以后恢复现场工作。

```
$ git stash
Saved working directory and index state WIP on dev: f52c633 add merge
```

首先确定要在哪个分支上修复bug，假定需要在`master`分支上修复，就从`master`创建临时分支：

```
git switch -c issue-101
```

然后执行`git add` 和`git commit` 。之后返回 master 分支，然后再次进行合并 master 和 issue-101分支

```
$ git commit -m "fix bug 101"
[issue-101 43eba37] fix bug 101
 1 file changed, 1 insertion(+), 1 deletion(-)

$ git switch master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 4 commits.
  (use "git push" to publish your local commits)

$ git merge --no-ff -m "merged bug fix 101" issue-101
Merge made by the 'recursive' strategy.
 readme.md | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

先查看工作区域，看到工作区是干净的，刚才的工作现场存到哪去了？用`git stash list`命令看看

```
git status
git stash list
```

工作现场还在，Git把stash内容存在某个地方了，但是需要恢复一下，有两个办法：

一是用`git stash apply`恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除；

另一种方式是用`git stash pop`，恢复的同时把stash内容也删了：

```
$ git stash pop
On branch dev
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   hello.py

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt

Dropped refs/stash@{0} (5d677e2ee266f39ea296182fb2354265b91b3b2a)
```

你可以多次stash，恢复的时候，先用`git stash list`查看，然后恢复指定的stash，用命令：

```
$ git stash apply stash@{0}
```

在master分支上修复了bug后，我们要想一想，dev分支是早期从master分支分出来的，所以，这个bug其实在当前dev分支上也存在。同样的bug，要在dev上修复，我们只需要把`43eba37 fix bug 101`这个提交所做的修改“复制”到dev分支。注意：我们只想复制`43eba37 fix bug 101`这个提交所做的修改，并不是把整个master分支merge过来。

为了方便操作，Git专门提供了一个`cherry-pick`命令，让我们能复制一个特定的提交到当前分支：

```
$ git cherry-pick 43eba37
[master 1d4b803] fix bug 101
 1 file changed, 1 insertion(+), 1 deletion(-)
```

Git自动给dev分支做了一次提交，注意这次提交的commit是`1d4b803`，它并不同于master的`43eba37`，因为这两个commit只是改动相同，但确实是两个不同的commit。用`git cherry-pick`，我们就不需要在dev分支上手动再把修bug的过程重复一遍。

## Feature分支

一切顺利的话，feature分支和bug分支是类似的，合并，然后删除。就在此时，接到上级命令，因经费不足，新功能必须取消！

虽然白干了，但是这个包含机密资料的分支还是必须就地销毁：`git branch -d 分支名`

```
$ git branch -d feature-vulcan
error: The branch 'feature-vulcan' is not fully merged.
If you are sure you want to delete it, run 'git branch -D feature-vulcan'.
```

销毁失败。Git友情提醒，`feature-vulcan`分支还没有被合并，如果删除，将丢失掉修改，如果要强行删除，需要使用大写的`-D`参数。

```
$ git branch -D feature-vulcan
Deleted branch feature-vulcan (was a87cbd5).
```

## 多人协作

要查看远程库的信息，用`git remote`

```
$ git remote
origin
```

或者使用`git remote -v` 显示更详细的信息

```
$ git remote -v
origin  git@github.com:ShawnZL/learngit.git (fetch)
origin  git@github.com:ShawnZL/learngit.git (push)
```

上面显示了可以抓取和推送的`origin`的地址。如果没有推送权限，就看不到push的地址

### 推送分支

推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上，如果推送其他分支，就应该其他分支，例如 dev

```
$ git push origin master
$ git push origin dev
```

但是，并不是一定要把本地分支往远程推送，那么，哪些分支需要推送，哪些不需要呢？

- `master`分支是主分支，因此要时刻与远程同步；
- `dev`分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
- bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；
- feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。

总之，就是在Git中，分支完全可以在本地自己藏着玩，是否推送，视你的心情而定！

### 抓取分支

多人协作时候，大家都会往 master 和 dev 分支上推送自己修改

查看远程分支

```
$ git branch -r
```

创建远程分支

```
$ git push origin 分支名 #必须与现在分支对应
```

你的小伙伴要在`dev`分支上开发，就必须创建远程`origin`的`dev`分支到本地，于是他用这个命令创建本地`dev`分支

```
$ git checkout -b dev origin/dev
```

现在，他就可以在`dev`上继续修改，然后，时不时地把`dev`分支`push`到远程：

```
$ git add env.txt

$ git commit -m "add env"
[dev 7a5e5dd] add env
 1 file changed, 1 insertion(+)
 create mode 100644 env.txt

$ git push origin dev
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 308 bytes | 308.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To github.com:michaelliao/learngit.git
   f52c633..7a5e5dd  dev -> dev
```

推送失败，因为你的小伙伴的最新提交和你试图推送的提交有冲突，解决办法也很简单，Git已经提示我们，先用`git pull`把最新的提交从`origin/dev`抓下来，然后，在本地合并，解决冲突，再推送：

```
$ git pull
There is no tracking information for the current branch.
Please specify which branch you want to merge with.
See git-pull(1) for details.

    git pull <remote> <branch>

If you wish to set tracking information for this branch you can do so with:

    git branch --set-upstream-to=origin/<branch> dev
```

`git pull`也失败了，原因是没有指定本地`dev`分支与远程`origin/dev`分支的链接，根据提示，设置`dev`和`origin/dev`的链接：

```
$ git branch --set-upstream-to=origin/dev dev
Branch 'dev' set up to track remote branch 'dev' from 'origin'.
```

再pull：

```
$ git pull
Auto-merging env.txt
CONFLICT (add/add): Merge conflict in env.txt
Automatic merge failed; fix conflicts and then commit the result.
```

这回`git pull`成功，但是合并有冲突，需要手动解决，解决的方法和分支管理中的[解决冲突](http://www.liaoxuefeng.com/wiki/896043488029600/900004111093344)完全一样。解决后，提交，再push。

因此，多人协作的工作模式通常是这样：

1. 首先，可以试图用`git push origin <branch-name>`推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！

如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`。

- 在本地创建和远程分支对应的分支，使用`git checkout -b branch-name origin/branch-name`，本地和远程分支的名称最好一致；

- 建立本地分支和远程分支的关联，使用`git branch --set-upstream branch-name origin/branch-name`；

## Rebase

用`git log`命令看看

```
$ git log --graph --pretty=oneline --abbrev-commit
```

我们输入命令`git rebase`试试：

```
$ git rebase
```

这就是rebase操作的特点：把分叉的提交历史“整理”成一条直线，看上去更直观。缺点是本地的分叉提交已经被修改过了。

## 创建标签

先切换到需要打标签的分支，之后敲打命令 git tag \<name> 就可以打一个新标签

```
$ git tage v1.0
```

默认标签是打在最新提交的commit上的。有时候，如果忘了打标签，比如，现在已经是周五了，但应该在周一打的标签没有打，怎么办？

方法是找到历史提交的commit id，然后打上就可以了：

```
$ git log --pretty=oneline --abbrev-commit
12a631b (HEAD -> master, tag: v1.0, origin/master) merged bug fix 101
4c805e2 fix bug 101
e1e9c68 merge with no-ff
f52c633 add merge
cf810e4 conflict fixed
5dc6824 & simple
14096d0 AND simple
b17d20e branch test
d46f35e remove test.txt
b84166e add test.txt
519219b git tracks changes
e43a48b understand how stage works
1094adb append GPL
e475afc add distributed
eaadf4e wrote a readme file

#然后直接带上标签
$ git tag v0.9 f52c633
```

命令`git tag` 查看标签

```
$ git tag
```

tag 是按照字母排序的。可以直接使用 `git show <tagname>` 查看标签信息。

还可以创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：

```
$ git tag -a <tagname> -m "information" 1094adb
```

1094adb 是commit ID

## 操作标签

- 命令`git push origin <tagname>`可以推送一个本地标签；
- 命令`git push origin --tags`可以推送全部未推送过的本地标签；
- 命令`git tag -d <tagname>`可以删除一个本地标签；
- 命令`git push origin :refs/tags/<tagname>`可以删除一个远程标签。