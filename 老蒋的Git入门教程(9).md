# 管理修改

 

现在，假定你已经完全掌握了暂存区的概念。下面，我们要讨论的就是，为什么 Git 比其他版本控制系统设计得优秀，因为 Git 跟踪并管理的是修改，而非文件。

 

你会问，什么是修改？比如你新增了一行，这就是一个修改，删除了一行，也是一个修改，更改了某些字符，也是一个修改，删了一些又加了一些，也是一个修改，甚至创建一个新文件，也算一个修改。

 

为什么说 Git 管理的是修改，而不是文件呢？我们还是做实验。第一步，对 readme.txt 做一个修改，比如加一行内容：

![1562071399943](.\Assert\1562071399943.png)

然后，添加：

 

```
git add readme.txt
git status
```

 

然后，再修改 readme.txt：

 ![1562072673875](.\Assert\1562072673875.png)

提交：

 

```
git commit -m "git tracks changes"
```

 

提交后，再看看状态：

 

```
git status
```

 ![1562072714546](.\Assert\1562072714546.png)

咦，怎么第二次的修改没有被提交？

 

别激动，我们回顾一下操作过程：

 

第一次修改 -> `git add` -> 第二次修改 -> `git commit`

 

你看，我们前面讲了，Git 管理的是修改，当你用`git add`命令后，在工作区的第一次修改被放入暂存区，准备提交，但是，在工作区的第二次修改并没有放入暂存区，所以，`git commit`只负责把暂存区的修改提交了，也就是第一次的修改被提交了，第二次的修改不会被提交。

 

提交后，用`git diff HEAD -- readme.txt`命令可以查看工作区和版本库里面最新版本的区别：

 

```
git diff HEAD -- readme.txt 
```

 ![1562072845768](C:\Users\Charl\AppData\Roaming\Typora\typora-user-images\1562072845768.png)

可见，第二次修改确实没有被提交。

那怎么提交第二次修改呢？你可以继续`git add`再`git commit`，也可以别着急提交第一次修改，先`git add`第二次修改，再`git commit`，就相当于把两次修改合并后一块提交了：

 

第一次修改 -> `git add` -> 第二次修改 -> `git add` -> `git commit`

 

好，现在，把第二次修改提交了，然后开始小结。

 

## 小结

 

现在，你又理解了 Git 是如何跟踪修改的，每次修改，如果不 add 到暂存区，那就不会加入到 commit 中。



# 撤销修改

 

自然，你是不会犯错的。不过现在是凌晨两点，你正在赶一份工作报告，你在 readme.txt 中添加了一行：

![1562072896774](.\Assert\1562072896774.png)

在你准备提交前，一杯咖啡起了作用，你猛然发现了“stupid boss”可能会让你丢掉这个月的奖金！

 

既然错误发现得很及时，就可以很容易地纠正它。你可以删掉最后一行，手动把文件恢复到上一个版本的状态。如果用`git status`查看一下：

 

```
git status
```

 ![1562072933637](.\Assert\1562072933637.png)

你可以发现，Git 会告诉你，`git checkout -- file`可以丢弃工作区的修改：

 

```
git checkout -- readme.txt
```

 

命令`git checkout -- readme.txt`意思就是，把 readme.txt 文件在工作区的修改全部撤销，这里有两种情况：

 

- 一种是 readme.txt 自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
- 一种是 readme.txt 已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

 

总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态。

 

现在，看看 readme.txt 的文件内容：

 

 ![1562072976429](.\Assert\1562072976429.png)

文件内容果然复原了。

 

`git checkout -- file`命令中的--很重要，没有--，就变成了“创建一个新分支”的命令，我们在后面的分支管理中会再次遇到`git checkout`命令。

 

![img](http://wiki.jikexueyuan.com/project/git-tutorial/images/discard-changes-of-working-dir.gif)

 

现在假定是凌晨 3 点，你不但写了一些胡话，还`git add`到暂存区了：

 ![1562073020796](.\Assert\1562073020796.png)



 ![1562073036514](.\Assert\1562073036514.png)

庆幸的是，在 commit 之前，你发现了这个问题。用`git status`查看一下，修改只是添加到了暂存区，还没有提交：

 

```
git status
```

 ![1562073063678](.\Assert\1562073063678.png)

Git 同样告诉我们，用命令`git reset HEAD file`可以把暂存区的修改撤销掉（unstage），重新放回工作区：

 

```
git reset HEAD readme.txt
```

 

git reset 命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用 HEAD 时，表示最新的版本。

 ![1562073094842](.\Assert\1562073094842.png)

再用`git status`查看一下，现在暂存区是干净的，工作区有修改：

 

```
git status
```

 ![1562073123810](.\Assert\1562073123810.png)

还记得如何丢弃工作区的修改吗？

 

```
git checkout -- readme.txt

git status
```

 

整个世界终于清静了！

 

现在，假设你不但改错了东西，还从暂存区提交到了版本库，怎么办呢？还记得[版本回退](http://wiki.jikexueyuan.com/project/git-tutorial/version-back.html)一节吗？可以回退到上一个版本。不过，这是有条件的，就是你还没有把自己的本地版本库推送到远程。还记得 Git 是分布式版本控制系统吗？我们后面会讲到远程版本库，一旦你把“stupid boss”提交推送到远程版本库，你就真的惨了……

 

## 小结

 

又到了小结时间。

 

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。

 

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD file`，就回到了场景 1，第二步按场景 1 操作。

 

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考[版本回退](http://wiki.jikexueyuan.com/project/git-tutorial/version-back.html)一节，不过前提是没有推送到远程库。