# K8s

token

```shell
eyJhbGciOiJSUzI1NiIsImtpZCI6IkRmN01LR1k5dzV6eDhFbVZUczg1aVQzVHdQRUs3ZTBva2YxVWlBcTYyM3MifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi10b2tlbi04cmJqYyIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJhZG1pbiIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjdiZTI1N2JmLWY0YjMtNDFmNC05ODAyLTNkY2M2YjQzMWZmNSIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlLXN5c3RlbTphZG1pbiJ9.r4X_9J7R-ns-WcOYQyXYkqztz_zrfLJOyVcnWW3DTzulHzQp--d5JGxMcfleitSQzEbttVbfDHyfYirNUUNQrW-c9RLyZU0QLMvb4VcZWwt9SgVExp6Kt9aiNG_eGBcgGQaFGRGGqlUnHbujMIxBIUm8s_TL-3e5ugmDSivyrCHo3LrX_Fwq-cXvjkrR_-_vBCHf-0VXIyChoKkynKgyAJvfhaWRgkDth63Pz8QgeF5J6GMqyXxBiwToQHrnGV9mcRB-nKlCOXAZoaLrOWqOLWdSLHdzyLCMZfG5TxKMbVV2eCrtUXWGaBS-2-Txd10XCUPwJGagWBu-_U5nSJMagA
```

### 2、组织架构

#### ![image-20220207142046588](/Users/mac/Library/Application Support/typora-user-images/image-20220207142046588.png)alias命令

```shell
alias node1='ssh root@39.107.79.142'

# 修改主机名
sudo hostnamectl set-hostname master

```

安装指定版本docker

```shell
yum install -y docker-ce-20.10.7 docker-ce-cli-20.10.7 containerd.io-1.4.6
```



#### 安装kubelet、kubeadm、kubectl

直接看网页无脑装：https://www.yuque.com/leifengyang/oncloud/ghnb83

#### 下载各个机器需要的镜像

需要在node节点注意一下

```shell
# node节点pull kube-proxy
docker pull registry.cn-hangzhou.aliyuncs.com/lfy_k8s_images/kube-proxy:v1.20.9
```

#### 初始化主节点

这里需要注意先看一下自己的ip

```shell
# 查看ip命令
ip a
```

注意：一定要是自己的ip

```shell
# 所有机器添加master域名映射，
# 以下需要修改为自己的,这里在三个节点都要运行
echo "172.31.0.172  cluster-endpoint" >> /etc/hosts



# 主节点初始化
# 只在主节点运行
# 这里二三行也要修改成master的ip地址和重命名
kubeadm init \
--apiserver-advertise-address=172.31.0.172 \
--control-plane-endpoint=cluster-endpoint \
--image-repository registry.cn-hangzhou.aliyuncs.com/lfy_k8s_images \
--kubernetes-version v1.20.9 \
--service-cidr=10.96.0.0/16 \
--pod-network-cidr=192.168.0.0/16

#所有网络范围不重叠

```

```shell
# master查看链接master的命令
kubeadm token create --print-join-command
# 生成token后，在worker节点执行一下上面的内容
```

```yaml
#创建访问账号，准备一个yaml文件； vi dash.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

获取访问令牌

```shell
kubectl -n kubernetes-dashboard get secret $(kubectl -n kubernetes-dashboard get sa/admin-user -o jsonpath="{.secrets[0].name}") -o go-template="{{.data.token | base64decode}}"
```

令牌

```shell
eyJhbGciOiJSUzI1NiIsImtpZCI6ImJzMllhZGIyTXRUcFZvcFhEVWdpQmxObmdyQW84ZkczNHhOTmF3YnpZb00ifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLWY1cWxrIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiJiYjY2MWE4Mi1lZWE4LTQxZTgtYjUzOS1jZjBlY2E0OTc1YmEiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZXJuZXRlcy1kYXNoYm9hcmQ6YWRtaW4tdXNlciJ9.BCdlA7jBurQypKiDtCTydMZsAqmBWfBwpHI1oDhlaqtxw8HlA7QOpqoQpYFjpxAZWaGdDe5Y1GWeHtz_dQFrAzh1tmDpH8haOhosx5XmpRC53X1tD14psHvyDQAEjEKk41sEQ0aENG5qo5WPtTQ4Surns8oHrTlW4J5SfklZ07BpaiNNRHncnkXz2Z4svQIL6EHklsP1XxzrRwU1ndQVsL6aNLEwqbKrIa6SN9VNPk_K6RzGQOV3gGs1onXKcbxn5OdzCeoiZOsEYuN9_HMYyNQ1cMdkYPe8tbNEQxadgR8TXIB6GC77jFqk_HOQwZH8eICmVtQJB7XXFsOJ1YzpNQ
```

## 核心实战

查看命名空间命令

```shell
# 想要获得资源文件的编写方式
kubectl explain +资源类型
# 例子
kubectl explain ReplicaSet

