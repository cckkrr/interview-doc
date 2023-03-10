# 1、JVM vs JDK vs JRE

JDK：编译、运行，用于开发Java程序

JRE：提供Java运行时环境，运行字节码文件

JVM：将字节码文件转成机器码指令，并执行

**JDK包含JRE，JRE包含JVM**



# 2、hashcode()和equals()比较

比较对象时，先用hashcode()，再用equals()



# 3、String、StringBuffer、StringBuilder 的区别？

1、可变性

2、线程安全

3、效率/性能



# 4、泛型

修饰：类、接口、方法

指定传入参数类型、提高代码复用率、方便在编译时就发现参数类型的错误；

编译时采取类型擦除，JVM执行字节码的时候是没有泛型的，这叫做泛型擦除

泛型擦除的原因：兼容以前的jdk版本



# 5、== 和 equals() 的区别

==：对基本数据类型，比较的是内容；对引用数据类型，比较的是对象的内存地址

equals()：对基本数据类型，不生效；  对引用数据类型，比较的是对象的内存地址



# 6、ApplicationContext和BeanFactory区别

![image-20230128023020989](E:\2021java\Typora\typora-user-images\image-20230128023020989.png)



# 7、ArrayList 与 LinkedList 区别

1、线程安全：两者都不安全

2、底层数据结构：前者是数组，后者是链表

3、插入和删除元素的时间复杂度

4、随机访问：前者支持，后者不支持

5、内存占用：后者要存放前驱和后驱数据，占用空间大

6：接口实现：两者都实现了List接口，但后者还实现了Deque接口，可以当做双端队列来用



# 8、B树和B+树的区别；为什么mysql使用B+树

1. B树所有节点都存放key和data；B+数只有叶子结点都存放key和data，其他节点只存key
2. B树叶子结点无关联；B+树叶子结点有指针关联
3. B树的查找相当于对key做二分查找，可能没到叶子节点就结束了；B+树查找稳定，每次都查到叶子结点



mysql使用B+树：

1. B+树在查询时比较稳定，每次都是查询到叶子结点，磁盘IO次数少。
2. InnoDB 引擎中，其数据文件本身就是索引文件，使用B+树可以将key和数据都存放在叶子节点，当根据key找到节点时，就能直接对应的值就是我们想要的元素。
3. 每个节点可以存储多个元素，因此树的高度不会太高。
4. 叶子结点的数据排序且有指针，方便范围查找和全表扫描。



# 9、CopyOnWriteArrayList

底层原理：

1. 大多数场景是“读多写少”，因此多个线程读操作应该要允许
2. 读操作：读操作不需要任何加锁的操作
3. 写操作：add()方法加锁，先复制原有数组为新数组，并且长度+1，再在新数组加入新元素，把原来的指针指向新的数组。
4. 写操作加锁主要是防止对个线程复制多个数组副本，造成数据丢失。



# 10、HashMap扩容机制原理

JDK1.8以前：

1. 复制一个新数组
2. 遍历原数组的每一个位置上的链表上的每一个元素
3. 计算出每个元素在原数组的key，再根据key算出在新数组的下标
4. 将这些元素根据下标放入新数组
5. 将新数组赋给HashMap的table属性

JDK1.8以后：

1. 复制一个新数组

2. 遍历原数组的每一个位置上的链表或红黑树的每一个元素

3. 如果这个位置是链表，则计算出链表上元素在新数组的下标，并添加到新数组

4. 如果这个位置时红黑树，则计算出红黑树所有元素在新数组的下标

   a. 如果这个下标位置的元素超过8个，则生成一个新的红黑树，并将根节点放在下标位置上

   b.如果不超过8个，则生成一个链表，并将头结点放在下标位置上

5. 将新数组赋给HashMap的table属性



# 11、InnoDB如何实现事务

通过Buffer Pool、Log Buffer、redo log、undo log实现，比如对update操作

1. 先查询该表，存在Buffer Pool
2. 在Buffer Pool进行更改
3. 生成redo log对象，存在Log Buffer
4. 生成undo log日志，用于事务回滚
5. 如果提交事务，将redo log对象进行持久化，后续还有其他机制将更改写入磁盘
6. 如果回滚事务，将使用undo log进行回滚



# 12、如何避免死锁

四个必要条件：

1. 互斥条件
2. 请求与保持条件
3. 不剥夺条件
4. 循环等待条件

前三个条件都是锁的特性，因此需要破坏“循环等待条件”：

