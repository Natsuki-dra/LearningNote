# 1.SpringMVC

## 	1.1、初始springMVC

​			springMVC框架以请求为驱动，围绕一个中心Servlet（即**DispatcherServlet**）分派请求及提供其他功能

## 	1.2、 初始化springMVC设置

### 		1.2.1 新建一个Moudle，添加web支持

### 		1.2.2 确定导入SpringMVC依赖

### 		1.2.3 配置web.xml，注册DispatcherServlet

​					只需要在web.xml中注册一个servlet。其他的controller由dispatcherservlet调用

```xml
<!--    注册DispatcherServlet-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>
            org.springframework.web.servlet.DispatcherServlet
        </servlet-class>
<!--        关联一个springmvc的配置文件：spring-servlet.xml-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
        
<!--        启动级别-1-->
<!--        与服务器一起启动-->
        <load-on-startup>1</load-on-startup>
    </servlet>

<!--    / 匹配所有请求，（不包括。jsp）-->
<!--    /* 匹配所有请求，（包括。jsp）-->
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
```

### 		1.2.4 编写springmvc配置文件

​		springmvc-servlet.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd">
    
</beans>
```

### 		1.2.5 添加处理器映射器*

```xml
<bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
```

### 		1.2.6 添加处理器适配器*

```xml
<bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
```

### 		1.2.7 添加视图解析器*

```xml
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
<!--        前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
<!--        后缀-->
        <property name="suffix" value=".jsp"/>
</bean>
```

### 		1.2.8 编写控制器

例：

```java
import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
public class HelloController implements Controller {
    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
        //ModelAndView 模型与视图
        ModelAndView mv = new ModelAndView();
        //调用业务层
        
        //往model中封装对象，
        mv.addObject("msg","HelloSpringMVC");
        //设置view要跳转的视图
        mv.setViewName("hello");
        return mv;
    }
}
```

### 		1.2.9 注册控制器

```xml
<bean id="/hello" class="com.natsuki.controller.HelloController"/>
```

## 1.3 使用注解开发springMVC

springmvc-servlet.xml设置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        https://www.springframework.org/schema/mvc/spring-mvc.xsd">

<!--自动扫描时更改包名即可-->
    <!--无需再配置映射器与解析器-->
    <context:component-scan base-package="com.natsuki.controller"/>
    <mvc:default-servlet-handler />

    <mvc:annotation-driven/>
    
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
        <!--        前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!--        后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>

</beans>
```

​		控制器的编写

```java
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
//控制器的声明
@Controller
//控制器映射
@RequestMapping("/hello")
public class HelloController {
    @RequestMapping("/h1")
    public String hello1(Model model){
        //封装数据
        model.addAttribute("msg","Hello,SpringMVCAnnotation");
        return "hello"; //被视图解析器处理
        
    }
}

```

## 1.4 Controller配置总结

​	实现接口Controller定义控制器是一个较老的方法，**尽量不要使用**

​	缺点：一个控制器只有一个方法，如果多个方法则需要定义多个Controller

​				定义的方法比较麻烦

​	**Controller中的方法返回值会被拼接为一个定位文件的url，会跳转到具体的页面**

## 1.5 RequestMapping

​	用于映射url到控制器类或一个特定的处理程序方法

​	会根据url来执行对应的程序，用于识别。

​	**一般都是在方法上注解**

## 1.6 RestFul风格

一个资源定位及资源操作的风格

最大的特点是安全，隐藏参数名

传统风格：www.example.com/web?method=abc&name=xxx

restful风格：www.example.com/web/abc/xxx

需要先用@PathVariable对需要的变量进行注解，requesMapping中的值要对被注解的值设置占位符

可以在method中控制请求类型

```java
@Controller
public class RestFulController {
    @RequestMapping(value = "/add/{a}/{b}" method = RequestMethod.GET)
    public String add(@PathVariable int a,@PathVariable int b, Model model){

        model.addAttribute("msg",a+b);

        return "test";
    }
}

```

### 		1.6.1 请求类型的其他方法

```java
//将RequestMapping的注解改为
@GetMapping
@PostMapping
@PutMapping
```

