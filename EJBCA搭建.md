# EJBCA搭建

## 前言：

**本次EJBCA搭建使用操作系统为centos 7，shell用户为root。**

## 1.下载jdk环境：

```shell
// 下载jdk
yum install -y java-1.8.0-openjdk-devel  
// 查看java版本
java -version 
```

本人jdk环境：

```shell
openjdk version "1.8.0_352"
OpenJDK Runtime Environment (build 1.8.0_352-b08)
OpenJDK 64-Bit Server VM (build 25.352-b08, mixed mode)
```

## 2.下载数据库:

```shell
// 下载数据库
yum install -y mariadb-server
// 启用数据库服务
systemctl start mariadb  
// 自启动数据库服务
systemctl enable mariadb 
```

## 3.创建EJBCA数据库以及相关配置

### 1.创建数据库配置ejbca账户

```shell
// 第一次进入不需要输入密码，直接enter进入即可
mysql -u root -p 
```

```mysql
CREATE USER 'ejbca'@'localhost' IDENTIFIED BY 'ejbca'; # 创建一个ejbca用户
CREATE USER 'ejbca'@'%' IDENTIFIED BY 'ejbca'; 
GRANT ALL ON *.* TO 'ejbca'@'localhost'; # 赋予ejbca用户超户权限
GRANT ALL ON *.* TO 'ejbca'@'%';
flush privileges; # 刷新权限
create database ejbca; # 创建ejbca数据库
exit # 退出数据库
```

### 2.找到mariadb数据库配置文件

```shell
find / -name my.cnf 
//本人测试在/etc/my.cnf
vim /etc/my.cnf
// 在[mysqld]下添加
character-set-server=utf8  
// 设置数据库默认编码方式为utf-8
```

## 4.下载ant

```shell
// install ant
yum -y install ant
```

## 5.下载jboss（版本6.4.0）

下载网址：https://developers.redhat.com/products/eap/download （PS：注意需要登录red hat上才能下载）

我的下载链接为（可能不适用）：https://access.cdn.redhat.com/content/origin/files/sha256/36/363ab89d81b1f496da3e745f6cac7f9456d76bd4a9d1908c597f514232f84c0a/jboss-eap-6.4.0-installer.jar?_auth_=1671199354_59f02789b5301ed4c37293db61847939

```shell
// install jboss
wget https://access.cdn.redhat.com/content/origin/files/sha256/36/363ab89d81b1f496da3e745f6cac7f9456d76bd4a9d1908c597f514232f84c0a/jboss-eap-6.4.0-installer.jar?_auth_=1671199354_59f02789b5301ed4c37293db61847939
```

## 6.下载ejbca（版本ejbca_ce_6_15_2_5）

```shell
// install ejbca
wget https://nchc.dl.sourceforge.net/project/ejbca/ejbca6/ejbca_6_15_2_5/ejbca_ce_6_15_2_5.zip --no-check-certificate
```

## 7.安装并且配置jboss

```shell
java -jar jboss-eap-6.4.0-installer.jar\?_auth_\=1671199354_59f02789b5301ed4c37293db61847939
```

### 1.安装

在此过程中，默认0或者enter即可