1. 每个线程对资源的申请顺序一致
2. 注意加锁实现，设置超时时间
3. 通过算法去检查死锁，进行规避



# 13、Java异常体系

1. 分为Exception和Error，有共同父类Trowable类

2. Exception可以通过程序捕获并特殊处理，比如NullPointerException、IllegalArgumentException；

   Error通常会导致程序无法运行，比如OutOfMemoryError、StackOverflowError

3. Exception分为运行时异常和非运行时异常

​		运行时异常：不受检查，在程序运行时才出现，比如NullPointerException、IllegalArgumentException

​		非运行时异常：编译时出现异常，会编译不通过，比如IOException、SQLException

	4. Trowable类常用方法
	4. try-catch-finally使用（不要在 finally 语句块中使用 return）

​		



# 14、Java有哪些类加载器

bootstrap classloader、Ext classloader、App classloader



# 15、JDK1.7到JDK1.8，HashMap底层原理变化

|          | 1.7                                                          | 1.8                                                          |
| :------- | :----------------------------------------------------------- | ------------------------------------------------------------ |
| 底层结构 | 数组+链表，即散列链表                                        | 数组+链表+红黑树，使用红黑树的目的是为了提高插入和查询效率   |
| 插入方式 | 1、定位到数组位置，如果没有元素，直接插入                                                                   2、如果该位置有元素，则从该位置链表的头结点开始，逐一与要插入元素的key进行比较，如果相同就覆盖，不同就采用头插法插入元素 | 1、定位到数组位置，如果没有元素，直接插入           2、如果该位置有元素，判断该位置有没有key，如果key相同则直接覆盖；如果没有key则判断该位置是否为树节点，如果是树节点，则调用插入树节点的方法插入元素；如果不是则遍历链表插入元素，采用尾插法 |
| 哈希算法 | 算法比较复杂，存在各种右移和异或运算                         | 简化了算法，因为复杂的哈希算法是为了提高散列性，由于1.8使用了红黑树提高效率，所以可以适当简化低哈希算法，提高CPU利用率 |



# 16、JVM有哪些垃圾回收算法

1. 标记-清除算法
2. 复制算法
3. 标记-整理算法
4. 分代收集算法



# 17、JVM各个内存区域

1. 堆（线程共享，放的是对象）
2. 方法区（线程共享，放的是类）
3. 程序计数器
4. 虚拟机栈
5. 本地方法栈



# 18、MyBatis优缺点

优点：

1. 基于SQL语句编程，比较灵活；写在XML文件里，解除了SQL语句与程序的耦合
2. 代码量减少
3. 与各种数据库兼容，与spring集成
4. 提供映射标签，支持对象与数据库的ORM字段的关系映射

缺点：

1. SQL语句编写工作量大，涉及到多个表关联的查询，需要有较好的功底
2. SQL语句依赖与数据库，导致数据库可移植性差



# 19、#{}和${}区别

1. #{}是占位符，调用preparedstatement；${}是拼接符，调用Statement来赋值
2. #{}可防止sql注入



# 20、MySQL慢查询如何优化

1. 检查是否用了索引
2. 检查索引是否最优
3. 检查所查询的字段是否都是必须的
4. 检查所查询数据是否过多
5. 检查机器性能



# 21、MySQL锁

按锁粒度分：

1. 表级锁：针对非索引字段加的锁
2. 行级锁（针对索引字段加的锁）：记录锁、间隙锁、临键锁

当我们执行 UPDATE、DELETE 语句时，如果 WHERE条件中字段没有命中唯一索引或者索引失效的话，就会导致扫描全表对表中的所有行记录进行加锁。

还可分为：

1. 共享锁（读锁）
2. 排他锁（写锁）

还可分为：

1. 乐观锁
2. 悲观锁

还可分为：（用于在加表锁之前，判断有无使用行锁）

1. 意向共享锁
2. 意向排他锁



# 22、Redis如何和MySQL保持数据一致

三种缓存策略

1. 旁路缓存模式（常用）：

   写：先更新db，再删除缓存

   读：读取缓存，如果内容，则直接返回；若无内容，则从数据库读取返回，并将新读取的数据放入缓存

2. 读写穿透模式

3. 异步缓存写入

数据不一致的情况：

1. 初次读取cache无数据的情况

   * 请求1先读取cache，但此时cache无数据
   * 请求2更新db
   * 请求1将从db读取到的数据更新到cache，这是旧数据
   * 请求2更新db完毕，此时db内是新数据，即和cache数据不一致

   解决办法：提前将热点数据放到cache中

