# Spring

# 1. Spring 是什么

- Spring是一个开源框架，最早由Rod Johnson创建。
- Spring是一个IOC(DI)和AOP容器框架。
- 具体描述Spring
  - 轻量级:Spring是非侵入式-基于Spring框架即可
  - DI:dependency -injection 依赖注入
  - IOC: Inverse of controller 控制反转
  - AOP: aspect oriented programming 面向切面
  - 容器:它包含和管理应用的生命周期
  - 框架 ：企业级的框架
  - Spring一站式框架: SpringMVC，Spring， Spring JDBC

# 2. 模块

![](img/1.png)

# 3. 开始HelloWorld小程序

- HelloWorld.java

  ```java
  public class HelloWorld {
      private String name;
  
      public void setName(String name) {
          this.name = name;
      }
  
      public void hello(){
          System.out.println("hello"+name);
      }
  }
  
  ```

- Test.java

  ```java
  @Test
  public void hello() {
      //创建对象
      HelloWorld hw = new HelloWorld();
      //设置属性的值
      hw.setName("张三");
      hw.hello();
  
      //上面代码的问题，硬编码，控制权在java中
  }
  
  @Test
  public void hello2(){
      //通过Spring来实现 创建Spring的IOC容器
      ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
      //从IOC容器中获取bean的实例
      HelloWorld helloWorld = (HelloWorld) ctx.getBean("helloWorld");
      helloWorld.hello();
  }
  
  ```

# 4. Spring中IOC和DI流程

- 创建配置文件applicationContext.xml，名称可以自定义

  参考：https://docs.spring.io/spring/docs/5.1.6.RELEASE/spring-framework-reference/core.html#spring-core

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          https://www.springframework.org/schema/beans/spring-beans.xsd">
      <bean id="helloWorld" class="net.wanho.HelloWorld">
          <property name="name" value="张三丰"/>
      </bean>
  </beans>
  ```

- 通过配置文件来创建Spring应用的上下文

- 从IOC容器中获取bean的实例

# 5. Spring配置Bean

## 5.1. bean的配置

- id  : class对象创建好后，给对象创建一个名称。id的名称唯一。
- name: class对象创建好后，给对象创建一个名称。但name中可以出现特殊字符。比如有多个名称时，多个之名称间用空格或逗号或分号隔开
- 也可以即没id也没name，则通过类的全限定名访问。但有id或name，则不能通过全限定名访问。
- class : 类的全限定的名，它是通过反射创建对象的，在类中必须提供一个空的构造方法

```xml
<bean id="helloWorld" name="h1 h2" class="net.wanho.HelloWorld">
    <property name="name" value="张三丰"/>
</bean>
```

## 5.2. bean的创建的三种方式

创建Bean实例的方式：

   1) 通过构造器(有参或无参) ，常用推荐。后面两种了解即可

​      方式: 

```java
<bean id="" class=""/>
```

  2) 通过静态工厂方法

​      方式: 

```java
<bean id="" class="工厂类" factory-method="静态工厂方法"/>
```

​      注: 工厂类实例没有创建

   3) 通过实例工厂方法(非静态方法)

​      方式:

```java
<bean id="factory" class="工厂类"/>
<bean id="" factory-bean="factory" factory-method="实例工厂方法"/>
```

​      注: 工厂类实例被创建

## 5.3. 属性注入的方式

- 属性注入
- 构造注入

### 5.3.1.  属性方式 

- 属性注入即通过setter方法的方式注入的

- 属性注入语法 ：在bean里面设置`<property name="属性名" value="属性值"/>`

- 属性注入是最常用的方式

  ```xml
  <bean id="helloWorld" name="h1 h2" class="net.wanho.HelloWorld">
      <property name="name" value="张三丰"/>
  </bean>
  ```

### 5.3.2.  构造方式

```xml
<bean id="helloWorld2"  class="net.wanho.HelloWorld">
    <constructor-arg value="李四光"/>
</bean>
```

## 5.4. 属性赋值的细节

### 5.4.1. 字面量

- 字面量: 可以用字符串表示的值，可以通过`<value>`标签或value属性进行注入
- 基本数据类型和String可以采用字面量直接注入
- 若字面量中含有特殊符号，可以使用`<![CDATA[ ]]>`

```xml
<bean id="stu4" class="net.wanho.Student">
  <!-- 要求: 在 Bean 中必须有对应的构造器. -->
  <constructor-arg value="1004" index="0"></constructor-arg>
  <constructor-arg value="张三4" index="1"></constructor-arg>
  <constructor-arg value="18" index="2"></constructor-arg>
  <constructor-arg value="男" index="3"></constructor-arg>
  <!-- 若字面值中包含特殊字符, 则可以使用 DCDATA 来进行赋值. (了解) -->
  <constructor-arg type="java.lang.String">
    <value><![CDATA[<南京>]]></value>
  </constructor-arg>
</bean>
```

### 5.4.2. 引用其它的bean

- 如果是值类型直接写value赋值，如果是引用类型则写ref引用其它的bean

```xml
    <bean id="score" class="net.wanho.po.Score">
        <property name="chinese" value="80"/>
        <property name="math" value="90"/>
        <property name="english" value="100"/>
    </bean>
    <bean id="stu" class="net.wanho.po.Student">
        <property name="id">
                <value>1001</value>
        </property>
        <property name="name" value="张三宝"/>
        <property name="age" value="18"/>
        <property name="gender" value="男"/>
        <property name="address" >
                <value> <![CDATA[<南京> ]]></value>
        </property>
<!--        <property name="birthday" value="2018-8-8"/>-->
        <property name="score"  ref="score"/>
     </bean>
```

### 5.4.3. null值

- 可以不给此属性赋值，也可以给此属性赋上`<null/>`标签

```xml
 <property name="gender">
     <null/>
</property>
```

### 5.4.4. 级联属性

- 当属性是一个对象时，还可以使用级联属性，即对象属性.属性的方式赋值

```xml
 <property name="score.math" value="100"/>
```

### 5.4.5. 集合属性

- List

  ```xml
  <property name="lsts">
       <list>
           <value>11</value>
           <value>aaa</value>
           <value>222</value>
       </list>
  </property>
  ```

- Set

  ```xml
  <property name="sets">
      <set>
          <value>11</value>
          <value>aaa</value>
          <value>11</value>
      </set>
  </property>
  ```

- Map

  ```xml
   <property name="map">
       <map>
           <entry key="k1" value="v1"/>
           <entry key="k1" value="v22"/>
           <entry key="k3" value="v3"/>
       </map>
  </property>
  ```

  

- Properties

  ```xml
  <property name="p">
      <props>
          <prop key="p1">v1</prop>
          <prop key="p1">v11</prop>
          <prop key="p2">v2</prop>
      </props>
  </property>
  ```

### 5.4.6. 使用util命名空间 可以集合，方便集合属性赋值时引用

```xml
 xmlns:util="http://www.springframework.org/schema/util"
 http://www.springframework.org/schema/util
        http://www.springframework.org/schema/util/spring-util.xsd
