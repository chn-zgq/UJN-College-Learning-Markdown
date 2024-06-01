# Web安全之CTF测试赛

## 1.000-我是谁

### 题目描述：

找到诈骗网站bsde.cn的域名注册人及邮箱，将域名注册人的126邮箱填写到下方答案框并点击送出

![image-20221116142510932](https://img-blog.csdnimg.cn/a32ba96692fb49b7be1fb2b6ee156e58.png)

考察点：whois查询

whois查询网址：

```txt
https://x.threatbook.com/  //微步在线
http://whois.chinaz.com/  //站长工具
http://whois.bugscaner.com/  //bugscaner
https://whois.aliyun.com/  //阿里云whois
```

Kail自带工具查询

```shell
whois <域名>
```

### 解题：

![image-20221116144336422](https://img-blog.csdnimg.cn/1904020d2db3435189b3cfcd3088fd50.png)

![image-20221116144532586](https://img-blog.csdnimg.cn/bb73985574a6447086a41a0e8b4fdf77.png)

## 2.001-透明人

### 题目描述：

![image-20221116144804032](https://img-blog.csdnimg.cn/fc2b9bddd2824151b8e1cf9ee00be768.png)

**考察点：Google hack 语法**

### 解题：

```txt
site:edu.cn intext:宋淑芬  //发现只有使用Google搜索引擎才能搜到，使用Google搜索引擎直接搜索济南大学宋淑芬也能得到结果
```

## 3.002-我在哪里

### 题目描述：

### 解题：

```
ping ctf.ujn.edu.cn -c 4 //ping获取IP地址
namp -v -A ctf.ujn.edu.cn  //namp扫描获取IP地址
```

![image-20221120142221122](https://img-blog.csdnimg.cn/c247482e70c249f5b4c458d95cb05f0c.png)

![image-20221120142616015](https://img-blog.csdnimg.cn/290bf155f6bd4bfe91bad68668b0fdad.png)

## 4.003-签到题目

### 题目描述：

![image-20221120142732618](https://img-blog.csdnimg.cn/8649d176501249c2a1c26ba45810e005.png)

### 解题：

1.F12查看源代码，发现flag值。

![image-20221120142821490](https://img-blog.csdnimg.cn/d867032b0d114cccb8a9b555ee917141.png)

2.CTR+U查看源代码，未渲染之前的源代码，发现flag值。

![image-20221120142930388](https://img-blog.csdnimg.cn/f3a55d35cf014501893d5feb80ca72c7.png)

## 5.004-1+1=？

### 题目描述：

![image-20221120143224626](https://img-blog.csdnimg.cn/01e15ccaaa49490e98804a25d8def2ee.png)

### 解题：

1.方框不能输入数值，尝试HTML源码加入value=“2”，得到flag值。

![image-20221120143402674](https://img-blog.csdnimg.cn/bbf5399af1be4682a2050d2e4e5faa9d.png)

![image-20221120143449377](https://img-blog.csdnimg.cn/3fdcaf2a983d44108cf933706c06239d.png)

![image-20221120143457747](https://img-blog.csdnimg.cn/8779f62ae797400fb7a350f2d487b1ad.png)

2.查看网页源码发现提交了JS验证，找到JS验证代码，发现flag值。

![image-20221120144450206](https://img-blog.csdnimg.cn/f665dc9500e14d51990ffa1636194ed5.png)

![image-20221120144525179](https://img-blog.csdnimg.cn/313eacc92dbe458bb53c47efd30c61e2.png)

## 6.005-get拿来吧你

### 题目描述：

![image-20221120144641058](https://img-blog.csdnimg.cn/f0e0bf2a92754cd08def0792b7db8060.png)

### 解题：

根据PHP源码，构造payload值，取得flag值。

![image-20221120144750793](https://img-blog.csdnimg.cn/2b4f5ca26d23483c8482c4ef146bd9ce.png)

![image-20221120144810020](https://img-blog.csdnimg.cn/35e1d621129145689853632861174183.png)

## 7.006-你从哪里来的朋友？

### 题目描述：

![image-20221120145038614](https://img-blog.csdnimg.cn/e4269324afb2424392317674d5b090f3.png)

### 解题：

Burp Suite抓包，修改 Referer 为http://www.ujn.edu.cn/后，得到flag值

![image-20221120145231695](https://img-blog.csdnimg.cn/afaa1de9daf14be79892bdf9ccf9045c.png)

![image-20221120145418671](https://img-blog.csdnimg.cn/4d533376c81242d2bc660ffade6bcf67.png)

![image-20221120145442266](https://img-blog.csdnimg.cn/40fcb572dd80417aa9c5af09ebb0f724.png)

## 8.007-host和hosts

### 题目描述：

![image-20221120145627494](https://img-blog.csdnimg.cn/1c92d3cc84f146c18c65d83557174447.png)

### 解题：

Burp Suite抓包，修改host值为flag.ujn.edu.cn后，得到flag值。(host值是web服务器的值)

![image-20221120150328700](https://img-blog.csdnimg.cn/73087d559f92410b8bcee453414ba1a4.png)

![image-20221120150406362](https://img-blog.csdnimg.cn/aec9d6abab9542d9b65be1d6bcd80d3e.png)

## 9.008-禁止访问

### 题目描述：

![image-20221120150528924](https://img-blog.csdnimg.cn/fa1f7e90e0ba4159a0e20f1c7f025e3a.png)

### 解题：

Burp Suite抓包，添加X-Forwarded-For: 6.6.6.6后，得到flag值。

![image-20221120151037342](https://img-blog.csdnimg.cn/22500c6b89b844448dc6564f026d0ee2.png)

![image-20221120151115885](https://img-blog.csdnimg.cn/53b9eb13cf404cdd82c7d8c921cf5f9a.png)

## 10.009-万能密码

### 题目描述：

![image-20221120151147066](https://img-blog.csdnimg.cn/562dd2958fe74908af4c27d6878dc3f6.png)

### 解题：

1.尝试简单密码，得到flag值。（admin，admin）

![image-20221120151346436](https://img-blog.csdnimg.cn/5484e184f49e498e9c4e7a5e711dc196.png)

2.Burp Suite抓包，CTR+I使用intruder进行字典爆破，获得flag值。

![image-20221120153843598](https://img-blog.csdnimg.cn/84e4ed50bd4c4ef6a9f462aa4283aec6.png)

WORDLIST路径为/usr/share/wordlists/dirbuster/

![image-20221120153920198](https://img-blog.csdnimg.cn/0e27d0ef21744c14a56074e6e4150953.png)

![image-20221120154110425](https://img-blog.csdnimg.cn/e8d1036d11024affb4baa2cc585ef0d6.png)

![image-20221120170045685](https://img-blog.csdnimg.cn/e915f4548ea64a35b1eae0f76eb06eea.png)

## 11.010-报错注入

### 题目描述：

![image-20221120155525617](https://img-blog.csdnimg.cn/1b3daf0656b14a99bf5393db6ee9403a.png)

### 解题：

1.报错注入，使用updatexml（）函数。

构造payload，获取数据库名

![image-20221120155842559](https://img-blog.csdnimg.cn/bacf03efc31f46b89906bed4d71f748f.png)

![image-20221120155904863](https://img-blog.csdnimg.cn/1b8fd9761292443da80ec13cae565276.png)

构造payload，获取表名

![image-20221120160539767](https://img-blog.csdnimg.cn/03df4af8d3494ef6a114b334d246ac58.png)

![image-20221120160552458](https://img-blog.csdnimg.cn/361eca243594442994774284185fbe4f.png)

构造payload，获取列名

![image-20221120160734850](https://img-blog.csdnimg.cn/603f12f2261942268e4b781551a44df3.png)

![image-20221120160758117](https://img-blog.csdnimg.cn/3bd43f3592e34a07ba044919dfd72e2b.png)

构造SQL语句，获取flag值。

![image-20221120160856938](https://img-blog.csdnimg.cn/b048c04af4d74008b2e3700985c16464.png)

![image-20221120160915696](https://img-blog.csdnimg.cn/07132aabf4fa4f41bc78212e6dd98a47.png)

2.使用sqlmap自动化工具进行注入

```
sqlmap -u 域名 --current-db  //获取当前数据库名
```

![image-20221120161338004](https://img-blog.csdnimg.cn/1efa2c8a1bbd4c8685e2c1e54407960e.png)

![image-20221120161412585](https://img-blog.csdnimg.cn/601284d2efca475ea7d4c411d730cc0b.png)

```
sqlmap -u 域名 -D 数据库名 --dump-all  //获取数据库所有信息
```

![image-20221120161638392](https://img-blog.csdnimg.cn/9d16a6cb883a415dba648e04f546b029.png)

![image-20221120161616476](https://img-blog.csdnimg.cn/66e886296cb94e84ad05e3ebd6a81d0b.png)

## 12.011-客户端校验真不靠谱

### 题目描述：

![image-20221120154150663](https://img-blog.csdnimg.cn/e2a73711441644dfb28620330b36f5c8.png)

### 解题：

F12查看源代码，修改提交函数返回值为1，得到flag值。

![image-20221120154318155](https://img-blog.csdnimg.cn/d159e2b7443f472e8dc3d5ccd97532cf.png)

![image-20221120155428138](https://img-blog.csdnimg.cn/5f7cf603192443e4959d0a121ef8749a.png)