2. 写操作比较频繁，导致cache被频繁删除，影响缓存命中率

   解决办法：

   * 更新db的同时更新cache，通过分布式锁，防止更新cache时出现线程安全问题
   * 更新db的同时更新cache，但给cache加一个较短的过期时间，可以使不一致的时间变短



# 23、Redis数据结构和应用场景

1. **字符串**：String 是一种二进制安全的数据结构，可以用来存储任何类型的数据比如字符串、整数、浮点数、图片（图片的 base64 编码或者解码或者图片的路径）、序列化后的对象。

   应用场景：session、计数器、分布式锁

2. **列表**：Redis 的 List 的实现为一个 双向链表，即可以支持反向查找和遍历，更方便操作，不过带来了部分额外的内存开销。

   应用场景：用于信息流的展示，比如最新文章、最新动态。

3. **哈希**：Redis 中的 Hash 是一个 String 类型的 field-value（键值对） 的映射表，特别适合用于存储对象。

   应用场景：用于对象信息的存储，比如用户信息、商品信息、文章信息、购物车信息

4. **集合**：Redis 中的 Set 类型是一种无序集合，集合中的元素没有先后顺序但都唯一

   应用场景：粉丝数量、共同关注数量

5. **有序集合**：相比于集合，增加了一个权重参数 score，使得集合中的元素能够按 score 进行有序排列，还可以通过 score 的范围来获取元素的列表

   应用场景：排行榜



# 24、ReentrantLock tryLock和lock的区别

1. tryLock()表示尝试加锁，如果获取不到锁，不会等待，而会继续执行下面的逻辑
2. lock()表示阻塞加锁，会一直阻塞直到加到锁为止



# 25、ReentrantLock公平锁和非公平锁底层实现

1. 底层使用AQS来进行线程排队
2. 如果是公平锁，首先检查队列中是否有线程在排队，如果有，则当前线程也进去排队
3. 如果是非公平锁，不会去检查队列中是否有线程在排队，而是直接去竞争锁



# 26、SOA、微服务、分布式的区别

1. SOA是一种分布式架构，所有服务都注册在总线上；调用服务时，通过总线查看服务的信息并调用
2. 微服务是更彻底面向服务的分布式架构，将总的程序拆分成多个服务，即多个独立的应用程序，一个应用对应一个服务
3. 分布式指的是将单体架构拆分成各个部分，部署不同的服务器；SOA和微服务都是分布式架构的实现



# 27、springboot如何启动tomcat

1. 首先启动springboot，创建一个spring容器
2. 创建spring容器过程中，会使用@ConditionOnClass检查当前classpath有没有Tomcat依赖，如果有，则生成一个启动Tomcat的bean
3. spring容器创建完后，就会获取启动Tomcat的bean，创建Tomcat对象，绑定端口，启动Tomcat



# 28、springboot常用注解以及实现

1. @SpringBootApplication，复合了

   @SpringBootConfiguration：允许在 Spring 上下文中注册额外的 bean 或导入其他配置类

   @EnableAutoConfiguration：启用 SpringBoot 的自动配置机制

   @ComponentScan：扫描被@Component (@Repository,@Service,@Controller)注解的 bean，注解默认会扫描该类所在的包下所有的类

2. @Bean、@Service、@Autowired



# 29、SpringCloud和Dubbo区别

1. dubbo一开始是一个RPC服务调用框架，侧重于服务调用，服务调用性能比SpringCloud高
2. SpringCloud是一个大而全的框架，包括了各个微服务的管理、服务间的调用
3. 两者不是对立的，而是可以结合使用的



# 30、SpringCloud有哪些组件

1. nacos：注册中心、配置中心
2. gateway：网管配置



# 31、synchronized偏向锁、轻量级锁、重量级锁

1. 偏向锁：当前锁被某一个线程占用时，锁对象的对象头会记录一个该线程的ID，如果该线程下次又来获取这个锁，就可以直接获取了
2. 轻量级锁：当一个锁被一个线程占用时，为偏向锁；当有第二个线程来竞争时，偏向锁会升级成轻量级锁；之所以叫轻量级锁，是为了和重量级锁区分开来，轻量级锁底层通过自旋实现，不会阻塞线程
3. 重量级锁：如果自旋次数过多仍没有获取到锁，就会升级成重量级锁，阻塞线程
4. 自旋锁：同步资源的锁定时间很短，为了这一小段时间去切换线程，线程挂起和恢复现场的花费可能会让系统得不偿失；如果机器有多个CPU核心，能够让两个或以上的线程同时并行执行，我们就可以让后面那个请求锁的线程不放弃CPU的执行时间，看看持有锁的线程是否很快就会释放锁；**为了让当前线程“稍等一下”，我们需让当前线程进行自旋，如果在自旋完成后前面锁定同步资源的线程已经释放了锁，那么当前线程就可以不必阻塞而是直接获取同步资源，从而避免切换线程的开销。**



