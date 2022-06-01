# Linux

## linux命令语法

## ![image-20211127143902595](/Users/mac/Library/Application Support/typora-user-images/image-20211127143902595.png)linux目录结构

![image-20211127143715922](/Users/mac/Library/Application Support/typora-user-images/image-20211127143715922.png)

## linux配置文件

```shell
# 命令-查看网络配置文件
[root@192 shell]# vim /etc/sysconfig/network-scripts/ifcfg-ens33

# 命令-ping baidu.com 这个命令好使
[root@192 shell]# vim /etc/resolv.conf
```

![image-20211127144554190](/Users/mac/Library/Application Support/typora-user-images/image-20211127144554190.png)

如果修改nameserver(域名解析器)那么ping baidu.com就不好使了，但是ping 123.206.16.61还是好使的

退出vim编辑器，先esc然后:wq!

### 主机名相关的配置

```shell
# cat /etc/hosts
vim /etc/hosts
```

### 开机显示信息文件

```shell
# 命令
vim /etc/motd

修改完之后logout注销，然后再登录就有登录显示信息了。
```

### 查看系统版本

```shell
[root@192 ~]# cat /etc/os-release
```

### 查看/proc重要路径

```shell
[root@192 ~]# ls /proc/   # 系统硬件的信息
```

/proc/meminfo:系统内存信息

/proc/cpuinfo:关于处理器的信息，如厂商，类型，型号，性能等；

/proc/loadvg:系统负载信息，uptime的结果

/proc/mounts :已加载的文件系统列表

### /var目录下的文件

/var/log:记录系统及软件运行信息文件所在的目录

/var/log/message:系统级别日志文件

/var/log/secure:用户登录信息日志文件

/var/log/dmesg:记录硬件信息加载情况的日志文件

## linux核心命令与文件管理

cd命令

```shell
# cd 
		.  # 当前工作目录
		.. # 上一级工作目录
		-  # 上一次工作目录
		~  # 当前系统登录的用户家目录
```

ls命令

```shell
list # 列出文件夹的内容

# ls参数
		-a # all的意思，显示出所有的文件内容，以及隐藏的
		-l # 详细的列出文件夹内容
		-h # 以人类可阅读的形式，输出文件大小
		--full-time # 已完整的时间格式显示
		-t # 根据最后修改的时间排序文件
		-F # 在不同的文件结尾，输出不同的特殊符号
			以/结尾的就是文件夹
			以*结尾的就是可执行文件
			以@结尾的就是软连接，快捷方式
			普通文件类型，结尾什么都没有
		-d #显示文件夹本身信息 不输出其中的内容
		-r # reverse 逆转排序
		-S # 大写S，针对文件大小排序
		-i # 显示出文件的详细信息（文件的身份证号，存储了文件的元信息：文件的大小，位置，权限等等)
```



pwd命令

```shell
print work directory 打印工作目录的意思
会输出当前所处的一个绝对路径
```

su

```shell
su命令用户切换

# 参数
		- 用户名 # 完全的环境变量用户切换
  
```

logout

```shell
# logout 退出当前用户登录
```

mkdir

```shell
mkdir 文件夹名 # 创建文件夹
# 例子
[root@192 shell]# mkdir {alex,mjj,peiqi,cunzhang} # 创建多个文件夹

# 递归常见多个文件夹
mkdir -p /opt/shell/alex # 递归创建文件夹，且绝对路径
[root@192 shell]# mkdir -p ./alex/chaoge.xiaochaoge/xx			# 这个是绝对路径

# 创建100个文件夹
mkdir chaoge{1..100} # 创建chaoge1到chaoge100文件夹，linux支持的bash脚本语法
```

#### 绝对/相对路径

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20211127160636755.png" alt="image-20211127160636755" style="zoom: 25%;" />

touch

