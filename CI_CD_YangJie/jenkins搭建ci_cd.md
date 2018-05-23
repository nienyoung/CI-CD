# Jenkins搭建CI/CD环境

杨杰 516030910465

## 目录

1. CI/CD介绍

1. 工具和功能介绍

1. 具体实现

1. 问题和可改进之处

## 一. CI/CD介绍

CI，即持续集成(Continuous integration)是一种软件开发实践，即团队开发成员经常集成它们的工作。每次集成都通过自动化的构建（包括编译，发布，自动化测试）来验证，从而尽早地发现集成错误。

CD，即持续部署（continuous deployment）是通过自动化的构建、测试和部署循环来快速交付高质量的产品，让 engineering productivity 最大化。

## 二. 工具和功能介绍

### **1. 工具**

### 自动构建工具：jenkins

* 易于安装
* 易于配置
* 集成RSS/E-mai
* 生成JUnit/TestNG测试报告。
* 分布式构建
* 文件识别
* 插入支持

### 版本控制工具：github

### **2. 实现的功能**

### 人工开发流程：

1. 开发
1. 提交
1. 编译
1. 测试
1. 发布

### 采用CI/CD后3,4,5步骤自动化：

1. 自动编译测试打包
1. 邮件发送自动构建结果
1. 自动化部署

### **3.采用分布式构建**

分布式构建能够让同一套代码或项目在不同的环境中编译、部署

