# Git

## git常用命令

```shell
# 新增文件的命令
git add file或者git add .

# 提交文件的命令
git commit –m或者git commit –a

# 工作区状况：
git status –s

# 拉取合并远程分支的操作
git fetch/git merge或者git pull

# 查看提交记录命令
git reflog

# 新建分支
git branch 分支名

# 切换分支
git checkout 分支名

# 删除本地分支
git branch -d 分支名

# 强制删除本地分支
git branch -D 分支名

# 删除远程分支
git push origin --delete 分支名

# 查看本地所有分支
git branch

# 查看远程所有分支
git branch -r

# 查看远程和本地所有分支—一般用这个
git branch -a

# 重命名本地分支
git branch -m <oldbranch> <newbranch>
```



## git 概念

### git 和 SVN有什么区别？

| **Git**                                  | **SVN**                              |
| ---------------------------------------- | ------------------------------------ |
| 1. Git是一个分布式的版本控制工具         | 1. SVN 是集中版本控制工具            |
| 2.它属于第3代版本控制工具                | 2.它属于第2代版本控制工具            |
| 3.客户端可以在其本地系统上克隆整个存储库 | 3.版本历史记录存储在服务器端存储库中 |
| 4.即使离线也可以提交                     | 4.只允许在线提交                     |
| 5.Push/pull 操作更快                     | 5.Push/pull 操作较慢                 |
| 6.工程可以用 commit 自动共享             | 6.没有任何东西自动共享               |

Git使用 C 语言编写。 GIT 很快，C 语言通过减少运行时的开销来做到这一点。

Git 是分布式版本控制系统（DVCS）。它可以跟踪文件的更改，并允许你恢复到任何特定版本的更改。

GIT是分布式版本控制系统，其他类似于SVN是集中式版本控制系统。
分布式区别于集中式在于：每个节点的地位都是平等，拥有自己的版本库，在没有网络的情况下，对工作空间内代码的修改可以提交到本地仓库，此时的本地仓库相当于集中式的远程仓库，可以基于本地仓库进行提交、撤销等常规操作，从而方便日常开发

### git 分区

在Git中有四个概念：**「远程仓库、工作区、暂存区、版本库」**。远程仓库就是我们Git的服务器，用于存储已经管理团队的代码。

工作区、暂存区、版本库是我们本地的，例如当我们初始化`git init`后，就会在当前的目录下出现`.git`目录，**「redis目录就是我们的工作区，而.git目录是我们的版本库所有的版本信息都在这里」**。



