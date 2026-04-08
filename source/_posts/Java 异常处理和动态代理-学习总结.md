---
title: Java 异常处理和动态代理学习总结
date: 2026-03-10
tags: [java, 基础]
---

# Java 异常处理和动态代理学习总结

**日期：2026-03-10**

## 一、知识点

1. **异常处理**
   - 异常的定义和体系结构
   - try-catch-finally的执行顺序
   - finally的特殊情况
   - return的执行顺序

2. **动态代理**
   - 动态代理的定义和与静态代理的区别
   - InvocationHandler接口
   - Proxy.newProxyInstance()方法
   - 动态代理的实现原理
   - 动态代理的实战应用

## 二、核心洞察

### 异常处理部分

**异常的定义和体系结构：**
- 异常是程序在执行过程中发生的中断正常流程的事件，分为编译时异常（Checked）和运行时异常（Runtime）
- 异常体系结构：Throwable → Error（系统错误）和Exception（程序异常）
  - Error：OutOfMemoryError（内存溢出）、StackOverflowError（栈溢出）
  - Exception：
    - RuntimeException：NullPointerException（空指针）、ArrayIndexOutOfBoundsException（数组越界）、ClassCastException（类型转换异常）
    - Checked Exception：IOException（IO异常）、SQLException（SQL异常）、ClassNotFoundException（类未找到）

**try-catch-finally的执行顺序：**
- 正常执行：try → finally → 方法返回
- 异常执行：try → catch → finally → 方法返回
- finally特殊情况：System.exit()、死循环、断电时不执行
- return执行顺序：先计算返回值，再执行finally，finally有return会覆盖之前的返回值

### 动态代理部分

**动态代理的定义和与静态代理的区别：**
- 动态代理是在运行时动态生成代理类，通过InvocationHandler接口拦截方法调用，实现无侵入式的功能增强
- 与静态代理的区别：
  - 静态代理：编译时确定代理类，一个目标类对应一个代理类
  - 动态代理：运行时生成代理类，一个InvocationHandler可以代理多个目标类

**动态代理的实现原理：**
- InvocationHandler接口：定义方法拦截逻辑，invoke()方法拦截方法调用
- Proxy.newProxyInstance()：动态生成代理对象，需要类加载器、接口数组、InvocationHandler
- 方法拦截原理：调用代理方法 → invoke()拦截 → 执行目标方法 → 返回结果

**动态代理的实战应用：**
- Spring AOP：基于动态代理实现切面编程（日志、事务、权限控制）
- Spring事务管理：@Transactional注解基于动态代理实现
- RPC调用：Dubbo使用动态代理实现远程调用
- 日志拦截：拦截方法调用，记录执行时间和参数

## 三、记忆口诀

### 异常处理

1. **异常体系**：异常体系Throwable，Error和Exception分；Error是系统错误，Exception是程序异常；Runtime运行时异常，Checked受检要处理；空指针和数组越界，IO和SQL异常！

2. **try-catch-finally**：try正常finally跟，异常catch再finally；finally块必执行，exit和死循环除外；return先算后finally，finally有return覆盖；资源释放放finally，防止泄漏要牢记！

### 动态代理

1. **动态代理基本概念**：动态代理运行时生，InvocationHandler拦截；静态代理编译时定，动态代理更灵活；AOP事务和RPC，动态代理是基础；无侵入式功能增强，代码解耦又优雅！

2. **动态代理实现原理**：InvocationHandler拦截，invoke方法核心；Proxy.newProxyInstance，运行时生成代理；方法调用被拦截，前后逻辑自由加；日志事务和权限，动态代理来实现！

## 四、实战锦囊

### 异常处理实战

1. **金融级异常处理**
   - 区分业务异常和系统异常
   - 业务异常：AccountNotFoundException（账户不存在）、InsufficientBalanceException（余额不足）
   - 系统异常：SQLException（数据库错误）、IOException（IO错误）
   - 使用try-catch-finally确保资源释放

2. **高并发异常处理**
   - 分布式锁处理InterruptedException
   - 使用finally确保锁的释放
   - 捕获运行时异常（NullPointerException、ClassCastException）

3. **资源释放**
   - 数据库连接、文件流、网络连接等资源必须在finally中释放
   - 使用try-with-resources语法（Java 7+）自动关闭资源

### 动态代理实战

