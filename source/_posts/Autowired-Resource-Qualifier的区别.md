---
title: '@Autowired @Resource @Qualifier的区别'
date: 2017-12-28 20:42:04
tags: [Autowired ,Resource ,Qualifier, Component]
---

Spring中Bean的配置
===

```xml
id:名称 保证唯一 不要出现特殊符号
class:类的全限定名(不论是继承类还是类本身都可以)
scope:声明bean的作用范围
常见值:
        singlton:单例 (默认值)
        prototype:多例
```

Spring常用的注解
===

![mark](http://ozaomob5f.bkt.clouddn.com/images/171228/GeDba62BeJ.png?imageslim)

  ![mark](http://ozaomob5f.bkt.clouddn.com/images/171228/lm1faI8clm.png?imageslim)

![mark](http://ozaomob5f.bkt.clouddn.com/images/171228/3D5fjCHiGi.png?imageslim)

Spring中@Autowired,@Resource ,@Qualifie
===



```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://www.springframework.org/schema/beans" xmlns:context="http://www.springframework.org/schema/context" xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd ">
	<!-- 配置注解代替xml配置Bean开关
		 base-package: 指定扫描哪些包中的注解
		 	1.可以使用逗号分隔多个包名
		 	2.cn.itcast => 所有以cn.itcast开头的包都包含
	 -->
	<context:component-scan base-package="cn.itcast"></context:component-scan>
	<bean name="car1" class="cn.itcast.domain.Car" >
		<property name="name" value="布加迪威航" ></property>
		<property name="color" value="绿色" ></property>
	</bean>
	
	<bean name="car2" class="cn.itcast.domain.Car" >
		<property name="name" value="兰博基尼" ></property>
		<property name="color" value="粉色" ></property>
	</bean>
</beans>
```

这种情况自动注入就会失败

```java
@Autowired 
private  Car  car ;
报错: 自动注入失败,没有找到唯一的car对象
```



解决1 :

```java
@Autowired 
@Qualifie("car1")
private  Car  car ;

@Autowired 
@Qualifie("car2")
private  Car  car ;
```

解决2 :

```java
@Resource(name="car")
private  Car  car ;
```