```shell
touch [选项] 文件名 # 创建文件
将文件的访问时间和修改时间改为当前时间

touch的两个作用
1、创建普通文件，在linux文件的后缀格式仅仅是一个名字而已，通过touch创建的都是普通文件
2、修改文件的时间

不存在的文件将会被创建为空文件，除非使用-c或-h选项

使用bash语法：
touch {连续数字或字母} 创建多个文件序列
touch {1..10}
touch {a..z}

参数
		-c, --no-create		# 不创建任何文件
		-t，STAMP				# 使用[[CC]YY]]MMDDhhmmm[.ss]格式的时间代替当前时间
		-r，--reference	# 使用指定文件的时间属性代替当前文件时间
```

cp

```shell
用法: cp [选项] [-T] 源文件 目标文件
或:   cp [选项] 源文件 目标文件
或:   cp [选项] -t 目录 源文件
将源文件复制至目标文件，或将多个源文件复制至目标目录

参数
    -r # 递归式复制目录，即复制目录下的所有层级的子目录即文件 -p 复制的时候保持属性不变
    -d # 复制的时候保持软连接(快捷方式)
    -a # 等于-pdr
    -p # 等于--preserve=模式,所有权，时间戳，复制文件时保持源文件的权限、时间属性
    -i，--interactive	# 覆盖前询问提示
```

cp命令案例

```shell
1、复制普通文件
cp mjj.txt mjj.jj		# 将源文件复制到当前目录下，改名

2、复制普通文件，且改名，放入到另一个文件夹中
cp mjj.txt ./xx/		# 复制放入其他文件夹，保留源文件名
cp mjj.txt ./xx/abc.txt		#复制文件放入其他文件夹，并改名

3、一次性复制多个文件，放到指定文件夹中
cp mjj.txt mjj.py mjj.exe  ./mjj/

4、复制整个文件夹，必须加上-r
cp -r mjj mjj2

5、复制且保持文件属性不变-p参数用法，文件创建时间等属性都不变
cp -p chaoge.txt chaoge.txt1

6、拷贝软连接时候，保持链接属性不变，-d参数
cp -d link_luffy link_luffy3

7、-i参数的用法，覆盖文件前提示
cp -i 文件1，文件2 # 如果文件2已经存在，则会覆盖，-i会让用户进行输入y确认覆盖
备注：为什么我们不加 -i 也会提示呢？
是因为linux存在一个别名的命令——alias
```

**alias命令**

```shell
alias命令
添加，删除，修改别名

用法：
alias ll='ls -l'  # 增加别名/修改别名
unalias ll				# 删除别名

备注：
mac电脑这两个命令都是临时添加别名，想要永久添加，则需要：
再根目录下新建touch ~/.bashrc
然后vim ~/.bash_profile添加上一行source ~/.bashrc
```

mv命令

```shell
mv命令就是mv的缩写，作用是移动或是重命名文件

用法: mv [选项] [-T] 源文件 目标文件
或:	 mv [选项] 源文件 目录
或:   mv [选项] -t 目录 源文件
将源文件重命名未目标文件，或将源文件移动至指定目录

-f, --force				# 覆盖前不询问
-i,--interactive	# 覆盖前询问
```

mv命令案例

```shell
1、移动文件到另一个文件夹
mv mjj.jpg ./xx

2、移动多个文件，放到另一个文件夹中
mv mjj* ./xx			#将当前目录所有的以mjj开头文件，文件夹，都移动到当前目录的xx目录下去

3、重命名方法
mv 旧的文件名 新的文件名
mv mjj.txt mjj.txttt

4、-i参数的用法，覆盖前询问
mv 再直接输的时候，系统做了别名alias，其实敲得是mv -i
语法是，如果修改后的文件名，已经存在，则会询问，是否覆盖该文件

mv mjj.txt mjj.txttt 		# 如果mjj.txttt已经存在了，则询问是否覆盖它

5、强制性覆盖-f
mv -f mjj.txt mjj.txttt			# 覆盖且不提示
```

