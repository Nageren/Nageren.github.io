---
title: JVM参数设置
date: 2017-12-24 15:23:50
tags: [java,垃圾回收,tomcat优化,jvm参数]
---

1.tomcat调优:
---

通过优化tomcat提高网站的并发能力。

1.优化配置在conf/ tomcat-users.xml下添加用户：

 ```xml
<role rolename="manager"/>

<rolerolename="manager-gui"/>

<role rolename="admin"/>

<rolerolename="admin-gui"/>

<user username="tomcat" password="tomcat"roles="admin-gui,admin,manager-gui,manager"/>
 ```

启动tomcat，登录查看信息：http://127.0.0.1:8080/

![mark](http://ozaomob5f.bkt.clouddn.com/images/171224/4JG15lBAf3.png?imageslim)

tomcat的运行模式有3种：

1、  bio
默认的模式,性能非常低下,没有经过任何优化处理和支持.

2、  nio
==nio(new I/O)，是Java SE 1.4及后续版本提供的一种新的I/O操作方式(即java.nio包及其子包)。Java nio是一个基于缓冲区、并能提供非阻塞I/O操作的Java API，因此nio也被看成是non-blocking I/O的缩写。它拥有比传统I/O操作(bio)更好的并发运行性能。==

3、  apr
安装起来最困难,但是从操作系统级别来解决异步的IO问题,大幅度的提高性能.

2 .启动NIO模式
---

修改server.xml里的Connector节点,修改protocol为org.apache.coyote.http11.Http11NioProtocol

```xml
<Connector port="8080"   protocol="org.apache.coyote.http11.Http11NioProtocol"    connectionTimeout="20000"  redirectPort="8443"/>
```

3.执行器（线程池）
---

在tomcat中每一个用户请求都是一个线程，所以可以使用线程池提高性能。

开启并且使用

![mark](http://ozaomob5f.bkt.clouddn.com/images/171224/4m3bhaFHK2.png?imageslim)

![mark](http://ozaomob5f.bkt.clouddn.com/images/171224/G3JAla02a2.png?imageslim)

### 参数说明

| Attribute                                | Description                              |
| ---------------------------------------- | ---------------------------------------- |
| threadPriority  （优先级）                    | (int) The thread priority for threads in the executor,  the default is `5` (the value of the`Thread.NORM_PRIORITY` constant) |
| daemon（守护进程）                             | (boolean) Whether the threads should be daemon threads or  not, the default is `true` |
| namePrefix（名称前缀）                         | (String) The name prefix for each thread created by the  executor. The thread name for an individual thread will be `namePrefix+threadNumber` |
| maxThreads（最大线程数）                        | (int) The max number of active threads in this pool,  default is `200` |
| minSpareThreads（最小活跃线程数）                 | (int) The minimum number of threads always kept alive,  default is `25` |
| maxIdleTime(空闲线程等待时间)                    | (int) The number of milliseconds before an idle thread  shutsdown, unless the number of active threads are less or equal to  minSpareThreads. Default value is `60000`(1 minute) |
| maxQueueSize（最大的等待队里数，超过则请求拒绝）           | (int) The maximum number of runnable tasks that can queue  up awaiting execution before we reject them. Default value is `Integer.MAX_VALUE` |
| prestartminSpareThreads（是否在启动时就生成minSpareThreads个线程） | (boolean) Whether minSpareThreads should be started when  starting the Executor or not, the default is `false` |
| threadRenewalDelay（重建线程的时间间隔）            | (long) If a [ThreadLocalLeakPreventionListener](http://127.0.0.1:8080/docs/config/listeners.html) is configured, it will notify this  executor about stopped contexts. After a context is stopped, threads in the  pool are renewed. To avoid renewing all threads at the same time, this option  sets a delay between renewal of any 2 threads. The value is in ms, default  value is `1000` ms. If value is negative, threads  are not renewed.  。重建线程池内的线程时，为了避免线程同时重建，每隔threadRenewalDelay（单位： ms ）重建一个线程。默认值为1000 ，设置为负则不重建 |



4. 连接器（Connector）
---

Connector是Tomcat接收请求的入口，每个Connector有自己专属的监听端口

Connector有两种：HTTP Connector和AJPConnector

.......

这里东西很多   具体看 文档

5.禁用AJP连接器	
---

AJP（Apache JServerProtocol）

AJPv13协议是面向包的。WEB服务器和Servlet容器通过TCP连接来交互；为了节省SOCKET创建的昂贵代价，WEB服务器会尝试维护一个永久TCP连接到servlet容器，并且在多个请求和响应周期过程会重用连接。

![mark](http://ozaomob5f.bkt.clouddn.com/images/171224/g3mc2K6aHg.png?imageslim)

我们一般是使用Nginx+tomcat的架构，所以用不着AJP协议，所以把AJP连接器禁用。

![mark](http://ozaomob5f.bkt.clouddn.com/images/171224/meDmH1D599.png?imageslim)

在管理界面中看不到ajp了：

![mark](http://ozaomob5f.bkt.clouddn.com/images/171224/kHaGCDg8Kk.png?imageslim)



6.JVM参数的优化
---

适当调整Tomcat的运行JVM参数可以提升整体性能

### 6.1 JVM内存模型

#### 6.1.1.  Java栈

​	Java栈是与每一个线程关联的，JVM在创建每一个线程的时候，会分配一定的栈空间给线程。它主要用来存储线程执行过程中的局部变量，方法的返回值，以及方法调用上下文。栈空间随着线程的终止而释放。

#### 6.1.2.  Java堆

​	Java中堆是由所有的线程共享的一块内存区域，堆用来保存各种JAVA对象，比如数组，线程对象等。

#### 6.1.3.  Java堆的分区	

​	JVM堆一般又可以分为以下三部分：

​	![mark](http://ozaomob5f.bkt.clouddn.com/images/171224/mH4k11bkBl.png?imageslim)



◆ Young 年轻区（代）

Young区被划分为三部分，Eden区和两个大小严格相同的Survivor区，其中，Survivor区间中，某一时刻只有其中一个是被使用的，另外一个留做垃圾收集时复制对象用，在Eden区间变满的时候， GC就会将存活的对象移到空闲的Survivor区间中，根据JVM的策略，在经过几次垃圾收集后，任然存活于Survivor的对象将被移动到Tenured区间。

 

◆ Tenured 年老区

Tenued区主要保存生命周期长的对象，一般是一些老的对象，当一些对象在Young复制转移一定的次数以后，对象就会被转移到Tenured区，一般如果系统中用了application级别的缓存，缓存中的对象往往会被转移到这一区间。

 

◆ Perm 永久区

Perm代主要保存class,method,filed对象，这部份的空间一般不会溢出，除非一次性加载了很多的类，不过在涉及到热部署的应用服务器的时候，有时候会遇到java.lang.OutOfMemoryError : PermGen space 的错误，造成这个错误的很大原因就有可能是每次都重新部署，但是重新部署后，类的class没有被卸载掉，这样就造成了大量的class对象保存在了perm中，这种情况下，一般重新启动应用服务器可以解决问题。

 

Virtual区：

最大内存和初始内存的差值，就是Virtual区。



#### 6.1.4.设置区大小

![mark](http://ozaomob5f.bkt.clouddn.com/images/171224/d862eA712H.png?imageslim)

◆ Total Heap

-Xms ：指定了JVM初始启动以后初始化内存

-Xmx：指定JVM堆得最大内存，在JVM启动以后，会分配-Xmx参数指定大小的内存给JVM，但是不一定全部使,JVM会根据-Xms参数来调节真正用于JVM的内存

-Xmx -Xms之差就是三个Virtual空间的大小

 

◆ Young Generation

-XX:NewRatio=8意味着tenured 和 young的比值8：1，这样eden+2*survivor=1/9

 

堆内存

-XX:SurvivorRatio=32意味着eden和一个survivor的比值是32：1，这样一个Survivor就占Young区的1/34.

-Xmn 参数设置了年轻代的大小

 

◆ Perm Generation

-XX:PermSize=16M -XX:MaxPermSize=64M

Thread Stack

-XX:Xss=128K



### 6.2. 常用参数

修改文件：bin/catalina.sh

JAVA_OPTS="-Dfile.encoding=UTF-8-server -Xms1024m -Xmx1024m -XX:NewSize=512m -XX:MaxNewSize=512m-XX:PermSize=256m -XX:MaxPermSize=256m -XX:NewRatio=2-XX:MaxTenuringThreshold=50 -XX:+DisableExplicitGC"

参数说明：

1、  file.encoding 默认文件编码

2、  -Xmx1024m  设置JVM最大可用内存为1024MB

3、  -Xms1024m  设置JVM最小内存为1024m。此值可以设置与-Xmx相同，以避免每次垃圾回收完成后JVM重新分配内存。

4、  -XX:NewSize  设置年轻代大小

5、  XX:MaxNewSize 设置最大的年轻代大小

6、  -XX:PermSize  设置永久代大小

7、  -XX:MaxPermSize 设置最大永久代大小

8、  -XX:NewRatio=4:设置年轻代（包括Eden和两个Survivor区）与终身代的比值（除去永久代）。设置为4，则年轻代与终身代所占比值为1：4，年轻代占整个堆栈的1/5

9、  -XX:MaxTenuringThreshold=0：设置垃圾最大年龄，默认为：15。如果设置为0的话，则年轻代对象不经过Survivor区，直接进入年老代。对于年老代比较多的应用，可以提高效率。如果将此值设置为一个较大值，则年轻代对象会在Survivor区进行多次复制，这样可以增加对象再年轻代的存活时间，增加在年轻代即被回收的概论。

10、             -XX:+DisableExplicitGC这个将会忽略手动调用GC的代码使得System.gc()的调用就会变成一个空调用，完全不会触发任何GC

### 6.3. 在tomcat中设置JVM参数

#### 6.3.1.  linux

修改bin/catalina.sh文件参数（第一行）

JAVA_OPTS="-Dfile.encoding=UTF-8-server -Xms1024m -Xmx2048m -XX:NewSize=512m -XX:MaxNewSize=1024m-XX:PermSize=256m -XX:MaxPermSize=256m -XX:MaxTenuringThreshold=10-XX:NewRatio=2 -XX:+DisableExplicitGC"

![mark](http://ozaomob5f.bkt.clouddn.com/images/171224/9Hhig3kk9B.png?imageslim)