# Openswan安装和简单配置

## 安装环境：

```txt
操作系统：Ubuntu20.0.4TLS
用户权限：root
```

## 下载Openswan:

```shell
wget https://github.com/xelerance/Openswan/archive/refs/tags/v3.0.0.zip
```

## 安装Openswan:

#### 解压Openswan：（PS：刚刚下载的是一个zip包）

```shell
unzip v3.0.0.zip
```

#### 解压后我的源码目录为：

```shell
/root/openswan-3.0.0
```

![image-20230411101711773](C:\Users\33121\AppData\Roaming\Typora\typora-user-images\image-20230411101711773.png)

#### 进入Openswan目录：

```shell
cd Openswan-3.0.0
```

#### 下载相关库依赖：

```shell
apt-get install libgmp-dev
apt-get install bison
apt-get install flex
```

#### 安装Openswan：

(PS：安装需要C++环境，没有环境，请下载：sudo apt-get install gcc)

```shell
make programs install
```

#### 检查是否安装成功：

```shell
ipsec --version
```

![image-20230411102032322](C:\Users\33121\AppData\Roaming\Typora\typora-user-images\image-20230411102032322.png)

## 配置Openswan：

#### 检查配置信息：

```shell
ipsec verify
```

（这是一个python脚本，运行脚本需要python2的环境）

出现问题：

![image-20230411102330616](C:\Users\33121\AppData\Roaming\Typora\typora-user-images\image-20230411102330616.png)

原因：运行需要python2环境

#### 下载python环境：

```shell
apt-get install python2
```

#### 运行python环境：

```shell
python
```

![image-20230411102925576](C:\Users\33121\AppData\Roaming\Typora\typora-user-images\image-20230411102925576.png)

原因：默认的python不是python2

解决方法：设置python2的优先级，如果你的环境中有多个python环境，或者默认python是python3，请为每一个python环境设置一个

优先级

#### 配置python环境：

（PS：python环境默认自动模式启用最高版本，一般现有的是python3，因此我们需要修改默认环境为python2再进行运行，如果你的环境本身是python2,请省略该步骤）
我的ubuntu环境存在的python环境为：

```txt
python2..7，python3.8
```

##### 配置python2.7的优先级为1：

```shell
update-alternatives --install /usr/bin/python python /usr/bin/python2.7 1
```

##### 配置python3.10的优先级为2：

```shell
update-alternatives --install /usr/bin/python python /usr/bin/python3.8 2
```

##### 查看配置列表：

```shell
update-alternatives --list python                                         
```

![image-20230411103533173](C:\Users\33121\AppData\Roaming\Typora\typora-user-images\image-20230411103533173.png)

##### 配置默认python版本：

```shell
update-alternatives --config python
```

![image-20230411103614962](C:\Users\33121\AppData\Roaming\Typora\typora-user-images\image-20230411103614962.png)

#### 内核参数调整：

##### 修改内核：

```shell
vim /etc/sysctl.conf
```

添加以下信息

```shell
# example entries for /etc/sysctl.conf
# forwarding is needed for subnet or l2tp connections
net.ipv4.ip_forward = 1

# rp_filter is stupid and cannot deal decrypted packets "appearing out of
# nowhere"
net.ipv4.conf.default.rp_filter = 0
net.ipv4.conf.all.rp_filter = 0

# when using 1 interface for two networks when using NETKEY, the kernel
# kernel thinks it can be clever by sending a redirect (cause it cannot
# tell an encrypted packet came in, but a decrypted packet came out),
# so it sends a bogus ICMP redirect
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0
net.ipv4.icmp_ignore_bogus_error_responses = 1
net.ipv4.conf.default.log_martians = 0
net.ipv4.conf.all.log_martians = 0
# seems the martian settings are not always enough. If not receiving packets
# try running this:
# for n in eth0 mast0 ipsec0 ipsec1 all default ; do sysctl net.ipv4.conf.$n.rp_filter=0; done
#

# these are non-ipsec specific security policies you should use
net.ipv4.conf.default.accept_source_route = 0
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
```

##### 加载内核：

```shell
sysctl -p
```

#### 检查配置信息：

```shell
ipsec verify
```

配置信息检查

![image-20230411103943627](C:\Users\33121\AppData\Roaming\Typora\typora-user-images\image-20230411103943627.png)

配置信息为上图就没有什么问题

#### 修改ipsec配置文件：

配置文件，我实验的是两台主机，配置文件都保持一致

```shell
vim /etc/ipsec.conf
```

修改相关配置参数，以下是我的配置参数