rm命令

```shell
rm命令就是remove的意思，删除一个或者多个文件，这是linux系统的重要命令
-f,--force				# 强制删除，忽略不存在的文件，不提示确认
-i								# 在删除前需要确认
-I								# 再删除超过三个文件或者递归删除前要求确认
-d,--dir					# 删除空目录
-r,-R,--recursive # 递归删除目录及其内容
-v,-verbose				#	详细显示进行的步骤
	--help					# 显示次帮助信息并退出
	--version				# 显示版本信息并退出
```

rm命令案例

```shell
1、删除普通文件,默认添加了-i参数
rm mjj.txt			# 删除前回去提示，是否要删除

2、一次性删除多个文件
rm a1.txt a2.txt a3.txt			# 删除多个文件,写入多个文件，空格分开就行了

3、删除文件夹，必须给rm添加 -r 参数才行,默认的rm只能删除文件类型
rm -r mjj		# 删除文件夹mjj，以及里面的内容

4、-d参数，只能用于删除空文件夹
rm -d mjj

5、强制删除文件，且不提示
rm -f chaoge*		# 强制删除以chaoge开头的文件，文件夹无法删除

6、强制删除所有的文件和文件夹
rm -rf ./*			# 强制删除当前目录下的所有文件

重要的事情说三遍啊

.		#表示是当前目录
/	#表示根目录
./	#	当前目录下的某内容


如果使用
rm -rf /			# 这表示强制删除根目录下所有内容，这个操作十分危险，严禁敲打
rm -rf ./			# 表示强制性删除当前目录下所有内容，看清楚了

```

## linux帮助命令

```shell
man命令
用法
man 命令
例如:
man ls		# 查看ls命令的man手册，想要退出按下q键

--help 命令
用法
命令 --help
例如
ls --help

info命令
用法
info 命令
例如
info ls
```

## Linux开关机命令

shutdown重启或者关机

```shell
语法：
shutdown -r参数		
-r --reboot		Reboot the machine

shutdown -r 10		# 10分钟后重启
shutdown -r 0			# 立刻重启
shutdown -r now		# 立刻重启
```

关机

````shell
语法
shutdown -h 	--halt 		# 停止的含义

shutdown -h 10		# 10分钟之后关机
shutdown -h 0			# 立刻关机
shutdown -h now		# 立刻关机
````

halt，poweroff, reboot命令关机与重启

```shell
reboot				# 重启

# 关机
poweroff
halt
```

## linux命令行常用的快捷键

```shell
ctrl + c		# cancel取消当前操作
ctrl + l		# 清空屏幕内容
ctrl + d		# 退出当前用户
ctrl + a		# 光标移到行首
ctrl + e		# 光标移到行尾
ctrl + u		# 删除光标到行尾的内容
```

### Linux的环境变量

```shell
执行命令：
echo $PATH		# 这里要带上$，如果不带上$符号的话，这表示输出PATH
echo有打印的意思
$符号后面跟上PATH,表示输出PATH的变量
PATH一定是大写的这个变量是由一对目录组成，分隔符是":"，而不是window上的";"。
```

![image-20211127202224455](/Users/mac/Library/Application Support/typora-user-images/image-20211127202224455.png)

### vim和vi

**vim和vi的使用**

基本上vi/vim共分为三种模式，分别是**命令模式**，**输入模式**和**底线模式**。

**vim工作模式**

命令模式:进入vim默认的模式

编辑模式：按i进入的，aio也可以进入

底行模式：按下:(冒号)之后进入的模式

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20211127202809152.png" alt="image-20211127202809152" style="zoom:33%;" />

vim默认机器是不安装的，需要手动安装这个命令工具

yum install vim -y			# 通过yum软件管理工具了，安装命令vim，且默认是yes，这个命令得保证机器可以上网

当vim打开不存在的文件的时候，默认会创建文件

