# 管理文件

# 删除文件

 

在 Git 中，删除也是一个修改操作，我们实战一下，先添加一个新文件 test.txt 到 Git 并且提交：

 

```
git add test.txt
git commit -m "add test.txt"
```

 ![1562074205207](.\Assert\1562074205207.png)

一般情况下，你通常直接在文件管理器中把没用的文件删了，或者用 delete 按键删了：



 

这个时候，Git 知道你删除了文件，因此，工作区和版本库就不一致了，`git status`命令会立刻告诉你哪些文件被删除了：

 

```
git status
```

![1562074339628](.\Assert\1562074339628.png)

现在你有两个选择，一是确实要从版本库中删除该文件，那就用命令`git rm`删掉，并且`git commit`：

```
git rm test.txt
git commit -m "remove test.txt"

```

 ![1562074359275](.\Assert\1562074359275.png)

现在，文件就从版本库中被删除了。

 

另一种情况是删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：

 

```
git checkout -- test.txt
```

 

git checkout 其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

 

## 小结

 

命令`git rm`用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失最近一次提交后你修改的内容。



# 忽略特殊文件

 

在 安装 Git一节中，我们已经配置了 user.name 和 user.email，实际上，Git 还有很多可配置项。

 

比如，让 Git 显示颜色，会让命令输出看起来更醒目：

 

```
git config --global color.ui true
```

 

这样，Git 会适当地显示不同的颜色，比如`git status`命令：

 

![1562074576797](.\Assert\1562074576797.png)

 

文件名就会标上颜色（虽然它一直都是这样标的）。

 

我们在后面还会介绍如何更好地配置 Git，以便让你的工作更高效。

 

有些时候，你必须把某些文件放到 Git 工作目录中，但又不能提交它们，比如保存了数据库密码的配置文件啦，等等，每次`git status`都会显示 Untracked files ...，有强迫症的童鞋心里肯定不爽。

 

好在 Git 考虑到了大家的感受，这个问题解决起来也很简单，在 Git 工作区的根目录下创建一个特殊的`.gitignore`文件，然后把要忽略的文件名填进去，Git 就会自动忽略这些文件。

 

不需要从头写`.gitignore`文件，GitHub 已经为我们准备了各种配置文件，只需要组合一下就可以使用了。所有配置文件可以直接在线浏览：https://github.com/github/gitignore

 

忽略文件的原则是：

 

- 忽略操作系统自动生成的文件，比如缩略图等；
- 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的.class文件；
- 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。
- 太大的没有办法被(免费)上传到远程仓库的文件;

 

举个例子：

 

假设你在 Windows 下进行 Python 开发，Windows 会自动在有图片的目录下生成隐藏的缩略图文件，如果有自定义目录，目录下就会有`Desktop.ini`文件，因此你需要忽略 Windows 自动生成的垃圾文件：

 

```
# Windows:
Thumbs.db
ehthumbs.db
Desktop.ini
```

 

然后，继续忽略 Python 编译产生的`.pyc`、`.pyo`、`dist`等文件或目录：

 

```
# Python:
*.py[cod]
*.so
*.egg
*.egg-info
dist
build
```

 

加上你自己定义的文件，最终得到一个完整的`.gitignore`文件，内容如下：

 

```
# Windows:
Thumbs.db
ehthumbs.db
Desktop.ini

# Python:
*.py[cod]
*.so
*.egg
*.egg-info
dist
build

# My configurations:
db.ini
deploy_key_rsa
```

 

最后一步就是把`.gitignore`也提交到 Git，就完成了！当然检验`.gitignore`的标准是`git status`命令是不是说 working directory clean。

 

使用 Windows 的童鞋注意了，如果你在资源管理器里新建一个`.gitignore`文件，它会非常弱智地提示你必须输入文件名，但是在文本编辑器里“保存”或者“另存为”就可以把文件保存为`.gitignore`了。

 

## 小结

 

忽略某些文件时，需要编写`.gitignore`；

 

`.gitignore`文件本身要放到版本库里，并且可以对`.gitignore`做版本管理！