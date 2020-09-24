toc: true
categories:
  - [Java]
  - [Nacos]
  - [SpringCloud]
tags: 
  - Nacos
  - Java
  - 注册中心
  - SpringCloud
---
### Nacos 单机、集群安装 Mysql持久化

关于Nacos是什么  跟Eureka Consul Etcd ZooKeeper等等注册中心有啥区别。这里就不提了 都能搜得到 ,我们公司使用Nacos做注册中心配置中心已经有一年多的时间了。一直很稳定 这里主要就写一下Nacos单机与集群的安装配置  数据持久化用mysql 这里为了演示只配置了一个mysql服务



#### 环境

nacos提供二进制包跟源代码两种方式 Java版本必须大于1.8 并且配置环境变量

>操作系统 : CentOS Linux release 7.8
>
>Java版本: 大于1.8
>
>Nacos版本 : 1.3.1

<!--more-->

#### Nacos下载

nacos所有版本均托管在github  https://github.com/alibaba/nacos/releases 

选择一个较新的稳定版下载二进制压缩包就可以了 我这里选择的是 `1.3.1`

![](https://joe-1253912574.cos.ap-shenzhen-fsi.myqcloud.com/images/Snipaste_2020-07-24_15-25-05.png)



#### 安装

```
unzip nacos-server-$version.zip 或者 tar -xvf nacos-server-$version.tar.gz
cd nacos/bin
```
##### 配置mysql持久化

解压压缩包之后 在 nacos/conf下有nacos-mysql.sql 脚本 ,新建个数据库导入 1.3.1的脚本有12张表

编辑nacos配置文件`application.properties` 添加如下配置

```yaml
#指定数据源为mysql
spring.datasource.platform=mysql
db.num=1
db.url.0=jdbc:mysql://192.168.124.36:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
#用户名
db.user=root 
#密码
db.password=root
```


##### 单机启动

linux/osx:

`sh startup.sh -m standalone`

Windows:

`cmd startup.cmd`



##### 集群启动

nacos集群非常简单 案例使用三台centos搭建集群 10.211.55.4、10.211.55.5、10.211.55.6 分别在三台机器上安装nacos 新建cluster.conf文件

```
10.211.55.4:8848
10.211.55.5:8848
10.211.55.6:8848
```

保存之后三台机器分别启动 执行

``sh startup.sh`

浏览器打开http://ip:port/nacos 访问nacos可视化页面  例如http://10.211.55.4:8848/nacos     

用户名:密码 默认 nacos : nacos

找到`集群管理 > 节点列表` 就可以看到刚才启动的三台在线了

![](https://joe-1253912574.cos.ap-shenzhen-fsi.myqcloud.com/images/Snipaste_2020-07-24_16-10-55.png)

8848是nacos的默认端口如果想修改则编辑`application.properties` 找到`server.port=8848`修改为你的端口

