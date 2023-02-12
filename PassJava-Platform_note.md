# 一、关于gulimall项目第三方模块引入oss后，renren-fast启动报错Error creating bean with name ‘ossClient‘

每一个模块都引入 或者引入第三发服务

```yml
spring:
  cloud:
    alicloud:
      access-key: XXXXXX
      secret-key: XXXXXXX
      oss:
        endpoint: oss-cn-beijing.aliyuncs.com

```

**或 注释掉oss依赖**

<dependency>
			<groupId>com.aliyun.oss</groupId>
			<artifactId>aliyun-sdk-oss</artifactId>
			<version>${aliyun.oss.version}</version>
</dependency>

或

```java
@SpringBootApplication(exclude = {OssContextAutoConfiguration.class})
```

# 二、配置passjava-question遇到的问题

Description:

Failed to configure a DataSource: 'url' attribute is not specified and no embedded datasource could be configured.

Reason: Failed to determine a suitable driver class

解决：bootstrap.properties文件

```yaml
spring.cloud.nacos.config.namespace=59164c5a-5d97-4806-8583-e43718f3ced8
```



# 三、每个微服务若没有用到对象存储，都要做到：

```java
@SpringBootApplication(exclude = {OssContextAutoConfiguration.class})
```

# 四、application.yml文件

记得配置相关信息

# 五、Cause: java.sql.SQLSyntaxErrorException: Unknown column 'ques_type_id' in 'f

```java
com.jackson0714.passjava.study.entity

/**
	 * 题目类型id
	 */
	private Long quesType;  
```



# 六、组件

Spring Cloud Alibaba - Nacos 实现注册中心 

Spring Cloud Alibaba - Nacos 实现配置中心 

Spring Cloud Alibaba - Sentinel  实现服务容错 

Spring Cloud Alibaba - Seata 实现分布式事务 

Spring Cloud - Ribbon 实现负载均衡 

Spring Cloud - Feign 实现远程调用 

Spring Cloud - Gateway API网关 

Spring Cloud - Sleuth 实现调用链监控



# 七、各组件功能

## 1、nacos

