# Git
---
## 安装Git
安装完成后，还需要最后一步设置，在命令行输入：
<font face = "Courier New"> `$ git config --global user.name "[Your Name]"` </font>
<font face = "Courier New"> `$ git config --global user.email "[email@example.com]"` </font>

## 创建版本库
在Windows系统下，目录名最好不要出现中文

初始化本地仓库：
<font face = "Courier New"> `$ git init` </font>

#### 把文件添加到版本库
所有的版本控制系统都只能跟踪文本文件的改动

1. 把文件添加到暂存区
<font face = "Courier New"> `$ git add [file-name]` </font>
`git add`命令可反复多次使用，添加多个文件
2. 把暂存区文件提交到当前分支
<font face = "Courier New"> `$ git commit -m [description]` </font>
一次性将暂存区的所有文件都提交到当前分支

## 时光机穿梭
`git status`命令显示仓库当前的状态
`git diff [file-name]`命令可以显示某个文件修改的内容（与本地仓库对比）

### 版本回退
`git log`命令显示提交的历史记录，可以加上`--pretty=oneline`参数使每条日志在一行中输出

在Git中，用HEAD表示当前版本，上一个版本是HEAD\^，上上一个版本是HEAD\^\^，为了方便，可以写成HEAD~n，n表示往上n个版本

版本回退：
`$ git reset --hard [version/commit-id]`

`git reflog`命令用来查看历史命令

### 工作区和暂存区
#### 工作区
电脑里能看见的目录

#### 版本库（repository）
工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。

Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD。

### 管理修改
Git跟踪并管理的是修改，而非文件。

### 撤销修改
撤销工作区的修改：
`$ git checkout -- [file-name]`

撤销修改有两种情况：
一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；  
一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。  
总之，就是让这个文件回到最近一次add或commit时的状态。

命令`git reset HEAD [file-name]`可以把暂存区的修改撤销掉（unstage），重新放回工作区

### 删除文件
首先在文件管理器中将文件删除，然后：
1. 如果确实要从版本库中删除该文件：
`$ git rm [file-name]`  
`$ git commit -m "[description]"`
2. 如果错删：
`$ git checkout -- [file-name]`
但是只能恢复文件到最新版本，而会丢失最近一次提交后修改的内容。