# 31、synchronized和ReentrantLock区别

1. synchronized是一个关键字，ReentrantLock是一个类
2. synchronized会自动加锁与释放锁；ReentrantLock需要通过lock/tryLock上锁，通过unlock释放锁
3. synchronized基于jvm，ReentrantLock基于API
4. synchronized只有非公平锁，ReentrantLock可以实现公平锁和非公平锁
5. ReentrantLock提供了一种能够中断等待锁的线程的机制，正在等待的线程可以选择放弃等待，改为处理其他事情。
6. synchronized锁的是对象，锁信息保存在对象头中；ReentrantLock通过int类型的state来标识锁的状态



# 32、TCP三次握手和四次挥手

三次握手：

1. 客户端向服务端发送SYN，建立连接
2. 服务端向客户端发送ACK和SYN，表示收到客户端的SYN了，同意建立连接
3. 客户端向服务端发送ACK，建立连接

四次挥手：

1. 客服端向服务端发送FIN，提出断开连接
2. 服务端向客户端发送ACK，表明接收到了客户端的FIN，客户端可以断开对服务端的连接了
3. 服务端向客服端发送FIN，表明服务端向客户端已经发送完消息，服务端可以断开连接
4. 客户端向服务端发送ACK，表明接收到服务端的FIN，客户端断开连接



# 33、ThreadLocal底层原理

https://blog.csdn.net/upstream480/article/details/120310476

1. ThreadLocal是Java提供的线程本地存储机制，可以在一个线程内存储线程私有的数据副本。

2. 底层基于ThreadLocalMap实现，每个Thread对象（注意不是ThreadLocal对象）都有一个ThreadLocalMap；key为当前的ThreadLocal对象，value为线程需要缓存的值。

3. 在线程池使用ThreadLocal会造成内存泄漏，因为当使用完ThreadLocal对象之后，应该对key和value进行回收，即对Entry对象进行回收，但线程池中的线程不会回收，线程对象通过强引用指向ThreadLocalMap，ThreadLocalMap又通过强引用指向Entry对象。因此线程不回收，导致Entry对象不回收，导致内存泄漏。

   解决办法：线程使用完ThreadLocal后手动调用remove()方法，清除Entry对象。



如果 ThreadLocal 没有被外部强引用的情况下，在垃圾回收的时候，key 会被清理掉，而 value 不会被清理掉。有可能出现key为null的entry，产生内存泄漏。



# 34、SpringCloud简单介绍

SpringCloud分为SpringCloud Netflix 和SpringCloud Alibaba

![image-20230203005340689](E:\2021java\Typora\typora-user-images\image-20230203005340689.png)

![image-20230203010947926](E:\2021java\Typora\typora-user-images\image-20230203010947926.png)





# 操作系统

## 1、什么是系统调用

1. 用户态、系统态的定义
2. 在我们运行的用户程序中，凡是与系统态级别的资源有关的操作（如文件管理、进程控制、内存管理等)，都必须通过系统调用方式向操作系统提出服务请求，并由操作系统代为完成。
3. 系统调用的分类：设备管理、文件管理、进程控制、进程通信、内存管理



## 2、进程和线程的区别



## 3、进程有哪几种状态

创建状态、就绪状态（准备）、运行状态、阻塞状态（等待）、结束状态



## 4、进程间通信方式

1. 匿名管道
2. 有名管道
3. 信号
4. 消息队列
5. 信号量
6. 共享内存
7. 套接字



## 5、线程间同步方式

1. 互斥量
2. 信号量
3. 事件



## 6、进程调度算法

1. 先到先服务调度算法
2. 短作业优先调度算法
3. 时间片轮转调度算法
4. 多级反馈队列调度算法
5. 优先级调度算法



## 7、什么是死锁