流程：

1、

vim 文件名		# 打开文件

2、输入 按下字母i，进入编辑模式，代表insert

输入字母o，在光标下一行进行编辑

3、写完代码之后，按下esc键，退出编辑模式

4、此时输入冒号，进入底线命令模式，然后输入

​			:wq！强制写入文件内容且退出vim， write quit ！表示强制性的

​			:q!	不保存直接退出

5、注意输入的冒号得是英文输入法的状态

#### vim快捷键

```shell
#vim提供的上下左右
h 向左
j 向下
k 向上
i 向右
```

```shell
# 移动光标的快捷键

w	移动到下一个单词
b 移动到上一个单词

数字0	移动到行首
字符$	移动到行尾

按下g 移动到文章的开头	
按下G	移动到文章的结尾

按下H	移动到屏幕的开头
按下L 移动到屏幕的结尾
按下M	移动到屏幕的中间
```

```shell
# 命令模式的查找

向下查找 /你要找的内容
			/love	# 我要找到love相关的信息，按下n跳转到下一个单词

向上查找 ？你要找的内容
			？love # 我要找love相关的信息，按下n跳转到下一个单词
```

```shell
# 命令模式下的复制，粘贴，删除
输入yy		# 复制光标所在行
输入4yy		# 复制4行内容

输入p			# 打印粘贴的内容

输入dd		# 删除光标所在的行

输入D			#	删除光标当前位置到行尾的内容

输入x			# 删除光标当前字符，向后删除

输入X			# 删除光标当前字符，向前删除

输入u			# 撤销
```

```shell
# 快捷操作
删除光标所在位置，到行尾的内容，且进入编辑模式，输入大写C
输入o，在当前光标的下一行开始编辑
输入O，在当前光标的上一行开始编辑
输入A，快速进入行尾，且进入编辑模式
输入ZZ，快速保存退出
```

```shell
# 批量快捷操作

步骤

快捷删除
1、输入ctrl + v 进入可视块模式
2、用上下左右命令，选择你想操作的块
3、选中块后，输入d 删除块内容

快捷插入多行
4、选中块后，输入大写T，进行写代码
5、按下esc两次会自动生成多行代码
```

vim的swp文件的解决方法

```shell
如果同时有多人操作同一个文件时，可能会生成swp文件，我们可以再vim打开文件，之后按下r，然后:wq!保存并退出，在当前目录中找到swp文件然后rm -f 删掉它
```

### 重定向符号

![image-20211203094314867](/Users/mac/Library/Application Support/typora-user-images/image-20211203094314867.png)

```shell
# 1、读取文件内容，且写入到另一个文件中，覆盖写入文件内容
cat douyin.txt > kuaishou.txt

# 2、追加写入文件内容
cat douyin.txt >> kuaishou.txt

# 3、重定向写入符号
cat < douyin.txt # 把文件中的数据，发送给cat命令去读取

# 将文本内容拆分成多行
# xargs命令
xargs -n 4 < shuzi.txt # 把文本内容拆成多行

# 4、重定向追加写入符号
cat >> gushi.txt <<EOF
```

### cat命令

cat 功能参数用法

```shell
# 1、查看文本,以及功能参数
cat gushi.txt

# 2、对非空行显示行号
cat -b gushi.txt

# 3、对所有行显示行号
cat -n gushi.txt

# 4、在每行结尾都加上$
cat -n -E gushi.txt

# 5、不输出多行空行，减少为1个空行
cat -s gushi.txt
```

cat命令合并多个文件

```shell
# 合并多个文件内容，写入到新的文件中
cat douyin.txt gushi.txt > ./合并内容.txt
```

cat非交互式的写入命令

```shell
cat >> douyin.txt <<EOF
最后用EOF结尾
```

cat清空文件的用法

