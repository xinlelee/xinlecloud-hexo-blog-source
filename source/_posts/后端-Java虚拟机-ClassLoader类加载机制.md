---
title: 后端-Java虚拟机-ClassLoader类加载机制
top: false
cover: false
toc: true
mathjax: true
date: 2020-09-14 22:11:10
password:
summary: 
tags: 类加载器
categories:
- 后端
- Java虚拟机
- 虚拟机类加载机制
Java:
LeetCode:
---

# ClassLoader类加载机制

## 类加载器的命名空间

类加载器的命名空间-是由类加载器本身及其所有父加载器所加载出来的binary name（full class name 组成）
1、在同一个命名空间里，不允许出现两个完全一样的binary name
2、在不同的命名空间中，可以出现两个相同的binary name,但是两者对应的class 对象是相互不能感知到的，也就是class对象的类型是不一样的
3、子加载器的命名空间中的binary name对应的类中可以访问 父加载器命名空间中的binary name对应的类，反之则不行

<!-- more -->

## 类加载机制：全盘负责和双亲委托

“全盘负责”是指当一个ClassLoader装载一个类时，除非显示地使用另一个ClassLoader，则该类所依赖及引用的类也由这个CladdLoader载入。

例如，系统类加载器AppClassLoader加载入口类（含有main方法的类）时，会把main方法所依赖的类及引用的类也载入，依此类推。“全盘负责”机制也可称为当前类加载器负责机制。显然，入口类所依赖的类及引用的类的当前类加载器就是入口类的类加载器。

以上步骤只是调用了ClassLoader.loadClass(name)方法，并没有真正定义类。真正加载class字节码文件生成Class对象由“双亲委派”机制完成。

“双亲委派”是指子类加载器如果没有加载过该目标类，就先委托父类加载器加载该目标类，只有在父类加载器找不到字节码文件的情况下才从自己的类路径中查找并装载目标类。

“双亲委派”机制加载Class的具体过程是：

1. 源ClassLoader先判断该Class是否已加载，如果已加载，则返回Class对象；如果没有则委托给父类加载器。

2. 父类加载器判断是否加载过该Class，如果已加载，则返回Class对象；如果没有则委托给祖父类加载器。

3. 依此类推，直到始祖类加载器（引用类加载器）。

4. 始祖类加载器判断是否加载过该Class，如果已加载，则返回Class对象；如果没有则尝试从其对应的类路径下寻找class字节码文件并载入。如果载入成功，则返回Class对象；如果载入失败，则委托给始祖类加载器的子类加载器。

5. 始祖类加载器的子类加载器尝试从其对应的类路径下寻找class字节码文件并载入。如果载入成功，则返回Class对象；如果载入失败，则委托给始祖类加载器的孙类加载器。

6. 依此类推，直到源ClassLoader。

7. 源ClassLoader尝试从其对应的类路径下寻找class字节码文件并载入。如果载入成功，则返回Class对象；如果载入失败，源ClassLoader不会再委托其子类加载器，而是抛出异常。

   

“双亲委派”机制只是[Java](http://lib.csdn.net/base/java)推荐的机制，并不是强制的机制。

我们可以继承java.lang.ClassLoader类，实现自己的类加载器。如果想保持双亲委派模型，就应该重写findClass(name)方法；如果想破坏双亲委派模型，可以重写loadClass(name)方法。

## 双亲委派的好处 及 如何打破

加载器自上而下分别为，**启动类加载器(Bootstrap ClassLoader), 拓展类加载器(Extension ClassLoader), 系统类加载器(Application ClassLoader) ,** 自定义类加载器(Custom ClassLoader)

其工作原理是，如果其中一个类加载器收到了类加载的请求，它并不会自己去加载而是会将该请求委托给父类的加载器去执行，如果父类加载器还存在父类加载器，则进一步向上委托，如此递归，请求最终到达顶层的启动类加载器。如果父类能加载，则直接返回，如果父类加载不了则交由子类加载，这就是双亲委派模式。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200404144426738.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2IxMzAzMTEwMzM1,size_16,color_FFFFFF,t_70)

双亲委派模型有两个好处：

1. 向上委托给父类加载，父类加载不了再自己加载
2. 避免重复加载，防止Java核心api被篡改

### **如何打破双亲委派模型？**

1. 自定义类加载器，重写loadClass方法；
2. 使用线程上下文类加载器；

## **双亲委派模型破坏史**

1.第一次破坏

由于双亲委派模型是在JDK1.2之后才被引入的，而类加载器和抽象类java.lang.ClassLoader则在JDK1.0时代就已经存在，面对已经存在的用户自定义类加载器的实现代码，Java设计者引入双亲委派模型时不得不做出一些妥协。在此之前，用户去继承java.lang.ClassLoader的唯一目的就是为了重写loadClass()方法，因为虚拟机在进行类加载的时候会调用加载器的私有方法loadClassInternal()，而这个方法唯一逻辑就是去调用自己的loadClass()。

