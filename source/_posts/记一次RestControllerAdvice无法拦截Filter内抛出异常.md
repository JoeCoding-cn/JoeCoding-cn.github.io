categories:
  - [Java]
  - [SpringBoot]
  - [Spring]
tags: 
  - Java
  - Spring
  - SpringBoot
  - Filter
  - 拦截器
---

今天有同事用到Shiro使用JWT的时候在Filter里做身份验证，然后在里面catch捕获并抛出了自定义异常。我们这边是用的RestControllerAdvice做统一异常处理，然后这个异常并没有被RestControllerAdvice所拦截到

#### 原因

请求进来 会按照 filter -> interceptor -> controllerAdvice -> aspect  -> controller的顺序调用

当controller返回异常 也会按照controller -> aspect -> controllerAdvice -> interceptor -> filter来依次抛出 

这种Filter发生的404、405、500错误都会到Spring默认的异常处理。如果你在配置文件配置了server.error.path的话，就会使用你配置的异常处理地址，如果没有就会使用你配置的error.path路径地址，如果还是没有，默认使用/error来作为发生异常的处理地址。如果想要替换默认的非Controller异常处理直接实现Spring提供的ErrorController接口就行了
<!--more-->

#### 解决方案

新建一个ErrorControllerImpl  实现ErrorController 把ErrorPath 指向`error` 再写一个方法把Error抛出 然后Controller全局统一异常处理RestControllerAdvice就能捕获到异常了


```java

import org.springframework.boot.web.servlet.error.ErrorController;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.servlet.http.HttpServletRequest;

/**
 * @author Joe
 * createTime 2020/07/27 14:39
 * mail joe-code@foxmail.com
 */
@Controller
public class ErrorControllerImpl implements ErrorController {

    @Override
    public String getErrorPath() {
        return "/error";
    }

    @RequestMapping("/error")
  	
    public void handleError(HttpServletRequest request) throws Throwable {
        if (request.getAttribute("javax.servlet.error.exception") != null) {
            throw (Throwable) request.getAttribute("javax.servlet.error.exception");
        }
    }
}

```



参考stackoverflow链接 https://stackoverflow.com/questions/34595605/how-to-manage-exceptions-thrown-in-filters-in-spring 