```txt
Select language :
0: eng
1: chn
2: deu
3: fra
4: jpn
5: por
6: spa
Please choose [0] :
0
END USER LICENSE AGREEMENT
 RED HAT JBOSS(r) MIDDLEWARE(tm)
PLEASE READ THIS END USER LICENSE AGREEMENT CAREFULLY BEFORE USING SOFTWARE FROM RED HAT. BY USING RED HAT SOFTWARE, YOU SIGNIFY YOUR ASSENT TO AND ACCEPTANCE OF THIS END USER LICENSE AGREEMENT AND ACKNOWLEDGE YOU HAVE READ AND UNDERSTAND THE TERMS. AN INDIVIDUAL ACTING ON BEHALF OF AN ENTITY REPRESENTS THAT HE OR SHE HAS THE AUTHORITY TO ENTER INTO THIS END USER LICENSE AGREEMENT ON BEHALF OF THAT ENTITY. IF YOU DO NOT ACCEPT THE TERMS OF THIS AGREEMENT, THEN YOU MUST NOT USE THE RED HAT SOFTWARE. THIS END USER LICENSE AGREEMENT DOES NOT PROVIDE ANY RIGHTS TO RED HAT SERVICES SUCH AS SOFTWARE MAINTENANCE, UPGRADES OR SUPPORT. PLEASE REVIEW YOUR SERVICE OR SUBSCRIPTION AGREEMENT(S) THAT YOU MAY HAVE WITH RED HAT OR OTHER AUTHORIZED RED HAT SERVICE PROVIDERS REGARDING SERVICES AND ASSOCIATED PAYMENTS.
This end user license agreement (EULA) governs the use of the Red Hat JBoss Middleware and any related updates, source code, appearance, structure and organization (the Programs), regardless of the delivery mechanism.
1. License Grant.  Subject to the following terms, Red Hat, Inc. (Red Hat) grants to you a perpetual, worldwide license to the Programs (each of which may include multiple software components) pursuant to the GNU Lesser General Public License v. 2.1. With the exception of certain image files identified in Section 2 below, each software component is governed by a license that permits you to run, copy, modify, and redistribute (subject to certain obligations in some cases) the software component. This EULA pertains solely to the Programs and does not limit your rights under, or grant you rights that supersede, the license terms applicable to any particular component.
2. Intellectual Property Rights.  The Programs and each of their components are owned by Red Hat and other licensors and are protected under copyright law and under other laws as applicable. Title to the Programs and any component, or to any copy, modification, or merged portion shall remain with Red Hat and other licensors, subject to the applicable license. The JBoss trademark, Red Hat trademark, the individual Program trademarks, and the Shadowman logo are registered trademarks of Red Hat and its affiliates in the U.S. and other countries. This EULA does not permit you to distribute the Programs using Red Hat's trademarks, regardless of whether they have been modified. You may make a commercial redistribution of the Programs only if (a) permitted under a separate written agreement with Red Hat authorizing such commercial redistribution or (b) you remove and replaced all occurrences of Red Hat trademarks and logos. Modifications to the software may corrupt the Programs. You should read the information found at http://www.redhat.com/about/corporate/trademark/ before distributing a copy of the Programs.
3. Limited Warranty.  Except as specifically stated in this Section 3, a separate agreement with Red Hat, or a license for a particular component, to the maximum extent permitted under applicable law, the Programs and the components are provided and licensed as is without warranty of any kind, expressed or implied, including the implied warranties of merchantability, non-infringement or fitness for a particular purpose. Red Hat warrants that the media on which the Programs and the components are provided will be free from defects in materials and manufacture under normal use for a period of 30 days from the date of delivery to you. Neither Red Hat nor its affiliates warrant that the functions contained in the Programs will meet your requirements or that the operation of the Programs will be entirely error free, appear or perform precisely as described in the accompanying documentation, or comply with regulatory requirements. This warranty extends only to the party that purchases subscription services for the Programs from Red Hat and/or its affiliates or a Red Hat authorized distributor.
4. Limitation of Remedies and Liability.  To the maximum extent permitted by applicable law, your exclusive remedy under this EULA is to return any defective media within 30 days of delivery along with a copy of your payment receipt and Red Hat, at its option, will replace it or refund the money you paid for the media. To the maximum extent permitted under applicable law, under no circumstances will Red Hat, its affiliates, any Red Hat authorized distributor, or the licensor of any component provided to you under this EULA be liable to you for any incidental or consequential damages, including lost profits or lost savings arising out of the use or inability to use the Programs or any component, even if Red Hat, its affiliates, an authorized distributor, and/or licensor has been advised of the possibility of such damages. In no event shall Red Hat's or its affiliates' liability, an authorized distributor's liability or the liability of the licensor of a component provided to you under this EULA exceed the amount that you paid to Red Hat for the media under this EULA.
5. Export Control. As required by the laws of the United States and other countries, you represent and warrant that you: (a) understand that the Programs and their components may be subject to export controls under the U.S. Commerce Department's Export Administration Regulations (EAR (b) are not located in a prohibited destination country under the EAR or U.S. sanctions regulations (currently Cuba, Iran, Iraq, North Korea, Sudan and Syria, subject to change as posted by the United States government); (c) will not export, re-export, or transfer the Programs to any prohibited destination, persons or entities on the U.S. Bureau of Industry and Security Denied Parties List or Entity List, or the U.S. Office of Foreign Assets Control list of Specially Designated Nationals and Blocked Persons, or any similar lists maintained by other countries, without the necessary export license(s) or authorizations(s); (d) will not use or transfer the Programs for use in connection with any nuclear, chemical or biological weapons, missile technology, or military end-uses where prohibited by an applicable arms embargo, unless authorized by the relevant government agency by regulation or specific license; (e) understand and agree that if you are in the United States and export or transfer the Programs to eligible end users, you will, to the extent required by EAR Section 740.17(e), submit semi-annual reports to the Commerce Department's Bureau of Industry and Security, which include the name and address (including country) of each transferee; and (f) understand that countries including the United States may restrict the import, use, or export of encryption products (which may include the Programs and the components) and agree that you shall be solely responsible for compliance with any such import, use, or export restrictions.
6. Third Party Programs. Red Hat may distribute third party software programs with the Programs that are not part of the Programs. These third party software programs are not required to run the Programs, are provided as a convenience to you, and are subject to their own license terms. The license terms either accompany the third party software programs or can be viewed at http://www.redhat.com/licenses/thirdparty/eula.html. If you do not agree to abide by the applicable license terms for the third party software programs, then you may not install them. If you wish to install the third party software programs on more than one system or transfer the third party software programs to another party, then you must contact the licensor of the applicable third party software programs.
7. General. If any provision of this EULA is held to be unenforceable, the enforceability of the remaining provisions shall not be affected. Any claim, controversy or dispute arising under or relating to this EULA shall be governed by the laws of the State of New York and of the United States, without regard to any conflict of laws provisions. The rights and obligations of the parties to this EULA shall not be governed by the United Nations Convention on the International Sale of Goods.
Copyright (c) 2013 Red Hat, Inc. All rights reserved. Red Hat, JBoss and the JBoss logo are registered trademarks of Red Hat, Inc. All other trademarks are the property of their respective owners.
press 1 to continue, 2 to quit, 3 to redisplay.
1

Select the installation path:  [/root/EAP-6.4.0]

press 1 to continue, 2 to quit, 3 to redisplay.
1


Select the packs you want to install:
1    [x] [Required]      [Red Hat JBoss Enterprise Application Platform] (542.89 KB)
2    [x]                 [AppClient] (34.24 KB)
3    [x] [Required]      [Bin] (10.99 MB)
4    [x] [Required]      [Bundles] (1.01 MB)
5    [x]                 [Docs] (4.75 MB)
6    [x] [Required]      [Domain] (125.56 KB)
7    [x] [Required]      [Domain Shell Scripts] (17.35 KB)
8    [x] [Required]      [Modules] (147.01 MB)
9    [x] [Required]      [Standalone] (152.77 KB)
10   [x] [Required]      [Standalone Shell Scripts] (14.16 KB)
11   [x] [Required]      [Welcome Content] (2.11 MB)
12   [x]                 [Red Hat JBoss Enterprise Application Platform Natives] (8 KB)
13   [x]                 [Native RHEL7 x86_64] (76 KB)
14   [x]                 [Native Utils RHEL7 x86_64] (53.04 KB)
15   [x]                 [Native Webserver RHEL7 x86_64] (254.97 KB)
Total Size Required: 167.11 MB
Press 0 to confirm your selections
Please select which packs you want to install
0
Pack Selection Done
press 1 to continue, 2 to quit, 3 to redisplay.
1

Create an administrative user
This user will be added to the host container's management realm for administrative purposes. It can be used to access the management console, the management CLI or other applications secured in this realm.

The password must be at least eight characters long, with one alphabetic character, one digit, and one non-alphanumeric character not including &.

Admin username: [admin]
admin

Admin password: []
*****
Validation error.: Password must have at least '8' characters.
Admin password: []
************
Confirm admin password: [************]
************
press 1 to continue, 2 to quit, 3 to redisplay.
1

Quickstarts
Red Hat JBoss Enterprise Application Platform comes with a series of quickstart examples designed to help users begin writing applications using the Java EE 6 technologies. Would you like to install quickstarts?
0  [x] No
1  [ ] Yes
Input Selection:


press 1 to continue, 2 to quit, 3 to redisplay.
1

Socket Bindings
Configure the socket bindings for Red Hat JBoss Enterprise Application Platform.

Select Port Configuration:
0  [x] Use the default port bindings for standalone and domain modes.
1  [ ] Configure an offset for all default port bindings.
2  [ ] Configure custom port bindings.
Input Selection:




If this computer is using a pure IPv6 configuration, please check the box below.
  [ ] Enable pure IPv6 configuration
Input 1 to select, 0 to deselect:
1
press 1 to continue, 2 to quit, 3 to redisplay.
1

Server Launch
Choose server startup mode:
0  [x] Don't start the server
1  [ ] Standalone Mode
2  [ ] Domain Mode
Input Selection:


press 1 to continue, 2 to quit, 3 to redisplay.
1

Logging Options

Configure the logging levels for Red Hat JBoss Enterprise Application Platform?
0  [x] No
1  [ ] Yes
Input Selection:



press 1 to continue, 2 to quit, 3 to redisplay.
1

Configure runtime environment
There are several additional options for configuring Red Hat JBoss Enterprise Application Platform now that the server has been installed. Each option can be individually chosen, and will be configured in the order displayed upon pressing next. Would you like to do this now?
0  [x] Perform default configuration
1  [ ] Perform advanced configuration
Input Selection:


press 1 to continue, 2 to quit, 3 to redisplay.
1

[ Starting to unpack ]
[ Processing package: Red Hat JBoss Enterprise Application Platform (1/15) ]
[ Processing package: AppClient (2/15) ]
[ Processing package: Bin (3/15) ]
[ Processing package: Bundles (4/15) ]
[ Processing package: Docs (5/15) ]
[ Processing package: Domain (6/15) ]
[ Processing package: Domain Shell Scripts (7/15) ]
[ Processing package: Modules (8/15) ]
[ Processing package: Standalone (9/15) ]
[ Processing package: Standalone Shell Scripts (10/15) ]
[ Processing package: Welcome Content (11/15) ]
[ Processing package: Red Hat JBoss Enterprise Application Platform Natives (12/15) ]
[ Processing package: Native RHEL7 x86_64 (13/15) ]
[ Processing package: Native Utils RHEL7 x86_64 (14/15) ]
[ Processing package: Native Webserver RHEL7 x86_64 (15/15) ]
[ Unpacking finished ]
[ Starting processing ]
Starting process Logging installation information (1/7)
IzPack variable state written to /root/EAP-6.4.0/installation/InstallationLog.txt
Starting process Adding temporary admin user (2/7)
Exit code: 0
Command completed successfully.
Starting process Adding admin user (3/7)
Exit code: 0
Command completed successfully.
Starting process Unpacking natives (4/7)
Command success: cp -rP /root/EAP-6.4.0/jboss-eap-6.4/modules /root/EAP-6.4.0
Command success: cp /root/EAP-6.4.0/jboss-eap-6.4/SHA256SUM /root/EAP-6.4.0
Unpacking complete.
Starting process Apply IPv6 configuration to server descriptors (5/7)
Replacing text: 127.0.0.1 with text: localhost in file:
/root/EAP-6.4.0/standalone/configuration/standalone.xml
/root/EAP-6.4.0/standalone/configuration/standalone-ha.xml
/root/EAP-6.4.0/standalone/configuration/standalone-full.xml
/root/EAP-6.4.0/standalone/configuration/standalone-full-ha.xml
/root/EAP-6.4.0/domain/configuration/domain.xml
/root/EAP-6.4.0/domain/configuration/host.xml
/root/EAP-6.4.0/appclient/configuration/appclient.xml
Starting process Apply IPv6 configuration to server start scripts (6/7)
Replacing text: -Djava.net.preferIPv4Stack=true with text: -Djava.net.preferIPv4Stack=false -Djava.net.preferIPv6Addresses=true in file:
/root/EAP-6.4.0/bin/standalone.conf
/root/EAP-6.4.0/bin/domain.conf
/root/EAP-6.4.0/bin/appclient.conf
Starting process Apply IPv6 configuration to domain server descriptor (7/7)
Replacing line matching regex: .*"java.net.preferIPv4Stack".* with text: <!-- ||SELF|| --> in files:
/root/EAP-6.4.0/domain/configuration/domain.xml
Installation has completed successfully.
Application installed on /root/EAP-6.4.0
Would you like to generate an automatic installation script and properties file? (y/n) [n]:y
Select path for the automatic installation script: [/root/EAP-6.4.0/auto.xml]

XML written successfully.
[ Console installation done ]
```

