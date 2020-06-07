Git 是一个开源的分布式版本控制系统，[官方文档](https://git-scm.com/docs)

 **SVN与Git的最主要的区别？**

　　SVN是集中式版本控制系统，版本库是集中放在中央服务器的，而干活的时候，用的都是自己的电脑，所以首先要从中央服务器哪里得到最新的版本，然后干活，干完后，需要把自己做完的活推送到中央服务器。集中式版本控制系统是必须联网才能工作，如果在局域网还可以，带宽够大，速度够快，如果在互联网下，如果网速慢的话，就纳闷了。

　　Git是分布式版本控制系统，那么它就没有中央服务器的，每个人的电脑就是一个完整的版本库，这样，工作的时候就不需要联网了，因为版本都是在自己的电脑上。既然每个人的电脑都有一个完整的版本库，那多个人如何协作呢？比如说自己在电脑上改了文件A，其他人也在电脑上改了文件A，这时，你们两之间只需把各自的修改推送给对方，就可以互相看到对方的修改了。

# 安装配置

## Linux 平台上安装

Git 的工作需要调用 curl，zlib，openssl，expat，libiconv 等库的代码，所以需要先安装这些依赖工具。 

在有 yum 的系统上（比如 Fedora）或者有 apt-get 的系统上（比如 Debian 体系），可以用下面的命令安装： 

各 Linux 系统可以使用其安装包管理工具（apt-get、yum 等）进行安装：

### Debian/Ubuntu

Debian/Ubuntu Git 安装命令为：

```
$ sudo apt-get install git
```

​	老一点的Debian或Ubuntu Linux，要把命令改为`sudo apt-get install git-core`，因为以前有个软件也叫GIT（GNU Interactive Tools），结果Git就只能叫`git-core`了。由于Git名气实在太大，后来就把GNU Interactive Tools改成`gnuit`，`git-core`正式改为`git`。

### Centos/RedHat

如果你使用的系统是 Centos/RedHat 安装命令为：

```
$ yum -y install git-core
```

### 源码安装

我们也可以在官网下载源码包来安装，最新源码包下载地址：https://git-scm.com/download

安装指定系统的依赖包：

```
########## Centos/RedHat ##########
$ yum install curl-devel expat-devel gettext-devel \
  openssl-devel zlib-devel

########## Debian/Ubuntu ##########
$ apt-get install libcurl4-gnutls-dev libexpat1-dev gettext \
  libz-dev libssl-dev
```

解压安装下载的源码包：

```
$ tar -zxf git-1.7.2.2.tar.gz
$ cd git-1.7.2.2
$ make prefix=/usr/local all
$ sudo make prefix=/usr/local install
```

## Windows 平台上安装

​	在 Windows 平台上安装 Git 同样轻松，有个叫做 msysGit 的项目提供了安装包，可以到 GitHub 的页面上下载 exe 安装文件并运行： 

​	安装包下载地址：https://gitforwindows.org/

​	完成安装之后，就可以使用命令行的 git 工具（已经自带了 ssh 客户端）了，另外还有一个图形界面的 Git 项目管理工具。

​	在开始菜单里找到"Git"->"Git Bash"，会弹出 Git 命令窗口，你可以在该窗口进行 Git 操作。

## Mac 平台上安装

 在 Mac 平台上安装 Git 最容易的当属使用图形化的 Git 安装工具，下载地址为：

http://sourceforge.net/projects/git-osx-installer/

## Git 配置

Git 提供了一个叫做 git config 的工具，专门用来配置或读取相应的工作环境变量。

​	这些环境变量，决定了 Git 在各个环节的具体工作方式和行为。这些变量可以存放在以下三个不同的地方： 

-   `/etc/gitconfig` 文件：系统中对所有用户都普遍适用的配置。若使用 `git config` 时用 `--system` 选项，读写的就是这个文件。
-   `~/.gitconfig` 文件：用户目录下的配置文件只适用于该用户。若使用 `git config` 时用 `--global` 选项，读写的就是这个文件。
-   当前项目的 Git 目录中的配置文件（也就是工作目录中的 `.git/config` 文件）：这里的配置仅仅针对当前项目有效。每一个级别的配置都会覆盖上层的相同配置，所以 `.git/config` 里的配置会覆盖 `/etc/gitconfig` 中的同名变量。

 在 Windows 系统上，Git 会找寻用户主目录下的 `.gitconfig` 文件。主目录即 `$HOME` 变量指定的目录，一般都是` C:\Documents and Settings\$USER`。

此外，Git 还会尝试找寻`/etc/gitconfig `文件，只不过看当初 Git 装在什么目录，就以此作为根目录来定位。 

### 用户信息

配置个人的用户名称和电子邮件地址：

```
$ git config --global user.name "GodZihang"
$ git config --global user.email test@GodZihang.com
```

如果用了 **--global** 选项，那么更改的配置文件就是位于你用户主目录下的那个，以后你所有的项目都会默认使用这里配置的用户信息。

如果要在某个特定的项目中使用其他名字或者电邮，只要去掉 --global 选项重新配置即可，新的设定保存在当前项目的 .git/config 文件里。 

### 文本编辑器

设置 Git 默认使用的文本编辑器, 一般可能会是 Vi 或者 Vim。如果你有其他偏好，比如 Emacs 的话，可以重新设置：

```
$ git config --global core.editor emacs
```

### 差异分析工具

 还有一个比较常用的是，在解决合并冲突时使用哪种差异分析工具。比如要改用 vimdiff 的话： 

```
$ git config --global merge.tool vimdiff
```

 Git 可以理解 kdiff3，tkdiff，meld，xxdiff，emerge，vimdiff，gvimdiff，ecmerge，和 opendiff 等合并工具的输出信息。

当然，你也可以指定使用自己开发的工具，具体怎么做可以参阅第七章。 

###  查看配置信息

 要检查已有的配置信息，可以使用 git config --list 命令： 

```
$ git config --list
core.symlinks=false
core.autocrlf=true
core.fscache=true
...
```

 有时候会看到重复的变量名，那就说明它们来自不同的配置文件（比如 /etc/gitconfig 和 ~/.gitconfig），不过最终 Git 实际采用的是最后一个。 

这些配置我们也可以在 **~/.gitconfig** 或 **/etc/gitconfig** 看到，如下所示：

```
vim ~/.gitconfig 
```

显示内容如下所示：

```
[http]
    postBuffer = 2M
[user]
    name = GodZihang
    email = test@GodZihang.com
```

 也可以直接查阅某个环境变量的设定，只要把特定的名字跟在后面即可，像这样： 

```
$ git config user.name
GodZihang
```

# 基本使用

## 创建版本库

​	Git 使用 **git init** 命令来初始化一个 Git 仓库，Git 的很多命令都需要在 Git 的仓库中运行，所以 **git init** 是使用 Git 的第一个命令。

​	在执行完成 **git init** 命令后，Git 仓库会生成一个 .git 目录，该目录包含了资源的所有元数据，其他的项目目录保持不变（不像 SVN 会在每个子目录生成 .svn 目录，Git 只在仓库的根目录生成 .git 目录）。

​	 使用当前目录作为Git仓库，我们只需使它初始化。 

```
git init
```

​	该命令执行完后会在当前目录生成一个 .git 目录。

​	使用我们指定目录作为Git仓库。 

```
git init newrepo
```

​	初始化后，会在 newrepo 目录下会出现一个名为 .git 的目录，所有 Git 需要的数据和资源都存放在这个目录中。

## 基本操作

### 把文件添加到版本库

​	首先这里再明确一下，所有的版本控制系统，其实只能跟踪文本文件的改动，比如TXT文件，网页，所有的程序代码等等，Git也不例外。版本控制系统可以告诉你每次的改动，比如在第5行加了一个单词“Linux”，在第8行删了一个单词“Windows”。而图片、视频这些二进制文件，虽然也能由版本控制系统管理，但没法跟踪文件的变化，只能把二进制文件每次改动串起来，也就是只知道图片从100KB改成了120KB，但到底改了啥，版本控制系统不知道，也没法知道。

​	不幸的是，Microsoft的Word格式是二进制格式，因此，版本控制系统是没法跟踪Word文件的改动的，前面我们举的例子只是为了演示，如果要真正使用版本控制系统，就要以纯文本方式编写文件。

​	因为文本是有编码的，比如中文有常用的GBK编码，日文有Shift_JIS编码，如果没有历史遗留问题，强烈建议使用标准的UTF-8编码，所有语言使用同一种编码，既没有冲突，又被所有平台所支持。

​	另外，避免使用 windows 自带的记事本编辑文本文件，因为微软在保存 UTF-8 编码的文件时，他们自作聪明地在每个文件开头添加了0xefbbbf（十六进制）的字符，这有时候会带来一些不必要的问题。

​	把一个文件放入 git 仓库需要两步

* 第一步，用命令`git add`告诉Git，把文件添加到仓库：

```
$ git add *.c
$ git add README
```

​	以上命令将目录下以 .c 结尾及 README 文件提交到仓库中。

​	执行上面的命令，没有任何显示，这就对了，Unix的哲学是“没有消息就是好消息”，说明添加成功。

​	第二步，用命令`git commit`告诉Git，把文件提交到仓库：

```
$ git commit -m '初始化项目版本'
```

​	在使用`commit`命令时请务必加上`-m`，`-m "xxx"`输入的是本次提交的内容的说明，输入说明是一个良好的编码习惯，另外如果不带上`-m`时，git 会报错并进入一个文本编辑的界面。

> 如果实在不想输入提交说明，可以在commit后加上`-a`

### 工作区与暂存区

#### 版本库（Repository）

工作区有一个隐藏目录`.git`，这个不算工作区，而是Git的版本库。  

Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

![工作区与版本库](http://118.25.85.24/img/git-guidence/git-Repository.jpg)

​	前面讲了我们把文件往Git版本库里添加的时候，是分两步执行的：

* 第一步是用`git add`把文件添加进去，实际上就是把文件修改添加到暂存区；

* 第二步是用`git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。

因为我们创建Git版本库时，Git自动为我们创建了唯一一个`master`分支，所以，现在，`git commit`就是往`master`分支上提交更改。

你可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。

### 查看历史与版本回退

#### git log

​	在使用 Git 提交了若干更新后，我们可以用它显示提交历史。`git log`命令显示从最近到最远的提交日志。

​	`git log`打印出来的信息一般有多行：

```
$ git log
commit a84cbe490ed363e7fac869a226f6321d6a7e16ff
Author: GidZiHang <godzihang@qq.com>
Date:   Fri May 3 15:52:12 2020 +0800

     update LATEX
```

​	如果嫌输出信息太多，看得眼花缭乱的，可以试试加上`--oneline`参数：

```
$ git log --oneline
a84cbe490ed363e7fac869a226f6321d6a7e16ff update LATEX
```

​	git 的`commit id`是一个由SHA1计算出来的非常大的数字，而不是像SVN一样1,2,3...递增的数字，这是因为 git 是分布式的版本控制系统，如果多人同时在一个版本库里工作，使用1,2,3...作为版本号肯定就会发生冲突了。

​	此外，`git log`指令还提供了很多有用的选项：

- `--garph`：查看历史中什么时候出现了分支、合并

- `--reverse`：逆向显示所有日志

- `author=xxx`：查找指定用户的提交日志

- `--since/befor/until/after={xxx}`：查看指定时间条件下的提交，例如

  ```
  $ git log --oneline --before={3.weeks.ago} --after={2010-04-18} --no-merges
  ```

  这个语句表示要查看三周前且在四月十八日之后的所有提交（这里的`no-merges`选项表示隐藏合并提交）

- 

#### git reset

​	首先，Git必须知道当前版本是哪个版本，在Git中，用`HEAD`表示当前版本，也就是最新的提交`a84cbe4...`（注意我的提交ID和你的肯定不一样），上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`。

```
$ git reset HEAD^
```

表示将仓库回退上上一个版本。

​	有时候，我们想要撤销一些已经添加进缓存，但还没有`commit`的文件，此时应该怎么做呢？只要将HEAD指定为当前指针，并标明要撤销的文件名即可，例如

```
$ git reset HEAD *.c
```

表示撤销已经提交到缓冲区的`*.c`文件。

#### 后悔药

​	在使用`reset`回退版本后，我们会发现之前的最新版本在`log`里已经看不到了，如果说这时候你又突然反悔了，肿么办？

​	办法其实还是有的，如果你的命令行窗口运行过`git log`指令，你可以找到被回退的那个版本的版本号，并且直接让HEAD指向它，像这样：

```
$ git reset a84cbe
```

​	版本号不用写全，git 会自动去找能匹配的版本号(但也不要太短)。从这里可以看出，实际上你的<u>HEAD指向哪个版本号，那个版本就是当前的版本</u>。

​	如果命令行已经被关闭，git 还记录了你的每一次命令，你可以用`git reflog`来查看它：

```
$ git reflog
e475afc HEAD@{1}: reset: moving to HEAD^
a84cbe (HEAD -> master) HEAD@{2}: commit message
...
```

​	从输出可知，被回退的版本的 commit id 是`a84cbe`。知道版本号后，使用`reset`指令把指针设置为这个id，即可将仓库又回溯到这个版本。

### 查看改动

#### git status

​	使用`git status`查看上次提交后，是否有提交到暂存区的问价修改

```
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        new file:   "git/Git\344\275\277\347\224\250\346\214\207\345\215\227.md"
...
```

​	如果在这个指令后加上`-s`参数，它会显示简略的信息。

#### git diff

​	执行 `git diff` 来查看执行 `git status` 的结果的详细信息。

​	`git diff` 命令显示已写入缓存与已修改但尚未写入缓存的改动的区别。`git diff` 有两个主要的应用场景。

- 尚未缓存的改动：**git diff** 
- 查看已缓存的改动： **git diff --cached** 
- 查看已缓存的与未缓存的所有改动：**git diff HEAD** 
- 显示摘要而非整个 diff：**git diff --stat**

### 删除文件

#### git rm

​	如果你直接删除仓库中的文件，那么在运行 `git status` 时就会出现 `Changes not staged for commit` 的提示。

​	要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除，然后提交。可以用以下命令完成此项工作

```
$ git rm <file>
```

​	如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项 **-f**

```
$ git rm -f <file>
```

​	如果把文件从暂存区域移除，但仍然希望保留在当前工作目录中，换句话说，仅是从跟踪清单中删除，使用 **--cached** 选项即可

```
$ git rm --cached <file>
```

​	当然，在 git 中的任何操作总是可以复原的，删除也不例外。如果你误删了文件，而此时还没有进行`commit`，你可以用这个指令把文件恢复到最新版本

```
$ git checkout -- test.txt
```

​	如果你已经提交了版本，你也可以通过版本回退的操作找回被删除的文件。

### 远程仓库



#### 

关联远程库

```
$ git remote add origin git@github.com:username/repositoryName.git
```

推送至远程仓库

```
$ git push -u origin master
```

第一次推送时使用`-u`参数，它会把本地的`master`分支和远程的`master`分支关联起来

从远程库克隆

```
$ git clone git@github.com:username/repositoryName.git
```



### 分支管理

创建一个`dev`分支

```
$ git checkout -b dev
```

`-b`参数表示在创建分支的同时切换分支，这条指令相当于下面的两条指令

```
$ git branch dev
$ git checkout dev
```

查看当前分支

```
$ git branch
```

`git branch`命令会列出所有分支，当前分支前面会标一个`*`号。

把`dev`分支的合并到`master`分支上：

```
$ git merge dev
```

合并指定分支到当前分支，所以这里的当前分支应该是`master`

删除`dev`分支

```
$ git branch -d dev
```

对分支的操作可以使用`switch`指令，这比`checkout`更容易理解

创建并切换到新的`dev`分支，可以使用：

```
$ git switch -c dev
```

直接切换到已有的`master`分支，可以使用：

```
$ git switch master
```

合并冲突

Git用`<<<<<<<`，`=======`，`>>>>>>>`标记出不同分支的内容

发生冲突的部分需要手动修改和保存