<util:list id="myList">
    <value>11</value>
    <value>aaa</value>
    <value>11</value>
</util:list>
<property name="lsts" ref="myList"></property>
```



### 5.4.7. 使用p命名空间给属性赋值

```xml
xmlns:p="http://www.springframework.org/schema/p"
<bean id="score2" class="net.wanho.po.Score" p:chinese="60" p:english="66" p:math="69" ></bean>
```

## 5.5.  自动装配 ，省略属性对象的引用

- 当前我要向一个Bean的某个对象属性赋值时，需要ref引用别一个bean的名称。如果引用的bean变化了，但我们在引用处可能忘记修改。随着项目变化是有可能出现的。
- 如果能够自动引用(自动装配)，就比较方便了
  - 根据名称 byName，要求名称必须一致
  - 根据类型 byType，要求类型必须唯一

- 可以通autowire属性来实现自动装配

  - autowire属性有5种模式，如下表：

  | 模式        | 说明                                                         |
  | ----------- | ------------------------------------------------------------ |
  | no          | (默认)不采用autowire机制.。这种情况，当我们需要使用依赖注入，只能用<ref/>标签。 |
  | byName      | 通过属性的名称自动装配（注入）。Spring会在容器中查找名称与bean属性名称一致的bean，并自动注入到bean属性中。当然bean的属性需要有setter方法。例如：bean A有个属性master，master的setter方法就是setMaster，A设置了autowire="byName"，那么Spring就会在容器中查找名为master的bean通过setMaster方法注入到A中。 |
  | byType      | 通过类型自动装配（注入）。Spring会在容器中查找类（Class）与bean属性类一致的bean，并自动注入到bean属性中，如果容器中包含多个这个类型的bean，Spring将抛出异常。如果没有找到这个类型的bean，那么注入动作将不会执行。 |
  | constructor | 类似于byType，但是是通过构造函数的参数类型来匹配。假设bean A有构造函数A(B b,   C c)，那么Spring会在容器中查找类型为B和C的bean通过构造函数A(B b, C c)注入到A中。与byType一样，如果存在多个bean类型为B或者C，则会抛出异常。但时与byType不同的是，如果在容器中找不到匹配的类的bean，将抛出异常，因为Spring无法调用构造函数实例化这个bean。 |
  | default     | 采用父级标签（即beans的default-autowire属性）的配置。        |

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd" default-autowire="byType">
  
      <bean id="stu" class="net.wanho.po.Student" autowire="default"/>
      <bean id="score2" class="net.wanho.po.Score"/>
  
  
  </beans>
  ```

## 5.6. 自动扫描，省略bean标签编写 

###  5.6.1. 可以发现的JavaBean

- @Component  普通类
- @Resposity    数据访问层
- @Service        业务层
- @Controller    控制层

- 上面四种写法功能一样，但一般有所区别，是为有见名有意义。

###  5.6.2. 指定要扫描的包 

```xml
 <context:component-scan base-package="net.wanho.po"/>
```

### 5.6.3. 在自动扫描里，要使用自动装配，使用@autowire注解或@Resource注解，它是按类型装配的

- @autowire 是Spring提供的，后面可以加上参数required=true ，推荐
- Resource是javax提供的，后面没有参数



```java
@Data
@Component(value = "student")
public class Student {
    private String name;

   @Autowired
    private Score score;
}


@Data
@Component
public class Score {
    private Integer math ;
}

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = { "classpath:/applicationContext.xml" })
public class StudentTest {


   @Autowired
    private Student student;

    @Test
    public void testStudent(){
        student.setName("张三丰");
        student.getScore().setMath(66);
        System.out.println(student);
    }
}
```

### 5.6.4. 配置文件的拆分

- 在后面项目框架变复杂的时候，配置文件仍然只是一个（主配置）。但我们需要按模块来拆分来放存放配置。例如spring-mvc.xml,spring.xml,spring-mybatis.xml
- 只要在主配置文件中加入import即可

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd"
       >
        <import resource="classpath:spring-mvc.xml"/>
        <import resource="classpath:spring.xml"/>
</beans>
```

### 5.6.5. 扫描排除的问题（重复扫描的问题）

- 当配置文件拆分了，如果多个配置文件中重复扫描包，可能会导致JavaBean重复创建对象。
- 扫描哪个包，需要的是经验。比如mvc配置只扫描 controller包，spring配置扫描service和mapper包。

- 怎么避免扫描其它包

  - 要么使用只包含xxx(类/注解)

    ```xml
    <context:component-scan base-package="net.wanho" use-default-filters="false">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
        <!-- context:include-filter type="assignable" expression="net.wanho.controller.StudentController"/ -->
    </context:component-scan>
    ```

    - use-default-filters="true" 默认是包含当前包和所有子包中类，设置成false则都不包含

  - 要么使用只排除xxx包

    ```xml
    <context:component-scan base-package="net.wanho" >
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
        <!-- context:exclude-filter type="assignable" expression="net.wanho.controller.StudentController"/ -->
    </context:component-scan>
    ```

### 5.6.6.  泛型依赖注入

- spring4.0 中可以为子类注入对应泛型 类型的对象,大大提高编程效率
- 例如：
  - 在BaseService中装配BaseDao，但这两个类本身都不能被发现
  - 但它们子类StudnetService和StudentDao，都能补发现
  - 所以aseService和BaseDao都能注入进来，只不过真正注入的子类对象
  - 这样泛型也可以注入进来，因为实现的子类肯定是具体真正类型的

```java
interface BaseDao<T>{
    public void add();
}
@Resposity
class StudentDao implements BaseDao<Student>{
    public void add(){
        System.out.println("student add");
    }
}