## 1.7 ModelAndView

设置ModelAndView对象，根据view的名称，和视图解析器跳到指定的页面

### 		1.7.1 通过设置ServletAPI，不需要视图解析器

例：

```java
@Controller
public class ResultGo {

    @RequestMapping("/result/t1")
    public void test1(HttpServletRequest request, HttpServletResponse response)throws IOException {
        response.getWriter().println("Hello,Spring By servletAPI");
    }

    @RequestMapping("/result/t2")
    public void test2(HttpServletRequest request,HttpServletResponse response)throws IOException{
        response.sendRedirect("/index.jsp");
    }
    @RequestMapping("/result/t3")
    public void test3(HttpServletRequest request,HttpServletResponse response)throws Exception{
        request.setAttribute("msg","/result/t3");
        request.getRequestDispatcher("/WEB-INF/jsp/test.jsp").forward(request,response);
    }
}
```

### 		1.7.2 通过SpringMVC来实现转发和重定向

```java
	@RequestMapping("/result/t4")
    public String test4(Model model){
        return "hello";
    }

    @RequestMapping("/result/t5")
    public String test5(Model model){
        //转发
        return "forward:/index.jsp";
    }

    @RequestMapping("/result/t6")
    public String test6(Model model){
        //重定向
        return "redirect:/index.jsp";
    }
```

## 1.8 接受请求参数

### 	1.8.1 接受基本字段

使用注解RequestParam("参数名"),在方法名前面进行注解

```java
@Controller
public class ReceiveController {

    @RequestMapping("rc")
    public String getName(@RequestParam("username") String name,
                          @RequestParam("password") String psd ,
                          Model model){

        System.out.println(name);
        System.out.println(psd);

        model.addAttribute("msg",name+"<br>"+psd);


        return "test";
    }
}
```

*加入只填写了一个参数（方法本身需要多个参数），会进行报错

这种情况下不会自动为没有填的参数设为null

如下

```url
http://localhost:8080/springmvc_03_annotation_war_exploded/rc?username=Natsuki
```

HTTP Status 400 – 错误的请求

------

**Type** Status Report

**消息** **Required String parameter 'password' is not present**

**描述** 由于被认为是客户端对错误（例如：畸形的请求语法、无效的请求信息帧或者虚拟的请求路由），服务器无法或不会处理当前请求。

------

Apache Tomcat/9.0.33

### 	1.8.2 接受对象

​			传递参数：

```url
http://localhost:8080/springmvc_03_annotation_war_exploded/user/t1?id=1&name=natsuki&age=7
```

​			控制器：

```java
@Controller
@RequestMapping("/user")
public class UserController {

    @GetMapping("/t1")
    public String test1(User user){
        System.out.println(user);
        return "test";
    }
}
```

​			bean写法：

​			*要导入lombok包，项目的版本为1.18.4（低版本不兼容10版本以上的java）

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private int id;
    private String name;
    private int age;

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

​	原理：

​		1.接收前端用户传递的参数，判断参数的名字，假设名字在直接在方法上，可以直接使用

​		2.假设传递的是一个对象，匹配User对象中的字段名，如果名字一致则匹配。前端传回参数名必须与对象中的字段名一致。



​	ModelMap: 继承了LinkedHashMap，具有LinkedHashMap全部功能

​	Model： 精简版

### *关于位于web目录下的jsp文件表单请求无法传达的问题

​	问题：

​		提交前的url：

```url
http://localhost:8080/springmvc_03_annotation_war_exploded/
```

​		提交后的url：

```url
http://localhost:8080/rc
```

​		本应该抵达的url为：

```url
http://localhost:8080/springmvc_03_annotation_war_exploded/rc
```

​	jsp中的action指定的是同级的文件夹下的目录，并将请求发送到项目目录下的控制器

解决方法：

​			在action中对应控制器前面添加${pageContext.request.contextPath}

```html
<form action="${pageContext.request.contextPath}/rc" method="post">
      <input type="text" name="username"/>
      <input type="text" name="password"/>
      <input type="submit" name="button"/>
    </form>
```