```shell
# 1、直接清空文件，但是留下了个空行
echo > gushi.txt

# 2、直接清空
> gushi.txt

# 3、利用cat读取一个黑洞文件。然后清空其他文本
cat /dev/null > douyin2.txt
```

tac命令

倒过来看

管道符

```shell
cat gushu.txt | grep  ’know me‘
```

more命令和less命令

```shell
more 文件名
按下enter回车试下一行
空格是向下滚动一个屏幕大小
=显示当前行号
按下q是退出more
```

head和tail命令

```shell
head -5 文件名
head 文件名 # head 默认显示10行

-c 参数，指定字符数量，显示字符数
head -c 5 文件名
```

tail默认从后向前看10行，也可以指定行数

```shell
-f 实时刷新文件内容变化
tail -f gushi.txt
tail -100f gushi.txt

-F 不断的打开文件，一般和-f结合使用
```

cut命令

切割每一行的字符

```shell
1.截取每一行第四个字符
cut -c  4 aliex.txt
2.截取4到6个字符
cut -c 4-6 alex.txt
3.截取5和7的字符
cut -c 5,7 alex.txt
4.截取一个范围的字符，如第四个到结尾
cut -c 4- alex.txt
5.截取一个范围的字符，如开头到第6个
cut -c -6 alex.txt
6.指定分割符，进行截取
cut -d ":" -f 3 alex.txt
7.找到开头到第三个区域的内容
cur -d ":" -f 3 alex.txt
```

sort

```shell
sort -n file.txt

sort -nr file.txt

sort -u file.txt

sort -t 
```

wc

```shell
wc -L 
wc -c 
wc -m
wc -l
wc -w
```

tr

```shell
echo "my name is alex" | tr '[a-z]' 'A-Z'

echo "my name is alex "
```

stat

```shell
stat hello.txt
```

find

```shell
# 根据名字进行全盘搜索
find -name "*.txt" 
find /opt -maxdepth 1 -name "*.txt"]

find . -type f -atime -2 

find . -maxdepth 2 -type f -size +10M

find . -path "./test_find" -prune -o -name  "*.txt" -print

find . -type f -name "*.txt" -ok rm {} \;
```

xargs命令

```shell
xargs 多行变为单行
xargs < chaoge.txt

-n 限制每行输出个数
xargs -d "," -n 2

-i 用{}替代传输的数据
find . -name "*.txt" | xargs -i mv {} alltmptxt/

-I 用字符串代替数据
find . -name "*.txt" | xargs -I alltxt mv alltxt ./
```

tar命令

```shell
压缩
tar -czvf hello.tar.gz hello.txt
解压
tar -xzvf hello.tar.gz .
显示压缩文件中内容
tar -ztvf hello.tar.gz

解压到指定文件夹
tar -zxvf hello.tar.gz -C ./dage/
```

useradd

```shell
tail /etc/passwd
tail /etc/shadow

chpasswd

who 
w
last
lastlog
id

chattr
lsattr
chmod
chown
umask

# 反引号相当于$()
``

"" 弱引用
'' 强引用

2>&1

cp /home/hello.txt{,.back}
```

三剑客

grep

```shell
grep -n -i 'root' pwd.txt
grep -n -i 'root' pwd.txt -c

grep '^$' pwd.txt -n 

grep '^$' hello.txt -n -v

grep ".s" hello.txt -n 

grep "." hello.txt

grep "\.$" hello.txt -n 

# 贪婪匹配
grep ".*e" hello.txt

grep [A_Z0-9a-z] hello.txt

grep -E "a|x" hello.txt

grep -E "goo|lad" pwd.txt

grep -E "g(oo|la)d" pwd.txt

grep -E "l..e" pwd.txt

grep -E "(s..n).*\1" pwd.txt -n

grep -E "o{1,3}" pwd.txt
```

sed

