# Jenkins

#### docker安装gitlab

```shell
docker run -d  -p 443:443 -p 80:80 -p 222:22 --name gitlab --restart always -v gitlab_config:/etc/gitlab -v gitlab_log:/var/log/gitlab -v gitlab_data:/var/opt/gitlab gitlab/gitlab-ce
```

下载jdk8

```shell
wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u141-b15/336fa29ff2bb4ef291e347e091f7f4a7/jdk-8u141-linux-x64.tar.gz"
```

配置环境变量

```shell
export JAVA_HOME=/usr/local/jdk1.8.0_181  #jdk安装目录,根据实际情况修改
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib:$CLASSPATH
export JAVA_PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin 
export PATH=$PATH:${JAVA_PATH}
```

第一步安装maven

1.切换到要安装的目录

```shell
cd /opt/software
```

2.下载maven包

```shell
 wget http://mirrors.aliyun.com/apache/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
```

3.解压

```shell
tar -xzvf apache-maven-3.6.3-bin.tar.gz
```

4.配置setting.xml

```xml
<mirror>
    <id>aliyunmaven</id>
    <mirrorOf>*</mirrorOf>
    <name>阿里云公共仓库</name>
    <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```

5.添加环境变量

```shell
vi /etc/profile
```

在底部添加

```shell
export M2_HOME=/opt/software/apache-maven-3.6.3                                                                                              
export PATH=$PATH:${M2_HOME}/bin
```

6、保存并退出编辑，使用一下命令让修改生效

```shell
source /etc/profile
```

7、验证maven安装

```shell
mvn -version
```

docker安装Jenkins

```shell
docker run \
  -u root \
  --name jenkins \
  -d \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins-data:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /opt/software/apache-maven-3.6.3:/usr/local/maven \
  -v /opt/software/jdk1.8.0_141:/usr/local/jdk1.8 \
  jenkinsci/blueocean
```

容器环境变量

```shell
export JAVA_HOME=/usr/local/jdk1.8
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib:$CLASSPATH
export JAVA_PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin
export PATH=$PATH:${JAVA_PATH}

export M2_HOME=/usr/local/maven
export PATH=$PATH:${M2_HOME}/bin
```

#### 安装后修改成国内镜像地址

参考博客：https://www.cnblogs.com/jack-jin/articles/12268484.html

查看jenkins挂载目录

```shell
docker volume inspect jenkins-data
```

然后cd到updates/default.json

修改镜像源

```shell
sed -i 's/http:\/\/updates.jenkins-ci.org\/download/https:\/\/mirrors.tuna.tsinghua.edu.cn\/jenkins/g' default.json && sed -i 's/http:\/\/www.google.com/https:\/\/www.baidu.com/g' default.json
```

然后到jenkins后台更换地址

```shell
https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json
```

```shell
FROM jenkinsci/blueocean
MAINTAINER dage<954457496@qq.com>


ENV MYPATH /usr/local
WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8
ENV JRE_HOME $JAVA_HOME/jre
ENV CLASSPATH $JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
ENV JAVA_PATH $JAVA_HOME/bin:$JRE_HOME/bin
ENV PATH=$PATH:$JAVA_PATH
ENV M2_HOME /usr/local/maven
ENV PATH $PATH:$M2_HOME/bin

EXPOSE 8080
EXPOSE 50000
CMD /bin/bash
```

安装tomcat

```shell
docker run -d -p 9090:8080 --name tomcat -v tomcat_apps:/usr/local/apache-tomcat-9.0.22/webapps -v tomcat_conf:/usr/local/apache-tomcat-9.0.22/conf -v tomcat_work:/usr/local/apache-tomcat-9.0.22/work mytomcat
```

修改tomcat配置添加用户

```xml
<tomcat-users>
   <role rolename="tomcat"/>
   <role rolename="role1"/>
   <role rolename="manager-script"/>
   <role rolename="manager-gui"/>
   <role rolename="manager-status"/>
   <role rolename="admin-gui"/>
   <role rolename="admin-script"/>
   <user username="tomcat" password="tomcat" roles="manager-gui,manager-script,tomcat,admin-gui,admin-script"/>
</tomcat-users>
```

先启动mysql

```shell
docker run -d --name mysql01 -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root mysql:5.7
```

在mysql中创建一个名字叫sonar的库

在安装sonar

```shell
docker run -d --name sonarqube -p 9000:9000 -p 9092:9092 -m 1g -v sq_logs:/opt/sonarqube/logs -v sq_conf:/opt/sonarqube/conf -v sq_data:/opt/sonarqube/data -v sq_extensions:/opt/sonarqube/extensions -e SONARQUBE_JDBC_USERNAME=root -e SONARQUBE_JDBC_PASSWORD=root -e SONARQUBE_JDBC_URL="jdbc:mysql://8.141.145.31:3310/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance&useSSL=false" sonarqube:7.4-community

docker volume rm -f $(docker volume ls | grep sq)
```

sonarqube秘钥：**c5b77a23b30c6c5fba26da1921d0d5700ed417bc**

maven命令

```shell
mvn sonar:sonar \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.login=c5b77a23b30c6c5fba26da1921d0d5700ed417bc
```

ssh远程调用

```shell
ssh-copy-id ip
```

