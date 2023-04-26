    # git是什么



# git结构

![image-20230302130913976](C:\Users\24811\Pictures\md图片\git\image-20230302130913976.png)





# 系列操作

## 本地创建仓库（windows）

**新建一个本地仓库后就完整的进行一次（add->commit）的操作。**

使用当前目录作为Git仓库

`git init`生成`.git`文件夹

使用指定目录作为Git仓库

`git init <newrepo>`在当前目录下生成指定名称的文件夹作为仓库

初始化后，会在 newrepo 目录下会出现一个名为 .git 的目录，所有 Git 需要的数据和资源都存放在这个目录中。

如果当前目录下有几个文件想要纳入版本控制，需要先用 git add 命令告诉 Git 开始对这些文件进行跟踪，然后提交：

```
git add *.C
git add gitstudt.md
git commit -m “提交信息”
```

克隆项目`git clone <repo仓库路径>`

克隆到指定目录`git clone <repo> <directory>`

配置`git config`

```
$ git config --list
credential.helper=osxkeychain
core.repositoryformatversion=0
core.filemode=true
core.bare=false
core.logallrefupdates=true
core.ignorecase=true
core.precomposeunicode=true
```

针对当前仓库配置`git config -e`

正对系统上所有仓库`git config e --global`

设置提交代码时候用户的信息
`git config --global user.name "name"`
`git config --global user.email 241115781@qq.com`
去掉`--global`就只对当前仓库有效

## 基本操作

Git 的工作就是创建和保存你项目的快照及与之后的快照进行对比。

常用指令有6个

```
git clone
git push
git add
git commit
git checkout
git pull
```

![image-20230302133923833](C:\Users\24811\Pictures\md图片\git\image-20230302133923833-16777355652561.png)

**说明**

- workspace：工作区
- staging area：暂存区/缓存区
- local repository：版本库或本地仓库
- remote repository：远程仓库

### 通常最简单的操作

```
git init # 初始化仓库。
git add .# 添加文件到暂存区。
git commit # 将暂存区内容添加到仓库中。
```

### 创建仓库命令

```
git init [<repo>]
git clone
```

### 提交和修改

```
git add			#添加文件到暂存区
git status		#查看仓库当前状态，显示有变更的文件
git diff		#比较文件的不同
git commit		#提交暂存区到本地仓库
git reset		#回退版本
git rm			#将文件从暂存器和工作区删除
git mv			#移动或者重命名工作区文件
```

### 提交日志

```
git log			#查看历史提交记录
git blame <file> #以列表形式查看历史修改记录
```

### 远程操作

```
git remote		#远程仓库操作
git fetch		#从远程获取代码库
git pull		#下载远程代码并合并
git push		#上传远程代码并合并
```

`git pull`(conflict:merger,修改了服务器原来的代码替换成你的代码,这些代码有冲突,是选择你的还是选择原来的,有冲突的时候,会自动修改你写的代码,并保留服务器原来的代码,如果你是要修改服务器的,只要删除自动添加的,再进行2<2>执行下一步步骤就行)

## 分支管理

![image-20230302135324462](C:\Users\24811\Pictures\md图片\git\image-20230302135324462-16777364056742.png)

创建分支命令

`git branch (branchname)`

切换分支命令

`git checkout (brachname)`

当你切换分支的时候，Git 会用该分支的最后提交的快照替换你的工作目录的内容， 所以多个分支不需要多个目录。

> 都存在上一次的快照里面了，就不需要重新再建立新的文件夹来存放分支更新的代码

**合并分支**(不是很懂)

`git merge`

不懂的点：分支里面有ABCDF，主线里面有ABCDE，合并之后主线里面是怎么样的呢，ABCDF？

创建新分支并且切换到新分支下

`git checkout -b <branchname>`

分支管理举例

```
需要新建一个仓库，新建一个readme文件然后add->commit再进行下面的操作，不然的话找不到master的分支

$ git branch
* master
$ git branch testing
$ git branch
* master
  testing
  #多了一个分支
  
  经过尝试后发现从master建立的分支是会有master已经有的文件，相当于是一个独立的副本，包括从分支建立新的分支，新的分支也相当于是旧分支的副本，文件都是继承的
```

