# GCC和GDB简单使用:

## GCC

### 前提：c语言文件

### 预编译

```shell
gcc -E main.c -o main.i
```

### 汇编

```shell
gcc -S main.i -o main.s
```

### 编译:

```shell
gcc -c main.s -o main.o
```

### 链接:

```shell
gcc -o main main.o
```

### 执行程序

```shell
/.main
```

### 直接生成exe

```shell
gcc -o main main.c
```

### 编译生成可调式的exe:

```shell
gcc -g main.c -o main
```

## GDB

### 启动GDB

```shell
gdb exename  #(eg: gdb main) 
gdb -q exename //表示不打印gdb版本信息，界面较为干净；
```

### 查看源码

```shell
(gdb)list
```

### 运行程序

```shell
(gdb)run
```

### 设置断点

```
break(简写 b) ：格式 b 行号，在某行设置断点

info breakpoints ：显示断点信息

信息如下:

Num： 断点编号

Disp：断点执行一次之后是否有效 kep：有效 dis：无效

Enb： 当前断点是否有效 y：有效 n：无效

Address：内存地址

What：位置
```

```shell
(gdb)b 5 #在第五行设置断点
(gdb)info breakpoints #显示断点信息
```

### 单步执行

```
continue： 继续执行程序，直到下一个断点或者结束

next：单步执行程序，但是遇到函数时会直接跳过函数，不进入函数

step：单步执行程序，但是遇到函数会进入函数
```

```shell
(gdb)continue
(gdb)next
(gdb)step
```

### 查看变量

```
print:打印变量的值

whatis:打印变量的类型
```

```shell
(gdb)print var_name
(gdb)whatis var_name
```

### 退出GDB

```
quit：退出gdb
```

```shell
(gdb)quit
```

### 具体使用

```
run：简记为 r ，其作用是运行程序，当遇到断点后，程序会在断点处停止运行，等待用户输入下一步的命令。

continue （简写c ）：继续执行，到下一个断点处（或运行结束）

next：（简写 n），单步跟踪程序，当遇到函数调用时，也不进入此函数体；此命令同 step 的主要区别是，step 遇到用户自定义的函

数，将步进到函数中去运行，而 next 则直接调用函数，不会进入到函数体内。

step （简写s）：单步调试如果有函数调用，则进入函数；与命令n不同，n是不进入调用的函数的

until：当你厌倦了在一个循环体内单步跟踪时，这个命令可以运行程序直到退出循环体。

until+行号： 运行至某行，不仅仅用来跳出循环

finish： 运行程序，直到当前函数完成返回，并打印函数返回时的堆栈地址和返回值及参数值等信息。

call 函数(参数)：调用程序中可见的函数，并传递“参数”，如：call fun_name()

quit：简记为 q ，退出gdb
```

