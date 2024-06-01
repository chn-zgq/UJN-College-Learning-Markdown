# MySQL安装教程

## 1.官网下载MySQL（本版本为MySQL Installer 8.0.31）

**官方下载路径**:https://dev.mysql.com/downloads/installer/

![image-20211120142941163](https://img-blog.csdnimg.cn/b4c519996dc94010b621222814bcce4e.png)

![image-20211120143333372](https://img-blog.csdnimg.cn/1946d24ba3934fd087259d020e62c6e9.png#pic_center)

## 2.点击下载的msi文件进行安装

### 1.选择自行选择安装

![image-20221112094540652](https://img-blog.csdnimg.cn/23d4a94bae274ae2b8c37fda09de35da.png)

### 2.选择安装部件（基本使用部件，请选择方框中的部件）

![image-20221112094752119](https://img-blog.csdnimg.cn/f0c951d05491499ea64fe81b87ee2fc4.png)

### 3.修改安装路径

**点击每一个安装的部件，选择Advanced Options，修改安装路径**

![image-20221112095040198](https://img-blog.csdnimg.cn/c6f4625d55d54bfcad07c6886483135b.png)

![image-20221112095133280](https://img-blog.csdnimg.cn/e7b9a3a0780d4e2e811cc672d8583d3a.png)

### 4.点击下一步，进行安装

![image-20221112095238665](https://img-blog.csdnimg.cn/541930d9bfd944ed93f8ac49c739eaa1.png)

![image-20221112095426939](https://img-blog.csdnimg.cn/afa1c2f151954e1b950e98841554def8.png)

![image-20221112095613373](https://img-blog.csdnimg.cn/1b3c179a5e5347219d479c3255ab9af7.png)

### 5.设置密码，选择强密码验证

![image-20221112095725571](https://img-blog.csdnimg.cn/dadc8630642f4bbc86cebb15e5a44324.png)

![image-20221112095910605](https://img-blog.csdnimg.cn/800bc1c50bbf45ac94dfd51317376178.png)

**PS：本图设置的仅仅是root账户密码，可以在下面随意添加用户并且设置密码**

![image-20221112100252173](https://img-blog.csdnimg.cn/69ca788c419347a8b1d6983ca51ffd88.png)

![image-20221112100503462](https://img-blog.csdnimg.cn/045645e6c665454396ff8e4116c21f09.png)

### 6.点击Execute，然后Finish完成，再点击next下一步

![image-20221112103242805](https://img-blog.csdnimg.cn/4e0913bfa59148ff8cffb13092aff247.png)

![image-20221112100757135](https://img-blog.csdnimg.cn/b79c7eeaf1224754a1bfc0a933176383.png)

![image-20221112100903871](https://img-blog.csdnimg.cn/e1cf2a45912043a9b413044a3cc98e72.png)

### 7.点击MySQL软件，进行登录

![image-20221112101242760](https://img-blog.csdnimg.cn/904bec3ac0a0498cb9f8e5ee32a955cf.png)

**输入刚才设置的root账户密码，进入界面**

![image-20221112101421697](https://img-blog.csdnimg.cn/25946c62bf234c929070b2043d8c8c90.png)

![image-20221112101514167](https://img-blog.csdnimg.cn/c81c349b095f4b6684bfe7c1054b8c57.png)

### 8.设置环境变量，以便于命令行调用MySQL

![image-20221112101704114](https://img-blog.csdnimg.cn/d27762c3b321441e9a5659a62ae05906.png)

**选择系统变量-->Path，添加MySQL Server的安装路径**

![image-20221112102030426](https://img-blog.csdnimg.cn/ac2487df0cb74cbcb1ce10e639c3d5ab.png)

### 9.Win + r，输入cmd启动命令行运行MySQL

![image-20221112102241392](https://img-blog.csdnimg.cn/d8fbdaab9b834a8da5bf3744a9ba3374.png)

**输入命令，启动MySQL(密码为root账户密码，前面设置过的)**

```shell
mysql -u root -p
```

![image-20221112102406755](https://img-blog.csdnimg.cn/383e8da158bd4e89a1fc7242c3d029f7.png)

### 10.MySQL安装到此结束

## 3.MySQL服务启动（设置了MySQL服务开机不自启动的用户）

### 1.启动命令行（管理员模式）

### 2.输入以下命令

```shell
net start MySQL //启动MySQL服务，因为我的MySQL服务在前面进行了修改，所以与默认服务名称不一致，该版本下MySQL服务默认名称为MySQL80
net stop MySQL  //停止MySQL服务
```