### 2.修改配置文件

```shell
vim /root/EAP-6.4.0/standalone/configuration/standalone.xml
```

```shell
#定位到interface的节点并修改监听地址为0.0.0.0

 <interfaces>
        <interface name="management">
            <inet-address value="${jboss.bind.address.management:0.0.0.0}"/>
        </interface>
        <interface name="public">
            <inet-address value="${jboss.bind.address:0.0.0.0}"/>
        </interface>
        <!-- TODO - only show this if the jacorb subsystem is added  -->
        <interface name="unsecure">
            <!--
              ~  Used for IIOP sockets in the standard configuration.
              ~                  To secure JacORB you need to setup SSL
              -->
            <inet-address value="${jboss.bind.address.unsecure:0.0.0.0}"/>
        </interface>
 </interfaces>
```

### 3.下载MariaDB connector

为了让 JBoss EAP 连接上 MariaDB

```shell
// 进入jboss根目录
cd /root/EAP-6.4.0 
// 创建数据库驱动目录
mkdir -p modules/org/mariadb/main/ 
// 进入驱动目录
cd modules/org/mariadb/main/ 
// 下载connector
wget https://downloads.mariadb.com/Connectors/java/connector-java-2.5.4/mariadb-java-client-2.5.4.jar
// 检查文件
ll 
// 创建配置文件
vim module.xml 
```

