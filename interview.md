# 2022.05.31 安恒信息

## 1、讲一下用了什么技术怎么实现这个项目

## 2、MyBatis 动态sql拼接

### if标签

用if标签对传入的参数进行非空操作。使用if标签来判断是否为空，如果为空则不参与sql执行。注意 两个判断条件之间用and来连接 不可以用&&符号。

```xml
<select id="findUserByConditions" parameterType="user" resultType="user">
        select * from user where 1 = 1
        <if test="username != null and username != ''">
            and username = #{username}
        </if>
        <if test="id != null and id != ''">
            and id = #{id}
        </if>
</select>
```

### where标签

在上述语句中 我们使用where 1= 1的写法，来保证sql的正常执行，其实mybatis为我们提供了where标签 来帮我们做这件事情，所以上面的sql我们也可以写成下面这种

where标签就是帮我们完成where 1= 1这样的操作。

```xml
<select id="findUserByConditions" parameterType="user" resultType="user">
        select * from user
        <where>
            <if test="username != null and username != ''">
                and username = #{username}
            </if>
            <if test="id != null and id != ''">
                and id = #{id}
            </if>
        </where>
</select>
```

### foreach标签（重点）

在开发过程中，很多时候我们都需要根据多个id查询记录 这个时候就要用到in关键字。如下

当我们使用list作为入参时，我们接口中的方法为

```java
List<User> findUserByIds(List ids);
```

xml中的sql为

```xml
<select id="findUserByIds" parameterType="list" resultType="user">
        select * from user
        <where>
            <if test = "list != null and list.size() != 0">
              <foreach collection="list" open="and id in (" close=")" item="id" separator=",">
                  #{id}
              </foreach>
            </if>
        </where>
</select>
```

### xml配置文件总结：

1. 使用where标签帮我们完成 where 1= 1的操作
2. 使用test标签判断传入的list是否为空，为空则不参与sql的执行
3. 使用foreach标签 来循环我们的list集合

### foreach标签详解：

foreach标签用来帮我们循环参数，类似java中的for循环。其中collection属性为传入的集合名称，这里需要注意，在我们接口方法中定义的参数为ids，但是在这里必须是list，因为mybatis在为我们的sql传参时，如果是list类型则会将其封装到Map中，其中key为list，所以collection = "list"的含义其实是 map.get(list); open属性则为在循环开始之前拼接的字符串，因为现在我们要根据id去查询 所以这里要写为 open = "and id in ("; close属性为结束循环时拼接的字符串，在这里就是 close = ")"; item属性为每次循环时，我们为list中的每个值赋予的别名， 注意：item属性的名称需要与#{}中的名称保持一致，比如这里我们的item="id"则下面要写为#{id}；separator属性则是在每次循环结束后拼接的字符串。



## 3、#{}和${}的区别是什么？ 

${} 是 Properties ⽂件中的变量占位符，它可以⽤于标签属性值和 sql 内部，属于静态⽂本替换，⽐如${driver}会被静态替换为 com.mysql.jdbc.Driver 。

#{} 是 sql 的参数占位符， Mybatis 会将 sql 中的 #{} 替换为?号，在 sql 执⾏前会使⽤PreparedStatement 的参数设置⽅法，按序给 sql 的?号占位符设置参数值，⽐如ps.setInt(0, parameterValue)， #{item.name} 的取值⽅式为使⽤反射从参数对象中获取item 对象的 name 属性值，相当param.getItem().getName() 



1.#{}是预编译处理，是占位符，${}是字符串替换，是拼接符

2.Mybatis在处理**#{}**的时候会将sql中的#{}替换成？号，调用**PreparedStatement**来赋值

如：select * from user where name = #{userName}；设userName=yuze

看日志我们可以看到解析时将#{userName}替换成了 ？

select * from user where name = ?;

3.Mybatis在处理**${}**的时候就是把${}替换成变量的值，调用**Statement**来赋值

如：select * from user where name = ${userName}；设userName=yuze

看日志可以发现就是直接把值拼接上去了

select * from user where name = yuze;

这极有可能发生sql注入

尽量使用**#{}**

## 4、MyBatis实现sql查询和JDBC的有什么不同？ statement类？Pre？...？类

1、mybatis把SQL语句从Java代码中抽取出来，方便维护。并且修改SQL时不需要修改Java代码，不用手动设置参数和对结果集的处理 。

2、JDBC数据库连接，使用时就创建，不使用立即释放，对数据库进行频繁连接开启和关闭，造成数据库资源浪费，影响数据库性能。

mybatis在SqlMapConfig.xml中配置数据链接池，使用连接池管理数据库链接。

3、**jdbc向preparedStatement中设置参数**，对占位符号位置和设置参数值，硬编码在java代码中，不利于系统维护。这种方式本身就有一定发 局限性，它是按照一定顺序传入参数的，要与占位符一一匹配。但是，如果我们传入的参数是不确定的（比如列表查询，根据用户填写的查询条件不同，传入查询的参数也是不同的，有时是一个参数、有时可能是三个参数），那么我们就得在后台代码中自己根据请求的传入参数去拼凑相应的SQL语句。

**Mybatis自动将java对象映射至sql语句，通过statement中的parameterType定义输入参数的类型**。更方便的是，Mybatis将sql语句及占位符号和参数全部配置在xml中。

4、 jdbc从resutSet中遍历结果集数据时，存在硬编码，将获取表的字段进行硬编码，不利于系统维护。

Mybatis自动将sql执行结果集自动映射成java对象，通过statement中的resultType定义输出结果的类型。

5、mybatis通过if where trim foreach set 等标签进行动态sql的编写

## 5、身份验证？ token？

![img](https://img-blog.csdnimg.cn/b313afd8f6074d15bb24ecf219780d90.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5qGR6Iux6LGq,size_20,color_FFFFFF,t_70,g_se,x_16)

基于 Token 的身份验证是无状态的，我们不用将用户信息存在服务器或 Session 中。

大致过程：

1. 用户通过用户名和密码发送请求。
2. 程序验证。
3. 程序返回一个签名的 token 给客户端。
4. 客户端储存 token, 并且每次请求都会附带它。
5. 服务端验证 token 并返回数据。

每一次请求都需要Token。Token 应该在 HTTP的头部发送从而保证了 Http 请求无状态。我们也需要设置服务器属性来让服务器能接受到来自所有域的请求.

```yaml
Access-Control-Allow-Origin: *
```

实现思路：

1.用户登录校验，校验成功后就返回Token给客户端。

2.客户端收到数据后保存在客户端

3.客户端每次访问API是携带Token到服务器端。

4.服务器端采用filter过滤器校验。校验成功则返回请求数据，校验失败则返回错误码

## 6、浏览器发送请求，token要一起发送吗？

要一起发送

当用户A已经登录时，服务器给用户A发送一个token（令牌），该token中包含了用户A的user id。重点是！！！服务器用了某种算法，已经对token进行了加密（形成了签名1），只有服务器才有密钥，所以别人不能伪造这个token了。服务器并不保存这个token，而是用户A保存。

2：每当用户A向服务器发送请求时，在请求头header中附带了自己的token（token中携带了user id和签名1）。

3：服务器用专属密钥对user id重新签名（签名2），并判断签名1和签名2是否相等。若相等，则告诉用户A可以成功登陆，反之，则需要用户重新认证。

这样服务器就不用存储session id，只要计算签名是否一致就可以。时间换空间

目前FaceBook、Twitter、Google+、Github的部分API和Web应用都使用到了tokens
————————————————
版权声明：本文为CSDN博主「SFONE_CC」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_38867012/article/details/123504000

## 7、选择你项目的一个模块，讲讲怎么用AOP实现它？

AOP常用的实现方式有两种，一种是采用声明的方式来实现（基于XML），一种是采用注解的方式来实现（基于AspectJ）。

AOP中一些比较重要的概念：

**Joinpoint（连接点）：**程序执行时的某个特定的点，在Spring中就是某一个方法的执行 。
**Pointcut（切点）：**说的通俗点，spring中AOP的切点就是指一些方法的集合，而这些方法是需要被增强、被代理的。一般都是按照一定的约定规则来表示的，如正则表达式等。切点是由一类连接点组成。 
**Advice（通知)：**还是说的通俗点，就是在指定切点上要干些什么。 
**Advisor（通知器)：**其实就是切点和通知的结合 。

### 7.1定义业务接口和业务实现

```java
package com.springboottime.time.service;

public interface AdviceService {
    /*查找用户*/
    public String findUser();
```


```java
package com.springboottime.time.service.serviceImpl;
 
import com.springboottime.time.service.AdviceService;
import lombok.Data;
 
@Data
public class AdviceServiceImpl implements AdviceService {
 
    private String name;
 
    @Override
    public String findUser() {
        System.out.println("***************执行业务方法findUser,查找的用户名字为:"+name+"****************");
        return name;
    }
 
    @Override
    public void addUser() {
        System.out.println("***************执行业务方法addUser****************");
        throw new RuntimeException();
    }
```
### 7.2切面类

```java
package com.springboottime.time.aop;
 
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
 
@Aspect
public class AopAspectJ {
 
    /**
     * 必须为final String类型的,注解里要使用的变量只能是静态常量类型的
     */
    public static final String EDP="execution(* com.springboottime.time.service.serviceImpl.AdviceServiceImpl..*(..))";
 
    /**
     * 切面的前置方法 即方法执行前拦截到的方法
     * 在目标方法执行之前的通知
     * @param jp
     */
    @Before(EDP)
    public void doBefore(JoinPoint jp){
 
        System.out.println("=========AopAspectJ执行前置通知==========");
    }
 
 
    /**
     * 在方法正常执行通过之后执行的通知叫做返回通知
     * 可以返回到方法的返回值 在注解后加入returning
     * @param jp
     * @param result
     */
    @AfterReturning(value=EDP,returning="result")
    public void doAfterReturning(JoinPoint jp,String result){
        System.out.println("===========AopAspectJ执行后置通知============");
    }
 
    /**
     * 最终通知：目标方法调用之后执行的通知（无论目标方法是否出现异常均执行）
     * @param jp
     */
    @After(value=EDP)
    public void doAfter(JoinPoint jp){
        System.out.println("===========AopAspectJ执行最终通知============");
    }
 
    /**
     * 在目标方法非正常执行完成, 抛出异常的时候会走此方法
     * @param jp
     * @param ex
     */
    @AfterThrowing(value=EDP,throwing="ex")
    public void doAfterThrowing(JoinPoint jp,Exception ex) {
        System.out.println("===========AopAspectJ执行异常通知============");
    }
 
    /**
     * 环绕通知：目标方法调用前后执行的通知，可以在方法调用前后完成自定义的行为。
     * @param pjp
     * @return
     * @throws Throwable
     */
    @Around(EDP)
    public Object doAround(ProceedingJoinPoint pjp) throws Throwable{
 
        System.out.println("======AopAspectJ执行环绕通知开始=========");
        // 调用方法的参数
        Object[] args = pjp.getArgs();
        // 调用的方法名
        String method = pjp.getSignature().getName();
        // 获取目标对象
        Object target = pjp.getTarget();
        // 执行完方法的返回值
        // 调用proceed()方法，就会触发切入点方法执行
        Object result=pjp.proceed();
        System.out.println("输出,方法名：" + method + ";目标对象：" + target + ";返回值：" + result);
        System.out.println("======AopAspectJ执行环绕通知结束=========");
        return result;
    }
 
}
```
### 7.3 spring的配置:spring-aop-aspectJ.xml

    <?xml version="1.0" encoding="UTF-8"?>
    <beans
            xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:aop="http://www.springframework.org/schema/aop"
            xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop-3.0.xsd">
     
        <!-- 声明spring对@AspectJ的支持 -->
        <aop:aspectj-autoproxy/>
     
        <!-- 声明一个业务类 -->
        <bean id="userManager" class="com.springboottime.time.service.serviceImpl.AdviceServiceImpl">
            <property name="name" value="lixiaoxi"></property>
        </bean>
     
        <!-- 声明通知类 -->
        <bean id="aspectBean" class="com.springboottime.time.aop.AopAspectJ" />
    </beans>
### 7.4 springboot启动类配置:

    package com.springboottime.time;
     
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.context.annotation.ImportResource;
     
    //@ImportResource("classpath:spring-aop.xml")
    @ImportResource("classpath:spring-aop-aspectJ.xml")
    @SpringBootApplication
    public class TimeApplication {
     
        public static void main(String[] args) {
            SpringApplication.run(TimeApplication.class, args);
        }
     
    }

### 7.5 测试结果

======AopAspectJ执行环绕通知开始=========
=========AopAspectJ执行前置通知==========
***************执行业务方法findUser,查找的用户名字为:lixiaoxi****************
输出,方法名：findUser;目标对象：AdviceServiceImpl(name=lixiaoxi);返回值：lixiaoxi
======AopAspectJ执行环绕通知结束=========
===========AopAspectJ执行最终通知============
===========AopAspectJ执行后置通知============
<><><><><><><><><><><><><>
======AopAspectJ执行环绕通知开始=========
=========AopAspectJ执行前置通知==========
***************执行业务方法addUser****************
===========AopAspectJ执行最终通知============
===========AopAspectJ执行异常通知============

java.lang.RuntimeException
	at com.springboottime.time.service.serviceImpl.AdviceServiceImpl.addUser(AdviceServiceImpl.java:20)



# 2022.06.06 三和软件

## 1、什么是微服务

为适应企业的业务发展，提高软件研发的生产力，降低软件研发的成本，软件架构也作了升级和优化，将一个独立的系统拆分成若干小的服务，每个小服务运行在不同的进程中，服务与服务之间采用 http 轻量协议（比如流行的 RESTful）传输数据，每个服务所拥有的功能具有独立性强、高内聚的特点，这样的设计就实现了单个服务的高内聚，服务与服务之间的低耦合效果，这一个一个的小服务就是微服务，基于这种方法设计的系统架构即微服务架构。
————————————————
版权声明：本文为CSDN博主「澜la」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/luo_xia530/article/details/122382517

## 2、Mybatis和MyBatis-Plus的区别？分页插件用过吗？

Mybatis-Plus是一个Mybatis的增强工具，只是在Mybatis的基础上做了增强却不做改变，MyBatis-Plus支持所有Mybatis原生的特性，所以引入Mybatis-Plus不会对现有的Mybatis构架产生任何影响。

**MyBatis**:

所有SQL语句全部自己写
手动解析实体关系映射转换为MyBatis内部对象注入容器
不支持Lambda形式调用
**Mybatis Plus**:

强大的条件构造器,满足各类使用需求
内置的Mapper,通用的Service,少量配置即可实现单表大部分CRUD操作
支持Lambda形式调用
提供了基本的CRUD功能,连SQL语句都不需要编写
自动解析实体关系映射转换为MyBatis内部对象注入容器
————————————————
版权声明：本文为CSDN博主「web15185420056」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/web15185420056/article/details/123987543

**分页插件**

Ans1:

PageHelper是MyBatis的一个插件，内部实现了一个PageInterceptor拦截器。Mybatis会加载这个拦截器到拦截器链中。在我们使用过程中先使用PageHelper.startPage这样的语句在当前线程上下文中设置一个ThreadLocal变量，再利用PageInterceptor这个分页拦截器拦截，从ThreadLocal中拿到分页的信息，如果有分页信息拼装分页SQL（limit语句等）进行分页查询，最后再把ThreadLocal中的东西清除掉。

https://blog.csdn.net/fedorafrog/article/details/104412140

Ans2:

分⻚插件的基本原理是使⽤ Mybatis 提供的插件接⼝，实现⾃定义插件，在插件的拦截⽅法内拦截待执⾏的 sql，然后重写 sql，根据 dialect ⽅⾔，添加对应的物理分⻚语句和物理分⻚参数。  

举例： select _ from student ，拦截 sql 后重写为： select t._ from (select \* from student) t limit 0, 10

## 3、怎么添加索引？

在没有索引的情况下，数据库会遍历全部200条数据后选择符合条件的；而有了相应的索引之后，数据库会直接在索引中查找符合条件的选项。如果我们把SQL语句换成“SELECT * FROM article WHERE id=2000000”，那么你是希望数据库按照顺序读取完200万行数据以后给你结果还是直接在索引中定位呢？（注：一般数据库默认都会为主键生成索引）。

常用的索引类型
1、普通索引
2、唯一索引
3、组合索引
**普通索引和唯一索引的创建方式**有三种，分别是**直接创建**、**修改表结构创建**、**创建表时同时创建**，注意组合索引的组合规则是最左前缀索引

#普通索引
CREATE TABLE test1(
id TINYINT UNSIGNED,
username VARCHAR(20),
INDEX in_id(id),
KEY in_username(username));

#删除索引，方法一
DROP INDEX in_id ON test1;

#删除索引，方法二
ALTER TABLE test1 DROP INDEX in_username;

#在已创建的表中创建索引,方法一
CREATE INDEX in_id ON test1(id);

#在已创建的表中创建索引,方法二
ALTER TABLE test1 ADD INDEX in_username(username);

#唯一索引
CREATE TABLE test2(
id TINYINT UNSIGNED AUTO_INCREMENT KEY,
username VARCHAR(20) NOT NULL UNIQUE,
card CHAR(16) NOT NULL,
UNIQUE KEY uni_card(card));

DROP INDEX uni_card ON test2;
CREATE INDEX uni_card ON test2(card);

#全文索引
CREATE TABLE test3(
id TINYINT UNSIGNED AUTO_INCREMENT KEY,
username VARCHAR(20) NOT NULL UNIQUE,
userDesc VARCHAR(20) NOT NULL,
FULLTEXT full_userDesc(userDesc));

#单列索引
CREATE TABLE test4(
id TINYINT UNSIGNED AUTO_INCREMENT KEY,
test1 VARCHAR(20) NOT NULL,
test2 VARCHAR(20) NOT NULL,
test3 VARCHAR(20) NOT NULL,
test4 VARCHAR(20) NOT NULL,
INDEX in_test(test1));

#多列索引
CREATE TABLE test5(
id TINYINT UNSIGNED AUTO_INCREMENT KEY,
test1 VARCHAR(20) NOT NULL,
test2 VARCHAR(20) NOT NULL,
test3 VARCHAR(20) NOT NULL,
test4 VARCHAR(20) NOT NULL,
INDEX mul_t1_t2_t3(test1, test2, test3));

#唯一多列索引
CREATE TABLE test6(
id TINYINT UNSIGNED AUTO_INCREMENT KEY,
test1 VARCHAR(20) NOT NULL,
test2 VARCHAR(20) NOT NULL,
test3 VARCHAR(20) NOT NULL,
test4 VARCHAR(20) NOT NULL,
UNIQUE KEY mul_t1_t2_t3(test1, test2, test3));

#空间索引
CREATE TABLE test7(
id TINYINT UNSIGNED AUTO_INCREMENT KEY,
test GEOMETRY NOT NULL,
SPATIAL KEY `spa_test`(`test`)) ENGINE = MYISAM;



## 4、什么是跨域？怎么解决？

### **什么是跨域**：

当一个请求url的**协议、域名、端口**三者之间任意一个与当前页面url不同即为跨域。

### **为什么出现跨域：**

出于浏览器的同源策略限制。同源策略（Sameoriginpolicy）是一种约定，它是浏览器最核心也最基本的安全功能，如果缺少了同源策略，则浏览器的正常功能可能都会受到影响。可以说Web是构建在同源策略基础之上的，浏览器只是针对同源策略的一种实现。同源策略会阻止一个域的javascript脚本和另外一个域的内容进行交互。所谓同源（即指在同一个域）就是两个页面具有相同的协议（protocol），主机（host）和端口号（port）。