class BaseService<T>{
    @AuotWire
    private BaseDao<T> baseDao;
    public void add(){
        baseDao.add();
    }
}
@Service
class StudentService  extends BaseService<Studnet>{
    
}
```

## 5.7. scope作用域

- scope是用来设置JavaBean的范围的
  - 单例 ：singleton （默认）
  - 原型 ：prototype

- 使用scope的两方式
  - 在xml中使用
    - bean后面加上scope属性
  - 在注解中使用
    - @Scope注解 

| singleton      | 在每个Spring IoC容器中一个bean定义对应一个对象实例。         |
| -------------- | ------------------------------------------------------------ |
| prototype      | 一个bean定义对应多个对象实例。                               |
| request        | 在一次HTTP请求中，一个bean定义对应一个实例；即每次HTTP请求将会有各自的bean实例，它们依据某个bean定义创建而成。该作用域仅在基于web的Spring `ApplicationContext`情形下有效。 |
| session        | 在一个HTTP `Session`中，一个bean定义对应一个实例。该作用域仅在基于web的Spring `ApplicationContext`情形下有效。 |
| global session | 在一个全局的HTTP `Session`中，一个bean定义对应一个实例。典型情况下，仅在使用portlet context的时候有效。该作用域仅在基于web的Spring `ApplicationContext`情形下有效。 |

### 5.7.1. xml示例

enity/Person.java

```java
public class Person {

    public Person(){
        System.out.println("person");
    }
}
```

```xml
<bean id="person" class="entity.Person" scope="prototype" />
```

```java
@Test
public void testPerson(){
    ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
    // 构造方法执行了两次
    Person person1 = (Person) ctx.getBean("person");
    Person person2 = (Person) ctx.getBean("person");
    //但对象是同一个对象。所以返回值为true
    System.out.println(person1==person2);

}
```

### 5.7.2. annotation示例

net/wanho/po/Student.java

```java
@Data
@Component
@Scope("prototype")
public class Student {
    private String name;

   @Autowired
    private Score score;
}
```



```xml
<context:component-scan base-package="net.wanho" >
            <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```



```java
@Autowired
private Student student;

@Autowired
private Student student2;

@Test
public  void testScope(){
    System.out.println(student==student2);//false
}
```

## 5.8. 使用外部属性文件

- 一般会把数据库的配置信息放置在单独的db.properties文件中，而不是放置在spring的配置中

  ```xml
  <context:property-placeholder location="classpath:db.properties"/>
  
  <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
        destroy-method="close">
      <property name="driverClassName" value="${jdbc.driver}" />
      <property name="url" value="${jdbc.url}" />
      <property name="username" value="${jdbc.username}" />
      <property name="password" value="${jdbc.password}" />
  </bean>
  ```

- db.properties

  ```pro
  jdbc.driver=com.mysql.jdbc.Driver
  jdbc.url=jdbc.mysql://localhost:3306/studb
  jdbc.username=root
  jdbc.password=root
  ```

# 6. Spring表达式语言: SpEL。了解

- Spring表达式语言(简称SpEL):是一个支持运行时查询和操作对象的语言
- 语法类似于EL,但EL使用${}表达式，而SpEL使用#{}

## 6.1.  常量 

我们可以在<property>元素的value属性中使用#{}界定符将值装配到Bean的属性中。

```xml
<property name="count" value="#{5}" />
```

浮点型数字一样可以出现在SpEL表达式中。

```xml-dtd
<property name="frequency" value="#{89.7}" />
```

表达式中的数字也可以实用科学计数法。

```xml
<property name="capacity" value="#{1e4}" />
```

这里将capacity属性设为了10000.0。
String类型的字面值可以使用单引号或者双引号作为字符串界定符。

```xml
<property name="name" value="#{'moonlit'}" />
```

或者

```xml
<property name="name" value='#{"moonlit"}' />
```

还可以使用布尔值true和false。

```xml
<property name="enabled" value="#{true}" />
```

## 6.2. 引用Bean、属性和方法

##  6.2.1. 引用其他对象：

方式一: 通过SpEL获得bean的对象。

```xml
<property name="score" value="#{stu.score}"></property>
```

方式二: 可以通过SpEL获得bean的对象。

```xml
<property name="score" value="#{stu.getScore()}"></property>
```

### 6.2.2. 引用其他对象的属性

```xml
<property name="stuName" value="#{stu.name}"></property>
```

### 6.2.3 .调用其他方法，还可以链式操作

在SpEL中避免抛出空指针异常（NullPointException）的方法是使用null-safe存取器：

```xml
<property name="stuMotto" value="#{stu.showMotto()}"></property>
```

### 6.2.4.调用静态方法或静态属性：

通过 T() 调用一个类的静态方法，它将返回一个 Class Object，然后再调用相应的方法或属性：

T(java.lang.Math)运算符的结果会返回一个java.lang.Math类对象。
装配PI或者一个随机值的配置方法如下：

```xml
<property name="multiplier" value="#{T(java.lang.Math).PI}" />
<property name="randomNumber" value="#{T(java.lang.Math).random()}" />
```

SpEL上还可以执行bean值和数值之间的多种运算。

## 6.3. 运算符

### 6.3.1. 算数运算符：+, -, *, /, %, ^：

<table>
<thead>
<tr>
  <th align="left">运算符类型</th>
  <th align="left">运算符</th>
</tr>
</thead>
<tbody><tr>
  <td align="left">算术运算</td>
  <td align="left">+、-、*、/、%、^</td>
</tr>
<tr>
  <td align="left">关系运算</td>
  <td align="left">&lt;、&gt;、==、&lt;=、&gt;=、lt、gt、eq、le、ge</td>
</tr>
<tr>
  <td align="left">逻辑运算</td>
  <td align="left">and、or、not、|</td>
</tr>
<tr>
  <td align="left">条件运算</td>
  <td align="left">?:(ternary)、?:(Elvis)</td>
</tr>
<tr>
  <td align="left">正则表达式</td>
  <td align="left">matches</td>
</tr>
</tbody></table>

```xml
<!-- +运算符：两个数字相加 -->
<property name="adjustedAmount" value="#{counter.total + 42}"/>
<!-- +运算符：用于连接字符串 -->
<property name="fullName" value="#{performer.firstName + ' ' + performer.lastName}"/>
<!-- -运算符：两个数字相减 -->
<property name="adjustedAmount" value="#{counter.total - 20}"/>
<!-- *运算符：乘法运算 -->
<property name="circumference" value="#{2 * T(java.lang.Math).PI * circle.radius}"/>
<!-- /运算符：除法运算 -->
<property name="average" value="#{counter.total / counter.count}"/>
<!-- %运算符：求余运算 -->
<property name="remainder" value="#{counter.total % counter.count}"/>
<!-- ^运算符：乘方运算 -->
<property name="area" value="#{T(java.lang.Math).PI * circle.radius ^ 2}"/>
```

- 加号还可以用作字符串连接：

### 6.3.2. 比较运算符： <, >, ==, <=, >=, lt, gt, eq, le, ge

<table>
<thead>
<tr>
  <th align="center">运算符</th>
  <th align="center">符号</th>
  <th align="center">文本类型</th>
</tr>
</thead>
<tbody><tr>
  <td align="center">等于</td>
  <td align="center">==</td>
  <td align="center">eq</td>
</tr>
<tr>
  <td align="center">小于</td>
  <td align="center">&lt;</td>
  <td align="center">lt</td>
</tr>
<tr>
  <td align="center">小于等于</td>
  <td align="center">&lt;=</td>
  <td align="center">le</td>
</tr>
<tr>
  <td align="center">大于</td>
  <td align="center">&gt;</td>
  <td align="center">gt</td>
</tr>
<tr>
  <td align="center">大于等于</td>
  <td align="center"><blockquote>
  =
</blockquote></td>
  <td align="center">ge</td>
</tr>
</tbody></table>

### 6.3.3. 逻辑运算符号： and, or, not, ！

<table>
<thead>
<tr>
  <th align="center">运算符</th>
  <th align="left">操作</th>
</tr>
</thead>
<tbody><tr>
  <td align="center">and</td>
  <td align="left">逻辑AND运算操作，只有运算符两边都是true，表达式才能是true</td>
</tr>
<tr>
  <td align="center">or</td>
  <td align="left">逻辑OR运算操作，只要运算符的任意一边是true，表达式就会是true</td>
</tr>
<tr>
  <td align="center">not或!</td>
  <td align="left">逻辑NOT运算操作，对运算结果求反</td>
</tr>
</tbody></table>

### 6.3.4. if-else 运算符：?: (ternary), ?: (Elvis)

```xml
<!-- ?:三元运算符 -->
<property name="song" value="#{kenny.song != null ? kenny.song : 'Greensleeves'}"/>
```



if-else 的变体

```xml
<!-- ?:三元运算符 -->
<property name="song" value="#{kenny.song != null ? 'Greensleeves'}"/>
```

### 6.3.5. 正则表达式：matches

```xml
<!-- 判断一个字符串是否是有效的邮件地址 -->
<property name="validEmail" value="#{admin.email matches '[a-zA-Z0-9.-%+-]+@[a-zA-Z0-9.-]+\\.com'}"/>
```

# 7. Bean的生命周期

- SpringIOC容器对Bean的生命周期管理过程 
  - 创建： 通过构造方法或工厂方法创建Bean实例 。
  - 初始化： 调用bean的初始化方法        需要通过init-method方法指定，如果不指定则无此生命周期。
  - 消亡前的方法: 需要通过destory-method方法指定，如果不指定则无此生命周期。
  - 消亡： 当容器关闭前，bean消亡了 

```xml
<bean id="person" class="entity.Person" scope="prototype"  init-method="before" destroy-method="after" />
```

# 8. AOP

## 8.1. 引出

### 8.1.1. 需求

![](img/24.png)

### 8.1.2.  改进

- 日志：记录操作的过程 
- 验证：控制操作权限

- 代码片段

ArithmeticCalculator.java

```java
public interface ArithmeticCalculator {
	int add(int i, int j);
	int sub(int i, int j);
	int mul(int i, int j);
	int div(int i, int j);
}
```

ArithmeticCalculatorImpl.java

```java
public class ArithmeticCalculatorImpl implements ArithmeticCalculator {
	@Override
	public int add(int i, int j) {
		int result = i + j;
		return result;
	}
	@Override
	public int sub(int i, int j) {
		int result = i - j;
		return result;
	}
	@Override
	public int mul(int i, int j) {
		int result = i * j;
		return result;
	}
	@Override
	public int div(int i, int j) {
		int result = i / j;
		return result;
	}
}
```

ArithmeticCalculatorImplLogging.java

```java
public class ArithmeticCalculatorLoggingImpl implements ArithmeticCalculator {

