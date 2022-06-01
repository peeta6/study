课程链接：https://www.bilibili.com/video/BV1w4411y7Go?p=19&spm_id_from=pageDriver

### 容器生命周期

![image-20220209162602017](/Users/mac/Library/Application Support/typora-user-images/image-20220209162602017.png)

![image-20220209162700239](/Users/mac/Library/Application Support/typora-user-images/image-20220209162700239.png)



![image-20220209162723105](/Users/mac/Library/Application Support/typora-user-images/image-20220209162723105.png)

#### 特殊说明

![image-20220209164225062](/Users/mac/Library/Application Support/typora-user-images/image-20220209164225062.png)

![image-20220209164316554](/Users/mac/Library/Application Support/typora-user-images/image-20220209164316554.png)

#### 容器探针

![image-20220209164547919](/Users/mac/Library/Application Support/typora-user-images/image-20220209164547919.png)

#### 探测方式

![ ](/Users/mac/Library/Application Support/typora-user-images/image-20220209164623598.png)

#### pod phase可能存在的值

![image-20220209170412640](/Users/mac/Library/Application Support/typora-user-images/image-20220209170412640.png)

### 控制器

![image-20220209170801003](/Users/mac/Library/Application Support/typora-user-images/image-20220209170801003.png)

![image-20220209171520757](/Users/mac/Library/Application Support/typora-user-images/image-20220209171520757.png)

deployment不直接管理pod而是通过rs来管理pod，如果要更新是县创建新的rs1然后创建一个删除一个

![image-20220209171509399](/Users/mac/Library/Application Support/typora-user-images/image-20220209171509399.png)

![image-20220209171945818](/Users/mac/Library/Application Support/typora-user-images/image-20220209171945818.png)

![image-20220209172247807](/Users/mac/Library/Application Support/typora-user-images/image-20220209172247807.png)

![image-20220209173247007](/Users/mac/Library/Application Support/typora-user-images/image-20220209173247007.png)

![image-20220209174508549](/Users/mac/Library/Application Support/typora-user-images/image-20220209174508549.png)

![image-20220209223622218](/Users/mac/Library/Application Support/typora-user-images/image-20220209223622218.png)

![image-20220209224303423](/Users/mac/Library/Application Support/typora-user-images/image-20220209224303423.png)

![image-20220209225006822](/Users/mac/Library/Application Support/typora-user-images/image-20220209225006822.png)

![image-20220209225143001](/Users/mac/Library/Application Support/typora-user-images/image-20220209225143001.png)

#### job和cronjob

![image-20220209225701746](/Users/mac/Library/Application Support/typora-user-images/image-20220209225701746.png)

![image-20220209225713540](/Users/mac/Library/Application Support/typora-user-images/image-20220209225713540.png)

![image-20220209230324123](/Users/mac/Library/Application Support/typora-user-images/image-20220209230324123.png)

![image-20220209230338046](/Users/mac/Library/Application Support/typora-user-images/image-20220209230338046.png)

![image-20220209230433254](/Users/mac/Library/Application Support/typora-user-images/image-20220209230433254.png)

![image-20220209230750349](/Users/mac/Library/Application Support/typora-user-images/image-20220209230750349.png)

![image-20220209230955043](/Users/mac/Library/Application Support/typora-user-images/image-20220209230955043.png)

![image-20220209232527112](/Users/mac/Library/Application Support/typora-user-images/image-20220209232527112.png)

#### 代理模式

![image-20220209232638196](/Users/mac/Library/Application Support/typora-user-images/image-20220209232638196.png)

![image-20220209232745996](/Users/mac/Library/Application Support/typora-user-images/image-20220209232745996.png)

![image-20220209232843118](/Users/mac/Library/Application Support/typora-user-images/image-20220209232843118.png)

![image-20220209232807027](/Users/mac/Library/Application Support/typora-user-images/image-20220209232807027.png)

![image-20220209233100260](/Users/mac/Library/Application Support/typora-user-images/image-20220209233100260.png)

![image-20220209233301347](/Users/mac/Library/Application Support/typora-user-images/image-20220209233301347.png)

#### service和pod绑定是通过标签来绑定的

![image-20220209234953431](/Users/mac/Library/Application Support/typora-user-images/image-20220209234953431.png)

![image-20220209234517778](/Users/mac/Library/Application Support/typora-user-images/image-20220209234517778.png)

#### headless service用来解决podname和hostname变化问题

![image-20220209234654073](/Users/mac/Library/Application Support/typora-user-images/image-20220209234654073.png)

![image-20220209235251078](/Users/mac/Library/Application Support/typora-user-images/image-20220209235251078.png)