## 远程仓库
### 准备工作
详见：[GitHub - 创建并配置SSH key](https://help.github.com/articles/connecting-to-github-with-ssh/)

### 添加远程库
1. 首先要在GitHub上建立新的仓库
2. 将本地仓库与远程库关联  
<font face = "Courier New"> `$ git remote add origin [ssh/https]` </font>
3. 将本地库内容推送到远程库上（初次）  
<font face = "Courier New"> `$ git push -u origin master` </font>
其中`-u`参数是将本地的`master`分支与远程的`master`分支关联起来，可以简化后面推送和拉取操作的命令
4. 推送  
<font face = "Courier New"> `$ git push origin master` </font>
5. 如果关联远程库时报错：
`fatal: 远程origin已存在`
则如下处理：
`$ git remote rm origin`
然后再关联

### 从远程库克隆
`$ git clone [ssh/https]`

> Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快。

如果从远程仓库克隆时报出如下错误：  
`Permission denied (publickey).`
`fatal: Could not read from remote repository.`

`Please make sure you have the correct access rights`
`and the repository exists.`
说明GitHub不能认证本机，需要进行如下检查：  
1. 是否在本机生成SSH Key
`$ cd ~/.ssh`
如果提示目录不存在，则说明本机未生成SSH Key
2. 如果本机已经生成SSH Key，则检查GitHub中是否添加了本机的SSH Key

### 删除远程仓库的文件
`$ git rm --cached [filename]`  
`$ git commit -m "[description]"`

## 分支管理

### 创建与合并分支
HEAD严格来说不是指向提交，而是指向master，master才是指向提交的，所以，HEAD指向的就是当前分支。

当我们创建新的分支，例如dev时，Git新建了一个指针叫dev，指向master相同的提交，再把HEAD指向dev，就表示当前分支在dev上。不过，从现在开始，对工作区的修改和提交就是针对dev分支了。

1. 创建分支  
<font face = "Courier New"> `$ git checkout -b dev` </font>  
`git checkout`命令加上`-b`参数表示创建分支并切换，相当于以下两条命令：  
<font face = "Courier New"> `$ git branch dev` </font>
<font face = "Courier New"> `$ git checkout dev` </font>
2. 查看所有分支  
<font face = "Courier New"> `$ git branch` </font>  
当前分支前面会标一个*
3. 合并指定分支到当前分支  
<font face = "Courier New"> `$ git merge [branch name]` </font>
4. 删除分支  
<font face = "Courier New"> `$ git branch -d [branch name]` </font>

Git鼓励使用分支完成某个任务，合并后再删掉分支，这和直接在master分支上工作效果是一样的，但过程更安全。

### 解决冲突
#### 快速合并（Fast-forward）
如果当前分支在创建/切换分支后没有进行过改动，将另一个分支合并到当前分支时，仅将当前分支的指针移向另一个分支处，不记录其他信息

#### 冲突情况
1. 可以用 <font face = "Courier New"> `git status` </font> 列出冲突的文件
2. 如果查看冲突的文件，Git会用`<<<<<<<`，`=======`，`>>>>>>>`标记出不同分支的内容
3. 使用命令
<font face = "Courier New"> `$ git log --graph` </font>
可以看到分支合并的情况，加上参数
<font face = "Courier New"> `--pretty=oneline --abbrev-commit`</font>
可以显示精简版的分支合并情况

### 分支管理策略
通常，合并分支时，如果可能，Git会用Fast-forward模式，但这种模式下，删除分支后，会丢掉分支信息。

#### 强制禁用Fast-forward模式
如果要强制禁用Fast-forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。  
<font face = "Courier New"> `$ git merge --no-ff -m "[commit description]" [branch name]` </font>  
其中， <font face = "Courier New"> `--no-ff` </font> 参数表示禁用Fast-forward模式

#### 分支策略
master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；  
干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；  
团队中每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。

### Bug分支
#### 存储工作现场
如果需要修复工作中的bug，但当前的工作还未完成，就需要先将工作现场存储起来  

用命令 <font face = "Courier New"> `git stash` </font> 将工作现场储藏起来，然后再用 <font face = "Courier New"> `git status` </font> 命令查看工作区，就是干净的（除非有未被Git管理的文件）

可以用 <font face = "Courier New"> `git stash list` </font> 命令查看stash内容

#### 恢复工作现场
1. 方式一
<font face = "Courier New"> `$ git stash apply` </font>  
恢复工作现场，但stash内容并不删除  
<font face = "Courier New"> `$ git stash drop` </font>  
删除stash  
2. 方式二
<font face = "Courier New"> `$ git stash pop` </font>  
恢复工作现场的同时删除stash  
3. 可以多次stash，恢复时先用 <font face = "Courier New"> `git stash list` </font> 查看，然后恢复指定stash，用命令：  
<font face = "Courier New"> `$ git stash apply stash@{[number]}` </font>

### Feature分支
强行销毁修改后未合并的分支，用命令：
<font face = "Courier New"> `$ git branch -D [branch name]` </font>

### 多人协作
从远程仓库克隆时，实际上Git自动把本地的master分支和远程的master分支对应起来了，并且，远程仓库的默认名称是origin。

要查看远程库的信息，用 <font face = "Courier New"> `git remote` </font> ，或者用<font face = "Courier New"> `git remote -v` </font> 查看更详细的信息，会显示可以推送和抓取的地址  

#### 推送分支
推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：  
<font face = "Courier New"> `$ git push origin [branch name]` </font>  

本地分支并不一定全部推送到远端：
1. master分支是主分支，因此要时刻与远程同步；
2. dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
3. bug分支只用于在本地修复bug，没必要推到远程；
4. feature分支是否推到远程，取决于是否和团队其他成员合作在上面开发。

#### 抓取分支
从远程仓库克隆时，默认情况下只能看到master分支

1. 如果要在dev分支上开发，就必须创建远程origin的dev分支到本地：  
<font face = "Courier New"> `$ git checkout -b dev origin/dev` </font>
2. 完成修改后，如果推送时产生冲突，需要进行pull：
<font face = "Courier New"> `$ git pull` </font>  
3. 如果pull失败，提示“no tracking information”，说明没有指定本地dev分支与远程origin/dev分支的链接，根据提示，设置dev和origin/dev的链接：  
<font face = "Courier New"> `$ git branch --setupstream dev origin/dev` </font>

## 标签管理
发布一个版本时，我们通常先在版本库中打一个标签（tag），这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。

Git的标签虽然是版本库的快照，但其实它就是指向某个commit的不可移动的指针，所以，创建和删除标签都是瞬间完成的。

### 创建标签
1. 创建标签时首先要切换到要打标签的分支下面
2. 使用命令 <font face = "Courier New"> `git tag [tag name]` </font> 打标签
3. 标签默认打在最新提交的commit上，如果要对历史版本打标签，首先要通过 <font face = "Courier New"> `git log` </font> 找到其commit id，然后，使用命令 <font face = "Courier New"> `git tag [tag name] [commit id]` </font> 打标签
4. 创建带说明的标签：
<font face = "Courier New"> `$ git tag -a [tag name] -m "[description]"` </font>
5. 用私钥签名一个标签：
<font face = "Courier New"> `$ git tag -s [tag name] -m "[description]" [secret key]` </font>
> 用PGP签名的标签是不可伪造的，因为可以验证PGP签名。
> 签名采用PGP签名，因此，必须首先安装gpg（GnuPG），如果没有找到gpg，或者没有gpg密钥对，就会报错。
6. 查看所有标签：
<font face = "Courier New"> `$ git tag` </font>
7. 查看标签详细信息：  
<font face = "Courier New"> `$ git show [tag name]` </font>

### 操作标签
1. 删除标签：
<font face = "Courier New"> `$ git tag -d [tag name]` </font>
因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除。
2. 将标签推送到远程仓库：
推送一个：  
<font face = "Courier New"> `$ git push origin [tag name]` </font>  
推送全部：  
<font face = "Courier New"> `$ git push origin --tags` </font>  
3. 如果标签已经推送到远程仓库，删除标签：  
先在本地删除：  
<font face = "Courier New"> `$ git tag -d [tag name]` </font>  
然后从远程删除：  
<font face = "Courier New"> `$ git push origin :refs/tags/[tag name]` </font>

## 使用GitHub
fork其他人的项目之后，如果希望对方接受自己的修改，就发起pull request

## 使用码云
如果本地仓库已经关联了远端仓库，可以删除已有的远端库：
<font face = "Courier New"> `$ git remote rm origin` </font>

git允许本地库与多个远程仓库关联，首先要删除名为oigin的远程仓库，然后关联一个远程库：
<font face = "Courier New"> `$ git remote add github [ssh/https]` </font>
再关联另一个远程库：
<font face = "Courier New"> `$ git remote add gitee [ssh/https]` </font>
注意此时远程仓库的名称不再为origin，推送时也是用关联时的远程仓库名称：
<font face = "Courier New"> `$ git push [remote-name] [branch-name]` </font>

## 自定义Git
### 忽略特殊文件
在Git工作区的根目录下创建一个特殊的.gitignore文件，然后把要忽略的文件名填进去，Git就会自动忽略这些文件。

不需要从头写.gitignore文件，GitHub已经为我们准备了各种配置文件，只需要组合一下就可以使用了：[github/gitignore](https://github.com/github/gitignore)

忽略文件的原则是：
1. 忽略操作系统自动生成的文件，比如缩略图等；
2. 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的.class文件；
3. 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。

检验.gitignore的标准是git status命令是不是说working directory clean。

如果想强制添加一个被.gitignore文件忽略的文件，使用<font face = "Courier New"> `-f` </font> ：
<font face = "Courier New"> `$ git add -f [file-name]` </font>

如果.gitignore可能写得有问题，需要找出来到底哪个规则写错了，可以用git check-ignore命令检查：<font face = "Courier New">
`$ git check-ignore -v App.class` </font>

.gitignore文件本身要放到版本库里，并且可以对.gitignore做版本管理

### 配置别名
为命令配置别名：<font face = "Courier New">
`$ git config --global alias.[short] [command]` </font>  
如果命令多于一个单词，则如下：<font face = "Courier New">
`$ git config --global alias.[short] '[command]'` </font>

使用 <font face = "Courier New">
`git log -l` </font> 显示最近一次提交

#### 配置文件
配置Git的时候，加上--global是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用。

每个仓库的Git配置文件都放在.git/config文件中，当前用户的Git配置文件放在用户主目录下的一个隐藏文件.gitconfig中。别名就在 <font face = "Courier New"> `[alias]` </font> 后面。要删除别名，直接把对应的行删掉即可。

### 搭建Git服务器
搭建Git服务器需要一台运行Linux的机器，搭建步骤如下：
1. 安装git
2. 创建一个git用户，用来运行git服务 <font face = "Courier New">
`$ sudo adduser git` </font>
3. 创建证书登录
收集所有需要登录的用户的公钥，即他们自己的id\_rsa.pub文件，把所有公钥导入到/home/git/.ssh/authorized\_keys文件里，一行一个。
4. 初始化Git仓库
先选定一个目录作为Git仓库，假定是/srv/sample.git，在/srv目录下输入命令：<font face = "Courier New">
`$ sudo git init --bare sample.git` </font>
Git就会创建一个裸仓库，裸仓库没有工作区，因为服务器上的Git仓库纯粹是为了共享，所以不让用户直接登录到服务器上去改工作区，并且服务器上的Git仓库通常都以.git结尾。然后，把owner改为git：
<font face = "Courier New"> `$ sudo chown -R git:git sample.git` </font>
5. 禁用shell登录
出于安全考虑，第二步创建的git用户不允许登录shell，这可以通过编辑/etc/passwd文件完成。找到类似下面的一行：
<font face = "Courier New"> `git:x:1001:1001:,,,:/home/git:/bin/bash` </font>
改为：
<font face = "Courier New"> `git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell` </font>
这样，git用户可以正常通过ssh使用git，但无法登录shell，因为我们为git用户指定的git-shell每次一登录就自动退出。
6. 克隆远程仓库
现在，可以通过git clone命令克隆远程仓库了，在各自的电脑上运行：
<font face = "Courier New"> `$ git clone git@server:/srv/sample.git` </font>

> #### 管理公钥
> 如果团队规模小，可以将每个人的公钥放在服务器的/home/git/.ssh/authorized_keys中，但如果团队规模很大，就需要使用Gitosis来管理公钥。

> #### 管理权限
> 因为Git是为Linux源代码托管而开发的，所以Git也继承了开源社区的精神，不支持权限控制。不过，因为Git支持钩子（hook），所以，可以在服务器端编写一系列脚本来控制提交等操作，达到权限控制的目的。Gitolite就是这个工具。
