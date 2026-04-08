---
title: Java线程池工作原理
date: 2026-03-25
tags: [java, 基础]
---

# Java学习知识库

## 2026-03-25 Java线程池工作原理

### 知识点
线程池的工作原理，包括线程池的组成、任务执行流程、线程池状态

### 核心洞察
1. **线程池的组成**：
   - 核心线程数（corePoolSize）：线程池长期保持的线程数量，即使空闲也不回收（除非设置allowCoreThreadTimeOut）。
   - 最大线程数（maximumPoolSize）：线程池允许的最大线程数，核心线程忙且队列满时创建新线程直到此上限。
   - 工作队列（workQueue）：存储等待执行的任务，类型有ArrayBlockingQueue、LinkedBlockingQueue、SynchronousQueue等。
   - 拒绝策略（RejectedExecutionHandler）：队列满且线程数达上限时的处理策略，如AbortPolicy、CallerRunsPolicy等。
   - 其他组件：线程工厂（ThreadFactory）、keepAliveTime（非核心线程空闲存活时间）等。

2. **任务执行流程**：
   - 提交任务时，先检查核心线程数：未达上限则创建核心线程执行任务。
   - 核心线程满则检查队列：队列未满则入队等待。
   - 队列满则检查最大线程数：未达上限则创建非核心线程执行任务。
   - 最大线程数满则触发拒绝策略。
   - 线程执行完任务后继续从队列获取任务，实现线程复用。
   - 任务执行异常时，线程会被终止并创建新线程替代。

3. **线程池状态**：
   - RUNNING：运行状态，接收新任务并处理队列任务。
   - STOP：停止状态，不接收新任务，中断正在执行的任务，清空队列。
   - TIDYING：整理状态，所有任务执行完毕，所有线程终止，执行terminated()方法。
   - TERMINATED：终止状态，terminated()方法执行完毕，线程池完全终止。

### 记忆口诀
线程池，四状态，RUNNING最繁忙，
STOP中断清队列，TIDYING整理忙，
TERMINATED终收场。
核心线程长期在，最大线程应急忙，
队列满时拒绝上，线程复用效率强。

### 实战锦囊
1. **核心线程数配置**：通常设置为CPU核心数或CPU核心数+1，充分利用CPU资源。
2. **最大线程数配置**：通常设置为核心线程数的2-4倍，应对突发流量。
3. **工作队列选择**：互联网应用用LinkedBlockingQueue，金融场景用ArrayBlockingQueue。
4. **拒绝策略选择**：秒杀场景用AbortPolicy配合熔断，后台任务用DiscardOldestPolicy。
5. **监控与优化**：监控活跃线程数、队列长度、任务执行时间，根据实际负载调整参数。
6. **异常处理**：任务中添加try-catch块，避免线程意外终止。
7. **优雅停机**：使用shutdown()方法，等待任务执行完毕后再停止线程池。

### 源码/扩展阅读链接
- ThreadPoolExecutor源码：https://github.com/openjdk/jdk/blob/master/src/java.base/share/classes/java/util/concurrent/ThreadPoolExecutor.java
- Java并发编程实战（书籍）
- Java线程池原理与实践：https://www.baeldung.com/java-thread-pool

### 用户回答整理
1. **线程池的组成**：
   - 核心线程与最大线程：核心线程数是线程池长期保持的线程数量，最大线程数是线程池允许的最大线程数。
   - 工作队列：用于存储等待执行的任务。
   - 拒绝策略：当线程池达到最大线程数且工作队列已满时，对新提交任务的处理策略。
   - 其他组件：线程工厂、keepAliveTime等。

2. **任务执行流程**：
   - 任务提交时的处理：检查核心线程数、队列、最大线程数，决定创建线程还是入队。
   - 线程执行任务的过程：线程从队列获取任务执行，执行完毕后继续获取下一个任务，实现线程复用。
   - 队列满时的处理：触发拒绝策略。
   - 任务执行异常的处理：线程被终止并创建新线程替代。

3. **线程池状态**：
   - RUNNING：运行状态，接收新任务并处理队列任务。
   - STOP：停止状态，不接收新任务，中断正在执行的任务。
   - TIDYING：整理状态，所有任务执行完毕，所有线程终止。
   - TERMINATED：终止状态，线程池完全终止。