![image-20220209235534000](/Users/mac/Library/Application Support/typora-user-images/image-20220209235534000.png)

![image-20220209235630973](/Users/mac/Library/Application Support/typora-user-images/image-20220209235630973.png)

![image-20220209235843281](/Users/mac/Library/Application Support/typora-user-images/image-20220209235843281.png)

#### ingress

ingress看之前的步骤就可以，但是ingress有多种-除了nginx还有多种如haproxy

![image-20220210001823037](/Users/mac/Library/Application Support/typora-user-images/image-20220210001823037.png)

![image-20220210094055057](/Users/mac/Library/Application Support/typora-user-images/image-20220210094055057.png)

![image-20220210094707927](/Users/mac/Library/Application Support/typora-user-images/image-20220210094707927.png)

![image-20220210094621980](/Users/mac/Library/Application Support/typora-user-images/image-20220210094621980.png)

#### configMap

![image-20220210144440418](/Users/mac/Library/Application Support/typora-user-images/image-20220210144440418.png)

![image-20220210144505573](/Users/mac/Library/Application Support/typora-user-images/image-20220210144505573.png)

##### configMap的创建

##### 1.使用目录创建

![image-20220210144610629](/Users/mac/Library/Application Support/typora-user-images/image-20220210144610629.png)

##### 2、使用文件创建

![image-20220210144634174](/Users/mac/Library/Application Support/typora-user-images/image-20220210144634174.png)

##### 3、使用字面值创建

![image-20220210144726187](/Users/mac/Library/Application Support/typora-user-images/image-20220210144726187.png)

##### configMap怎么用

1、用configMap来代替环境变量

![image-20220210170224473](/Users/mac/Library/Application Support/typora-user-images/image-20220210170224473.png)

![image-20220210170242827](/Users/mac/Library/Application Support/typora-user-images/image-20220210170242827.png)

2、用configMap设置命令行参数

![image-20220210165941063](/Users/mac/Library/Application Support/typora-user-images/image-20220210165941063.png)

通过数据卷插件使用configMap

![image-20220210170731948](/Users/mac/Library/Application Support/typora-user-images/image-20220210170731948.png)

configMap的热部署

![image-20220210171439935](/Users/mac/Library/Application Support/typora-user-images/image-20220210171600588.png)

![image-20220210171733503](/Users/mac/Library/Application Support/typora-user-images/image-20220210171733503.png)

![image-20220210171805927](/Users/mac/Library/Application Support/typora-user-images/image-20220210171805927.png)

#### secret

![image-20220210172523513](/Users/mac/Library/Application Support/typora-user-images/image-20220210172523513.png)

##### service Account

![image-20220210172600447](/Users/mac/Library/Application Support/typora-user-images/image-20220210172600447.png)

##### Opaque Secret

![image-20220210172828123](/Users/mac/Library/Application Support/typora-user-images/image-20220210172828123.png)

使用方式

1、将Secret挂载到volume中

![image-20220210173327359](/Users/mac/Library/Application Support/typora-user-images/image-20220210173327359.png)

将secret导入到环境变量中

![image-20220210173638438](/Users/mac/Library/Application Support/typora-user-images/image-20220210173638438.png)

到私有仓库下载镜像

![image-20220211111600334](/Users/mac/Library/Application Support/typora-user-images/image-20220211111600334.png)

#### volume

![image-20220211112253732](/Users/mac/Library/Application Support/typora-user-images/image-20220211112253732.png)

![image-20220211112202018](/Users/mac/Library/Application Support/typora-user-images/image-20220211112202018.png)

##### emptyDir

![image-20220211112517911](/Users/mac/Library/Application Support/typora-user-images/image-20220211112517911.png)

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20220211112922781.png" alt="image-20220211112922781" style="zoom:50%;" />

![image-20220211112722212](/Users/mac/Library/Application Support/typora-user-images/image-20220211112722212.png) 

##### hostPath

![image-20220211114114581](/Users/mac/Library/Application Support/typora-user-images/image-20220211114114581.png)

![image-20220211123816143](/Users/mac/Library/Application Support/typora-user-images/image-20220211123816143.png)

![image-20220211123907146](/Users/mac/Library/Application Support/typora-user-images/image-20220211123907146.png)

![image-20220211124034604](/Users/mac/Library/Application Support/typora-user-images/image-20220211124034604.png)

![image-20220211124119026](/Users/mac/Library/Application Support/typora-user-images/image-20220211124119026.png)

##### pv和pvc

![image-20220211124740620](/Users/mac/Library/Application Support/typora-user-images/image-20220211124740620.png)

