# Git分享

## 一、什么是版本控制？

**可以把一个版本控制系统（缩写VCS）理解为一个“数据库”，在需要的时候，它可以帮你完整地保存一个项目的快照。当你需要查看一个之前的快照（称之为“版本”）时，版本控制系统可以显示出当前版本与上一个版本之间的所有改动的细节。**

## 二、在团队开发中使用版本控制系统的好处

### 1.作为数据备份

这是一个主要的原因了。虽然数据备份同样可以用比较笨的方法：本地复制副本，但这种古老的方法有很多不足，无法应对频繁的修改，会照成版本混乱，不便于管理，并且占用空间。

### 2.版本管理

避免版本管理混乱。这是使用版本管理的最主要原因，也是版本管理的目的所在。你肯定不会希望在本地手动备份了多个副本后，到头来却不知道那个备份是最新的，那个备份进行了什么修改，修改日期是什么时候等等一切你记不清的问题。而版本管理软件能解决这些问题，它有详细的日志，能记住你的每一次提交、每一次改动，并且能够比较查看不同版本之间的异同，并且可以恢复到之前的任一版本。

### 3.提高代码质量

在没有版本管理之前，可能经常要在代码里写些不相关的注释，比如：某人某日对某代码进行修改；或是将一些不确定是否使用的代码用注释的形式保留等等,这些也就是僵尸代码啦。现在这些工作都可以交由版本管理工具完成，把这些不相关的僵尸代码从代码里删掉吧。

### 4.提高协同、多人开发时的效率

及时提交更新代码，能让团队中的成员了解到代码的最新情况，避免重复劳动。

### 5.明确分工责任

什么时候谁对代码做了修改、修改了什么内容，版本管理都会记录在案，方便查询，追究责任。

### 6.除了代码以外，很多文档、个人资料，如：简历等等都可以进行版本管理，这是有趣而高效的。凡是需要持续修改的文档资料都可以进行版本管理

## 三、实战

### 1、安装git

```shell
brew install git
yum -y install git 

# windows安装
https://www.jianshu.com/p/414ccd423efc
```

### 2、查看配置

```shell
#	查看系统配置
git config --system --list

# 查看全局配置，我们自己配的
git config --global --list

# 设置用户名和邮箱
git config --global user.name "peeta" # 用户名
git config --global user.email 954457496@qq.com		# 邮箱

# 通过上面的命令设置的信息会保存在~/.gitconfig文件中
```

### 3、git原理图

### 

<img src="https://images0.cnblogs.com/blog2015/687225/201508/211915189723300.jpg" alt="img" style="zoom:67%;" />

工作区: 即我们创建的工程文件， 在编辑器可直观显示；

暂存区: 只能通过git GUI或git shell 窗口显示，提交代码、解决冲突的中转站；

本地仓库: 只能在git shell 窗口显示，连接本地代码跟远程代码的枢纽，不能联网时本地代码可先提交至该处；

远程仓库: 即保存我们代码的服务器，本文以公共版本控制系统：github为例，登录github账号后可直观显示；

### 4、git项目搭建

获取git仓库通常有两种方法：

```shell
# 获取git仓库的两种方式
# 初始化项目
git init

# 克隆项目
git clone [url]

```

### 5、git文件操作

文件四种状态

![image-20211210122908945](/Users/mac/Library/Application Support/typora-user-images/image-20211210122908945.png)

```shell
# 添加到暂存区域
git add .

# 取消暂存
git reset hello.txt

# 查看文件状态
git status

# 输出的信息更加简洁
git status -s

# 提交到本地库
git commit -m "modify my date"
```

![image-20211210123533830](/Users/mac/Library/Application Support/typora-user-images/image-20211210123533830.png)



```shell
# 将未跟踪的文件加入暂存区
git add hello.txt
# 取消暂存
git reset hello.txt
# 将新创建的文件加入暂存区后查看文件状态
git status
```

```shell
# 将暂存区的文件修改提交到本地仓库
git commit -m "init hello.txt"
```

```shell
# 删除文件,只是将工作区的文件删除，但是不需要add，直接commit就行了
git rm hello.txt
# commit
git commit
```

### 6、git忽略文件

![image-20211210124132609](/Users/mac/Library/Application Support/typora-user-images/image-20211210124132609.png)

注意：/在前和后是有区别的，在前表示向上忽略，在后面表示忽略后面的所有文件

### 7、git log查看日志

```shell
# 查看日志记录
git log

# 回退版本
git reset --hard Id
git push -f -u origin master
```

###   8、远程仓库的操作

![image-20220210153010984](/Users/mac/Library/Application Support/typora-user-images/image-20220210153010984.png)

#### 查看远程仓库操作

```shell
# 查看远程仓库的命令,它会列出指定的每一个远程服务器的简写
git remote
# 加上 -v 会给出详细信息
git remote -v
# 如果已经克隆了远程仓库，那么至少应该能看到origin，这是Git克隆的仓库服务器的默认名字

# 查看更详细的信息
git remote show origin
```

