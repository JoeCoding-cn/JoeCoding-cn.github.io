toc: true
categories:
  - [Java]
  - [SpringCloud,Feign]
tags: 
  - Feign
  - Java
  - SpringCloud
---

### SpringCloud Feign调用报错feign.RetryableException: too many bytes written executing

版本:

> SpringCloud : Greenwich.SR5
>
> SpringBoot : 2.1.9.RELEASE
>
> SpringCloudAlibaba : 2.1.0.RELEASE



看到这个错误第一时间我也是打开百度/Goole 但是搜出来的，无一例外 基本都是添加feign增强包 feign-httpclient 或者feign-okhttp 包;
<!--more-->

![](https://joe-1253912574.cos.ap-shenzhen-fsi.myqcloud.com/images/Snipaste_2020-07-05_12-18-19.png)






无奈之下只好一步步debug 发现是把request.body 写入到流时发生的错误.` java.io.IOException: insufficient data written`

![](https://joe-1253912574.cos.ap-shenzhen-fsi.myqcloud.com/images/Snipaste_2020-07-05_12-41-43.png)

后面搜到body是跟`Content-Length` 有关系的...  附上博主链接 https://my.oschina.net/u/4410077/blog/3323588 看了之后 原来发生这个问题的原因跟我一样，因为服务之间调用需要携带一些用户信息之类的 所以实现了Feign的`RequestInterceptor`拦截器复制请求头，复制的时候是所有头都复制的,可能导致Content-length长度跟body不一致. 所以只需要判断如果是Content-length就跳过 

原配置 : 

```java
/**
 * @author Joe
 * createTime 2020/06/10 18:13
 */
@Log4j2
@Configuration
public class FeignConfiguration implements RequestInterceptor {


    @Override
    public void apply(RequestTemplate template) {
        ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder
                .getRequestAttributes();
        HttpServletRequest request = attributes.getRequest();
        Enumeration<String> headerNames = request.getHeaderNames();
        if (headerNames != null) {
            while (headerNames.hasMoreElements()) {
                String name = headerNames.nextElement();
                String values = request.getHeader(name);
                template.header(name, values);
            }
        } else {
            log.info("feign interceptor error header:{}", template);
        }
    }
}
```

修改之后:

```java
/**
 * @author Joe
 * createTime 2020/06/10 18:13
 */
@Log4j2
@Configuration
public class FeignConfiguration implements RequestInterceptor {


    @Override
    public void apply(RequestTemplate template) {
        ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder
                .getRequestAttributes();
        HttpServletRequest request = attributes.getRequest();
        Enumeration<String> headerNames = request.getHeaderNames();
        if (headerNames != null) {
            while (headerNames.hasMoreElements()) {
                String name = headerNames.nextElement();
                String values = request.getHeader(name);
                // 跳过 content-length
                if (name.equals("content-length")){
                    continue;
                }
                template.header(name, values);
            }
        } else {
            log.info("feign interceptor error header:{}", template);
        }
    }
}

```

content-length详解参考文章 ：https://juejin.im/post/5d772cb4e51d453b5f1a0502