1. **Spring AOP应用**
   - @Aspect注解定义切面
   - @Around环绕通知拦截方法调用
   - @Before、@After前置和后置通知

2. **事务管理**
   - @Transactional注解实现声明式事务
   - 动态代理拦截方法调用，自动开启和提交事务
   - 异常时自动回滚事务

3. **RPC调用**
   - Dubbo使用动态代理实现远程调用
   - @Reference注解注入代理对象
   - 方法调用时动态代理拦截，发起远程调用

## 五、用户回答内容

### 问题1：什么是异常？Java中的异常体系结构是怎样的？

**用户回答：**
- 定义：程序运行时发生的异常
- 继承关系：throwable--是所有异常的顶端父类，其中他有两个子类——exception（常见的有RuntimeException运行时异常和CheckedException受检异常）和error（jvm错误常见的有内存溢出和栈溢出）

### 问题2：什么是动态代理？它与静态代理有什么区别？

**用户回答：**
- 动态代理就是分离方法调用者和代码，充当一个中间商的角色，防止侵入式代码，动态代理要实现相同的方法
- 实现方式：Proxy类，中的newProxyInstance()方法

### 问题3：try-catch-finally的执行顺序是怎样的？

**用户回答：**
- 正常：直接执行try{}里面的代码
- 异常执行：一旦发生异常就执行catch{}
- finally：一定会执行

### 问题4：动态代理的实现原理是什么？

**用户回答：**
- 不会啊

## 六、源码/扩展阅读链接

1. **Java异常体系**
   - Java官方文档：https://docs.oracle.com/javase/8/docs/api/java/lang/Throwable.html
   - 异常处理最佳实践：https://docs.oracle.com/javase/tutorial/essential/exceptions/

2. **动态代理**
   - Proxy类源码：https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/Proxy.html
   - InvocationHandler接口：https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/InvocationHandler.html

3. **Spring AOP**
   - Spring AOP官方文档：https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#aop
   - Spring事务管理：https://docs.spring.io/spring-framework/docs/current/reference/html/data-access.html#transaction

## 七、明日学习计划

### 复习任务（强关联）

1. **复习异常处理**
   - 打开 `D:\桌面\笔记文件夹\Note.md`，重读【2026-03-10 Java 异常处理和动态代理】章节中关于'try-catch-finally的执行顺序'的实战锦囊
   - 尝试口头复述异常体系的记忆口诀："异常体系Throwable，Error和Exception分"

2. **复习动态代理**
   - 重读【2026-03-10 Java 异常处理和动态代理】章节中关于'动态代理的实现原理'的核心洞察
   - 尝试口头复述动态代理的记忆口诀："InvocationHandler拦截，invoke方法核心"

### 新学任务

1. **学习Optional类**
   - 学习Optional类的定义和用途（解决空指针问题）
   - 掌握Optional的常用方法：of()、ofNullable()、isPresent()、ifPresent()、orElse()、orElseGet()、orElseThrow()
   - 理解Optional与Stream流的结合使用

2. **探索Java 8的其他新特性**
   - 学习接口的默认方法（default关键字）
   - 学习接口的静态方法（static关键字）
   - 理解这些新特性与之前学习的Lambda表达式、Stream流、方法引用的联系

**知识串联预告：**
- Optional类是Java 8引入的容器类，用于更优雅地处理空值问题，与Stream流、方法引用等Java 8新特性形成完整的函数式编程工具链
- 接口的默认方法和静态方法是Java 8对接口的增强，与之前学习的Lambda表达式、Stream流共同构成了Java 8函数式编程的基础

## 八、补充说明

1. **异常处理注意事项**
   - 不要捕获Exception或Throwable，应该捕获具体的异常类型
   - 不要在finally块中使用return，会覆盖try/catch中的return
   - 不要在finally块中抛出异常，会掩盖try/catch中的异常
   - 使用try-with-resources语法（Java 7+）自动关闭资源

2. **动态代理注意事项**
   - 动态代理只能代理接口，不能代理类
   - 如果需要代理类，可以使用CGLIB（Spring默认使用）
   - 在invoke()方法中不要使用proxy对象，否则会死循环
   - 动态代理的性能开销比静态代理大，但在实际应用中可以忽略

3. **学习建议**
   - 异常处理和动态代理是Java高级特性，需要多练习才能掌握
   - 建议结合Spring框架学习动态代理的实际应用
   - 异常处理是编程基本功，需要在实际项目中不断积累经验