[Nacos] (https://github.com/alibaba/Nacos) 是阿里巴巴开源的一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台。

（member等服务并未完全配置完）

- 1.引入Nacos依赖
- 2.配置Nacos数据源
- 3.配置中心配置数据集`DataId`和配置内容
- 4.开启动态刷新配置`@RefreshScope`
- 5.获取配置项的值`@value`
- 6.优先使用配置中心的配置
- 7.使用命名空间`namespace`来创建各服务的配置
- 8.使用分组`group`来区分不同环境
- 9.使用多配置集`extension-configs`区分不同类型的配置

## 2、openfeign

- Feign声明式客的HTTP客户端，让远程调用更简单。

- 提供了HTTP请求的模板，编写简单的接口和插入注解，就可以定义好HTTP请求的参数、格式、地址等信息

- 整合了Ribbon（负载均衡组件）和Hystix（服务熔断组件），不需要显示使用这两个组件

- Spring Cloud Feign 在Netflix Feign的基础上扩展了对SpringMVC注解的支持

  使用：

- 引入OpenFeign依赖
- 定义FeignClient接口类（注解`@FeignClient`），声明这个接口类是用来远程调用其他服务的
- 接口类中定义要远程调用的接口方法，指定远程服务方法的路径
- Controller类中调用接口方法
- PassjavaMemberApplication开启远程调用

```java
@EnableFeignClients(basePackages = "com.jackson0714.passjava.member.feign")
```

## 3、gateway

- 网关:流量的入口

- 网关常用功能:路由转发,权限校验,限流控制

- PassJava项目中,小程序和管理后台请求先访问到API网关.

- API网关通过注册中心实时感知微服务的状态的路由地址,准确地将请求路由到各个服务.

  ```yaml
  spring:
    cloud:
      gateway:
        routes:
          - id: route_thirdparty # 题目微服务路由规则
            uri: lb://passjava-thirdparty # 负载均衡，将请求转发到注册中心注册的passjava-thirdparty服务
            predicates: # 断言
              - Path=/api/thirdparty/** # 如果前端请求路径包含 api/thirdparty，则应用这条路由规则
            filters: #过滤器
              - RewritePath=/api/(?<segment>.*),/$\{segment} # 将跳转路径中包含的api替换成空
  ```

- 请求到达网关后,先经过断言Predicate,是否符合某个路由规则
- 如果符合,则按路由规则路由到指定地址
- 请求和响应都可以通过过滤器Filter进行过滤

## 4、OSS对象存储(passjava-thirdparty有配置)

![原理介绍](http://cdn.jayh.club/blog/20200428/Uq4PAu1zk720.png?imageslim)

服务端签名后直传的原理如下：

1. 用户发送上传Policy请求到应用服务器。
2. 应用服务器返回上传Policy和签名给用户。
3. 用户直接上传数据到OSS。



```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: route_thirdparty # 题目微服务路由规则
          uri: lb://passjava-thirdparty # 负载均衡，将请求转发到注册中心注册的passjava-thirdparty服务
          predicates: # 断言
            - Path=/api/thirdparty/** # 如果前端请求路径包含 api/thirdparty，则应用这条路由规则
          filters: #过滤器
            - RewritePath=/api/(?<segment>.*),/$\{segment} # 将跳转路径中包含的api替换成空
```

http://localhost:8060/api/thirdparty/v1/admin/oss/getPolicy  转发到

http://localhost:14000/thirdparty/v1/admin/oss/getPolicy

![配置跨域访问](http://cdn.jayh.club/blog/20200428/1UhhbsBvpGbY.png?imageslim)

web端上传组件？

## 5、统一异常处理



# 八、链路跟踪

Sleuth + Zipkin 结合使用来更好地实现链路追踪

**主要用来用来收集系统的时序数据，进而可以跟踪系统的调用问题。**

## Span跨度：

- 大白话：远程调用和 Span `一对一`。
- 基本的工作单元，每次发送一个远程调用服务就会产生一个 Span。
- Span 是一个 64 位的唯一 ID。
- 通过计算 Span 的开始和结束时间，就可以统计每个服务调用所花费的时间。

## Trace跟踪：

- 大白话：一个 Trace 对应多个 Span，`一对多`。
- 它由一系列 Span 组成，树状结构。
- 64 位唯一 ID。
- 每次客户端访问微服务系统的 API 接口，可能中间会调用多个微服务，每次调用都会产生一个新的 Span，而多个 Span 组成了 Trace

## Annotation注解：

链路追踪系统定义了一些核心注解，用来定义一个请求的开始和结束，注意是微服务之间的请求，而不是浏览器或手机等设备。注解包括：

- `cs` - Client Sent：客户端发送一个请求，描述了这个请求调用的 `Span` 的开始时间。注意：这里的客户端指的是微服务的调用者，不是我们理解的浏览器或手机等客户端。
- `sr` - Server Received：服务端获得请求并准备开始处理它，如果将其 `sr` 减去 `cs` 时间戳，即可得到网络传输时间。
- `ss` - Server Sent：服务端发送响应，会记录请求处理完成的时间，`ss` 时间戳减去 `sr` 时间戳，即可得到服务器请求的时间。
- `cr` - Client Received：客户端接收响应，Span 的结束时间，如果 `cr` 的时间戳减去 `cs` 时间戳，即可得到一次微服务调用所消耗的时间，也就是一个 `Span` 的消耗的总时间。

![微服务调用链路图](http://cdn.jayh.club/blog/20201113/0DBcQe6jT0D5.png?imageslim)

## **Zipkin 包含四大组件：**

- Collection（收集器组件），主要负责收集外部系统跟踪信息。
- Storage（存储组件），主要负责将收集到的跟踪信息进行存储，默认存放在内存中，支持存储到 MySQL 和 ElasticSearch。
- API（查询组件），提供接口查询跟踪信息，给 UI 组件用的。
- UI （可视化 Web UI 组件），可以基于服务、时间、注解来可视化查看跟踪信息。注意：Web UI 不需要身份验证。

## **跟踪流程：**

- 第一步：用户代码发起 HTTP Get 请求，请求路径：/foo。
- 第二步：请求到到跟踪工具后，请求被拦截，会被记录两项信息：标签和时间戳。以及HTTP Headers 里面会增加跟踪头信息。
- 第三步：将封装好的请求传给 HTTP 客户端，请求中包含 X-B3-TraceID 和 X-B3-SpanId 请求头信息。
- 第四步：由HTTP 客户端发送请求。
- 第五步：Http 客户端返回响应 200 OK 后，跟踪工具记录耗时时间。
- 第六步：跟踪工具发送 200 OK 给用户端。
- 第七步：异步报告 Span 信息给 Zipkin 收集器。

## 使用ES作为存储介质：

（ES未配置）

docker run --env STORAGE_TYPE=elasticsearch --env ES_HOSTS=192.168.56.10:9200 openzipkin/zipkin-dependencies

# 九、缓存，redis部署

## 1、redis 在passjava-question的application.yml配置

```yaml
spring:
    redis:
      host: 127.0.0.1
      port: 6379
```

## 2、ITypeService接口文件

```java
List<TypeEntity> getTypeEntityList();
```

## 3、TypeServiceImpl实现ITypeService接口方法

```java
@Override
public List<TypeEntity> getTypeEntityList() {
    // 1.初始化 redis 组件
    ValueOperations<String, String> ops = redisTemplate.opsForValue();
    // 2.从缓存中查询数据
    String typeEntityListCache = ops.get("typeEntityList");
    // 3.如果缓存中没有数据
    if (StringUtils.isEmpty(typeEntityListCache)) {
        System.out.println("The cache is empty");
        // 4.从数据库中查询数据
        List<TypeEntity> typeEntityListFromDb = this.list();
        // 5.将从数据库中查询出的数据序列化 JSON 字符串
        typeEntityListCache = JSON.toJSONString(typeEntityListFromDb);
        // 6.将序列化后的数据存入缓存中
        ops.set("typeEntityList", typeEntityListCache);
        return typeEntityListFromDb;
    }
    // 7.如果缓存中有数据，则从缓存中拿出来，并反序列化为实例对象
    List<TypeEntity> typeEntityList = JSON.parseObject(typeEntityListCache, new TypeReference<List<TypeEntity>>(){});
    return typeEntityList;
}
```

## 4、TypeAppController调用getTypeEntityList方法

```java
@RequestMapping("/list-by-redis")
public R listByRedis(){

    List<TypeEntity> typeEntityList = ITypeService.getTypeEntityList();
    return R.ok().put("typeEntityList", typeEntityList);
}
```

## 5、postman查询测试

http://localhost:8060/api/question/v1/app/type/list-by-redis

# 十、缓存存在的问题

高并发下使用缓存会带来的几个问题：缓存穿透、雪崩、击穿。

## 1、缓存穿透

缓存穿透指一个一定不存在的数据，由于缓存未命中这条数据，就会去查询数据库，数据库也没有这条数据，所以返回结果是 `null`。如果每次查询都走数据库，则缓存就失去了意义，就像穿透了缓存一样。

### 1.1为什么出现缓存穿透

- 业务层误操作：缓存中的数据和数据库中的数据被误删除了，所以缓存和数据库中都没有数据；
- 恶意攻击：专门访问数据库中没有的数据。

### 1.2如何解决缓存穿透

- 对结果 `null` 进行缓存，并加入短暂的过期时间。
- 使用布隆过滤器快速判断数据是否存在，避免从数据库中查询数据是否存在，减轻数据库压力。
- 前端进行请求检测。把恶意的请求（例如请求参数不合理、请求参数是非法值、请求字段不存在）直接过滤掉，不让它们访问后端缓存和数据库。

## 2、缓存雪崩

缓存雪崩是指我们缓存多条数据时，采用了相同的过期时间，比如 00:00:00 过期，如果这个时刻缓存同时失效，而有大量请求进来了，因未缓存数据，所以都去查询数据库了，数据库压力增大，最终就会导致雪崩。

### 2.1如何解决缓存雪崩

在原有的实效时间基础上增加一个随机值，比如 1-5 分钟随机，降低缓存的过期时间的重复率，避免发生缓存集体实效。

## 3、缓存击穿

某个 key 设置了过期时间，但在正好失效的时候，有大量请求进来了，导致请求都到数据库查询了。

### 3.1如何解决缓存击穿

大量并发时，只让一个请求可以获取到查询数据库的锁，其他请求需要等待，查到以后释放锁，其他请求获取到锁后，先查缓存，缓存中有数据，就不用查数据库。

# 十一、Redis分布式锁

# 十二、Redisson

```java
	@Autowired
    RedissonClient redisson;

    @ResponseBody
    @GetMapping("test-lock")
    public String testLock() {


        // 1.获取锁，只要锁的名字一样，获取到的锁就是同一把锁。
        RLock lock = redisson.getLock("WuKong-lock");

        // 2.加锁
        lock.lock(8, TimeUnit.SECONDS);   //8秒是锁设置的时间，锁设置的时间小于了被锁程序的执行时间会报异常
        try {
            System.out.println("加锁成功，执行后续代码。线程 ID：" + Thread.currentThread().getId());
//            Thread.sleep(10000);  //ck注释掉
            Thread.sleep(5000);   //被锁程序的执行时间
        } catch (Exception e) {
            //TODO
        } finally {
            lock.unlock();
            // 3.解锁
            System.out.println("Finally，释放锁成功。线程 ID：" + Thread.currentThread().getId());
        }

        return "test lock ok";
    }
```

如果设置了锁的自动过期时间，则执行业务的时间一定要小于锁的自动过期时间，否则就会报错。

# 十三、Spring Cache

通过传统使用缓存的方式的痛点引出 Spring 框架中的 Cache 组件。然后详细介绍了 Spring Cache 组件的用法：

- 五大注解。 @Cacheable、@CachePut、@CacheEvict、@Caching,、@CacheConfig。
- 如何自定义缓存条目的 key。
- 如何自定义 Cache 配置。
- 如何自定义缓存的条件。

# 十四、跨域访问

![image-20220521154642417](E:\2021java\javaguide\image-20220521154642417.png)

