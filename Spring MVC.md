# Spring MVC

# 1. 相关知识 

- MVC:它是一种架构，分成Model-View-Controller

- 主流MVC架构的实现框架，struts和springmvc

- Servlet流程

  ![img](img/9.jpg)

# 2. 代码回顾

- Model 模型

User.java

```java
@Data
public class User {
	private String uname;
	private String upass;
}
```

- View视图

```jsp

<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>登录</title>
</head>
<body>
	<form action="LoginServlet" method="post">
		uname:<input type="text" name="uname" /> 
        upass:<input type="password" name="upass" />
		<input type="submit" value="登录" />
	</form>
</body>
</html>
```

- 控制器

```java
@WebServlet("/LoginServlet")
public class LoginServlet extends HttpServlet {


    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doPost(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        

        req.setCharacterEncoding("UTF-8");

        User user = new User();
        try {
            Map<String,String[]> map =  req.getParameterMap();
            BeanUtils.populate(user,req.getParameterMap());
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        }

        //调用业务
        //resp.setCharacterEncoding("UTF-8");
        resp.setContentType("text/html;charset=UTF-8");
        PrintWriter out = resp.getWriter();

        out.println("姓名:" + user.getUname() + "，密码" + user.getUpass());
        out.flush();
        out.close();
    }


}
```

# 3. Spring MVC

## 3.1. 介绍

- Spring MVC是Spring框架中提供的MVC架构
- 它包含了下面几个模块
  - 前端控制器
  - 处理映射器和适配器
  - 视图解析器
  - 页面控制器

## 3.2. 优点

- 简单：支持注解和XML两种配置
- 高效：单例。将Controller交由给IOC容器托管
- SpringMVC和Spring无缝对接，亲生

## 3.3. 流程

![](img/6.png)

1） 用户请求-->DisaptcherServlet前端控制器，控制器委托处理映射器进行处理，它是一个全局的流程控制

2）DisaptcherServlet前端控制器-->处理映射器(HandlerMapping)，处理映射器会把请求映射为HandleExecutionChain对象

3）DisaptcherServlet前端控制器-->处理适配器(HandlerAdapter)，使处理支持多种类型的处理，最终适配到对应的自定义的Controoler，返回是ModelAndView的对象。

4）ModelAndView它是处理适配器返回对象，model是包含的数据，view是视图名，

5）view-->渲染 。把视图要加上前缀和后缀，找到对应的视图

6）在对应的视图界面上，显示内容。内容可以从model中获取。

7）最终把页面响应给用户

# 4. SpringMVC之HelloWorld

- Maven的web项目

- 加pom.xml

  ```xml
  <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.1.5.RELEASE</version>
      </dependency>
  
      <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.6</version>
      </dependency>
  ```

  

- 前端控制器 web.xml

  ```xml
  <!--  前端控制器-->
    <servlet>
      <servlet-name>springmvc</servlet-name>
      <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
      <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring-mvc.xml</param-value>
      </init-param>
  <!--    web服务器启动,springmvc控制器即启动-->
      <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
      <servlet-name>springmvc</servlet-name>
      <url-pattern>/</url-pattern>
    </servlet-mapping>
  
  ```

  

- 处理映射器和适配器  spring-mvc.xml

  ```xml
  <!--    请求映射器-->
      <bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping"></bean>
  
  <!--    请求适配器-->
  <bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter"></bean>
  ```

  

- 视图解析器 spring-mvc.xml

  ```xml
  <!--    视图解析器-->
      <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
          <property name="prefix" value="/WEB-INF/jsp/"/>
          <property name="suffix" value=".jsp"/>
      </bean>
  ```

  

- 页面控制器 net.wanho.controller.HelloController.java

```java
@Controller
public class HelloController {

    @RequestMapping("/hello")
    //返回的视图名，它会自动使用视图解析，拼上前缀(/WEB-INF/)和后缀(.jsp)
    public String sayHello(){
        return "login";
    }
    
    @RequestMapping("/hello2")
    //返回的视图名，它会自动使用视图解析，拼上前缀(/WEB-INF/)和后缀(.jsp)
    public ModelAndView sayHello2(){
        ModelAndView mv = new ModelAndView();
        //同一个请求
        mv.addObject("uname","张三");
        mv.setViewName("login");
        return mv;
    }
}
```

## 4.1.  Controller中返回对象

- String类型，返回的是视图名
- ModelAndView类型，返回的是Model和视图名,Model的值的作用域是request范围

##  4.2. request范围保存值的3种写

- 第一种存放在Model中，它也有两种写法

```java
@RequestMapping("/hello")
//返回的视图名，它会自动使用视图解析，拼上前缀(/WEB-INF/)和后缀(.jsp)
public ModelAndView sayHello(){
    ModelAndView mv = new ModelAndView();
    //同一个请求
    mv.addObject("uname","张三");
    mv.setViewName("login");
}

@RequestMapping("/hello4")
//返回的视图名，它会自动使用视图解析，拼上前缀(/WEB-INF/)和后缀(.jsp)
public String sayHello4(Model model){
    //同一个请求
    model.addAttribute("uname","张三4");
    return "login";
}

```

- 第二种存放HttpServletRequest中，要依赖于Servlet，不推荐

  ```java
  @RequestMapping("/hello2")
      //返回的视图名，它会自动使用视图解析，拼上前缀(/WEB-INF/)和后缀(.jsp)
      public String sayHello2(HttpServletRequest request){
          //同一个请求
          request.setAttribute("uname","张三2");
          return "login";
      }
  ```

- 第三种存放Map中

  ```java
  @RequestMapping("/hello3")
      //返回的视图名，它会自动使用视图解析，拼上前缀(/WEB-INF/)和后缀(.jsp)
      public String sayHello3(Map map){
          //同一个请求
          map.put("uname","张三3");
          return "login";
      }
  ```

## 4.3. @RequestMapping("/xxx") 说明  

- 请求映射，它即能处理get请求，也能处理post请求

