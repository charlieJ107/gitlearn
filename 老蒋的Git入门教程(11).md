# 分支的创建和合并

分支就是科幻电影里面的平行宇宙，当你正在电脑前努力学习 Git 的时候，另一个你正在另一个平行宇宙里努力学习 SVN。

 

如果两个平行宇宙互不干扰，那对现在的你也没啥影响。不过，在某个时间点，两个平行宇宙合并了，结果，你既学会了 Git 又学会了 SVN！

 

![img](http://wiki.jikexueyuan.com/project/git-tutorial/images/git18.png)

 

分支在实际中有什么用呢？假设你准备开发一个新功能，但是需要两周才能完成，第一周你写了50%的代码，如果立刻提交，由于代码还没写完，不完整的代码库会导致别人不能干活了。如果等代码全部写完再一次提交，又存在丢失每天进度的巨大风险。

 

现在有了分支，就不用怕了。你创建了一个属于你自己的分支，别人看不到，还继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作。

 

其他版本控制系统如 SVN 等都有分支管理，但是用过之后你会发现，这些版本控制系统创建和切换分支比蜗牛还慢，简直让人无法忍受，结果分支功能成了摆设，大家都不去用。

 

但 Git 的分支是与众不同的，无论创建、切换和删除分支，Git 在 1 秒钟之内就能完成！无论你的版本库是1个文件还是 1 万个文件。

# 创建与合并分支

 

在版本回退里，你已经知道，每次提交，Git 都把它们串成一条时间线，这条时间线就是一个分支。截止到目前，只有一条时间线，在 Git 里，这个分支叫主分支，即 master 分支。HEAD 严格来说不是指向提交，而是指向 master，master 才是指向提交的，所以，HEAD指向的就是当前分支。

 

一开始的时候，master 分支是一条线，Git 用 master 指向最新的提交，再用 HEAD 指向 master，就能确定当前分支，以及当前分支的提交点：

 

![img](http://wiki.jikexueyuan.com/project/git-tutorial/images/branch.png)

 

每次提交，master 分支都会向前移动一步，这样，随着你不断提交，master 分支的线也越来越长：

 

![img](http://wiki.jikexueyuan.com/project/git-tutorial/images/master-branch-forward.gif)

 

当我们创建新的分支，例如 dev 时，Git 新建了一个指针叫 dev，指向 master 相同的提交，再把 HEAD 指向 dev，就表示当前分支在 dev 上：

 

![img](http://wiki.jikexueyuan.com/project/git-tutorial/images/branch1.png)

 

你看，Git 创建一个分支很快，因为除了增加一个 dev 指针，改改 HEAD 的指向，工作区的文件都没有任何变化！

 

不过，从现在开始，对工作区的修改和提交就是针对 dev 分支了，比如新提交一次后，dev 指针往前移动一步，而 master 指针不变：

 

![img](http://wiki.jikexueyuan.com/project/git-tutorial/images/branch2.png)

 

假如我们在 dev 上的工作完成了，就可以把 dev 合并到 master 上。Git 怎么合并呢？最简单的方法，就是直接把 master 指向 dev 的当前提交，就完成了合并：

 

![img](http://wiki.jikexueyuan.com/project/git-tutorial/images/branch3.png)

 

所以 Git 合并分支也很快！就改改指针，工作区内容也不变！

 

合并完分支后，甚至可以删除 dev 分支。删除 dev 分支就是把 dev 指针给删掉，删掉后，我们就剩下了一条 master 分支：

 

![img](http://wiki.jikexueyuan.com/project/git-tutorial/images/branch4.png)

 

真是太神奇了，你看得出来有些提交是通过分支完成的吗？

 

![img](http://wiki.jikexueyuan.com/project/git-tutorial/images/master-and-dev-ff.gif)

 

下面开始实战。

 

首先，我们创建 dev 分支，然后切换到 dev 分支：

 

```
git checkout -b dev
```

 ![1562074721400](.\Assert\1562074721400.png)

`git checkout`命令加上`-b`参数表示创建并切换，相当于以下两条命令：

 

```
git branch dev
git checkout dev
```

 

然后，用`git branch`命令查看当前分支：

 

```
git branch
```

 ![1562074753490](.\\1562074753490.png)

`git branch`命令会列出所有分支，当前分支前面会标一个`*`号。

 

然后，我们就可以在 dev 分支上正常提交，比如对 readme.txt 做个修改，加上一行：

 

```
Creating a new branch is quick.
```

 

然后提交：

 

```
git add readme.txt 
git commit -m "branch test"
```

 ![1562074820848](.\Assert\1562074820848.png)

现在，dev 分支的工作完成，我们就可以切换回 master 分支：

 

```
git checkout master
```

 ![1562074842384](.\Assert\1562074842384.png)

切换回 master 分支后，再查看一个 readme.txt 文件，刚才添加的内容不见了！因为那个提交是在 dev 分支上，而 master 分支此刻的提交点并没有变：

 

![img](http://wiki.jikexueyuan.com/project/git-tutorial/images/branch5.png)

 

现在，我们把 dev 分支的工作成果合并到 master 分支上：

 

```
git merge dev
```

 ![1562074863421](.\Assert\1562074863421.png)

`git merge`命令用于合并指定分支到当前分支。合并后，再查看 readme.txt 的内容，就可以看到，和 dev 分支的最新提交是完全一样的。

 

注意到上面的`Fast-forward`信息，Git 告诉我们，这次合并是“快进模式”，也就是直接把 master 指向 dev 的当前提交，所以合并速度非常快。

 

当然，也不是每次合并都能 Fast-forward，我们后面会将其他方式的合并。

 

合并完成后，就可以放心地删除 dev 分支了：

 

```
git branch -d dev
```

 

删除后，查看 branch，就只剩下 master 分支了：

 

```
git branch
```

 ![1562074899438](.\Assert\1562074899438.png)

因为创建、合并和删除分支非常快，所以 Git 鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在 master 分支上工作效果是一样的，但过程更安全。

 

![img](http://wiki.jikexueyuan.com/project/git-tutorial/images/create-dev-merge-delete.gif)

 

## 小结

 

Git 鼓励大量使用分支：

 

查看分支：git branch

 

创建分支：git branch 

 

切换分支：git checkout 

 

创建+切换分支：git checkout -b 

 

合并某分支到当前分支：git merge 

 