### 1.8.3 乱码

​	post方法发送中文数据乱码

​	发送前：

![post中文字符乱码（1）](D:\STUDY\CS\学习笔记\post中文字符乱码（1）.png)

​	发送后：

​	![post中文字符乱码（2）](D:\STUDY\CS\学习笔记\post中文字符乱码（2）.png)

​	原因：后端java代码层出现问题

​	不能在控制器中对encoding进行控制，

​	**需要写一个filter，在请求到达web容器时就被过滤一遍**

​	继承servlet.Filter

```java
import javax.servlet.*;
import java.io.IOException;

public class EncodingFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        servletRequest.setCharacterEncoding("utf-8");
        servletResponse.setCharacterEncoding("utf-8");

        filterChain.doFilter(servletRequest,servletResponse);
    }

    @Override
    public void destroy() {

    }
}
```

​	web.xml中进行配置

```xml
<filter>
        <filter-name>Encoding</filter-name>
        <filter-class>com.natsuki.filter.EncodingFilter</filter-class>
    </filter>

    <filter-mapping>
        <filter-name>Encoding</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```

**url-pattern中应该是/*，jsp的页面必须要带有\*号**



#### 	使用springmvc的过滤器

```xml
<filter>
        <filter-name>encoding</filter-name>
        <filter-class>
            org.springframework.web.filter.CharacterEncodingFilter
    	</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>

    <filter-mapping>
        <filter-name>encoding</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```

# 2. json

​		[JSON](https://baike.baidu.com/item/JSON)([JavaScript](https://baike.baidu.com/item/JavaScript) Object Notation, JS 对象简谱) 是一种轻量级的数据交换格式。它基于 [ECMAScript](https://baike.baidu.com/item/ECMAScript) (欧洲计算机协会制定的js规范)的一个子集，采用完全独立于编程语言的文本格式来存储和表示数据。简洁和清晰的层次结构使得 JSON 成为理想的数据交换语言。 易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率。



​	**JSON 是 JS 对象的字符串表示法，它使用文本表示一个 JS 对象的信息，本质是一个字符串。**

​	用的是键值对

要实现从JSON字符串转换为JS对象，使用 JSON.parse() 方法：

```javascript
var obj = JSON.parse(``'{"a": "Hello", "b": "World"}'``);
//结果是 {a: 'Hello', b: 'World'}
```

要实现从JS对象转换为JSON字符串，使用 JSON.stringify() 方法：

```javascript
var json = JSON.stringify({a: ``'Hello'``, b: ``'World'``});
//结果是 '{"a": "Hello", "b": "World"}'
```



## 	2.1 json解析工具(Jackson)

maven依赖：

```xml
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.11.1</version>
        </dependency>
```

**对方法注解@ResponseBody后，方法只会返回一个字符串，而不会通过模型解析器**

**对类注解RestController后，类内所有方法只会返回字符串，都不会通过模型解析器**

简单应用

```java
@RequestMapping("/j1")
    @ResponseBody//不会走视图解析器，直接返回一个字符串
    public String jsonTest1() throws JsonProcessingException {
        ObjectMapper mapper = new ObjectMapper();

        User user = new User("natsuki",20,"male");

        String str = mapper.writeValueAsString(user);
        return str;
    }
```

**用以上方法传递中文字符时仍然会产生乱码（配了过滤器也不行）**

注解可改为：

```java
@RequestMapping(value = "/j1" , produces = "application/json;charactor=utf-8")
```

*但是这样很麻烦

最好方法：

**在配置文件中springmvc-servlet.xml中修改\<mvc:annotation-driver/\>**

```xml
<mvc:annotation-driven>
        <mvc:message-converters register-defaults="true">
            <bean 		 class="org.springframework.http.converter.StringHttpMessageConverter">
                <constructor-arg value="utf-8"/>
            </bean>
            <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
                <property name="objectMapper">
                    <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                        <property name="failOnEmptyBeans" value="false"/>
                    </bean>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
```

还有alibaba的fastjson包

只需要使用静态方法

```java
JSON.writeJsonString(Object object);
```

