



# docker

## 一、认识docker

### 1.1 安装docker

```shell
# 安装gcc相关环境，保证虚拟机可以上网
yum -y install gcc
yum -y install gcc-c++
# 移除旧版本
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
                  
# 安装yum-utils
yum install -y yum-utils

# 使用阿里云源
yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 更新yum软件包索引
yum makecache fast

# 安装docker
yum install -y docker-ce docker-ce-cli containerd.io

# 启动docker
systemctl start docker

# 测试命令
docker version
docker images
```



## 二、Docker的常用命令

### 2.1 帮助命令

```shell
docker version
docker info
docekr 命令 --help
```

### 2.2 镜像命令

```shell
[root@iZ2zeg4ytp0whqtmxbsqiiZ ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        7 months ago        13.3kB
 
# 解释
REPOSITORY      # 镜像的仓库
TAG             # 镜像的标签
IMAGE ID        # 镜像的ID
CREATED         # 镜像的创建时间
SIZE            # 镜像的大小
 
# 可选项
--all , -a      # 列出所有镜像
--quiet , -q    # 只显示镜像的id
```

### 2.3 查找镜像

```shell
[root@iZ2zeg4ytp0whqtmxbsqiiZ ~]# docker search mysql --filter=STARS=3000
NAME                DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql               MySQL is a widely used, open-source relation…   9822                [OK]                
mariadb             MariaDB is a community-developed fork of MyS…   3586                [OK]  
```

### 2.3 下拉镜像

```shell
# 下载镜像，docker pull 镜像名[:tag]
[root@iZ2zeg4ytp0whqtmxbsqiiZ ~]# docker pull mysql
Using default tag: latest           # 如果不写tag，默认就是latest
latest: Pulling from library/mysql
bf5952930446: Pull complete         # 分层下载，dockerimages的核心，联合文件系统
8254623a9871: Pull complete 
938e3e06dac4: Pull complete 
ea28ebf28884: Pull complete 
f3cef38785c2: Pull complete 
894f9792565a: Pull complete 
1d8a57523420: Pull complete 
6c676912929f: Pull complete 
ff39fdb566b4: Pull complete 
fff872988aba: Pull complete 
4d34e365ae68: Pull complete 
7886ee20621e: Pull complete 
Digest: sha256:c358e72e100ab493a0304bda35e6f239db2ec8c9bb836d8a427ac34307d074ed     # 签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest      # 真实地址
 
# 等价于
docker pull mysql
docker pull docker.io/library/mysql:latest
 
# 指定版本下载
[root@iZ2zeg4ytp0whqtmxbsqiiZ ~]# docker pull mysql:5.7
5.7: Pulling from library/mysql
bf5952930446: Already exists 
8254623a9871: Already exists 
938e3e06dac4: Already exists 
ea28ebf28884: Already exists 
f3cef38785c2: Already exists 
894f9792565a: Already exists 
1d8a57523420: Already exists 
5f09bf1d31c1: Pull complete 
1b6ff254abe7: Pull complete 
74310a0bf42d: Pull complete 
d398726627fd: Pull complete 
Digest: sha256:da58f943b94721d46e87d5de208dc07302a8b13e638cd1d24285d222376d6d84
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
 
# 查看本地镜像
[root@iZ2zeg4ytp0whqtmxbsqiiZ ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mysql               5.7                 718a6da099d8        6 days ago          448MB
mysql               latest              0d64f46acfd1        6 days ago          544MB
hello-world         latest              bf756fb1ae65        7 months ago        13.3kB
```

### 3.4 列出所有运行的容器

```shell
# docker ps 
		#
-a  #列出当前正在运行的容器+带出历史运行过的容器
-n=? # 显示最近创建的容器
-q # 只显示容器的编号
```

### 2.5 退出容器

```shell
exit # 直接容器停止并退出
CTRL + P + Q # 容器不停止退出
```

### 2.6 删除容器

```shell
docker rm 容器id     					#删除指定的容器，不能删除正在运行的容器，如果要强制删除 rm -f
docker rm -f $(docker ps -aq)  # 删除所有容器
docker ps -a -q|xargs docker rm #删除所有容器
```

### 2.7 启动和停止容器的操作

```shell
docker start 容器id 		# 启动容器
docker restart 容器id		# 重启容器
docker stop 容器id 			# 停止当前正在运行的容器
docker kill 容器id			# 强制停止当前容器
```

### 2.8 其他常用指令

#### 后台启动容器

```shell
# 命令 docker run -d 镜像名
docker run -d centos
# 问题docker ps，发现centos停止了
# 常见的坑，docker容器使用了后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止
# nginx 容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了，“自杀了”
```

#### 查看日志

```shell
docker logs -tf --tail +number  #容器，没有日志
# 编写一段shell脚本
# [root@localhost /]# docker run -d centos /bin/sh -c "while true;do echo kuangshen;sleep 1;done"

# 显示日志
# docker logs -tf --tail 
 -tf							# 显示日志
 --tail number		# 显示日志条数
[root@localhost /]# docker logs -tf --tail 10 142280045aa5
```

#### 查看容器中进程信息

```shell
top命令
[root@localhost /]# docker top 95174671c03e
UID       PID       PPID         C            STIME         TTY           TIME                CMD
root      20292     20271        0            19:24         pts/0         00:00:00            /bin/bash
```

#### 查看镜像的元数据

```shell
# 命令
docker inspect 容器id
[root@localhost /]# docker inspect 95174671c03e

```

#### 进入当前正在运行的容器

```shell
#	容器通常都是使用后台方式运行的，需要进入容器，修改一些配置

# 命令
docker exec -it 容器id bashShell

# 测试
[root@192 ~]# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
727cdd420dbf   centos    "/bin/bash"   32 seconds ago   Up 31 seconds             upbeat_johnson
[root@192 ~]# docker exec -it 727cdd420dbf /bin/bash		## 这是关键
[root@727cdd420dbf /]# ps -ef
UID         PID   PPID  C STIME TTY          TIME CMD
root          1      0  0 13:21 pts/0    00:00:00 /bin/bash
root         17      0  0 13:25 pts/1    00:00:00 /bin/bash
root         31     17  0 13:25 pts/1    00:00:00 ps -ef

# 方式二
docker attach 容器id

# 测试
[root@192 ~]# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
2afd80f593ff   centos    "/bin/bash"   29 seconds ago   Up 27 seconds             vibrant_newton
[root@192 ~]# docker attach 2afd80f593ff
[root@2afd80f593ff /]#
进去后执行当前的代码

# docker exec 	# 进入容器后开启一个新的终端，可以在里面操作(常用)
# docker attach # 进入容器正在执行的终端，不会启动新的进程
```

#### 从容器内拷贝文件到主机上

```shell
docker cp 容器id:容器内路径  目的的主机路径

# 查看当前主机目录下的文件
[root@192 ~]# ls
anaconda-ks.cfg  opt
[root@192 ~]# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
817666e8bc67   centos    "/bin/bash"   26 seconds ago   Up 26 seconds             distracted_bhaskara
# 进入docker容器内
[root@192 ~]# docker attach 817666e8bc67
# 在容器内新建一个文件
[root@817666e8bc67 /]# touch java.txt
[root@817666e8bc67 /]# exit
exit
[root@192 ~]# docker ps -a
CONTAINER ID   IMAGE    COMMAND        CREATED              STATUS      	           PORTS     NAMES
817666e8bc67   centos   "/bin/bash"    About a minute ago   Exited (0) 8 seconds ago        distracted_bhaskara
# 将这个文件拷贝出来到主机上
[root@192 ~]# docker cp 817666e8bc67:/java.txt ./
[root@192 ~]# ls
anaconda-ks.cfg  java.txt  opt

# 拷贝是一个手动过程，未来我们使用-v卷的技术，可以实现自动同步，/home  /home
```

### 小结

