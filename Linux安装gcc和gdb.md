# Linux安装gcc以及环境配置和gdb

#### 安装gcc-10.0

添加源:

```shell
sudo add-apt-repository ppa:ubuntu-toolchain-r/ppa
```

更新源:

```shell
sudo apt update
```

下载gcc:

```shell
sudo apt install gcc-10 g++-10
```

默认GCC版本设置为gcc-10.0:

```shell
 sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-10 60 --slave /usr/bin/g++ g++ /usr/bin/g++-10
```

查看gcc版本:

```shell
gcc -v
```

安装环境依赖:

```shell
sudo apt-get  install  build-essential  
```

#### 安装gdb:

```shell
apt-get install gdb
```