# 获得命名空间
kubectl get ns
kubectl get pod -A 

# 只找kubernetes-dashboard这个命名空间的pod
kubectl get pod -n kubernetes-dashboard

# 创建和删除命名空间
kubectl create ns hello
kubectl delete ns hello
```

```yaml
# 使用yaml创建命名空间
apiVersion: v1
kind: Namespace
metadata:
  name: hello
```

```shell
# 使用yaml创建命名空间的命令
kubectl apply -f hello.yaml

# 删除的方式
kubectl delete -f hello.yaml
```

创建pod等命令

```shell
# 创建一个nginx pod
kubectl run mynginx --image=nginx

# 查看default命名空间的pod
kubectl get pod
# 描述
kubectl describe pod
# 删除
kubectl delete pod Pod名字

# 删除default命名空间的所有pod
kubectl delete pod --all
# 查看pod日志
kubectl logs -f pod名字
# 查看某个容器的日志
kubectl logs pod名 -c 容器名称

# 每个Pod - k8s都会分配一个ip
kubectl get pod -owide
# 使用Pod的ip+pod里面运行容器的端口
curl 192.168.169.136

# 集群中的任意一个机器以及任意的应用都能通过Pod分配的ip来访问这个Pod


# 进入容器
kubectl exec -it pod名字 -- /bin/bash
```

yaml方式创建nginx

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: mynginx
  name: mynginx
spec:
  containers:
  - image: nginx
    name: mynginx
```

yaml方式部署多容器pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: myapp
  name: myapp
spec:
  containers:
  - image: nginx
    name: nginx
  - image: tomcat:8.5.68
    name: tomcat
```

deployment

```shell
# 清除所有Pod，比较下面两个命令有何不同效果？
kubectl run mynginx --image=nginx

kubectl create deployment mytomcat --image=tomcat:8.5.68
# 自愈能力
```

多副本

```shell
kubectl create deployment my-dep --image=nginx --replicas=3

# 查看部署的副本
kubectl get deploy

# 监控pod
watch -n 1 kubectl get pod
```

扩所容

```shell
# 扩容
kubectl scale deploy/my-dep --replicas=5
# 缩容
kubectl scale deploy/my-dep --replicas=2

kubectl edit deploment my-dep
```

service

```shell
#暴露Deploy
kubectl expose deployment my-dep --port=8000 --target-port=80

# 查看pod标签
kubectl get pod --show-labels

# 可以通过ip:端口的方式访问，也可以用域名:端口的方式实现
# 域名的格式是：服务名.所在命名空间.svc
# 这种方式只能在其他pod内
my-dep.default.svc:8000

#使用标签检索Pod
kubectl get pod -l app=my-dep


```

![image-20220208142616983](/Users/mac/Library/Application Support/typora-user-images/image-20220208142616983.png)

ingress:Service的统一网关入口

ingress的端口

![image-20220208151144275](/Users/mac/Library/Application Support/typora-user-images/image-20220208151144275.png)

存储对象

环境准备

所有节点

```shell
# 所有机器安装
yum install -y nfs-utils
```

主节点

```shell
#nfs主节点
echo "/nfs/data/ *(insecure,rw,sync,no_root_squash)" > /etc/exports

mkdir -p /nfs/data
systemctl enable rpcbind --now
systemctl enable nfs-server --now
#配置生效
exportfs -r
```

从节点

```shell
showmount -e 172.31.0.172

#执行以下命令挂载 nfs 服务器上的共享目录到本机路径 /root/nfsmount
mkdir -p /nfs/data

mount -t nfs 172.31.0.172:/nfs/data /nfs/data
# 写入一个测试文件
echo "hello nfs server" > /nfs/data/test.txt
```

原生方式数据挂载

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx-pv-demo
  name: nginx-pv-demo
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx-pv-demo
  template:
    metadata:
      labels:
        app: nginx-pv-demo
    spec:
      containers:
      - image: nginx
        name: nginx
        volumeMounts:
        - name: html
          mountPath: /usr/share/nginx/html
      volumes:
        - name: html
          nfs:
            server: 172.31.0.172
            path: /nfs/data/nginx-pv
```