```shell
<?xml version="1.0" encoding="UTF-8"?>
<module xmlns="urn:jboss:module:1.0" name="org.mariadb">
  <resources>
    <resource-root path="mariadb-java-client-2.5.4.jar"/>
  </resources>
  <dependencies>
    <module name="javax.api"/>
    <module name="javax.transaction.api"/>
    <module name="org.slf4j"/>
  </dependencies>
</module>
```

启动jboss

```shell
// 启动jboss
/root/EAP-6.4.0/bin/standalone.sh
```

新建一个终端或者ssh窗口，因为本窗口需要运行jboss

启用 MariaDB Connector，使用 jboss-cli.sh 进入 JBoss cli

```shell
// 进入JBoss cli
/root/EAP-6.4.0/bin/jboss-cli.sh
```

```shell
You are disconnected at the moment. Type 'connect' to connect to the server or 'help' for the list of supported commands.
[disconnected /] connect
# 注册驱动
[standalone@localhost:9999 /] /subsystem=datasources/jdbc-driver=org.mariadb.jdbc.Driver:add(driver-name=org.mariadb.jdbc.Driver,driver-module-name=org.mariadb,driver-xa-datasource-class-name=org.mariadb.jdbc.MySQLDataSource)
{"outcome" => "success"}
# 重新加载
[standalone@localhost:9999 /] :reload
{
    "outcome" => "success",
    "result" => undefined
}
[standalone@localhost:9999 /] exit
```