![img](https://img-blog.csdn.net/2018071915491757?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE2MjkwNzkx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```shell
attach    Attach to a running container  #当前shell下attach连接指定运行镜像
build     Build an image from a Dockerfile  #通过Dockerfile定制镜像
commit    Create a new image from a containers changes  #提交当前容器为新的镜像
cp    Copy files/folders from a container to a HOSTDIR or to STDOUT  #从容器中拷贝指定文件或者目录到宿主机中
create    Create a new container  #创建一个新的容器，同run 但不启动容器
diff    Inspect changes on a containers filesystem  #查看docker容器变化
events    Get real time events from the server#从docker服务获取容器实时事件
exec    Run a command in a running container#在已存在的容器上运行命令
export    Export a containers filesystem as a tar archive  #导出容器的内容流作为一个tar归档文件(对应import)
history    Show the history of an image  #展示一个镜像形成历史
images    List images  #列出系统当前镜像
import    Import the contents from a tarball to create a filesystem image  #从tar包中的内容创建一个新的文件系统映像(对应export)
info    Display system-wide information  #显示系统相关信息
inspect    Return low-level information on a container or image  #查看容器详细信息
kill    Kill a running container  #kill指定docker容器
load    Load an image from a tar archive or STDIN  #从一个tar包中加载一个镜像(对应save)
login    Register or log in to a Docker registry#注册或者登陆一个docker源服务器
logout    Log out from a Docker registry  #从当前Docker registry退出
logs    Fetch the logs of a container  #输出当前容器日志信息
pause    Pause all processes within a container#暂停容器
port    List port mappings or a specific mapping for the CONTAINER  #查看映射端口对应的容器内部源端口
ps    List containers  #列出容器列表
pull    Pull an image or a repository from a registry  #从docker镜像源服务器拉取指定镜像或者库镜像
push    Push an image or a repository to a registry  #推送指定镜像或者库镜像至docker源服务器
rename    Rename a container  #重命名容器
restart    Restart a running container  #重启运行的容器
rm    Remove one or more containers  #移除一个或者多个容器
rmi    Remove one or more images  #移除一个或多个镜像(无容器使用该镜像才可以删除，否则需要删除相关容器才可以继续或者-f强制删除)
run    Run a command in a new container  #创建一个新的容器并运行一个命令
save    Save an image(s) to a tar archive#保存一个镜像为一个tar包(对应load)
search    Search the Docker Hub for images  #在dockerhub中搜索镜像
start    Start one or more stopped containers#启动容器
stats    Display a live stream of container(s) resource usage statistics  #统计容器使用资源
stop    Stop a running container  #停止容器
tag         Tag an image into a repository  #给源中镜像打标签
top       Display the running processes of a container #查看容器中运行的进程信息
unpause    Unpause all processes within a container  #取消暂停容器
version    Show the Docker version information#查看容器版本号
wait         Block until a container stops, then print its exit code  #截取容器停止时的退出状态值
```

## 三、练习

### 3.1 安装Nginx

```shell
# 1、搜索镜像 search  建议大家到dockerhub上搜索，可以看到帮助文档
#	2、下载镜像 pull
# 3、运行测试

# -d表示后台方式启动
# --name nginx01给这个nginx服务起名字为nginx01
# -p 3344:80表示在公网的3344可以访问到docker的80端口
[root@192 ~]# docker run -d --name nginx01 -p 3344:80 nginx
6133bcd401a46e77b89db1c73b255ee4effab7526e0b17d79ba91f3af3b3766a
[root@192 ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                   NAMES
6133bcd401a4   nginx     "/docker-entrypoint.…"   3 minutes ago   Up 3 minutes   0.0.0.0:3344->80/tcp, :::3344->80/tcp   nginx01
# 看看有没有welcome nginx！，然后再自己电脑浏览器输入服务器ip:3344看看能不能启动nginx
[root@192 ~]# curl localhost:3344

4、进入容器
[root@192 ~]# docker exec -it nginx01 /bin/bash
wroot@6133bcd401a4:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@6133bcd401a4:/# cd /etc/nginx/
root@6133bcd401a4:/etc/nginx# ls
conf.d	fastcgi_params	mime.types  modules  nginx.conf  scgi_params  uwsgi_params
```

每次修改nginx配置文件，都需要进入容器内部，十分的麻烦，我们可以再容器外部提供一个映射路径，达到在容器修改文件名，容器内部就可以自动修改？-v 数据卷技术

### 3.2 安装tomcat

```shell
# 官方的使用
docker run -it --rm tomcat:9.0

# 我们之前的启动都是后台运行，停止了容器之后，容器还是可以查到的， docker run -it -rm，一般用来测试，用完即删除

1、下载并启动
docker run -d -p 3355:8080 --name tomcat01 tomcat
# 测试访问没有问题

# 进入容器
docker exec -it tomcat01 /bin/bash

# 发现问题：1、linux命令少了 2、没有webapps。阿里云的镜像的原因，默认是最小的镜像，所有不必要的都要剔除掉
# 保证最小可运行的环境
```

还是那个问题？

### 3.3 部署es+kibana

```shell
# es 暴露的端口很多！！
# es 十分消耗内存
# es 的数据一般需要放置到安全目录！挂载

# 启动elasticsearch,官方方法
docker network create somenetwork
# --net somenetwork 这是什么？ 网络配置
docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.2

# 启动了 linux就卡住了 docker stats 查看cpu的状态

# es是十分耗内存的

# 停止整个docker

# 测试一下es是否成功了
[root@192 ~]# curl localhost:9200
{
  "name" : "61de00248ec3",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "GQ-xuIhtRAS9_xZJBEAVjQ",
  "version" : {
    "number" : "7.6.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "ef48eb35cf30adf4db14086e8aabd07ef6fb113f",
    "build_date" : "2020-03-26T06:34:37.794943Z",
    "build_snapshot" : false,
    "lucene_version" : "8.4.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}

# docker stats 查看cpu的状态
```

内存情况

![image-20211128203546167](/Users/mac/Library/Application Support/typora-user-images/image-20211128203546167.png)

```shell
# 添加内存限制，修改配置文件 -e 环境配置修改
docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx521m" elasticsearch:7.6.2

# 不知道为什么添加了限制，cpu反而被打满了，不添加配置反而没事，这里可能是因为elasticresearch官方添加了限制？
```

小问题kibana怎么链接es？

### 可视化

```shell
portainer
```

**什么是portainer？**

docker图形化界面管理工具！提供一个后台面板供我们操作！

```shell
docker run -d -p 8088:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

访问测试：ip:8088

可视化界面自己平时玩玩就行，平时不用

## 四、Docker镜像

### 4.1 镜像是什么

镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时、库、环境变量、和配置文件。

所有的应用，直接打包docker镜像，就可以直接跑起来！

如何得到镜像：

从远程仓库下载

2、朋友拷贝

3、自己制作一个镜像DockerFile

### 4.2 UnionFS(联合文件系统)

Union文件系统(UnionFS)是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下(unite several directories into a single virtual filesystem)。Union文件系统是Docker镜像的基础。镜像可以通过分层来进行继承, 基于基础镜像(没有父镜像)， 可以制作各种具体的应用镜像。

特性: 一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录。

###  4.3 Docker镜像加载原理

docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统UnionFS。

**bootfs(boot file system)**主要包含bootloader和kernel，bootloader主要是引导加载kernel，Linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是bootfs。这一层与我们典型的Linux/Unix系统是一样的, 包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。

**rootfs(root file system)**, 在bootfs之上。包含的就是典型Linux系统中的/dev, /proc, /bin, /etc等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu，Centos等等。

![Docker镜像的内部结构(四)](http://i2.51cto.com/images/blog/201711/27/462718f3cd35608c20481b33b09db020.jpg?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

平时我们安装虚拟机的CentOS都是好几个G，为什么docker这里才200M？

![image-20211128213958163](/Users/mac/Library/Application Support/typora-user-images/image-20211128213958163.png)

对于一个精简的OS，rootfs可以很小，只需要包括最基本的命令、工具和程序库就可以了，因为底层直接用Host的kernel，自己只需要提供rootfs就行了。由此可见对于不同的linux发行版，bootfs基本是一致的，rootfs会有差别，因此不同的发行版可以共用bootfs。

### 4.4 分层的理解

分层的镜像

可以去下载一个镜像，注意观察下载的日志输出，可以看到一层一层的在下载！

以下载一个redis镜像为例

![image-20211128214844303](/Users/mac/Library/Application Support/typora-user-images/image-20211128214844303.png)

可以看到红框部分已经存在了不需要pull，而本地不存在的蓝框部分被pull下来

思考：为什么Docker镜像要采用这种分层的结构呢？

最大的好处，我觉得莫过于资源共享了！比如有多个镜像都从相同的base进行共享构建而来，那么宿主机只需要在磁盘上保留一份base镜像，同时内存中也只需要加载一份base镜像，这样就可以为所有容器服务了，而且镜像的每一层都可以被共享。

查看镜像分层的方式可以通过**docker image inspect**命令！

```shell
# 举例
docker image inspect redis
```

![image-20211128215232363](/Users/mac/Library/Application Support/typora-user-images/image-20211128215232363.png)

所有的Docker镜像都起始于一个基础镜像层，当进行修改或增加新的内容时，就会在当前镜像层之上，创建新的镜像层。
比如：

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg-blog.csdnimg.cn%2F2021021613182633.png%3Fx-oss-process%3Dimage%2Fwatermark%2Ctype_ZmFuZ3poZW5naGVpdGk%2Cshadow_10%2Ctext_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d1bGVpMjkyMTYyNTk1Nw%3D%3D%2Csize_16%2Ccolor_FFFFFF%2Ct_70&refer=http%3A%2F%2Fimg-blog.csdnimg.cn&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1640700095&t=b5c8b6eb71d183238cbd1dc84ef43a39" alt="img" style="zoom: 67%;" />

我现在要制作一个镜像。

1. 这个镜像基于Ubuntu linux 16.04，这也是镜像的第一层。
2. 继续还要安装python包，就会在第一层之上创建第二个镜像层。
3. 继续打补丁的话，还会再创建第三个镜像层。

要**注意**的是：
在添加额外的镜像层的同时，镜像始终保持是当前所有镜像的组合，理解这一点非常重要。下图中举了一个简单的例子，每个镜像层包含3个文件，而镜像包含了来自两个镜像层的6个文件。

<img src="https://pic4.zhimg.com/80/v2-b7e557bed8e7c7b4c14e263b22d023c3_1440w.jpg" alt="img" style="zoom:67%;" />

上图中的镜像层跟之前图中的略有区別，主要目的是便于展示文件。

下图中展示了一个稍微复杂的三层镜像，在外部看来整个镜像只有6个文件，这是因为最上层中的文件7是文件5的一个更新版。

<img src="https://pic1.zhimg.com/80/v2-5ee50f8596b43f94226b41aa2ff8b48c_1440w.jpg" alt="img" style="zoom:67%;" />

这种情況下，上层镜像层中的文件覆盖了底层镜像层中的文件。这样就使得文件的更新版本作为一个新镜像层添加到镜像当中。Docker通过存储引擎（新版本采用快照机制）的方式来实现镜像层堆栈，并保证多镜像层对外展示为统一的文件系统Linux上可用的存储引撃有AUFS、 Overlay2、 Device Mapper、Btrfs以及ZFS。顾名思义，每种存储引擎都基于 Linux中对应的文件系统或者块设备技术，井且每种存储引擎都有其独有的性能特点。

Docker在 Windows上仅支持 windowsfilter 一种存储引擎，该引擎基于NTFS文件系统之上实现了分层和CoW [1]。下图展示了与系统显示相同的三层镜像。所有镜像层堆并合井，对外提供统一的视图。

<img src="https://pic1.zhimg.com/80/v2-7a9978d719f9d7159f5ad2900ae9a8c8_1440w.jpg" alt="img" style="zoom:67%;" />

**特点**

Docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部！

这一层就是我们通常说的容器层，容器之下的都叫镜像层！

### 4.5 commit镜像

```shell
docker commit			# 提交容器成为一个新的副本

# 命令和git原理相似
docker commit -="提交的描述信息" -a="作者" 容器id 目标镜像名:[TAG]
```

自测

```shell
# 启动tomcat

# 发现默认的tomcat是没有webapps应用的，镜像的原因，官方的镜像默认webapps下面是没有文件的

# 执行一下的命令我们自己拷贝一份
cp -r webapps.dist/* webapps

# 打包
使用命令
docker commit -a="dage" -m="add tomcat application" 9fff2c4ccc41 tomcat01:1.1.0
	-a				# 创建人
	-m				# commit信息
	容器id
	:TAG			#别忘记tag
	
详细过程如下：
[root@192 ~]# docker ps
CONTAINER ID   IMAGE                 COMMAND             CREATED          STATUS          PORTS                                       NAMES
9fff2c4ccc41   tomcat                "catalina.sh run"   13 minutes ago   Up 13 minutes   0.0.0.0:8080->8080/tcp, :::8080->8080/tcp   nervous_rubin
[root@192 ~]# docker commit -a="dage" -m="add tomcat application" 9fff2c4ccc41 tomcat01:1.1.0
sha256:ff8e1421258532945955591d1cf4226df946755e80752e2f7754b6dad3c00765
[root@192 ~]# docker images
REPOSITORY            TAG       IMAGE ID       CREATED         SIZE
tomcat01              1.1.0     ff8e14212585   7 seconds ago   684MB
tomcat                latest    904a98253fbf   10 days ago     680MB
redis                 latest    40c68ed3a4d2   10 days ago     113MB
nginx                 latest    ea335eea17ab   11 days ago     141MB
centos                latest    5d0da3dc9764   2 months ago    231MB
portainer/portainer   latest    580c0e4e98b0   8 months ago    79.1MB
elasticsearch         7.6.2     f29a1ee41030   20 months ago   791MB
```

提示：

如果想要保存当前容器的状态，就可以通过commit来提交，获得一个镜像，这个概念比较想vm虚拟机快照的概念。

恭喜你！！看到这里算你已经入门docker了！！

## 五、容器数据卷

### 5.1 什么是容器数据卷

**docker的理念回顾****

将应用和环境打包成一个镜像！

数据？如果数据都在容器中，那么我们容器删除，数据就会丢失！**需求：数据持久化**

MySQL，如果删除容器，那么数据是不是就丢了呢！**需求：MySQL数据可以存储在本地**

容器之间可以有一个数据共享的技术！Docker容器中产生的数据，同步到本地！

这就是卷技术！目录的挂载，将我们容器内的目录，挂载到linux上面！

总结成一句话：容器的持久化和同步操作！容器间也是可以数据共享的

### 5.2 使用数据卷

#### 方式一、直接使用命令来挂载-v

```shell
docker run -it -v 主机目录:容器内目录 

# 测试
docker run -it -v /home/ceshi:/home centos /bin/bash

#启动后可以通过docker inspect 容器id的方式查看挂载情况，如下图
docker inspect 7637f76bee77
```

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20211128230405015.png" alt="image-20211128230405015" style="zoom:67%;" />

```shell
# 可以再进入容器然后创建java.txt文件，然后查看本地目录中是否出现java.txt文件
```

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20211128231321922.png" alt="image-20211128231321922" style="zoom: 67%;" />

```shell
# 反向操作一下，在本地修改java.txt文件，然后可以看到容器里的文件也修改了，即使这个容器已经停止运行了！！！

# 1、停止容器
docker stop 容器id

# 2、修改本地文件java.txt

# 3、重启容器，查看容器内文件时候修改
docker restart 容器id
docker attach 容器id
```

好处：我们以后只需要修改本地文件即可，容器内会自动同步。

### 5.3 实战：安装MySQL

思考：MySQL都需要考虑容器的数据持久化的问题

```shell
# 获取镜像
docker pull mysql:5.7

# 运行容器，需要数据挂载，这里要注意一下，安装启动MySQL，需要配置密码，这是需要特别注意的！！！！！
# 官方的命令
docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag
# 我们的命令
-d				# 后台运行
-p				# 端口映射
-v				# 卷挂载
-e				# 环境配置，这里是用来配置MySQL密码
--name		# 容器名字:TAG
docker run -d --name mysql01 -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root mysql:5.7

# 启动成功之后，本地使用Navicat来接测试一下
# Navicat可以链接到服务器的3310--3310和容器内的3306映射，这个时候我们可以连接上

# 在本地测试创建一个数据库，查看映射路径是不是可以看到
```

假设将容器删除

```shell
docker rm -f mysql01

# 然后看本地的数据卷依旧没有丢失，这就实现了容器数据持久化功能！
```

### 5.4 具名挂载和匿名挂载

#### 匿名挂载

```shell
# 匿名挂载
-v 容器内路径
docker run -d -P --name nginx -v /etc/nginx nginx
		-P 	# 	将所有暴露的端口发布到随机端口

# 使用docker volume ls查看所有volume的情况，这里就是匿名挂载，这是因为没有指定本地挂载的位置
docker volume ls
# 发现这就是匿名挂载，我们在-v 只写了容器内的路径，没有写容器外的路径
DRIVER    VOLUME NAME
local     0e020138b64967ca1d8b13ab3e6d0b671568f9334166db5ecf6a0d7fbc26f66e
local     55a0ed73f91358ef458c902d37a67af00ded7254c7f8e1a5be9c4e25cf88d141
```

#### 具名挂载

```shell
# 具名挂载
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx ngin

# 通过 -v 卷名:容器内路径，进行具名挂载
# 查看一下卷的情况
docker volume ls
```

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20211129113634054.png" alt="image-20211129113634054" style="zoom:67%;" />

```shell
# 查看juming-nginx的存放路径，可以用命令docker volume inspect 卷名
docker volume inspect juming-nginx
# 查看到被挂载都本地的地址如下所示
```

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20211129114034017.png" alt="image-20211129114034017" style="zoom:67%;" />

所有的docker容器内的卷，没有指定目录的情况下都是在'var/lib/docker/volume/xxxxx/_data'的路径下

我们通过具名挂载可以方便的找到我们的一个卷，大多数情况都是使用**具名挂载**，不建议使用匿名挂载。

#### 如何确定挂载方式

```shell
# 如何确定是具名挂载还是匿名挂载还是指定路径挂载
-v 容器内路径			# 匿名挂载
-v 卷名:容器内路径		# 具名挂载
-v /宿主机路径:容器内路径		# 指定路径挂载
```

#### 拓展

```shell
# 通过 -v 容器内路径:ro或者rw改变读写权限
ro readonly # 只读
rw readwrite # 可读可写

# 一旦设定了容器的权限，容器对我们挂载出来的内容就有限定了
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:ro nginx
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:rw nginx

# ro 只要看到ro就说明这个路径只能通过宿主机来操作，容器内部是无法操作的！
```

### 5.6 初识DockerFile

Docker就是用来构建docker镜像的构建文件，实际上就是一段命令脚本

通过脚本可以生成镜像，镜像是一层一层的，脚本就是一个个的命令，每一个命令都是一层

#### 方式二：

```shell
# 创建一个dockerfile文件，名字可以随机，建议dockerfile，文件内容如下
# 这里的每个命令就是镜像的一层
FROM centos
VOLUME ["volume01","volum02"]
CMD echo "---end---"
CMD /bin/bash

# 使用docker build创建
docker build -f dockerfile1 -t dage/centos:1.1.0 .
	-f 文件		# 指定dockerfile文件
	-t 名字:TAG		# 指定名字和tag
```

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20211129120907071.png" alt="image-20211129120907071" style="zoom:67%;" />

```shell
# 启动容器，可以看到目录中有volume01和02两个目录，这两个卷和外部一定有一个同步的目录
```

![image-20211129121823868](/Users/mac/Library/Application Support/typora-user-images/image-20211129121823868.png)

```shell
# 从dockerfile中看到这两个卷是匿名挂载
```

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20211129125130518.png" alt="image-20211129125130518" style="zoom:67%;" />

```shell
# 查看匿名挂载的路径
docker inspect 容器id
# 实例
docker inspect 75795487d835
```

![image-20211129125524729](/Users/mac/Library/Application Support/typora-user-images/image-20211129125524729.png)

```shell
# 1、在容器中的volume02中创建一个java.txt文件
# 2、退出容器
# 3、cd 到本地的映射目录，可以看到在本地已经出现了这个文件了
```

![image-20211129141248735](/Users/mac/Library/Application Support/typora-user-images/image-20211129141248735.png)

这种个方式我们未来使用的十分多，因为我们通常会构建自己的镜像。

假设构建镜像时没有挂载卷，要手动镜像挂载 -v 卷名:容器内路径。

### 5.7 数据卷容器-容器跟容器同步

多个MySQL同步数据。

![image-20211129143041234](/Users/mac/Library/Application Support/typora-user-images/image-20211129143041234.png)

```shell
# 启动3个容器，通过我们刚才我们自己写的镜像启动,命令是--volumes-from
# 先启动一个容器
```

![image-20211129142816330](/Users/mac/Library/Application Support/typora-user-images/image-20211129142816330.png)

```shell
# 创建docker02并将其与docker01同步起来
docker run -it --name docker02 --volumes-from docker01 47abebf3e5cf
# 然后退出docker02
# 进入docker01 
docker attach 0a7f29c4d4c1		# 这里注意下0a7f29c4d4c1这是容器id，先用docker ps命令查一下
# 在docker01中创建一个java.txt文件，然后去docker02看看对用目录下是否出现java.txt
```

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20211129145613292.png" alt="image-20211129145613292" style="zoom: 50%;" />

如果把容器docker01删除掉的话docker02和docker03还能不能数据共享呢？

答案是可以。

```shell
# 1、删除docker01
docker rm -f docker01

# 2、进入docker02
docker attach docker02

# 3、在volume01下新建一个python.txt,然后退出docker02

# 4、进入docker03查看是不是有python.txt文件
docker attach docker03
```

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20211129151257575.png" alt="image-20211129151257575" style="zoom: 50%;" />

#### 多个MySQL实现数据共享

```shell
docker run -d --name mysql01 -p 3310:3306 -v /etc/mysql/conf.d -v /var/lib/mysql -e MYSQL_ROOT_PASSWORD=root mysql:5.7

docker run -d --name mysql02 -p 3310:3306 --volumes-from mysql01 -e MYSQL_ROOT_PASSWORD=root mysql:5.7

# 这样两个MySQL实例就可以数据同步了
```

#### 结论：

容器之间配置信息的传递，数据卷容器的生命周期一直持续到没有容器位置。

但是一旦你持久化到了本地，这个时候，本地的数据是不会删除的。

## 六、DockerFile

### 6.1 DockerFiles说明

dockerfile是用来构建docker镜像的文件！实际上就是一段命令脚本。

构建步骤

1、编写一个dockerfile文件

2、docker build 构建成为一个容器

3、docker run 运行镜像

4、docker push 发布镜像(DockerHub、阿里云镜像仓库)

查看一下官方是怎么做的

![image-20211129153036769](/Users/mac/Library/Application Support/typora-user-images/image-20211129153036769.png)

点击红框中的版本，跳转到了github

![image-20211129153201153](/Users/mac/Library/Application Support/typora-user-images/image-20211129153201153.png)

可以看到这也是个dockerfile

很多官方的镜像都是基础包，很多功能都没有，我们通常会自己搭建自己的镜像！

官方既然可以制作镜像，那么我们也可以

### 6.2 DokcerFile构建过程

**基础知识：**

1、每个保留关键字(指令)都是必须是大写字母

2、从上到下顺序执行

3、# 表示注释

4、每一个指令都会创建提交一个新的镜像层，并提交。

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg-blog.csdnimg.cn%2F20210216133417109.png%3Fx-oss-process%3Dimage%2Fwatermark%2Ctype_ZmFuZ3poZW5naGVpdGk%2Cshadow_10%2Ctext_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d1bGVpMjkyMTYyNTk1Nw%3D%3D%2Csize_16%2Ccolor_FFFFFF%2Ct_70&refer=http%3A%2F%2Fimg-blog.csdnimg.cn&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1640764027&t=2dc18ca60e3f3e1394328548a5d4ce33" alt="img" style="zoom:50%;" />

dokcerfile是面向开发的，我们以后要发布项目，做镜像，就需要编写dockerfile文件，这个文件十分简单。

Docker镜像逐渐成为企业交付的标准，必须掌握。

步骤：开发，部署，运维。。。无一不可

DcokerFile：构建文件，定义了一切的步骤，源代码。

DockerImages：通过DockerFile构建生成的镜像，最终发布和运行的产品，以前的是war，jar。

Docker容器：容器就是镜像运行起来提供服务的服务器。

### 6.3 DockerFile的指令

```shell
FROM						# 基础镜像，一切从这里开始构建
MAINTAINER			# 镜像的维护者信息，姓名+邮箱
RUN							# 镜像构建的时候需要运行的命令
ADD							# 步骤，tomcat镜像，这个tomcat压缩包，添加内容
WORKDI					# 镜像的工作目录
VOLUME					# 挂载的目录
EXPOSE					# 保留端口配置
CMD							# 指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT			# 指定这个容器启动的时候要运行的命令，可以追加命令
ONBUILD					# 当构建一个被继承 DockerFile 这个时候就会运行ONBUILD的指令，触发指令
COPY						# 类似ADD，将我们文件拷贝到镜像中
EYV							# 构建的时候设置环境变量
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190930150525311.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzQ1NjU5OA==,size_16,color_FFFFFF,t_70)

#### 实战测试

DockerHub中99%的镜像都是从scratch这个基础镜像开始的，然后配置需要的软件和配置来进行构建

##### 创建一个自己的centos

```shell
# 1、编写自己的dockerfile-mycentos
[root@iZ2zeckvbpoomb2mjqv2auZ dockerfile]# cat mycentos
FROM centos

MAINTAINER PeetaJi<954457496@qq.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum install -y vim
RUN yum install -y net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "---end---"
CMD /bin/bash

# 2、通过这个文件构建镜像,注意 . 不能省
docker build -f mycentos -t mycentos:0.1 .
```

可以看到我们自己创建的centos镜像，工作目录已经改变，ifconfig和vim等命令也也有了

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20211130095648389.png" alt="image-20211130095648389" style="zoom:67%;" />

还可以列出本地镜像的变更历史

```shell
#	查看镜像的变更历史命令
docker history 镜像id
```

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20211130100057562.png" alt="image-20211130100057562" style="zoom:67%;" />

#### CMD和ENTRYPOINT的区别

```shell
CMD							# 指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT			# 指定这个容器启动的时候要运行的命令，可以追加命令
```

##### 测试CMD

```shell
# 创建cmd测试文件
[root@dage dockerfile]# vim dockerfile-cmd-test
FROM centos

CMD ["ls","-a"]

#	构建镜像，别忘记最后的 "." 
docker build -f dockerfile-cmd-test  -t cmdtest .

# run运行，发现ls -a命令生效
[root@iZ2zeckvbpoomb2mjqv2auZ dockerfile]# docker run 5858471a0777
.
..
.dockerenv
bin
dev
etc
home
lib
lib64
lost+found
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var

# 想追加一个命令-l 变成ls -al
[root@iZ2zeckvbpoomb2mjqv2auZ dockerfile]# docker run 5858471a0777 -l
docker: Error response from daemon: OCI runtime create failed: container_linux.go:380: starting container process caused: exec: "-l": executable file not found in $PATH: unknown.
ERRO[0000] error waiting for container: context canceled
# CMD的情况下，-l替换了CMD["ls","-a"]命令，-l不是命令，所以报错

# 如果想让它不报错就要在最后添加ls -al,就不报错了
[root@iZ2zeckvbpoomb2mjqv2auZ dockerfile]# docker run 5858471a0777 ls -al
total 0
drwxr-xr-x   1 root root   6 Nov 30 02:48 .
drwxr-xr-x   1 root root   6 Nov 30 02:48 ..
-rwxr-xr-x   1 root root   0 Nov 30 02:48 .dockerenv
lrwxrwxrwx   1 root root   7 Nov  3  2020 bin -> usr/bin
drwxr-xr-x   5 root root 340 Nov 30 02:48 dev
drwxr-xr-x   1 root root  66 Nov 30 02:48 etc
drwxr-xr-x   2 root root   6 Nov  3  2020 home
lrwxrwxrwx   1 root root   7 Nov  3  2020 lib -> usr/lib
lrwxrwxrwx   1 root root   9 Nov  3  2020 lib64 -> usr/lib64
drwx------   2 root root   6 Sep 15 14:17 lost+found
drwxr-xr-x   2 root root   6 Nov  3  2020 media
drwxr-xr-x   2 root root   6 Nov  3  2020 mnt
drwxr-xr-x   2 root root   6 Nov  3  2020 opt
dr-xr-xr-x 119 root root   0 Nov 30 02:48 proc
dr-xr-x---   2 root root 162 Sep 15 14:17 root
drwxr-xr-x  11 root root 163 Sep 15 14:17 run
lrwxrwxrwx   1 root root   8 Nov  3  2020 sbin -> usr/sbin
drwxr-xr-x   2 root root   6 Nov  3  2020 srv
dr-xr-xr-x  13 root root   0 Nov 30 02:48 sys
drwxrwxrwt   7 root root 171 Sep 15 14:17 tmp
drwxr-xr-x  12 root root 144 Sep 15 14:17 usr
drwxr-xr-x  20 root root 262 Sep 15 14:17 var
```

##### 测试ENTRYPOINT

```shell
# 创建dockerfile文件
[root@iZ2zeckvbpoomb2mjqv2auZ dockerfile]# vim dockerfile-cmd-entrypoint
FROM centos
ENTRYPOINT ["ls","-a"]

# 构建镜像，别忘记后面的."
[root@iZ2zeckvbpoomb2mjqv2auZ dockerfile]# docker build -f dockerfile-cmd-entrypoint -t entrytest .
Sending build context to Docker daemon  4.096kB
Step 1/2 : FROM centos
 ---> 5d0da3dc9764
Step 2/2 : ENTRYPOINT ["ls","-a"]
 ---> Running in 3d4d6b4122a9
Removing intermediate container 3d4d6b4122a9
 ---> bc468a2d3eb8
Successfully built bc468a2d3eb8
Successfully tagged entrytest:latest
# 查看images
[root@iZ2zeckvbpoomb2mjqv2auZ dockerfile]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
entrytest    latest    bc468a2d3eb8   6 seconds ago    231MB
cmdtest      latest    5858471a0777   29 minutes ago   231MB
mycentos     0.1       9b3efdd1aa20   15 hours ago     322MB
redis        latest    40c68ed3a4d2   12 days ago      113MB
nginx        latest    ea335eea17ab   12 days ago      141MB
mysql        5.7       8b43c6af2ad0   12 days ago      448MB
centos       latest    5d0da3dc9764   2 months ago     231MB
# 运行试一下
[root@iZ2zeckvbpoomb2mjqv2auZ dockerfile]# docker run bc468a2d3eb8
.
..
.dockerenv
bin
dev
etc
home
lib
lib64
lost+found
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
# 在后面追加-l，看看情况
[root@iZ2zeckvbpoomb2mjqv2auZ dockerfile]# docker run bc468a2d3eb8 -l
total 0
drwxr-xr-x   1 root root   6 Nov 30 02:54 .
drwxr-xr-x   1 root root   6 Nov 30 02:54 ..
-rwxr-xr-x   1 root root   0 Nov 30 02:54 .dockerenv
lrwxrwxrwx   1 root root   7 Nov  3  2020 bin -> usr/bin
drwxr-xr-x   5 root root 340 Nov 30 02:54 dev
drwxr-xr-x   1 root root  66 Nov 30 02:54 etc
drwxr-xr-x   2 root root   6 Nov  3  2020 home
lrwxrwxrwx   1 root root   7 Nov  3  2020 lib -> usr/lib
lrwxrwxrwx   1 root root   9 Nov  3  2020 lib64 -> usr/lib64
drwx------   2 root root   6 Sep 15 14:17 lost+found
drwxr-xr-x   2 root root   6 Nov  3  2020 media
drwxr-xr-x   2 root root   6 Nov  3  2020 mnt
drwxr-xr-x   2 root root   6 Nov  3  2020 opt
dr-xr-xr-x 120 root root   0 Nov 30 02:54 proc
dr-xr-x---   2 root root 162 Sep 15 14:17 root
drwxr-xr-x  11 root root 163 Sep 15 14:17 run
lrwxrwxrwx   1 root root   8 Nov  3  2020 sbin -> usr/sbin
drwxr-xr-x   2 root root   6 Nov  3  2020 srv
dr-xr-xr-x  13 root root   0 Nov 30 02:54 sys
drwxrwxrwt   7 root root 171 Sep 15 14:17 tmp
drwxr-xr-x  12 root root 144 Sep 15 14:17 usr
drwxr-xr-x  20 root root 262 Sep 15 14:17 var
```

DockerFile中许多命令都十分的相似，了解它们区别的最好的方式就是测试一下。

### 6.4 实战:Tomcat镜像

1、准备tomcat压缩包，jdk的压缩包



2、编写DockerFIle文件，命名方式“DockerFile”，build的时候会自动寻找这个文件，不需要加-f了。

```shell
# 1、编写DockerFile文件,创建touchme.txt
FROM centos
MAINTAINER dage<954457496@qq.com>

COPY readme.txt /usr/local/readme.txt

ADD jdk-8u161-linux-x64.tar.gz /usr/local
ADD apache-tomcat-9.0.22.tar.gz /usr/local

RUN yum -y install vim

ENV MYPATH /usr/local
WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8.0_161
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.22
ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.22
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080

CMD /usr/local/apache-tomcat-9.0.22/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.22/bin/logs/catalina.out

# tail -F 文件名 # tail命令启动时，文件不可访问或者文件稍后变得不接访问，都始终尝试打开文件

# 2、构建构建自己的tomcat
docker build -t mytomcat .

# 3、启动tomcat，访问测试
docker run -d -p 9090:8080 --name 01 -v /home/dashixin/build/tomcat/test:/usr/local/apache-tomcat-9.0.52/webapps/test -v /home/dashixin/build/tomcat/tomcatlogs/:/usr/local/apache-tomcat-9.0.52/logs mytomcat

# 4、进入tomcat
docker exec -it tomcat01 /bin/bash

# 5、退出，ping localhost:9090
# 使用公网ip访问测试，别忘记开阿里云的防火墙

# 6、发布项目（由于做了卷挂载，我们直接在本地编写项目就可以发布了！）

# 7、创建xml文件
mkdir WEB-INF
vim web.xml

<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://java.sun.com/xml/ns/javaee"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
version="2.5">

</web-app>
# 8、创建jsp文件
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>ls-a</title>
</head>
<body>
Hello World!<br/>
<%
System.out.println("---end---");
%>
</body>
</html>
```

### 6.5 发布自己的镜像

Docker Hub

1、Docker Hub地址:https://registry.hub.docker.com/d，注册自己的账号

2、确定账号可以登陆

3、在我们的服务器上提交自己的镜像

```shell
[root@iZ2zeckvbpoomb2mjqv2auZ ~]# docker login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
```

4、登录

```shell
[root@iZ2zeckvbpoomb2mjqv2auZ ~]# docker login -u dockermowang
Password:
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

5、登录完毕就可以提交自己的镜像了，**注意**：镜像名必须是“用户名/镜像名”,尽量带上版本号

```shell
#	创建tag
docker tag cec2185cca79 dockermowang/mytomcat:1.0
# push到仓库
docker push dockermowang/mytomcat:1.0
```

提交的时候也是按照镜像的层级来进行提交的

#### 阿里云镜像发布

1、登录阿里云

2、找到容器镜像服务

3、创建命名空间

![image-20211201111712357](/Users/mac/Library/Application Support/typora-user-images/image-20211201111712357.png)

4、创建镜像仓库

![image-20211201111737923](/Users/mac/Library/Application Support/typora-user-images/image-20211201111737923.png)

5、浏览阿里云，操作指南

![image-20211201111843501](/Users/mac/Library/Application Support/typora-user-images/image-20211201111843501.png)

### 小结

![img](https://img-blog.csdnimg.cn/20200913222649722.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JhaWR1XzQxMzg4NTMz,size_16,color_FFFFFF,t_70)

## 七、Docker网络（容器编排，）

### 7.1 理解Docker0

![image-20211201121923611](/Users/mac/Library/Application Support/typora-user-images/image-20211201121923611.png)

#### 思考：docker是如何处理容器网络访问的

```shell
# 启动一个tomcat
[root@iZ2zeckvbpoomb2mjqv2auZ ~]# docker run -d -P --name tomcat01 tomcat

# 查看容器的内部网络地址
docker exec -it tomcat01 ip addr
# 报错 OCI runtime exec failed: exec failed: container_linux.go:380: starting container process caused: exec: "ip": executable file not found in $PATH: unknown
# 解决这个问题,进入容器，执行两行命令
apt update
apt install -y iproute2

# 接下来就可以了
[root@iZ2zeckvbpoomb2mjqv2auZ ~]# docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
74: eth0@if75: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever

# ping一下docker的地址
# 如果ping不了 # apt install iputils-ping
[root@iZ2zeckvbpoomb2mjqv2auZ ~]# ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.048 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.042 ms

# linux可以ping通docker容器内部
```

#### 原理

1、我们每启动一个docker容器，docker就会给docker容器分配一个ip，我们只要安装了docker，就会有一个网卡docker0

桥接模式，使用的技术就是veth-pair技术

再次测试，**发现多了一个网卡**

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20211201124240859.png" alt="image-20211201124240859" style="zoom: 50%;" />

2、再启动一个容器测试，发现又多了一对网卡

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20211201124528139.png" alt="image-20211201124528139" style="zoom:50%;" />

我们发现这些容器带来的网卡都是一对一对的

veth-pair 就是一对的虚拟设备接口，他们都是成对出现，一段连着协议，一段彼此相连

正因为有这个特性，veth-pir充当一个桥梁，连接各种虚拟网络设备

OpenStac，Docker容器之间的连接，OVS的连接，都是使用veth-pair技术

3、我们来测试下，在tomcat02中ping tomcat01，看看容器之间是否可以ping通

```shell
# tomcat02 中没有ping命令，先安装
apt-get install inetutils-ping

# 然后ping tomcat02，就可以ping通了
[root@iZ2zeckvbpoomb2mjqv2auZ ~]# docker exec -it tomcat02 ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2): 56 data bytes
64 bytes from 172.17.0.2: icmp_seq=0 ttl=64 time=0.089 ms
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.066 ms
```

绘制网络模型图

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20211201140925394.png" alt="image-20211201140925394" style="zoom: 67%;" />

结论：tomcat01和tomcat02是公用一个路由器，docker0

所有的容器不指定网络的情况下，都是docker0路由的，docker会给我们的容器分配一个默认的可用ip

255.255.0.1/16

#### 小结

Docker使用的是linux桥接，宿主机中是一个Docker容器的网桥docker0。

![image-20211201141548583](/Users/mac/Library/Application Support/typora-user-images/image-20211201141548583.png)

Docker中的所有的网络接口都是虚拟的，虚拟的转发效率高（内网传递文件）

只要容器删除，对应网桥一对就没了

#### 思考

一个场景，我们编写了一个微服务，database url = ip，项目不重启，数据库ip换掉了，我们希望可以处理这个问题，可以用名字来来访问容器。

--link解决这个问题

在这里我们遇到了问题

```shell
# 创建tomcat03将它连接到tomcat02上
docker run -P -d --name tomcat03 --link tomcat02:tomcat02 tomcat
# 这里的--link问题可以看 https://www.jianshu.com/p/21d66ca6115e
# 也可以这样写
docker run -P -d --name tomcat03 --link tomcat02 tomcat

# 这里会出现问题，没有ping 命令，解决方式就是进入容器执行如下两行命令
apt update
apt install iputils-ping

# 执行ping
[root@iZ2zeckvbpoomb2mjqv2auZ ~]# docker exec -it tomcat03 ping tomcat02
PING tomcat02 (172.17.0.3) 56(84) bytes of data.
64 bytes from tomcat02 (172.17.0.3): icmp_seq=1 ttl=64 time=0.067 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=2 ttl=64 time=0.056 ms
# 注意这里只能是03 ping 02， 而02是ping不通03的
```

查看tomcat03的/etc/hosts文件可以看到tomcat02已经加进去了

![image-20211201172211003](/Users/mac/Library/Application Support/typora-user-images/image-20211201172211003.png)

而tomcat02中是没有tomcat03的配置的

![image-20211201172413129](/Users/mac/Library/Application Support/typora-user-images/image-20211201172413129.png)

Docker network的命令

```shell
# 查看docker内的网络
[root@iZ2zeckvbpoomb2mjqv2auZ ~]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
c644f117fa4d   bridge    bridge    local
c9cd926f2da6   host      host      local
edd2e068bf1e   none      null      local
# 查看网络的具体情况
[root@iZ2zeckvbpoomb2mjqv2auZ ~]# docker network inspect c644f117fa4d
```

本质：--link就是在我们在hosts配置中增加了一个172.17.0.3	tomcat02 ed3ca43b667e

我们现在玩docker已经不建议使用--link了

自定义网络！不适用dokcer0

docker0问题：不支持容器名连接访问

### 7.2 自定义网络

查看所有的docker网络

![image-20211201174505575](/Users/mac/Library/Application Support/typora-user-images/image-20211201174505575.png)



bridge：桥接，docker（默认）

none：不配置网络

host：和宿主机共享网络

container：容器网络连通（用的少，局限很大）

```shell
# 以前的方式
docker run -d -P --name tomcat01 tomcat
# 实际上是带默认参数--net bridge的，这个就是docker0
docker run -d -P --name tomcat01 --net bridge tomcat

# docker0特点，默认，域名不可访问，--link可以打通连接

# 我们可以自定义一个网络
# --driver bridge
# --subnet 192.168.0.0/16		192.168.0.2 - 192.168.255.255
# --gateway 192.168.0.1
docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet

[root@iZ2zeckvbpoomb2mjqv2auZ ~]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
c644f117fa4d   bridge    bridge    local
c9cd926f2da6   host      host      local
c1d24aee23eb   mynet     bridge    local
edd2e068bf1e   none      null      local
```

![image-20211201201246479](/Users/mac/Library/Application Support/typora-user-images/image-20211201201246479.png)

```shell
# 在ping之前先保证能执行ping命令，进入容器里安装
apt update
apt install iputils-ping

# 再次测试ping连接，现在可以不使用--link也可以ping名字了
[root@iZ2zeckvbpoomb2mjqv2auZ ~]# docker exec -it tomcat-net01 ping tomcat-net02
PING tomcat-net02 (192.168.0.3) 56(84) bytes of data.
64 bytes from tomcat-net02.mynet (192.168.0.3): icmp_seq=1 ttl=64 time=0.076 ms
64 bytes from tomcat-net02.mynet (192.168.0.3): icmp_seq=2 ttl=64 time=0.074 ms
64 bytes from tomcat-net02.mynet (192.168.0.3): icmp_seq=3 ttl=64 time=0.064 ms
```

我们自定义的网络docker都已经帮我们维护好了对应的关系，**推荐**平时这种方式使用网络



好处：

redis：不同集群使用不同的网络，保证集群是安全和健康的

MySQL：不同集群使用不同的网络，保证集群是安全和健康的

#### 网络连通

再次创建两个tomcat

![image-20211201203127653](/Users/mac/Library/Application Support/typora-user-images/image-20211201203127653.png)

测试打通tomcat和tomcat-net的

```shell
# tomcat01连接到mynet上
[root@iZ2zeckvbpoomb2mjqv2auZ ~]# docker network connect mynet tomcat01
[root@iZ2zeckvbpoomb2mjqv2auZ ~]# docker network inspect mynet
# 连通之后就把tomcat01和tomcat-net01和tomcat-net02就连通了
```

看到的mynet的详细信息中，增加了tomcat01：

![image-20211201230351827](/Users/mac/Library/Application Support/typora-user-images/image-20211201230351827.png)

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20211201230551794.png" alt="image-20211201230551794" style="zoom:67%;" />

**注意：**这里连通的是**mynet**和**tomcat01**

```shell
# 这里就可以 ping 通 tomcat01 了 
[root@iZ2zeckvbpoomb2mjqv2auZ ~]# docker exec -it tomcat-net01 ping tomcat01
PING tomcat01 (192.168.0.4) 56(84) bytes of data.
64 bytes from tomcat01.mynet (192.168.0.4): icmp_seq=1 ttl=64 time=0.097 ms
64 bytes from tomcat01.mynet (192.168.0.4): icmp_seq=2 ttl=64 time=0.062 ms

# tomcat02是ping不通的
[root@iZ2zeckvbpoomb2mjqv2auZ ~]# docker exec -it tomcat-net01 ping tomcat02
ping: tomcat02: Name or service not known

# 将tomcat02加入到mynet中就可以ping通了
```

结论：假设要跨网操作别人，就需要使用docker network coonect 连通

### 7.3 实战：部署Redis集群

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20211201231525425.png" alt="image-20211201231525425" style="zoom:67%;" />

```shell
# 创建network
docker network create redis --subnet 172.38.0.0/16

# 通过shell脚本创建六个redis配置
for port in $(seq 1 6); \
do \
mkdir -p /mydata/redis/node-${port}/conf
touch /mydata/redis/node-${port}/conf/redis.conf
cat << EOF >/mydata/redis/node-${port}/conf/redis.conf
port 6379
bind 0.0.0.0
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 172.38.0.1${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
appendonly yes
EOF
done


docker run -p 637${port}:6379 -p 1637${port}:16379 --name redis-${port} \
-v /mydata/redis/node-${port}/data:/data \
-v /mydata/redis/node-${port}/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.1${port} redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf; \

# 生成redis实例
docker run -p 6376:6379 -p 16376:16379 --name redis-6 \
-v /mydata/redis/node-6/data:/data \
-v /mydata/redis/node-6/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.16 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

# 进入其中一个容器
[root@iZ2zeckvbpoomb2mjqv2auZ conf]# docker exec -it redis-1 /bin/sh

# 执行创建集群命令
/data # redis-cli --cluster create 172.38.0.11:6379 172.38.0.12:6379 172.38.0.13:6379 172.38.0.14:6379 172.38.0.15:6379 172.38.0.16:6379 --cluster-replicas 1

>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 172.38.0.15:6379 to 172.38.0.11:6379
Adding replica 172.38.0.16:6379 to 172.38.0.12:6379
Adding replica 172.38.0.14:6379 to 172.38.0.13:6379
M: 2054289eea1523078a131f626d42973e594541d5 172.38.0.11:6379
   slots:[0-5460] (5461 slots) master
M: 3604cae931a23b88655a60cb3d4f13a069f564e9 172.38.0.12:6379
   slots:[5461-10922] (5462 slots) master
M: 8c6807a62a37a362f1681f1a76a29de2cc7bbe1b 172.38.0.13:6379
   slots:[10923-16383] (5461 slots) master
S: 1771c1db29312a1fda86b5abe0ea2fb105ecf85c 172.38.0.14:6379
   replicates 8c6807a62a37a362f1681f1a76a29de2cc7bbe1b
S: d36e5b59a28c6ed2ae5e0cb9e2e1ce696216de8f 172.38.0.15:6379
   replicates 2054289eea1523078a131f626d42973e594541d5
S: 9d7ed7af9e11ed79abc51ce1c50c532d118138ce 172.38.0.16:6379
   replicates 3604cae931a23b88655a60cb3d4f13a069f564e9
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
..
>>> Performing Cluster Check (using node 172.38.0.11:6379)
M: 2054289eea1523078a131f626d42973e594541d5 172.38.0.11:6379
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
S: 9d7ed7af9e11ed79abc51ce1c50c532d118138ce 172.38.0.16:6379
   slots: (0 slots) slave
   replicates 3604cae931a23b88655a60cb3d4f13a069f564e9
S: 1771c1db29312a1fda86b5abe0ea2fb105ecf85c 172.38.0.14:6379
   slots: (0 slots) slave
   replicates 8c6807a62a37a362f1681f1a76a29de2cc7bbe1b
M: 3604cae931a23b88655a60cb3d4f13a069f564e9 172.38.0.12:6379
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
M: 8c6807a62a37a362f1681f1a76a29de2cc7bbe1b 172.38.0.13:6379
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: d36e5b59a28c6ed2ae5e0cb9e2e1ce696216de8f 172.38.0.15:6379
   slots: (0 slots) slave
   replicates 2054289eea1523078a131f626d42973e594541d5
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

**进入redis-1中，设置set  a b 发现这个值存在redis-3，然后停掉redis-3 再取值，发现可以再redis-4中取到值，高可用，主从结构**

![image-20211202142851175](/Users/mac/Library/Application Support/typora-user-images/image-20211202142851175.png)

docker搭建redis集群真的是简单！这就是docker的魅力！

### 7.4 SpringBoot微服务打包Docker镜像

1、构建springboot项目

2、打包应用

3、编写DockerFile

4、构建镜像

5、发布运行

以后发布直接给一个镜像就可以了



## 八、Docker Compose

### 8.1 简介

DockerFile build run 手动操作，单个容器

100微服务，手动启停的话就累死了

Docker Compose来轻松高效的管理容器，定于运行多个容器

看看官方定义：https://docs.docker.com/compose/

定义运行多个容器

YAML file配置文件

single command 命令有哪些

三步骤：

1、DockerFile保证我们的项目在任何地方都可以运行

2、service什么是服务，docker-compos.yml这个文件怎么写

3、启动项目

作用：批量容器编排

Compose是Docker官方的开源项目。需要安装

DockerFile让程序在任何地方运行。web服务，redis，mysql、nginx。。多个容器。run

Compose

```yaml
version: "3.9"  # optional since v1.27.0
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/code
      - logvolume01:/var/log
    links:
      - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```

Docker-compose up 100个服务,一键上线

Compose：重要概念

​	服务service，容器，应用（web，redis，mysql）

​	项目project，一组关联的容器，博客，web，MySQL



### 8.2 安装docker-compose

从官网直接按步骤安装：https://docs.docker.com/compose/install/

安装完毕后使用 docker-compose version来看安装成功了没有

### 8.3 体验docker-compose

去官网看：https://docs.docker.com/compose/gettingstarted/

1、应用 app.py

2、Dockerfile 应用打包成镜像

3、Docker-compose yaml文件（定义整个服务，需要的环境。web、redis）完整的上线服务

4、启动compose项目

流程：

1、创建网络

2、执行Docker-Compose yaml文件

3、

自动的默认规则

![image-20211203110505293](/Users/mac/Library/Application Support/typora-user-images/image-20211203110505293.png)

Docker-compose文件中的相关的镜像都创建了

![image-20211203110622574](/Users/mac/Library/Application Support/typora-user-images/image-20211203110622574.png)

```shell
# 没有服务
[root@iZ2zeckvbpoomb2mjqv2auZ ~]# docker service ls
Error response from daemon: This node is not a swarm manager. Use "docker swarm init" or "docker swarm join" to connect this node to swarm and try again.
```

默认的服务名:文件名_服务名 _ num

多个服务器。集群。A B _num 副本数量

redis服务=>4个副本

集群状态，服务都不可能只有一个运行实例。弹性、10 高可用，高并发

kubectl service负载均衡

3、网络规则

![image-20211203111204592](/Users/mac/Library/Application Support/typora-user-images/image-20211203111204592.png)

项目中的内容都在同个网路下。域名访问

mysql：3306

10个容器实例

```shell
docker network inspect composetest_default
```

![image-20211203111408010](/Users/mac/Library/Application Support/typora-user-images/image-20211203111408010.png)

如果在同一个网络下，我们可以直接通过域名访问

停止：docker-compse down 和 ctrl+c，第一个必须到指定文件目录下，ctrl+c必须在服务窗口

![image-20211203112442486](/Users/mac/Library/Application Support/typora-user-images/image-20211203112442486.png)

Docker-compose

以前都是单个docker run启动容器

docker-compose。通过docker-compose编写yaml配置文件、可以通过compose一键启动，停止所有服务。

## 九、docker小结

1、Docker镜像。run->容器

2、DockerFile构建镜像（服务打包）

3、docker-compose启动项目（编排、多个微服务、环境）

4、Docker网络

### ymal规则

docker-compose.yaml 核心。

```yaml
# 3层

version: '' # 版本
service: 		# 服务
	服务1: web
		# 服务配置
		images
		build
		network
	服务2: redis
# 其他配置 网络/卷、全局规则
volumes:
network:
config:
```

学习，要掌握规律。

1、官方文档。

https://docs.docker.com/compose/compose-file/compose-file-v3/#aliases

2、开源项目 compose.yaml

Redis、mysql、mq！

### 开源项目

#### 博客

下载程序、安装数据库、配置。。。

compose应用 => 一键启动

1、下载项目（docker-compose.yaml）

2、如果需要文件。DockerFile

3、文件准备齐全（直接一键启动）





## 十、Docker Swarm

集群方式的部署、单机

集群的管理和编排：docker可以初始化一个swarm集群，其他节点可以加入。(管理、工作者)

**Node**

就是一个docker节点，多个节点就组成了一个网络集群

**service**

任务，可以在管理节点或者工作节点来运行，核心，用户访问

**Task**

<img src="https://img2020.cnblogs.com/blog/1444343/202108/1444343-20210818160853466-1726195621.png" alt="image" style="zoom:50%;" />

 <img src="https://img2018.cnblogs.com/blog/1479220/201810/1479220-20181020220312417-120406041.png" alt="img" style="zoom:67%;" />

<img src="https://img2018.cnblogs.com/blog/1479220/201810/1479220-20181020220303428-146351236.png" alt="img" style="zoom:67%;" />

容器内的命令

```shell
# 初始化节点
[root@iZ2zeckvbpoomb2mjqv2auZ ~]# docker swarm init --advertise-addr 172.25.45.166

# 生成token
docker swarm join-token manager

# 启动服务
docker service create -p 8080:80 --name nginx01 nginx

# 查看service列表
docker service ls

# 查看nginx01 service的docker
docker service ps nginx01

# 启动三个副本
docker service update --replicas 3 nginx01

# 回滚成1个
docker service update --replicas 1 nginx01

# 扩容命令scale
docker service scale nginx01=2d

# 移除service
docker service rm nginx01
```

## 十一、后续命令

RabbitMQ容器

创建容器并运行（15672是管理界面的端口，5672是服务的端口。这里顺便将管理系统的用户名和密码设置为admin admin）

```shell
docker run -dit --name Myrabbitmq -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin -p 15672:15672 -p 5672:5672 rabbitmq:management
```

docker搭建zipkin

```shell
docker run -d --restart always -p 9411:9411 --name zipkin openzipkin/zipkin
```

docker安装nacos

```shell
docker run --env MODE=standalone --name nacos -d -p 8848:8848 nacos/nacos-server
```