2.第二次破坏

双亲委派模型的第二次“被破坏”是由这个模型自身的缺陷所导致的，双亲委派很好地解决了各个类加载器的基础类的同一问题（越基础的类由越上层的加载器进行加载），基础类之所以称为“基础”，是因为它们总是作为被用户代码调用的API，但世事往往没有绝对的完美。

如果基础类又要调用回用户的代码，那该么办？

一个典型的例子就是JNDI服务，JNDI现在已经是Java的标准服务，
它的代码由启动类加载器去加载（在JDK1.3时放进去的rt.jar），但JNDI的目的就是对资源进行集中管理和查找，它需要调用由独立厂商实现并部署在应用程序的ClassPath下的JNDI接口提供者的代码，但启动类加载器不可能“认识”这些代码。

为了解决这个问题，Java设计团队只好引入了一个不太优雅的设计：线程上下文类加载器(Thread Context ClassLoader)。这个类加载器可以通过java.lang.Thread类的setContextClassLoader()方法进行设置，如果创建线程时还未设置，他将会从父线程中继承一个，如果在应用程序的全局范围内都没有设置过的话，那这个类加载器默认就是应用程序类加载器。

有了线程上下文加载器，JNDI服务就可以使用它去加载所需要的SPI代码，也就是父类加载器请求子类加载器去完成类加载的动作，这种行为实际上就是打通了双亲委派模型层次结构来逆向使用类加载器，实际上已经违背了双亲委派模型的一般性原则，但这也是无可奈何的事情。Java中所有涉及SPI的加载动作基本上都采用这种方式，例如JNDI、**JDBC**、JCE、JAXB和JBI等。

3.第三次破坏

双亲委派模型的第三次“被破坏”是由于用户对程序动态性的追求导致的，这里所说的“动态性”指的是当前一些非常“热门”的名词：代码热替换、模块热部署等，简答的说就是机器不用重启，只要部署上就能用。
OSGi实现模块化热部署的关键则是它自定义的类加载器机制的实现。每一个程序模块(Bundle)都有一个自己的类加载器，当需要更换一个Bundle时，就把Bundle连同类加载器一起换掉以实现代码的热替换。在OSGi幻境下，类加载器不再是双亲委派模型中的树状结构，而是进一步发展为更加复杂的网状结构，当受到类加载请求时，OSGi将按照下面的顺序进行类搜索：
1）将java.＊开头的类委派给父类加载器加载。
2）否则，将委派列表名单内的类委派给父类加载器加载。
3）否则，将Import列表中的类委派给Export这个类的Bundle的类加载器加载。
4）否则，查找当前Bundle的ClassPath，使用自己的类加载器加载。
5）否则，查找类是否在自己的Fragment Bundle中，如果在，则委派给Fragment Bundle的类加载器加载。
6）否则，查找Dynamic Import列表的Bundle，委派给对应Bundle的类加载器加载。
7）否则，类加载器失败。



## 聊聊JDBC是如何破坏双亲委派模型的

```java
// (1)注册驱动
     Class.forName(name);
//(2) 获取链接
     conn = DriverManager.getConnection(url, user, password);
```

**Class.forName(name); ：尝试加载MySQL厂商的Driver驱动，此时执行该类的静态代码块，该代码块中，执行DriverManager的regist静态方法，此时又会触发classloader加载DriverManager，rt.jar中的DriverManager加载时，执行静态代码块，使用SPI的方式来加载jdbc驱动,首先从线程上下文类加载器Thread.currentThread().getContextClassLoader()中获取系统类加载器applicationClassLoader,并使用该类加载器去加载各自厂商实现的Driver**

```java
//权限校验
private static boolean isDriverAllowed(Driver driver, ClassLoader classLoader) {
    boolean result = false;
    if(driver != null) {
        Class<?> aClass = null;
        try {
            aClass =  Class.forName(driver.getClass().getName(), true, classLoader);
        } catch (Exception ex) {
            result = false;
        }

         result = ( aClass == driver.getClass() ) ? true : false;
    }

    return result;
}
```

使用callerCL类加载器去加载driver，然后判断当前加载的Class和driver的Class是否是同一个对象，是则成功，否者失败。这里注册Driver时候类加载器为BootStrap,而callerCL是AppClassloader，由于委托机制，所以这里返回true.也就是注册驱动时候类加载器和使用时候必须是同一个或者具有委托关系时候才会鉴权成功，也就是说才有权限去调用驱动的connect方法。