![image-20220211125631901](/Users/mac/Library/Application Support/typora-user-images/image-20220211134728702.png)

![image-20220211135903138](/Users/mac/Library/Application Support/typora-user-images/image-20220211135903138.png)

![image-20220211140948174](/Users/mac/Library/Application Support/typora-user-images/image-20220211140948174.png)

![image-20220211141612091](/Users/mac/Library/Application Support/typora-user-images/image-20220211141612091.png)

![image-20220211141732400](/Users/mac/Library/Application Support/typora-user-images/image-20220211141732400.png)

![image-20220211141805092](/Users/mac/Library/Application Support/typora-user-images/image-20220211141805092.png)

回收策略

![image-20220211144515195](/Users/mac/Library/Application Support/typora-user-images/image-20220211144515195.png)

状态

![image-20220211144534294](/Users/mac/Library/Application Support/typora-user-images/image-20220211144534294.png)

演示

![image-20220211144621255](/Users/mac/Library/Application Support/typora-user-images/image-20220211144621255.png)

![image-20220211152329048](/Users/mac/Library/Application Support/typora-user-images/image-20220211152329048.png)

![image-20220211152353927](/Users/mac/Library/Application Support/typora-user-images/image-20220211152353927.png)

![image-20220211152513838](/Users/mac/Library/Application Support/typora-user-images/image-20220211171057424.png)

![image-20220211171759166](/Users/mac/Library/Application Support/typora-user-images/image-20220211171759166.png)

![image-20220211172214543](/Users/mac/Library/Application Support/typora-user-images/image-20220211172214543.png)

#### 调度策略

![image-20220211172744283](/Users/mac/Library/Application Support/typora-user-images/image-20220211172744283.png)

##### 调度过程是预选加优选

![image-20220211173648909](/Users/mac/Library/Application Support/typora-user-images/image-20220211173648909.png)

![image-20220211174048650](/Users/mac/Library/Application Support/typora-user-images/image-20220211174048650.png)

![image-20220211174111580](/Users/mac/Library/Application Support/typora-user-images/image-20220211174111580.png)

#### 调度的亲和性

##### 节点亲和性

![image-20220211174221052](/Users/mac/Library/Application Support/typora-user-images/image-20220211174221052.png)

硬策略

![image-20220211174345982](/Users/mac/Library/Application Support/typora-user-images/image-20220211174345982.png)

软策略

![image-20220211190812805](/Users/mac/Library/Application Support/typora-user-images/image-20220211190812805.png)

合体：软策略和硬策略可以一起存在

![image-20220211191218768](/Users/mac/Library/Application Support/typora-user-images/image-20220211191218768.png)

##### Pod亲和性

![image-20220211191518148](/Users/mac/Library/Application Support/typora-user-images/image-20220211191518148.png)

![image-20220211192127411](/Users/mac/Library/Application Support/typora-user-images/image-20220211192127411.png)

![image-20220211192226783](/Users/mac/Library/Application Support/typora-user-images/image-20220211192226783.png)

![image-20220212151243380](/Users/mac/Library/Application Support/typora-user-images/image-20220212151243380.png)

##### Taint和Toleration

![image-20220212151554301](/Users/mac/Library/Application Support/typora-user-images/image-20220212151554301.png)

![image-20220212151521722](/Users/mac/Library/Application Support/typora-user-images/image-20220212151521722.png)

![image-20220212151735390](/Users/mac/Library/Application Support/typora-user-images/image-20220212151735390.png)

##### 容忍

![image-20220212152254464](/Users/mac/Library/Application Support/typora-user-images/image-20220212152254464.png)

![image-20220212152335021](/Users/mac/Library/Application Support/typora-user-images/image-20220212152335021.png)

![image-20220212152405547](/Users/mac/Library/Application Support/typora-user-images/image-20220212152405547.png)

##### 固定节点

![image-20220212152813378](/Users/mac/Library/Application Support/typora-user-images/image-20220212152813378.png)

#### 机制说明

![image-20220212153346518](/Users/mac/Library/Application Support/typora-user-images/image-20220212153346518.png)

##### 认证

![image-20220212153448220](/Users/mac/Library/Application Support/typora-user-images/image-20220212153448220.png)

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20220212153649097.png" alt="image-20220212153649097" style="zoom: 50%;" />

![image-20220212153816006](/Users/mac/Library/Application Support/typora-user-images/image-20220212153816006.png)

![image-20220212154037412](/Users/mac/Library/Application Support/typora-user-images/image-20220212154037412.png)

![image-20220212153946997](/Users/mac/Library/Application Support/typora-user-images/image-20220212153946997.png)

