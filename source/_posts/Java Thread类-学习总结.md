---
title: Java Thread类-学习总结
date: 2026-03-23
tags: [java, 基础]
---

# Java Thread类-学习总结

**日期：2026-03-23**

## 一、今日学习内容汇总

### 1. Thread类的定义和作用

**定义**：Thread类是Java中用于创建和管理线程的核心类，位于`java.lang`包中。它实现了`Runnable`接口，提供了线程的生命周期管理方法。

**核心作用**：
- **创建线程**：通过继承Thread类或实现Runnable接口创建线程
- **启动线程**：调用`start()`方法启动线程
- **管理线程**：提供了线程的暂停、恢复、中断等方法
- **线程同步**：支持线程间的同步和通信

**应用场景**：
- **后台任务**：执行耗时操作（如文件下载、数据处理）
- **并行计算**：利用多核CPU进行并行计算，提高程序运行效率
- **实时响应**：处理实时数据（如传感器数据、网络消息），确保系统及时响应
- **多任务处理**：同时处理多个独立任务，提高系统吞吐量

**代码示例**：
```java
// 继承Thread类
public class MyThread extends Thread {
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println("Thread: " + i);
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

// 使用示例
public class Main {
    public static void main(String[] args) {
        MyThread thread = new MyThread();
        thread.start(); // 启动线程
    }
}
```

### 2. Thread类的常用方法

**start()**：
- **作用**：启动线程，使线程进入就绪状态
- **注意**：不能重复调用start()方法，否则会抛出IllegalThreadStateException

**run()**：
- **作用**：线程的任务逻辑，线程启动后会自动调用run()方法
- **注意**：直接调用run()方法不会启动新线程，只是普通方法调用

**sleep(long millis)**：
- **作用**：使当前线程暂停执行指定时间（毫秒）
- **特点**：睡眠时不会释放锁，其他线程无法访问该锁保护的资源
- **注意**：会抛出InterruptedException异常，需要捕获或声明抛出

**join()**：
- **作用**：等待该线程终止，直到该线程执行完毕
- **场景**：主线程等待子线程执行完毕后再继续执行

**interrupt()**：
- **作用**：中断线程，设置线程的中断状态为true
- **注意**：不会立即终止线程，需要线程自己检查中断状态并响应

**yield()**：
- **作用**：暂停当前正在执行的线程对象，并执行其他线程
- **特点**：礼让线程，但不保证其他线程会立即执行，取决于CPU调度

**代码示例**：
```java
// join()方法示例
public class JoinExample {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                System.out.println("Thread: " + i);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        thread.start();
        thread.join(); // 主线程等待子线程执行完毕
        System.out.println("Main thread: Done");
    }
}
```

### 3. Thread类的线程状态

**NEW**：
- **状态**：线程对象已创建，但尚未调用start()方法
- **特点**：线程尚未启动，处于初始状态

**RUNNABLE**：
- **状态**：线程已启动，正在运行或等待CPU调度
- **特点**：包含就绪（Ready）和运行（Running）两种子状态
- **转换**：调用start()方法后进入RUNNABLE状态

**BLOCKED**：
- **状态**：线程等待获取锁，进入阻塞状态
- **场景**：尝试访问被其他线程锁定的同步代码块或方法
- **转换**：获取锁后进入RUNNABLE状态

**WAITING**：
- **状态**：线程无限期等待，直到被其他线程唤醒
- **方法**：wait()、join()、LockSupport.park()
- **转换**：被notify()、notifyAll()或interrupt()唤醒后进入RUNNABLE状态

**TIMED_WAITING**：
- **状态**：线程在指定时间内等待，超时后自动苏醒
- **方法**：sleep(long)、wait(long)、join(long)、LockSupport.parkNanos()、LockSupport.parkUntil()
- **转换**：超时或被唤醒后进入RUNNABLE状态

**TERMINATED**：
- **状态**：线程执行完毕或异常终止
- **特点**：线程生命周期结束，无法再启动
- **转换**：run()方法执行完毕或抛出未捕获异常

**线程状态转换关系**：
```
NEW → RUNNABLE → BLOCKED → RUNNABLE
       ↓         ↑
       ↓         ↑
       WAITING ←→ RUNNABLE
       ↓         ↑
       ↓         ↑
       TIMED_WAITING ←→ RUNNABLE
       ↓
       TERMINATED
```

**代码示例**：
```java
// 线程状态转换示例
public class ThreadStateExample {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(() -> {
            System.out.println("Thread state: " + Thread.currentThread().getState());
            synchronized (ThreadStateExample.class) {
                try {
                    ThreadStateExample.class.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        
        System.out.println("Thread state: " + thread.getState()); // NEW
        thread.start();
        Thread.sleep(100);
        System.out.println("Thread state: " + thread.getState()); // BLOCKED
        
        synchronized (ThreadStateExample.class) {
            ThreadStateExample.class.notify();
        }
        
        Thread.sleep(100);
        System.out.println("Thread state: " + thread.getState()); // TERMINATED
    }
}
```

## 二、今日记忆口诀汇总

### 1. Thread类
> Thread类是线程基，创建线程有两种；
> 继承Thread重写run，实现Runnable接口；
> start()方法来启动，run()方法是任务；
> 多任务并行执行，提高效率响应快！

### 2. Thread方法
> Thread方法有哪些，start run sleep join；
> start()启动线程，run()方法是任务；
> sleep()睡眠带锁，join()等待线程完；
> interrupt()中断线程，yield()礼让不保证！

### 3. 线程状态
> 线程状态有六种，NEW RUNNABLE BLOCKED；
> WAITING无限期等，TIMED_WAITING计时等；
> TERMINATED已终止，状态转换要记清；
> NEW→RUNNABLE start()，RUNNABLE→BLOCKED等锁；
> WAITING需唤醒，TIMED_WAITING超时醒；
> TERMINATED run()完，线程生命周期结束！

## 三、学习时长统计

本次学习时长：约1.5小时

## 四、明日学习计划

### 复习任务
1. **复习Thread类的定义和作用**
   - 重读今日学习内容，重点掌握线程的创建方式和应用场景
   - 尝试口头复述Thread类的记忆口诀

2. **复习Thread类的常用方法**
   - 重点掌握start()、run()、sleep()、join()、interrupt()等方法的作用
   - 尝试口头复述Thread方法的记忆口诀

3. **复习线程状态**
   - 重点掌握线程状态的转换关系
   - 尝试口头复述线程状态的记忆口诀

### 新学任务
1. **学习线程同步机制**
   - 学习synchronized关键字的使用
   - 学习Lock接口的使用
   - 理解线程同步的原理和应用场景

2. **学习线程间通信**
   - 学习wait()、notify()、notifyAll()方法的使用
   - 理解线程间通信的原理和应用场景

3. **学习线程池**
   - 学习Executor框架的使用
   - 学习ThreadPoolExecutor的配置和使用
   - 理解线程池的原理和应用场景

## 五、学习心得

通过费曼学习法的问答模式，我对Thread类的核心知识点有了更深入的理解。特别是线程状态和转换关系，通过具体代码示例，我对这些知识点的掌握更加牢固。

Thread类是Java多线程编程的基础，掌握它对于编写高效、稳定的多线程程序至关重要。这种"问-答-纠错-深化"的学习方法非常有效，能够暴露自己的认知盲区，通过纠正错误来加深理解。我将继续使用这种方法来学习其他Java多线程相关的知识点。