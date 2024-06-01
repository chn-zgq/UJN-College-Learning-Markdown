## Nessus安装、激活以及破解（Windows）

### 官网下载安装包

https://www.tenable.com/downloads/nessus?loginAttempted=true

### 安装须知

直接点击next安装就行，唯一注意的就是**安装目录**，可以设置为自己常用的目录下，因为在后面会在该目录下，进行操作；若弹出报警，点击“是”就行。
![在这里插入图片描述](https://img-blog.csdnimg.cn/ac2a65f644764e5fb8cd5fb8d8087e42.jpeg)


#### 1.Connect via SSL，点击高级，继续访问;
![在这里插入图片描述](https://img-blog.csdnimg.cn/a21d366a2156428d8afb2a05f9eaf338.png)


#### 2.选择Managed Scanner，继续下一步;
![在这里插入图片描述](https://img-blog.csdnimg.cn/6927d8965ed744f2910980166cc937db.png)


#### 3.选择Tenable.sc，继续下一步；
![在这里插入图片描述](https://img-blog.csdnimg.cn/875cca81085b400e821efddccb656676.png)


#### 4.设置账号密码，登录等待初始化完成。
![在这里插入图片描述](https://img-blog.csdnimg.cn/c3fa669806304026b1e72a1805442f84.png)


### 在线激活Scanner

#### 1.获取激活码 <ACTIVATION_CODE>

http://www.tenable.com/products/nessus-home

PS：名字可以随意填写，邮箱可以使用临时邮箱，这里有一个选择是https://10minutemail.org/error-due.html

#### 2.使用命令行（管理员模式），转移到Nessus的安装目录，接下来的操作需要在该目录下进行，停止Nessus服务

```shell
net stop "Tenable Nessus"
```

PS：如果你在网络上已经尝试了其他的方法进行破解，但是未成功，建议使用以下命令后，再进行如下操作

```shell
nessuscli fix --reset
```

#### 3.注册Nessus（时间比较长，具体与你的CPU和网络有关）

```shell
nessuscli fetch --register <ACTIVATION_CODE>
```

#### 4.重建插件数据库

```shell
nessusd -R
```

#### 5.重启Nessus服务

```shell
net start "Tenable Nessus"
```

#### 6.验证是否成功

```shell
nessuscli fetch --check
```

```shell
nessuscli update --all
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/ca1fc96a7e55428f98509e97df6d59eb.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/abd483133bb548939c2ce865c0bc24e2.png)

### 离线激活Scanner

#### 1.获取激活码 <ACTIVATION_CODE>

http://www.tenable.com/products/nessus-home

PS：名字可以随意填写，邮箱可以使用临时邮箱，这里有一个选择是https://10minutemail.org/error-due.html

#### 2.使用命令行（管理员模式），转移到Nessus的安装目录下，获取质询码（challenge code）

```shell
net stop "Tenable Nessus"
nessuscli fetch  --challenge
```

#### 3.获取离线插件包地址及激活证书

网址：https://plugins.nessus.org/v2/offline.php

#### 4.下载离线插件包和license证书

![image-20221114015455824](https://img-blog.csdnimg.cn/a7a550e1f14d4f5b8f34e8434b0dad4a.png)

![image-20221114015611867](https://img-blog.csdnimg.cn/c9f2ce3c18a446a6a199a3bcbfd6f782.png)

#### 5.将下载好的文件复制到Nessus根目录下

![image-20221114015735005](https://img-blog.csdnimg.cn/dc05e8d05ef740e0932f70b5564eb9e2.png)

#### 6.离线激活Nessus

```shell
nessuscli fetch --register-offline ./nessus.license
```

#### 7.安装插件包

```shell
nessuscli update ./all-2.0.tar.gz
```

#### 8.重启Nessus服务

```shell
net start "Tenable Nessus"
```

#### 9.重新访问网址https://127.0.0.1:8834

### 破解Nessus

#### 1.创建一个plugin_feed_info.inc文件，内容如下

```txt
PLUGIN_SET = "202210311525";
PLUGIN_FEED = "ProfessionalFeed (Direct)";
PLUGIN_FEED_TRANSPORT = "Tenable Network Security Lightning";
```

#### 2.替换D:\Nessus\nessus目录下的plugin_feed_info.inc文件

#### 3.删除D:\Nessus\nessus\plugins目录下的plugin_feed_info.inc文件

```shell
del /f D:\Nessus\nessus\plugins\plugin_feed_info.inc
```