## 8.安装EJBCA

### 1.修改配置文件

```shell
// /root/ejbca是我创建的临时目录，ejbca刚刚是下载在该目录下
cd /root/ejbca 
// 解压ejbca
unzip ejbca_ce_6_15_2_5.zip
// 进入ejbca
cd /root/ejbca/ejbca_ce_6_15_2_5/
// 进入配置目录
cd conf/
```

```shell
cp database.properties.sample database.properties
vim database.properties
// 去掉前面的#，修改以下内容
database.name=mysql
database.url=jdbc:mysql://127.0.0.1:3306/ejbca?characterEncoding=UTF-8
database.driver=org.mariadb.jdbc.Driver
database.username=ejbca
database.password=ejbca
```

```shell
cp ejbca.properties.sample ejbca.properties
vim ejbca.properties

// 修改以下内容
appserver.home=/root/EAP-6.4.0
appserver.type=jboss
approval.defaultrequestvalidity=28800
approval.defaultapprovalvalidity=28800
ejbca.passwordlogrounds=8
```

```shell
cp install.properties.sample install.properties
vim install.properties
// 修改以下内容
ca.name=ManagementCA
ca.dn=CN=ManagementCA,O=CHN_Zgq,C=CN
ca.tokentype=soft
ca.tokenpassword=null
ca.keyspec=4096
ca.keytype=RSA
ca.signaturealgorithm=SHA256WithRSA
ca.validity=3650
ca.policy=null
ca.certificateprofile=ROOTCA
```