![Alt text](https://s1.ax1x.com/2018/05/23/CRTNCQ.png)

### 项目过多的问题：

项目过多和Jenkins分支过多，每个项目都登录jenkins进行操作，没有多线的概念，不便于管理

### 分布式方案：

使用一台服务器作为Jenkins的master，其余项目均应用在节点机子上

便利：

1. 节省维护多个Jenkins 的成本，只需要维护主机上的Jenkins配置和插件
1. 从机器只需要安装主机上的节点jar包

## 三. 具体实现

### **1. 配置slave**

1）登录jenkins，选中“系统管理”，右侧框中点进“系统设置”

![Alt text](https://s1.ax1x.com/2018/05/23/CR7SVf.png)

2）展开“代理协议”并全部选中

此处选中之后，会启用"Java Web Start Agent"，否则其在创建slave时处于不可见状态

![Alt text](https://s1.ax1x.com/2018/05/23/CR7pa8.png)

3）回到“系统管理”界面，选择“管理节点"

![Alt text](https://s1.ax1x.com/2018/05/23/CRTxqP.png)

4）选中左侧“新建节点”创建node并填写信息

远程目录用于存储Jenkins客户端运行文件

![CRbNCD.png](https://s1.ax1x.com/2018/05/23/CRbNCD.png)

5）此时新建node处于未连接状态

在作为server的机器上登录jenkins并点击“launch”

安装浏览器下载的.jnlp文件

[![CRbU8e.png](https://s1.ax1x.com/2018/05/23/CRbU8e.png)](https://imgchr.com/i/CRbU8e)

6）.jnlp文件要求java环境，连接成功后回到主机查看已连接

创建好node，就和某一台server连接上了，接下来需要创建job，完成工作

[![CRbagH.png](https://s1.ax1x.com/2018/05/23/CRbagH.png)](https://imgchr.com/i/CRbagH)
[![CRbY4O.png](https://s1.ax1x.com/2018/05/23/CRbY4O.png)](https://imgchr.com/i/CRbY4O)

### **2. 构建CI任务**

1）选中“新建任务”，填写名称，选择自由风格的项目

[![CRb75T.png](https://s1.ax1x.com/2018/05/23/CRb75T.png)](https://imgchr.com/i/CRb75T)

2）任务详细页勾选“限制项目的运行节点”，填写node标签

该项目仅由此节点响应，实现分布式构建

[![CRbL24.png](https://s1.ax1x.com/2018/05/23/CRbL24.png)](https://imgchr.com/i/CRbL24)

3）“源码管理”选中git并填入github地址，账号密码

Branches to build选择需要构建的分支

[![CRbvrR.png](https://s1.ax1x.com/2018/05/23/CRbvrR.png)](https://imgchr.com/i/CRbvrR)

4）“构建触发器”选中github hook trigger for GITScm polling

当有push到达github则开始构建

[![CRqSVx.png](https://s1.ax1x.com/2018/05/23/CRqSVx.png)](https://imgchr.com/i/CRqSVx)

5）登陆Github

在项目的settings -> Webhooks -> Add Hook 里填上

http://jenkins_url/github-webhook/

[![CRq9IK.png](https://s1.ax1x.com/2018/05/23/CRq9IK.png)](https://imgchr.com/i/CRq9IK)

6）“构建”里选择语言并写入命令，即push后希望对项目进行的操作

可执行构建，测试，打包等

[![CRqiGD.png](https://s1.ax1x.com/2018/05/23/CRqiGD.png)](https://imgchr.com/i/CRqiGD)

7）保存并返回“系统管理”——>“系统设置”
，填写Jenkins url和管理员邮件地址（和发件地址保持一致）

[![CRqFRe.png](https://s1.ax1x.com/2018/05/23/CRqFRe.png)](https://imgchr.com/i/CRqFRe)

8）“邮件通知”填发件邮箱的smtp服务器和后缀

勾选“使用smtp认证”，填写用户名密码
（邮箱需开通smtp功能并获得smtp服务使用的密码）

[![CRqZqI.png](https://s1.ax1x.com/2018/05/23/CRqZqI.png)](https://imgchr.com/i/CRqZqI)

9）选择测试并填写收件邮箱，配置成功即可收到邮件

[![CRqGss.png](https://s1.ax1x.com/2018/05/23/CRqGss.png)](https://imgchr.com/i/CRqGss)

10）返回任务配置页

“构建后操作”选择editable email notification

[![CRqNd0.png](https://s1.ax1x.com/2018/05/23/CRqNd0.png)](https://imgchr.com/i/CRqNd0)

11）“trigger”里选择“failure-any”和“success”
，表示构建失败和成功时发送邮件

自动构建功能已经完成

[![CRqdiT.png](https://s1.ax1x.com/2018/05/23/CRqdiT.png)](https://imgchr.com/i/CRqdiT)

### **3.构建CD任务**

1）使用CD的项目为maven项目

“系统管理”——>“全局工具配置”，设置jdk和maven与本地path的路径一致

[![CRq2o6.png](https://s1.ax1x.com/2018/05/23/CRq2o6.png)](https://imgchr.com/i/CRq2o6)
[![CRqhWD.png](https://s1.ax1x.com/2018/05/23/CRqhWD.png)](https://imgchr.com/i/CRqhWD)

2） “系统管理”——>“插件管理”
下载deploy to container plugin

可以将打包好的war包发送到指定的Servlet容器中部署

[![CRqoyd.png](https://s1.ax1x.com/2018/05/23/CRqoyd.png)](https://imgchr.com/i/CRqoyd)

3） “构建后操作”选择deploy war to a container，将构建后的war包部署到tomcat上

WAR/EAR：构建后的war包地址

Tomcat账号信息：tomcat的安装目录的conf目录下，tomcat-user.xml文件的tomcat-users标签中

[![CRLSyj.png](https://s1.ax1x.com/2018/05/23/CRLSyj.png)](https://imgchr.com/i/CRLSyj)

## 四. 问题和可改进之处

### **1. 问题**

### 1）java环境

之前自己配置了java9，Jenkins要求java8环境，下载jdk1.8并且配置环境变量后依然是java9在运行

上网了解到在安装jdk1.9时电脑自动将java.exe、javaw.exe、javaws.exe三个可执行文件复制到了C:\Windows\System32（或C:\ProgramData\Oracle\Java\javapath）目录，而且这个目录在WINDOWS环境变量中的优先级高于JAVA_HOME设置的环境变量优先级

将这三个文件删除，再次执行java -version，发现成功将jdk换成1.8

### 2）自动发送邮件

设置自动发送邮件时，把信息填在“Extended E-mail Notification”就无法发送邮件，填在“邮件通知”之后才能正确发送，没有找到原因

### **2. 可改进之处**

发送邮件和自动部署都在“构建后步骤”里执行，所以邮件只能发送构建结果而不能发送部署结果

## Reference：

[https://blog.csdn.net/pandaphinex/article/details/79175604](https://blog.csdn.net/pandaphinex/article/details/79175604)

[https://blog.csdn.net/liyucheng12131/article/details/54667805](https://blog.csdn.net/liyucheng12131/article/details/54667805)

[https://blog.csdn.net/achuo/article/details/51086599](https://blog.csdn.net/achuo/article/details/51086599)