- 也可以通过method去设置它的请求方式

- 如果限定了Get请求，可以使用@GetMapping的注解与其功能就一致，类似的还有@PostMapping，它是Spring4.3之后出现

  ```java
  @RequestMapping(value = "/hello",method = RequestMethod.GET)
  //@GetMapping("/hello")
  ```

## 4.4. 语法方式说明 

- 标准页面请求，只有两种方式

  - get请求：
    - 链接
    - 直接输入url访问
    - 表单method设置成get
  - post请求
    - 只有在表单中把method设置成post方式

- 但在Spring中，推荐使用REST风格请求路径，此方式要求使用多种请求方式
  - GET     :  查询

  - POST   : 新增

  - PUT    :   修改

  - DELETE: 删除

  - 问题来，网页请求不认识这么多请求方式啊，只认识get和post

    - 其它SpringMVC还只是识别标准的get和post

    - 但把POST、PUT和DELETE都设置成post请求，然后通过一个隐藏域来区分

      ```html
      <form method="post" action="">
          <inpu type="hidden" name="_method" value="delete">
      ```

## 4.5. 请求映射路径问题

- @RequestMapping路径 
  -  @RequestMapping("/路径")
  -  @RequestMapping(value="/路径")
  -  @RequestMapping(path="/路径")
  - value和path都可以
  - /代表绝对路，是相当于项目的根目录，一般要加上/