```shell
sed "2,3p" pwd.txt -n

sed "2,+3p" pwd.txt -n

sed "/admin/p" pwd.txt -n

sed "/admin/d" pwd.txt

sed '3,$d' pwd.txt

sed "s/sbin/我是大哥/g" pwd.txt -i

sed -e "s/sbin/我是大哥/g" -e "s/var/哈哈哈/g" pwd.txt

sed "2a 哈哈哈" pwd.txt

sed "2i 哈哈哈" pwd.txt

sed "3a haha \nwoshidage" pwd.txt 

ifconfig eth0 | sed "2p" -n | sed "s/^.*inet//" | sed "s/net.*$//"

ifconfig eth0 | sed  -e "2s/^.*inet//" -e "2s/net.*$//p" -n
```

awk

```shell
awk '{print $1}' pwd.txt

awk '{print $1,$2,$3}' pwd.txt

awk '{print "第一列:",$1,"第二列:",$1}' pwd.txt

awk '{print} ' pwd.txt

awk '{print $0} ' pwd.txt

awk '{print "每一行的内容是: "$0}' pwd.txt

awk 'NR=3,NR=6' pwd.txt

awk '{print NR,$0}' pwd.txt

awk '{print $0,$(NF-1)}' pwd.txt

ifconfig eth0 | awk 'NR==2{print $2}'

awk -F ":" '{print $1}' pwd.txt

awk -F ":" '{print $1, "----" ,$NF}' pwd.txt

awk -F ":" -v OFS="===" '{print $1,$NF}' pwd.txt

awk -F ":" -v OFS="----" '{print $1,$NF}' pwd.txt

awk -F ":" -v OFS="\t" '{print $1,$NF}' pwd.txt

awk -F ":" '{print NR,NF,$1}' pwd.txt

awk -v FS=":" '{print NR,NF,$1}' pwd.txt

awk -F ":" -v ORS='dage' 'NR==1,NR==10{print NR,$0}' pwd.txt

awk 'BEGIN{print "我是你大哥"} {print ARGV[0],ARGV[1],ARGV[2]}' pwd.txt dage.txt

awk -v myname="大哥" 'BEGIN{print "我是谁？",myname}'

awk '{printf "%s\n",$0}' pwd.txt

awk 'BEGIN{printf "%d\n%d\n%d---\n",1,2,3}'

awk -F ":" '{printf "第一列:%s  第二列:%s  第三列:%s",$1,$2,$3 }' pwd.txt

# 等于 grep ”game“
awk -F ":" '/^game/{print $1,$NF}' pwd.txt

awk -F ":" 'BEGIN{printf "%-30s\t%-30s\t%-30s\t%-30s\t\n","用户名","用户id","用户家目录","用户解释器"}{printf "%-30s\t%-30s\t%-30s\t%-30s\t\n",$1,$3,$6,$7}' pwd.txt

awk '/\/sbin\/nologin$/{print $0}' pwd.txt

awk '/^operator/,/^unbound/{print NR,$0}' pwd.txt

awk '{print $0}' nginx.log | sort -n | uniq | wc -l

awk '{print $0}' nginx.log | sort -n | uniq -c | sort -nr | head -10
```

grep练习题

```shell
grep -E "^(root|admin)\>" /etc/passwd

grep -v "^root" pwd.txt

grep -c "^admin" pwd.txt

grep -m 3 "^s" pwd.txt

grep -l "^s" pwd.txt

# 两位数或三位数
grep -E "\<[0-9]{2,3}\>" pwd.txt

grep -E "^[[:space:]]+[^[:space:]]" hello.txt

grep -E "^[^:]+\>" pwd.txt

grep -E "^([^:]+\>).*\1$" pwd.txt
```

sed练习

```shell
sed '5,$d' pwd.txt
```

![image-20220304160000785](/Users/mac/Library/Application Support/typora-user-images/image-20220304160000785.png)

```shell
pkill -9 -t pts/2
```

![image-20220304163035633](/Users/mac/Library/Application Support/typora-user-images/image-20220304163035633.png)