#### 添加远程仓库

```shell
# 添加远程仓库命令 添加一个新的远程Git仓库，同时指定一个可以引用的简写
git remote add <shortname> <url> 

git remote add origin <url>

# 一个本地库可以添加多个远程仓库
```

#### 从远程仓库克隆

如果你想要获得一份已经存在了的Git仓库的拷贝，这是就要用git clone命令。Git克隆的是该Git仓库服务器上的几乎所有数据（包括日志信息、历史记录等），而不仅仅是复制工作所需要的文件。当你执行git clone命令的时候，默认配置下远程Git仓库中的每一个文件的每一个版本都将被拉取下来。

克隆仓库的命令格式是git clone [url]

#### 移除无效的远程仓库

```shell
# 移除无效的远程仓库命令
git remote rm <远程仓库名>

# 注意：此命令只是从本地移除远程仓库的记录，并不会真正影响到远程仓库
```

#### 从远程仓库中抓取与拉取

##### fetch

```shell
# git fetch是从远程仓库获取最新版本到本地仓库，不会自动merge
git fetch # 可以直接用此命令
git fetch origin master # 指定远程仓库和分支
git merge origin/master
```

##### pull

```shell
# git pull是从远程仓库获取最新版本并merge到本地仓库
git pull # 可以直接用此命令
git pull origin master # 指定远程仓库和分支

# 注意：如果当前本地仓库不是从远程仓库克隆，而是本地创建的仓库，并且仓库中存在文件，此时再从远程仓库拉取文件的时候会报错(fatal:refusing to merge inrelated histories)，解决此问题可以在git pull命令后加入参数--allow-unrelated-histories
```

##### push

```shell
# git push 将本地代码推送到远程仓库 git push [remote-name] [branch-name]
git push origin master
```

### 9、git分支

```shell
# 查看分支
# 列出所有本地分支
git branch
# 列出所有远程分支
git branch -r
# 列出所有本地分支和远程分支
git branch -a
```

```shell
# 创建分支 git branch [分支名称]
git branch dev

# 切换分支 git checkout [分支名称]
git checkout dev
```

```shell
# 分支合并 merge到哪，当前就在那个分支，如我要把dev merge到master上
git checkout master 
git merge dev

# 解决冲突问题，在两个分支中提交两次commit，然后解决问题
```

```shell
# 本地分支推送到远程仓库,这个意思是将本地的dev分支推送到origin这个远程仓库中，即使现在是在master分支上也是推送的dev
git push origin dev
```

```shell
# 删除分支--这里指的是删除本地分支
git branch -d dev
# 如果在要删除的分支中进行了一些开发工作，此时执行上面的删除命令并不会删除分支，如果坚持删除这个分支

# 如果要删除远程仓库的分支 git push origin -d [branch-name]
git push origin -d b1
```

```shell
# 新建一个分支，并切换到该分支
git checkout -b [branch]

# 拉取远程分支并切换到该分支
git checkout -b dev_acl origin/dev_acl
# 其中远程分支为：origin/dev_acl
# 本地分支为：dev_acl

# 删除远程分支--补充
git push origin --delete [branch-name]
git branch -dr [remote/branch]
```

### 10、git 标签

标签用于标记某一提交点，唯一绑定一个固定的commitId，相当于为这次提交记录指定一个别名，方便提取文件。
可以为重要的版本打上标签，标签可以是一个对象，也可以是一个简单的指针，但是指针不会移动。

#### 创建与查看标签

```shell
# 列出已有标签
git tag

# 查看tag信息
git show tag 

# 显示标签名及其描述信息
git tag -ln [tag_name] 

# 创建一个新的标签 git tag [tag-name],为当前分支指向的commit记录创建标签
git tag v1.0
# 为指定的commit Id创建标签 git tag [tag-name] [hash-val]
git tag v1.2 
# 创建标签同时添加说明信息
git tag -a <tag_name> -m "msg" <hash_val> 

# 将标签推送到远程仓库 git push [remote] [tag]
git push origin v1.0
# 将本地的全部tag推送到远程服务器 git push [remote_name] --tags 
git push origin --tags
```

#### 删除标签

```shell
#删除本地的标签
git tag -d <tag_name>
#删除远程标签
git push <remote_name> :refs/tags/<tag_name>
```



#### 切换标签

```shell
# 检出标签

# 新建一个分支，并指向某个tag
git checkout -b [branch] [tag]
```

示例切换到v1.0，此时提示我们处于`detached HEAD state`(分离头指针状态)，即说明HEAD指针没有指向具体的分支，查看HEAD指针它直接指向了一个commit对象，此时进行开发操作没有任何意义。

![image-20220210131351764](/Users/mac/Library/Application Support/typora-user-images/image-20220210131351764.png)

### 11、补充命令

```shell
# 补充命令

# 暂存
git stash

# pop暂存
git stash pop

```