- 路径形式
  - 普通形式
  - rest风格
  - ant形式
    - /?一个,示例/a ，/1
    - /*一级，示例/abc,/123
    - /**多级，示例/a/b

## 4.6. 请求参数

- 普通传值
  - 请求参数名与方法参数名一致，就会自动匹配，同时也会做类型转换
  - 如果请求参数与方法参数名不一致，要加上  @RequstParam("请求参数名")-->  方法参数名
- 对象传值
  - 请求参数名与对象参数中的属性一致，就会自动赋值给对象属性，同时也会做类型转换

```java
@Data
public class User {
    private String uname;
    private String upass;
    private Integer age;
    @DateTimeFormat(pattern = "yyyy-MM-dd")
    private Date birth;
}

```

```java
//http://localhost:8080/login?uname=张三&upass=123
    @GetMapping("/login")
    public String login1(String uname,String upass){
        System.out.println(uname+"-"+upass);
        return "hello";
    }

    //http://localhost:8080/login2?uname=张三&upass=123
    @GetMapping("/login2")
    public String login1(User user){
        System.out.println(user.getUname()+"-"+user.getUpass());
        return "hello";
    }

    //http://localhost:8080/login?uname=张三&upass=123
    @GetMapping("/login3")
    public String login3(@RequestParam(name = "uname") String name, String upass){
        System.out.println(name+"-"+upass);
        return "hello";
    }

    //http://localhost:8080/login4?uname=张三&upass=123&age=108&birth=2018-8-8
    @GetMapping("/login4")
    public String login4(User user){
        System.out.println(user);
        return "hello";
    }
```

# 5. 请求映射

## 5.1. 请求方式,见上面

## 5.2. 请求参数,见上面

## 5.3. 请求路径，见上面

## 5.4. ajax请求

- 页面
  - 页面向控制器传值有三种

```javascript
 $("button:eq(0)").click(function () {
     $.ajax({
         url:"/user/login3?uname=admin&upass=admin",
         //url:"/user/login3",
         //data:{uname:"admin",upass:"admin"},
         //data:"uname=admin&upass=admin",
         type:"post",
         dataType:"json",
         success:function(data){
             if(data.code==200)
                 $("#res").text(data.msg);
             else
                 alert("登录失败");
         },
         error:function () {

         }
     });
 });
```

- 控制器

  - 在方法上使用  @ResponseBody，但表此方法不是跳转到视图，而直接响应字符至页面

  ```java
  @Controller
  @RequestMapping("/user")
  public class LoginController {
  
      @RequestMapping("/login")
      //此注解一加，就直接返回字符串到页面上了
      @ResponseBody
      public String login(String uname,String upass){
          if("admin".equals(uname) && "admin".equals(uname))
              return "login success";
          return "login fail";
      }
  
      @RequestMapping("/login2")
      //此注解一加，就直接返回字符串到页面上了
      @ResponseBody
      public String login2(String uname,String upass){
          if("admin".equals(uname) && "admin".equals(uname))
              return "{\"msg\":\"login success\"}";
          return "{\"msg\":\"login fail\"}";
      }
  
      @RequestMapping("/login3")
      //此注解一加，就直接返回字符串到页面上了
      @ResponseBody
      public String login3(String uname,String upass){
          if("admin".equals(uname) && "admin".equals(uname)){
              AjaxResult ajaxResult = new AjaxResult();
              ajaxResult.setMsg("login success");
              ajaxResult.setCode(200);
              return JSON.toJSONString(ajaxResult);
          }else{
              AjaxResult ajaxResult = new AjaxResult();
              ajaxResult.setMsg("login fail");
              ajaxResult.setCode(500);
              return  JSON.toJSONString(ajaxResult);
          }
      }
  }
  ```

  - @ResponseBody也可以在类上，代表此类下面所有的方法都是直接响应内容到页面

    但如果有个别方法想响应到视图，可以使用ModelAndView返回类型

  ```java
  @Controller
  @RequestMapping("/user2")
  //代表此类中的有的方法都直接响应到页面，都不走视图
  @ResponseBody
  public class LoginController2 {
  
      //直接响应内容
      @GetMapping("/login")
      public String login(){
          return "login processor";
      }
      
      //响应到视图
       @GetMapping("/login4")
      public  ModelAndView textMine() throws IOException {
          ModelAndView mv = new ModelAndView();
          mv.setViewName("hello");
          return mv;
      }
  }
  ```

## 5.5. 日期类型

- 日期类型也能自动转换，但前提你得告知啥格式

- 配置方法如下，有两种方式

  - po中加上

    ```java
    //输入入格式
    @DateTimeFormat(pattern = "yyyy-MM-dd")
    //输出格式
    @JSONField(format = "yyyy-MM-dd")
    ```

  - xml中加上

    ```xml
    <!--    上面两行，可以用这行两简写-->
        <mvc:annotation-driven>
           <mvc:message-converters>
               <ref bean="fastjsonBean"/>
           </mvc:message-converters>
        </mvc:annotation-driven>
    
        <!--使用fastjson-->
        <bean id="fastjsonBean" class="com.alibaba.fastjson.support.spring.FastJsonHttpMessageConverter">
            <property name="supportedMediaTypes">
                <list>
                    <value>text/html;charset=UTF-8</value>
                    <value>application/json;charset=UTF-8</value>
                </list>
            </property>
            <property name="fastJsonConfig">
                <bean class="com.alibaba.fastjson.support.config.FastJsonConfig">
                    <property name="features">
                        <list>
                            <value>AllowArbitraryCommas</value>
                            <value>AllowUnQuotedFieldNames</value>
                            <value>DisableCircularReferenceDetect</value>
                        </list>
                    </property>
                    <property name="dateFormat" value="yyyy-MM-dd HH:mm:sss"/>
                </bean>
    
            </property>
        </bean>
    ```

    ```java
    @GetMapping("/user2")
    @ResponseBody
    public User add2(User user){
        //直接返回对象，fastjsonBean转application/json;同时dateFormat做了格式化
        //不需要JSON.toJSONString(user)
        return user;
    }
    ```

    

## 5.6. 获取请求头值

## 5.7. 获取cookie的值

## 5.8. 获取请求参数和头中 必须包含参数名或值

```java
   @GetMapping("/header")
    public String header(@RequestHeader("User-Agent") String userAgent){
        return userAgent;
    }

    @GetMapping("/cookie")
    public String cookie(@CookieValue("JSESSIONID") String sessionId){
        return sessionId;
    }

    @GetMapping(value="/queryParam",params = {"a","b=2"},headers = {"Cookie"})
    public String queryParam(String a,String b){
        return ""+a+b;

    }
```

## 5.9.  path变量

```java
@GetMapping("/queryParam2/{name}")
public String queryParam2(@PathVariable String name){
    return name;
}
```



## 5.10. RESTful风格 

- REST:Representational State Transfer : 表现层状态转化

- 它是一种浏览网页风格，不是框架，不是架构，不是插件

  /user/1001     GET 查询

  /user                POST新增

  /user/1001      PUT修改

  /user/1001     DELETE删除

- 如果在SpringMVC中支持Rest风格

  - 加入filter+加入隐藏域就可以了

    ```xml
    <!--  支持rest风格的过滤器-->
      <filter>
        <filter-name>filter</filter-name>
        <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
      </filter>
      <filter-mapping>
        <filter-name>filter</filter-name>
        <url-pattern>/*</url-pattern>
      </filter-mapping>
    
    ```

    ```html
    <form action="/user" method="get">
        <input type="submit" value="查所有">
    </form>
    <form action="/user/1001" method="get">
        <input type="submit" value="查某个">
    </form>
    
    <form action="/user" method="post">
        <input type="text" name="uname" value="admin">
        <input type="text" name="upass" value="admin">
        <input type="submit" value="新增">
    </form>
    
    <form action="/user/1001" method="post">
        <input type="hidden" name="_method" value="PUT"/>
        <input type="submit" value="修改">
    </form>
    
    <form action="/user/1001" method="post">
        <input type="hidden" name="_method" value="DELETE"/>
        <input type="submit" value="删除">
    </form>
    ```

    ```java
    @Controller
    @ResponseBody
    public class UserController {
    
        @GetMapping("/user")
        public String  selALl(){
    
            return "select all";
        }
    
        @GetMapping("/user/{id}")
        public String  selOne(@PathVariable("id") Integer id){
            return "select one"+id;
        }
    
        @PostMapping("/user")
        public String add(User user){
            return JSON.toJSONString(user);
        }
    
        @PutMapping("/user/{id}")
        public String  update(@PathVariable("id") Integer id){
            return "update one"+id;
        }
    
        @DeleteMapping("/user/{id}")
        public String  delete(@PathVariable("id") Integer id){
            return "delete one"+id;
        }
    }
    ```

## 5.11. consumes和produces属性

- 一般设置请求和响应类型: 限制请求参数必须为xx类型，限制响应类型必须为xx类型
- produces: 提供方（controller）-->响应
  - 响应编码
  - 响应类型
- consumes: 消息方 (*.jsp) -->请求
  - 请求类型

ajax.jsp

```javascript
$.ajax({
    url:'/ajaxRequest',
    type:'post',
    contentType:'application/json',
    data:'{"uname":"zsf"}',
    dataType:'json',
    success:function (data) {
    alert(data.msg);
    }
})
```



controller.java

```java
@ResponseBody
@Controller
public class AjaxController {

    @PostMapping(value = "/ajaxRequest",produces = "charset=utf-8;application/json",consumes = "application/json")
    public AjaxResult request(@RequestBody User user){
        System.out.println(user);
       AjaxResult ajaxResult = new AjaxResult();
        ajaxResult.setCode(200);
        ajaxResult.setMsg("请求成功");
        return ajaxResult;

    }
}
```

# 6. 静态资源 

- 处理静态资源有两种方式
  - springmvc来处理
  - servlet处理

## 6.1. 直接跳转视图名。（它并不是静态资源的解决方式）

```xml
<mvc:view-controller path="/xx" view-name="home">
```

## 6.2. servlet处理

```xml
<!--    springmvc先拦截，匹配不了，再交给servlet-->
<mvc:default-servlet-handler/>
```

## 6.3. springmvc来处理

```xml
<mvc:resources mapping="/img/**" location="/img/"/>
<mvc:resources mapping="/js/**" location="/js/"/>
<mvc:resources mapping="/css/**" location="/css/"/>

```

## 6.4. 请示转发和请求重定向

```java
@Controller
public class ForwardAndRedirectController {

    @RequestMapping("/redirect")
    public String redirect(){
        //重定向
        return "redirect:success.jsp";
    }
    
    //转发1
    @RequestMapping("/forward")
    public String forward(){
        return "success";
    }
    
    //转发2
     @RequestMapping("/forward2")
    public String forward(){
        return "forward:success";
    }
}
```

# 7. 数据绑定、类型转换和类型格式化

## 7.1. 数据绑定

- SpringMVC内置了一系列的类型前置处理，一般够用了，除了特殊定制

- 示例

  - 025-1234578
  - PhoneVo : areaCode-telphoneNumber

- 有两种级别：

  - 当前Controller有效

    ```java
    @Controller
    public class PhoneController {
    
    //    @InitBinder
        public void  before(WebDataBinder binder){
            binder.registerCustomEditor(PhoneVo.class,new PropertyEditorSupport(){
                @Override
                public String getAsText() {
                   PhoneVo vo = (PhoneVo)getValue();
                   return vo.toString();
                }
    
                @Override
                public void setAsText(String text) throws IllegalArgumentException {
                    String[] strs = text.split("-");
                    PhoneVo vo = new PhoneVo();
                    vo.setAreaCode(strs[0]);
                    vo.setPhoneNumber(strs[1]);
                    setValue(vo);
                }
            });
            System.out.println("before");
        }
    
        @PostMapping("/reg")
        @ResponseBody
        public String reg(@RequestParam("phone") PhoneVo phone){
            return phone.toString();
        }
    }
    ```

    

  - 所有Controller有效

    ```java
    @ControllerAdvice
    public class GlobalAdiviceController {
        @InitBinder
        public void  before(WebDataBinder binder){
            binder.registerCustomEditor(PhoneVo.class,new PropertyEditorSupport(){
                @Override
                public String getAsText() {
                    PhoneVo vo = (PhoneVo)getValue();
                    return vo.toString();
                }
    
                @Override
                public void setAsText(String text) throws IllegalArgumentException {
                    String[] strs = text.split("-");
                    PhoneVo vo = new PhoneVo();
                    vo.setAreaCode(strs[0]);
                    vo.setPhoneNumber(strs[1]);
                    setValue(vo);
                }
            });
            System.out.println("before22");
        }
    
    }
    
    ```

## 7.2. 类型转换

- Springmvc中内置N多类型转换

  ```java
  ConversionServiceconverters = 
  - java.lang.Boolean  ->  java.lang.String:  org.springframework.core.convert.support.ObjectToStringConverter@f874ca
  - java.lang.Character-> java.lang.Number: CharacterToNumberFactory@f004c9
  - java.lang.Character-> java.lang.String: ObjectToStringConverter@68a961
  - java.lang.Enum-> java.lang.String: EnumToStringConverter@12f060a
  - java.lang.Number-> java.lang.Character: NumberToCharacterConverter@1482ac5
  - java.lang.Number-> java.lang.Number: NumberToNumberConverterFactory@126c6f
  - java.lang.Number-> java.lang.String: ObjectToStringConverter@14888e8
  - java.lang.String-> java.lang.Boolean: StringToBooleanConverter@1ca6626
  - java.lang.String-> java.lang.Character: StringToCharacterConverter@1143800
  - java.lang.String-> java.lang.Enum: StringToEnumConverterFactory@1bba86e
  - java.lang.String-> java.lang.Number: StringToNumberConverterFactory@18d2c12
  - java.lang.String-> java.util.Locale: StringToLocaleConverter@3598e1
  - java.lang.String-> java.util.Properties: StringToPropertiesConverter@c90828
  - java.lang.String-> java.util.UUID: StringToUUIDConverter@a42f23
  - java.util.Locale-> java.lang.String: ObjectToStringConverter@c7e20a
  - java.util.Properties-> java.lang.String: PropertiesToStringConverter@367a7f
  - java.util.UUID-> java.lang.String: ObjectToStringConverter@112b07f ......
  ```

  ```xml
   <mvc:annotation-driven conversion-service="conversionService"/>
  
  <!--    自定义类型转换器-->
      <bean id="conversionService" class="org.springframework.format.support.FormattingConversionServiceFactoryBean">
          <property name="converters">
              <list>
                  <bean class="net.wanho.convert.StringToDateConverter"/>
                  <bean class="net.wanho.convert.StringToPhoneVo"/>
              </list>
          </property>
  
      </bean>
  ```

StringToDateConverter.java

```java
public class StringToDateConverter implements Converter<String, Date> {
    public Date convert(String s) {
        try {
            return new SimpleDateFormat("yyyy-MM-dd").parse(s);
        } catch (ParseException e) {
            throw new RuntimeException(e);
        }
    }
}
```

StringToPhoneVo.java

```java
public class StringToPhoneVo implements Converter<String, PhoneVo> {
    public PhoneVo convert(String s) {
        System.out.println("StringToPhoneVo类型转换");
        String [] strs = s.split("-");
        PhoneVo vo = new PhoneVo();
        vo.setAreaCode(strs[0]);
        vo.setPhoneNumber(strs[1]);
        return vo;
    }
}

```

## 7.3. 类型格式化

- 日期类型:@DateTimeFormat(pattern="")
- 数值类型: @NumberFormate
- 百分类型: @NumberFormate
- 货币类型: @NumberFormate

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Employee {
    private Integer id;
    private String name;
    /*
        ISO.DATE        yyyy-MM-dd
        ISO.TIME        hh:mm:ss:SSS
        ISO.DATA_TIME   2000-10-31T01:30:00.000-05:00
        pattern 自定义
    * */