pv&pvc

创建pv池

```shell
#nfs主节点
mkdir -p /nfs/data/01
mkdir -p /nfs/data/02
mkdir -p /nfs/data/03
```

创建PV

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv01-10m
spec:
  capacity:
    storage: 10M
  accessModes:
    - ReadWriteMany
  storageClassName: nfs
  nfs:
    path: /nfs/data/01
    server: 172.31.0.172
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv02-1gi
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  storageClassName: nfs
  nfs:
    path: /nfs/data/02
    server: 172.31.0.172
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv03-3gi
spec:
  capacity:
    storage: 3Gi
  accessModes:
    - ReadWriteMany
  storageClassName: nfs
  nfs:
    path: /nfs/data/03
    server: 172.31.0.172
```

pvc创建与绑定

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: nginx-pvc
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 200Mi
  storageClassName: nfs
```

创建pod绑定pvc

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx-deploy-pvc
  name: nginx-deploy-pvc
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx-deploy-pvc
  template:
    metadata:
      labels:
        app: nginx-deploy-pvc
    spec:
      containers:
      - image: nginx
        name: nginx
        volumeMounts:
        - name: html
          mountPath: /usr/share/nginx/html
      volumes:
        - name: html
          persistentVolumeClaim:
            claimName: nginx-pvc
```

ConfigMap

redis示例

把配置文件创建为配置集

```shell
# 创建配置，redis保存到k8s的etcd；
kubectl create cm redis-conf --from-file=redis.conf
```

也可以使用yaml方式创建cm

```yaml
apiVersion: v1
data:    #data是所有真正的数据，key：默认是文件名   value：配置文件的内容
  redis.conf: |
    appendonly yes
kind: ConfigMap
metadata:
  name: redis-conf
  namespace: default
```

创建pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: redis
spec:
  containers:
  - name: redis
    image: redis
    command:
      - redis-server
      - "/redis-master/redis.conf"  #指的是redis容器内部的位置
    ports:
    - containerPort: 6379
    volumeMounts:
    - mountPath: /data
      name: data
    - mountPath: /redis-master
      name: config
  volumes:
    - name: data
      emptyDir: {}
    - name: config
      configMap:
        name: redis-conf
        items:
        - key: redis.conf
          path: redis.conf
```

进入到redis pod中去查看redis.conf

```shell
lin# 修改cm
kubectl edit cm cm名字
kubectl edit cm redis-conf
```

```shell
# 查看pod的标签
kubectl get pod --show-labels
# 在node上打标签
kubectl label node node1 disk=ssd
# 修改标签
kubectl label pod +pod名字 +修改的地方 --overwrite=True 

# 扩缩容
kubectl scale deployment deployment名字 --replicas=10

# 修改deployment的image
kubectl set image deployment/nginx-deployment nginx=wangyanglinux/myapp:v2

# 回滚到上一个版本
kubectl rollout undo deployment/nginx-deployment
kubectl rollout undo deployment/nginx-deployment --to-revision=2 # 可以通过--torevision来指定某个历史版本

# 在创建是加上--record才可以看到版本历史
kubectl apply -f deployment.yaml --record

# 查看回滚状态
kubectl rollout status deployment/nginx-deployment

# 查看回滚历史
kubectl rollout history deployment/nginx-deployment

# 暂停回滚
kubectl rollout pause deployment/nginx-deployment

# 查看是不是回滚成功了可以用kubectl rollout status命令查看Deployment是否完成，如果rollout成功，kubectl rollout status会返回一个0值的Exit code
kubectl rollout status deploy/nginx-deployment
echo $?

# 查看ipvs的
ipvsadm -Ln

# 查看节点的labels
kubectl get node --show-labels

# 污点的设置、查看和去除
# 设置污点
kubectl taint nodes node1 key1=value1:NoSchedule

# 节点说明中查找taints字段
kubectl describe pod pod-name
# 删除污点
kubectl taint nodes node1 key1=:NoSchedule-
```

```shell
# 在进行k8s集群鉴权的过程中，需要在linux系统中创建用户，而k8s中是没有用户的管理
useradd devuser
passwd devuser
```