1. 死锁定义
2. 四个必要条件：互斥条件、请求与保持条件、不剥夺条件、循环等待条件
3. 解决死锁的方法：预防（破坏导致死锁的必要条件）、避免（提前预测，使其不会发生）、检测、接触
   1. 预防：静态分配策略（破坏请求与保持条件）、层次分配策略（破坏循环等待条件）
   2. 避免：银行家算法，当一个进程申请使用资源的时候，银行家算法 通过先 试探 分配给该进程资源，然后通过 安全性算法 判断分配后系统是否处于安全状态，若不安全则试探分配作废，让该进程继续等待，若能够进入到安全的状态，则就 真的分配资源给该进程。
   3. 检测：这种方法对资源的分配不加以任何限制，也不采取死锁避免措施，但系统 定时地运行一个 “死锁检测” 的程序，判断系统内是否出现死锁，如果检测到系统发生了死锁，再采取措施去解除它。
   4. 当死锁检测程序检测到存在死锁发生时，应设法让其解除，让系统从死锁状态中恢复过来，常用的解除死锁的方法有以下四种：
      1. 立即结束所有进程的执行，重新启动操作系统 ：这种方法简单，但以前所在的工作全部作废，损失很大。
      2. 撤销涉及死锁的所有进程，解除死锁后继续运行 ：这种方法能彻底打破死锁的循环等待条件，但将付出很大代价，例如有些进程可能已经计算了很长时间，由于被撤销而使产生的部分结果也被消除了，再重新执行时还要再次进行计算。
      3. 逐个撤销涉及死锁的进程，回收其资源直至死锁解除。
      4. 抢占资源 ：从涉及死锁的一个或几个进程中抢占资源，把夺得的资源再分配给涉及死锁的进程直至死锁解除



## 8、内存管理机制

1. 连续分配管理方式：块式管理，易浪费空间

2. 非连续分配管理方式：页式管理、段式管理、段页式管理

   页是物理单位，段是逻辑单位



## 9、快表和多级页表

1. 分页内存管理的两个要点：虚拟地址到物理地址的转换要快、解决虚拟地址空间大导致页表很大的问题
2. 快表的概念、地址转换流程
3. 多级页表的概念



## 10、分页机制和分段机制的异同

共同点：

1. 分页机制和分段机制都是为了**提高内存利用率，减少内存碎片**。
2. 页和段都是**离散存储**的，所以两者都是离散分配内存的方式。但是，每个**页和段中的内存是连续的**。

区别：

1. **页的大小是固定的**，由操作系统决定；而**段的大小不固定**，取决于我们当前运行的程序。
2. 分页仅仅是为了满足操作系统内存管理的需求，而段是逻辑信息的单位，在程序中可以体现为代码段，数据段，能够更好满足用户的需要。



## 11、逻辑地址和物理地址

https://blog.csdn.net/weixin_52148070/article/details/123835194

1. 逻辑地址：在程序运行时由中央处理单元生成的内容的地址称为逻辑地址。该地址也称为虚拟地址。当我们谈论逻辑地址时，我们指的是CPU分配给每个进程的地址，一个进程在内存中所处的实际地址与进程认为它所处的地址是不一样的。
2. 物理地址：物理地址指的是真实物理内存中地址，更具体一点来说就是内存地址寄存器中的地址。物理地址是内存单元真正的地址。

比如：有四个人要租房 ，房子的地址是XX街道XX号,这个地址就是实际的地址，是物理地址。房东将这四间房子进行编号1 2 3 4 号 。  这四人平时聊天会说自己住在几号房，这个就是逻辑地址，但实际地址还是XX街道XX号。



## 12、CPU寻址

现代处理器使用的是一种称为 虚拟寻址(Virtual Addressing) 的寻址方式。使用虚拟寻址，CPU 需要将虚拟地址翻译成物理地址，这样才能访问到真实的物理内存。 实际上完成虚拟地址转换为物理地址转换的硬件是 CPU 中含有一个被称为 内存管理单元（Memory Management Unit, MMU） 的硬件。



## 13、什么是虚拟内存(Virtual Memory)

1. 很多时候我们使用了很多占内存的软件，这些软件占用的内存可能已经远远超出了我们电脑本身具有的物理内存。
2. 为什么可以这样呢？ 正是因为虚拟内存的存在，通过虚拟内存可以让程序可以拥有超过系统物理内存大小的可用内存空间。另外，虚拟内存为每个进程提供了一个一致的、私有的地址空间，它让每个进程产生了一种自己在独享主存的错觉（每个进程拥有一片连续完整的内存空间）。这样会更加有效地管理内存并减少出错。
3. 虚拟内存 使得应用程序认为它拥有连续的可用的内存（一个连续完整的地址空间），而**实际上，它通常是被分隔成多个物理内存碎片，还有部分暂时存储在外部磁盘存储器上**，在需要时进行数据交换。与没有使用虚拟内存技术的系统相比，使用这种技术的系统使得大型程序的编写变得更容易，对真正的物理内存（例如 RAM）的使用也更有效率。目前，大多数操作系统都使用了虚拟内存，如 Windows 家族的“虚拟内存”；Linux 的“交换空间”等。