![img](https://pic2.zhimg.com/v2-60135a3803017f61ba560d1560813991_b.png)

在.git目录下index文件(`.git/index`)，这就是**「暂存区」**，叫做`stage`或者`index`，index和我们的数据库的index类似，所以我们有时候也叫它为**「索引」**。

这四个区域实现的原理图所下所示，使用过Git的对于下面的命令再熟悉不过了。

![img](https://pic2.zhimg.com/v2-2ba5b5e220c73820daadea21c9a1c4e5_b.jpg)

从原理图中可以看出代码可以再不同的level之间转移，也可以跨level之间转移，所有的这些动作都是通过Git的命令去实现。

初始化的时候Git还会自动为我们创建第一个分支`master`，以及指向master的一个指针叫做`HEAD`。

![img](https://pic3.zhimg.com/v2-dfb5ffce50f4ac3656e470b6281cc5ca_b.jpg)



### git中的“staging area”或“index”是什么？

在完成提交之前，可以在称为“staging area”或“index”的中间区域中对其进行格式化和审查。从图中可以看出，每个更改首先在暂存区域中进行验证，我将其称为“stage file”，然后将更改提交到存储库。

![2.png](https://img.php.cn/upload/image/783/240/282/1578101560984261.png)



**在提交前进行代码检查，并在检查失败后拒绝提**

这可以通过与存储库的 pre-commit hook 相关的简单脚本来完成。git 会在提交之前触发 pre-commit hook。你可以在这个脚本中运行其他工具，例如 linters，并对提交到存储库中的更改执行完整性检查。

最后举个例子，你可以参考下面的脚本：

```
#!/bin/shfiles=$(git diff –cached –name-only –diff-filter=ACM | grep ‘.go$’)if [ -z files ]; then  exit 0fiunfmtd=$(gofmt -l $files)if [ -z unfmtd ]; then  exit 0fiecho “Some .go files are not fmt’d”exit 1
```

这段脚本检查是否需要通过标准 Go 源代码格式化工具 gofmt 传递所有即将提交的 .go 文件。如果脚步以非 `0` 状态退出，脚本会有效地阻止提交操作。





### 什么是 Git 中的“裸存储库”？

你应该说明 “工作目录” 和 “裸存储库” 之间的区别。

Git 中的 “裸” 存储库只包含版本控制信息而没有工作文件（没有工作树），并且它不包含特殊的 `.git` 子目录。相反，它直接在主目录本身包含 `.git` 子目录中的所有内容，其中工作目录包括：

　1.一个 `.git` 子目录，其中包含你的仓库所有相关的 Git 修订历史记录。

　2.工作树，或签出的项目文件的副本。





## git 提交

### 提交到远程仓库

```shell
git init  //初始化版本库
git add .  //添加到缓存区中
git commit -m 'first commit' //提交到版本库，并注释
git remote add origin 你的远程库地址  //本地库和远程库关联
git pull origin master(或者分支名)   //push前最好使用pull来拉取下最新资源。避免冲突
git push -u origin master(或者分支名) //第一次推送时候要写
git push origin master(或者分支名)     //如果不是第一次，则直接使用该命令即可推送修改
```



Git中代码从低level到高leve的移动主要依靠以下命令：

- `git add .`：文件添加进暂存区。
- `git commit -m "提交信息"`：文件添加进本地仓库，-m参数改为-am可以直接推向本地仓库。
- `git push`：文件推向远程仓库。



![img](https://pic3.zhimg.com/v2-7d454a2570bf96ad8f770a2975a4e532_b.jpg)

运行`git commit -a`相当于运行`git add`把所有文件加入暂存区，然后再运行`git commit`把文件提交本地仓库。

### git冲突原因和解决冲突

**产生原因：**多个开发者同时使用或者操作git中的同一个文件，最后在依次提交commit和推送push的时候，第一个操作的是可以正常提交的，而之后的开发者想要执行pull和fetch操作的时候，就会报冲突异常conflict。

1. 两个分支中修改了同一个文件（不管什么地方）
2. 两个分支中修改了同一个文件的名称



开发过程中，我们都有自己的特性分支，所以冲突发生的并不多，但也碰到过。诸如公共类的公共方法，我和别人同时修改同一个文件，他提交后我再提交就会报冲突的错误。
发生冲突，在IDE里面一般都是对比本地文件和远程分支的文件，然后把远程分支上文件的内容手工修改到本地文件，然后再提交冲突的文件使其保证与远程分支的文件一致，这样才会消除冲突，然后再提交自己修改的部分。特别要注意下，修改本地冲突文件使其与远程仓库的文件保持一致后，需要提交后才能消除冲突，否则无法继续提交。必要时可与同事交流，消除冲突。



发生冲突，也可以使用命令。

通过git stash命令，把工作区的修改提交到栈区，目的是保存工作区的修改；

通过git pull命令，拉取远程分支上的代码并合并到本地分支，目的是消除冲突；

通过git stash pop命令，把保存在栈区的修改部分合并到最新的工作空间中；



### 提交对象包含什么？

Commit 对象包含以下组件，你应该提到以下这三点：

　●　一组文件，表示给定时间点的项目状态

　●　引用父提交对象

　●　SHAI 名称，一个40个字符的字符串，提交对象的唯一标识。



### 怎样将 N 次提交压缩成一次提交？

将N个提交压缩到单个提交中有两种方式：

　●　如果要从头开始编写新的提交消息，请使用以下命令：

```
git reset –soft HEAD~N &&git commit
```

　●　如果你想在新的提交消息中串联现有的提交消息，那么需要提取这些消息并将它们传给 git commit，可以这样：

```
git reset –soft HEAD~N &&git commit –edit -m"$(git log –format=%B –reverse .HEAD@{N})"
```







### 如果我想修改提交的历史信息，应该用什么命令？

如果修改最近一次提交的历史记录，就可以用git commit –amend命令；vim编辑的方式；
如果修改之前提交的历史记录，就需要按照下面的步骤：
第一步：首先查看前三次的提交历史记录：

```
$ git log -3
commit a762fcafecbd92bbde088054644e1b0586589c4b (HEAD -> slave)
Author: 18073638 <18073638@cnsuning.com>
Date:   Sat Mar 30 10:58:44 2019 +0800
four commit
commit eedbc93d58780f63dd47f8388f8217892096e89a
Author: 18073638 <18073638@cnsuning.com>
Date:   Thu Mar 28 17:19:52 2019 +0800
third commit third commit
commit 05396135eba85140602107e01e5c211d74f6c739
Author: 18073638 <18073638@cnsuning.com>
Date:   Thu Mar 28 16:56:19 2019 +0800
second commit注意：这里我们想把053961的committer对象信息修改为“second commit second commit”.
```

第二步：执行命令git rebase –i HEAD~3，会把前3次的提交记录按照倒叙列出来；

```
# Rebase c8d7ad7..a762fca onto c8d7ad7 (3 commands)

#

# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

这里把第一行的‘pick’修改为‘edit’，然后esc + :wq退出vim编辑器；

```
$ git rebase -i HEAD~3
Stopped at 0539613...  second commit
You can amend the commit now, with

  git commit --amend

Once you are satisfied with your changes, run

  git rebase --continue第三步：根据提示，执行git commit –amend命令，进入vim编辑器并修改提交信息。
```

第三步：根据提示，执行git commit –amend命令，进入vim编辑器并修改提交信息。

```
$ git commit --amend
[detached HEAD 20fe643] second commit second commit
 Date: Thu Mar 28 16:56:19 2019 +0800
 1 file changed, 1 insertion(+)
```

第四步：然后执行git rebase –continue命令

```shell
$ git rebase --continue
Successfully rebased and updated refs/heads/slave.
```

查看修改结果

    $ git log -3
    commit 9024049ef990e79fa61295d5c2b64d70017cf412 (HEAD -> slave)
    Author: 18073638 <18073638@cnsuning.com>
    Date:   Sat Mar 30 10:58:44 2019 +0800
    four commit
    commit 79cb4e26dd300591e6352d0488802f43b65c8ba2
    Author: 18073638 <18073638@cnsuning.com>
    Date:   Thu Mar 28 17:19:52 2019 +0800
    third commit third commit
    commit 20fe643cbf80cdcc649d732065e8ebf4caf773c7
    Author: 18073638 <18073638@cnsuning.com>
    Date:   Thu Mar 28 16:56:19 2019 +0800
    second commit second commit

修改成功。



### 如何找到特定提交中已更改的文件列表？

对于这个问题，不能仅仅是提供命令，还要解释这个命令究竟做了些什么。

要获取特定提交中已更改的列表文件，请使用以下命令：

**git diff-tree -r {hash}**

给定提交哈希，这将列出在该提交中更改或添加的所有文件。 `-r` 标志使命令列出单个文件，而不是仅将它们折叠到根目录名称中。

你还可以包括下面提到的内容，虽然它是可选的，但有助于给面试官留下深刻印象。

输出还将包含一些额外信息，可以通过包含两个标志把它们轻松的屏蔽掉：

**git diff-tree –no-commit-id –name-only -r {hash}**

这里 `-no-commit-id` 将禁止提交哈希值出现在输出中，而 `-name-only` 只会打印文件名而不是它们的路径。



### 使用过git cherry-pick，有什么作用？

命令 `git cherry-pick` 可以把 branch A 的 commit 复制到 branch B 上。
在 branch B上进行命令操作：

复制单个提交：`git cherry-pick commitId`
复制多个提交：`git cherry-pick commitId1…commitId3`
注意：复制多个提交的命令不包含commitId1.



## git 回退

### **代码回退**

那么从高level向低level移动代码的命令如下：

- `git pull`：从远程仓库拉取代码到本地。
- `git reset --files`：用本地仓库覆盖暂存区中修改，也就是覆盖最后一次git add的内容。
- `git checkout --files`：把文件从暂存区复制到工作区，用于放弃本地的修改。
- `git checkout HEAD --files`：回退最后一次的提交内容。

![img](https://pic3.zhimg.com/v2-52d2e66e643e3cf7feeb5fa7bed2f9fe_b.jpg)

下面我用自己本地与github的操作测试上面的命令，加深对上面的命令的理解和使用，当我在本地新建一个github仓库中没有的文件：

![img](https://pic3.zhimg.com/v2-c2baac0e4085417908074db22e78cc62_b.jpg)

可以看到文件的显示`Untracked files：`未被追踪的文件，**「表示该文件未被git追踪管理」**。

新添加的文件可以通过**「git add添加到在暂存区」**，**「这样文件就能够被git进行追踪」**，此时再使用`git status`查看文件时，就可以看到两个文件已经是以new file的形式进行显示：

![img](https://pic2.zhimg.com/v2-e8c1b37811e6437fec401f3875595e95_b.jpg)



### **版本回退**

若是你想撤销提交到暂存区的内容，使用git reset，可以撤销向暂存区新添加的文件：

![img](https://pic2.zhimg.com/v2-ea9a19652afa2e4f15c69dd99d00db91_b.jpg)

也可以在使用命令：`git reset --hard HEAD^`，表示回退上一个版本，**「在Git中HEAD表示当前版本，HEAD^表示上一个版本」**，若是有多个版本，这样表示就不方便了，可以使用`HEAD~10`，表示版本的次数。

在Git每一个commit都会有自己的commit的ID，可以通过`git log`进行查看：



![img](https://pic2.zhimg.com/v2-092d7604be6242c326b8f374de94322d_b.jpg)

`commit`的本质就是：**「每次Git都会用暂存区的文件创建一个新的提交，把当前的分支指向新的提交节点，这样就完成了一次新的提交」**：



![img](https://pic2.zhimg.com/v2-56bc64049856cd5746561aef85505d8d_b.jpg)

若是`HEAD`指针指向的是`bran`分支，那么新的节点就会成为`jh509`的子节点，并且形成新的分支：

![img](https://pic2.zhimg.com/v2-98d8b69a3f602ed00405e6f8b60da061_b.jpg)

也就可以使用`git log --pretty=oneline`：直接输出commit的ID，信息比较简短，然后直接指定ID的回退：

```text
$ git reset --hard  5567a
```

当你再次检查你的代码的时候就会回到了id为5567a版本，在Git的版本回退原理中，Git的内部有一个指当当前版本的HEAD指针，只要从当前版本指回去就行了，所以Git版本的回退是特别快的，只需要移动指针，实现的原理图如下所示：

![img](https://pic4.zhimg.com/v2-d56bdeb42747fbc15f4eeed864642e5b_b.gif)

**撤销修改**

丢弃工作区的修改使用：`git checkout -- file`命令，这条命令中的--files是不能漏的，若是只是`git checkout`就表示切换另一条分支的命令了。

在我的本地我直接修改：README.md文件，然后使用git status进行查看，他表示文件处于modified状态：

![img](https://pic3.zhimg.com/v2-3f0202b08603514e728c6e33eb20ec96_b.jpg)

此时的README.md文件是还没有被添加进暂存区的，可以直接使用以下命令，撤销掉工作区的修改：

```text
git checkout -- README.md
```

若是已经添加到暂存区了，就是用以下的命令进行回撤：

```text
git reset HEAD README.md
```

上面也演示了`git reset`命令，它既可以回退版本，又可以把暂存区的修改回退到工作区。当我们用HEAD时，表示最新的版本。

当你提交了修改后，可以使用`git diff`查看两次提交之间的变动，它的本质就是**「任意比较两个仓库之间的差异」**：

![img](https://pic3.zhimg.com/v2-fe22f7502674dcefc2490811bac6cec6_b.jpg)

## git 分支

### **分支管理**

Git中比较最重要的一点就是分支的概念，有了分支就有了合并和衍合的操作，**「合并」**和**「衍合」**能够**「有效的对代码版本的管理」**。

Git的初始化中有一条默认的主分支叫做`master`，每一次的提交都会串成一条时间线，这就是一条分支，当前分支由`HEAD`指针指向：

![img](https://pic2.zhimg.com/v2-97435fd76d4f0e088418905c07ee57d5_b.gif)

当每次发生代码提交的时候，当前指向就会向前形成一个新的版本，假如再创建一个新的分支bran，并且当前的提交指向新的分支，这样新的分支随着时间的推移就会形成许多版本：

![img](https://pic4.zhimg.com/v2-1fa112dd234bf38af795102b04cb8183_b.jpg)

当新分支开发完后，提交仓库，并合并到主干master，最后删除bran分支，这样就完成了一次个人的开发：



![img](https://pic1.zhimg.com/v2-6dfa3b0db0406db580b9869dc6b5d294_b.jpg)

所以，假如主分支上只建立一条分支的话，分支的合并是非常快速的，只需要移动master分支到当前提交，然后将HEAD指针指向master，最后删除bran分支就完成了。

但是，事实上并不是这样的，在一个多人协作的开发团队中，往往每个人都会建立自己的分支，有自己的提交，最后合并到主干，当自己提交的时候，远程仓库代码就会存在自己本地仓库并未有的代码，这样就会导致push失败。

例如：程序员Tom和Jerry同时迁出代码，他们的初始代码分支都如下图所示：



![img](https://pic4.zhimg.com/v2-8da8eda06cf5af7f6d7a07ae47c10d5f_b.jpg)

当Tom开发自己的业务模块，提交代码并且合并到主干后，远程主干分支如下图所示：



![img](https://pic1.zhimg.com/v2-56aded61773e239bf11f4326145a9f44_b.jpg)

**「远程仓库master已经不再指向gs234，而是新生成了一个版本dfd453，作为当前指向的版本」**。

与此同时，Jerry的本地也同时开发完自己的模块后，分支如下图所示：



![img](https://pic2.zhimg.com/v2-9272ba481dfa50b453944cc7cabb5af1_b.jpg)

在Jerry的本地环境中，他的**「本地仓库master还是指向gs234」**，Jerry在自己新建立的一个分支`bran`中进行开发，开发完后合并分支，最后`master`就会指向`ed489`。

当Jerry再次提交代码，Git就会检查远程仓库与Jerry的本地仓库，进行对比后，发现远程仓库存在Jerry的本地仓库不存在的代码，就需要Jerry将远程仓库执行`git pull`后，自行解决冲突。

上面说了分支基本原理，已经管理分支出现的问题，下面我们就来一步一步的深入操作分支的基本命令。

### **新建分支**

Git新建一个分支的命令为：`git branch <分支名字>`，新建立后分之后，切换分支的命令为：`git checkout <分支名字>`。

新建分支的实质：**「就是新建立一个引用，指向当前提交，master就好比一个引用」**；切换分支的实质：就是将`HEAD`由指向原来的引用，重新指向要切换的分支的引用上：

![img](https://pic3.zhimg.com/v2-a76627a106ee2a4faa15354dc9762036_b.jpg)

当然上面创建分支并且合并分支的两条命令可以合并成一条命令：`git checkout -b <分支名字>`

当切换分之后，每次commit提交代码时HEAD指针就会跟随着新的bran分支移动，形成bran分支上的每一个版本：

![img](https://pic4.zhimg.com/v2-96999b450c9b98d1756a00d8052cc667_b.jpg)

假如，在新的bran分支上开发到某一个版本，再次切换回master分支进行开发就会形成分叉：

![img](https://pic2.zhimg.com/v2-1c5331fb5b9d4e4d3a6a2aaf3b2622b1_b.jpg)

### **查看分支**

当分支创建好了，你可以通过：`git branch`，来查看自己本地的分支情况：



![img](https://pic3.zhimg.com/v2-e61fd20e83c800a39d0a80e597f6c84e_b.png)

分支前面带有*号的表示当前的分支，查看分之后，你就可以很清楚的知道自己要checkout哪条分支了。

### **合并分支**

开发玩自己模块后，后面就会在自己本地进行合并分支，合并分支的命令：`git merge <分支名字>`，它表示**「合并指定的分支到当前分支」**，比如：当前分支为master，执行：`git merge bran`，表示合并bran分支到当前master分支上。

分支合并也会有失败的情况，当你的两条分支都修改的相同的文件，这时候Git就无法判断你要保留哪一个修改，就会出现merge冲突。

例如：我先在`master`分支修改README.md文件，然后提交本地仓库：

![img](https://pic1.zhimg.com/v2-85a2f5a768a0c72e8e3d3a7404e30164_b.jpg)

然后切换回分支dev，再次修改README.md文件，再次提交

![img](https://pic3.zhimg.com/v2-b5eaa864dabcc5ec7e1084daa5a87c46_b.jpg)

最后进行合并分支，此时在你两次修改的README.md文件中就会出现两次修改的冲突代码：

![img](https://pic3.zhimg.com/v2-bcf10be2a5af5c42ac03d6d72073b3f6_b.jpg)

因为你两次修改同一文件的操作，合并后Git并不知道你要保留哪一次的操作，所以它就会将这个决定交给你自己决定，它只告诉你文件中哪里的代码冲突了，具体怎么改就由你自己去弄。

![img](https://pic3.zhimg.com/v2-cdc68291bf3452176ac3710edaf5595e_b.jpg)

### **删除分支**

最后是删除自己新建的分支，通过：`git branch -d <分支名字>`，进行删除分支，假如分支删除不了，可以通过：`git branch -D <分支名字>`，强制删除分支：



![img](https://pic4.zhimg.com/v2-55d1a9c7d35693b2787b6ee4184c3e23_b.jpg)

Git 中删除分支的实质：dev 只是一个分支的引用，所以删除分支也就是删除这个引用，并不会删除任何conmit，所以删除操作也是非常高效的。

假如一条分支commit的引用被删除，那么这条分支的就没有任何引用指向，这样就会找不到这条分支，最后就会被Git回收机制回收。



### git rebase和git merge

分支管理中有**「合并」**和**「衍合」**操作，合并操作在在第二年篇的分支章节已经详细讲解过了，就来讲解一下衍合操作：`git rebase`操作。

假如有两位开发人员Tom和Jerry，Tom和Jerry都把远程的`master`分支签出到本地，此时当前的Tom和Jerry本地都是只有一条`master`分支：



![img](https://pic2.zhimg.com/v2-b3a1420217cc8d9c74d5edcbb455c249_b.jpg)

此时Tom开发人员，创建一条新的分支`branch`，并且将新的分支`branch`推向远程仓库（`git push origin branch`）：

![img](https://pic2.zhimg.com/v2-51d1d62e0697dda58f2e60d9233da241_b.png)

此时Tom本地仓库和远程仓库的分支保持一致，分支如下图所示：

![img](https://pic2.zhimg.com/v2-ba56eab4e47979d1ac5e78e12306c9c9_b.jpg)

Tom在自己的分支`branch`上开发自己的模块，假如开发期间Tom进行了**「两次」**的提交，最后Tom本地的分支形成如下所示：

![img](https://pic4.zhimg.com/v2-0179396bd40ee4bc7efaee3bdc03f593_b.jpg)

也可以通过`git log`查看两次提交的记录：



![img](https://pic2.zhimg.com/v2-a6de56fd07040500fd2e4eb9f8f56da5_b.jpg)

若是，此时Tom开发人员准备把自己的branch的分支推向远程仓库，但是，Jerry在此之前已经在master提交了自己的开发代码，所以master分支相比之前记录，已经向前推进了一个版本。

所以此时Tom想提交，必须先更新一下自己的本地分支：

![img](https://pic3.zhimg.com/v2-bdc3c2c45bcaf6d0d329dd2031f73dfe_b.png)

Tom中通过`git log`命令可以查看到Jerry的提交记录情况，说明此时分支已经与远程仓库同步：

![img](https://pic4.zhimg.com/v2-a817ebd3d8e7f354b4b6b826be07392f_b.jpg)

此时Tom更新分之后，本地的分支情况如下所致，相比原来master指向c1，现在向前推进了一个版本指向c4：



![img](https://pic4.zhimg.com/v2-f7315299cd334728b7f2d95993723b7f_b.jpg)

此时，Tom必须重新合并分支进行提交，把branch的代码合并到master分支上，现在Tom可以有两种方式：

1. 直接`git merge`。
2. 先`git rebase`，然后`git merge`。

（1）若是使用第一种方法直接在master分支上执行`git merge`命令，**「Git会把master分支上最新的提交c4的内容和branch分支上最新的提交c3 合并后生成一个新的提交点c5」**：



![img](https://pic1.zhimg.com/v2-56a9e05f96cd60f80c009777f9c845c4_b.jpg)

上面实在没有冲突的前提下，若是有冲突，则解决冲突：



![img](https://pic4.zhimg.com/v2-90715119dda14fa1cbf5c0ecdeedfb2f_b.jpg)

![img](https://pic4.zhimg.com/v2-ecd4191cb664236ca99f82d438a3dd43_b.jpg)

此时就完成了master和branch分支的合并。

（2）若是使用第二个方法，先master也需要拉取到最新版本，然后是切换到branch分支，这个也就是要切换到`rebase`的分支，这里指的是branch分支。

在branch分支执行`git rebase master`，表示chanch上新提交的c4节点会在master上的最新提交点后重新设立起点重新执行，若是有冲突则解决冲突，没有冲突执行`git add`，最后执行`git rebase --continue`。

![img](https://pic1.zhimg.com/v2-166b0497ddddb2c1810f451b60600ff8_b.jpg)

通过`git log`查看你可以发现，之前branch分支上进行【**「删除HELLO ABC」**】的操作`commit hash`已经发生改变，最后切换到master分支，执行`git merge master`。

通过测试可以发现，原来branch分支上重新commit的节点c4在rebase到master分之后hash发生了改变，并且提交的内容被复制保留，从而使得master分支整个呈现线性的commit记录，而不是直接git merge后的分叉记录。

在master分支进行branch分支rebase的原理图如下：

![img](https://pic1.zhimg.com/v2-102ea79790d4b586a83e8dbbb62100cc_b.gif)

注意：**「一般不是在branch进行rebase主分支master的提交，因为会导致master的新提交在本地丢失，这样有可能会导致本地仓库与远程仓库发生冲突，从而无法push操作」**。

**「总结」**：

**「git merge会将两个分支的最新提交点进行一次合并，形成一个新的提交点，最终形成树状的提交记录」**，但是有些人并不是喜欢merge，觉得merge之后出现的分叉会难以管理，那么可以选择rebase操作来替代merge。

**「git rebase操作是将要rebase的分支最新提交点作为新的基础点，将当前执行git rebase master的分支的新commit点重新生成commit hash值，rebase完后再次切换到另一条分支进行合并，就可以保证线性的commit的记录」**。

**「最后只选择merge还是rebase取决于个人和时机情况，假如你想提交记录呈现线性整洁那么选择rebase，否则选择merge，实际情况也有可能是这样的，每个人本地开发，可能会提交非常的多次，有些提交可能是修一些简单的bug，那么最后的提交只想做一次完整、正确的提交，那么也可以使用rebase。」**





### 描述一下你所使用的分支策略？

这个问题被要求用Git来测试你的分支经验，告诉他们你在以前的工作中如何使用分支以及它的用途是什么，你可以参考以下提到的要点：

　●　功能分支（Feature branching）

要素分支模型将特定要素的所有更改保留在分支内。当通过自动化测试对功能进行全面测试和验证时，该分支将合并到主服务器中。

　●　任务分支（Task branching）

在此模型中，每个任务都在其自己的分支上实现，任务键包含在分支名称中。很容易看出哪个代码实现了哪个任务，只需在分支名称中查找任务键。

　●　发布分支（Release branching）

一旦开发分支获得了足够的发布功能，你就可以克隆该分支来形成发布分支。创建该分支将会启动下一个发布周期，所以在此之后不能再添加任何新功能，只有错误修复，文档生成和其他面向发布的任务应该包含在此分支中。一旦准备好发布，该版本将合并到主服务器并标记版本号。此外，它还应该再将自发布以来已经取得的进展合并回开发分支。

最后告诉他们分支策略因团队而异，所以我知道基本的分支操作，如删除、合并、检查分支等。





### 如果分支是否已合并为master，你可以通过什么手段知道？

要知道某个分支是否已合并为master，你可以使用以下命令：

`git branch –merged` 它列出了已合并到当前分支的分支。

`git branch –no-merged` 它列出了尚未合并的分支。



## git 远程

### **查看远程**

在多人协作的团队下，你可能要随时查看远程仓库的情况，可以通过：git remote，进行查看，加上-v参数可以查看远程仓库的详细情况。

```text
git remote
git remote -v
```

### **推送分支**

![img](https://pic3.zhimg.com/v2-01dc8b71690bdf305870b0baf0ecf0a2_b.jpg)

分支的推送到远程上一节已经提过，使用git push命令就可以进行分支的推送，命令后面加上分支的命令，表示具体推送哪条分支：

```text
git push origin master // 将本地master分支推送到远程库
```

### **拉取分支**

分支的拉取使用git pull命令，这条命令相当于以下两条命令：

```text
git fetch
git merge
```

但是一般实际工作中，都可能会直接使用git pull命令：

![img](https://pic4.zhimg.com/v2-7c72f7cc053376d2f842e60bad0f65a3_b.jpg)



### git pull 和 git fetch 有什么区别？

`git pull` 命令从中央存储库中提取特定分支的新更改或提交，并更新本地存储库中的目标分支。

`git fetch` 也用于相同的目的，但它的工作方式略有不同。当你执行 `git fetch` 时，它会从所需的分支中提取所有新提交，并将其存储在本地存储库中的新分支中。如果要在目标分支中反映这些更改，必须在 `git fetch` 之后执行`git merge`。只有在对目标分支和获取的分支进行合并后才会更新目标分支。为了方便起见，请记住以下等式：

<center><h5>git pull = git fetch + git merge</h5></center>

简单来说：git fetch branch是把名为branch的远程分支拉取到本地；而git pull branch是在fetch的基础上，把branch分支与当前分支进行merge；因此pull = fetch + merge。



`git fetch`的意思是将远程主机的最新内容拉到本地，用户再检查无误后再决定是否合并到工作本地分支中

`git pull`是将远程主机中的最新内容拉取下来后直接合并，即：git pull = git fetch+git merge，这样可能会产生冲突，需要手动解决。



## git 日志

查看分支的提交历史记录：

* 命令git log –number：表示查看当前分支前number个详细的提交历史记录；
* 命令git log –number –pretty=oneline：在上个命令的基础上进行简化，只显示sha-1码和提交信息；
* 命令git reflog –number: 表示查看所有分支前number个简化的提交历史记录；
* 命令git reflog –number –pretty=oneline：显示简化的信息历史信息；
	如果要查看某文件的提交历史记录，直接在上面命令后面加上文件名即可。
	注意：如果没有number则显示全部提交次数。



Git查看日志前面有提到过可以通过`git log`命令进行查看：

![img](https://pic1.zhimg.com/v2-60699368a4a3f618130a4a39f2477698_b.jpg)

`git log`可以查看你**「提交的时间、提交的作者、以及提交的id」**都可以查到，如果你觉得查询的信息太多，可以加上参数`--pretty=oneline`，只会显示版本号和提交时的备注信息。

如果你想查最近的几条历史记录，可以通过加参数"`-n`"的形式制定查询几条记录，历史记录是**「按照操作的时间」**进行排序的：

![img](https://pic4.zhimg.com/v2-d9c418c32de666a59979ebcbc4b471b7_b.jpg)

还可以通过加参数" `--graph`"，以图形化的形式展示历史记录，方便与查看历史记录与分支的关系：



![img](https://pic3.zhimg.com/v2-cb52f2634aae2010554ee6afec7f13fa_b.jpg)

还可以加参数"`-p`"，可以查看每一个commit操作更改的文件的哪一行，加参数"`-stat`"查看哪些文件改动了，进行简要的统计。更加详细的`git log`参数可以查看命令帮助。

Git查看历史记录的另一个命令是`git reflog`，它可以查看**「所有分支的所有操作记录，包括已经删除的commit记录和reset记录」**。

![img](https://pic1.zhimg.com/v2-458046b940b14d567e23b8af7c7827c4_b.jpg)



## git 标签

Git中使用的标签有两种类型：**「轻量级的（lightweight）和含附注的（annotated）」**。轻量级标签只需在`git tag`后加上标签的名字，就可以添加标签。

标签管理作为开发人员可能很少使用，可以作为了解，**「tag是针对Git中某一时间某一版本打上标签」**，tag的使用命令也是非常的简单。

### **新建标签**

新建一个标签，默认是在`HEAD`新建，可以指定`commit id`新建，具体命令如下所示：

```text
$ git tag <标签名>
$ git tag <标签名> <commit id>
$ git tag -a <标签名> -m "备注"
```

### **删除标签**

删除标签若是标签没有推向远程仓库，直接使用以下命令删除：

```text
$ git tag -d <标签名>
```

若是标签已经推向远程仓库，先删除本地，再删除远程仓库的标签：

```text
$ git tag -d <标签名>
$ git push origin :refs/tags/<标签名>
```

### **查看标签**

git tag的方式是查看所有的标签，git show <标签名>的方式是查看每个特定的标签

```text
$ git tag
$ git show <标签名>
```

### **推送标签到远程**

推送标签也是分两种情况，一种是指定标签的推送，另一种是推送所有标签。

```text
$ git push origin <标签名>
$ git push origin --tags
```





## git 配置

### git config 的功能是什么？

首先说明为什么我们需要 `git config`。

git 使用你的用户名将提交与身份相关联。 `git config` 命令可用来更改你的 git 配置，包括你的用户名。

下面用一个例子来解释。

假设你要提供用户名和电子邮件 ID 用来将提交与身份相关联，以便你可以知道是谁进行了特定提交。为此，我将使用：

**git config –global user.name "Your Name":** 此命令将添加用户名。

**git config –global user.email "Your E-mail Address":** 此命令将添加电子邮件ID。



### 如何忽略文件？

首先利用命令touch .gitignore新建文件

```
$ touch .gitignore
```

然后往文件中添加需要忽略哪些文件夹下的什么类型的文件

```
$ vim .gitignore
$ cat .gitignore
/target/class
.settings
.imp
*.ini
```

注意：忽略/target/class文件夹下所有后缀名为.settings，.imp的文件，忽略所有后缀名为.ini的文件。











## 你使用过git stash命令吗？你一般什么情况下会使用它？

在开发中，若是某一时刻你想把当前的改动临时进行存放起来，可以使用`git stash`命令，它表示将改动的文件存储到一个独立的存储区域，并不会被提交，当再次需要的时候可以随时取出来。

这里要注意的是：**「git stash的是改动的文件，也就是被Git追踪的文件，新添加的文件并没有被Git追踪，所以git stash并不会stash」**。

![img](https://pic3.zhimg.com/v2-a052dfaaaaa8e139690ec44e55b5ffa6_b.jpg)

git stash命令也可以加上save命令后面再加上备注信息，方便查看：

```text
git stash save "备注信息"
```

`git stash`成功后**「本地的工作目录的代码会和本地仓库一样」**，`git stash`后可以通过`git stash list`命令查看之前stash的历史记录，当再次需要将改动的文件取出来时候，可以通过以下命令：

```text
git stash pop
```

`git stash pop`表示**「弹出第一个被stash的记录，并且该stash会从历史记录中删除」**；也可以使用`git stash apply`命令**「弹出stash，但是这条命令stash任然会保存在stash历史记录中」**，你也可以通过：`git stash drop`命令来删除。

![img](https://pic4.zhimg.com/v2-af1bc5689c80f48e09b7accd68b41ccb_b.jpg)



通常情况下，当你一直在处理项目的某一部分时，如果你想要在某个时候切换分支去处理其他事情，事情会处于混乱的状态。问题是，你不想把完成了一半的工作的提交，以便你以后就可以回到当前的工作。解决这个问题的答案是 git stash。

`git stash` 会将你的工作目录，即修改后的跟踪文件和暂存的更改保存在一堆未完成的更改中，你可以随时重新应用这些更改。命令 `git stash` 是把工作区修改的内容存储在栈区。`git stash`命令用于暂时保存没有提交的工作。运行该命令后，所有没有commit的代码，都会暂时从工作区移除，回到上次commit时的状态。

以下几种情况会使用到它：

* 解决冲突文件时，会先执行git stash，然后解决冲突；
* 遇到紧急开发任务但目前任务不能提交时，会先执行git stash，然后进行紧急任务的开发，然后通过git stash pop取出栈区的内容继续开发；
* 切换分支时，当前工作空间内容不能提交时，会先执行git stash再进行分支切换；



它处于`git reset --hard`（完全放弃还修改了一半的代码）与`git commit`（提交代码）命令之间，很类似于“暂停”按钮。

```
# 暂时保存没有提交的工作$ git stashSaved working directory and index state WIP on workbranch: 56cd5d4 Revert "update old files"HEAD is now at 56cd5d4 Revert "update old files"# 列出所有暂时保存的工作$ git stash liststash@{0}: WIP on workbranch: 56cd5d4 Revert "update old files"stash@{1}: WIP on project1: 1dd87ea commit "fix typos and grammar"# 恢复某个暂时保存的工作$ git stash apply stash@{1}# 恢复最近一次stash的文件$ git stash pop# 丢弃最近一次stash的文件$ git stash drop# 删除所有的stash$ git stash clear
```

上面命令会将所有已提交到暂存区，以及没有提交的修改，都进行内部保存，没有将工作区恢复到上一次commit的状态。

使用下面的命令，取回内部保存的变化，它会与当前工作区的代码合并。

```
$ git stash pop
```

这时，如果与当前工作区的代码有冲突，需要手动调整。

`git stash`命令可以运行多次，保存多个未提交的修改。这些修改以“先进后出”的stack结构保存。

`git stash list`命令查看内部保存的多次修改。

```
$ git stash liststash@{0}: WIP on new-feature: 5cedccc Try something crazystash@{1}: WIP on new-feature: 9f44b34 Take a different directionstash@{2}: WIP on new-feature: 5acd291 Begin new feature
```

上面命令假设曾经运行过`git stash`命令三次。

`git stash pop`命令总是取出最近一次的修改，但是可以用`git stash apply`指定取出某一次的修改。

```
$ git stash apply stash@{1}
```

上面命令不会自动删除取出的修改，需要手动删除。

```
$ git stash drop stash@{1}
```

git stash 子命令一览。

```
# 展示目前存在的stash
$ git stash show -p
# 切换回stash
$ git stash pop
# 清除stash
$ git stash clear

git stash drop 命令用于删除隐藏的项目。默认情况下，它将删除最后添加的存储项，如果提供参数的话，它还可以删除特定项。

下面举个例子。

如果要从隐藏项目列表中删除特定的存储项目，可以使用以下命令：
git stash list：它将显示隐藏项目列表，如：

stash@{0}: WIP on master: 049d078 added the index file
stash@{1}: WIP on master: c264051 Revert “added file_size”
stash@{2}: WIP on master: 21d80a5 added number to log

如果要删除名为 stash@{0} 的项目，请使用命令 **git stash drop stash@{0}**
```









## 能说一下git系统中HEAD、工作树和索引之间的区别吗？

HEAD文件包含当前分支的引用（指针）；
工作树是把当前分支检出到工作空间后形成的目录树，一般的开发工作都会基于工作树进行；
索引index文件是对工作树进行代码修改后，通过add命令更新索引文件；GIT系统通过索引index文件生成tree对象；



### GitFlow

### Git主要优点有

分布式存储 , 本地仓库包含了远程仓库的所有内容 . 安全性高 , 远程仓库文件丢失了也不怕
优秀的分支模型 , 创建/合并分支非常的方便
方便快速 , 由于代码本地都有存储 , 所以从远程拉取和分支合并时都非常快捷
当分支过多时 , 如何管理这些分支呢 ? 我们团队采用了Git Flow的模式

### GitFlow的常用分支

**master**：主分支 , 产品的功能全部实现后 , 最终在master分支对外发布

* 该分支为只读唯一分支 , 只能从其他分支(release/hotfix)合并 , 不能在此分支修改
* 另外所有在master分支的推送应该打标签做记录,方便追溯
* 例如release合并到master , 或hotfix合并到master

**develop**：主开发分支 , 基于master分支克隆

* 包含所有要发布到下一个release的代码
* 该分支为只读唯一分支 , 只能从其他分支合并
* feature功能分支完成 , 合并到develop(不推送)
* develop拉取release分支 , 提测
* release/hotfix 分支上线完毕 , 合并到develop并推送

**feature**：功能开发分支 , 基于develop分支克隆 , 主要用于新需求新功能的开发

* 功能开发完毕后合到develop分支(未正式上线之前不推送到远程中央仓库!!!)
* feature分支可同时存在多个 , 用于团队中多个功能同时开发 , 属于临时分支 , 功能完成后可选删除

**release**：测试分支 , 基于feature分支合并到develop之后  , 从develop分支克隆

* 主要用于提交给测试人员进行功能测试 , 测试过程中发现的BUG在本分支进行修复 , 修复完成上线后合并到develop/master分支并推送(完成功能) , 打Tag
* 属于临时分支 , 功能上线后可选删除

**hotfix**：补丁分支 , 基于master分支克隆 , 主要用于对线上的版本进行BUG修复

* 修复完毕后合并到develop/master分支并推送 , 打Ta

* 属于临时分支 , 补丁修复上线后可选删除

* 所有hotfix分支的修改会进入到下一个release

	

### 主要工作流程

1 . 初始化项目为gitflow , 默认创建master分支 , 然后从master拉取第一个develop分支
2 . 从develop拉取feature分支进行编码开发(多个开发人员拉取多个feature同时进行并行开发 , 互不影响)

3 . feature分支完成后 , 合并到develop(不推送 , feature功能完成还未提测 , 推送后会影响其他功能分支的开发)
    合并feature到develop , 可以选择删除当前feature , 也可以不删除 . 但当前feature就不可更改了 , 必须从release分支继续编码修改

4 . 从develop拉取release分支进行提测 , 提测过程中在release分支上修改BUG

5 . release分支上线后 , 合并release分支到develop/master并推送
     合并之后 , 可选删除当前release分支 , 若不删除 , 则当前release不可修改 . 线上有问题也必须从master拉取hotfix分支进行修改

6 . 上线之后若发现线上BUG , 从master拉取hotfix进行BUG修改

7 . hotfix通过测试上线后 , 合并hotfix分支到develop/master并推送
    合并之后 , 可选删除当前hostfix , 若不删除 , 则当前hotfix不可修改 , 若补丁未修复 , 需要从master拉取新的hotfix继续修改

8 . 当进行一个feature时 , 若develop分支有变动 , 如其他开发人员完成功能并上线 , 则需要将完成的功能合并到自己分支上
    即合并develop到当前feature分支
9 . 当进行一个release分支时 , 若develop分支有变动 , 如其他开发人员完成功能并上线 , 则需要将完成的功能合并到自己分支上
    即合并develop到当前release分支 (!!! 因为当前release分支通过测试后会发布到线上 , 如果不合并最新的develop分支 , 就会发生丢代码的情况)




![img](https://img-blog.csdn.net/20180803135443287?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmdiYW96aGVuMTIxMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)









