# 解决冲突

人生不如意之事十之八九，合并分支往往也不是一帆风顺的。

 

准备新的 feature1 分支，继续我们的新分支开发：

 

```
git checkout -b feature1
```

 

修改 readme.txt 最后一行，改为：

 

```
Creating a new branch is quick AND simple.
```

 

在 feature1 分支上提交：

 

```
git add readme.txt 
git commit -m "AND simple"
```

 

切换到 master 分支：

 

```
git checkout master
```

 ![1562074982751](.\Assert\1562074982751.png)

Git 还会自动提示我们当前 master 分支比远程的 master 分支要超前 1 个提交。

 

在 master 分支上把 readme.txt 文件的最后一行改为：

 

```
Creating a new branch is quick & simple.
```

 

提交：

 

```
git add readme.txt 
git commit -m "& simple"
```

 

现在，master 分支和 feature1 分支各自都分别有新的提交，变成了这样：

 

![img](http://wiki.jikexueyuan.com/project/git-tutorial/images/branch6.png)

 

这种情况下，Git 无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就可能会有冲突，我们试试看：

 

```
git merge feature1
```

 ![1562075038456](.\Assert\1562075038456.png)

果然冲突了！Git 告诉我们，readme.txt 文件存在冲突，必须手动解决冲突后再提交。git status 也可以告诉我们冲突的文件：

 

```
git status
```

 ![1562075060998](.\Assert\1562075060998.png)

我们可以直接查看 readme.txt 的内容：

 ![1562075093433](.\Assert\1562075093433.png)

把冲突的地方修改一下, 让它变成“从原来的基础上变成了一个新的样子”， 比如：

![1562075218938](.\Assert\1562075218938.png)

再提交：

 

```
git add readme.txt 
git commit -m "conflict fixed"
```

 

现在，master 分支和 feature1 分支变成了下图所示：

 

![img](http://wiki.jikexueyuan.com/project/git-tutorial/images/branch7.png)

 

用带参数的`git log`也可以看到分支的合并情况：

 

```
git log --graph --pretty=oneline --abbrev-commit
```

 ![1562075166187](.\Assert\1562075166187.png)

最后，删除 feature1 分支：

 

```
git branch -d feature1
```

 ![1562075248314](.\Assert\1562075248314.png)

工作完成。

 

![img](http://wiki.jikexueyuan.com/project/git-tutorial/images/resolv-conflix-on-merge.gif)

 

## 小结

 

当 Git 无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

 

用`git log --graph`命令可以看到分支合并图。