	@Override
	public int add(int i, int j) {
		System.out.println("The method add begins with [" + i + "," + j + "]");
		int result = i + j;
		System.out.println("The method add ends with " + result);
		return result;
	}

	@Override
	public int sub(int i, int j) {
		System.out.println("The method sub begins with [" + i + "," + j + "]");
		int result = i - j;
		System.out.println("The method sub ends with " + result);
		return result;
	}

	@Override
	public int mul(int i, int j) {
		System.out.println("The method mul begins with [" + i + "," + j + "]");
		int result = i * j;
		System.out.println("The method mul ends with " + result);
		return result;
	}
	@Override
	public int div(int i, int j) {
		System.out.println("The method div begins with [" + i + "," + j + "]");
		int result = i / j;
		System.out.println("The method div ends with " + result);
		return result;
	}
}
```

### 8.1.3. 问题

- 额外代码混乱
- 额外代码重复
- 额外代码分散

### 8.1.4. 解决方案，使用代理

#### 8.1.4.1.  静态代理，缺点：必须为每个类创建一个静态代理类，同时必须要有接口

ArithmeticCalculator.java

```java
public interface ArithmeticCalculator {
	int add(int i, int j);
	int sub(int i, int j);
	int mul(int i, int j);
	int div(int i, int j);
}
```

​    ArithmeticCalculatorImpl.java

```java
public class ArithmeticCalculatorImpl implements ArithmeticCalculator {
	@Override
	public int add(int i, int j) {
		int result = i + j;
		return result;
	}
	@Override
	public int sub(int i, int j) {
		int result = i - j;
		return result;
	}
	@Override
	public int mul(int i, int j) {
		int result = i * j;
		return result;
	}
	@Override
	public int div(int i, int j) {
		int result = i / j;
		return result;
	}
}
```

ArithmeticCalculatorImplProxy .java

```java
public class ArithmeticCalculatorImplProxy implements ArithmeticCalculator {

   private ArithmeticCalculator prxoyTarget ;

   public ArithmeticCalculatorImplProxy(ArithmeticCalculator prxoyTarget) {
      this.prxoyTarget = prxoyTarget;
   }

   public int add(int i, int j) {
      System.out.println("增加开始");
      int res = prxoyTarget.add(i,j);
      System.out.println("增加结束");
      return res;
   }

   public int sub(int i, int j) {
      System.out.println("减法开始");
      int res = prxoyTarget.sub(i,j);
      System.out.println("减法结束 ");
      return res;
   }

   public int mul(int i, int j) {
      System.out.println("乘法开始");
      int res = prxoyTarget.mul(i,j);
      System.out.println("乘法结束");
      return res;
   }

