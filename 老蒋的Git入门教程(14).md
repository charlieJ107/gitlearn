# 标签管理

## 一、创建标签

发布一个版本时，我们通常先在版本库中打一个标签，这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。

 

Git 的标签虽然是版本库的快照，但其实它就是指向某个 commit 的指针（跟分支很像对不对？但是分支可以移动，标签不能移动），所以，创建和删除标签都是瞬间完成的。

 

在 Git 中打标签非常简单，首先，切换到需要打标签的分支上：

 

```
$ git branch
* dev
  master
$ git checkout master
Switched to branch 'master'
```

 

然后，敲命令`git tag <name>`就可以打一个新标签：

 

```
$ git tag v1.0
```

 

可以用命令`git tag`查看所有标签：

 

```
$ git tag
v1.0
```

 

默认标签是打在最新提交的 commit 上的。有时候，如果忘了打标签，比如，现在已经是周五了，但应该在周一打的标签没有打，怎么办？

 

方法是找到历史提交的`commit id`，然后打上就可以了：

 

```
$ git log --pretty=oneline --abbrev-commit
6a5819e merged bug fix 101
cc17032 fix bug 101
7825a50 merge with no-ff
6224937 add merge
59bc1cb conflict fixed
400b400 & simple
75a857c AND simple
fec145a branch test
d17efd8 remove test.txt
...
```

 

比方说要对`add merge`这次提交打标签，它对应的`commit id`是`6224937`，敲入命令：

 

```
$ git tag v0.9 6224937
```

 

再用命令`git tag`查看标签：

 

```
$ git tag
v0.9
v1.0
```

 

注意，标签不是按时间顺序列出，而是按字母排序的。可以用`git show <tagname>`查看标签信息：

 

```
$ git show v0.9
commit 622493706ab447b6bb37e4e2a2f276a20fed2ab4
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Thu Aug 22 11:22:08 2013 +0800

    add merge
...
```

 

可以看到，v0.9 确实打在`add merge 这次提交上。

 

还可以创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：

 

```
$ git tag -a v0.1 -m "version 0.1 released" 3628164
```

 

用命令`git show <tagname>`可以看到说明文字：

 

```
$ git show v0.1
tag v0.1
Tagger: Michael Liao <askxuefeng@gmail.com>
Date:   Mon Aug 26 07:28:11 2013 +0800

version 0.1 released

commit 3628164fb26d48395383f8f31179f24e0882e1e0
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Tue Aug 20 15:11:49 2013 +0800

    append GPL
```

 

还可以通过`-s`用私钥签名一个标签：

 

```
$ git tag -s v0.2 -m "signed version 0.2 released" fec145a
```

 

签名采用 PGP 签名，因此，必须首先安装 gpg（GnuPG），如果没有找到 gpg，或者没有 gpg 密钥对，就会报错：

 

```
gpg: signing failed: secret key not available
error: gpg failed to sign the data
error: unable to sign the tag
```

 

如果报错，请参考 GnuPG 帮助文档配置 Key。

 

用命令`git show <tagname>`可以看到 PGP 签名信息：

 

```
$ git show v0.2
tag v0.2
Tagger: Michael Liao <askxuefeng@gmail.com>
Date:   Mon Aug 26 07:28:33 2013 +0800

signed version 0.2 released
-----BEGIN PGP SIGNATURE-----
Version: GnuPG v1.4.12 (Darwin)

iQEcBAABAgAGBQJSGpMhAAoJEPUxHyDAhBpT4QQIAKeHfR3bo...
-----END PGP SIGNATURE-----

commit fec145accd63cdc9ed95a2f557ea0658a2a6537f
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Thu Aug 22 10:37:30 2013 +0800

    branch test
```

 

用 PGP 签名的标签是不可伪造的，因为可以验证 PGP 签名。验证签名的方法比较复杂，这里就不介绍了。

 

## 小结

 

- 命令`git tag <name>`用于新建一个标签，默认为 HEAD，也可以指定一个`commit id`；
- `git tag -a <tagname> -m "blablabla..."`可以指定标签信息；
- `git tag -s <tagname> -m "blablabla..."`可以用 PGP 签名标签；
- 命令`git tag`可以查看所有标签。

## 二、操作标签

# 操作标签

 

如果标签打错了，也可以删除：

 

```
$ git tag -d v0.1
Deleted tag 'v0.1' (was e078af9)
```

 

因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除。

 

如果要推送某个标签到远程，使用命令`git push origin <tagname>`：

 

```
$ git push origin v1.0
Total 0 (delta 0), reused 0 (delta 0)
To git@github.com:michaelliao/learngit.git
 * [new tag]         v1.0 -> v1.0
```

 

或者，一次性推送全部尚未推送到远程的本地标签：

 

```
$ git push origin --tags
Counting objects: 1, done.
Writing objects: 100% (1/1), 554 bytes, done.
Total 1 (delta 0), reused 0 (delta 0)
To git@github.com:michaelliao/learngit.git
 * [new tag]         v0.2 -> v0.2
 * [new tag]         v0.9 -> v0.9
```

 

如果标签已经推送到远程，要删除远程标签就麻烦一点，先从本地删除：

 

```
$ git tag -d v0.9
Deleted tag 'v0.9' (was 6224937)
```

 

然后，从远程删除。删除命令也是 push，但是格式如下：

 

```
$ git push origin :refs/tags/v0.9
To git@github.com:michaelliao/learngit.git
 - [deleted]         v0.9
```

 

要看看是否真的从远程库删除了标签，可以登陆 GitHub 查看。

 

## 小结

 

- 命令`git push origin <tagname>`可以推送一个本地标签；
- 命令`git push origin --tags`可以推送全部未推送过的本地标签；
- 命令`git tag -d <tagname>`可以删除一个本地标签；
- 命令`git push origin :refs/tags/<tagname>`可以删除一个远程标签。