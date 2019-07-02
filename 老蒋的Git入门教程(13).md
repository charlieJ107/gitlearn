# 如何用好分支

## 一、分支管理的基本策略

通常，合并分支时，如果可能，Git 会用 Fast forward 模式，但这种模式下，删除分支后，会丢掉分支信息。

 

如果要强制禁用 Fast forward 模式，Git 就会在 merge 时生成一个新的 commit，这样，从分支历史上就可以看出分支信息。

 

下面我们实战一下`--no-ff`方式的`git merge`：

 

首先，仍然创建并切换 dev 分支：

 

```
git checkout -b dev
```

 

修改 readme.txt 文件，并提交一个新的 commit：

 **![1562078512391](C:\Users\Charl\AppData\Roaming\Typora\typora-user-images\1562078512391.png)**

```
git add readme.txt 
git commit -m "add merge"
```

 

现在，我们切换回 master：

 

```
git checkout master
```

 

准备合并 dev 分支，请注意`--no-ff`参数，表示禁用 Fast forward：

 

```
git merge --no-ff -m "merge with no-ff" dev
```

 ![1562078567672](C:\Users\Charl\AppData\Roaming\Typora\typora-user-images\1562078567672.png)

因为本次合并要创建一个新的 commit，所以加上`-m`参数，把 commit 描述写进去。

 

合并后，我们用`git log`看看分支历史：

 

```
git log --graph --pretty=oneline --abbrev-commit
```

 ![1562078597621](C:\Users\Charl\AppData\Roaming\Typora\typora-user-images\1562078597621.png)

可以看到，不使用 Fast forward 模式，merge 后就像这样：

 