   public int div(int i, int j) {
      System.out.println("除法开始");
      int res = prxoyTarget.div(i,j);
      System.out.println("除法开始");
      return res;
   }
```

#### 8.1.4.2. 动态代理

- jdk 自带的代理类Proxy, 为所有类创建代理对象，但也必须要有接口

- cglib第三方，spring里同时支持jdk和cglib， 为所有类创建代理对象，且目标类可以不实现接口

  ![这里写图片描述](F:/course/java126/step5/03_spring/%E7%AC%94%E8%AE%B0/img/23.png)

- ```java
  jdk动态代理示例
  public class DynamicProxy implements InvocationHandler {
  
      //目标
      private Object target;
  
      //创建代理对象
      public Object createPrxoy(Object target){
          this.target=target;
          return Proxy.newProxyInstance(target.getClass().getClassLoader(),target.getClass().getInterfaces(),this);
      }
  
      public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
          //核心方法
          System.out.println("日志开始:当前方法为"+method.getName()+",操作时间:"+new Date().toLocaleString());
          Object res = method.invoke(target,args);
          System.out.println("日志结束");
          return res;
      }
  }
  
  ```

```java
private ArithmeticCalculator arithmeticCalculator;
    @Before
    public void before(){
        DynamicProxy proxy = new DynamicProxy();
         arithmeticCalculator = (ArithmeticCalculator) proxy.createPrxoy(new ArithmeticCalculatorImpl());
    }

    @Test
    public void add() {
       int res =  arithmeticCalculator.add(1,2);
        System.out.println(res);
    }