## 14、局部性原理

1. 时间局部性 ：如果程序中的某条指令一旦执行，不久以后该指令可能再次执行；如果某数据被访问过，不久以后该数据可能再次被访问。产生时间局部性的典型原因，是由于在程序中存在着大量的循环操作。
2. 空间局部性 ：一旦程序访问了某个存储单元，在不久之后，其附近的存储单元也将被访问，即程序在一段时间内所访问的地址，可能集中在一定的范围之内，这是因为指令通常是顺序存放、顺序执行的，数据也一般是以向量、数组、表等形式簇聚存储的。

时间局部性是通过将近来使用的指令和数据保存到高速缓存存储器中，并使用高速缓存的层次结构实现。空间局部性通常是使用较大的高速缓存，并将预取机制集成到高速缓存控制逻辑中实现。虚拟内存技术实际上就是建立了 “内存一外存”的两级存储器的结构，利用局部性原理实现髙速缓存。



## 15、虚拟内存的技术实现

1. 请求分页存储管理 ：建立在分页管理之上，为了支持虚拟存储器功能而增加了请求调页功能和页面置换功能。请求分页是目前最常用的一种实现虚拟存储器的方法。请求分页存储管理系统中，在作业开始运行之前，仅装入当前要执行的部分段即可运行。假如在作业运行的过程中发现要访问的页面不在内存，则由处理器通知操作系统按照对应的页面置换算法将相应的页面调入到主存，同时操作系统也可以将暂时不用的页面置换到外存中。
2. 请求分段存储管理 ：建立在分段存储管理之上，增加了请求调段功能、分段置换功能。请求分段储存管理方式就如同请求分页储存管理方式一样，在作业开始运行之前，仅装入当前要执行的部分段即可运行；在执行过程中，可使用请求调入中断动态装入要访问但又不在内存的程序段；当内存空间已满，而又需要装入新的段时，根据置换功能适当调出某个段，以便腾出空间而装入新的段。
3. 请求段页式存储管理



请求分页与分页存储管理的区别：

它们之间的根本区别在于是否将一作业的全部地址空间同时装入主存。**请求分页存储管理不要求将作业全部地址空间同时装入主存**。基于这一点，请求分页存储管理可以提供虚存，而分页存储管理却不能提供虚存。



不管是上面那种实现方式，我们一般都需要：

1. **一定容量的内存和外存**：在载入程序的时候，只需要将程序的一部分装入内存，而将其他部分留在外存，然后程序就可以执行了；
2. **缺页中断**：如果需执行的指令或访问的数据尚未在内存（称为缺页或缺段），则由处理器通知操作系统将相应的页面或段调入到内存，然后继续执行程序；
3. **虚拟地址空间** ：逻辑地址到物理地址的变换。



## 16、页面置换算法

**定义**：

当发生缺页中断时，如果当前内存中并没有空闲的页面，操作系统就必须在内存选择一个页面将其移出内存，以便为即将调入的页面让出空间。用来选择淘汰哪一页的规则叫做页面置换算法，我们可以把页面置换算法看成是淘汰页面的规则。



种类：

1. OPT 页面置换算法（最佳页面置换算法） ：最佳(Optimal, OPT)置换算法所选择的被淘汰页面将是以后永不使用的，或者是在最长时间内不再被访问的页面,这样可以保证获得最低的缺页率。但由于人们目前无法预知进程在内存下的若千页面中哪个是未来最长时间内不再被访问的，因而该算法无法实现。一般作为衡量其他置换算法的方法。
2. FIFO（First In First Out） 页面置换算法（先进先出页面置换算法） : 总是淘汰最先进入内存的页面，即选择在内存中驻留时间最久的页面进行淘汰。
3. LRU （Least Recently Used）页面置换算法（最近最久未使用页面置换算法） ：LRU 算法赋予每个页面一个访问字段，用来记录一个页面自上次被访问以来所经历的时间 T，当须淘汰一个页面时，选择现有页面中其 T 值最大的，即最近最久未使用的页面予以淘汰。
4. LFU （Least Frequently Used）页面置换算法（最少使用页面置换算法） : 该置换算法选择在之前时期使用最少的页面作为淘汰页。