```shell
# /etc/ipsec.conf - Openswan IPsec configuration file

# This file:  /usr/local/share/doc/openswan/ipsec.conf-sample
#
# Manual:     ipsec.conf.5


version 2.0     # conforms to second version of ipsec.conf specification

# basic configuration
config setup
        dumpdir=/var/run/pluto/
        nat_traversal=yes
        virtual_private=%v4:10.0.0.0/8,%v4:192.168.0.0/16,%v4:172.16.0.0/12,%v4:25.0.0.0/8,%v6:fd00::/8
        oe=off
        protostack=netkey

conn test						# 定义连接名称为openswan_IPsec
        auto=start				# 可选择add、route和start
        pfs=no                  # PFS(Perfect Forward Secrecy)
        compress=no             # 是否压缩
        type=tunnel				# 开启隧道模式
        authby=secret			# 定义认证方式为PSK
        
        ## phase 1 ##
        ike=aes128-sha1;modp1024  # 按照对端配置定义ike阶段算法和group
        ikelifetime=86400s        # ike阶段生命周期
        keyexchange=ike			  # ike密钥交换方式
        
        ## phase 2 ##
        phase2alg=aes128-sha1;modp1024     # 按照对端配置定义IPsec阶段算法和group
        salifetime=3600s          		   # 二阶段生命周期
        phase2=esp				  		   # 二阶段传输格式
		
		# IP配置阶段
        left=192.168.3.208				# 本地IP，nat场景选择真实的主机地址
        leftid=@left					# 本地标识ID（可以是@开头的标识，也可以是你的本地IP地址）
        leftsourceip=192.168.3.208		# 存在nat源地址选择nat后IP
        leftsubnet=0.0.0.0/0			# 本地子网
        leftnexthop=%defaultroute		# nat场景下一跳选择nat后的网关IP（一般按缺省配置）
        right=192.168.3.22				# 远端IP，nat场景选择真实的主机地址
        rightid=@right					# 远端标识ID（可以是@开头的标识，也可以是你的本地IP地址）
        rightsourceip=192.168.3.22		# 存在nat源地址选择nat后IP
        rightsubnet=0.0.0.0/0			# 远端子网
        rightnexthop=%defaultroute		# nat场景下一跳选择nat后的网关IP（一般按缺省配置）
```

上述配置阶段其中最难，网络上资料也没有详细说，现在我将我的理解如下：

**1.两台同一私有网络的主机，或者是公网上的两台主机之间建立IPSec；**

```txt
# 两台主机通信
left=本地IP地址
leftid=本地标识符
rught=远端IP地址
rightid=远端标识符
```

**2.一台公网IP的主机访问一个公网IP下NAT的私有IP的主机之间建立IPSec；**

```txt
# 公网IP主机访问公有IP网关NAT下的私有IP的服务器
# 注意部署位置公网IP主机，部署IPSec在本机，私有IP主机的IPSec需要部署到它所在的公网IP的主机上
# 假设本地端为私有IP一端，远程端为公有IP一端
left=服务器私有IP地址
leftid=本地标识符
leftsourceip=本地公网IP网关地址
leftsubnet=服务器端子网
right=远端公网IP地址
rightid=远端标识符
```

**3.两台处于公网IP网关NAT下的私有IP地址的主机之间建立IPSec。**

```txt
# 处于不同的公网IP网关下的两台私有IP地址的主机
# IPSec的部署位置都是公网IP网关
left=本地私有IP地址
leftid=本地标识符
leftsourceip=本地公网IP网关地址
leftsubnet=本地子网
right=远端私有IP地址
rightid=远端标识符
rightsourceip=远端公网IP网关地址
rightsubnet=远端子网
```

#### 配置共享密钥：

```shell
vim /etc/ipsec.secrets
```

修改为以下信息：

```txt
%any %any : PSK "预共享密钥"
# %any %any : PSK "预共享密钥"，默认是所有的连接都使用共享密钥
# IP地址(标识符) IP地址(标识符) : PSK "预共享密钥"，某个连接共享密钥
# left right : PSK "预共享密钥"
# left，right是上述ipsec.conf中指代的IP地址
```

## 运行ipsec：

```shell
/etc/init.d/ipsec start
servic ipsec start
```

运行成功应该显示的信息

![image-20230425141033643](C:\Users\33121\AppData\Roaming\Typora\typora-user-images\image-20230425141033643.png)

## 查看状态信息：

查看是否建立通道：

```
/etc/init.d/ipsec status
```

![image-20230425140248908](C:\Users\33121\AppData\Roaming\Typora\typora-user-images\image-20230425140248908.png)

查看隧道建立过程：

```shell
ipsec auto status
```

![image-20230425140302656](C:\Users\33121\AppData\Roaming\Typora\typora-user-images\image-20230425140302656.png)

![image-20230425140311305](C:\Users\33121\AppData\Roaming\Typora\typora-user-images\image-20230425140311305.png)

![image-20230425140320131](C:\Users\33121\AppData\Roaming\Typora\typora-user-images\image-20230425140320131.png)

![image-20230425140345481](C:\Users\33121\AppData\Roaming\Typora\typora-user-images\image-20230425140345481.png)

监听IPSec建立过程：

```shell
tcpdump -i whlp0s20f3 host 192.168.3.22
```

![image-20230425140548642](C:\Users\33121\AppData\Roaming\Typora\typora-user-images\image-20230425140548642.png)

## 写在最后：

**本文档只描述了Openswan安装和简单的相关配置，具体情况请按照你所需要的场景进行布置，本文档仅作为一个参考。**