```

## 8.2. AOP简介

- AOP（Aspect Oritented Programming）:面向切面编程
- AOP重点是面向切面，切面模块横切关注点
- AOP编程切面中实现的是额外功能 ，支持配置。

## 8.3. AOP好外

- 每个额外功能位于一个切面，便于维护和升级 
- 核心业务模块更简洁，只包含核心代码

![](img/25.png)



## 8.4. AOP术语

- 切面(aspect): 横切关注点，一个切面对应一个额外功能
- 通知(advice): 额外功能
- 目标(target)：被代理的类
- 代理(proxy): 代理对象
- 连接点(joinpoint): 执行位置
- 切点(pointcut):找到执行位置

## 8.5. 基于注解 的方式

### 8.5.1. 使用三步曲

- 要加入对应的jar包

  ```xml
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aspects</artifactId>
      <version>${spring-version}</version>
  </dependency>
  ```

- 启用注解

  ```xml
  <aop:aspect-autoproxy/>
  ```

- 编码三步曲

  - 第一步定义切面

    ```java
    @Aspect
    @Component
    @Order(2)
    public class LoggingAspect {
    ```

  - 第二步定义切点

    ```java
    @Pointcut("execution(* net.wanho.service..*.*(..) )")
    public void declareWeaverExpression(){}
    ```

  - 第三步定义通知：五种位置(五种通知)+织语表达式

    - @Before    前置通知 ，方法执行之前
    - @After       后置通知，方法执行之后，返回结果之前执行
    - @Around    环绕通知 ，围绕着方法
    - @AfterRunning 返回通知，方法返回结果之后执行
    - @AfterThrowing 异常通知，在方法抛出异常之后

- 示例

  ```java
  @Aspect
  @Component
  public class LoggingAspect {
     //前置通知
      @Before("execution(* net.wanho.service..*.*(..) )")
      public void beforeMethod(JoinPoint joinPoint){
         String methodName = joinPoint.getSignature().getName();
         System.out.println(methodName+"开始");
      }
  
      //后置通知：方法后，但在返回值前
      @After("execution(* net.wanho.service..*.*(..) )")
      public void  afterMethod(JoinPoint joinPoint){
          String methodName = joinPoint.getSignature().getName();
          System.out.println(methodName+"afterMethod");
      }
  
      //返回通知方法后，且在返回值后
      @AfterReturning(value = "execution(* net.wanho.service..*.*(..) )",returning = "result")
      public void afterReturning(JoinPoint joinPoint,Object result){
          String methodName = joinPoint.getSignature().getName();
          System.out.println(methodName+"afterReturning"+result);
      }
  
      //异常通知
      @AfterThrowing(value = "execution(* net.wanho.service..*.*(..) )",throwing = "ex")
      public void afterThrowing(JoinPoint joinPoint,Exception ex){
          String methodName = joinPoint.getSignature().getName();
          System.out.println(methodName+"AfterThrowing"+ex.getMessage());
      }
  
     //环绕通知
      @Around("execution(* net.wanho.service..*.*(..) )" )
      public  Object around (ProceedingJoinPoint joinPoint){
          Object result=null;
          String methodName;
          try {
              //前置通知
              System.out.println("before");
              methodName = joinPoint.getSignature().getName();
              //核心方法
              result=joinPoint.proceed();
              //返回通知
              System.out.println("afterReturning");
          }catch(Throwable e){
              System.out.println("afterThrowing");
              throw new RuntimeException(e);
          }
          //重置通知
          System.out.println("after");
          return result;
      }
  }
  ```

### 8.5.2. 指定切面的优先级 @Order

- 如果有多个切面，不确认指定顺序，会都执行，但顺序是不可控。
- 如果要按顺序加载切面，可以设置@Order(数值)
- 数值是一个int值，值越小优先级越高

```java
@Aspect
@Order(1) //优先级高
@Component
public class ValidationAspect {
   //前置通知
    @Before("execution(* net.wanho.service..*.*(..) )")
    public void beforeMethod(JoinPoint joinPoint){
       String methodName = joinPoint.getSignature().getName();
       System.out.println(methodName+"验证开始");
    }

}

@Aspect
@Component
@Order(2) //优先级较低
public class LoggingAspect {
   //前置通知
    @Before("execution(* net.wanho.service..*.*(..) )")
    public void beforeMethod(JoinPoint joinPoint){
       String methodName = joinPoint.getSignature().getName();
       System.out.println(methodName+"日志开始");
    }
}

```

### 8.5.3. 定义可重用的切点

-  在定义不同通知时，可以需要重复编写一样的织语表达式。后期维护麻烦且代码重复

- 就可以把织语表达式抽取到切点中，在通知中调用切点

  ```java
  @Pointcut("execution(* net.wanho.service..*.*(..) )")
  public void declareWeaverExpression(){}
  
  //前置通知
  @Before("declareWeaverExpression()")
  public void beforeMethod(JoinPoint joinPoint){
      String methodName = joinPoint.getSignature().getName();
      System.out.println(methodName+"日志开始");
  }
  ```

## 8.6. 基于XML的方式

### 8.6.1. 声明切面 `<aop:aspect>`

###  8.6.2. 声明切点`<aop:pointcut>`

- 但切点要定义AOP配置下 `<aop:config>`

### 8.6.3. 声明通知  

- 需要引入切点知道在哪些类些方法上加入`pointcut-ref或pointcut` 属性
- 通知还是五种 

- `<aop:before pointcut-ref="">`
- `<aop:after pointcut-ref="">`
- `<aop:after-throwing pointcut-ref="">`
- `<aop:after-returning pointcut-ref="">`
- `<aop:around pointcut-ref="">`

### 8.6.4. 示例

```xml
<!--    定义切面的类-->
<bean id="privilegeAspect" class="net.wanho.aop.PrivilegeAspect"></bean>

<!--    配置aop-->
<aop:config>
    <!--    定义切点-->
    <aop:pointcut id="privilegeCut" expression="execution(* net.wanho.service..*.*(..))"/>
    <!--        定义切面-->
    <aop:aspect ref="privilegeAspect" order="3">
        <!--            定义通知-->
        <aop:before method="before" pointcut-ref="privilegeCut"/>
    </aop:aspect>

</aop:config>
```

# 9. JdbcTemplate 简介

- Spring为了简化JDBC操作，为JDBC操作提供了模板方法，这个类即为JdbcTemplate 。
- 示例

pom.xml

```xml
 <dependency>
     <groupId>org.springframework</groupId>
     <artifactId>spring-context</artifactId>
     <version>5.1.5.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.1.5.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.1.5.RELEASE</version>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.47</version>
</dependency>

<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.6</version>
</dependency>

<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.16</version>
</dependency>

<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
</dependency>


</dependencies>
```

spring.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="net.wanho"/>

    <!-- 引入外部的db配置文件    -->
    <context:property-placeholder location="classpath:db.properties"/>

    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
        <!-- 基本属性 url、user、password -->
        <property name="driverClassName" value="${jdbc.driver}" />
        <property name="url" value="${jdbc.url}" />
        <property name="username" value="${jdbc.username}" />
        <property name="password" value="${jdbc.password}" />
    </bean>

    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>
</beans>
```



StudentDao.java

```java
@Repository
public class StudentDao {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    public void add(Student stu){
        String sql = "insert into student values(null,?,?,?,?,now())";
        jdbcTemplate.update(sql,stu.getName(),stu.getAge(),stu.getGender(),stu.getAddress());
    }

    public void deleteById(Integer id){
        jdbcTemplate.update("delete from student where id =?",id);

    }

    public void update(Student stu){
        jdbcTemplate.update("update student set name=? where id=?",stu.getName(),stu.getId());
    }

    public List<Student> findStus(){
        RowMapper rowMapper = new BeanPropertyRowMapper(Student.class);
        return jdbcTemplate.query("select * from student",rowMapper);
    }

    public Student findById(Integer id){
        RowMapper rowMapper = new BeanPropertyRowMapper(Student.class);
        return (Student) jdbcTemplate.queryForObject("select id,name name,age,gender,address,birthday from student where id =?", rowMapper,1);
    }

    public Long findCount(){
        return jdbcTemplate.queryForObject("select count(0) from student",Long.class);
    }
}

```

# 10. 事务

## 10.1. 事务位置与特性

-  事务应该位置业务逻辑层，用来保证数据完整性和有效性
- 事务ACID属性
  - A(原子性): 要么都成功，要么都失败
  - C(一致性): 操作完成后，数据提交，数据总额一致
  - I(隔离性): 可能有多个事务对同一个表操作，事务彼此之间都不知道对方的存在
  - D(持久性):一量事务提交，数据是真正保存到数据库的

## 10.2. 之前在java中事务处理，后面也通动态代理处理事务

```java

//开启事务  con.setAutoCommit(true);
//提交事务  con.commit();
//回滚事务  con.rollback();

public void transfer(){
    Connect con = null;
    con = DBUtils.getConnection();
    try{
        //开启事务
        con.setAutoCommit(false);
        bankDao.update("张三"-100);
        bankDao.update("李四"+100);
        //提交事件
        con.commit();
    }catch(Exception e){
        //回滚事务
        con.rollback();
    }finally{
    	DBUtils.closeCon();    
    }    
}
```

## 10.3. 现在Spring的AOP也可以来处理事务，有两种实现方式

- xml配置，推荐
- annotation配置

### 10.3.1.  基于xml 配置，推荐

- 四步曲

  - 第一步:pom.xml

    ```xml
    spring-jdbc
    druid
    mysql-connector
    spring-aspect
    ```

  - 第二步:配置事务管理器

  - 第三步:aop配置

  - 第四步:advisor与advice配置

    - advice配置是<tx:advice打头

  ```xml
  	<context:component-scan base-package="net.wanho"/>
  
      <!-- 引入外部的db配置文件    -->
      <context:property-placeholder location="classpath:db.properties"/>
  
      <!--1. 数据源    -->
      <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
          <!-- 基本属性 url、user、password -->
          <property name="driverClassName" value="${jdbc.driver}" />
          <property name="url" value="${jdbc.url}" />
          <property name="username" value="${jdbc.username}" />
          <property name="password" value="${jdbc.password}" />
      </bean>
  
      <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
          <property name="dataSource" ref="dataSource"/>
      </bean>
  
      <!-- 2. 配置事务管理器    -->
      <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
          <property name="dataSource" ref="dataSource"/>
      </bean>
  
      <!--  3. aop配置    -->
      <aop:config>
          <aop:pointcut id="txPointCut" expression="execution(* net.wanho.service..*.*(..))"/>
          <!-- 4.advisor配置       -->
          <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointCut"/>
      </aop:config>
  
  <!--  4.advice配置  -->
      <tx:advice id="txAdvice" transaction-manager="transactionManager">
          <tx:attributes>
              <tx:method name="add*"/>
              <tx:method name="insert*"/>
              <tx:method name="update*"/>
              <tx:method name="delete*"/>
              <tx:method name="remove*"/>
          </tx:attributes>
      </tx:advice>
  ```

StudentService.java

```java
@Service
public class StudentService {

    @Autowired
    private StudentDao studentDao;

    public void update(Student stu1,Student stu2){

        studentDao.update(stu1);
        int res=10/0;
        studentDao.update(stu2);

    }
}
```

StudentService.java

```java
 @Autowired
    private StudentService studentService;

    @Test
    public void testUpdate(){
        Student stu1 = new Student();
        stu1.setId(46);
        stu1.setName("46");
        Student stu2 = new Student();
        stu2.setId(47);
        stu2.setName("47");
        studentService.update(stu1,stu2);
    }

```

### 10.3.2.  基于annotation配置

四步曲

- 第一步:pom.xml

  ```xml
  spring-jdbc
  druid
  mysql-connector
  spring-aspect
  ```

- 第二步:配置事务管理器

  ```xml
  <context:component-scan base-package="net.wanho"/>
  
  <!-- 引入外部的db配置文件    -->
  <context:property-placeholder location="classpath:db.properties"/>
  
  <!--1. 数据源    -->
  <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
      <!-- 基本属性 url、user、password -->
      <property name="driverClassName" value="${jdbc.driver}" />
      <property name="url" value="${jdbc.url}" />
      <property name="username" value="${jdbc.username}" />
      <property name="password" value="${jdbc.password}" />
  </bean>
  
  <!-- 2. 配置事务管理器    -->
  <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
          <property name="dataSource" ref="dataSource"/>
  </bean>
  ```

  

- 第三步: 启用事务注解

  ```xml
  <!--  3. 启用事务注解  -->
  <tx:annotation-driven transaction-manager="transactionManager"/>
  ```

  

- 第四步: 在所需要添加事务的方法上加上@Transactional的注解

  - 注意：如果忘记加上注解，即未加事务

```java
@Service
public class StudentService {

    @Autowired
    private StudentDao studentDao;

    @Transactional
    public void update(Student stu1,Student stu2){

        studentDao.update(stu1);
        int res=10/0;
        studentDao.update(stu2);

    }
}

```



## 10.4. `<tx:method />` 相关设置

此标签有如下属性：

| 属性              | 是否需要？ | 默认值   | 描述                                                         |
| ----------------- | ---------- | -------- | ------------------------------------------------------------ |
| `name`            | 是         |          | 与事务属性关联的方法名。通配符（`*`）可以用来指定一批关联到相同的事务属性的方法。如：`'get*'`、`'handle*'`、`'on*Event'`等等。 |
| `propagation`     | 不         | REQUIRED | 事务传播行为                                                 |
| `isolation`       | 不         | DEFAULT  | 事务隔离级别                                                 |
| `timeout`         | 不         | -1       | 事务超时的时间（以秒为单位）                                 |
| `read-only`       | 不         | false    | 事务是否只读？                                               |
| `rollback-for`    | 不         |          | 将被触发进行回滚的 `Exception(s)`；以逗号分开。 如：`'com.foo.MyBusinessException,ServletException'` |
| `no-rollback-for` | 不         |          | *不* 被触发进行回滚的 `Exception(s)`；以逗号分开。 如：`'com.foo.MyBusinessException,ServletException'` |

### 10.4.1. 事务传播行为

- 七大行为
  - requires_new: 需要新事务，之前有事务挂起
  - required: 需要事务，有则使用，无则创建
  - supports:有支持，没有也行
  - not_supports:不支持，有则挂起
  - never:不能有事务，有则抛异常
  - mandatory:必须要有事务，没有则抛异常
  - nested:嵌入事务，之前有事务开启内嵌事务。之前没有创建事务

- xml配置

  ```xml
   <tx:method name="add*"  propagation="REQUIRES_NEW"/>
  ```

- annotation配置

  ```java
    @Transactional(propagation = Propagation.REQUIRES_NEW)
  ```

### 10.4.2. 并发

- 问题
  - 脏读
    - A事务读取B事务尚未提交的数据。如果B事务回滚数据，则A读数据是无效的，这种现象就是脏读
    - 解决：把数据库的隔离级别设置成 read committed
  - 不可重复读
    - A事务读取B事务已经提交更改的数据。假如A在取款事务过程中，先查了一下余额，就在此时B往该账户转账100，A两次读取的余额发生不一致。
    - 解决：把数据库的隔离级别设置成 read repetatable

- 解决方案: 设置隔离级别(可在数据库设置，也可以编程中设置)
  - read uncommit    :   会出现脏读

  - read committed :    会出现不可重复读（推荐）

  - repeatable read :

  - serializable :

    注：从上往下，安全级别越高，性能越低。

- 在Spring中进行设置

  - xml

    ```xml
     <tx:method name="add*"  propagation="REQUIRES_NEW" isolation="READ_COMMITTED" />
    ```

  - annotaion

    ```java
     @Transactional(propagation = Propagation.REQUIRES_NEW,isolation = Isolation.READ_COMMITTED)
    ```

### 10.4.3.  有针对回滚，了解

- 默认情况 下，只要有异常都要进行回滚。
- 如果想要设置对应的异常有针对性的回滚
  - rollback-for : 只回滚xxx异常
  - no-rollback-for: 除xxx异常都回滚

- 在Spring中进行设置

  - xml

    ```xml
    <tx:method name="add*"  propagation="REQUIRES_NEW" isolation="READ_COMMITTED"  rollback-for="java.lang.RuntimeException" no-rollback-for="java.lang.Exception" />
    ```

  - annotation

    ```java
    @Transactional(propagation = Propagation.REQUIRES_NEW,isolation = Isolation.READ_COMMITTED,rollbackFor = RuntimeException.class,noRollbackFor = Exception.class)
    ```

    

### 10.4.4. 超时时间  timeout单位为秒，了解

### 10.4.5. 只读事务 readonly=true

- 应用场合：

- 如果你一次执行单条查询语句，则没有必要启用事务支持，数据库默认支持SQL执行期间的读一致性； 
- 如果你一次执行多条查询语句，例如统计查询，报表查询，在这种场景下，多条查询SQL必须保证整体的读一致性，否则，在前条SQL查询之后，后条SQL查询之前，数据被其他用户改变，则该次整体的统计查询将会出现读数据不一致的状态，此时，应该启用事务支持。
- 【注意是一次执行多次查询来统计某些信息，这时为了保证数据整体的一致性，要用只读事务】



# 11.Spring整合MyBatis

## 11.1. 新建基于maven构建的java项目

## 11.2. pom.xml

```xml
lombok
junit
mysql-connector
druid
spring-test
spring-jdbc
spring-context
spring-aspect
logback-classic
mybatis
mybatis-spring

<plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.2</version>
                <configuration>
                    <configurationFile>src/main/resources/mybatis-generator/generatorConfig.xml</configurationFile>
                    <verbose>true</verbose>
                    <overwrite>true</overwrite>
                </configuration>
                <executions>
                    <execution>
                        <id>Generate MyBatis Artifacts</id>
                        <goals>
                            <goal>generate</goal>
                        </goals>
                    </execution>
                </executions>
                <dependencies>
                    <dependency>
                        <groupId>org.mybatis.generator</groupId>
                        <artifactId>mybatis-generator-core</artifactId>
                        <version>1.3.2</version>
                    </dependency>
                </dependencies>
            </plugin>
```

## 11.3. resources

- mybatis-generator/generatorConfig.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE generatorConfiguration
          PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
          "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
  
  <generatorConfiguration>
  
      <classPathEntry location="C:\Users\choco\.m2\repository\mysql\mysql-connector-java\5.1.47\mysql-connector-java-5.1.47.jar"/>
  
      <context id="testTables" targetRuntime="MyBatis3">
          <commentGenerator>
              <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
              <property name="suppressAllComments" value="true" />
          </commentGenerator>
          <!--数据库连接的信息：驱动类、连接地址、用户名、密码 -->
          <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                          connectionURL="jdbc:mysql://localhost:3306/studb" userId="root"
                          password="root">
          </jdbcConnection>
          <!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL 和
              NUMERIC 类型解析为java.math.BigDecimal -->
          <javaTypeResolver>
              <property name="forceBigDecimals" value="false" />
          </javaTypeResolver>
  
          <!-- targetProject:生成PO类的位置 -->
          <javaModelGenerator targetPackage="net.wanho.po"
                              targetProject="src/main/java">
              <!-- enableSubPackages:是否让schema作为包的后缀 -->
              <property name="enableSubPackages" value="false" />
              <!-- 从数据库返回的值被清理前后的空格 -->
              <property name="trimStrings" value="true" />
          </javaModelGenerator>
          <!-- targetProject:mapper映射文件生成的位置 -->
          <sqlMapGenerator targetPackage="net.wanho.mapper"
                           targetProject="src/main/resources">
              <!-- enableSubPackages:是否让schema作为包的后缀 -->
              <property name="enableSubPackages" value="false" />
          </sqlMapGenerator>
          <!-- targetPackage：mapper接口生成的位置 -->
          <javaClientGenerator type="XMLMAPPER"
                               targetPackage="net.wanho.mapper"
                               targetProject="src/main/java">
              <!-- enableSubPackages:是否让schema作为包的后缀 -->
              <property name="enableSubPackages" value="false" />
          </javaClientGenerator>
          <!-- 指定数据库表 -->
          <table schema="" tableName="student"></table>
  
      </context>
  </generatorConfiguration>
  
  ```

  

- spring

  - mybatis.xml

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

    

  - spring-mybatis.xml

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:context="http://www.springframework.org/schema/context"
           xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    
        <context:component-scan base-package="net.wanho"/>
    
        <!-- 引入外部的db配置文件    -->
        <context:property-placeholder location="classpath:properties/db.properties"/>
    
        <!--1. 数据源    -->
        <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
            <!-- 基本属性 url、user、password -->
            <property name="driverClassName" value="${jdbc.driver}" />
            <property name="url" value="${jdbc.url}" />
            <property name="username" value="${jdbc.username}" />
            <property name="password" value="${jdbc.password}" />
        </bean>
    
    
        <!-- 2. 配置sqlSessionFactory    -->
        <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
            <!--        数据源-->
            <property name="dataSource" ref="dataSource"/>
            <!--        类型别名-->
            <property name="typeAliasesPackage" value="net.wanho.po"/>
            <!--        映射文件所在的路径-->
            <property name="mapperLocations" value="net/wanho/mapper/StudentMapper.xml"/>
            <!--        mybatis的配置文件，主要负责全局参数的配置-->
            <property name="configLocation" value="classpath:spring/mybatis.xml"/>
        </bean>
    
        <!-- 3.映射代理类    -->
        <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
            <!--        接口所在的包名-->
            <property name="basePackage" value="net.wanho.mapper"/>
            <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
        </bean>
    </beans>
    ```

    

  - spring.xml

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:context="http://www.springframework.org/schema/context"
           xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
    
           xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd
           http://www.springframework.org/schema/aop
           http://www.springframework.org/schema/aop/spring-aop.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">
    
        <!--    注解扫描-->
        <context:component-scan base-package="net.wanho"/>
    
        <!--    事务-->
        <import resource="classpath:spring/spring-transaction.xml"/>
    
    
    </beans>
    ```

    

  - spring-transaction.xml

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
           xmlns:tx="http://www.springframework.org/schema/tx"
           xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">
    
        <!--    1.事务管理-->
        <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
            <property name="dataSource" ref="dataSource"/>
        </bean>
    
        <!--    2.事务-->
        <aop:config>
            <aop:pointcut id="txPointcut" expression="execution(* net.wanho.service..*.*(..))"/>
            <aop:advisor advice-ref="txtAdvice" pointcut-ref="txPointcut"/>
        </aop:config>
    
        <!--    3.advice-->
        <tx:advice id="txtAdvice"  transaction-manager="transactionManager">
            <tx:attributes>
                <tx:method name="find*" read-only="true"/>
                <tx:method name="select*" read-only="true"/>
                <tx:method name="get*" read-only="true"/>
                <tx:method name="add*" isolation="READ_COMMITTED"/>
                <tx:method name="insert*" isolation="READ_COMMITTED"/>
                <tx:method name="delete*" isolation="READ_COMMITTED"/>
                <tx:method name="remove*" isolation="READ_COMMITTED"/>
                <tx:method name="update*" isolation="READ_COMMITTED"/>
            </tx:attributes>
        </tx:advice>
    </beans>
    ```

    

- properties

  - db.properties

    ```prop
    jdbc.driver=com.mysql.jdbc.Driver
    jdbc.url=jdbc:mysql://localhost:3306/studb
    jdbc.username=root
    jdbc.password=root
    ```

    

- logback.xml

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

  ## 11.3. StudentService.java 

  ```java
  @Service
  public class StudentService {
  
      @Autowired
      private StudentMapper studentMapper;
  
      public List<Student> findByPage(){
          StudentExample example = new StudentExample();
          List<Student> stus=studentMapper.selectByExample(example);
          return stus;
      }
  }
  
  ```

  

  ## 11.4. StudentServiceTest.java 

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"classpath:spring/spring-mybatis.xml", "classpath:spring/spring.xml"})
public class StudentServiceTest {


    @Autowired
    private StudentService studentService;

    @Test
    public void findByPage() {
        List<Student> stus = studentService.findByPage();
        System.out.println(stus);
    }
}
```













































