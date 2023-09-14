title: 【Spring Security】记一次奇怪的CORS错误 
date: 2021-02-25 04:38:00
categories: Java
tags: []
---
#### 昨天我想给项目加上Spring Security 来实现API的权限控制，刚开始好好的。而且Postman是可以进行请求的，单单一个登录无法实现，报的错误如下图。
![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2021/02/3776466593.png)

#### 苦思冥想了一晚上，都TMD烦死了，搞都搞不掉。第二天转念一想，是不是我的登录的Handler返回json数据的时候没有下面这个头。
> Access-Control-Allow-Origin

#### 然后我就在handler里面加入了这个如下代码
```java
/**
 * 无权访问
 */
@Component
public class LoginDenyHandler implements AccessDeniedHandler {
    @Override
    public void handle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, AccessDeniedException e) throws IOException, ServletException {
        httpServletResponse.setHeader("Access-Control-Allow-Origin", "*");
        httpServletResponse.getWriter().write(JSON.toJSONString(Result.fail(CodeMsg.PERMIT_DENY)));
        httpServletResponse.getWriter().flush();
        httpServletResponse.getWriter().close();
    }
}
```
#### 结果就好了，我也不知道有什么更好的方法，如果有请教教我，谢谢！