![img](http://wiki.jikexueyuan.com/project/git-tutorial/images/branch8.png)

 

![img](http://wiki.jikexueyuan.com/project/git-tutorial/images/merge-with-no-ff.gif)

 

## 分支策略

 

在实际开发中，我们应该按照几个基本原则进行分支管理：

 

首先，master 分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

 

那在哪干活呢？干活都在 dev 分支上，也就是说，dev 分支是不稳定的，到某个时候，比如 1.0 版本发布时，再把 dev 分支合并到 master 上，在 master 分支发布 1.0 版本；

 

你和你的小伙伴们每个人都在 dev 分支上干活，每个人都有自己的分支，时不时地往 dev 分支上合并就可以了。

 

所以，团队合作的分支看起来就像这样：

 

![img](http://wiki.jikexueyuan.com/project/git-tutorial/images/branch9.png)

 

## 小结

 

Git 分支十分强大，在团队开发中应该充分应用。

 

合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而 fast forward 合并就看不出来曾经做过合并。



## 二、为修改Bug使用分支

软件开发中，bug 就像家常便饭一样。有了bug就需要修复，在 Git 中，由于分支是如此的强大，所以，每个 bug 都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。

 

当你接到一个修复一个代号101的bug的任务时，很自然地，你想创建一个分支 issue-101 来修复它，但是，等等，当前正在 dev 上进行的工作还没有提交：

 

```
$ git status
# On branch dev
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
#       new file:   hello.py
#
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#       modified:   readme.txt
#
```

 

并不是你不想提交，而是工作只进行到一半，还没法提交，预计完成还需1天时间。但是，必须在两个小时内修复该 bug，怎么办？

 

幸好，Git 还提供了一个 stash 功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：

 

```
$ git stash
Saved working directory and index state WIP on dev: 6224937 add merge
HEAD is now at 6224937 add merge
```

 

现在，用`git status`查看工作区，就是干净的（除非有没有被 Git 管理的文件），因此可以放心地创建分支来修复 bug。

 

首先确定要在哪个分支上修复 bug，假定需要在 master 分支上修复，就从 master 创建临时分支：

 

```
$ git checkout master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 6 commits.
$ git checkout -b issue-101
Switched to a new branch 'issue-101'
```

 

现在修复 bug，需要把“Git is free software ...”改为“Git is a free software ...”，然后提交：

 

```
$ git add readme.txt 
$ git commit -m "fix bug 101"
[issue-101 cc17032] fix bug 101
 1 file changed, 1 insertion(+), 1 deletion(-)
```

 

修复完成后，切换到 master 分支，并完成合并，最后删除 issue-101 分支：

 

```
$ git checkout master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 2 commits.
$ git merge --no-ff -m "merged bug fix 101" issue-101
Merge made by the 'recursive' strategy.
 readme.txt |    2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
$ git branch -d issue-101
Deleted branch issue-101 (was cc17032).
```

 

太棒了，原计划两个小时的 bug 修复只花了 5 分钟！现在，是时候接着回到 dev 分支干活了！

 

```
$ git checkout dev
Switched to branch 'dev'
$ git status
# On branch dev
nothing to commit (working directory clean)
```

 

工作区是干净的，刚才的工作现场存到哪去了？用`git stash list`命令看看：

 

```
$ git stash list
stash@{0}: WIP on dev: 6224937 add merge
```

 

工作现场还在，Git 把 stash 内容存在某个地方了，但是需要恢复一下，有两个办法：

 

一是用`git stash apply`恢复，但是恢复后，stash 内容并不删除，你需要用`git stash drop`来删除；

 

另一种方式是用`git stash pop`，恢复的同时把 stash 内容也删了：

 

```
$ git stash pop
# On branch dev
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
#       new file:   hello.py
#
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#       modified:   readme.txt
#
Dropped refs/stash@{0} (f624f8e5f082f2df2bed8a4e09c12fd2943bdd40)
```

 

再用`git stash list`查看，就看不到任何 stash 内容了：

 

```
$ git stash list
```

 

你可以多次 stash，恢复的时候，先用`git stash list`查看，然后恢复指定的 stash，用命令：

 

```
$ git stash apply stash@{0}
```

 

![img](http://wiki.jikexueyuan.com/project/git-tutorial/images/stash-fix-bug.gif)

 

## 小结

 

修复 bug 时，我们会通过创建新的 bug 分支进行修复，然后合并，最后删除；

 

当手头工作没有完成时，先把工作现场`git stash`一下，然后去修复 bug，修复后，再`git stash pop`，回到工作现场。





*注意：接下来的大多数介绍均为模拟场景，本地不适合用截图演示，我们会通过模拟Bash下的命令提示符来展示命令和输出，如果不太记得命令提示符的相关知识，可以回顾本教程开始时《思想动员与战斗准备工作》一章中找到相关内容。*



## 三、为添加功能使用分支

软件开发中，总有无穷无尽的新的功能要不断添加进来。

 

添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个 feature 分支，在上面开发，完成后，合并，最后，删除该 feature 分支。

 

现在，你终于接到了一个新任务：开发代号为 Vulcan 的新功能，该功能计划用于下一代星际飞船。

 

于是准备开发：

 

```
$ git checkout -b feature-vulcan
Switched to a new branch 'feature-vulcan'
```

 

5 分钟后，开发完毕：

 

```
$ git add vulcan.py
$ git status
# On branch feature-vulcan
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
#       new file:   vulcan.py
#
$ git commit -m "add feature vulcan"
[feature-vulcan 756d4af] add feature vulcan
 1 file changed, 2 insertions(+)
 create mode 100644 vulcan.py
```

 

切回 dev，准备合并：

 

```
$ git checkout dev
```

 

一切顺利的话，feature 分支和 bug 分支是类似的，合并，然后删除。

 

但是，

 

就在此时，接到上级命令，因经费不足，新功能必须取消！

 

虽然白干了，但是这个分支还是必须就地销毁：

 

```
$ git branch -d feature-vulcan
error: The branch 'feature-vulcan' is not fully merged.
If you are sure you want to delete it, run 'git branch -D feature-vulcan'.
```

 

销毁失败。Git 友情提醒，feature-vulcan 分支还没有被合并，如果删除，将丢失掉修改，如果要强行删除，需要使用命令`git branch -D feature-vulcan`。

 

现在我们强行删除：

 

```
$ git branch -D feature-vulcan
Deleted branch feature-vulcan (was 756d4af).
```

 

终于删除成功！

 在Linux或mac下，整个过程大概是这样的：

![img](http://wiki.jikexueyuan.com/project/git-tutorial/images/force-delete-br.gif)

 

## 小结

开发一个新 feature，最好新建一个分支； 

如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。