//    @DateTimeFormat(pattern = "yyyy-MM-dd")
    @DateTimeFormat(iso = DateTimeFormat.ISO.DATE)
    private Date birth;
    @NumberFormat(pattern = "#,###,###.#")
    private Double salary;
}

```

```java
@Controller
public class EmployeController {

    @GetMapping("/emp")
    @ResponseBody
    public String list(Employee emp){
        return emp.toString();
    }
}

```



# 8. 拦截器

- 使用场景 
  - 权限检查
  - 日志记录
  - 性能检测

- 实现拦截四步曲

  - pom.xml加上Servlet的jar

    ```xml
     <dependency>
          <groupId>javax.servlet</groupId>
          <artifactId>javax.servlet-api</artifactId>
          <version>4.0.1</version>
          <scope>provided</scope>
        </dependency>
    ```

    

  - 编写功能类实现HandleInteceptor或继承HandleInteceptorAdapter

    ```xml
    public class EffectInteceptor extends HandlerInterceptorAdapter {
    
        private long beginTime;
    
    
        public boolean preHandle(javax.servlet.http.HttpServletRequest request, javax.servlet.http.HttpServletResponse response, Object handler) throws Exception {
            System.out.println("preHandle");
             beginTime = new Date().getTime();
             //如果前置处理返回true，接着往后执行，否则程序返回
            return true;
        }
    
    
        public void afterCompletion(javax.servlet.http.HttpServletRequest request, javax.servlet.http.HttpServletResponse response, Object handler, Exception ex) throws Exception {
            System.out.println("afterCompletion");
            long costTime = new Date().getTime()-beginTime;
            System.out.println(costTime);
        }
    }
    ```

    

  - 在xml中进行配置

    ```xml
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/*"/>
            <bean class="net.wanho.inteceptor.EffectInteceptor"/>
        </mvc:interceptor>
    </mvc:interceptors>
    ```

# 9. 乱码

## 9.1. servlet中乱码处理

- request

  - post

    ```java
    request.setCharacterEncoding("UTF-8")
    ```

  - get  

  ```java
  在tomcat8.5之前 在地址栏中请求的编码永远是ISO-8859-1
  request.setCharacterEncoding("UTF-8"); //还不行
  //第一种方式
  String uname = request.getParamater("uname");
  uname = new String(uname.getBytes("ISO-8859-1"),"UTF-8");
  
  //第二种方式
  <Connector port="8080" protocol="HTTP/1.1"
                 connectionTimeout="20000"
                 redirectPort="8443"  URIEncoding="UTF-8"/>
                 
  在8.5.tomcat之，修复了这个bug,只需要设置
   request.setCharacterEncoding("UTF-8");
  ```

  

- response

  ```java
  //response.setCharacterEncoding("UTF-8");
  //response.setContentType("text/html")
  response.setContextType("text/html;charset=UTF-8");
  ```

## 9.2. Springmvc

- CharacterEncodingFilter:在web.xml中设置，注意参数必加
  - 只解决post请求
- 需要自己解决
  - request 的 get请求 ,tomcat8.5后不需要设置，之前设置URIEncoding=UTF-8
  - response响应，通过produces设置类型和编码

```html
 <form action="/login" method="get">
        <input type="text" name="uname" value="get"/> <br>
        <input type="text" name="upass" value="get请求"/> <br>
        <input type="submit" name="登录">
    </form>

    <form action="/login" method="post">
        <input type="text" name="uname" value="post"/> <br>
        <input type="text" name="upass" value="post请求"/> <br>
        <input type="submit" name="登录">
    </form>
```

```java
@Controller
public class LoginController {
    @RequestMapping(value = "/login",produces = "text/html;charset=utf-8")
    @ResponseBody
    public String login(String uname, String upass){
        System.out.println(uname+"-"+upass);
        return uname+"-"+upass;
    }
}
```

# 10. 文件上传

- 两个地方
  - 请求方式必须为post
  - 请求表单中的enctype必须为mutilpart/form-data

## 10.1. springmvc中的方式

- pom.xml 要加入commons-fileupload,springmvc的上传依赖于它

  ```xml
  <dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.3</version>
  </dependency>
  ```

- 加文件解析器 id必须为multipartResolver，且不能省略

  ```xml
  <!--    文件解析器 依赖common-fileupload-->
      <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
          <property name="defaultEncoding" value="UTF-8"/>
          <property name="maxUploadSizePerFile" value="1024000"/>
      </bean>
  ```

- upload.jsp

  ```jsp
   <h2>一个文件</h2>
      <form action="/upload" method="post" enctype="multipart/form-data">
          <input type="file" name="photo"/><br>
          <input type="text" name="uname"/><br>
          <input type="submit" name="上传">
      </form>
  
      <h2>所有文件</h2>
      <form action="/upload2" method="post" enctype="multipart/form-data">
          <input type="file" name="photo1"/><br>
          <input type="file" name="photo2"/><br>
          <input type="text" name="uname"/><br>
          <input type="submit" name="上传">
      </form>
  ```

  

- Controller.java

  ```java
  @PostMapping("/upload")
  //    @ResponseBody
      public String upload(String uname, @RequestParam("photo") CommonsMultipartFile file, HttpServletRequest request){
          String path = request.getServletContext().getRealPath("/photo/");
          try {
              file.transferTo(new File(path+"/"+file.getOriginalFilename()));
          } catch (IOException e) {
              e.printStackTrace();
          }
          System.out.println(uname+"-"+file.getOriginalFilename());
          return "success";
      }
  
      @PostMapping("/upload2")
  //    @ResponseBody
      public String upload2(String uname, HttpServletRequest request){
          System.out.println(uname);
          String path = request.getServletContext().getRealPath("/photo/");
          CommonsMultipartResolver resolver = new CommonsMultipartResolver(request.getServletContext());
          if(resolver.isMultipart(request)){
              MultipartHttpServletRequest req = (MultipartHttpServletRequest) request;
              Iterator<String> i  = req.getFileNames();
              while(i.hasNext()){
                   MultipartFile file = req.getFile(i.next());
                  try {
                      file.transferTo(new File(path+"/"+file.getOriginalFilename()));
                  } catch (IOException e) {
  
                  }
              }
          }
          return "success";
      }
  ```

  

# 11. 全局异常处理

```java
@ControllerAdvice
public class GlobalExceptionAdviceController {

    //异常处理，作用域存值，不能使用map
    @ExceptionHandler(Exception.class)
    public String catchException(Exception e, HttpServletRequest request){
        System.out.println("有异常啦");
        request.setAttribute("ex",e);
        return "error";
    }
}

```

- 方式二

```xml
 <bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
        <!-- 		遇到异常时默认跳转的视图   error其实是error.jsp是视图-->
        <property name="defaultErrorView" value="error"/>
        <!-- 		抛出异常后在页面可用 ${errMsg.message}  读出异常信息 -->
        <property name="exceptionAttribute" value="errMsg"/>
        <!-- 		自定义的异常转向页面可自定义多个而实现不同异常转向不同页面 -->
        <property name="exceptionMappings">
            <props>
                <prop key="java.lang.Exception">error</prop>
            </props>
        </property>
    </bean>
```

- 方式三

  ```java
  public class MyExceptionHandler implements HandlerExceptionResolver {
      public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) {
          ModelAndView mv = new ModelAndView();
          mv.setViewName("error");
          mv.addObject("errMsg",e.getMessage());
          return mv;
      }
  }
  ```

  ```xml
    <!--     全局异常处理器 -->
  <bean class="net.wanho.config.MyExceptionHandler"></bean>
  ```

# 12. springmvc+spring+mybatis==>SSM框架 

## 12.1.  pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>net.wanho</groupId>
  <artifactId>006_springmvc_SSM</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>

  <name>006_springmvc_SSM Maven Webapp</name>

  <properties>
    <spring-version>5.1.5.RELEASE</spring-version>
    <mybatis-version>3.5.0</mybatis-version>
  </properties>

  <dependencies>

<!--spring相关-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>${spring-version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring-version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>${spring-version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aspects</artifactId>
      <version>${spring-version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>${spring-version}</version>
    </dependency>

<!--    mybatis  -->
      <dependency>
          <groupId>org.mybatis</groupId>
          <artifactId>mybatis</artifactId>
          <version>${mybatis-version}</version>
      </dependency>

<!--      mybatis与spring-->
      <dependency>
          <groupId>org.mybatis</groupId>
          <artifactId>mybatis-spring</artifactId>
          <version>2.0.0</version>
      </dependency>


<!--      文件上传-->
    <dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.3.3</version>
    </dependency>

<!--logback-->
      <dependency>
          <groupId>ch.qos.logback</groupId>
          <artifactId>logback-classic</artifactId>
          <version>1.2.3</version>
      </dependency>


<!--    servlet-->
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>4.0.1</version>
      <scope>provided</scope>
    </dependency>

<!--    jsp-->
    <dependency>
      <groupId>javax.servlet.jsp</groupId>
      <artifactId>jsp-api</artifactId>
      <version>2.2</version>
      <scope>provided</scope>
    </dependency>

<!--    lombok-->
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.18.6</version>
      <scope>provided</scope>
    </dependency>

<!--    连接池-->
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.1.16</version>
    </dependency>

<!--    驱动-->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.47</version>
    </dependency>

<!--    junit-->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>2.2</version>
        <configuration>
          <path>/</path>
          <port>8080</port>
          <uriEncoding>UTF-8</uriEncoding>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>

```

## 12.2.  spring-mvc

- web.xml

  ```xml
  <!DOCTYPE web-app PUBLIC
   "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
   "http://java.sun.com/dtd/web-app_2_3.dtd" >
  
  <web-app>
    <display-name>Archetype Created Web Application</display-name>
  <!--  处理springmvc中的post请求的乱码问题-->
    <filter>
      <filter-name>encodingFilter</filter-name>
      <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
      <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
      </init-param>
      <init-param>
        <param-name>forceRequestEncoding</param-name>
        <param-value>true</param-value>
      </init-param>
    </filter>
    <filter-mapping>
      <filter-name>encodingFilter</filter-name>
      <url-pattern>/*</url-pattern>
    </filter-mapping>
  
    <!--  post请求-->
  
  
  <!--  前端控制器-->
    <servlet>
      <servlet-name>springmvc</servlet-name>
      <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
      <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring/spring-mvc.xml,classpath:spring/spring-service.xml</param-value>
      </init-param>
    </servlet>
    <servlet-mapping>
      <servlet-name>springmvc</servlet-name>
      <url-pattern>/</url-pattern>
    </servlet-mapping>
  
    <!--  前端控制器-->
  
  </web-app>
  
  ```

  

- spring-mvc.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xmlns:mvc="http://www.springframework.org/schema/mvc"
  
  
         xsi:schemaLocation="http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/context
         http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">
  
      <!--    controller中的扫描包-->
      <context:component-scan base-package="net.wanho.controller"/>
      <!--    处理映射器和适配器-->
      <mvc:annotation-driven/>
      <!--    视图解析器-->
      <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
          <property name="prefix" value="/WEB-INF/jsp/"/>
          <property name="suffix" value=".jsp"/>
      </bean>
      <!--    静态资源-->
      <mvc:resources mapping="/js/" location="/js/"/>
      <mvc:resources mapping="/css/" location="/css/"/>
      <mvc:resources mapping="/img/" location="/img/"/>
      <mvc:default-servlet-handler/>
  
      <!--    文件上传-->
      <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
          <property name="defaultEncoding" value="UTF-8"/>
          <!--        每个文件大小不能超过100M-->
          <property name="maxUploadSizePerFile" value="1024000"/>
      </bean>
  
      <!--    全局异常-->
      <bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
          <property name="exceptionAttribute" value="errMsg"/>
          <property name="exceptionMappings">
              <props>
                  <prop key="java.lang.Exception">error</prop>
              </props>
          </property>
      </bean>
  
  </beans>
  ```

  

## 12.3. spring-service.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!--    扫描除了controller-->
    <context:component-scan base-package="net.wanho">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <!--    导入mybatis配置-->
    <import resource="classpath:spring/spring-mybatis.xml"/>

    <!--   导入事务配置-->
    <import resource="classpath:spring/spring-transaction.xml"/>


</beans>
```



## 12.4. spring-transaction.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">

    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>

    <aop:config>
        <aop:pointcut id="txPointcut" expression="execution(* net.wanho.service..*.*(..))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointcut"/>
    </aop:config>

    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="add*" propagation="REQUIRED" />
            <tx:method name="append*" propagation="REQUIRED" />
            <tx:method name="insert*" propagation="REQUIRED" />
            <tx:method name="save*" propagation="REQUIRED" />
            <tx:method name="update*" propagation="REQUIRED" />
            <tx:method name="modify*" propagation="REQUIRED" />
            <tx:method name="edit*" propagation="REQUIRED" />
            <tx:method name="delete*" propagation="REQUIRED" />
            <tx:method name="remove*" propagation="REQUIRED" />
            <tx:method name="repair" propagation="REQUIRED" />
            <tx:method name="delAndRepair" propagation="REQUIRED" />

            <tx:method name="get*" propagation="SUPPORTS" read-only="true" />
            <tx:method name="find*" propagation="SUPPORTS" read-only="true" />
            <tx:method name="load*" propagation="SUPPORTS"  read-only="true"/>
            <tx:method name="search*" propagation="SUPPORTS"  read-only="true"/>
            <tx:method name="datagrid*" propagation="SUPPORTS" read-only="true" />

            <tx:method name="*" propagation="SUPPORTS" />
        </tx:attributes>
    </tx:advice>

</beans>
```



## 12.5.  spring-mybatis.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"

       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!--    指定外部db配置-->
    <context:property-placeholder location="classpath:properties/db.properties"/>

    <!--    数据源-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

    <!--   SqlSessionFactoryBean -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <!--        类型别名-->
        <property name="typeAliasesPackage" value="net.wanho.po"/>
        <!--        找到所有映射文件-->
        <property name="mapperLocations" value="classpath:net/wanho/mapper/*.xml"/>
        <!--        mybatis配置:全局配置（二级缓步、懒加载、sql打印）-->
        <property name="configLocation" value="classpath:mybatis/mybatis.xml"/>
    </bean>

    <!--    映射代理-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!--        接口所在包-->
        <property name="basePackage" value="net.wanho.mapper"/>
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
    </bean>

</beans>
```

## 12.6.  db.proeprties

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/studb
jdbc.username=root
jdbc.password=root
```



## 12.7. mybatis.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <!-- 开启二级缓存        -->
        <setting name="cacheEnabled" value="true"/>

        <!-- 启动懒加载        -->
        <setting name="lazyLoadingEnabled" value="true"/>
        <setting name="aggressiveLazyLoading" value="false"/>

        <!-- 输出sql语句        -->
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
</configuration>
```



 

## 12.8. logback.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- 日志级别从低到高分为TRACE < DEBUG < INFO < WARN < ERROR < FATAL，如果设置为WARN，则低于WARN的信息都不会输出 -->
<!-- scan:当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true -->
<!-- scanPeriod:设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。当scan为true时，此属性生效。默认的时间间隔为1分钟。 -->
<!-- debug:当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false。 -->
<configuration  scan="true" scanPeriod="10 seconds">

    <!-- name的值是变量的名称，value的值时变量定义的值。通过定义的值会被插入到logger上下文中。定义变量后，可以使“${}”来使用变量。 -->
    <property name="log.path" value="./logs" />
    <property name="log.pattern" value="%d{HH:mm:ss.SSS} [%thread] %-5level %logger{20} - [%method,%line] - %msg%n" />

    <!-- 彩色日志 -->
    <!-- 彩色日志依赖的渲染类 -->
    <conversionRule conversionWord="clr" converterClass="org.springframework.boot.logging.logback.ColorConverter" />
    <conversionRule conversionWord="wex" converterClass="org.springframework.boot.logging.logback.WhitespaceThrowableProxyConverter" />
    <conversionRule conversionWord="wEx" converterClass="org.springframework.boot.logging.logback.ExtendedWhitespaceThrowableProxyConverter" />
    <!-- 彩色日志格式 -->
    <property name="CONSOLE_LOG_PATTERN" value="%yellow(%date{yyyy-MM-dd HH:mm:ss}) |%highlight(%-5level) |%blue(%thread) |%blue(%file:%line) |%green(%logger) |%cyan(%msg%n)"/>
    <!-- 控制台输出 -->
    <!--输出到控制台-->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <!--此日志appender是为开发使用，只配置最底级别，控制台输出的日志级别是大于或等于此级别的日志信息-->
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>info</level>
        </filter>
        <encoder>
            <Pattern>${CONSOLE_LOG_PATTERN}</Pattern>
            <!-- 设置字符集 -->
            <charset>UTF-8</charset>
        </encoder>
    </appender>

    <!--输出到文件-->
    <!-- 时间滚动输出 level为 DEBUG 日志 -->
    <appender name="DEBUG_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${log.path}/log_debug.log</file>
        <!--日志文件输出格式-->
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
            <charset>UTF-8</charset> <!-- 设置字符集 -->
        </encoder>
        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 日志归档 -->
            <fileNamePattern>${log.path}/debug/log-debug-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>100MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <!--日志文件保留天数-->
            <maxHistory>15</maxHistory>
        </rollingPolicy>
        <!-- 此日志文件只记录debug级别的 -->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>debug</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <!-- 时间滚动输出 level为 INFO 日志 -->
    <appender name="INFO_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${log.path}/log_info.log</file>
        <!--日志文件输出格式-->
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
            <charset>UTF-8</charset>
        </encoder>
        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 每天日志归档路径以及格式 -->
            <fileNamePattern>${log.path}/info/log-info-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>100MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <!--日志文件保留天数-->
            <maxHistory>15</maxHistory>
        </rollingPolicy>
        <!-- 此日志文件只记录info级别的 -->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>info</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <!-- 时间滚动输出 level为 WARN 日志 -->
    <appender name="WARN_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${log.path}/log_warn.log</file>
        <!--日志文件输出格式-->
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
            <charset>UTF-8</charset> <!-- 此处设置字符集 -->
        </encoder>
        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log.path}/warn/log-warn-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>100MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <!--日志文件保留天数-->
            <maxHistory>15</maxHistory>
        </rollingPolicy>
        <!-- 此日志文件只记录warn级别的 -->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>warn</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>


    <!-- 时间滚动输出 level为 ERROR 日志 -->
    <appender name="ERROR_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${log.path}/log_error.log</file>
        <!--日志文件输出格式-->
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
            <charset>UTF-8</charset> <!-- 此处设置字符集 -->
        </encoder>
        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log.path}/error/log-error-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>100MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <!--日志文件保留天数-->
            <maxHistory>15</maxHistory>
        </rollingPolicy>
        <!-- 此日志文件只记录ERROR级别的 -->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>ERROR</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <!-- 显示形成的sql、使用的参数、结果集 -->
    <!--
       <logger name="java.sql" level="debug" />
       <logger name="org.springframework.jdbc" level="debug" />
    -->

    <!--
        指定某一个包或者某一个类的打印级别以及是否传入root进行打印
        <logger>用来设置某一个包或者具体的某一个类的日志打印级别、
        以及指定<appender>。<logger>仅有一个name属性，
        一个可选的level和一个可选的addtivity属性。
        name:用来指定受此logger约束的某一个包或者具体的某一个类。
        level:用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，
              还有一个特俗值INHERITED或者同义词NULL，代表强制执行上级的级别。
              如果未设置此属性，那么当前logger将会继承上级的级别。
        addtivity:是否向上级logger传递打印信息。默认是true。

    -->

    <!--
        root节点是必选节点，用来指定最基础的日志输出级别，只有一个level属性
        level:用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，
        不能设置为INHERITED或者同义词NULL。默认是DEBUG
        可以包含零个或多个元素，标识这个appender将会添加到这个logger。
    -->
    <!--<logger name="net.wanho" level="DEBUG" />
     <logger name="com.ibatis" level="INFO"/>
     <logger name="org.springframework" level="INFO"/>
     <logger name="java.sql.PreparedStatement" level="INFO"/>
     <logger name="org.springframework.web.servlet.DispatcherServlet" level="INFO"/>
     <logger name="com.ibatis.sqlmap.engine.impl.SqlMapClientDelegate" level="INFO"/>
     <logger name="java.sql" level="INFO"/>
     <logger name="org.apache.commons" level="INFO"/>
     <logger name="java.sql.Statement" level="INFO"/>
     <logger name="org.springframework.web.context.support.XmlWebApplicationContext" level="INFO"/>
     <logger name="com.ibatis.common.jdbc.SimpleDataSource" level="INFO"/>
     <logger name="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping" level="INFO"/>
     <logger name="java.sql.ResultSet" level="INFO"/>
     <logger name="java.sql.Connection" level="INFO"/>
     <logger name="com.ibatis.common.jdbc.ScriptRunner" level="INFO"/>
     <logger name="com.alibaba.dubbo"  level="INFO"/>
     <logger name="org.apache.zookeeper"  level="INFO"/>-->


    <root level="error">
        <!--<level value="error" />-->
        <!--<level value="info" />-->
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="DEBUG_FILE" />
        <appender-ref ref="INFO_FILE" />
        <appender-ref ref="WARN_FILE" />
        <appender-ref ref="ERROR_FILE" />
    </root>


</configuration>
```



 

## 12.9.  Student.java

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Student implements Serializable {
	private Integer id;
	private String name;
	private Integer age;
	private String gender;
	private String address;
}
```

- 使用MybatisCodeHelper生成Service 和Mapper,其它没有大用

## 12.10.  StudentMapper.java

```java
public interface StudentMapper {
    int insert(Student student);


    List<Student> findAllStus();
}

```



## 12.11.  StudentMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="net.wanho.mapper.StudentMapper">
    <!--auto generated Code-->
    <resultMap id="BaseResultMap" type="net.wanho.po.Student">
        <result column="id" property="id" jdbcType="INTEGER"/>
        <result column="name" property="name" jdbcType="VARCHAR"/>
        <result column="age" property="age" jdbcType="INTEGER"/>
        <result column="gender" property="gender" jdbcType="VARCHAR"/>
        <result column="address" property="address" jdbcType="VARCHAR"/>
    </resultMap>

    <!--auto generated Code-->
    <sql id="Base_Column_List">
        id,
        name,
        age,
        gender,
        address
    </sql>
    <select id="findAllStus" resultType="Student">
        select <include refid="Base_Column_List"></include> from student
    </select>
    <!--auto generated Code-->
    <insert id="insert" useGeneratedKeys="true" keyProperty="id" parameterType="Student">
        INSERT INTO student (
            name,
            age,
            gender,
            address
        ) VALUES (
            #{name},
            #{age},
             #{gender},
              #{address}
        )
    </insert>


</mapper>


```



## 12.12.  StudentService.java

```java
public interface StudentService{

    int insert(Student student);



    List<Student> findAllStu();
}

```



## 12.13. StudentServiceImpl.java

```java
@Service
public class StudentServiceImpl implements StudentService {

    @Autowired
    private StudentMapper studentMapper;


    public int insert(Student student){
        return studentMapper.insert(student);
    }




    public List<Student> findAllStu() {
        return studentMapper.findAllStus();
    }
}

```



## 12.14. StudentServiceImplTest.java

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:spring/spring-service.xml")
public class StudentServiceImplTest {

    @Autowired
    private StudentService studentService;

    @Test
    public void insert() {
        Student stu = new Student();
        stu.setName("test1111");
        stu.setAge(18);
        stu.setGender("女");
        stu.setAddress("南京");
        studentService.insert(stu);
        System.out.println(stu.getId());
    }

    @Test
    public void findAll(){
         studentService.findAllStu();
    }


}
```

































