![img](https://img-blog.csdnimg.cn/img_convert/597a6e9d6370ad20c44814bd503e907f.png)

### **如何解决：**

#### 4.1方法或者类上标注@CrossOrigin注解

在Controller层对应的方法上添加@CrossOrigin

```java
@RestController
@RequestMapping("index")
public class IndexController {

    @GetMapping("/test")
    @CrossOrigin
    public String index() {
        return "hello world";
    }
}
```
也可以加在类上

#### 4.2添加CORS过滤器（本项目用的方法）

新建配置类CorsConfig，创建CorsFilter过滤器，允许跨域

```java
@Configuration
public class CorsConfig {
    // 跨域请求处理
    @Bean
    public CorsFilter corsFilter() {
        CorsConfiguration config = new CorsConfiguration();
        //允许所有域名进行跨域调用
        config.addAllowedOrigin("*");
        //允许所有请求头
        config.addAllowedHeader("*");
        //允许所有方法
        config.addAllowedMethod("*");
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", config);
        return new CorsFilter(source);
    }
}
```

```java
package com.jackson0714.passjava.gateway.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.reactive.CorsWebFilter;
import org.springframework.web.cors.reactive.UrlBasedCorsConfigurationSource;

@Configuration
public class PassJavaCorsConfiguration {

    @Bean
    public CorsWebFilter corsWebFilter() {
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();

        CorsConfiguration corsConfiguration = new CorsConfiguration();

        // 配置跨域
        corsConfiguration.addAllowedHeader("*"); // 允许所有请求头跨域
        corsConfiguration.addAllowedMethod("*"); // 允许所有请求方法跨域
        corsConfiguration.addAllowedOrigin("*"); // 允许所有请求来源跨域
        corsConfiguration.setAllowCredentials(true); //允许携带cookie跨域，否则跨域请求会丢失cookie信息

        source.registerCorsConfiguration("/**", corsConfiguration);

        return new CorsWebFilter(source);
    }
}
```

#### 4.3实现WebMvcConfigurer，重写addCorsMappings方法

```java
@Configuration
public class CorsConfiguration implements WebMvcConfigurer{

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedOriginPatterns("*")
                .allowCredentials(true)
                .allowedMethods("GET", "POST", "DELETE", "PUT")
                .maxAge(3600);
    }
}
```



## 5、为什么是三次握手而不是四次、五次？

最主要的原因是：为了防止已失效的连接请求报文段突然又传送到了服务端，因而产生错误，也就是网络中存在延迟的重复分组。

引用谢希仁版《计算机网络》中的例子

“已失效的连接请求报文段”的产生在这样一种情况下：client发出的第一个连接请求报文段并没有丢失，而是在某个网络结点长时间的滞留了，以致延误到连接释放以后的某个时间才到达server。本来这是一个早已失效的报文段。但server收到此失效的连接请求报文段后，就误认为是client再次发出的一个新的连接请求。于是就向client发出确认报文段，同意建立连接。
假设不采用“三次握手”，那么只要server发出确认，新的连接就建立了。由于现在client并没有发出建立连接的请求，因此不会理睬server的确认，也不会向server发送数据。但server却以为新的运输连接已经建立，并一直等待client发来数据。这样，server的很多资源就白白浪费掉了。采用“三次握手”的办法可以防止上述现象发生。例如刚才那种情况，client不会向server的确认发出确认。server由于收不到确认，就不知道client并没有要求建立连接。

理论上讲不论握手多少次都不能确认一条信道是“可靠”的，但通过3次握手可以至少确认它是“可用”的，再往上加握手次数不过是提高“它是可用的”这个结论的可信程度。
————————————————
版权声明：本文为CSDN博主「码农研究僧」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/weixin_47872288/article/details/123166180



20220812补充  TCP为什么三次握手和四次挥手

https://mp.weixin.qq.com/s/LHaImSd-sTvt7QWqisE21w

## 6、JVM里面有什么？

Java 虚拟机在执⾏ Java 程序的过程中会把它管理的内存划分成若⼲个不同的数据区域。

 **JVM由JVM运行时数据区（图示中蓝色框包含部分）、执行引擎、本地库接口、本地方法库组成。**

 **JVM运行时数据区，分为方法区、堆、虚拟机栈、本地方法栈和程序计数器。**



<img src="E:\2021java\Typora\typora-user-images\363636d2b3e715a8a99d3c0dc846c045.png" alt="img" style="zoom:67%;" />

**线程私有的：**程序计数器、虚拟机栈、本地方法栈

**线程共享的**：堆、方法区、直接内存 (⾮运⾏时数据区的⼀部分)。

### 6.1**程序计数器的两个作用**

1. 字节码解释器通过改变程序计数器来依次读取指令，从⽽实现代码的流程控制，如：顺序执⾏、选择、循环、异常处理。
2. 在多线程的情况下，程序计数器⽤于记录当前线程执⾏的位置，从⽽当线程被切换回来的时候能够知道该线程上次运⾏到哪⼉了。

### 6.2虚拟机栈

它的⽣命周期和线程相同，**描述的是 Java⽅法执⾏的内存模型**，每次⽅法调⽤的数据都是通过栈传递的。

Java 内存可以粗糙的区分为堆内存（Heap）和栈内存 (Stack),其中**栈就是现在说的虚拟机栈，或者说是虚拟机栈中局部变量表部分**。 

（实际上， Java 虚拟机栈是由⼀个个栈帧组成，⽽每个栈帧中都拥有：局部变量表、操作数栈、动态链接、⽅法出⼝信息。）

**局部变量表**主要存放了编译期可知的**各种数据类型**（boolean、 byte、 char、 short、 int、 float、long、 double）、 **对象引⽤**（reference 类型，它不同于对象本身，可能是⼀个指向对象起始地址的引⽤指针，也可能是指向⼀个代表对象的句柄或其他与此对象相关的位置）。

**Java 虚拟机栈会出现两种错误**： StackOverFlowError 和 OutOfMemoryError 。

**StackOverFlowError** ： 若 Java 虚拟机栈的内存⼤⼩不允许动态扩展，那么当线程请求栈的深度超过当前 Java 虚拟机栈的最⼤深度的时候，就抛出 StackOverFlowError 错误。
**OutOfMemoryError** ： 若 Java 虚拟机堆中没有空闲内存，并且垃圾回收器也⽆法提供更多内存的话。就会抛出 OutOfMemoryError 错误。  

**那么⽅法/函数如何调⽤？**  

Java 栈可⽤类⽐数据结构中栈， Java 栈中保存的主要内容是栈帧，每⼀次函数调⽤都会有⼀个对应的栈帧被压⼊ Java 栈，每⼀个函数调⽤结束后，都会有⼀个栈帧被弹出。  

**Java ⽅法有两种返回⽅式**： return 语句、抛出异常。不管哪种返回⽅式都会导致栈帧被弹出。  

### 6.3本地方法栈

和虚拟机栈所发挥的作⽤⾮常相似，区别是： **虚拟机栈为虚拟机执⾏ Java ⽅法** （也就是字节码）服务，⽽**本地⽅法栈则为虚拟机使⽤到的 Native ⽅法服务**。 在 HotSpot 虚拟机中和 Java虚拟机栈合⼆为⼀。

本地⽅法被执⾏的时候，在本地⽅法栈也会创建⼀个栈帧，⽤于存放该本地⽅法的局部变量表、操作数栈、动态链接、出⼝信息。

⽅法执⾏完毕后相应的栈帧也会出栈并释放内存空间，也会出现 StackOverFlowError 和OutOfMemoryError 两种错误。

### 6.4堆

Java 虚拟机所管理的内存中最⼤的⼀块， Java 堆是所有线程共享的⼀块内存区域，在虚拟机启动时创建。 此内存区域的唯⼀⽬的就是存放对象实例，⼏乎所有的对象实例以及数组都在这⾥分配内存。  

Java 堆是垃圾收集器管理的主要区域，因此也被称作GC 堆 。

### 6.5方法区

它⽤于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。

⽅法区和永久代的关系：  ⽅法区和永久代的关系很像Java 中接⼝和类的关系，类实现了接⼝，⽽永久代就是 HotSpot 虚拟机对虚拟机规范中⽅法区的⼀种实现⽅式。  

JDK1.8 hotspot移除了永久代⽤元空间(Metaspace)取⽽代之, 这时候字符串常量池还在堆, 运⾏时常量池还在⽅法区, 只不过⽅法区的实现从永久代变成了元空间(Metaspace)

### 6.6运行时常量池

运⾏时常量池是⽅法区的⼀部分 。



## 7、IOC、AOP是什么？

​	IoC（Inverse of Control:控制反转）是⼀种设计思想，就是 将原本在程序中⼿动创建对象的控制权，交由Spring框架来管理。 IoC 在其他语⾔中也有应⽤，并⾮ Spring 特有。 IoC 容器是Spring ⽤来实现 IoC 的载体， IoC 容器实际上就是个Map（key， value） ,Map 中存放的是各种对象。

​	将对象之间的相互依赖关系交给 IoC 容器来管理，并由 IoC 容器完成对象的注⼊。这样可以很⼤程度上简化应⽤的开发，把应⽤从复杂的依赖关系中解放出来。

​	IOC初始化过程：

![image-20220607190708037](C:\Users\陈康瑞\AppData\Roaming\Typora\typora-user-images\image-20220607190708037.png)



​	AOP(Aspect-Oriented Programming:⾯向切⾯编程)能够将那些与业务⽆关， 却为业务模块所共同调⽤的逻辑或责任（例如事务处理⽇志管理、权限控制等）封装起来，便于减少系统的重复代码， 降低模块间的耦合度，并有利于未来的可拓展性和可维护性。

​	Spring AOP就是基于动态代理的，如果要代理的对象，实现了某个接⼝，那么Spring AOP会使⽤JDK Proxy，去创建代理对象，⽽对于没有实现接⼝的对象，就⽆法使⽤ JDK Proxy 去进⾏代理了，这时候Spring AOP会使⽤Cglib ，这时候Spring AOP会使⽤ Cglib ⽣成⼀个被代理对象的⼦类来作为代理，

## 8、用过Redis吗？

http://www.passjava.cn/#/88.Interview/01.Redis/Redis1

## 9、谈谈操作系统？

​	1.操作系统（Operating System，简称 OS）是管理计算机**硬件**与**软件**资源的程序，是计算机的基⽯。

2. 操作系统本质上是⼀个运⾏在计算机上的软件程序 ，⽤于管理计算机硬件和软件资源。 举例：运⾏在你电脑上的所有应⽤程序都通过操作系统来**调⽤系统内存以及磁盘**等等硬件。
3. 操作系统存在屏蔽了硬件层的复杂性。 操作系统就像是硬件使⽤的负责⼈，统筹着各种相关事项。
4. 操作系统的**内核**（Kernel）是操作系统的核⼼部分，它负责系统的内存管理，硬件设备的管理，⽂件系统的管理以及应⽤程序的管理。 内核是连接应⽤程序和硬件的桥梁，决定着系统的性能和稳定性。

**操作系统管理内存**：操作系统的内存管理主要负责**内存的分配与回收**（malloc 函数：申请内存， free 函数：释放内存），另外**地址转换**也就是将逻辑地址转换成相应的物理地址等功能也是操作系统内存管理做的事情。  

**内存管理机制：**

1.连续分配管理方式：为⼀个⽤户程序分配⼀个连续的内存空间（块式管理）

2.非连续分配管理方式：允许⼀个程序使⽤的内存分布在离散或者说不相邻的内存中（页式管理、段式管理）

3.段⻚式管理 ：结合了段式管理和⻚式管理的优点。简单来说段⻚式管理机制就是把主存先分成若⼲段，每个段⼜分成若⼲⻚，也就是说 段⻚式管理机制 中段与段之间以及段的内部的都是离散的。

**快表和多级页表：**

在分⻚内存管理中，很重要的两点是：
1. 虚拟地址到物理地址的转换要快。
2. 解决虚拟地址空间⼤，⻚表也会很⼤的问题。

快表：为了解决虚拟地址到物理地址的转换速度，操作系统在 ⻚表⽅案 基础之上引⼊了 快表 来加速虚拟地址到物理地址的转换。我们可以把快表理解为⼀种特殊的⾼速缓冲存储器（Cache），其中的内容是⻚表的⼀部分或者全部内容。作为⻚表的 Cache，它的作⽤与⻚表相似，但是提⾼了访问速率。

多级页表：引⼊多级⻚表的主要⽬的是为了避免把全部⻚表⼀直放在内存中占⽤过多空间，特别是那些根本就不需要的⻚表就不需要保留在内存中。多级⻚表属于时间换空间的典型场景 。

## 10、内存溢出发生在什么时候？

内存溢出是指应用系统中存在无法回收的内存或使用的内存过多，最终使得程序运行要用到的内存大于虚拟机能提供的最大内存。

**原因及解决：**

1.内存中加载的数据量过于庞大，如一次从数据库取出过多数据。

解决方法：检查对数据库查询中，是否有一次获得全部数据的查询；对于数据库查询尽量采用分页的方式查询。

2.集合类中有对对象的引用，使用完后未清空，使得JVM不能回收。

解决方法：检查List、MAP等集合对象是否有使用完后，未清除的问题。List、MAP等集合对象会始终存有对对象的
引用，使得这些对象不能被GC回收。

3.代码中存在死循环或循环产生过多重复的对象实体。

解决方法：检查代码中是否有死循环或递归调用；检查是否有大循环重复产生新对象实体。

4.使用的第三方软件中的BUG。

解决方法：使用内存查看工具动态查看内存使用情况。

5.启动参数内存值设定的过小；

解决方法：修改JVM启动参数(-Xms，-Xmx)，直接增加内存。
————————————————
版权声明：本文为CSDN博主「Rick1993」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/thqtzq/article/details/124251110

## 11、说说GC垃圾回收？

Java 堆是垃圾收集器管理的主要区域，因此也被称作GC 堆（Garbage Collected Heap） 。从垃圾回收的⻆度，由于现在收集器基本都采⽤**分代垃圾收集算法**，所以 Java 堆还可以细分为：新⽣代和⽼年代，新生代可分为： Eden 空间、 From Survivor、 To Survivor 空间等。 进⼀步划分的⽬的是更好地回收内存，或者更快地分配内存。 

Eden 区、 From Survivor0("From") 区、 To Survivor1("To") 区都属于新⽣代， OldMemory 区属于⽼年代。

堆内存中对象的分配的基本策略  

![image-20220608124336332](C:\Users\陈康瑞\AppData\Roaming\Typora\typora-user-images\image-20220608124336332.png)

### 11.1对象优先在 eden 区分配 ：

⽬前主流的垃圾收集器都会采⽤分代回收算法，因此需要将堆内存分为新⽣代和⽼年代，这样我们就可以根据各个年代的特点选择合适的垃圾收集算法。  

⼤多数情况下，对象在新⽣代中 eden 区分配。当 eden 区没有⾜够空间进⾏分配时，虚拟机将发起⼀次 Minor GC。

### 11.2⼤对象直接进⼊⽼年代 

⼤对象就是需要⼤量连续内存空间的对象（⽐如：字符串、数组）。原因：为了**避免**为⼤对象分配内存时由于分配担保机制带来的**复制⽽降低效率**。  

### 11.3⻓期存活的对象将进⼊⽼年代  ：

如果对象在 Eden 出⽣并经过第⼀次 Minor GC 后仍然能够存活，并且能被 Survivor 容纳的话，将被移动到 Survivor 空间中，并将对象年龄设为 1.对象在 Survivor 中每熬过⼀次 MinorGC,年龄就增加 1 岁，当它的年龄增加到⼀定程度（默认为 15 岁），就会被晋升到⽼年代中。对象晋升到⽼年代的年龄阈值，可以通过参数 -XX:MaxTenuringThreshold 来设置。  

## 12、代码管理？

## 13、有遇到过编程问题吗？例如遇到ArrayList不会？

## 14、为什么想从事互联网？



# 2022.06.10 格灵深瞳

## 1、手写循环缓冲队列

https://blog.csdn.net/weixin_31459297/article/details/114514922

https://blog.csdn.net/qianlia/article/details/104924435

## 2、讲一下关系型数据库

数据库分为关系型数据库（Mysql，SqlServer等）和非关系型数据库（MongoDB、Redis等）

关系型数据库采用了**关系模型**来组织数据的数据库，其以**行和列的形式存储数据**，以便于用户理解，关系型数据库这一系列的行和列被称为**表**，一组表组成了数据库，**存储的格式可以直观地反映实体间的关系**。

**特点**：

### 2.1存储方式

传统的关系型数据库采用**表格**的储存方式，数据以**行和列**的方式进行存储，要读取和查询都十分方便。

### 2.2存储结构

关系型数据库按照**结构化**的方法存储数据，先定义好表的结构，再根据表的结构存入数据，所以整个数据表的可靠性和稳定性都比较高，但存入数据后，如果需要修改数据表的结构就会十分困难。

### 2.3查询方式

关系型数据库采用**结构化查询语言（即SQL）**来对数据库进行查询。

### 2.4规范化

在数据库的设计开发过程中开发人员通常会面对同时需要对一个或者多个数据实体（包括数组、列表和嵌套数据）进行操作，这样在关系型数据库中，一个数据实体一般首先要分割成多个部分，然后再对分割的部分进行规范化，规范化以后再分别存入到多张关系型数据表中。

### 2.5事务性

关系型数据库强调**ACID规则（原子性（Atomicity）、一致性（Consistency）、隔离性（Isolation）、持久性（Durability））**，可以满足对事务性要求较高或者需要进行复杂数据查询的数据操作，而且可以充分满足数据库操作的高性能和操作稳定性的要求。并且关系型数据库十分强调数据的强一致性，对于事务的操作有很好的支持。关系型数据库可以控制事务原子性细粒度，并且一旦操作有误或者有需要，可以马上回滚事务。

### 2.6读写性能

关系型数据库十分强调数据的一致性，并为此降低读写性能付出了巨大的代价一旦面对海量数据的处理的时候效率就会变得很差，特别是遇到高并发读写的时候性能就会下降的非常厉害。

## 3、刚刚你讲到了索引，索引为什么能提高查询效率？

### ans1

索引就是通过事先排好序，从而在查找时可以应用二分查找等高效率的算法。

一般的顺序查找，复杂度为O(n)，而二分查找复杂度为O(log2n)。当n很大时，二者的效率相差及其悬殊。

**举个例子**：

表中有一百万条数据，需要在其中寻找一条特定id的数据。如果顺序查找，平均需要查找50万条数据。而用二分法，至多不超过20次就能找到。二者的效率差了2.5万倍！

在一个或者一些字段需要频繁用作查询条件，并且表数据较多的时候，创建索引会明显提高查询速度，因为可由全表扫描改成索引扫描。

（无索引时全表扫描也就是要逐条扫描全部记录，直到找完符合条件的，索引扫描可以直接定位）

### ans2

InnoDB中，表数据文件本身就是按B+Tree组织的一个索引结构，这棵树的叶节点data域保存了完整的数据记录。这个索引的key是数据表的主键，因此InnoDB表数据文件本身就是主索引。

是InnoDB主索引（同时也是数据文件）的示意图，可以看到叶节点包含了完整的数据记录。这种索引叫做聚集索引。

![img](https://img-blog.csdnimg.cn/img_convert/62d3baebbd60c13771f5b79f1ebed6cc.png)

很明显的是：**没有用索引**我们是需要**遍历双向链表**来定位对应的页，现在通过“目录”就可以很快地定位到对应的页上了！

其实底层结构就是**B+树**，B+树作为树的一种实现，能够让我们**很快地**查找出对应的记录。

————————————————
版权声明：本文为CSDN博主「洋气月」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/arispy/article/details/123371287



B+树可以说是为了磁盘而生的，因为B+树的所有数据都存在叶子节点中，包存了父节点的指针，最重要的是，B+树最高只有三层，也就是说，无论数据在哪，最多只需要三次IO扫描便可以拿到数据。大大的节省了IO磁盘扫描次数。所以索引有利于增加数据获取的速度。

## 4、B+树的结构？

![img](https://img-blog.csdnimg.cn/img_convert/39a0c3a7add07766c9945a8335b2c3a2.png)

二叉查找树会出现斜树问题，导致时间复杂度增加，因此又引入了一种**平衡二叉树（AVL树）**，它具有二叉查找树的所有特点，同时增加了一个规则：”**它的左右两个子树的高度差的绝对值不超过1**“。平衡二叉树会采用左旋、右旋的方式来实现平衡。

![img](https://img-blog.csdnimg.cn/img_convert/8ac3204c47362d8e848ead657be1466d.png)

而**B树是一种多路平衡查找树**，它满足平衡二叉树的规则，但是**它可以有多个子树**，子树的数量取决于关键字的数量，比如这个图中根节点有两个关键字3和5，那么它能够拥有的子路数量=关键字数+1。

![img](https://img-blog.csdnimg.cn/img_convert/bf15fbc1608b41c619f6417359deca33.png)

B+树，其实是在B树的基础上做的增强，最大的区别有两个：

1. B树的数据存储在每个节点上，而B+树中的数据是存储在叶子节点，并且通过链表的方式把叶子节点中的数据进行连接。
2. B+树的子路数量等于关键字数

**这个是B树的存储结构，从B树上可以看到每个节点会存储数据。**

![img](https://img-blog.csdnimg.cn/img_convert/05b2d6466c915dd5960d7eb16e29a99e.png)



**这个是B+树，B+树的所有数据是存储在叶子节点，并且叶子节点的数据是用双向链表关联的。**

![img](https://img-blog.csdnimg.cn/img_convert/2d9bf41e2cbec5d2e0f237f087ca2eb1.png)

B树和B+树，一般都是应用在文件系统和数据库系统中，用来减少磁盘IO带来的性能损耗。

以Mysql中的InnoDB为例，当我们通过`select`语句去查询一条数据时，InnoDB需要从磁盘上去读取数据，这个过程会涉及到磁盘IO以及磁盘的随机IO。

**磁盘IO的工作原理是**，首先系统会把数据逻辑地址传给磁盘，**磁盘控制电路**按照寻址逻辑把**逻辑地址**翻译成**物理地址**，也就是确定要读取的数据在哪个磁道，哪个扇区。为了读取这个扇区的数据，需要把**磁头**放在这个扇区的上面，为了实现这一个点，**磁盘会不断旋转**，把目标扇区旋转到磁头下面，使得磁头找到对应的磁道，这里涉及到寻道事件以及旋转时间。很明显，磁盘IO这个过程的性能开销是非常大的，特别是查询的数据量比较多的情况下。

**所以在InnoDB中，干脆对存储在磁盘块上的数据建立一个索引，然后把索引数据以及索引列对应的磁盘地址，以B+树的方式来存储。**

如图所示，当我们需要查询目标数据的时候，根据索引从B+树中查找目标数据即可，由于**B+树分路较多**，所以只需要**较少次数的磁盘IO**就能查找到。

**为什么用B树或者B+树来做索引结构？原因是AVL树的高度要比B树的高度要高，而高度就意味着磁盘IO的数量。所以为了减少磁盘IO的次数，文件系统或者数据库才会采用B树或者B+树。**

![image-20220612155725587](C:\Users\陈康瑞\AppData\Roaming\Typora\typora-user-images\image-20220612155725587.png)



# 2022.06.13海纳数智

## 项目里数据库表怎么设计的？

数据库这里使用的是 MySQL ，并使用主从模式实现读写分离，以提高读性能。

- 用PowerDisigner工具创建数据库

![用PowerDisigner工具创建数据库](http://cdn.jayh.club/blog/20200411/rGkitjMmv7T2.png?imageslim)

- 总共有5个微服务数据库：内容、学习、渠道、用户、题目

![5个数据库](http://cdn.jayh.club/blog/20200411/Xo71F4ku86PB.png?imageslim)

- 内容微服务的数据库

![内容微服务的数据库](http://cdn.jayh.club/blog/20200411/PkKsIdaWrcUA.png?imageslim)

学习微服务的数据库

![学习微服务的数据库](http://cdn.jayh.club/blog/20200411/j9dtS9xryyEv.png?imageslim)

渠道微服务的数据库

![渠道微服务的数据库](http://cdn.jayh.club/blog/20200411/60lbRmKajihg.png?imageslim)

用户微服务的数据库

![用户微服务的数据库](http://cdn.jayh.club/blog/20200411/qa2OQTzGyR9U.png?imageslim)

- 题目微服务的数据库

![题目微服务的数据库](http://cdn.jayh.club/blog/20200411/LTxxK6fEeL6E.png?imageslim)

以题目微服务为例，题目微服务的数据库有两张表，分别是题目表和题目类型表，题目表的字段包含了题目id，题目标题、题目解答、题目难度等级、题目类型等等；题目类型表的字段包括id、类型名称、类型logo路径等等。

## 表和表之间怎么关联？外键？id？

## 下划线命名？驼峰命名？转驼峰？

## 跨域怎么解决？

## git的基本使用

### Git是什么

Git是目前世界上最先进的分布式版本控制系统。

![img](https://img-blog.csdnimg.cn/img_convert/ff9c077e0447c6aef3179d930e2653ac.png)

Workspace：工作区
Index / Stage：暂存区
Repository：仓库区（或本地仓库）
Remote：远程仓库

### SVN与Git的最主要的区别？

　　SVN是集中式版本控制系统，版本库是集中放在中央服务器的，而干活的时候，用的都是自己的电脑，所以首先要从中央服务器哪里得到最新的版本，然后干活，干完后，需要把自己做完的活推送到中央服务器。集中式版本控制系统是必须联网才能工作，如果在局域网还可以，带宽够大，速度够快，如果在互联网下，如果网速慢的话，就纳闷了。

　　Git是分布式版本控制系统，那么它就没有中央服务器的，每个人的电脑就是一个完整的版本库，这样，工作的时候就不需要联网了，因为版本都是在自己的电脑上。既然每个人的电脑都有一个完整的版本库，那多个人如何协作呢？比如说自己在电脑上改了文件A，其他人也在电脑上改了文件A，这时，你们两之间只需把各自的修改推送给对方，就可以互相看到对方的修改了。



git init 把这个目录变成git可以管理的仓库



使用命令 git add readme.txt添加到暂存区里面去

![img](https://img-blog.csdnimg.cn/img_convert/3cae16efa68807c8be38fe064f6a6d36.png)

用命令 **git commit**告诉Git，把文件提交到仓库

![img](https://img-blog.csdnimg.cn/img_convert/a9bc03902177897529d77440f91d98c7.png)

可以通过命令**git status**来查看是否还有文件未提交

![img](https://img-blog.csdnimg.cn/img_convert/92eb31088171a00259957460c8a12e13.png)

继续来改下readme.txt内容，比如我在下面添加一行2222222222内容，继续使用**git status**来查看下结果，如下：

![img](https://img-blog.csdnimg.cn/img_convert/f4ec46652ea56c3883cdbe3ca6af44cc.png)

上面的命令告诉我们 readme.txt文件已被修改，但是未被提交的修改。

　　接下来我想看下readme.txt文件到底改了什么内容，如何查看呢？可以使用如下命令：**git diff readme.txt** 

![img](https://img-blog.csdnimg.cn/img_convert/aaa150964e82df85daf3907bf036b14b.png)

查看下历史记录，可以使用命令 **git log** 

![img](https://img-blog.csdnimg.cn/img_convert/7720dd5603a42733200666ca3824561e.png)

git log命令显示从最近到最远的显示日志，我们可以看到最近三次提交，最近的一次是,增加内容为333333.上一次是添加内容222222，第一次默认是 111111.如果嫌上面显示的信息太多的话，我们可以使用命令 git log –pretty=oneline

![img](https://img-blog.csdnimg.cn/img_convert/1101972cec3b649d0cd37ee482a8c046.png)

回退到前100个版本，**git reset --hard HEAD~100**

回退到上一个版本 **git reset --hard HEAD^**

![img](https://img-blog.csdnimg.cn/img_convert/cb5f044cdd9a2c0b11ab424f83bdb4c6.png)

再来查看下 readme.txt内容如下：通过命令**cat readme.txt**查看

![img](https://img-blog.csdnimg.cn/img_convert/e2a54ca5ed61acee8519e0875c7f68f2.png)

通过如下命令即可获取到版本号：git reflog 

![img](https://img-blog.csdnimg.cn/img_convert/8496c809fd41481afa6a4dee2625e50a.png)

增加内容3333的版本号是 6fcfc89.我们现在可以命令 git reset --hard 6fcfc89来恢复了

![img](https://img-blog.csdnimg.cn/img_convert/d5da7ad4624255703316515ef37ffc1e.png)

### 理解工作区与暂存区的区别？

​		工作区：就是你在电脑上看到的目录，比如目录下testgit里的文件(.git隐藏目录版本库除外)。或者以后需要再新建的目录文件等等都属于工作区范畴。

　　版本库(Repository)：工作区有一个隐藏目录.git,这个不属于工作区，这是版本库。其中版本库里面存了很多东西，其中最重要的就是stage(暂存区)，还有Git为我们自动创建了第一个分支master,以及指向master的一个指针HEAD。

　　我们前面说过使用Git提交文件到版本库有两步：

　　第一步：是使用 git add 把文件添加进去，实际上就是把文件添加到暂存区。

　　第二步：使用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支上。



命令 **git checkout --readme.txt** 意思就是，把readme.txt文件在工作区做的修改全部撤销

git checkout -b dev  表示创建并切换

![img](https://img-blog.csdnimg.cn/img_convert/01ed5241e849ca791fbdc87534e1d444.png)

​		查看分支：git branch

　　创建分支：git branch name

　　切换分支：git checkout name

　　创建+切换分支：git checkout –b name

　　合并某分支到当前分支：git merge name

　　删除分支：git branch –d name

## 项目有没有上线

# 2022.06.13 天讯瑞达

static关键字的使用。成员变量？内部属性？内部变量？

## ArrayList和LinkedList的区别

1、两者都不是线程安全的

2、ArrayList底层是Object数组结构，LinkedList底层是双向链表结构。

3、插⼊和删除是否受元素位置的影响：ArrayList执行add(E e)是在末尾插入一个元素，时间复杂度是O(1)；如果在指定位置插入或者删除一个元素，时间复杂度就为 O(n-i)，因为第i个元素和后面的n - i个元素要进行移位操作。

LinkedList对于add(E e)方法的插入、删除元素的时间复杂度不受元素位置影响，时间复杂度是O(1)；如果在指定位置插入或者删除一个元素，时间复杂度就为 O(n)，因为要将其移动到指定位置。

4、是否⽀持快速随机访问：LinkedList不支持，ArrayList支持。快速随机访问就是通过元素的序号快速获取元素对象(对应于 get(int index) ⽅法)。

5、内存空间占用： ArrayList 的空 间浪费主要体现在在 list 列表的结尾会预留⼀定的容量空间，⽽ LinkedList 的空间花费则体现在它的每⼀个元素都需要消耗⽐ ArrayList 更多的空间（因为要存放直接后继和直接前驱以及数据）。

## ArrayList扩容机制 ck

**add()添加第一个元素前**：数组长度为0，先调用ensureCapacityInternal(size + 1)方法，更新mincapacity = 10。

再调用ensureExplicitCapacity(minCapacity)方法，再调用grow(minCapacity)方法，更新newcapacity = 10，再调用Arrays.copyOf(elementData, newCapacity)，返回一个新的长度的数组(同名)，此时数组长度为10。

**添加第2,3，...，10个元素时**：因为需要的数组长度都不会超过10，所以不会进入扩容。

**添加第11个元素时**：所需要的数组长度（size + 1 = 11）大于现有数组长度10，进入扩容，使用移位法，扩容为原有长度的1.5倍左右（如果原长度是偶数，结果是1.5倍；如果是奇数，则不够1.5倍）

往后以此类推。

https://javaguide.cn/java/collection/arraylist-source-code.html



## LinkedList怎么查找的？（二分查找？HashMap？）

说一下HashMap？(hashcode有关的问题)

项目里有用到hashmap/hashset吗

说一下HashSet？如何检查重复？

方法重写和重载？

方法重载的返回值类型一定要和原来一样吗？

== 和equals有什么不同

运行时异常有哪些？

数据库关联？讲一下左连接和右连接查询？

用户表和题目表怎么关联查询？

用户关联菜单？

如果查询数据量大，在哪些字段添加索引比较好？

## 单例模式

https://maureen.blog.csdn.net/article/details/82454714?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-82454714-blog-125328523.t0_searchtargeting_v1&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-82454714-blog-125328523.t0_searchtargeting_v1&utm_relevant_index=2

调用数据库时常用

实战应用 https://blog.csdn.net/qq_46514118/article/details/123637748

### 饿汉式

类加载时已经创建好对象，避免了多线程同步问题，线程安全，但占用内存较大

```java
package testsingleton;

/**
 * 饿汉式，类加载时就创建对象
 * @author ck
 * @create 2022-08-14  18:09
 */
public class EhSingleton {
    private static EhSingleton ehSingleton = new EhSingleton();

    private EhSingleton (){}

    public static EhSingleton getInstance(){
        return ehSingleton;
    }
}
```

### 饿汉式-变种

```java
package testsingleton;

/**
 * @author ck
 * @create 2022-08-14  19:48
 */
public class EhSingleton2 {
    private static EhSingleton2 ehSingleton2 = null;

    static {
        ehSingleton2 = new EhSingleton2();
    }
    private EhSingleton2(){}
    public static EhSingleton2 getInstance(){
        return ehSingleton2;
    }
}
```

### 懒汉式

线程不安全

```java
package testsingleton;

/**
 * 懒汉式，类初始化不会创建实例，只有被调用时才会创建实例
 * @author ck
 * @create 2022-08-14  18:38
 */
public class LazySingleton {
    private static LazySingleton lazySingleton = null;

    private LazySingleton(){}

    public static LazySingleton getInstance() {
        if (lazySingleton == null){
            lazySingleton = new LazySingleton();
        }
        return lazySingleton;
    }
}
```

### 加锁的懒汉式

线程安全，但效率低

```java
package testsingleton;

/**
 * 加锁的懒汉式
 * @author ck
 * @create 2022-08-14  18:44
 */
public class LazySingletonSync {
    private static LazySingletonSync lazySingletonSync = null;
    private LazySingletonSync(){}

    public static synchronized LazySingletonSync getInstance(){
        if (lazySingletonSync == null){
            lazySingletonSync = new LazySingletonSync();
        }
        return lazySingletonSync;
    }

}
```

### 双重检验懒汉式

使用volatile禁止其指令重排，线程安全，效率高

```java
package testsingleton;

/**
 * 双重检验懒汉式，volatile禁止其指令重排
 * @author ck
 * @create 2022-08-14  18:54
 */
public class LazySingletonDoubleCheck {
    private static  volatile LazySingletonDoubleCheck lazySingletonDoubleCheck = null;
    private LazySingletonDoubleCheck(){}

    public static LazySingletonDoubleCheck getInstance(){
        if (lazySingletonDoubleCheck == null){
            synchronized (LazySingletonDoubleCheck.class){
                if (lazySingletonDoubleCheck == null){
                    lazySingletonDoubleCheck = new LazySingletonDoubleCheck();
                }
            }
        }
        return lazySingletonDoubleCheck;
    }
}
```

### 静态内部类

线程安全，效率高

```java
package testsingleton;

/**
 * 静态内部类
 * @author ck
 * @create 2022-08-14  19:10
 */
public class StaticClass {
    private StaticClass(){}
    private static class SingleHolder{
        private static final SingleHolder INSTANCE = new SingleHolder();
    }

    public static final SingleHolder getInstance(){
        return SingleHolder.INSTANCE;
    }
}
```

### 测试使用

```java
package testsingleton;

/**
 * @author ck
 * @create 2022-08-14  18:11
 */
public class TestSingleton {
    public static void main(String[] args) {
        //测试懒汉式
        LazySingletonDoubleCheck singletonDoubleCheck1 = LazySingletonDoubleCheck.getInstance();
        LazySingletonDoubleCheck singletonDoubleCheck2 = LazySingletonDoubleCheck.getInstance();
        System.out.println(singletonDoubleCheck2 == singletonDoubleCheck1);  //地址相同，是同一个对象
        
        //测试饿汉式
        EhSingleton singleton1 = EhSingleton.getInstance();
        EhSingleton singleton2 = EhSingleton.getInstance();
        System.out.println(singleton2 == singleton1);  //地址相同，是同一个对象
    }
}
```

### 单例模式的特点

持有自己类型的属性：类构造器私有：对外提供获取实例的静态方法。

# 2022.12.23 百望股份

## java三大特性以及应用场景

**封装**

封装把⼀个对象的属性私有化，同时提供⼀些可以被外界访问的属性的⽅法，如果属性不想被外界访问，我们⼤可不必提供⽅法给外界访问。但是如果⼀个类没有提供给外界访问的⽅法，那么这个类也没有什么意义了。

**继承**

继承是使⽤已存在的类的定义作为基础建⽴新类的技术，新类的定义可以增加新的数据或新的功能，也可以⽤⽗类的功能，但不能选择性地继承⽗类。通过使⽤继承我们能够⾮常⽅便地复⽤以前的代码。

1. ⼦类拥有⽗类对象所有的属性和⽅法（包括私有属性和私有⽅法），但是⽗类中的私有属性和⽅法⼦类是⽆法访问，只是拥有。
2. ⼦类可以拥有⾃⼰属性和⽅法，即⼦类可以对⽗类进⾏扩展。
3. ⼦类可以⽤⾃⼰的⽅式实现⽗类的⽅法。（以后介绍）。

**多态**

所谓多态就是指程序中定义的引⽤变量所指向的具体类型和通过该引⽤变量发出的⽅法调⽤在编程时并不确定，⽽是在程序运⾏期间才确定，即⼀个引⽤变量到底会指向哪个类的实例对象，该引⽤变量发出的⽅法调⽤到底是哪个类中实现的⽅法，必须在由程序运⾏期间才能决定。

在 Java 中有两种形式可以实现多态：继承（多个⼦类对同⼀⽅法的重写）和接⼝（实现接⼝并覆盖接⼝中同⼀⽅法）。

实现多态的条件：继承、重写、向上转型

假设A是父类，a是子类，a重写了一部分A的方法，重载了一部分A的方法，当A b = new a()时，即**向上转型**时：

如果调用的是子类**重写**的父类的方法，则执行的是**重写后的内容**；

如果调用的是子类**重载**的父类的方法，则执行的是**父类的方法内容**。

## 接口和抽象类的区别

（方法修饰符、变量修饰符、多个接口实现、有无构造方法、面向属性/行为）

https://blog.csdn.net/github_37130188/article/details/89931916

1. 接⼝的⽅法默认是 public ，所有⽅法在接⼝中不能有实现(Java 8 开始接⼝⽅法可以有默认实现），⽽抽象类可以有⾮抽象的⽅法。
2. 接⼝中除了 static 、 final 变量，不能有其他变量，⽽抽象类中则不⼀定。
3. ⼀个类可以实现多个接⼝，但只能实现⼀个抽象类。接⼝⾃⼰本身可以通过 extends 关键字扩展多个接⼝。
4. 接⼝⽅法默认修饰符是 public ，抽象⽅法可以有 public 、 protected 和 default 这些修饰符（抽象⽅法就是为了被重写所以不能使⽤ private 关键字修饰！）。
5. 从设计层⾯来说，抽象是对类的抽象，是⼀种模板设计，⽽接⼝是对⾏为的抽象，是⼀种⾏为的规范。

**从定义上看，接口是个集合，并不是类。类描述了属性和方法，而接口只包含方法（未实现的方法）。**

**接口要求只能包含抽象方法，抽象方法是指没有实现的方法。接口就根本不能存在方法的实现。**

**抽象类中可以有已经实现了的方法，也可以有被abstract修饰的方法（抽象方法），因为存在抽象方法，所以该类必须是抽象类。**

**抽象类是类，具有类的所有属性（但是不能实例化），当然拥有构造方法，而接口没有。**

**面试官补充：接口面向行为，抽象类面向属性**

## TCP三次握手、四次挥手



## TCP、UDP区别

**TCP 提供⾯向连接的服务**。在传送数据之前必须先建⽴连接，数据传送结束后要释放连接。
TCP 不提供⼴播或多播服务。由于 TCP 要提供可靠的，⾯向连接的传输服务（TCP的可靠体现在TCP在传递数据之前，会有三次握⼿来建⽴连接，⽽且在数据传递时，有确认、窗⼝、重传、拥塞控制机制，在数据传完后，还会断开连接⽤来节约系统资源），这⼀难以避免增加了许多开销，如确认，流量控制，计时器以及连接管理等。这不仅使协议数据单元的⾸部增⼤很多，还要占⽤许多处理机资源。TCP ⼀般⽤于**⽂件传输、发送和接收邮件、远程登录**等场景。



**UDP 在传送数据之前不需要先建⽴连接**，远地主机在收到 UDP 报⽂后，不需要给出任何确认。虽然 UDP **不提供可靠交付**，但在某些情况下 UDP 确是⼀种最有效的⼯作⽅式（⼀般⽤于即时通信），⽐如： QQ 语⾳、 QQ 视频 、直播**等等**

## 线程安全的集合类有哪些

https://blog.csdn.net/lixiaobuaa/article/details/79689338?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~Rate-1-79689338-blog-124155722.pc_relevant_aa2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~Rate-1-79689338-blog-124155722.pc_relevant_aa2&utm_relevant_index=1

1.Vector：就比Arraylist多了个同步化机制（线程安全）。
2.Hashtable：就比Hashmap多了个线程安全。
3.ConcurrentHashMap:是一种高效但是线程安全的集合。
4.Stack：栈，也是线程安全的，继承于Vector。

## 项目里怎么用redis？redis挂了怎么办

**redis应用**

1、保存用户信息，这个需要频繁的获取。比如在打开某一个页面进行查询时，就先需要获取用户信息，看用户是否具有查询权限；

2、当数据库查询比较慢时，也会使用到redis缓存，第一次查询可能会比较慢，就将结果缓存在redis中，当第二次进行访问时就快多了；

3、使用字典表进行翻译某些字段的值，将字典表进行放在redis中进行存储；

4、当查询用户的权限时，将所有用户对应的权限信息放在redis中进行存储。

**redis挂了**

https://blog.csdn.net/sun_lm/article/details/123467103

系统中只有一台redis服务器是不可靠的，容易出现单点故障。为了避免单点故障，可以使用多台redis服务器组成**redis集群**

**1、主从模式**  

集群中有多台redis节点，就必须保证每个节点中的数据是一致的。redis中，为了保持数据一致性，数据总是从master复制到slave，这就是redis的**主从复制**。

![img](E:\2021java\Typora\typora-user-images\watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAc3VuX2xt,size_12,color_FFFFFF,t_70,g_se,x_16.png)

**主从复制的作用**：

​	数据冗余：实现了数据的热备份，是持久化之外的另一种数据冗余方式
​	故障恢复：master故障时，slave可以提供服务，实现故障快速恢复
​	负载均衡：master负责写，slave负责读。在写少读多的场景下可以极大提高redis吞吐量
​	高可用基石：主从复制是redis哨兵模式和集群模式的基础。

**主从复制实现原理**：

主从复制过程主要可以分为3个阶段：连接建立阶段、数据同步阶段、命令传播阶段。

​	连接建立阶段：在主从节点之间建立连接，为数据同步做准备。
​	数据同步阶段：执行数据的全量（或增量）复制（复制RDB文件）
​	命令传播阶段：主节点将已执行的命令发送给从节点，从节点接收命令并执行，从而实现主从节点的数据一致性
主从模式中，一个主节点可以有多个从节点。为了减少主从复制对主节点的性能影响，一个从节点可以作为另外一个从节点的主节点进行主从复制。

**不足之处**：**主节点宕机之后，需要手动拉起从节点来提供业务，不能达到高可用**。



**2、哨兵模式**  

Redis Sentinel是Redis的高可用实现方案，它可以实现对redis的监控、通知和自动故障转移，**当redis master挂掉之后，可以自动拉起slave提供业务，从而实现redis的高可用**。为了避免Sentinel本身出现单点故障，Sentinel自己也可采用集群模式。

Sentinel是一种特殊的redis节点，每个sentinel节点会维护与其他redis节点（包括master/slave/sentinel）的心跳。

当一个sentinel节点与master节点的心跳丢失时，这个sentinel节点就会认为master节点出现了故障，处于不可用的状态，这种判定叫作主观下线（即sentinel节点自己主观认为master下线了）

之后，这个sentinel节点会与其他sentinel节点交换信息，如果发现认为主节点发生故障的sentinel节点的个数超过了某个阈值（通常为sentinel节点总数的1/2+1，即超过半数），则sentinel会认为master节点已经处于客观下线的状态，即大家都认为master故障不可用了。

之后，sentinel节点中会选举处一个sentinel leader来执行redis主节点的故障转移。

![img](E:\2021java\Typora\typora-user-images\watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAc3VuX2xt,size_20,color_FFFFFF,t_70,g_se,x_16.png)



**3、集群模式**

主从模式实现了数据的热备份，哨兵模式实现了redis的高可用。但是有一个问题，这两种模式都没有解决，**这两种模式都只能有一个master节点负责写操作，在高并发的写操作场景，master节点就会成为性能瓶颈**。

redis的集群模式中可以实现**多个节点同时提供写操作**，redis集群模式采用无中心结构，每个节点都保存数据，节点之间互相连接从而知道整个集群状态。

如图所示集群模式其实就是多个主从复制的结构组合起来的，每一个主从复制结构可以看成一个节点，那么上面的Cluster集群中就有三个节点。

<img src="E:\2021java\Typora\typora-user-images\watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAc3VuX2xt,size_20,color_FFFFFF,t_70,g_se,x_16-16718785978443.png" alt="img" style="zoom: 67%;" />



## mybatis ***flow？

## 输入URL到页面的全过程

**1、DNS解析**：url解析成ip地址和对应的端口号

​	网络进程会先从DNS数据缓存服务器查找是否缓存过当前域名信息，有则直接返回，否则，会进行DNS解析成对应的ip和端口号。

**2、建立TCP连接**：通过三次握手与服务器建立连接，然后进行数据传输

​	第一次握手，发送SYN报文。
​	客户端主动向服务器发送请求建立连接的报文SYN，客户端会随机初始化一个序列号x，然后把SYN的标志置为1，之后客户端处于SYN-	SENT的状态。
​	SYN=1 seq=x

​	第二次握手，发送SYN + ACK报文。
​	服务端接收到客户端的报文后，服务端也会随机初始化一个序列号y，对请求的应答做响应，即ack=x+1，接着把SYN和ACK的标志置为	1，之后服务端处于SYN-RCVD状态。
​	SYN=1 ACK=1 seq=y ack=x+1

​	第三次握手，发送ACK报文。
​	客户端接受到服务端的报文后，对应答号进行回应ack=y+1，把ACK的标志置为1，序列号为x+1，返回确认的报文给服务端，服务端接	到确认报文后进入连接的状态，此时TCP连接成功。
​	ACK=1 ack=y+1 seq=x+1

**3、客户端发送HTTP请求**：把输入的地址封装成HTTP Request请求行，发送给服务器

​	请求行 = 请求方法 + 请求url + HTTP版本 + 请求头。

**4、服务器处理请求并返回 HTTP 报文**

​	服务器收到请求后，响应报文，返回资源。

​	HTTP响应报文也是由三部分组成: 状态码, 响应报头和响应报文。

**5、浏览器渲染页面**

​	1、html被html解析器解析成DOM Tree，css被css解析器解析成CSSOM Tree（并行解析）；
​	2、DOM Tree 和 CSSOM Tree 合并到一起，形成 Render Tree ；
​	3、重排，根据渲染树计算出每个节点的几何信息，位置及大小；
​	4、重绘，绘制渲染出最终的页面。

**6、断开TCP连接**：通过四次挥手断开连接

​	第一次挥手，发送FIN报文。
​	客户端发送FIN=1，序列号seq=x的报文，进入FIN_WAIT_1的状态。

​	第二次挥手，服务端发送ACK报文。
​	服务端接受到客户端报文后，会给客户端发送一个ACK的报文，表示收到了。服务端进入CLODED_WAIT状态，客户端进入FIN_WAIT_2	状态。

​	第三次挥手，服务端发送FIN报文。
​	服务端处理完数据，也会向客户端发送一个FIN报文，服务端进入LAST_ACK状态。

​	第四次挥手，客户端发送ACK报文。
​	客户端收到服务端FIN报文后，会向服务端发送一个ACK的报文，表示收到了。然后客户端进入TIME_WAIT状态，服务端收到ACK报文	后进入CLOSED状态。至此连接断开。



# 2023.02.01 首汽约车

## 介绍项目

## 项目-如何更改队长

1. 先创建队伍，再添加成员，再从成员里选出队长
2. 若要更换队长，则先撤销队长，再重新选定队长
3. 可以创建多个类型的服务队，设置多个成员

## 项目-服务之间有调用吗

## springboot和springmvc区别

## springboot启动过程

## 讲一下nacos原理

## A、B服务调用

https://www.bilibili.com/video/BV1ca411f7We/?p=3&vd_source=c9d3254824fec51fbdb7ce2b87a48cb4

以passjava项目为例，content服务调用question服务的接口

1. 两个服务都要注册到nacos

2. content里创建一个接口

   ```java
   package com.jackson0714.passjava.content.feign;
   
   import org.springframework.cloud.openfeign.FeignClient;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.PathVariable;
   
   /**
    * @author ck
    * @create 2023-02-01  22:47
    */
   @FeignClient(value = "passjava-question")
   public interface QuestionFeignService {
       @GetMapping("/question/v1/admin/question/getStr/{id}")
       public String getStr(@PathVariable("id") Integer id);
   }
   ```

   

3. 在content启动类添加注解@EnableFeignClients

4. 在content的controller调用接口

   ```java
    	@Autowired
       QuestionFeignService questionFeignService;
   
       @GetMapping("/getStr/{id}")
       public String testFeign(@PathVariable("id") Integer id) {
           log.info("content ok");
           return questionFeignService.getStr(id);
       }
   ```

   

5. 

## B服务下线对A服务有无影响

## 常见的线程类型

## 线程的核心参数

## 如果最大线程数满了，等待队列也满了怎么办

执行拒绝策略

## 讲一下对索引的理解

## A-B联合索引

1. 只查询A，索引是否生效
2. 只查询B。索引是否生效

## B树和B+树的区别

## 有无使用Redis

讲了一下作用

## java常见集合

讲的不够深入，应该深入源码

1. 概念

   Java 集合， 也叫作容器，主要是由两大接口派生而来：一个是 Collection接口，主要用于存放单一元素；另一个是 Map 接口，主要用于存放键值对。对于Collection 接口，下面又有三个主要的子接口：List、Set 和 Queue。

2.  List, Set, Queue, Map 四者的区别

3. 集合框架底层数据结构



## set和ArrayList区别

讲一下set去重的原理

## 算法题-链表相交



# 2023.02.06 阿里云一面

## 算法题-LRU

## 操作系统-死锁

## 乐观锁

## 进程、线程区别

## 讲一下集合/容器

## mysql四大特性

## 讲一下索引

## git原理





# 2023.02.07 Momenta

## redis线程

## 跳表

## 操作系统-分页

## 常见数据结构

## JVM内存模型

## mysql事务隔离级别

## git为什么出现冲突



# 2323.02.08 阿里云二面

## 介绍情况

## 数据包从接收到进入底层的原理



## 从编程的角度解释网络通信

## 了解容器吗（云）

## 云原生

## concurrentHashMap

## 项目-索引优化



# 索引 20220915

InnoDB 引擎中，其数据文件本身就是索引文件。相比 MyISAM的索引文件和数据文件是分离的，InnoDB的表数据文件本身就是按 B+Tree 组织的一个索引结构，树的叶节点 data 域保存了完整的数据记录。这个索引的 key 是数据表的主键，因此 InnoDB 表数据文件本身就是主索引。这被称为“**聚簇索引**（或聚集索引）”，而其余的索引都作为**辅助索引**，辅助索引的 data 域存储相应记录主键的值而不是地址。**在根据主索引搜索时，直接找到 key 所在的节点即可取出数据；在根据辅助索引查找时，则需要先取出主键的值，再走一遍主索引。**



MyISAM 引擎中，B+Tree 叶节点的 data 域存放的是数据记录的地址。在索引检索的时候，首先按照 B+Tree 搜索算法搜索索引，如果指定的 Key 存在，则取出其 data 域的值，然后以 data 域的值为地址读取相应的数据记录。这被称为“**非聚簇索引**”。

![img](E:\2021java\Typora\typora-user-images\20210420165326946.png)

## 主键索引

数据表的主键列使用的就是主键索引。

一张数据表有只能有一个主键，并且主键不能为 null，不能重复。

在 MySQL 的 InnoDB 的表中，当没有显示的指定表的主键时，InnoDB 会自动先检查表中是否有唯一索引且不允许存在null值的字段，如果有，则选择该字段为默认的主键，否则 InnoDB 将会自动创建一个 6Byte 的自增主键。

![img](E:\2021java\Typora\typora-user-images\cluster-index.png)

## 二级索引（辅助索引）

二级索引又称为辅助索引，是因为二级索引的叶子节点存储的数据是主键。也就是说，通过二级索引，可以定位主键的位置。

唯一索引，普通索引，前缀索引等索引属于二级索引。

![image-20220915162649035](E:\2021java\Typora\typora-user-images\image-20220915162649035.png)

### 唯一索引(Unique Key)

唯一索引也是一种约束。唯一索引的属性列不能出现重复的数据，但是允许数据为 NULL，一张表允许创建多个唯一索引。 建立唯一索引的目的大部分时候都是为了该属性列的数据的唯一性，而不是为了查询效率。

### 普通索引(Index)

普通索引的唯一作用就是为了快速查询数据，一张表允许创建多个普通索引，并允许数据重复和 NULL。

### 前缀索引(Prefix)

前缀索引只适用于字符串类型的数据。前缀索引是对文本的前几个字符创建索引，相比普通索引建立的数据更小， 因为只取前几个字符。

### 全文索引(Full Text)

全文索引主要是为了检索大文本数据中的关键字的信息，是目前搜索引擎数据库使用的一种技术。Mysql5.6 之前只有 MYISAM 引擎支持全文索引，5.6 之后 InnoDB 也支持了全文索引。

## 聚集索引

**聚集索引即索引结构和数据一起存放的索引。主键索引属于聚集索引。**

在 MySQL 中，InnoDB 引擎的表的 .ibd文件就包含了该表的索引和数据，对于 InnoDB 引擎表来说，该表的索引(B+树)的每个非叶子节点存储索引，叶子节点存储索引和索引对应的数据。

**聚集索引的优点**：聚集索引的查询速度非常的快，因为整个 B+树本身就是一颗多叉平衡树，叶子节点也都是有序的，定位到索引的节点，就相当于定位到了数据。

**聚集索引的缺点**：

1.依赖于有序的数据 ：因为 B+树是多路平衡树，如果索引的数据不是有序的，那么就需要在插入时排序，如果数据是整型还好，否则类似于字符串或 UUID 这种又长又难比较的数据，插入或查找的速度肯定比较慢。
2.更新代价大 ： 如果对索引列的数据被修改时，那么对应的索引也将会被修改，而且聚集索引的叶子节点还存放着数据，修改代价肯定是较大的，所以对于主键索引来说，主键一般都是不可被修改的。

## 非聚集索引

**非聚集索引即索引结构和数据分开存放的索引。**

**二级索引属于非聚集索引。**

非聚集索引的叶子节点并不一定存放数据的指针，因为二级索引的叶子节点就存放的是主键，根据主键再回表查数据。

**非聚集索引的优点**：更新代价比聚集索引要小 。非聚集索引的更新代价就没有聚集索引那么大了，非聚集索引的叶子节点是不存放数据的

**非聚集索引的缺点**：

1.跟聚集索引一样，非聚集索引也依赖于有序的数据
2.可能会二次查询(回表) :这应该是非聚集索引最大的缺点了。 当查到索引对应的指针或主键后，可能还需要根据指针或主键再到数据文件或表中查询。

## 覆盖索引

如果一个索引包含（或者说覆盖）所有需要查询的字段的值，我们就称之为“覆盖索引”。我们知道在 InnoDB 存储引擎中，如果不是主键索引，叶子节点存储的是主键+列值。最终还是要“回表”，也就是要通过主键再查找一次。这样就会比较慢。覆盖索引就是把要查询出的列和索引是对应的，不做回表操作！

**覆盖索引即需要查询的字段正好是索引的字段，那么直接根据该索引，就可以查到数据了，而无需回表查询。**

**例子**：

**如主键索引，如果一条 SQL 需要查询主键，那么正好根据主键索引就可以查到主键。**

**再如普通索引，如果一条 SQL 需要查询 name，name 字段正好有索引， 那么直接根据这个索引就可以查到数据，也无需回表。**

![img](E:\2021java\Typora\typora-user-images\20210420165341868.png)

## 联合索引

使用表中的多个字段创建索引，就是 联合索引，也叫 组合索引 或 复合索引。

## 最左前缀匹配原则

最左前缀匹配原则指的是，在使用联合索引时，MySQL 会根据联合索引中的字段顺序，从左到右依次到查询条件中去匹配，如果查询条件中存在与联合索引中最左侧字段相匹配的字段，则就会使用该字段过滤一批数据，直至联合索引中全部字段匹配完成，或者在执行过程中遇到范围查询，如 >、<、between 和 以%开头的like查询 等条件，才会停止匹配。

所以，我们在使用联合索引时，可以将区分度高的字段放在最左边，这也可以过滤更多数据。

## 索引下推

索引下推是 MySQL 5.6 版本中提供的一项索引优化功能，可以在非聚簇索引遍历过程中，对索引中包含的字段先做判断，过滤掉不符合条件的记录，减少回表次数。

## 创建索引的注意事项

### 选择合适的字段创建索引

1.**不为 NULL 的字段** ：索引字段的数据应该尽量不为 NULL，因为对于数据为 NULL 的字段，数据库较难优化。如果字段频繁被查询，但又避免不了为 NULL，建议使用 0,1,true,false 这样语义较为清晰的短值或短字符作为替代。
2.**被频繁查询的字段** ：我们创建索引的字段应该是查询操作非常频繁的字段。
3.**被作为条件查询的字段** ：被作为 WHERE 条件查询的字段，应该被考虑建立索引。
4.**频繁需要排序的字段** ：索引已经排序，这样查询可以利用索引的排序，加快排序查询时间。
5.**被经常频繁用于连接的字段** ：经常用于连接的字段可能是一些外键列，对于外键列并不一定要建立外键，只是说该列涉及到表与表的关系。对于频繁被连接查询的字段，可以考虑建立索引，提高多表连接查询的效率。

### 被频繁更新的字段应该慎重建立索引

虽然索引能带来查询上的效率，但是维护索引的成本也是不小的。 如果一个字段不被经常查询，反而被经常修改，那么就更不应该在这种字段上建立索引了。

### 尽可能的考虑建立联合索引而不是单列索引

因为索引是需要占用磁盘空间的，可以简单理解为每个索引都对应着一颗 B+树。如果一个表的字段过多，索引过多，那么当这个表的数据达到一个体量后，索引占用的空间也是很多的，且修改索引时，耗费的时间也是较多的。如果是联合索引，多个字段在一个索引上，那么将会节约很大磁盘空间，且修改数据的操作效率也会提升。

### 注意避免冗余索引 

冗余索引指的是索引的功能相同，能够命中索引(a, b)就肯定能命中索引(a) ，那么索引(a)就是冗余索引。如（name,city ）和（name ）这两个索引就是冗余索引，能够命中前者的查询肯定是能够命中后者的 在大多数情况下，都应该尽量扩展已有的索引而不是创建新索引。

### 考虑在字符串类型的字段上使用前缀索引代替普通索引

前缀索引仅限于字符串类型，较普通索引会占用更小的空间，所以可以考虑使用前缀索引带替普通索引。

# 柠檬微趣（准备）

https://blog.csdn.net/Mengbabe_2018/article/details/114461129

## 笔试

### 290easy 单词规律

https://leetcode.cn/problems/word-pattern/

```java
class Solution {
    public boolean wordPattern(String pattern, String s) {
        String[] strs = s.split(" ");
        char[] charP = pattern.toCharArray();
        if(strs.length != charP.length) return false;
        Map<Character, String> map = new HashMap<>();
        for(int i = 0; i < charP.length; i++) {
            if(map.containsKey(charP[i])) {
                String str = map.get(charP[i]);
                if(!str.equals(strs[i])) return false;
            } 
            else if (map.containsValue(strs[i])) return false;
            else map.put(charP[i], strs[i]);
        }
        return true;
    }
}
```

### 556mid 下一个更大元素 III 

【单调栈解决】

https://leetcode.cn/problems/next-greater-element-iii/

```
利用单调栈解决；

如: 1236541;
从末尾遍历到3 (i=2)时，栈内不再递增，栈为[1,4,5,6[; 右边是栈顶
开始出栈，依次出栈6,5,4. 此时count=3;
交换3(i=2)和4(i=2+3=5)，得到1246531；
从3(i=2)开始 reverse剩余数字；124-6531 => 124-1356;

返回1241356；
```



```java
class Solution {
    public int nextGreaterElement(int n) {
        String str = String.valueOf(n);
        char[] ch = str.toCharArray();
        int len = ch.length;
        int count = 0;
        Deque<Integer> stack = new LinkedList<>();
        stack.push(len - 1);
        for(int i = len - 2; i >= 0; i--) {
            if(ch[i] >= ch[stack.peek()]) stack.push(i); //要用>=
            else{
                while(!stack.isEmpty() && ch[i] < ch[stack.peek()]) {
                    stack.pop();
                    count++;
                }
                char temp = ch[i + count];
                ch[i + count] = ch[i];
                ch[i] = temp;
                int left = i + 1, right = len - 1;
                while(left < right) {
                    char temp1 = ch[right];
                    ch[right] = ch[left];
                    ch[left] = temp1;
                    left++;
                    right--;
                }
                break;
            }        
        }
        long res = Long.parseLong(String.valueOf(ch));
        if(res > Integer.MAX_VALUE) return -1;
        if(res > n) return (int)res;
        return -1;
    }
}
```

### 24mid 两两交换链表中的节点

https://leetcode.cn/problems/swap-nodes-in-pairs/

【虚拟头结点】

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode swapPairs(ListNode head) {
        if(head == null || head.next == null) return head;
        ListNode dummyHead = new ListNode(-1, head);
        ListNode cur = dummyHead;
        while(cur.next != null && cur.next.next != null) {
            ListNode temp1 = cur.next;
            ListNode temp3 = cur.next.next.next;
            cur.next = temp1.next;
            cur.next.next = temp1;
            temp1.next = temp3;
            cur = temp1;
        }
        return dummyHead.next;
    }
}
```

### 93mid 复原IP地址 

## 面试

### 堆和栈的区别

**堆：存储对象，可在运行时动态分配存储空间，但存取速度较慢**

（1）Java的堆是一个运行时数据区，类的对象从堆中分配空间。这些对象通过new等指令建立，通过垃圾回收器来销毁。

（2）堆的优势是可以动态地分配内存空间，需要多少内存空间不必事先告诉编译器，因为它是在运行时动态分配的。但缺点是，由于需要在运行时动态分配内存，所以存取速度较慢。

**栈：存储基本数据类型和对象的引用，存储空间在编译时确定**

（1）栈中主要存放一些基本数据类型的变量（byte，short，int，long，float，double，boolean，char）和对象的引用。

（2）栈的优势是，存取速度比堆快，栈数据可以共享。但缺点是，存放在栈中的数据占用多少内存空间需要在编译时确定下来，缺乏灵活性。

### 内存区域

程序私有的：程序计数器、虚拟机栈、本地方法栈

程序共享的：堆、方法区、直接内存（非运行时数据区的一部分）

### 讲一讲HashMap

1、HashMap非线程安全，效率比hashtable高

2、可以将null作为key和value，但作为key只能有一个，作为value可以有多个

3、默认初始量为16，之后每次扩容变成原来的2倍。如果指定容量初始值，之后会将其扩充为 2 的幂次方

4、底层结构：

在JDK1.7 中，由“数组+链表”组成，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的。

在JDK1.8 中，由“数组+链表+红黑树”组成。当链表过长，则会严重影响 HashMap 的性能，红黑树搜索时间复杂度是 O(logn)，而链表是糟糕的 O(n)。因此，JDK1.8 对数据结构做了进一步的优化，引入了红黑树，链表和红黑树在达到一定条件会进行转换

5、解决hash冲突：

当链表超过 8 且数据总量超过 64 才会转红黑树。

将链表转换成红黑树前会判断，如果当前数组的长度小于64，那么会选择先进行数组扩容，而不是转换为红黑树，以减少搜索时间。

6、为什么在解决 hash 冲突的时候，不直接用红黑树？而选择先用链表，再转红黑树：

因为红黑树需要进行左旋，右旋，变色这些操作来保持平衡，而单链表不需要。当元素小于 8 个的时候，此时做查询操作，链表结构已经能保证查询性能。当元素大于 8 个的时候， 红黑树搜索时间复杂度是 O(logn)，而链表是 O(n)，此时需要红黑树来加快查询速度，但是新增节点的效率变慢了。

7、不用二叉查找树的原因：二叉查找树在特殊情况下会变成一条线性结构（这就跟原来使用链表结构一样了，造成很深的问题），遍历查找会非常慢。

### 多态的特点，Java怎么实现多态

所谓多态就是指程序中定义的引⽤变量所指向的具体类型和通过该引⽤变量发出的⽅法调⽤在编
程时并不确定，⽽是在程序运⾏期间才确定，即⼀个引⽤变量到底会指向哪个类的实例对象，该
引⽤变量发出的⽅法调⽤到底是哪个类中实现的⽅法，必须在由程序运⾏期间才能决定。

在 Java 中有两种形式可以实现多态：继承（多个⼦类对同⼀⽅法的重写）和接⼝（实现接⼝并
覆盖接⼝中同⼀⽅法）。

向上转型、向下转型、instanceof

### 红黑树，时间复杂度，为什么要有红黑树这种数据结构

B树和B+树介绍

https://blog.csdn.net/u014453898/article/details/112469113?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522167085879316800180629654%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=167085879316800180629654&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-112469113-null-null.142



https://blog.csdn.net/jiang_wang01/article/details/113739230?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-113739230-blog-112469113.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-113739230-blog-112469113.pc_relevant_default&utm_relevant_index=1



AVL树（平衡二叉树）的查找、插入和删除在平均和最坏情况下都是O(logn)

**红黑树与AVL树的比较**：
1.AVL树的时间复杂度虽然优于红黑树，但是对于现在的计算机，cpu太快，可以忽略性能差异
2.红黑树的插入删除比AVL树更便于控制操作
3.红黑树整体性能略优于AVL树（红黑树旋转情况少于AVL树）



https://blog.csdn.net/qq15035899256/article/details/126678970

红黑树和AVL树都是高效的平衡二叉树，增删改查的时间复杂度都是O(logN)，红黑树不追求绝对平衡，其只需保证最长路径不超过最短路径的2倍，相对而言，**降低了插入和旋转的次数**，所以在经常进行**增删**的结构中性能比AVL树更优，而且**红黑树实现比较简单，所以实际运用中红黑树更多**。

**红黑树的应用**：

java集合框架中的：TreeMap、TreeSet底层使用的就是红黑树
C++ STL库 – map/set、mutil_map/mutil_set
linux内核：进程调度中使用红黑树管理进程控制块，epoll在内核中实现时使用红黑树管理事件块
其他一些库：比如nginx中用红黑树管理timer等



**红黑树、TreeMap的应用**

https://lizhengi.blog.csdn.net/article/details/127374253?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EYuanLiJiHua%7EPosition-2-127374253-blog-122653001.pc_relevant_recovery_v2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EYuanLiJiHua%7EPosition-2-127374253-blog-122653001.pc_relevant_recovery_v2&utm_relevant_index=5



### 二分和红黑树在更新元素上的区别

https://blog.csdn.net/vincent_wen0766/article/details/113121074

红黑树的优势在于它是一个平衡二叉查找树，对于普通的二叉查找树（非平衡二叉查找树）在极端情况下可能会退化为链表的结构，例如，当我们依次插入 3、4、5、6、7、8 这些数据时，二叉树会退化为如下链表结构

<img src="https://img-blog.csdnimg.cn/20210125164202147.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZpbmNlbnRfd2VuMDc2Ng==,size_16,color_FFFFFF,t_70" alt="img" style="zoom:50%;" />

当二叉查找树退化为链表数据结构后，再进行元素的添加、删除以及查询时，它的时间复杂度就会退化为 O(n)；而如果使用红黑树的话，它就会将以上数据转化为平衡二叉查找树，这样就可以更加高效的添加、删除以及查询数据了，这就是红黑树的优势。

红黑树的高度近似 log2n，它的添加、删除以及查询数据的时间复杂度为 O(logn)



### 怎么用两个栈实现一个队列，口撕

232easy 用栈实现队列 

https://leetcode.cn/problems/implement-queue-using-stacks/



### 算法题：最长无重复字符序列

3mid 无重复字符的最长子串 【每次判断是否出现重复字符，如果出现，判断是否需要更新左边界start】

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if(s.length() == 0) return 0;
        Map<Character, Integer> map = new HashMap<>();
        int res = 0;
        int start = 0;
        char[] ch = s.toCharArray();
        for(int i = 0; i < ch.length; i++) {
            if(map.containsKey(ch[i])) {
                int index = map.get(ch[i]);
                if(index >= start) start = index + 1; //如果重复字符出现在字符串中间
                // start = start; //如果重复字符在字符串外，start不用改变
            }
            map.put(ch[i], i);
            res = Math.max(res, i - start + 1); //每次遍历都更新最大长度
        }
        return res;
    }
}
```

# 百望股份（准备）

## 面试

### J2EE架构

J2EE （Java 2 Platform, Enterprise Edition）即Java2平台企业版，它提供了基于组件的方式来设计、开发、组装和部署企业应用。J2EE使用多层分布式的应用模型，这个多层通常通过三层或四层来实现：
①客户层，运行在客户计算机上的组件。
② Web 层，运行在J2EE服务器上的组件。
③业务层，同样是运行在J2EE服务器上的组件。
④企业信息系统层（EIS），是指运行在EIS服务器上的软件系统。
以上层次一般也指三层应用，因分布在三个不同位置：客户计算机、J2EE服务器及后台的数据库或过去遗留下来的系统

### SpringMVC

MVC 是⼀种设计模式,Spring MVC 是⼀款很优秀的 MVC 框架。Spring MVC 可以帮助我们进⾏更简洁的Web层的开发，并且它天⽣与 Spring 框架集成。Spring MVC 下我们⼀般把后端项⽬分为 Service层（处理业务）、Dao层（数据库操作）、Entity层（实体类）、Controller层(控制层，返回数据给前台⻚⾯)。

SpringMVC的简单原理图如下：

<img src="E:\2021java\Typora\typora-user-images\image-20221222231458603.png" alt="image-20221222231458603" style="zoom: 80%;" />

流程：

1. 客户端（浏览器）发送请求， `DispatcherServlet`拦截请求。
2. `DispatcherServlet` 根据请求信息调用 `HandlerMapping` 。`HandlerMapping` 根据 uri 去匹配查找能处理的 `Handler`（也就是我们平常说的 `Controller` 控制器） ，并会将请求涉及到的拦截器和 `Handler` 一起封装。
3. `DispatcherServlet` 调用 `HandlerAdapter`适配执行 `Handler` 。
4. `Handler` 完成对用户请求的处理后，会返回一个 `ModelAndView` 对象给`DispatcherServlet`，`ModelAndView` 顾名思义，包含了数据模型以及相应的视图的信息。`Model` 是返回的数据对象，`View` 是个逻辑上的 `View`。
5. `ViewResolver` 会根据逻辑 `View` 查找实际的 `View`。
6. `DispaterServlet` 把返回的 `Model` 传给 `View`（视图渲染）。
7. 把 `View` 返回给请求者（浏览器）





### 怎么读取大文件

思路：将大文件分成多个小文件读取，采用多线程的方式

1.计算出文件总大小

2.分段处理,计算出每个线程读取文件的开始与结束位置

(文件大小/线程数)*N,N是指第几个线程,这样能得到每个线程在读该文件的大概起始位置

使用"大概起始位置",作为读文件的开始偏移量(fileChannel.position("大概起始位置")),来读取该文件,直到读到第一个换行符,记录下这个换行符的位置,作为该线程的准确起 始位置.同时它也是上一个线程的结束位置.最后一个线程的结束位置也直接设置为-1

3.启动线程,每个线程从开始位置读取到结束位置为止

### 怎么确定多线程的线程数

其实对于**IO密集型**类型的应用，网上还有一个公式：**线程数 = CPU核心数/(1-阻塞系数)**

引入了**阻塞系数的概念，一般为0.8~0.9之间**。

在我们的业务开发中，基本上都是IO密集型，因为往往都会去操作数据库，访问redis，es等存储型组件，涉及到磁盘IO，网络IO。

那什么场景下是CPU密集型呢？纯计算类 

IO密集型，可以考虑多设置一些线程，主要目的是可以增加IO的并发度，CPU密集型不宜设置过多线程，因为是会造成线程切换，反而损耗性能。 

如果套用 **线程数 = CPU核心数/(1-阻塞系数) 阻塞系数取 0.8 ，线程数为 20 。阻塞系数取 0.9，大概线程数40**，20个线程数我觉得可以。 

那我们怎么判断需要增加更多线程呢？ 其实可以用jstack命令查看一下进程的线程栈，如果发现线程池中大部分线程都处于等待获取任务，则说明线程够用。如果大部分线程都处于运行状态，可以继续适当调高线程数量。 

如果我们发现数据库的操作耗时比较多，此时可以继续提高阻塞系数，从而增大线程数量。 

### kafka的应用

**Kafka是一种分布式、高吞吐量的分布式发布订阅消息系统**，它可以处理消费者规模的网站中的所有动作流数据，主要应用于大数据实时处理领域。简单地说，Kafka就相比是一个邮箱，生产者是发送邮件的人，消费者是接收邮件的人，Kafka就是用来存东西的，只不过它提供了一些处理邮件的机制



**Kafka 是⼀个分布式流式处理平台**。这到底是什么意思呢？
流平台具有三个关键功能：

1. 消息队列：发布和订阅消息流，这个功能类似于消息队列，这也是 Kafka 也被归类为消息队
列的原因。
2. 容错的持久⽅式存储记录消息流： Kafka 会把消息持久化到磁盘，有效避免了消息丢失的⻛
险·。
3. 流式处理平台： 在消息发布的时候进⾏处理，Kafka 提供了⼀个完整的流式处理类库。

Kafka 主要有两⼤应⽤场景：

1. 消息队列 ：建⽴实时流数据管道，以可靠地在系统或应⽤程序之间获取数据。
2. 数据处理： 构建实时的流数据处理程序来转换或处理数据流。

https://blog.csdn.net/qq_41544550/article/details/123534948

### rpc的原理

https://blog.csdn.net/qq_34272760/article/details/120734068

**RPC是什么** （分布式、远程调用）（Spring Cloud、阿里的 Dubbo）

RPC是远程调用过程的简写，是一个协议，处于网络通信协议的第五层：会话层，其下就是TCP/IP协议，在建立在其基础上的通信会话协议。RPC定义了交互的模式，而应用程序使用这些模式，来访问其他服务器的方法，并不需要关系具体的网络上的细节。

比如两台服务器A，B，一个应用部署在A服务器上，想要调用B服务器上应用提供的函数或者方法，由于不在一个内存空间，不能直接调用，这时候需要通过就可以应用RPC框架的实现来解决。

**RPC原理**

<img src="E:\2021java\Typora\typora-user-images\format,png.jpeg" alt="图片描述" style="zoom: 50%;" />

1、服务集成 RPC 后，服务（这里的服务就是图中的 Provider，服务提供者）启动后会通过 Register（注册）模块，把服务的唯一 ID 和 IP 地址，端口信息等注册到 RPC 框架注册中心（图中的 Registry 部分）。
2、当调用者（Consumer）想要调用服务的时候，通过 Provider 注册时的的服务唯一 ID 去注册中心查找在线可供调用的服务，返回一个 IP 列表（3.notify 部分）。
3、第三步 Consumer 根据一定的策略，比如随机 or 轮训从 Registry 返回的可用 IP 列表真正调用服务（4.invoke）。
4、最后是统计功能，RPC 框架都提供监控功能，监控服务健康状况，控制服务线上扩展和上下线（5.count）

### 除了常见的设计模式都用过哪些

设计模式的分类（23种）
**创建型（5种**）
创建对象时，不直接实例化对象，根据特定场景，由程序创建对象
工厂方法模式、抽象工厂模式、单例模式、建造者模式、原型模式
**结构型（7种）**
用于帮助将多个对象组织成更大的结构
适配器模式、装饰器模式、代理模式、外观模式、桥接模式、组合模式、享元模式
**行为型（11种）**
用于帮助系统间各对象的通信，以及如何控制复杂系统中流程
策略模式、模板方法模式、观察者模式、迭代子模式、责任链模式、命令模式、备忘录模式、状态模式、访问者模式、中介者模式、解释器模式

**单例模式**：要求能够返回对象的一个引用和一个获得实例的方法

​				适合场景：需要频繁创建和销毁对象时、创建对象耗时或消耗资源过多时
​				使用实例：多线程的线程池设计一般也是采用单例模式，因为线程池要方便对池中的线程进行控制

**工厂模式**：主要是为创建对象提供接口，降低程序的耦合性

​				适合场景：编码时不确定需要哪种类的实例

**代理模式**：

1、通过代理控制对象的访问，可以在这个对象调用方法之前或之后去添加新的功能，在原有代码不修改的情况下，直接在业务流程中切入新代码，增加新功能
2、静态代理：在程序运行前就已经存在代理类
3、动态代理：利用 JDK 的 API，动态的在内存中构建代理对象				

**观察者模式**：又叫发布-订阅模式，主要用于1对N的通知。当一个对象的状态变化时，他需要及时告知一系列对象，令他们做出相应。

​					使用实例：比如说微信公众号，假设微信用户就是观察者，微信公众号是被观察者，有多个的微信用户关注了程序猿这个公众号，当这个公众号更新时就会通知这些订阅的微信用户。

**适配器模式**：将一个类的接口转换成客户希望的另外一个接口 （**实习项目用到，将原有查询改成按条件查询，改写原有接口**）

​					三种：类适配器——继承、对象适配器——继承+接口实现、接口适配器——抽象类适配

​					使用场景：

​							系统需要使用现有的类，但这些类的接口不符合系统需要
​							建立一个可以重复使用的类

​					使用实例：一个登录系统最开始的登录方法是login方法中判断账号+密码是否正确的方式登录；接着要改成短信登录，就继承原来的登录类，重写login方法，判断账号和短信验证码；如果要使用微信登录，就要继承原来的login方法，把他变成账号+微信验证的方法登录

### cpu爆满的排查

https://blog.csdn.net/jianzhang11/article/details/125567952

1、使用 top 找到占用 CPU 最高的 Java 进程（按CPU使用率排序，键入大写的P）

2、用 top -Hp 命令查看占用 CPU 最高的线程（按CPU使用率排序，键入大写的P），查到占用CPU最高的那个线程 PID

3、查看堆栈信息，定位对应代码

先通过printf命令将其转化成16进制，之所以需要转化为16进制，是因为堆栈里，线程id是用16进制表示的

```shell
[root@review-dev ~]# printf "%x\n" 16756
4174
```

得到16进制的线程ID为4174。

通过jstack命令查看堆栈信息：jstack 16738 | grep '0x4174' -C10 --color



### 切片的原理和应用

AOP：这种在运行时，动态地将代码切入到类的指定方法、指定位置上的编程思想就是面向切面的编程。

AOP(Aspect-Oriented Programming:⾯向切⾯编程)能够将那些与业务⽆关，却为业务模块所**共同调⽤**的逻辑或责任（例如**事务处理、⽇志管理、权限控制**等）**封装**起来，便于减少系统的重复代码，**降低模块间的耦合度**，并有利于未来的可拓展性和可维护性。

**原理**：Spring AOP就是基于动态代理的，如果要代理的对象，实现了某个接⼝，那么Spring AOP会使⽤JDK Proxy，去创建代理对象，⽽对于没有实现接⼝的对象，就⽆法使⽤ JDK Proxy 去进⾏代理了，这时候Spring AOP会使⽤Cglib ，这时候Spring AOP会使⽤ Cglib ⽣成⼀个被代理对象的⼦类来作为代理。**使⽤ AOP 之后我们可以把⼀些通⽤功能抽象出来，在需要⽤到的地⽅直接使⽤即可，这样⼤⼤简化了代码量。我们需要增加新功能时也⽅便，这样也提⾼了系统扩展性。**

**应用**：⽇志功能、事务管理等等场景都⽤到了 AOP

**例子**：

​		实际上也就是说，让不同的类设计不同的方法。这样代码就分散到一个个的类中去了。这样做的好处是降低了代码的复杂程度，使类可重用。
​		但是人们也发现，在分散代码的同时，也增加了代码的重复性。什么意思呢？比如说，我们在两个类中，可能都需要在每个方法中做日志。按面向对象的设计方法，我们就必须在两个类的方法中都加入日志的内容。也许他们是完全相同的，但就是因为面向对象的设计让类与类之间无法联系，而不能将这些重复的代码统一起来。
​		也许有人会说，那好办啊，我们可以将这段代码写在一个独立的类独立的方法里，然后再在这两个类中调用。但是，这样一来，这两个类跟我们上面提到的独立的类就有耦合了，它的改变会影响这两个类。那么，有没有什么办法，能让我们在需要的时候，随意地加入代码呢？这种在运行时，动态地将代码切入到类的指定方法、指定位置上的编程思想就是面向切面的编程。

​		**一般而言，我们管切入到指定类指定方法的代码片段称为切面，而切入到哪些类、哪些方法则叫切入点。有了AOP，我们就可以把几个类共有的代码，抽取到一个切片中，等到需要时再切入对象中去，从而改变其原有的行为。**

### HashMap的实现原理

**底层实现：**

**JDK1.8 之前 HashMap 底层是 数组和链表 结合在⼀起使⽤也就是 链表散列。HashMap 通过key 的 hashCode 经过扰动函数处理过后得到 hash 值，然后通过 (n - 1) & hash 判断当前元素存放的位置（这⾥的 n 指的是数组的⻓度），如果当前位置存在元素的话，就判断该元素与要存⼊的元素的 hash 值以及 key 是否相同，如果相同的话，直接覆盖，不相同就通过拉链法解决冲突。**

**相⽐于之前的版本， JDK1.8 之后在解决哈希冲突时有了较⼤的变化，当链表⻓度⼤于阈值（默认为 8）（将链表转换成红⿊树前会判断，如果当前数组的⻓度⼩于 64，那么会选择先进⾏数组扩容，⽽不是转换为红⿊树）时，将链表转化为红⿊树，以减少搜索时间。**



1、HashMap非线程安全，效率比hashtable高

2、可以将null作为key和value，但作为key只能有一个，作为value可以有多个

3、默认初始量为16，之后每次扩容变成原来的2倍。如果指定容量初始值，之后会将其扩充为 2 的幂次方

**4、底层结构：**

**在JDK1.7 中，由“数组+链表”组成，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的。**

**在JDK1.8 中，由“数组+链表+红黑树”组成。当链表过长，则会严重影响 HashMap 的性能，红黑树搜索时间复杂度是 O(logn)，而链表是糟糕的 O(n)。因此，JDK1.8 对数据结构做了进一步的优化，引入了红黑树，链表和红黑树在达到一定条件会进行转换**

5、解决hash冲突：

当链表超过 8 且数据总量超过 64 才会转红黑树。

将链表转换成红黑树前会判断，如果当前数组的长度小于64，那么会选择先进行数组扩容，而不是转换为红黑树，以减少搜索时间。

6、为什么在解决 hash 冲突的时候，不直接用红黑树？而选择先用链表，再转红黑树：

因为红黑树需要进行左旋，右旋，变色这些操作来保持平衡，而单链表不需要。当元素小于 8 个的时候，此时做查询操作，链表结构已经能保证查询性能。当元素大于 8 个的时候， 红黑树搜索时间复杂度是 O(logn)，而链表是 O(n)，此时需要红黑树来加快查询速度，但是新增节点的效率变慢了。

7、不用二叉查找树的原因：二叉查找树在特殊情况下会变成一条线性结构（这就跟原来使用链表结构一样了，造成很深的问题），遍历查找会非常慢。

### Redis缓存在项目中如何应用

羊城先锋：验证码缓存、密码缓存（会到期）

缓存击穿：key到期，导致大量请求直接到数据库

缓存穿透：用户不断发起请求缓存和数据库中都没有的数据

### 对多线程的理解

**总体上说**：

**从计算机底层来说**： 线程可以⽐作是轻量级的进程，是程序执⾏的最⼩单位,线程间的切换和调度的成本远远⼩于进程。另外，多核 CPU 时代意味着多个线程可以同时运⾏，这减少了线程上下⽂切换的开销。
**从当代互联⽹发展趋势来说**： 现在的系统动不动就要求百万级甚⾄千万级的并发量，⽽多线程并发编程正是开发⾼并发系统的基础，利⽤好多线程机制可以⼤⼤提⾼系统整体的并发能⼒以及性能。

**计算机底层上说**：

**单核时代**： 在单核时代多线程主要是为了提⾼ CPU 和 IO 设备的综合利⽤率。举个例⼦：当只有⼀个线程的时候会导致 CPU 计算时，IO 设备空闲；进⾏ IO 操作时，CPU 空闲。我们可以简单地说这两者的利⽤率⽬前都是 50%左右。但是当有两个线程的时候就不⼀样了，
当⼀个线程执⾏ CPU 计算时，另外⼀个线程可以进⾏ IO 操作，这样两个的利⽤率就可以在理想情况下达到 100%了。
**多核时代**: 多核时代多线程主要是为了提⾼ CPU 利⽤率。举个例⼦：假如我们要计算⼀个复杂的任务，我们只⽤⼀个线程的话，CPU 只会⼀个 CPU 核⼼被利⽤到，⽽创建多个线程就可以让多个 CPU 核⼼被利⽤到，这样就提⾼了 CPU 的利⽤率。

### 悲观锁与乐观锁的理解及实际应用

**悲观锁**：是一种对数据的修改持有悲观态度的并发控制方式。总是假设最坏的情况，每次读取数据的时候都默认其他线程会更改数据，因此需要进行加锁操作，当其他线程想要访问数据时，都需要阻塞挂起。

优点：对每次读取数据都进行加锁，解决了脏读、幻读和不可重复读等可能存在的问题
缺点：每次都加锁降低了系统的吞吐量，并发量大时许多线程都被阻塞

**适用场景：适用于读少写多的情况下，经常产生冲突的场景**

```mysql
select * from tbl_user where id=1 for update;
需要注意的是，for update 生效需要同时满足两个条件时才生效：
1、数据库的引擎为 innoDB
2、操作位于事务块中（BEGIN/COMMIT）
```

**乐观锁**：乐观锁是相对悲观锁而言的，是一种对数据的修改持有悲观态度的并发控制方式。乐观锁假设数据一般情况不会造成冲突，所以在**数据进行提交更新的时候**，才会正式对数据的冲突与否进行检测，如果冲突，则返回给用户异常信息，让用户决定如何去做。

优点：省去了锁的开销，加大了系统的整个吞吐量

缺点：1、ABA问题。 2、循环时间长开销大。 3、只能保证一个共享变量的原子操作

**适用场景：适用于读多写少的情况(多读场景)，冲突较少的场景**

### SQL优化

**限定数据的范围**：务必禁⽌不带任何限制数据范围条件的查询语句。⽐如：我们当⽤户在查询订单历史的时候，我们可以控制在⼀个⽉的范围内。

**读/写分离**：经典的数据库拆分⽅案，主库负责写，从库负责读

**垂直分区**：根据数据库⾥⾯数据表的相关性进⾏拆分。 例如，⽤户表中既有⽤户的登录信息⼜有⽤户的基本信息，可以将⽤户表拆分成两个单独的表，甚⾄放到单独的库做分库。简单来说垂直拆分是指数据表列的拆分，把⼀张列⽐较多的表拆分为多张表。 如下图所示，这样来说⼤家应该就更容易理解了。

![image-20221223133703795](E:\2021java\Typora\typora-user-images\image-20221223133703795.png)

**垂直拆分的优点**： 可以使得列数据变⼩，在查询时减少读取的Block数，减少I/O次数。此外，垂直分区可以简化表的结构，易于维护。
**垂直拆分的缺点**： 主键会出现冗余，需要管理冗余列，并会引起Join操作，可以通过在应⽤层进⾏Join来解决。此外，垂直分区会让事务变得更加复杂；

### 项目开发遇到的困难

数据查询量大，mybatis单独使用一个info_COUNT

# 小米（准备）

## 面试

### 几种常用的设计模式

几大类型？工厂模式？手写DCL？

1、什么是设计模式

设计模式，是一套被反复使用、多数人知晓的、经过分类编目的、代码设计经验的总结。使用设计模式是为了可重用代码、让代码更容易被他人理解、保证代码可靠性、程序的重用性。

2、设计模式分类

三种类型：创建型、结构型、行为型

**创建型模式**，共五种：**工厂方法模式**、抽象工厂模式、**单例模式**、建造者模式、原型模式。

**结构型模式**，共七种：**适配器模式**、装饰器模式、**代理模式**、外观模式、桥接模式、组合模式、享元模式。

**行为型模式**，共十一种：策略模式、**模板方法模式**、观察者模式、迭代子模式、责任链模式、命令模式、备忘录模式、状态模式、访问者模式、中介者模式、解释器模式



常用模式：单例模式、工厂模式、适配器模式

**单例模式**：保证一个类只有一个实例，并且提供一个访问该全局访问点



**工厂模式**：工厂类可以根据条件生成不同的子类实例，这些子类有一个公共的抽象父类并且实现了相同的方法，但是这些方法针对不同的数据进行了不同的操作（多态方法）。当得到子类的实例后，开发人员可以调用基类中的方法而不必考虑到底返回的是哪一个子类的实例。

工厂模式是我们最常用的实例化对象模式了，是用工厂方法代替new操作的一种模式。利用工厂模式可以降低程序的耦合性，为后期的维护修改提供了很大的便利。**将选择实现类、创建对象统一管理和控制**。从而将调用者跟我们的实现类解耦。

**工厂模式在spring的应用**：

看过Spring源码就知道，在Spring IOC容器创建bean的过程是使用了工厂设计模式

Spring中无论是通过xml配置还是通过配置类还是注解进行创建bean，大部分都是通过简单工厂来进行创建的。

当容器拿到了beanName和class类型后，动态的通过反射创建具体的某个对象，最后将创建的对象放到Map中。

**工厂模式在spring应用的原因**：

在实际开发中，如果我们A对象调用B，B调用C，C调用D的话我们程序的耦合性就会变高。（耦合大致分为类与类之间的依赖，方法与方法之间的依赖。）

在很久以前的三层架构编程时，都是控制层调用业务层，业务层调用数据访问层时，都是是直接new对象，耦合性大大提升，代码重复量很高，对象满天飞

为了避免这种情况，Spring使用工厂模式编程，写一个工厂，由工厂创建Bean，以后我们如果要对象就直接管工厂要就可以，剩下的事情不归我们管了。Spring IOC容器的工厂中有个静态的Map集合，是为了让工厂符合单例设计模式，即每个对象只生产一次，生产出对象后就存入到Map集合中，保证了实例不会重复影响程序效率。



**适配器模式**：将⼀个类的接⼝转换成⽤户希望得到的另⼀个接⼝。它使原本不相容的接⼝得以协同⼯作——速记关键字:转换接⼝



**代理模式**：通过代理控制对象的访问，可以在这个对象调用方法之前、调用方法之后去处理/添加新的功能。(也就是AO的P微实现)。

代理在原有代码乃至原业务流程都不修改的情况下，直接在业务流程中切入新代码，增加新功能，这也和Spring的（面向切面编程）很相似

**代理模式应用场景**：Spring AOP、日志打印、异常处理、事务控制、权限控制等



**模板方法模式**：提供一个**抽象类**，将部分逻辑以具体方法或构造器的形式实现，然后声明一些抽象方法来迫使子类实现剩余的逻辑。不同的子类可以以不同的方式实现这些抽象方法（**多态实现**），从而实现不同的业务逻辑。



### 手写单例模式

类初始化时静态变量也初始化、类初始化时静态代码块自动执行

①将构造器私有，不允许外界通过构造器创建对象；②通过公开的静态方法向外界返回类的唯一实例。

```java
//饿汉式：类初始化时就创建类对象，利用静态变量的初始化
public class EhSingleton{
    private static EhSingleton ehSingleton = new EhSingleton(); //静态变量的初始化
    private EhSingleton(){} //构造方法要设置为私有
    public static EhSingleton getInstance(){
        return ehSingleton;
    }
}

//饿汉式：类初始化时就创建类对象，利用静态代码块的自动执行
public class EhSingleton{
    private static EhSingleton ehSingleton = null; //静态变量的初始化
    static{
        ehSingleton = new EhSingleton();
    }
    private EhSingleton(){} //构造方法要设置为私有
    public static EhSingleton getInstance(){
        return ehSingleton;
    }
}

//懒汉式（线程不安全版本），需要用要对象时，才创建实例
public class LazySingleton{
    private LazySingleton lazySingleton = null;
    private LazySingleton(){}
    public static LazySingleton getInstance(){
        if(lazySingleton == null) { //如果实例还没创建，就创建；如果已经创建了，则直接返回
            lazySingleton = new LazySingleton();
        }
        return lazySingleton;
    }
}

//懒汉式（线程安全版本，效率低），需要用要对象时，才创建实例
public class LazySingleton{
    private LazySingleton lazySingleton = null;
    private LazySingleton(){}
    public static synchronized LazySingleton getLazySingleton(){
        if(lazySingleton == null) { //如果实例还没创建，就创建；如果已经创建了，则直接返回
            lazySingleton = new LazySingleton();
        }
        return lazySingleton;
    }
}
```

### synchronized锁原理？与Lock锁的区别

**synchronized 关键字底层原理属于 JVM 层⾯**

理解：synchronized 关键字解决的是多个线程之间访问资源的同步性， synchronized 关键字可以保证被它修饰的⽅法或者代码块在任意时刻只能有⼀个线程执⾏。

1、**作⽤/原理**： 
Java中的synchronized，通过使⽤**内置锁**，来实现对共享变量的**同步操作**，进⽽解决了对共享变量操作的**原⼦性**、保证了其他线程对共享变量的**可⻅性**、**有序性**，从⽽确保了并发情况下的线程安全。

同时synchronized是可重入的锁，避免了同⼀个线程重复请求⾃身已经获取的锁时出现死锁问题（请求于保持、不可剥夺感觉都有体现）

2、**基础⽤法**： 
普通同步⽅法、静态同步⽅法、同步代码块

3、**什么是内置锁**： 
在Java中，每个对象都有⼀把锁，放置于对象头中，⽤于记录当前对象被哪个线程所持有。

**原理** 

**1、synchronized 同步语句块的实现使⽤的是 monitorenter 和 monitorexit 字节码指令，其中monitorenter 指令指向同步代码块的开始位置， monitorexit 指令则指明同步代码块的结束位置。**

**2、当执⾏ monitorenter 指令时，线程试图获取锁也就是获取 对象监视器 monitor 的持有权。**

**3、在执⾏ monitorenter 时，会尝试获取对象的锁，如果锁的计数器为 0 则表示锁可以被获取，获取后将锁计数器设为 1 也就是加 1。**

**4、在执⾏ monitorexit 指令后，将锁计数器设为 0，表明锁被释放。如果获取对象锁失败，那当前线程就要阻塞等待，直到锁被另外⼀个线程释放为⽌。**

**5、synchronized 修饰的方法并没有 monitorenter 指令和 monitorexit 指令，取得代之的确实是 ACC_SYNCHRONIZED 标识，该标识指明了该方法是一个同步方法。**

**不过两者的本质都是对对象监视器 monitor 的获取。**





**与Lock锁的区别**

https://blog.csdn.net/qq_44750696/article/details/103883082

1、**Synchronized** 是Java的一个**关键字**，而**Lock**是java.util.concurrent.Locks 包下的一个**接口**；

2、Synchronized 使用过后，会自动释放锁，而Lock需要手动上锁、手动释放锁。（在 finally 块中）

3、Lock提供了更多的实现方法，而且 可响应中断、可定时， 而synchronized 关键字不能响应中断；

响应中断：
A、B 线程同时想获取到锁，A获取锁以后，B会进行等待，这时候等待着锁的线程B，会被Tread.interrupt()方法给中断等待状态、然后去执行其他的事情，而synchronized锁无法被Tread.interrupt()方法给中断掉；

4、synchronized关键字是非公平锁，即，不能保证等待锁的那些线程们的顺序，而Lock的子类ReentrantLock默认是非公平锁，但是可通过一个布尔参数的构造方法实例化出一个公平锁；

公平锁与非公平锁：

​		公平锁就是：先等待的线程，先获得锁。
​		非公平锁就是：不能够保证 等待锁的 那些线程们的顺序，
​		公平锁因为需要维护一个等待锁资源的队列，所以性能相对低下；

5、synchronized无法判断，是否已经获取到锁，而Lock通过tryLock()方法可以判断，是否已获取到锁；

6、Lock可以通过分别定义读写锁提高多个线程读操作的效率。

7、二者的底层实现不一样：**synchronized是同步阻塞**，采用的是**悲观并发策略**；**Lock是同步非阻塞**，采用的是**乐观并发策略**（底层基于volatile关键字和CAS算法实现）



### java为什么要有泛型？为什么要泛型擦除？

泛型是JDK1.5之后提出的新概念，本意是**让同一套代码可以适应更多的类型**。

泛型的本质是为了**参数化类型**（在不创建新的类型的情况下，通过泛型指定的不同类型来控制形参具体限制的类型）。也就是说在泛型使用过程中，操作的数据类型被指定为一个参数，这种参数类型可以用在类、接口和方法中，分别被称为泛型类、泛型接口、泛型方法。

1、适用于多种数据类型执行相同的代码
2、泛型中的类型在使用时指定，不需要强制类型转换（类型安全，编译器会检查类型）

**泛型的好处**：

 1、减少了强制类型转换的次数：获取数据值更⽅便  

2、类型安全：调⽤⽅法时更安全  

3、泛型只在编译时期起作⽤：运⾏阶段 JVM 看不⻅泛型类型（JVM 只能看⻅对应的原始类型，因为进⾏了类型擦除） 

4、带泛型的类型 在使⽤时没有指定泛型类型时，默认使⽤ Object 类型 

5、比如在创建容器的时候，指定容器持有何种类型，**以便在编译期间就能够保证类型的正确性，而不是将错误留到运行的时候**。

6、无论是创建类、方法的时候都可以泛化参数，可以做到代码的复用。**泛型的出现最主要目的为了更好的创建容器类**



**泛型擦除**：

jdk是通过类型擦除来实现泛型的，编译器在编译之后会擦除了所有类型相关的信息，所以在运行时不存在任何类型相关的信息。比如如果编写 List<String> names = new Arraylist<>();在运行的时候，仅用List来表示。



**为什么要泛型擦除**：

Java泛型这个特性是从JDK 1.5才开始加入的，因此为了兼容之前的版本，Java泛型的实现采取了“伪泛型”的策略，即Java在语法上支持泛型，但是在编译阶段会进行所谓的“类型擦除”（Type Erasure），将所有的泛型表示（尖括号中的内容）都替换为具体的类型（其对应的原生态类型），就像完全没有泛型一样。



### java类加载机制

https://blog.csdn.net/JokerLJG/article/details/123783973?spm=1001.2101.3001.6650.8&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-8-123783973-blog-124052305.pc_relevant_3mothn_strategy_recovery&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-8-123783973-blog-124052305.pc_relevant_3mothn_strategy_recovery&utm_relevant_index=10

java虚拟机将编译后的class文件加载到内存中，进行校验、转换、解析和初始化，到最终的使用。这就是java类加载机制；

**1、类的生命周期**

<img src="E:\2021java\Typora\typora-user-images\watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6aqR5Liq5bCP6JyX54mb,size_20,color_FFFFFF,t_70,g_se,x_16.png" alt="请添加图片描述" style="zoom:67%;" />

其中加载、验证、准备、初始化和卸载这几个的顺序是确定的，类的加载过程必定按上面的流程顺序进行。但解析过程顺序不一定，它在某些情况下可能在初始化阶段后再开始，因为Java支持运行时绑定的。

注意区分**类加载和加载阶段的区别**：

​		**类加载**：指整个类加载过程，包括：加载阶段、链接阶段、初始化阶段。
​		**加载阶段**：指类的加载过程中的一个阶段（加载阶段）。

**2、类的加载过程**

一般所说的类的加载过程指的是类的生命周期中的这几个阶段：**加载**、**链接**（验证、准备、解析）、**初始化**的过程。

**加载时机**
		加载阶段，Java虚拟机规范中没有进行约束。

​		初始化阶段，Java虚拟机规范中有严格的约束。

**下面5种情况必须立即进行初始化**：

​		1.创建类的实例，也就是new一个对象；获取或者赋值类的静态变量、静态非字面值常量（静态字面值常量除外）。
​		2.调用类的静态方法。
​		3.通过反射调用（Class.forName(“xxx”)）。
​		4.初始化一个类的子类（会首先初始化子类的父类）。
​		5.启动程序所使用的main方法所在类。

​		上面的5中情况称为主动引用，除此主动引用之外还有被动引用。

**3、类的加载方式**

类加载的方式分为：隐式加载、显式加载。

**隐式加载**：

​		创建类对象
​		使用类的静态域
​		创建子类对象
​		使用子类的静态域
​		在JVM启动时，BootStrapLoader会加载一些JVM自身运行所需的class
​		在JVM启动时，ExtClassLoader会加载指定目录下一些特殊的class
​		在JVM启动时，AppClassLoader会加载classpath路径下的class，以及main函数所在的类的class文件

显式加载：

​		ClassLoader.loadClass(className)

​		只加载和连接、不会进行初始化。

​		Class.forName(String name, boolean initialize,ClassLoader loader)

​		使用loader进行加载和连接，根据参数initialize决定是否初始化。

**4、类加载器**

JVM的类加载是通过ClassLoader及其子类来完成的

**加载器**：

​		**BootstrapClassLoader（启动类加载器）**

负责加载$JAVA_HOME中jre/lib/rt.jar里所有的class或者 -Xbootclassoath选项指定的jar包。由C++实现，不是ClassLoader子类。

​		ExtensionClassLoader（标准扩展类加载器）

负责加载java平台中扩展功能的一些jar包，包括$JAVA_HOME中jre/lib/*.jar或者 -Djava.ext.dirs指定目录下的jar包。

​		**AppClassLoader（系统类加载器）**

负责加载classpath中指定的jar包或者 -Djava.class.path所指定目录下的类和jar包。

​		**CustomClassLoader（自定义加载器）**

通过java.lang.ClassLoader的子类自定义加载class，属于应用程序根据自身需要自定义的ClassLoader。如tomcat、jboss都会根据j2ee规范自行实现ClassLoader。

**加载顺序：**

​		1、加载过程中会先检查类是否被已加载，检查顺序是自底向上，从CustomClassLoader到BootStrapClassLoader逐层检查，只要某个classloader已加载就视为已加载此类，保证此类只所有ClassLoader加载一次。而加载的顺序是自顶向下，也就是由上层来逐层尝试加载此类。
​		2、在加载类时，每个类加载器会将加载任务上交给其父，如果其父找不到，再由自己去加载。
​		3、Bootstrap Loader（启动类加载器）是最顶级的类加载器了，其父加载器为null。

**5、详细阶段分析（加载、链接、初始化、使用、卸载）**

​		1、**加载阶段**
加载主要是将.class文件通过二进制字节流读入到JVM中。加载是类加载过程的一个阶段，这两个概念一定不要混淆。

​		**加载阶段，虚拟机需要完成以下三件事情**：

​			通过classloader在classpath中获取XXX.class文件，将其以二进制流的形式读入内存。
​			将字节流所代表的静态存储结构转化为方法区的运行时数据结构。
​			在内存中生成一个该类的java.lang.Class对象，这样便可以通过该对象访问方法区中的这些数据。

​		2、**链接阶段**

当类被加载后，系统为之生成一个对应的Class对象，接着会进入链接阶段，链接阶段将会负责把类的二进制文件合并到JRE中。类的链接阶段分为如下三个阶段：

​		验证：验证阶段用于检验被加载的类是否有正确的内部结构，并和其他类协调一致；
​		准备：准备阶段则负责为类的静态属性分配内存，并设置默认初始值；
​		解析：将类的二进制数据中的符号引用替换成直接引用（符号引用是用一组符号描述所引用的目标；直接引用是指向目标的指针）

​		3、**初始化阶段**

为类的静态变量赋初值

特别注意：类在初始化前，必须先经过加载、验证、准备阶段

​		4、**使用阶段**

​		5、**卸载阶段**

如下几种情况下，Java虚拟机将结束生命周期：

执行了System.exit()方法
程序正常执行结束
程序在执行过程中遇到了异常或错误而异常终止
由于操作系统出现错误而导致Java虚拟机进程终止



### 什么是反射？反射的优缺点

https://blog.csdn.net/qq_51515673/article/details/124830558

Reflection(反射) 是 Java 程序开发语言的特征之一，它允许运行中的 Java 程序对自身进行检查。被private封装的资源只能类内部访问，外部是不行的，但反射能直接操作类私有属性。反射可以在**运行时**获取一个类的所有信息，（包括成员变量，成员方法，构造器等），并且可以操纵类的字段、方法、构造器等部分。

要想解剖一个类，必须先要获取到该类的字节码文件对象。而解剖使用的就是Class类中的方法。所以先要获取到每一个字节码文件对应的Class类型的对象。

**反射就是把java类中的各种成分映射成一个个的Java对象。**



1、获取类对应的字节码的对象，常用第三种方法

```java
//1、调用某个类的对象的getClass()方法，即：对象.getClass()；
Person p = new Person();
Class clazz = p.getClass();

//2、调用类的class属性类获取该类对应的Class对象，即：类名.class
Class clazz = Person.class;

//3、使用Class类中的forName()静态方法（最安全，性能最好）即：Class.forName(“类的全路径”)
Class clazz = Class.forName("类的全路径");
```

2、当我们获得了想要操作的类的Class对象后，可以通过Class类中的方法获取和查看该类中的方法和属性。



**反射的优缺点**

**优点**：

​		1.增加程序的灵活性，避免将程序写死到代码里。

​		2.代码简洁，提高代码的复用率，外部调用方便

​		3.对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法

缺点：

​		1.性能问题

使用反射基本上是一种解释操作，用于字段和方法接入时要远慢于直接代码。因此Java反射机制主要应用在对灵活性和扩展性要求很高的系统框架上,普通程序不建议使用。

反射包括了一些动态类型，所以JVM无法对这些代码进行优化。因此，反射操作的效率要比那些非反射操作低得多。我们应该避免在经常被 执行的代码或对性能要求很高的程序中使用反射。

​		2.使用反射会模糊程序内部逻辑

程序人员希望在源代码中看到程序的逻辑，反射等绕过了源代码的技术，因而会带来维护问题。反射代码比相应的直接代码更复杂。

​		3.安全限制

使用反射技术要求程序必须在一个没有安全限制的环境中运行。如果一个程序必须在有安全限制的环境中运行，如Applet，那么这就是个问题了。

​		4.内部暴露

由于反射允许代码执行一些在正常情况下不被允许的操作(比如访问私有的属性和方法)，所以使用反射可能会导致意料之外的副作用－－代码有功能上的错误，降低可移植性。反射代码破坏了抽象性，因此当平台发生改变的时候，代码的行为就有可能也随着变化。



### 介绍下JVM

 **JVM由JVM运行时数据区（图示中蓝色框包含部分）、执行引擎、本地库接口、本地方法库组成。**

 **JVM运行时数据区，分为方法区、堆、虚拟机栈、本地方法栈和程序计数器。**

<img src="E:\2021java\Typora\typora-user-images\363636d2b3e715a8a99d3c0dc846c045.png" alt="img" style="zoom:67%;" />



### JVM的各个内存区域

https://blog.csdn.net/m0_63270506/article/details/124367177

**线程私有的：**程序计数器、虚拟机栈、本地方法栈

**线程共享的**：堆、方法区、直接内存 (⾮运⾏时数据区的⼀部分)。

**1、程序计数器的两个作用**

1. **字节码解释器**通过改变程序计数器来**依次读取指令**，从⽽实现代码的流程控制，如：顺序执⾏、选择、循环、异常处理。
2. 在多线程的情况下，程序计数器⽤于记录当前线程执⾏的位置，从⽽当线程被切换回来的时候能够知道该线程上次运⾏到哪⼉了。



**2、虚拟机栈**

它的⽣命周期和线程相同，**描述的是 Java⽅法执⾏的内存模型**，每次⽅法调⽤的数据都是通过栈传递的。

Java 内存可以粗糙的区分为堆内存（Heap）和栈内存 (Stack),其中**栈就是现在说的虚拟机栈，或者说是虚拟机栈中局部变量表部分**。 

（实际上， Java 虚拟机栈是由⼀个个栈帧组成，⽽每个**栈帧**中都拥有：**局部变量表**、**操作数栈**、**动态链接**、**⽅法出⼝信息**。）

**局部变量表**主要存放了编译期可知的**各种数据类型**（boolean、 byte、 char、 short、 int、 float、long、 double）、 **对象引⽤**（reference 类型，它不同于对象本身，可能是⼀个指向对象起始地址的引⽤指针，也可能是指向⼀个代表对象的句柄或其他与此对象相关的位置）。

**Java 虚拟机栈会出现两种错误**： StackOverFlowError 和 OutOfMemoryError 。

**StackOverFlowError** ： 若 Java 虚拟机栈的内存⼤⼩不允许动态扩展，那么当线程请求栈的深度超过当前 Java 虚拟机栈的最⼤深度的时候，就抛出 StackOverFlowError 错误。
**OutOfMemoryError** ： 若 Java 虚拟机堆中没有空闲内存，并且垃圾回收器也⽆法提供更多内存的话。就会抛出 OutOfMemoryError 错误。  

**那么⽅法/函数如何调⽤？**  

Java 栈可⽤类⽐数据结构中栈， Java 栈中保存的主要内容是栈帧，每⼀次函数调⽤都会有⼀个对应的栈帧被压⼊ Java 栈，每⼀个函数调⽤结束后，都会有⼀个栈帧被弹出。  

**Java ⽅法有两种返回⽅式**： return 语句、抛出异常。不管哪种返回⽅式都会导致栈帧被弹出。  



**3、本地方法栈**

和虚拟机栈所发挥的作⽤⾮常相似，区别是： **虚拟机栈为虚拟机执⾏ Java ⽅法** （也就是字节码）服务，⽽**本地⽅法栈则为虚拟机使⽤到的 Native ⽅法服务**。 在 HotSpot 虚拟机中和 Java虚拟机栈合⼆为⼀。

本地⽅法被执⾏的时候，在本地⽅法栈也会创建⼀个栈帧，⽤于存放该本地⽅法的**局部变量表**、**操作数栈**、**动态链接**、**出⼝信息**。

⽅法执⾏完毕后相应的栈帧也会出栈并释放内存空间，也会出现 StackOverFlowError 和OutOfMemoryError 两种错误。



**4、堆**

Java 虚拟机所管理的内存中最⼤的⼀块， Java 堆是所有线程共享的⼀块内存区域，在虚拟机启动时创建。 此内存区域的唯⼀⽬的就是存放对象实例，⼏乎所有的对象实例以及数组都在这⾥分配内存。  

Java 堆是垃圾收集器管理的主要区域，因此也被称作GC 堆 。

**在 Java 中，堆被划分成两个不同的区域：新生代 ( Young )、老年代 ( Old )。新生代 ( Young ) 又被划分为三个区域：Eden、From Survivor、To Survivor。**

这样划分的目的是为了使 JVM 能够更好的管理堆内存中的对象，包括内存的分配以及回收。



**5、方法区**

当虚拟机要使用一个类时，它需要读取并解析 Class 文件获取相关信息，再将信息存入到方法区。方法区会存储已被虚拟机加载的 类信息、字段信息、方法信息、常量、静态变量、即时编译器编译后的代码缓存等数据。

⽅法区和永久代的关系：  ⽅法区和永久代的关系很像Java 中接⼝和类的关系，类实现了接⼝，⽽永久代就是 HotSpot 虚拟机对虚拟机规范中⽅法区的⼀种实现⽅式。  

JDK1.8 hotspot移除了永久代⽤元空间(Metaspace)取⽽代之, 这时候字符串常量池还在堆, 运⾏时常量池还在⽅法区, 只不过⽅法区的实现从永久代变成了元空间(Metaspace)



**6、运行时常量池**

运⾏时常量池是⽅法区的⼀部分 。



### 垃圾回收算法？怎么判断是否可以回收？

https://blog.csdn.net/Polaris_XY_/article/details/127591344

**垃圾回收(GC)：GC就是要回收java程序允许过程中产生的垃圾，也可以理解为不再使用的内存**。

一个程序的内存是有限的，随着程序的运行，肯定存在申请内存的情况，如果使用完内存迟迟得不到释放，那么程序最终会因为没有内存而停止，所以内存的释放很重要。**在面向对象程序中内存的释放意味着对象的销毁**，只有对象销毁了内存才有可能得以释放。

**垃圾回收是什么**

Java 堆是垃圾收集器管理的主要区域，因此也被称作GC 堆（Garbage Collected Heap） 。从垃圾回收的⻆度，由于现在收集器基本都采⽤**分代垃圾收集算法**，所以 Java 堆还可以细分为：新⽣代和⽼年代，新生代可分为： Eden 空间、 From Survivor、 To Survivor 空间等。 进⼀步划分的⽬的是更好地回收内存，或者更快地分配内存。 

Eden 区、 From Survivor0("From") 区、 To Survivor1("To") 区都属于新⽣代， OldMemory 区属于⽼年代。

堆内存中对象的分配的基本策略  

![image-20220608124336332](C:\Users\陈康瑞\AppData\Roaming\Typora\typora-user-images\image-20220608124336332.png)

**1、对象优先在 eden 区分配** 

⽬前主流的垃圾收集器都会采⽤分代回收算法，因此需要将堆内存分为新⽣代和⽼年代，这样我们就可以根据各个年代的特点选择合适的垃圾收集算法。  

⼤多数情况下，对象在新⽣代中 eden 区分配。当 eden 区没有⾜够空间进⾏分配时，虚拟机将发起⼀次 Minor GC。



**2、⼤对象直接进⼊⽼年代** 

⼤对象就是需要⼤量连续内存空间的对象（⽐如：字符串、数组）。原因：为了**避免**为⼤对象分配内存时由于分配担保机制带来的**复制⽽降低效率**。  



**3、⻓期存活的对象将进⼊⽼年代**  (Eden区多次没有被回收的对象，就会进入老年代)

如果对象在 Eden 出⽣并经过第⼀次 Minor GC 后仍然能够存活，并且能被 Survivor 容纳的话，将被移动到 Survivor 空间中，并将对象年龄设为 1.对象在 Survivor 中每熬过⼀次 MinorGC,年龄就增加 1 岁，当它的年龄增加到⼀定程度（默认为 15 岁），就会被晋升到⽼年代中。对象晋升到⽼年代的年龄阈值，可以通过参数 -XX:MaxTenuringThreshold 来设置。



**垃圾回收算法**

**1、标记-清除算法**

该算法分为“标记”和“清除”阶段：⾸先**标记出所有不需要回收的对象**，在标记完成后统⼀回收掉所有没有被标记的对象。它是最基础的收集算法，后续的算法都是对其不⾜进⾏改进得到。这种垃圾收集算法会带来两个明显的问题：

1. 效率问题
2. 空间问题（标记清除后会产⽣⼤量不连续的碎⽚）

<img src="E:\2021java\Typora\typora-user-images\image-20230101171821098.png" alt="image-20230101171821098" style="zoom:67%;" />

**2、复制算法**

为了解决效率问题，“复制”收集算法出现了。它可以将内存分为⼤⼩相同的两块，每次使⽤其中的⼀块。当这⼀块的内存使⽤完后，就**将还存活的对象复制到另⼀块去**，然后再把使⽤的空间⼀次清理掉。这样就使每次的内存回收都是对内存区间的⼀半进⾏回收。

<img src="E:\2021java\Typora\typora-user-images\image-20230101171911592.png" alt="image-20230101171911592" style="zoom:67%;" />

**3、标记-整理算法**

根据⽼年代的特点提出的⼀种标记算法，标记过程仍然与“标记-清除”算法⼀样，但后续步骤不是直接对可回收对象回收，⽽是让所有存活的对象向⼀端移动，然后直接清理掉端边界以外的内存。



**4、分代收集算法**

当前虚拟机的垃圾收集都采⽤分代收集算法，这种算法没有什么新的思想，只是根据对象存活周期的不同将内存分为⼏块。⼀般将 java 堆分为新⽣代和⽼年代，这样我们就可以根据各个年代的特点选择合适的垃圾收集算法。

**新⽣代中**，每次收集都会有⼤量对象死去，所以可以选择**复制算法**，只需要付出少量对象的复制成本就可以完成每次垃圾收集。

**⽼年代**的对象存活⼏率是⽐较⾼的，⽽且没有额外的空间对它进⾏分配担保，所以我们必须选择“**标记-清除**”或“**标记-整理**”算法进⾏垃圾收集。



**HotSpot 为什么要分为新⽣代和⽼年代**

主要是为了提升 GC 效率。上⾯提到的分代收集算法已经很好的解释了这个问题。



**怎么判断是否可以回收**

对堆垃圾回收前的第⼀步就是要判断哪些对象已经死亡

 **1、引⽤计数法(java不使用)**

给对象中添加⼀个**引⽤计数器**，每当有⼀个地⽅引⽤它，计数器就加1；当引⽤失效，计数器就减1；任何时候计数器为0的对象就是不可能再被使⽤的。

引用计数法实现简单，判定效率高。但是目前来讲**Java的垃圾回收器是没有实现这个算法的**。因为当栈里没有引用的话，**可能堆里几个内存块相互引用，这样虽然是垃圾，但是引用计数法却不能判断此为垃圾**，因为它们相互引用，计数不为0。



**2、可达性分析算法(java使用)**

这个算法的基本思想就是通过⼀系列的称为 “**GC Roots**” 的对象作为起点，从这些节点开始向下搜索，节点所⾛过的路径称为**引⽤链**，当⼀个对象到 GC Roots 没有任何引⽤链相连的话，则证明此对象是不可⽤的。

<img src="E:\2021java\Typora\typora-user-images\image-20230101164036837.png" alt="image-20230101164036837" style="zoom:67%;" />



### 为什么会出现死锁？怎么解决？

**死锁是什么**

线程死锁描述的是这样⼀种情况：多个线程同时被阻塞，它们中的⼀个或者全部都在等待某个资源被释放。由于线程被⽆限期地阻塞，因此程序不可能正常终⽌。如下图所示，线程 A 持有资源 2，线程 B 持有资源1，他们同时都想申请对⽅的资源，所以这两个线程就会互相等待⽽进⼊死锁状态。

```java
new Thread(() -> {
            synchronized (resource1) {
                System.out.println(Thread.currentThread() + "get resource1");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread() + "waiting get resource2");
                synchronized (resource2) {
                    System.out.println(Thread.currentThread() + "get resource2");
                }
            }
        }, "线程 1").start();

new Thread(() -> {
            synchronized (resource2) {
                System.out.println(Thread.currentThread() + "get resource2");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread() + "waiting get resource1");
                synchronized (resource1) {
                    System.out.println(Thread.currentThread() + "get resource1");
                }
            }
        }, "线程 2").start();
```

<img src="E:\2021java\Typora\typora-user-images\image-20230101173107718.png" alt="image-20230101173107718" style="zoom:67%;" />

**原因**：**在申请锁时发生了交叉闭环申请**。

线程在获得一个锁L1的情况下再去申请另外一个锁L2，也就是锁L1想要包含了锁L2，也就是说在获得了锁L1，并且没有释放锁L1的情况下，又去申请获得锁L2，这个是产生死锁的最根本原因。**另一个原因是默认的锁申请操作是阻塞的**。



**如何避免死锁**

产生死锁的四个必要条件

1. 互斥条件：该资源任意⼀个时刻只由⼀个线程占⽤。
2. 请求与保持条件：⼀个进程因请求资源⽽阻塞时，对已获得的资源保持不放。
3. 不剥夺条件:线程已获得的资源在末使⽤完之前不能被其他线程强⾏剥夺，只有⾃⼰使⽤完毕后才释放资源。
4. 循环等待条件:若⼲进程之间形成⼀种头尾相接的循环等待资源关系。

所以要破坏其中一个条件即可，**破坏循环等待条件**

可以考虑避免两个锁发生交叉申请，但很多时候两个锁申请必须交叉，比如银行转账，必须同时获得两个账户上的锁，才能进行操作，两个锁的申请必须发生交叉。解决如下：

**在涉及到要同时申请两个锁的方法中，总是以相同的顺序来申请锁**，比如总是先申请 id 大的账户上的锁 ，然后再申请 id 小的账户上的锁，这样就无法形成导致死锁的那个闭环。

```java
//两个线程申请资源的顺序，总是先申请resource1，在申请resource2，破坏了循环等待条件
new Thread(() -> {
            synchronized (resource1) {
                System.out.println(Thread.currentThread() + "get resource1");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread() + "waiting get resource2");
                synchronized (resource2) {
                    System.out.println(Thread.currentThread() + "get resource2");
                }
            }
        }, "线程 1").start();

new Thread(() -> {
            synchronized (resource1) {
                System.out.println(Thread.currentThread() + "get resource1");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread() + "waiting get resource2");
                synchronized (resource2) {
                    System.out.println(Thread.currentThread() + "get resource2");
                }
            }
        }, "线程 2").start();
```



### 介绍一下HTTP？

HTTP协议是Hyper Text Transfer Protocol（**超文本传输协议**）的缩写，是用于从万维网（WWW:World Wide Web ）**服务器传输超文本到本地浏览器的传送协议**。在 OSI 七层模型中，HTTP协议位于最顶层的应用层中。通过浏览器访问网页就直接使用了 HTTP 协议。使用 HTTP 协议时，客户端首先与服务端的 80 端口建立一个 TCP 连接，然后在这个连接的基础上进行请求和应答，以及数据的交换。

HTTP 有两个常用版本，分别是 HTTP1.0和 HTTP1.1。**主要区别**在于 HTTP1.0 中每次请求和应答都会使用一个新的 TCP 连接，而从 HTTP1.1 开始，运行在一个 TCP 连接上发送多个命令和应答。因此大幅度减少了 TCP 连接的建立和断开，提高了效率。

特点：

**1、简单**

基本报⽂格式为header+body，头部信息也是key-value简单⽂本的形式，易于理解。

**2、灵活和易于扩展**

1. HTTP协议⾥的各种请求⽅法、URI/URL、状态码、头字段等每个组成要求都没有被固定死，都允许开发⼈员
⾃定义和扩充; 
2. HTTP⼯作在应⽤层（OSI第七层），下层可以随意变化; 
3. HTTPS就是在HTTP与TCP之间增加了SSL/TSL安全传输层，HTTP/3把TCP换成了基于UDP的QUIC。 

**3、⽆状态**

服务器不会去记忆HTTP的状态，所以不需要额外的资源来记录状态信息，这能减轻服务器的负担。但它在完成有
关联性的操作时会⾮常麻烦。

**4、明文传输**

传输过程中的信息，是可⽅便阅读的，通过浏览器的F12控制台或Wireshark抓包都可以直接⾁眼查看为我们调试⼯作带来了极⼤的便利性，但信息透明，容易被窃取。 

**5、不安全**

1. 通信使⽤明⽂（不加密），内容可能被窃听
2. 不验证通信⽅的身份，因此有可能遭遇伪装
3. ⽆法证明报⽂的完整性，所以有可能已遭篡改



### HTTP请求头有哪些东西？状态码？

**请求头**：**包含请求的附加信息，有key：value组成**

HTTP头字段（HTTP header fields），是指在超文本传输协议（HTTP）的请求和响应消息中的消息头部分

它们定义了一个超文本传输协议事务中的操作参数

**Accept**：能够接受的回应内容类型（Content-Types）

示例：Accept: text/plain

**Host**：服务器的域名(用于虚拟主机 )，以及服务器所监听的传输控制协议端口号

示例：Host: en.wikipedia.org:80                    Host: en.wikipedia.org

**User-Agent**：浏览器的浏览器身份标识字符串

示例：User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:12.0) Gecko/20100101 Firefox/21.0

```java
//示例
Host: developer.mozilla.org
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:50.0) Gecko/20100101 Firefox/50.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9;

```



**状态码**

<img src="E:\2021java\Typora\typora-user-images\image-20230102022410246.png" alt="image-20230102022410246" style="zoom:67%;" />

常⻅状态码以及描述
200：客户端请求成功 
206：partial content 服务器已经正确处理部分GET请求，实现断点续传或同时分⽚下载，该请求必须包含Range
请求头来指示客户端期望得到的范围 
301（永久重定向）：该资源已被永久移动到新位置，将来任何对该资源的访问都要使⽤本响应返回的若⼲个URL
之⼀ 
302（临时重定向）：请求的资源现在临时从不同的URI中获得 
304：如果客户端发送⼀个待条件的GET请求并且该请求以经被允许，⽽⽂档内容未被改变，则返回304,该响应不
包含包体（即可直接使⽤缓存） 
400：请求报⽂语法有误，服务器⽆法识别 
401：请求需要认证 
403：请求的对应资源禁⽌被访问 
404：服务器⽆法找到对应资源 
500：服务器内部错误



### HTTPS？怎么实现加密的？CA证书怎么认证的？

**HTTPS**：

HTTPS 解决HTTP 不安全的缺陷，在 TCP 和 HTTP ⽹络层之间加⼊了 **SSL/TLS 安全协议**，使得报⽂能够加密传
输。

HTTPS 在 TCP 三次握⼿之后，还需进⾏ SSL/TLS 的握⼿过程，才可进⼊加密报⽂传输。

HTTP 的端⼝号是 80，HTTPS 的端⼝号是 443。

HTTPS 协议需要向 CA（证书权威机构）申请数字证书，来保证服务器的身份是可信的。

**特点**

1. 信息加密：交互信息⽆法被窃取
2. 校验机制：⽆法篡改通信内容，篡改了就不能正常显示
3. 身份证书：证明报⽂的完整

**优点**

1. 在数据传输过程中，使⽤秘钥加密，安全性更⾼
2. 可认证⽤户和服务器，确保数据发送到正确的⽤户和服务器

**缺点**

1. 握⼿阶段延时较⾼：在会话前还需进⾏SSL握⼿
2. 部署成本⾼：需要购买CA证书；需要加解密计算，占⽤CPU资源，需要服务器配置或数⽬⾼



**如何实现加密**（HTTPS采用的是混合加密）

1、对称加密

只使⽤⼀个密钥，运算速度快，密钥必须保密，⽆法做到安全的密钥交换;

**密钥只有⼀个，加密解密为同⼀个密码，且加解密速度快，典型的对称加密算法有DES、AES等；**

2、非对称加密

使⽤两个密钥，公钥可以任意分发⽽私钥保密，解决密钥交换问题，但速度慢。

密钥成对出现（且根据公钥⽆法推知私钥，根据私钥也⽆法推知公钥），加密解密使⽤不同密钥（公钥加密需要私钥解密，私钥加密需要公钥解密），相对对称加密速度较慢，典型的⾮对称加密算法有**RSA、DSA**等。

3、混合加密

实现信息的机密性，解决窃听⻛险; 

**HTTPS采⽤对称加密和⾮对称加密结合的[混合加密]⽅式。**

**所有传输的内容都经过加密，加密采⽤对称加密，但对称加密的密钥⽤服务器⽅的证书进⾏了⾮对称加密。**



**CA证书认证/HTTPS验证过程**

用非对称加密的方式（数字证书）去加密“对称加密的秘钥”，对称加密的秘钥负责加密传输内容

1、客户端发出https请求，连接443端口
2、服务器准备好自己的私钥和公钥证书
3、服务器将自己的**数组证书**发给客户端（证书包含私钥公钥、⽹站地址、证书颁发机构、失效⽇期等）
4、客户端验证证书是否来自服务器，并产生随机的会话密钥，然后**使用服务器的公钥加密**
5、客户端将会话密钥的密文发给服务器
6、服务器使用自己的私钥解密，得到会话密钥，并**使用会话密钥对需要传输的数据加密**
7、服务器将数据的密文发给客户端
8、客户端使用会话密钥解密，得到数据



**CA证书如何做到**

1、证书管理机构CA在设置好所有信息后，对证书首先使用指纹算法（即进行一次hash），然后使用签名算法（使用CA自己的私钥加密）对hash作签名，并把签名和证书一起颁发给服务器。

2、当浏览器请求时，服务器需要将证书和签名一起发给浏览器。

3、浏览器收到证书以后，使用权威机构的公钥解密签名，并将证书进行hash，并且将两者信息对比，如果相同便可以证明证书信息是没有被修改的。

​		其他的中间人如果进行伪造并篡改签名，因为证书是使用权威机构的私钥生成的，浏览器使用权威机构的公钥解密后和信息不匹配，从而不会信任该服务器。
​		如果第三方同样获取到了权威机构颁发的证书和签名，他可能进行重放攻击，但是解密后的信息和证书的信息也不会匹配。
​		**hash的作用是为了保证证书信息的完整性。在上面在签名之前有一个hash的操作，看上去不进行hash同样可以实现。这里的hash与加密的开销有很大关系，因为非对称加密是很耗时的，对hash后的数据进行加密会大大减少加密开销。至于安全性还有待研究**
​		那这个权威机构的公钥怎么来的呢，我们上面说的都是为了证明服务器的公钥确实是该服务器的公钥吧，同样道理，该权威机构的公钥也可以通过其父权威机构来证明它的可信性。而当我们安装操作系统时，已经内嵌了可信的证书发布机构。



## 笔试

### 反转每对括号间的子串

1190mid 反转每对括号间的子串

【预处理括号，时间复杂度为O(n)】

```java
class Solution {
    public String reverseParentheses(String s) {
        Deque<Integer> stack = new ArrayDeque<>();
        int len = s.length();
        int[] pair = new int[len]; //构建数组存储括号下标映射
        for(int i = 0; i < len; i++) {
            if(s.charAt(i) == '(') {
                stack.push(i);
            }
            if(s.charAt(i) == ')') {
                int j = stack.pop();
                pair[i] = j;
                pair[j] = i;
            }
        }
        StringBuilder sb = new StringBuilder();
        int index = 0;
        int dir = 1;//初始化指针遍历方向，向右遍历
        while(index < len) {
            if(s.charAt(index) == '(' || s.charAt(index) == ')') { //遇到括号
                index = pair[index]; //指针跳变到对应的括号位置
                dir = -dir; //改变指针遍历方向
            } else {
                sb.append(s.charAt(index));
            }
            index += dir;
        }
        return sb.toString();
    }
}
```

【栈遍历，时间复杂度为O(n2)】

```java
class Solution {
    public String reverseParentheses(String s) {
        Deque<String> stack = new ArrayDeque<>();
        StringBuilder sb = new StringBuilder();
        char[] ch = s.toCharArray();
        for(char c : ch) {
            if(c == '(') { //遇到'('
                stack.push(sb.toString()); //存到的字符串压入栈
                sb.delete(0, sb.length()); //再清空字符串变量
            } else if(c == ')') { //遇到')'
                sb.reverse(); //将现在字符串变量反转
                sb.insert(0, stack.pop()); //再弹出栈顶元素插在字符串前面
            } else {
                sb.append(c); //都不是，就将字符存入字符串sb
            }
        }
        return sb.toString();
    }
}
```



# 航天宏图（准备）

## 怎么理解多线程

1. 线程与进程的对比、现在互联网时代高并发
2. 多线程的定义
3. 提高CPU利用率，单核、多核对比
4. 可能带来的问题：内存泄漏、死锁、线程不安全



## 代码冲突解决

merge



## JAVA的集合用过哪些

HashMap()、ArrayList()、LinkedList()



## hashset怎么判断不重复

延伸一下到HashMap的底层

1. 底层基于HashMap，其add()方法只是简单调用了HashMap的put()方法。
2. 计算对象的hashcode值来判断对象加入的位置，同时也会与其他加入的对象的 hashcode 值作比较，如果没有相符的 hashcode，HashSet 会假设对象没有重复出现。
3. 但是如果发现有相同 hashcode 值的对象，这时会调用equals()方法来检查 hashcode 相等的对象是否真的相同。如果两者相同，HashSet 就不会让加入操作成功。



## 哪个不重复自带排序的集合

treeset：(有序，唯一): 红黑树(自平衡的排序二叉树)



## HashMap的底层结构

1. JDK1.8以前：数组+链表，即链表散列；JDK1.8以后：数组+链表+红黑树
2. 扩容机制：默认初始容量为16，以后扩展为2倍。容量总是2的幂次方。
3. 哈希冲突解决：拉链法、扰动函数



## 字符串拼接有哪几种方式，for循环的时候选择那种方式拼接

1. String、StringBuilder、StringBuffer

2. 三者的区别：String不可变，其余可变

3. 线程安全、效率

4. for循环选择StringBuilder或StringBuffer。因为在循环过程中，如果用String，字符串对象通过“+”的字符串拼接方式，实际上是通过 StringBuilder 调用 append() 方法实现的，拼接完成之后调用 toString() 得到一个 String 对象 。 在循环内使用“+”进行字符串的拼接的话，存在比较明显的缺陷：编译器不会创建单个 StringBuilder 以复用，会导致创建过多的 StringBuilder 对象。

   



## 抽象类和接口的区别

1. 抽象类是被继承的，而接口是被实现的，两者使用的关键字不同。一个是extends，一个是implement。
2. 一个类只能继承一个抽象类，但可以实现多个接口。
3. 接口中的成员变量只能是 public static final 类型的，不能被修改且必须有初始值，而抽象类的成员变量默认 default，可在子类中被重新定义，也可被重新赋值。
4. 抽象类属于类，有构造方法；而接口属于方法的集合，不是类，没有构造方法。
5. 抽象类重点在于封装对象的属性（面向属性），接口的重地在于方法的实现（面向行为）。



## 面向对象三特性

封装、继承、多态

1. 封装：属性私有化、提供外界访问方法
2. 继承：子类对父类属性的继承，可以拥有父类所有的属性和方法，但是对于私有属性和方法不能访问。可以对父类方法进行重写，可以添加新的成员变量。
3. 多态：通过引用变量调用的方法，要在程序运行时才能决定。有可能调用父类的方法，也有可能调用子类的方法。
4. 多态不能调用子类有而父类没有的方法。



## 重写和重载的区别

1. 重写：继承父类，方法名、参数、返回类型都一样，就是方法体不一样。
2. 重载：一个方法的多个实现，比如多个构造方法。方法名一定相同，参数列表、返回值类型、修饰符可以不同。



## 良好的编程习惯有哪些

1. 类的命名，大写
2. 方法的命名，驼峰
3. 多查阅、多思考



## 场景题

十个业务对应十个情况，不用switch case ，保证可扩展性，用什么方式实现（反射）？

1. 利用反射 

   ```java
   public static Validator newInstance(String validatorClass) {
                   return Class.forName(validatorClass).newInstance();             
   }
   ```

 2. 提前将对象根据相应的key-object放入map

    ```java
    Map<String, Validator> validators =  new HashMap<String,Validator>(){
                    {
                            put("INT",new IntValidator());
                            put("LOOKUPVALUE",new LookupValueValidator());
                            put("DATE",new DateValidator());
                            put("STRINGPATTERN",new StringPatternValidator());
                    }
            };
            public Validator newInstance(String validatorType) {
                    return validators.get(validatorType);
            }
    ```

3. 根据不同的枚举类型值调用newInstance创建出不同的对象

   ```java
   enum ValidatorType {
           INT {
                   public Validator create() {
                           return new IntValidator();
                   }
           },
           LOOKUPVALUE {
                   public Validator create() {
                           return new LookupValueValidator();
                   }
           },
           DATE {
                   public Validator create() {
                           return new DateValidator();
                   }
           };
           public Validator create() {
                   return null;
           }
   }
   
   
   
   public Validator newInstance(ValidatorType validatorType) {
           return validatorType.create();
   }
   ```

## aop是什么，怎么在方法中面向切面编程

1. AOP：面向切面编程，aspect oriented programming。
2. AOP作用：封装共同的逻辑，比如权限控制、日志信息、事务处理等
3. AOP实现：JDK proxy或Cglib



## mysql存储引擎

1. MyIsam和InnoDB。MySQL 5.5.5 之前，MyISAM 是 MySQL 的默认存储引擎。5.5.5 版本之后，InnoDB 是 MySQL 的默认存储引擎。
2. 存储引擎结构：插件式架构，可自定义存储引擎适配不同的表。存储引擎是基于表的，而不是数据库。
3. MyIsam和InnoDB的区别：行级锁、事务支持、外键、数据崩溃恢复、MVCC、索引实现



## mysql存储过程

我们可以把存储过程看成是一些 SQL 语句的集合，中间加了点逻辑控制语句。存储过程在业务比较复杂的时候是非常实用的，比如很多时候我们完成一个操作可能需要写一大串 SQL 语句，这时候我们就可以写有一个存储过程，这样也方便了我们下一次的调用。存储过程一旦调试完成通过后就能稳定运行，另外，使用存储过程比单纯 SQL 语句执行要快，因为存储过程是预编译过的。存储过程在互联网公司应用不多，因为存储过程难以调试和扩展，而且没有移植性，还会消耗数据库资源。



存储过程就是事先经过编译并存储在数据库中的一段 SQL 语句的集合；

原因：调用存储过程可以简化应用开发人员的很多工作，减少数据在数据库和应用服务器之间的传输，对于提高数据处理的效率是有好处的。存储过程思想上很简单，就是数据库 SQL 语言层面的代码封装与重用。



## 左连接和右连接区别

![img](E:\2021java\Typora\typora-user-images\4224ef5f79884bf9bd282b29d04bc0c6.png)

![img](E:\2021java\Typora\typora-user-images\afff43b92f994c29bd118bfc19352cf9-16738841725783.png)

left join结果

<img src="E:\2021java\Typora\typora-user-images\image-20230116235112233.png" alt="image-20230116235112233" style="zoom:67%;" />

right join结果

<img src="E:\2021java\Typora\typora-user-images\image-20230116235136783.png" alt="image-20230116235136783" style="zoom:67%;" />

join/inner join结果

<img src="E:\2021java\Typora\typora-user-images\image-20230116235212842.png" alt="image-20230116235212842" style="zoom:67%;" />



## synchronized和重入锁区别，什么是锁

1. synchronized修饰实例方法、静态方法、代码块，保证资源访问的唯一性
2. 底层：修饰代码块：monitorenter、monitorexit，计数器。修饰方法：ACC_SYNCHRONIZED 标识，该标识指明了该方法是一个同步方法。



# 阿里云一面（准备）

## springcloud及其组件

SpringCloud分为SpringCloud Netflix 和SpringCloud Alibaba

![image-20230203005340689](E:\2021java\Typora\typora-user-images\image-20230203005340689.png)

![image-20230203010947926](E:\2021java\Typora\typora-user-images\image-20230203010947926.png)

以SpringCloud Netflix为例

![image-20230203011732341](E:\2021java\Typora\typora-user-images\image-20230203011732341.png)

![image-20230203011852549](E:\2021java\Typora\typora-user-images\image-20230203011852549.png)

![image-20230203012026053](E:\2021java\Typora\typora-user-images\image-20230203012026053.png)



## 网关

https://blog.csdn.net/cnm10050/article/details/127261680

gateway 本质就是一个 Springboot 应用， 他是通过 **webflux** 框架处理请求和响应，而 webflux 底层是基于 Netty。

以gateway为例：

1. 当 请求方 发送一个请求到达 gateway 时，gateway 根据 配置的**路由规则**，找到 对应的**服务**名称。
2. 当某个服务存在**多个实例**时，gateway 会根据 **负载均衡**算法（比如：轮询）从中挑**选出一个实例**，然后**将请求 转发 过去**。
3. 服务实例返回的的响应结果会再经过 gateway 转发给请求方。

官网解释流程：

1. 客户端向 Spring Cloud Gateway 发出请求。
2. 如果 **Gateway Handler Mapping** 找到与请求相匹配的路由，将其发送到 Gateway Web Handler。
3. **Handler** 再通过指定的 过滤器链**Filter Chain** 来将请求发送到我们实际的服务执行业务逻辑，然后返回。
4. 过滤器之间用虚线分开是因为过滤器可能会在发送代理请求之前（“pre”）或之后（“post”）执行业务逻辑。



## nacos服务注册与发现

1. 服务注册：Nacos Client会通过**发送REST请求的方式向Nacos Server注册自己的服务**，提供自身的元数据，比如ip地址、端口等信息。 Nacos Server接收到注册请求后，就会把这些元数据信息存储在一个双层的内存Map中。
2. 服务心跳：在服务注册后，Nacos Client会维护一个定时心跳来持续通知Nacos Server，说明服务一直处于可用状态，防止被剔除。默认 5s发送一次心跳。
3. 服务同步：Nacos Server集群之间会互相同步服务实例，用来保证服务信息的一致性。
4. 服务发现：服务消费者（Nacos Client）在调用服务提供者的服务时，会**发送一个REST请求给Nacos Server**，获取上面注册的服务清 单，并且缓存在Nacos Client本地，同时会在Nacos Client本地开启一个定时任务定时拉取服务端最新的注册表信息更新到本地缓存
5. 服务健康检查：Nacos Server会开启一个定时任务用来检查注册服务实例的健康情况，对于超过15s没有收到客户端心跳的实例会将它的 healthy属性置为false(客户端服务发现时不会发现)，如果某个实例超过30秒没有收到心跳，直接剔除该实例(被剔除的实例如果恢复发送 心跳则会重新注册)



## rpc调用细节

![RPC原理图](E:\2021java\Typora\typora-user-images\37345851.jpg)



1. 服务消费端（client）以本地调用的方式调用远程服务；
2. 客户端 Stub（client stub） 接收到调用后负责将方法、参数等组装成能够进行网络传输的消息体（序列化）：`RpcRequest`；
3. 客户端 Stub（client stub） 找到远程服务的地址，并将消息发送到服务提供端；
4. 服务端 Stub（桩）收到消息将消息反序列化为Java对象: `RpcRequest`；
5. 服务端 Stub（桩）根据`RpcRequest`中的类、方法、方法参数等信息调用本地的方法；
6. 服务端 Stub（桩）得到方法执行结果并将组装成能够进行网络传输的消息体：`RpcResponse`（序列化）发送至消费方；
7. 客户端 Stub（client stub）接收到消息并将消息反序列化为Java对象:`RpcResponse` ，这样也就得到了最终结果。over!



## 负载均衡

客户端负载均衡是将负载均衡逻辑以代码的形式封装到客户端上，即负载均衡器位于客户端。客户端通过服务注册中心（例如 Eureka Nacos ）获取到一份服务端提供的可用服务清单。有了服务清单后，负载均衡器会在客户端发送请求前通过负载均衡算法选择一个服务端实例再进行访问，以达到负载均衡的目的



负载均衡的方式：

![image-20230204020615594](E:\2021java\Typora\typora-user-images\image-20230204020615594.png)

![image-20230204020645448](E:\2021java\Typora\typora-user-images\image-20230204020645448.png)

加权轮询算法使用场景是建立在每个节点存储的数据都是相同的前提。所以，每次读数据的请求，访问任意一个节点都能得到结果。

5的源地址哈希法，对于后端服务器列表不变的情况、要求使用共享sessionID的情况，可行。



## 一致性哈希

https://blog.csdn.net/wdj_yyds/article/details/124710751

针对传统的负载均衡算法，一致性哈希算法就很好地解决了分布式系统在扩容或者缩容时，发生过多的数据迁移的问题。

但一致性哈希算法可能会导致节点分布不均匀，导致大量请求可能同时压在一个节点上，当节点发生崩溃时，压力又瞬间转移到下一个节点，导致雪崩式的节点崩溃。为了解决这个问题，引入了虚拟节点。



## 算法-K 个一组翻转链表



## 数组、链表、队列、栈有什么区别

1. 数组的特性：长度一定
2. 链表：ArrayList和LinkedList，底层，扩容
3. 队列：单端队列、双端队列
4. 栈：后进先出



## 索引有哪几种类型

主键索引与辅助索引

1. 主键索引（InnoDB的主键索引就是聚簇索引）
2. 辅助索引（二级索引/非聚簇索引）：唯一索引、普通索引、前缀索引、全文索引



聚簇索引与非聚簇索引

1. 聚簇索引
2. 非聚簇索引



## BTREE索引底层结构

B树结构、B+树结构



## 索引什么时候会失效

1. 使用 SELECT * 进行查询;
2. 使用like查询%开头的字段，比如 LIKE ‘%abc’
3. 使用了联合索引，但没有满足最左前缀准则
4. 在索引列上计算、函数、类型转换等操作
5. 使用or的时候，且 or 的前后条件中有一个列没有索引，涉及的索引都不会被使用到
6. 隐式转换



## 平时遇到什么技术上或者非技术上的难题

映射文件中的where标签可以过滤掉条件语句中的第一个and或or关键字。



## 静态类，实例类区别 

1. 静态类只能是静态内部类，不能作为顶层类

2. 静态内部类，可以直接通过外部类调用内部类进行实例化；

   非静态内部类，要先声明外部类对象再调用内部类进行实例化

3. 静态内部类只能访问外部类的静态成员，不能访问非静态成员；

   非静态内部类既可以访问外部类的静态成员，也可以访问非静态成员

4. 静态内部类可以声明普通成员变量和方法，而普通内部类不能声明static成员变量和方法

```java
package JavaSE;

/**
 * @author ck
 * @create 2023-02-04  15:56
 */
public class StaticClassDemo {

    public static void main(String[] args) {
        //静态内部类，可以直接通过外部类调用内部类进行实例化
        OuterClass.InnerStaticClass innerStaticClass = new OuterClass.InnerStaticClass();
        innerStaticClass.staticSay();

        //非静态内部类，要先声明外部类对象再调用内部类进行实例化
        OuterClass.InnerClass innerClass = new OuterClass().new InnerClass();
        innerClass.say();
    }
}

class OuterClass {
    private static String staticMsg = "静态变量";
    private String nonStaticMsg = "非静态变量";

    //静态内部类
    public static class InnerStaticClass {
        //内部类只有静态的才可以声明静态变量
        private static String staticText = "I am static Inner private";
        public void staticSay() {
            System.out.println("静态内部类打印：" + OuterClass.InnerStaticClass.class);
            System.out.println(new OuterClass().nonStaticMsg); //要新建外部类对象才能访问非静态变量
            System.out.println("静态内部类打印：" + staticMsg);//可以直接调用静态变量
            System.out.println(OuterClass.staticMsg); //可以通过外部类直接调用静态变量
//            System.out.println(OuterClass.nonStaticMsg); //不可直接访问
        }
    }

    //普通内部类
    public class InnerClass {
        //普通内部类不能声明static成员变量和方法
//        private static String staticText = "I am static Inner private"; 
        public void say() {
            System.out.println("-----------内部类打印：" + OuterClass.InnerClass.class);
            System.out.println("-----------内部类打印：" + staticMsg);//可以直接调用静态变量
            System.out.println(OuterClass.staticMsg); //可以通过外部类直接调用静态变量
            System.out.println("-----------内部类打印：" + nonStaticMsg);
            System.out.println(new OuterClass().nonStaticMsg); //要新建外部类对象才能访问非静态变量
//            System.out.println(OuterClass.nonStaticMsg); //不可直接访问
        }
    }
}

```



## equals和==区别

1. ==：对基本数据类型，比较的是值；对引用类型，比较的是对象的内存地址
2. equals：对基本数据类型不可用；对引用类型，用来判断两个对象是否相等；
   1. 如果equals没有重写：比较的就是对象地址
   2. 如果equals重写：如果重写比较的是属性值，那就相当于比较对象的内容/属性是否相等
3. String类型的equals被重写了



## ThreadLocal

https://blog.csdn.net/upstream480/article/details/120310476

1. ThreadLocal是Java提供的线程本地存储机制，可以在一个线程内存储线程私有的数据副本。

2. 底层基于ThreadLocalMap实现，每个Thread对象（注意不是ThreadLocal对象）都有一个ThreadLocalMap；key为当前的ThreadLocal对象，value为线程需要缓存的值。

3. 在线程池使用ThreadLocal会造成内存泄漏，因为当使用完ThreadLocal对象之后，应该对key和value进行回收，即对Entry对象进行回收，但线程池中的线程不会回收，线程对象通过强引用指向ThreadLocalMap，ThreadLocalMap又通过强引用指向Entry对象。因此线程不回收，导致Entry对象不回收，导致内存泄漏。

   解决办法：线程使用完ThreadLocal后手动调用remove()方法，清除Entry对象。



如果 ThreadLocal 没有被外部强引用的情况下，在垃圾回收的时候，key 会被清理掉，而 value 不会被清理掉。有可能出现key为null的entry，产生内存泄漏。



## JVM模型

介绍一下JVM

JVM由JVM运行时数据区、执行引擎、本地库接口、本地方法库组成；

其中，运行时数据区就是我们常说的JVM内存模型，由堆、方法区、程序计数器、虚拟机栈、本地方法栈组成



JVM内存模型

1. 线程共享：堆、方法区；线程私有：程序计数器、虚拟机栈、本地方法栈

2. 堆：是内存区域最大的一块，主要负责存放java程序运行过程中创建的对象，以及在其中进行对象的垃圾回收

3. 方法区：存放java的方法；如果是类的静态方法，则属于类；如果是非静态方法，则属于对象

   当虚拟机要使用一个类时，要读取Class文件，并将信息存入方法区。方法区存的内容包括：类信息、字段信息、方法信息、常量、静态变量、即时编译器编译后缓存的代码数据

4. 程序计数器：

   1. 字节码解释器通过改变程序计数器来读取指令，负责代码的流程控制，包括顺序执行、选择、循环、异常处理等
   2. 负责记录线程运行到什么地方，等切换回来的时候可以从记录的地方开始

5. 虚拟机栈：就是经常说的java的栈，主要存放局部变量表、操作数栈、动态链接、方法出口信息等

   方法调用：虚拟机栈栈帧的压入与弹出

   局部变量表存放了编译时期可知的基本数据类型（boolean、byte、char、short、int、long、float、double）和对象引用

6. 本地方法栈：和虚拟机栈类似，执行的是操作系统的native方法



## 排序算法

快排、冒泡排序、归并排序、堆排序都属于比较类排序算法



## 线程和进程的区别

1. 进程是程序运行的过程，是系统运行程序的基本单位，一个程序运行就是进程从创建、运行到消亡的过程。
2. 线程和进程类似，但线程是进程的更小单位；一个进程可以分为多个线程，线程之间共享进程的堆和方法区，但每个线程都有自己的程序计数器、虚拟机栈、本地方法栈。
3. 进程之间基本是独立的，而线程之间很可能相互影响；线程是更轻量级的进程，执行开销小，但不利于资源管理和保护，进程正好相反。



## 线程同步

线程同步是两个或多个共享关键资源的线程的并发执行。应该同步线程以避免关键的资源使用冲突。操作系统一般有下面三种线程同步的方式：

1. 互斥量(Mutex)：采用互斥对象机制，只有拥有互斥对象的线程才有访问公共资源的权限。因为互斥对象只有一个，所以可以保证公共资源不会被多个线程同时访问。比如 Java 中的 synchronized 关键词和各种 Lock 都是这种机制。
2. 信号量(Semaphore) ：它允许同一时刻多个线程访问同一资源，但是需要控制同一时刻访问此资源的最大线程数量。
3. 事件(Event) :Wait/Notify：通过通知操作的方式来保持多线程同步，还可以方便的实现多线程优先级的比较操作。



## 死锁

1. 死锁是什么：多个线程被阻塞，他们都在等待一个或多个资源被释放；由于线程之间相互握有别的线程需要的资源而不释放，导致线程长时间阻塞
2. 必要条件：互斥条件、请求与保持条件、不剥夺条件、循环等待条件
3. 避免死锁：破坏循环等待条件，让各个线程申请资源的顺序一直



## ACID

1. 数据库事务的四大特性：原子性、一致性、隔离性、持久性
   1. 原子性：事务是最小的执行单位，事务要完成就要确保所有动作都完成，否则全部不生效
   2. 一致性：事务提交前后，数据应该保持一致；比如转账业务，无论事务是否成功，总额应该不变
   3. 隔离性：各并发事务并发访问数据库时，数据库应该相互独立
   4. 事务一旦提交，对数据库数据的更改应该是持久化的，即使数据库发生故障，也不影响数据更改
2. 要保证原子性、隔离性、持久性，目的是为了实现一致性



## 索引设计



## 最左匹配原则



## 交换机路由器异同点

https://blog.csdn.net/daidadeguaiguai/article/details/108635486

路由谋短，交换求快

1. 工作层次不同：交换机工作在数据链路层，路由器工作在网络层
2. 转发依据不同：交换机转发依据的对象是MAC地址（物理地址），路由器转发依据的对象是IP地址（网络地址）
3. 主要功能不同：交换机用于组件局域网；路由器用于把组建好的局域网相互连接起来，或者接入Internet



## http https区别

1. http概念
2. https在http基础上加入SSL/TLS安全协议
3. https验证、CA证书验证



## cookie session



## http 请求报文 响应报文



# 阿里云二面(准备)

## 项目

## Java OOM

**定义**

OOM，全称“Out Of Memory”，翻译成中文就是“内存用完了”,当JVM因为没有足够的内存来为对象分配空间并且垃圾回收器也已经没有空间可回收时，就会抛出这个error。

按照JVM规范，**除了程序计数器**不会抛出OOM外，其他各个内存区域都可能会抛出OOM。



**出现原因**

1. 内存分配的少了：比如虚拟机本身可使用的内存（一般通过启动时的VM参数指定）太少。

2. 应用用的太多，并且用完没释放，浪费了。此时就会造成**内存泄露**或者**内存溢出**。

   **内存泄露**：申请使用完的内存没有释放，导致虚拟机不能再次使用该内存，此时这段内存就泄露了，因为申请者不用了，而又不能被虚拟机分配给别人用。

   **内存溢出**：申请的内存超出了JVM能提供的内存大小，此时称之为溢出。



**最常见的OOM情况**

1. java.lang.OutOfMemoryError: Java heap space ------>**java堆内存溢出**，此种情况最常见，一般由于内存泄露或者堆的大小设置不当引起。对于内存泄露，需要通过内存监控软件查找程序中的泄露代码，而堆大小可以通过虚拟机参数-Xms,-Xmx等修改。
2. java.lang.OutOfMemoryError: PermGen space ------>**java永久代溢出**，即**方法区溢出**了，一般出现于大量Class或者jsp页面，或者采用cglib等反射机制的情况，因为上述情况会产生大量的Class信息存储于方法区。此种情况可以通过更改方法区的大小来解决，使用类似-XX:PermSize=64m -XX:MaxPermSize=256m的形式修改。另外，过多的常量尤其是字符串也会导致方法区溢出。
3. java.lang.StackOverflowError ------> 不会抛OOM error，但也是比较常见的Java内存溢出。**JAVA虚拟机栈溢出**，一般是由于程序中存在死循环或者深度递归调用造成的，栈大小设置太小也会出现此种溢出。可以通过虚拟机参数-Xss来设置栈的大小。



## 算法

88easy 合并两个有序数组

https://leetcode.cn/problems/merge-sorted-array/



215mid 数组中的第K个最大元素

https://leetcode.cn/problems/kth-largest-element-in-an-array/



## 红黑树

https://blog.csdn.net/yy12345_6_/article/details/122928973

**5大性质**

1. 结点是红色或者黑色。
2. 根结点是黑色。
3. 所有的叶子结点都是黑色。（叶子是NIL结点）
4. 每个红色结点的两个子节点都是黑色。（即不可能出现两个相邻的红色结点）
5. 从任一结点到每个叶子的所有路劲都包含相同数目的黑色结点

这五个约束条件保证了红⿊树的新增、删除、查找的最坏时间复杂度均为 O(logn)。如果⼀个树的左⼦节点或右⼦节点不存在，则均认定为⿊⾊。红⿊树的任何旋转在 3 次之内均可完成。



**应用场景**

java集合框架中的：TreeMap、TreeSet底层使用的就是红黑树

TreeMap可以对集合中的元素根据键进行排序



linux内核：进程调度中使用红黑树管理进程控制块，epoll在内核中实现时使用红黑树管理事件块

HashMap：

1. JDK1.7以前，数组+链表，拉链法解决冲突，头插法
2. JDK1.8以后，数组+链表+红黑树，判断扩容数组还是将链表转化为红黑树，尾插法（便利到最后直接插入，比头插法效率高）

TreeMap、TreeSet 以及 JDK1.8 之后的 HashMap 底层都用到了红黑树。**红黑树就是为了解决二叉查找树的缺陷，因为二叉查找树在某些情况下会退化成一个线性结构。**







## HashMap特征、安全性

1. 底层

2. 线程不安全：

   https://blog.csdn.net/qq_35958391/article/details/125015642

   Java7中头插法扩容会导致死循环和数据丢失，Java8中将头插法改为尾插法后死循环和数据丢失已经得到解决，但仍然有数据覆盖的问题。



## ConcurrentHashMap 

ConcurrentHashMap 1.7

Segment 数组 + HashEntry 数组 + 链表

1. 底层：

   ![Java 7 ConcurrentHashMap 存储结构](E:\2021java\Typora\typora-user-images\java7_concurrenthashmap.png)

   Java 7 中 ConcurrentHashMap 的存储结构如上图，ConcurrnetHashMap 由很多个 Segment 组合，而每一个 Segment 是一个类似于 HashMap 的结构，所以每一个 HashMap 的内部可以进行扩容。但是 Segment 的个数一旦初始化就不能改变，默认 Segment 的个数是 16 个，你也可以认为 ConcurrentHashMap 默认支持最多 16 个线程并发。

2. 初始化segment

   1. 检查计算得到的位置的 Segment 是否为null.
   2. 为 null 继续初始化，使用 Segment[0] 的容量和负载因子创建一个 HashEntry 数组。
   3. 再次检查计算得到的指定位置的 Segment 是否为null.
   4. 使用创建的 HashEntry 数组初始化这个 Segment.
   5. 自旋判断计算得到的指定位置的 Segment 是否为null，使用 CAS 在这个位置赋值为 Segment

3. put

   由于 Segment 继承了 ReentrantLock，所以 Segment 内部可以很方便的获取锁，put 流程就用到了这个功能。

   1. tryLock() 获取锁，获取不到使用 scanAndLockForPut 方法继续获取。

   2. 计算 put 的数据要放入的 index 位置，然后获取这个位置上的 HashEntry 。

   3. 遍历 put 新元素，为什么要遍历？因为这里获取的 HashEntry 可能是一个空元素，也可能是链表已存在，所以要区别对待。

      如果这个位置上的 HashEntry 不存在：

      1. 如果当前容量大于扩容阀值，小于最大容量，进行扩容
      2. 直接头插法插入

      如果这个位置上的 HashEntry 存在：

      1. 判断链表当前元素 key 和 hash 值是否和要 put 的 key 和 hash 值一致。一致则替换值
      2. 不一致，获取链表下一个节点，直到发现相同进行值替换，或者链表表里完毕没有相同的。
         1.  如果当前容量大于扩容阀值，小于最大容量，进行扩容。
         2. 直接链表头插法插入。

   4. 如果要插入的位置之前已经存在，替换后返回旧值，否则返回 null.

4. get



ConcurrentHashMap 1.8

Node 数组 + 链表 / 红黑树

1. 底层

   <img src="E:\2021java\Typora\typora-user-images\java8_concurrenthashmap.png" alt="Java8 ConcurrentHashMap 存储结构（图片来自 javadoop）" style="zoom:67%;" />

   Java8 的 ConcurrentHashMap 相对于 Java7 来说变化比较大，不再是之前的 Segment 数组 + HashEntry 数组 + 链表，而是 Node 数组 + 链表 / 红黑树。当冲突链表达到一定长度时，链表会转换成红黑树。

2. 初始化 initTable

3. put

   1. 根据 key 计算出 hashcode 。
   2. 判断是否需要进行初始化。
   3. 即为当前 key 定位出的 Node，如果为空表示当前位置可以写入数据，利用 CAS 尝试写入，失败则自旋保证成功。
   4. 如果当前位置的 hashcode == MOVED == -1,则需要进行扩容。
   5. 如果都不满足，则利用 synchronized 锁写入数据。
   6. 如果数量大于 TREEIFY_THRESHOLD 则要执行树化方法，在 treeifyBin 中会首先判断当前数组长度≥64时才会将链表转换为红黑树。

4. get



## 红黑树是平衡的吗

不是想二叉平衡搜索树一样严格平衡，但在添加元素的过程中通过变色和旋转保持平衡



##  创建线程

1. 线程池：

   使用 ThreadPoolExecutor 构造函数创建线程池（推荐使用）

   通过 Executor 框架的工具类 Executors 来实现 我们可以创建三种类型的 ThreadPoolExecutor FixedThreadPool、SingleThreadExecutor、CachedThreadPool

2. 使用线程池的原因/好处

   1. 降低资源消耗。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
   2. 提高响应速度。当任务到达时，任务可以不需要等到线程创建就能立即执行。
   3. 提高线程的可管理性。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。

   

## 线程池管理方式

线程增多了会带来cpu调度的负载增加，cpu需要调度大量的线程，包括创建线程、销毁线程、线程是否需要换出cpu、是否需要分配到cpu。这些都是需要消耗系统资源的

1. 配置核心线程数、最大线程数、等待队列、拒绝策略、等待时间、时间单位、线程工厂
2. 通过一些手段来检测线程池的运行状态比如 SpringBoot 中的 Actuator 组件。
3. 建议是不同的业务使用不同的线程池，配置线程池的时候根据当前业务的情况对当前线程池进行配置，因为不同的业务的并发以及对资源的使用情况都不同，重心优化系统性能瓶颈相关的业务。
4. 给线程池命名
5. 正确配置线程池参数



## Java集合

List、Set、Map、Queue、Stack

Java 集合， 也叫作容器，主要是由两大接口派生而来：一个是 Collection接口，主要用于存放单一元素；另一个是 Map 接口，主要用于存放键值对。对于Collection 接口，下面又有三个主要的子接口：List、Set 和 Queue。

1. List、Set、Map、Queue四者区别

2. List：ArrayList、Vector、LinkedList，

   前两个基于Object[] 数组，LinkedList为双向链表（JDK1.6 之前为循环链表，JDK1.7 取消了循环）

3. Set

   HashSet（无序、唯一）：基于 HashMap 实现的，底层采用 HashMap 来保存元素

   LinkedHashSet：LinkedHashSet 是 HashSet 的子类，并且其内部是通过 LinkedHashMap 来实现的。有点类似于我们之前说的 LinkedHashMap 其内部是基于 HashMap 实现一样，不过还是有一点点区别的

   TreeSet（有序、唯一）：红黑树(自平衡的排序二叉树)

4. Map

   **JDK1.8之前**：HashMap 底层是 数组和链表 结合在一起使用也就是 链表散列。HashMap 通过 key 的 hashcode 经过扰动函数处理过后得到 hash 值，然后通过 (n - 1) & hash 判断当前元素存放的位置（这里的 n 指的是数组的长度），如果当前位置存在元素的话，就判断该元素与要存入的元素的 hash 值以及 key 是否相同，如果相同的话，直接覆盖，不相同就通过拉链法解决冲突。

   

   **JDK1.8 之后**：在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间。

   

5. Queue

   PriorityQueue：Object[] 数组来实现二叉堆

   ArrayQueue: Object[] 数组 + 双指针



## ArrayList的底层实现

1. ArrayList 的底层是数组队列，相当于动态数组。与 Java 中的数组相比，它的容量能动态增长。在添加大量元素前，应用程序可以使用ensureCapacity操作来增加 ArrayList 实例的容量。这可以减少递增式再分配的数量。
2. ArrayList继承于 AbstractList ，实现了 List, RandomAccess, Cloneable, java.io.Serializable 这些接口。
   1. RandomAccess 是一个标志接口，表明实现这个接口的 List 集合是支持快速随机访问的。在 ArrayList 中，我们即可以通过元素的序号快速获取元素对象，这就是快速随机访问。
   2. ArrayList 实现了 Cloneable 接口 ，即覆盖了函数clone()，能被克隆。
   3. ArrayList 实现了 java.io.Serializable接口，这意味着ArrayList支持序列化，能通过序列化去传输。



## ArrayList扩容

以无参数构造方法创建 ArrayList 时，实际上初始化赋值的是一个空数组。当真正对数组进行添加元素操作时，才真正分配容量。即向数组中添加第一个元素时，数组容量扩为 10。 



1. **add()添加第一个元素前**：数组长度为0，先调用ensureCapacityInternal(size + 1)方法，更新mincapacity = 10，再在里面调用ensureExplicitCapacity(minCapacity)方法，minCapacity表示所需长度
2. 进入ensureExplicitCapacity(minCapacity)方法，如果现有数据长度（此时未添加第一个元素，长度为0）小于所需长度minCapacity（此时为10），就调用grow(minCapacity)方法
3. 进入grow(minCapacity)方法，更新newcapacity为oldCapacity的1.5倍，如果newcapacity还是小于minCapacity，就令newcapacity = minCapacity；如果newcapacity > MAX_ARRAY_SIZE，则调用hugeCapacity(minCapacity)来比较minCapacity 和 MAX_ARRAY_SIZE（最大容量），如果minCapacity大于最大容量，则newcapacity为Integer.MAX_VALUE，否则新容量为MAX_ARRAY_SIZE 即为 Integer.MAX_VALUE - 8。
4. 由于第一次添加元素时，oldCapacity为0，1.5倍算出newcapacity为0，所以令newcapacity = minCapacity = 10；
5. 再调用Arrays.copyOf(elementData, newCapacity)，返回一个新的长度的数组(同名)，此时数组长度为10。

**添加第2,3，...，10个元素时**：因为需要的数组长度都不会超过10，所以不会进入扩容。

**添加第11个元素时**：所需要的数组长度（size + 1 = 11）大于现有数组长度10，进入扩容，使用移位法，扩容为原有长度的1.5倍左右（如果原长度是偶数，结果是1.5倍；如果是奇数，则不够1.5倍）

往后以此类推。

https://javaguide.cn/java/collection/arraylist-source-code.html



## Set怎么保证数据唯一

**不针对具体某个位置，只对比hashcode**

当你把对象加入HashSet时，HashSet 会先计算对象的hashcode值来判断对象加入的位置，同时也会与其他加入的对象的 hashcode 值作比较，如果没有相符的 hashcode，HashSet 会假设对象没有重复出现。但是如果发现有相同 hashcode 值的对象，这时会调用equals()方法来检查 hashcode 相等的对象是否真的相同。如果两者相同，HashSet 就不会让加入操作成功。



## Java的异常

受检查异常和非受检查异常（运行时异常）

1. 受检查异常： IO 相关的异常、ClassNotFoundException 、SQLException
2. 非受检查异常：
   1. NullPointerException(空指针错误)
   2. IllegalArgumentException(参数错误比如方法入参类型错误)
   3. NumberFormatException（字符串转换为数字格式错误，IllegalArgumentException的子类）
   4. ArrayIndexOutOfBoundsException（数组越界错误）
   5. ClassCastException（类型转换错误）
   6. ArithmeticException（算术错误）
   7. SecurityException （安全错误比如权限不够）
   8. UnsupportedOperationException(不支持的操作错误比如重复创建同一用户)



## ClassNotFoundException

意思就是找不到指定的class

**遇到的场景**：

1. 调用class的forName方法时，找不到指定的类
2. ClassLoader 中的 findSystemClass() 方法时，找不到指定的类
3. ClassLoader 中的 loadClass() 方法时，找不到指定的类

**解决办法**

检查类名是否正确、或是否存在该类。

**常见的场景**：
1、类依赖的class或者jar不存在
2、类文件存在，但是存在不同的域中



## TCP/IP协议（四层模型）

1. 应用层

   应用层位于传输层之上，主要提供**两个**终端设备上的**应用程序之间**信息交换的服务，它定义了信息交换的格式，消息会交给下一层传输层来传输。 我们把应用层交互的数据单元称为报文。

2. 传输层

   传输层的主要任务就是负责向**两台**终端设备**进程之间的通信**提供通用的数据传输服务。 应用进程利用该服务传送应用层报文。“通用的”是指并不针对某一个特定的网络应用，而是多种应用可以使用同一个运输层服务。

   

   运输层主要使用以下两种协议：

   **传输控制协议 TCP**（Transmisson Control Protocol）--提供 面向连接 的，可靠的 数据传输服务。

   **用户数据协议 UDP**（User Datagram Protocol）--提供 无连接 的，尽最大努力的数据传输服务（不保证数据传输的可靠性）。

   

3. 网络层

   **网络层负责为分组交换网上的不同主机提供通信服务**。 在发送数据时，网络层把运输层产生的报文段或用户数据报封装成分组和包进行传送。在 TCP/IP 体系结构中，由于网络层使用 IP 协议，因此分组也叫 IP 数据报，简称数据报。

   

   网络层的还有一个任务就是**选择合适的路由**，使源主机运输层所传下来的分组，能通过网络层中的路由器找到目的主机。

   

4. 网络接口层

   我们可以把网络接口层看作是数据链路层和物理层的合体。

   1. 数据链路层(data link layer)通常简称为链路层（ 两台主机之间的数据传输，总是在一段一段的链路上传送的）。数据链路层的作用是将网络层交下来的 IP 数据报组装成帧，**在两个相邻节点间的链路上传送帧**。每一帧包括数据和必要的控制信息（如同步信息，地址信息，差错控制等）。
   2. 物理层的作用是实现相邻计算机节点之间**比特流的透明传送**，尽可能屏蔽掉具体传输介质和物理设备的差异



<img src="E:\2021java\Typora\typora-user-images\tcp-ip-4-model.png" alt="TCP/IP 四层模型" style="zoom:67%;" />





## 滑动窗口

https://javaguide.cn/cs-basics/network/tcp-reliability-guarantee.html

TCP 利用滑动窗口实现流量控制。流量控制是为了控制发送方发送速率，保证接收方来得及接收。 接收方发送的确认报文中的窗口字段可以用来控制发送方窗口大小，从而影响发送方的发送速率。将窗口字段设置为 0，则发送方不能发送数据



**TCP 发送窗口可以划分成四个部分** 

1. 已经发送并且确认的TCP段（已经发送并确认）；
2. 已经发送但是没有确认的TCP段（已经发送未确认）；
3. 未发送但是接收方准备接收的TCP段（可以发送）；
4. 未发送并且接收方也并未准备接受的TCP段（不可发送）。

**TCP发送窗口结构图示** ：

![TCP发送窗口结构](E:\2021java\Typora\typora-user-images\tcp-send-window.png)

**TCP 接收窗口可以划分成三个部分** ：

1. 已经接收并且已经确认的 TCP 段（已经接收并确认）；
2. 等待接收且允许发送方发送 TCP 段（可以接收未确认）；
3. 不可接收且不允许发送方发送TCP段（不可接收）。

**TCP 接收窗口结构图示** ：

![TCP接收窗口结构](E:\2021java\Typora\typora-user-images\tcp-receive-window.png)

**接收窗口的大小是根据接收端处理数据的速度动态调整的。 如果接收端读取数据快，接收窗口可能会扩大。 否则，它可能会缩小。**



**为什么需要流量控制?** 这是因为双方在通信的时候，发送方的速率与接收方的速率是不一定相等，如果发送方的发送速率太快，会导致接收方处理不过来。如果接收方处理不过来的话，就只能把处理不过来的数据存在 接收缓冲区(Receiving Buffers) 里（失序的数据包也会被存放在缓存区里）。如果缓存区满了发送方还在狂发数据的话，接收方只能把收到的数据包丢掉。出现丢包问题的同时又疯狂浪费着珍贵的网络资源。因此，我们需要控制发送方的发送速率，让接收方与发送方处于一种动态平衡才好。



## 索引设计



## o(1)的复杂度下找出数组的最大数

```java
	@Test
    void test6() {
        int[] arr = new int[]{1, 2, 3, 1, 500, 45, 8};
        Stack<int[]> stack = new Stack<>();
        stack.push(new int[]{1, 1});
        for(int a : arr) {
            if(stack.peek()[1] >= a) {
                int max = stack.peek()[1];
                stack.push(new int[]{a, max});
            } else {
                stack.push(new int[]{a, a});
            }
        }
        System.out.println(stack.peek()[1]);
    }
```



## Java新技术

JDK19

1. 虚拟线程

   虚拟线程（Virtual Thread-）是 JDK 而不是 OS 实现的轻量级线程(Lightweight Process，LWP），许多虚拟线程共享同一个操作系统线程，虚拟线程的数量可以远大于操作系统线程的数量。

   虚拟线程在其他多线程语言中已经被证实是十分有用的，比如 Go 中的 Goroutine、Erlang 中的进程。

   虚拟线程避免了上下文切换的额外耗费，兼顾了多线程的优点，简化了高并发程序的复杂，可以有效减少编写、维护和观察高吞吐量并发应用程序的工作量。

2. 外部函数和内存 API

   Java 程序可以通过该 API 与 Java 运行时之外的代码和数据进行互操作。通过高效地调用外部函数（即 JVM 之外的代码）和安全地访问外部内存（即不受 JVM 管理的内存），该 API 使 Java 程序能够调用本机库并处理本机数据，而不会像 JNI 那样危险和脆弱。

   

   Foreign Function & Memory API (FFM API) 定义了类和接口：

   1. 分配外部内存 ：MemorySegment、、MemoryAddress和SegmentAllocator）；
   2. 操作和访问结构化的外部内存： MemoryLayout, VarHandle；
   3. 控制外部内存的分配和释放：MemorySession；
   4. 调用外部函数：Linker、FunctionDescriptor和SymbolLookup。

3. 



# Momenta（准备）

## 项目



## NIO BIO区别

应用程序发起I/O调用后，会经历两个步骤：

1. 内核等待I/O设备准备好数据
2. 内核将数据从内核空间拷贝到用户空间



两者区别

1. BIO：同步阻塞IO模型

   应用程序发起read调用后，会一直阻塞，直到内核把数据拷贝到用户空间；

   客户端连接数量不高是，BIO是没问题的；但是面对上千万的并发量却无能为力

   <img src="E:\2021java\Typora\typora-user-images\6a9e704af49b4380bb686f0c96d33b81tplv-k3u1fbpfcp-watermark-16756969130362.image" alt="图源：《深入拆解Tomcat & Jetty》" style="zoom:50%;" />

2. NIO：同步非阻塞

   Java 中的 NIO 于 Java 1.4 中引入，对应 java.nio 包，提供了 Channel , Selector，Buffer 等抽象。NIO 中的 N 可以理解为 Non-blocking，不单纯是 New。它是支持面向缓冲的，基于通道的 I/O 操作方法。 对于高负载、高并发的（网络）应用，应使用 NIO 。Java 中的 NIO 可以看作是 **I/O 多路复用模型**。也有很多人认为，Java 中的 NIO 属于**同步非阻塞 IO 模型**。

   1. 同步非阻塞：应用程序会一直发起read调用，直到数据准备完成；在内核将数据拷贝到用户空间时，线程依然是阻塞的，直到拷贝数据完成；

      相比于同步阻塞 IO 模型，同步非阻塞 IO 模型确实有了很大改进。通过轮询操作，避免了一直阻塞。

      但是，这种 IO 模型同样存在问题：应用程序不断进行 I/O 系统调用轮询数据是否已经准备好的过程是十分消耗 CPU 资源的。

      

      <img src="E:\2021java\Typora\typora-user-images\bb174e22dbe04bb79fe3fc126aed0c61tplv-k3u1fbpfcp-watermark.image" alt="图源：《深入拆解Tomcat & Jetty》" style="zoom:50%;" />

   2.  I/O 多路复用：线程首先发起 select 调用，询问内核数据是否准备就绪，等内核把数据准备好了，用户线程再发起 read 调用。read 调用的过程（数据从内核空间 -> 用户空间）还是阻塞的。

      <img src="E:\2021java\Typora\typora-user-images\88ff862764024c3b8567367df11df6abtplv-k3u1fbpfcp-watermark-16756975801736.image" alt="img" style="zoom:67%;" />

      目前支持 IO 多路复用的系统调用，有 select，epoll 等等。select 系统调用，目前几乎在所有的操作系统上都有支持。

      select 调用 ：内核提供的系统调用，它支持一次查询多个系统调用的可用状态。几乎所有的操作系统都支持。

      epoll 调用 ：linux 2.6 内核，属于 select 调用的增强版本，优化了 IO 的执行效率。

      IO 多路复用模型，通过减少无效的系统调用，减少了对 CPU 资源的消耗。

      Java 中的 NIO ，有一个非常重要的选择器 ( Selector ) 的概念，也可以被称为 多路复用器。通过它，只需要一个线程便可以管理多个客户端连接。当客户端数据到了之后，才会为其服务。

      <img src="E:\2021java\Typora\typora-user-images\0f483f2437ce4ecdb180134270a00144tplv-k3u1fbpfcp-watermark.image" alt="img" style="zoom:50%;" />

   

## Redis的基本数据类型

Redis 共有 5 种基本数据结构：String（字符串）、List（列表）、Hash（散列）、Set（集合）、Zset（有序集合）。

1. String（字符串）

   String 是一种二进制安全的数据结构，可以用来存储任何类型的数据比如字符串、整数、浮点数、图片（图片的 base64 编码或者解码或者图片的路径）、序列化后的对象。

   **底层**：虽然 Redis 是用 C 语言写的，但是 Redis 并没有使用 C 的字符串表示，而是自己构建了一种 简单动态字符串（Simple Dynamic String，SDS）。相比于 C 的原生字符串，Redis 的 SDS 不光可以保存文本数据还可以保存二进制数据，并且获取字符串长度复杂度为 O(1)（C 字符串为 O(N)）,除此之外，Redis 的 SDS API 是安全的，不会造成缓冲区溢出。

   **应用场景**：存储常规数据（session、token、图片地址、序列化后的对象）、计数场景（请求数、页面访问量）、分布式锁（缺陷）

2. List（列表）

   Redis的List使用了链表的数据结构。

   **底层**：由于C语言没有链表，所以Redis实现了自己的链表结构。Redis的链表为双向链表，支持反向查找，但会带来部分额外内存开销。

   **应用场景**：信息流展示（最新文章、最新动态）、消息队列（缺陷）

3. Hash（哈希）

   Redis中的Hash是字符串类型的键值对映射表，特别适合用于存储对象，后续操作时，可以直接修改对象中的字段值

   **底层**：类似于JDK1.8以前的HashMap，内部实现是数组+链表，但Redis做了更多优化

   **应用场景**：存储对象数据（用户信息、商品信息、文章信息、购物车信息）

4. Set（集合）

   **底层**：Redis中的Set是一种无序、不重复的集合，类似于Java的HashSet。

   **应用场景**：可以基于 Set 轻易实现交集、并集、差集的操作，比如你可以将一个用户所有的关注人存在一个集合中，将其所有粉丝存在一个集合。这样的话，Set 可以非常方便的实现如共同关注、共同粉丝、共同喜好等功能。这个过程也就是求交集的过程。

5. Sorted Set（有序集合）

   **底层**：Sorted Set 类似于 Set，但和 Set 相比，Sorted Set 增加了一个权重参数 score，使得集合中的元素能够按 score 进行有序排列，还可以通过 score 的范围来获取元素的列表。有点像是 Java 中 HashMap 和 TreeSet 的结合体。

   **应用场景**：排行榜



## ZSet是怎么实现的 *

https://blog.csdn.net/m0_72134256/article/details/125371451



## 深拷贝浅拷贝

1. 浅拷贝：会在堆上创建一个新对象；如果对象的属性是引用类型，会直接复制内部对象的内存地址，也就是拷贝对象和原对象公用一个内部对象
2. 深拷贝：完全复制原对象，包括这个对象的内部对象

![浅拷贝、深拷贝、引用拷贝示意图](E:\2021java\Typora\typora-user-images\shallow&deep-copy.png)

clone的使用

浅拷贝：

1. 类实现Cloneable接口，并重写clone()方法
2. clone()方法的实现：调用父类Object的clone()方法

深拷贝：

1. clone()方法的实现有所不同，需要对内部对象进行一次clone()的复制并写入



## Integer a = 100,Integer b=100,是不是一样的

一样的

Byte,Short,Integer,Long 这 4 种包装类默认创建了数值 [-128，127] 的相应类型的缓存数据，Character 创建了数值在 [0,127] 范围的缓存数据，Boolean 直接返回 True or False。



如果超出对应范围仍然会去创建新的对象，缓存的范围区间的大小只是在性能和资源之间的权衡。



## 继承和多态

继承

1. 子类拥有父类对象所有的属性和方法（包括私有属性和私有方法），但是父类中的私有属性和方法子类是无法访问，只是拥有。
2. 子类可以拥有自己属性和方法，即子类可以对父类进行扩展。
3. 子类可以用自己的方式实现父类的方法。

多态：一个对象具有多种的状态，具体表现为**父类的引用**指向子类的实例。

```java
Map<Integer, Integer> map = new HashMap<>();
```

1. 对象类型和引用类型之间具有继承（类）/实现（接口）的关系；
2. 引用类型变量发出的方法调用的到底是哪个类中的方法，必须在程序运行期间才能确定；
3. 多态不能调用“只在子类存在但在父类不存在”的方法；
4. 如果子类重写了父类的方法，真正执行的是子类覆盖的方法，如果子类没有覆盖父类的方法，执行的是父类的方法。



## 常量池的理解

https://blog.csdn.net/IT__learning/article/details/121873196

常量池分为 Class 常量池、运行时常量池、字符串常量池。

1. Class常量池

   Java 文件被编译成 Class 文件，Class 文件中除了包含类的版本、字段、方法、接口等描述信息外，还有一项就是 Class 常量池，Class 常量池是当 Class 文件被 Java 虚拟机加载进来后存放各种字面量 (Literal)和符号引用 。

   ![img](E:\2021java\Typora\typora-user-images\watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBASVRfX2xlYXJuaW5n,size_20,color_FFFFFF,t_70,g_se,x_16.png)

 2. 运行时常量池（在方法区）

    运行时常量池是**方法区**的一部分。运行时常量池是**当 Class 文件被加载到内存后**，Java虚拟机会将 **Class 文件常量池**里的内容转移到**运行时常量池**里，即编译期间生成的字面量、符号引用(运行时常量池也是每个类都有一个)。一般来说，除了保存 Class 文件中描述的符号引用外，还会把翻译出来的直接引用也存储到运行时常量池中。

    

    运行时常量池相对于 Class 文件常量池的另外一个重要特征是**具备动态性**，Java 语言并不要求常量一定只有编译期才能产生，也就是并非预置入 Class 文件中常量池的内容才能进入方法区运行时常量池，运行期间也可能将新的常量放入池中。

    ![img](E:\2021java\Typora\typora-user-images\watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBASVRfX2xlYXJuaW5n,size_13,color_FFFFFF,t_70,g_se,x_16.png)

 3. 字符串常量池（在堆）

    字符串常量池又称为字符串池，全局字符串池，英文也叫 String Pool。JVM 为了提升性能和减少内存开销，避免字符串的重复创建，其维护了一块特殊的内存空间，这就是字符串常量池。字符串常量池由 String 类私有的维护。
    全局字符串池里的内容是在类加载完成，经过验证，准备阶段之后在堆中生成字符串对象实例，然后将该字符串对象实例的字符串值存到 string pool 中（记住：string pool中存的是字符串值而不是具体的实例对象，具体的实例对象是在堆中开辟的一块空间存放的。）

    在 JDK7 之前字符串常量池是在永久代里边的，但是在 JDK7 中，把字符串常量池移到了堆里边。



## ==和equals



## 分布式怎么确定 id唯一

id自增主键



## 线程池怎么创建

1. 使用 ThreadPoolExecutor 构造函数创建线程池（推荐使用）

2. 通过 Executor 框架的工具类 Executors 来实现 我们可以创建三种类型的 ThreadPoolExecutor

   FixedThreadPool、SingleThreadExecutor、CachedThreadPool



## 怎么判断对象相等

1. 比较hashcode值是否相等
2. 如果不相等，则认为两个对象不相等
3. 如果相等，再用equals()比较，如果返回true，则说明对象相等，否则不相等

**使用hashcode的原因/好处**：

这是因为在一些容器（比如 HashMap、HashSet）中，有了 hashCode() 之后，判断元素是否在对应容器中的效率会更高（参考添加元素进HashSet的过程）。

如果 HashSet 在对比的时候，同样的 hashCode 有多个对象，它会继续使用 equals() 来判断是否真的相同。也就是说 hashCode 帮助我们大大缩小了查找成本。

**那为什么两个对象有相同的 hashCode 值，它们也不一定是相等的？**

因为 hashCode() 所使用的哈希算法也许刚好会让多个对象传回相同的哈希值。越糟糕的哈希算法越容易碰撞，但这也与数据值域分布的特性有关（所谓哈希碰撞也就是指的是不同的对象得到相同的 hashCode )。



**hashcode应用场景**：HashSet去重

当你把对象加入 HashSet 时，HashSet 会先计算对象的 hashCode 值来判断对象加入的位置，同时也会与其他已经加入的对象的 hashCode 值作比较，如果没有相符的 hashCode，HashSet 会假设对象没有重复出现。但是如果发现有相同 hashCode 值的对象，这时会调用 equals() 方法来检查 hashCode 相等的对象是否真的相同。如果两者相同，HashSet 就不会让其加入操作成功。如果不同的话，就会重新散列到其他位置。这样我们就大大减少了 equals 的次数，相应就大大提高了执行速度。



## Stream

https://blog.csdn.net/MinggeQingchun/article/details/123184273

Stream是Java 8 API添加的一个新的抽象，称为流Stream，以一种声明性方式处理数据集合（侧重对于源数据计算能力的封装，并且支持序列与并行两种操作方式）

Stream流是从支持数据处理操作的源生成的元素序列，源可以是数组、文件、集合、函数。流不是集合元素，它不是数据结构并不保存数据，它的主要目的在于计算；

.filter:根据某一条件对集合进行筛选

.map:抽取对象集合中的某个元素组成集合



**流类型**：

1. stream 串行流
2. parallelStream 并行流，可多线程执行



**流程**：

1. 将集合转换为Stream流（或者创建流）
2. 操作Stream流（中间操作，终端操作）



**特点**：

1. 通过简单的链式编程，使得它可以方便地对遍历处理后的数据进行再处理。
2. 方法参数都是函数式接口类型。
3. 一个 Stream 只能操作一次，操作完就关闭了，继续使用这个 stream 会报错。
4. Stream 不保存数据，不改变数据源





## jdk8新特性

Interface & functional Interface、Lambda、Stream、Optional、Date time-api

**Interface**

为了解决接口的修改与现有的实现不兼容的问题。新 interface 的方法可以用default 或 static修饰，这样就可以有方法体，实现类也不必重写此方法。

1. default修饰的方法，是普通实例方法，可以用this调用，可以被子类继承、重写。
2. static修饰的方法，使用上和一般类静态方法一样。但它不能被子类继承，只能用Interface调用。

**functional interface 函数式接口**

**Lambda 表达式**

1. 定义线程执行的内容（Runnable 接口）
2. Comparator 接口
3. Listener 接口
4. 自定义接口

**Stream**

**Optional**

层层判断对象非空

传统解决 NPE 的办法如下：

```java
Zoo zoo = getZoo();
if(zoo != null){
   Dog dog = zoo.getDog();
   if(dog != null){
      int age = dog.getAge();
      System.out.println(age);
   }
}
```

Optional 是这样的实现的：

```java
Optional.ofNullable(zoo).map(o -> o.getDog()).map(d -> d.getAge()).ifPresent(age ->
    System.out.println(age)
);
```

**Date-Time API**





## 框架的分层

1. DAO层： 持久层，主要与数据库进行交互

   DAO层主要是做数据持久层的工作，主要与数据库进行交互。DAO层首先会创建DAO接口，然后会在配置文件中定义该接口的实现类，接着就可以在模块中就可以调用DAO 的接口进行数据业务的而处理，并且不用关注此接口的具体实现类是哪一个类。DAO 层的数据源和数据库连接的参数数都是在配置文件中进行配置的。

2. Entity层(domain层) 实体层  ，数据库在项目中的类

3. Service层(biz)：业务层，控制业务

   Service层主要负责业务模块的逻辑应用设计。和DAO层一样都是先设计接口，再创建要实现的类，然后在配置文件中进行配置其实现的关联。接下来就可以在service层调用接口进行业务逻辑应用的处理。

4. Controller层:(action层) 控制层  控制业务逻辑

   Controller层负责具体的业务模块流程的控制，controller层主要调用Service层里面的接口控制具体的业务流程，控制的配置也需要在配置文件中进行。

5. View层 此层与控制层结合比较紧密，需要二者结合起来协同工发。View层主要负责前台jsp页面的表示



**Conroller层和Service层的区别**：Controlle层负责具体的业务模块流程的控制；Service层负责业务模块的逻辑应用设计；

**总结**：在具体的项目中，其流程为：Controller层调用Service层的方法，Service层调用Dao层中的方法，其中调用的参数是使用Entity层进行传递的。



## 索引

**类型**

1. 主键索引
2. 辅助索引：唯一索引、普通索引、前缀索引、全文索引

**哪些列适合加索引**：

1. 不为 NULL 的字段 ：索引字段的数据应该尽量不为 NULL，因为对于数据为 NULL 的字段，数据库较难优化。如果字段频繁被查询，但又避免不了为 NULL，建议使用 0,1,true,false 这样语义较为清晰的短值或短字符作为替代。
2. 被频繁查询的字段 ：我们创建索引的字段应该是查询操作非常频繁的字段。
3. 被作为条件查询的字段 ：被作为 WHERE 条件查询的字段，应该被考虑建立索引。
4. 频繁需要排序的字段 ：索引已经排序，这样查询可以利用索引的排序，加快排序查询时间。
5. 被经常频繁用于连接的字段 ：经常用于连接的字段可能是一些外键列，对于外键列并不一定要建立外键，只是说该列涉及到表与表的关系。对于频繁被连接查询的字段，可以考虑建立索引，提高多表连接查询的效率。

**哪些列不适合加索引**：

1. 被频繁更新的字段应该慎重建立索引



## 慢查询

1. 检查是否用了索引
2. 检查索引是否最优
3. 检查所查询的字段是否都是必须的
4. 检查所查询数据是否过多
5. 检查机器性能



## 算法题 （lc 20）



# 电信研究院（准备）

## Mysql主从同步

**MySQL主从同步简介**

MySQL主从同步，即MySQL Replication，可以实现将数据从一台数据库服务器同步到多台数据库服务器。MySQL数据库自带主从同步功能，经过配置，可以实现基于库、表结构的多种方案的主从同步。
MySQL主从同步的作用主要有以下几点：

1. 故障切换
2. 提供一定程度上的备份服务
3. 实现MySQL数据库的读写分离



**MySQL主从同步过程**

1. 当有数据更改语句执行时，MySQL主库要在更新数据的同时，写二进制日志，将数据修改的内容记录进入日志中。
2. MySQL从库上运行这一些I/O进程，这个进程会监视MySQL主库上的二进制日志，当发现修改时，会立即同步到自身的中继日志。
3. MySQL从库上还会运行一个SQL进程，该进程用于监视自身的中继日志，当发现自身的中继日志发生改变时，立即将该中继日志改变对应的数据更改操作写入自身的数据库。

<img src="E:\2021java\Typora\typora-user-images\watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAd2VpeGluXzQwMjI4MjAw,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center.png" alt="img" style="zoom:67%;" />



**主从同步常见方案**

1. M-S，即采用一个MySQL主库，多个MySQL从库的方式

   缺点：主库面临单点瓶颈和单点故障问题，I/O压力过大。

2. M-S-S，其中一个Slave Relay负责从MySQL主库同步数据，但是其本身并不存在数据，该设备作用仅仅是在日志层面。其他的MySQL从库不是从MySQL主库上同步数据，而是从中继Slave上同步数据，这样一来，就可以缓解Master的I/O压力了

3. M-M，即两台数据库互为主备，互相同步，每个数据库的更新都会同步到另一个数据库中，有时，我们还可以采用多台数据库，组成环装更新架构。



**主从同步方式**

MySQL主从同步有三种方式，这些方式的差异主要存在于写入日志的内容不同，也会导致MySQL主从同步性能上的区别。

1. 基于SQL语句的复制

   基于SQL语句的复制模式的Binlog格式为STATEMENT，在这种模式下，每一条修改数据的SQL语句都会记录到Binlog中，这样做的优点是并不需要记录每一条SQL语句和每一行的变化，减少了二进制日志日志量，节约了I/O。但是会导致某些情况下主、从库数据不一致的场景。

2. 基于行的复制

   不记录每条SQL语句，仅记录哪条记录数据被修改了，以及修改后的结果。这样做的优点是不容易出现主、从库数据不一致的场景，但是缺点在于会产生大量日志。

3. 混合模式复制

   在混合模式下，边的复制采用基于SQL语句的复制方式，只有当该方式无法复制（比如触发器、存储过程等等）时，才会使用基于行的方式。



## redis的内部结构

Redis 共有 5 种基本数据结构：String（字符串）、List（列表）、Set（集合）、Hash（散列）、Zset（有序集合）。

底层数据结构实现如下：

| String | List                         | Hash                | Set             | Zset              |
| ------ | ---------------------------- | ------------------- | --------------- | ----------------- |
| SDS    | LinkedList/ZipList/QuickList | Hash Table、ZipList | ZipList、Intset | ZipList、SkipList |



# 简历准备

## 集体码项目

- 项目特点

1. 1. 对“重点行业从业人员”模块进行开发，统计相关行业人员的信息。
   2. 对“行业健康码”模块进行开发，并提供黄码和红码人员的详情清单展示和清单导出功能。
   3. 对“核酸完成情况”模块进行开发，筛查未完成核酸检测人员。
   4. 对“哨点药店”模块进行开发，统计药店注册情况。

- 解决问题：利用微服务思想将网站分为政企集体模块、哨点药店模块等，使用SpringCloud对不同服务进行统一管理，实现全局统一返回和全局跨域处理，结合SpringBoot和MyBatis框架实现各个模块的业务需求。 



1、对“重点行业从业人员”进行去重统计，并提供同比昨日数量的增量统计

去重：体现在distinct对t.group_code的去重处理

![image-20221214233238380](C:\Users\陈康瑞\AppData\Roaming\Typora\typora-user-images\image-20221214233238380.png)

2、对绿码、黄码、红码人员进行去重统计，并提供黄码和红码人员的详情清单展示和清单导出功能

![image-20221215022010005](E:\2021java\Typora\typora-user-images\image-20221215022010005.png)

需要用到MapKey注解

![image-20221215022047090](E:\2021java\Typora\typora-user-images\image-20221215022047090.png)

根据返回的Map，判断长度是否为3，如果是3，说明三种类别的码都有，否则，需要补齐，并映射为0值

![image-20221215022303779](E:\2021java\Typora\typora-user-images\image-20221215022303779.png)

3、对核酸完成情况根据身份证号码进行去重统计，并可根据在岗和离岗状态进行条件查询，提供未完成核酸检测人员清单查询和导出功能

关于count(concat)去重：https://www.cnblogs.com/tujia/p/13717931.html

![image-20221215025055774](E:\2021java\Typora\typora-user-images\image-20221215025055774.png)

## 羊城先锋项目

### 1、党员服务队

对党员服务队详情开发模糊查询功能，实现根据姓名、手机号、居住地址对队伍党员信息进行模糊查询

获取服务小队下成员

![image-20221215150007816](E:\2021java\Typora\typora-user-images\image-20221215150007816.png)

### 2、党员服务记录

对党员服务记录开发年度查询功能，实现根据该党员最早服务年份，获取该年份或者后续年份的服务记录

效果如下

![image-20221215162806358](E:\2021java\Typora\typora-user-images\image-20221215162806358.png)

先获取该党员的最早服务年份，目的是为了限制前端日期筛选框的最早日期，是最早日期前的所有日期都不可选

![image-20221215152837175](E:\2021java\Typora\typora-user-images\image-20221215152837175.png)

![image-20221215152911108](E:\2021java\Typora\typora-user-images\image-20221215152911108.png)

**其中where 1 = 1是为了解决“where 后面如果不传入参数，会导致where后面没有条件”的sql语法错误**

根据所选年份，查询该年度的服务记录（用服务开始年份作为年度条件）

![image-20221215154815747](E:\2021java\Typora\typora-user-images\image-20221215154815747.png)

**联表查询**（社区服务表、服务报名表）

![image-20221215163849487](E:\2021java\Typora\typora-user-images\image-20221215163849487.png)

### 3、账号数据权限升级

负责账号数据权限升级的工作，通过判断不同用户的类别id，筛选出这个类别账户下的所有相应的网格党组织、楼栋党组织和党员服务队的数据，并予以展示权限

3.1、以服务队为例，因为原本服务队只有社区账号才有权限查看和导出，所以相当于需要扩大查询权限。

3.2、查询条件主要通过社区id查询。原有的查询时默认账号就是社区账号，直接通过pbuser获取社区id并传入查询。现在要对街道账号/区级账号/市级账号进行判别。

3.3、需要获取PBUser用户类型，判断是社区账号/街道账号/区级账号/市级账号，通过社区id/街道id/区级id去查询他们底下的所有社区id，通过社区id列表去进行批量查询。

具体解决如下：

**查看服务队列表**

<img src="E:\2021java\Typora\typora-user-images\image-20221214191138820.png" alt="image-20221214191138820" style="zoom:67%;" />

**导出服务队列表**

<img src="E:\2021java\Typora\typora-user-images\image-20221214191351149.png" alt="image-20221214191351149" style="zoom:67%;" />

查询参数类加入街道id和区级id

<img src="E:\2021java\Typora\typora-user-images\image-20221214184915063.png" alt="image-20221214184915063" style="zoom:67%;" />

通过社区id/街道id/区级id去查询他们底下的所有社区id，通过社区id列表去进行批量查询

<img src="E:\2021java\Typora\typora-user-images\image-20221214185030201.png" alt="image-20221214185030201" style="zoom:67%;" />



### 4、导出功能实现

```java
/**
     * 网格-网络党支部管理导出
     *
     * @return
     */
    @ApiOperation(value = "网格-网络党支部管理导出")
    @PostMapping("/exportGridPartyOrg")
    @ApiImplicitParams({
            @ApiImplicitParam(paramType = "query", dataType = "string", name = PartyUtils.pbTokenKey, value = "授权token", required = true)
    })
    public void exportGridPartyOrg(@RequestBody GridQueryDto dto, HttpServletResponse response) {
        List<PbGridPartyOrgVo> exportDtos = pbGridPartyOrgV2Service.selectPartyOrgListByExport(dto);
        if (CollectionUtils.isEmpty(exportDtos)){
            throw new RockException(StatusCode.Fail, "暂无无符合数据");
        }
        String titleDesc  = "网络党支部管理";
        try {
            String filename =titleDesc+".xls";
            Workbook workbook = MyExcelEasyPoiUtil.getWorkbookMoreSheet(exportDtos, titleDesc, PbGridPartyOrgVo.class);
            response.setContentType("multipart/form-data");
            response.setHeader("content-disposition",
                    "attachment;filename=" + URLEncoder.encode(filename, "UTF-8"));
            workbook.write(response.getOutputStream());
        } catch (Exception e) {
            log.error(titleDesc+"-导出 失败.入参：{}",JSON.toJSONString(dto),e);
            throw new RockException(StatusCode.Fail, titleDesc+"-导出 失败");
        }
    }
```



主要实现

```java
			String filename =titleDesc+".xls";
            Workbook workbook = MyExcelEasyPoiUtil.getWorkbookMoreSheet(exportDtos, titleDesc, PbGridPartyOrgVo.class);
            response.setContentType("multipart/form-data");
            response.setHeader("content-disposition",
                    "attachment;filename=" + URLEncoder.encode(filename, "UTF-8"));
            workbook.write(response.getOutputStream());
```



### 5、获取服务队成员-表索引

report_id_card：报到人身份证

grid_party_id：网格党组织id

com_id：社区id

build_party_id：网格（楼栋）党小组id

service_team_id：服务队id

create_time：创建时间

![image-20230208021801502](E:\2021java\Typora\typora-user-images\image-20230208021801502.png)



6、服务表索引

![image-20230208023043155](E:\2021java\Typora\typora-user-images\image-20230208023043155.png)



# 零碎知识

## System.arraycopy() 、 Arrays.copyOf()、Arrays.copyOfRange()

System.arraycopy()方法

```java
// 我们发现 arraycopy 是一个 native 方法,接下来我们解释一下各个参数的具体意义
    /**
    *   复制数组
    * @param src 源数组
    * @param srcPos 源数组中的起始位置
    * @param dest 目标数组
    * @param destPos 目标数组中的起始位置
    * @param length 要复制的数组元素的数量
    */
    public static native void arraycopy(Object src,  int  srcPos,
                                        Object dest, int destPos,
                                        int length);
```

Arrays.copyOf()方法

```java
public static int[] copyOf(int[] original, int newLength) {
    	// 申请一个新的数组
        int[] copy = new int[newLength];
	// 调用System.arraycopy,将源数组中的数据进行拷贝,并返回新的数组
        System.arraycopy(original, 0, copy, 0,
                         Math.min(original.length, newLength));
        return copy;
    }
```

Arrays.copyOf()方法可以理解为原有数组扩容，其中调用了System.arraycopy()方法

区别：

1、arraycopy() 需要目标数组，将原数组拷贝到你自己定义的数组里或者原数组，而且可以选择拷贝的起点和长度以及放入新数组中的位置

2、copyOf() 是系统自动在内部新建一个数组，并返回该数组。

Arrays.copyOfRange()方法

```java
return Arrays.copyOfRange(res, 0, index); //复制数组范围[0, index)的内容并返回
```

## return res.toArray(new int[res.size()] []);

将链表集合转成数组输出

## 类加载、类实例化

类加载时，静态代码块、静态变量初始化会自动执行。



## Integer 和int

== 比较的时候触发自动拆箱

.equals()比较的时候触发自动装箱





https://xiaolincoding.com/network/

https://interviewguide.cn/notes/02-learning_route/02-language/01-C++.html



## sleep()、wait()

https://www.cnblogs.com/chunxiaoxi/p/15060760.html



## (n - 1) & hash

(n - 1) & hash = hash % n



## QPS（Query Per Second）

服务器每秒可以执行的查询次数；



# 埋

## ThreadPoolExecutor

## PriorityQueue

## SpringMVC核心组件

## GET和POST有什么区别？

## HashMap为什么线程不安全

https://blog.csdn.net/qq_35958391/article/details/125015642

描述：HashMap的线程不安全体现在会造成死循环、数据丢失、数据覆盖等问题。其中死循环和数据丢失是在JDK1.7中出现的问题，在JDK1.8中已经得到解决，但是1.8中仍会有数据覆盖这样的问题。

原因：Java7中头插法扩容会导致死循环和数据丢失，Java8中将头插法改为尾插法后死循环和数据丢失已经得到解决，但仍然有数据覆盖的问题。



## CAS

无锁操作，但保证线程安全

CAS 操作包含三个操作数 —— 内存位置（V）、预期原值（A）和新值(B)。如果内存位置的值V与预期原值A相匹配，那么处理器会自动将该位置值V更新为新值B，否则，处理器不做任何操作，整个操作保证了原子性，即在对比V==A后、设置V=B之前不会有其他线程修改V的值。



## 动态代理