```shell
cp web.properties.sample web.properties
vim web.properties
// 修改以下内容
java.trustpassword=changeit #默认即可
superadmin.cn=SuperAdmin #默认即可
superadmin.dn=CN=${superadmin.cn},O=CHN_Zgq,C=CN
superadmin.password=ejbca #默认即可
superadmin.batch=true #默认即可
httpsserver.password=serverpwd #默认即可
httpsserver.hostname=localhost #默认即可
httpsserver.dn=CN=${httpsserver.hostname},O=CHN_Zgq,C=CN
httpserver.pubhttp=8080
httpserver.pubhttps=8442
httpserver.privhttps=8443
```

### 2.部署和安装

```shell
// 进入pks目录下补充环境文件
cd /root/ejbca/ejbca_ce_6_15_2_5/modules/cesecore-p11/src/sun/security/pkcs11
// 下载P11Key.java
wget https://raw.githubusercontent.com/frohoff/jdk8u-dev-jdk/da0da73ab82ed714dc5be94acd2f0d00fbdfe2e9/src/share/classes/sun/security/pkcs11/P11Key.java
// 下载ECUtil.java
wget https://raw.githubusercontent.com/frohoff/jdk8u-dev-jdk/da0da73ab82ed714dc5be94acd2f0d00fbdfe2e9/src/share/classes/sun/security/util/ECUtil.java
// 检查文件
ll
total 60
-rw-------. 1 root root  6888 Nov 21  2019 CESeCoreUtils.java
-rw-r--r--. 1 root root  7970 Dec 16 22:41 ECUtil.java
-rw-r--r--. 1 root root 42036 Dec 16 22:40 P11Key.java
```

```shell
// 返回ejbca根目录
cd /root/ejbca/ejbca_ce_6_15_2_5/
// 部署
ant deploy
// 安装，jboss需要在运行当中
ant install
```

## 9.开放端口

```shell
// 开放防火墙端口以便对外开放
firewall-cmd --zone=public --permanent --add-port="8080/tcp"
firewall-cmd --zone=public --permanent --add-port="8442/tcp"
firewall-cmd --zone=public --permanent --add-port="8443/tcp"
firewall-cmd --reload
```

## 10.登入

http://192.168.3.36:8080/ejbca/ (IP地址为虚拟机IP)

## 11.管理员入口

![image-20221216231409950](C:\Users\Zgq\AppData\Roaming\Typora\typora-user-images\image-20221216231409950.png)

安装管理员证书

```shell
// 进入管理员界面需要管理员证书
ll /root/ejbca/ejbca_ce_6_15_2_5/p12/
total 20
-rw-r--r--. 1 root root 4329 Dec 16 23:01 superadmin.p12
-rw-r--r--. 1 root root 5317 Dec 16 23:01 tomcat.jks
-rw-r--r--. 1 root root 1446 Dec 16 23:01 truststore.jks
```

复制虚拟机里面的证书到Windows

<img src="C:\Users\Zgq\AppData\Roaming\Typora\typora-user-images\image-20221216231803219.png" alt="image-20221216231803219" style="zoom:50%;" />

<img src="C:\Users\Zgq\AppData\Roaming\Typora\typora-user-images\image-20221216231906497.png" alt="image-20221216231906497" style="zoom: 50%;" />

密码为**ejbca**

<img src="C:\Users\Zgq\AppData\Roaming\Typora\typora-user-images\image-20221216231925447.png" alt="image-20221216231925447" style="zoom:50%;" />

导入成功，进入管理员界面

![image-20221216231958929](C:\Users\Zgq\AppData\Roaming\Typora\typora-user-images\image-20221216231958929.png)