![image-20220212154057748](/Users/mac/Library/Application Support/typora-user-images/image-20220212154057748.png)

![image-20220212154438075](/Users/mac/Library/Application Support/typora-user-images/image-20220212154438075.png)

![image-20220212154506803](/Users/mac/Library/Application Support/typora-user-images/image-20220212154506803.png)

![image-20220212154717513](/Users/mac/Library/Application Support/typora-user-images/image-20220212154717513.png)

![image-20220212154757319](/Users/mac/Library/Application Support/typora-user-images/image-20220212154757319.png)

![image-20220212154856091](/Users/mac/Library/Application Support/typora-user-images/image-20220212154856091.png)

##### 鉴权

![image-20220212155041358](/Users/mac/Library/Application Support/typora-user-images/image-20220212155041358.png)

![image-20220212155242977](/Users/mac/Library/Application Support/typora-user-images/image-20220212155242977.png)

![image-20220212155831424](/Users/mac/Library/Application Support/typora-user-images/image-20220212155831424.png)

![image-20220212160411571](/Users/mac/Library/Application Support/typora-user-images/image-20220212160411571.png)

![image-20220212160454374](/Users/mac/Library/Application Support/typora-user-images/image-20220212160454374.png)

![image-20220212160236246](/Users/mac/Library/Application Support/typora-user-images/image-20220212160236246.png)

![image-20220212160521278](/Users/mac/Library/Application Support/typora-user-images/image-20220212160521278.png)

![image-20220212160743781](/Users/mac/Library/Application Support/typora-user-images/image-20220212160743781.png)

![image-20220212182946509](/Users/mac/Library/Application Support/typora-user-images/image-20220212182946509.png)

![image-20220212183954292](/Users/mac/Library/Application Support/typora-user-images/image-20220212183954292.png)

![image-20220212184105841](/Users/mac/Library/Application Support/typora-user-images/image-20220212184105841.png)

![image-20220212184136367](/Users/mac/Library/Application Support/typora-user-images/image-20220212184136367.png)

resources

![image-20220212184317925](/Users/mac/Library/Application Support/typora-user-images/image-20220212184317925.png)

to-subject

![image-20220212184446119](/Users/mac/Library/Application Support/typora-user-images/image-20220212184446119.png)

##### 实践：创建一个用户只能管理dev空间

![image-20220212185335700](/Users/mac/Library/Application Support/typora-user-images/image-20220212185335700.png)

![image-20220212185408770](/Users/mac/Library/Application Support/typora-user-images/image-20220212185408770.png)

![image-20220212185728906](/Users/mac/Library/Application Support/typora-user-images/image-20220212185728906.png)

![image-20220212185817868](/Users/mac/Library/Application Support/typora-user-images/image-20220212185817868.png)

![image-20220212185943197](/Users/mac/Library/Application Support/typora-user-images/image-20220212185943197.png)

![image-20220212190002721](/Users/mac/Library/Application Support/typora-user-images/image-20220212190002721.png)

##### 准入控制

![image-20220212190344231](/Users/mac/Library/Application Support/typora-user-images/image-20220212190344231.png)

#### Helm

![image-20220212191444797](/Users/mac/Library/Application Support/typora-user-images/image-20220212191444797.png)

![image-20220212211513063](/Users/mac/Library/Application Support/typora-user-images/image-20220212211513063.png)

![image-20220212212134564](/Users/mac/Library/Application Support/typora-user-images/image-20220212212134564.png)

![image-20220212213141319](/Users/mac/Library/Application Support/typora-user-images/image-20220212213141319.png)

![image-20220212213226731](/Users/mac/Library/Application Support/typora-user-images/image-20220212213226731.png)

![image-20220212213313626](/Users/mac/Library/Application Support/typora-user-images/image-20220212213313626.png)

![image-20220212213332933](/Users/mac/Library/Application Support/typora-user-images/image-20220212213332933.png)

![image-20220212214002859](/Users/mac/Library/Application Support/typora-user-images/image-20220212214002859.png)

![image-20220212214210171](/Users/mac/Library/Application Support/typora-user-images/image-20220212214210171.png)

##### 资源限制

![image-20220212220302029](/Users/mac/Library/Application Support/typora-user-images/image-20220212220302029.png)

![image-20220212220406411](/Users/mac/Library/Application Support/typora-user-images/image-20220212220406411.png)

![image-20220212220508675](/Users/mac/Library/Application Support/typora-user-images/image-20220212220508675.png)

![image-20220212220549077](/Users/mac/Library/Application Support/typora-user-images/image-20220212220549077.png)

#### ca证书可用年限修改

思路是修改kubeadm的源码，直接而看视频吧！！