![image-20230302144532560](C:\Users\24811\Pictures\md图片\git\image-20230302144532560-16777395353233.png)

遇到了一个问题，就是ls出来的txt文件有的有引号包围，原因：上传文件到Linux系统中时，如果文件名有空格，系统会自动将其用引号包裹起来以保证这个文件名[字符串](https://so.csdn.net/so/search?q=字符串&spm=1001.2101.3001.7020)是一个整体（否则会以空格为间隔将字符串分隔成不同的字符串，显然要避免这个情况）

删除分支

`git branch -d (branchname)`



**新建文件**

```c
新建文件有两种方式：
1，touch+文件名，直接新建一个文件

2，vi+文件名，新建一个文件并进入编辑状态（如果文件已存在，则直接进入编辑状态）

vi其实是linux的一个文本编辑器，所以 vi+文件名 后，其实是进入vi程序了。
```

### 分支合并

一旦某分支有了独立内容，你终究会希望将它合并回到你的主分支。 你可以使用以下命令将任何分支合并到当前分支中去：

`git merge <branchname>`

### 合并冲突





查看提交历史

`git log`

在使用 Git 提交了若干更新之后，又或者克隆了某个项目，想回顾下提交历史，我们可以使用 **git log** 命令查看

出现冒号表示显示不完全，继续按下enter或下滑继续显示，显示完毕后按q退出

```
git log --oneline	#查看历史记录的简洁的版本。
git log --graph 	#查看历史中什么时候出现了分支、合并。以下为相同的命令，开启了拓扑图选项
git log --reverse	#逆向显示所有日志
git log --author=Linus --oneline -5#查找指定用户的提交日志
git log --oneline --before={3.weeks.ago} --after={2010-04-18} --no-merges	#指定日期，可以执行几个选项：--since 和 --before，但是你也可以用 --until 和 --after
#以上后缀都可以叠加时钟，中间用空格隔开
```



`git blame <file>`





## git标签

如果你达到一个重要的阶段，并希望永远记住那个特别的提交快照，你可以使用 git tag 给它打上标签。

比如说，我们想为我们的 runoob 项目发布一个"1.0"版本。 我们可以用 git tag -a v1.0 命令给最新一次提交打上（HEAD）"v1.0"的标签。

-a 选项意为"创建一个带注解的标签"。 不用 -a 选项也可以执行的，但它不会记录这标签是啥时候打的，谁打的，也不会让你添加个标签的注解。 我推荐一直创建带注解的标签。

```
git tag -a v1.0 
```

注意当我们执行 ·`git log --decorate` 时，我们可以看到我们的标签了：`git log --oneline --decorate --graph`

追加标签

`git tag -a v0.9 85fc7e7`

查看标签

`git tag`

指定标签信息命令：

```
git tag -a <tagname> -m "runoob.com标签"
```

PGP签名标签命令：

```
git tag -s <tagname> -m "runoob.com标签"
```


## git回退版本

```
操作思路 ： 
1.使用 git log 或者 git reflog 命令 获取到要回退或者切换的版本id
2.使用 git reset --hard命令回退/切换到某个历史版本；
3.git reset --hard 命令会重置  本地仓库、暂存区和工作区，三者的状态保持一致！
```

```
版本回退/切换的命令：
1.git reset --hard [索引值] : 可切换到任意版本[推荐使用这个方式]
2.git reset --hard HEAD^ ： 只能后退，一个 ^ 表示回退一个版本，两个^ 表示回退两个版本，。。。依次类推
3.git reset --hard HEAD~n ：只能后退，n表示后退n个版本
```
```
版本回退/切换的命令：
1.git reset --hard [索引值] : 可切换到任意版本[推荐使用这个方式]
2.git reset --hard HEAD^ ： 只能后退，一个 ^ 表示回退一个版本，两个^ 表示回退两个版本，。。。依次类推
或者直接git reset --hard HEAD~n，回退n个版本
3.git reset --hard HEAD~n ：只能后退，n表示后退n个版本
```
git reset的一些参数
```
git reset --soft  ：1.仅在本地版本库移动指针。
git reset --mixed : 1.移动本地版本库的指针；2.重置暂存区。（默认的参数）
git reset --hard  : 1.移动本地版本库的指针；2.重置暂存区；3.重置工作区。
```




