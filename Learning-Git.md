# Git
---
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

### 从远程库克隆
<font face = "Courier New"> `$ git clone [ssh/https]` </font>

> Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快。

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
<font face = "Courier New"> `git merge --no-ff -m "[commit description]" [branch name]` </font>
其中， <font face = "Courier New"> `--no-ff` </font> 参数表示禁用Fast-forward模式

#### 分支策略
master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；  
干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；  
团队中每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。