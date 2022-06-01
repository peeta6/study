# Git

## 实战

### 1、安装git

```shell
brew install git
yum -y install git 
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

# 以上信息保存在~/.gitconfig里面
```

### 3、git原理图

<img src="https://images0.cnblogs.com/blog2015/687225/201508/211915189723300.jpg" alt="img" style="zoom:67%;" />

### 4、git项目搭建

```shell
# 获取git仓库的两种方式
# 初始化项目
git init

# 克隆项目
git clone [url]

# 
```

### 5、git文件操作

文件四种状态

![image-20211210122908945](/Users/mac/Library/Application Support/typora-user-images/image-20211210122908945.png)

```shell
# 添加到暂存区域
git add .

# 取消暂存
git reset hello.txt

git reset HEAD [文件夹名/或者文件名]
git checkout 文件

# 查看状态
git status -s

# 提交到本地库
git commit -m "modify my date"
```

![image-20211210123533830](/Users/mac/Library/Application Support/typora-user-images/image-20211210123533830.png)

### 6、git忽略文件

![image-20211210124132609](/Users/mac/Library/Application Support/typora-user-images/image-20211210124132609.png)

注意：/在前和后是有区别的，在前表示向上忽略，在后面表示忽略后面的所有文件

### 7、git分支的操作

```bash
# 列出所有本地分支
git branch 

# 列出所有远程分支
git branch -r

# 新建一个分支，但依然停留在当前分支
git branch [branch-name]

# 新建一个分支，并切换到该分支
git checkout -b [branch]

# 拉取远程分支并切换到该分支
git checkout -b dev_acl origin/dev_acl
# 其中远程分支为：origin/dev_acl
# 本地分支为：dev_acl

# 合并指定分支到当前分支
git merge [branch]

# 删除分支
git branch -d [branch-name]

# 删除远程分支
git push origin --delete [branch-name]
git branch -dr [remote/branch]
```

## git常用命令

### 1.环境配置

```shell
# 设置用户信息
git config --global user.name "peeta"
git config --global user.email "peeta@163.com"
# 查看配置信息
git config --list
git config user.name

# 通过上面的命令设置的信息会保存在~/.gitconfig文件中
```

### 2.获取git仓库

获取git仓库通常有两种方法：

```shell
#	1. 在本地初始化一个git仓库
git init
# 2. 从远程仓库clone
git clone 远程仓库的网络地址
```

### 3.工作目录、暂存区以及本地库概念

![image-20211229171514878](/Users/mac/Library/Application Support/typora-user-images/image-20211229171514878.png)

### 4.git工作目录下文件的两种状态

Git工作目录下的文件存在的两种状态：

- untracked 未跟踪（未被纳入版本控制）
- tracked 已跟踪（被纳入版本控制）
  - Unmodified 未修改状态
  - Modified 已修改状态
  - Staged 已暂存状态

这些文件的状态都会随着我们执行Git命令发生变化

### 5.本地仓库操作

```shell
# 查看文件状态
git status
# 输出的信息更加简介
git status -s
```

![image-20211229173157880](/Users/mac/Library/Application Support/typora-user-images/image-20211229173157880.png)

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

#### 将文件添加至忽略列表

 ![image-20211229194722858](/Users/mac/Library/Application Support/typora-user-images/image-20211229194722858.png)

#### git log查看日志

```shell
# 查看日志记录
git log

# 回退版本
git reset --hard Id
git push -f -u origin master
```

###   6.远程仓库的操作

![image-20211229195417031](/Users/mac/Library/Application Support/typora-user-images/image-20211229195417031.png)

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

![image-20211229201312496](/Users/mac/Library/Application Support/typora-user-images/image-20211229201312496.png)

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

### 7.git分支

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

### 8.git 标签

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



```shell
# 补充命令

# 暂存
git stash

# pop暂存
git stash pop

git submodle

```

