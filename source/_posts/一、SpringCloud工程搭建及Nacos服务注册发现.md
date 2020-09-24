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

工程结构我就用的是父子模块的Maven工程 所有的服务都在父工程下方便管理

源码地址 : https://github.com/JoeCoding-cn/spring-cloud


#### 父工程

idea创建一个空的maven项目  用于管理子模块 以及jar包版本管理  父工程pom.xml如下
<!--more-->

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>joe.space</groupId>
    <artifactId>spring-cloud</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <!-- 版本控制其他配置 -->
    <properties>
        <spring-cloud.version>Hoxton.SR6</spring-cloud.version>
        <spring-boot.version>2.3.1.RELEASE</spring-boot.version>
        <java.version>1.8</java.version>
        <encoding>UTF-8</encoding>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.build.version>3.8.1</maven.build.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <lang3.version>3.9</lang3.version>
        <guava.version>29.0-jre</guava.version>
        <hutool.version>5.1.0</hutool.version>
    </properties>

    <dependencyManagement>
      	<!-- SpringBoot SpringCloud版本管理 -->
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <!-- 打包插件依赖管理 -->
        <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
                    <version>${spring-boot.version}</version>
                    <executions>
                        <execution>
                            <goals>
                                <goal>repackage</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>${maven.build.version}</version>
                    <configuration>
                        <source>${maven.compiler.target}</source>
                        <target>${maven.compiler.source}</target>
                        <encoding>${encoding}</encoding>
                    </configuration>
                </plugin>
            </plugins>
        </pluginManagement>
    </build>
</project>
```

父工程结构:

![](https://joe-1253912574.cos.ap-shenzhen-fsi.myqcloud.com/images/Snipaste_2020-07-23_17-46-04.png)



#### 子工程  Comsumer  Producer

在父工程下分别创建comsumer producer 两个模块，同样也是maven工程

![](https://joe-1253912574.cos.ap-shenzhen-fsi.myqcloud.com/images/1595493852425.jpg)



producer  pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>spring-cloud</artifactId>
        <groupId>joe.space</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>producer</artifactId>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
</dependencies>

</project>
```

consumer pom.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>spring-cloud</artifactId>
        <groupId>joe.space</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>consumer</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
    </dependencies>

</project>
```

在启动类加上@EnableDiscoveryClient 注解

最后配置上nacos的地址 以及服务名

```yaml
spring:
  cloud:
    nacos:
      discovery:
        server-addr: 10.211.55.4:8848
  application:
    name: consumer
server:
  port: 8763

```

启动服务  打开nacos管理页面地址 的服务列表 不出意外两个服务就注册上来了

用了nacos之后真的是非常简单

![](https://joe-1253912574.cos.ap-shenzhen-fsi.myqcloud.com/images/Snipaste_2020-07-28_16-42-42.png)

