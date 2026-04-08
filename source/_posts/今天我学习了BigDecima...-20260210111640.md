---
title: Java BigDecimal API - 学习总结
date: 2026-02-10
tags: [java, 基础]
---

# Java BigDecimal API - 学习总结

**学习日期**：2026-02-10  
**学习时长**：约 30 分钟  
**学习方法**：费曼学习法（问答互动）

---

## 一、今日学习内容汇总

### 1. 为什么使用 BigDecimal

**知识点**：
- **核心原因**：float 和 double 是二进制浮点数，无法精确表示如 0.1 这类十进制小数（因为 1/10 在二进制中是无限循环小数）
- **后果**：金融计算会出现舍入误差（如 0.1 + 0.2 != 0.3）
- **解决**：BigDecimal 基于十进制，支持任意精度，完全避免舍入误差

**实际应用场景**：
- ✅ 金融计算（如银行账户、交易金额）
- ✅ 科学计算（需要高精度）
- ✅ 任何需要精确小数计算的场景

### 2. 如何正确创建 BigDecimal

**知识点**：
- ❌ 避免：`new BigDecimal(0.1)` → 因为 0.1 作为 double 已经不精确
- ✅ 推荐：
  - `new BigDecimal("0.1")` → 字符串构造，精确无误
  - `BigDecimal.valueOf(0.1)` → 静态工厂方法，内部做了安全转换

**实际应用场景**：
- ✅ 从用户输入、配置文件或数据库读取数值时，使用字符串构造
- ✅ 从 double 变量转换时，使用 `BigDecimal.valueOf()`

### 3. BigDecimal 的不可变性

**知识点**：
- 所有运算方法（add, subtract, multiply, divide）都返回新对象，原对象不变
- 必须用变量接收结果：`a = a.add(b);`

**实际应用场景**：
- ✅ 链式调用：`BigDecimal result = a.add(b).multiply(c).subtract(d);`
- ✅ 避免副作用：多个方法可以安全地使用同一个 BigDecimal 对象

### 4. 除法必须指定舍入模式

**知识点**：
- `divide()` 若不能整除且未指定精度和舍入方式，会抛出 `ArithmeticException`
- 正确写法：`a.divide(b, 10, RoundingMode.HALF_UP);`

**常见的 RoundingMode**：
- `HALF_UP`：四舍五入（最常用）
- `HALF_DOWN`：五舍六入
- `DOWN`：直接截断（向零舍入）
- `UP`：远离零舍入
- `CEILING`：向正无穷方向舍入
- `FLOOR`：向负无穷方向舍入

**实际应用场景**：
- ✅ 金融计算：保留 2 位小数，使用 `RoundingMode.HALF_UP`
- ✅ 科学计算：根据精度要求选择合适的舍入模式

### 5. 正确比较 BigDecimal

**知识点**：
- `.equals()` 比较值 + 小数位数（scale） → `1.0 != 1.00`
- `.compareTo()` 只比数值大小 → `1.0 == 1.00`
- 判断相等：`a.compareTo(b) == 0`

**实际应用场景**：
- ✅ 数值比较：`if (balance.compareTo(BigDecimal.ZERO) > 0)`
- ✅ 排序：使用 `compareTo()` 方法
- ✅ 集合去重：使用 `.equals()` 方法

## 二、今日记忆口诀汇总

1. **为什么用 BigDecimal**：
   > **金钱不用 double，二进浮点会失准；十进 BigDecimal，精确可靠保分文。**

2. **如何创建 BigDecimal**：
   > **构造要用字符串，double 入坑要吃苦；valueOf 也可行，安全转换有保障。**

3. **BigDecimal 的不可变性**：
   > **BigD 不可变，运算返回新对象；忘记赋值等于白算，结果丢了别喊冤。**

4. **除法必须指定舍入模式**：
   > **除法不设舍入，程序立马崩；scale 加 mode，安全又从容。**

5. **正确比较 BigDecimal**：
   > **比值用 compareTo，equals 看精度；1.0 和 1.00，equals 说不行！**

## 三、学习时长统计

- **开始时间**：2026-02-10 11:16
- **结束时间**：2026-02-10 11:46
- **总时长**：约 30 分钟

## 四、明日学习计划

### 建议复习内容
1. 复习今日学习的 BigDecimal API 知识点，重点记忆口诀
2. 练习编写代码，使用 BigDecimal 进行金融计算
3. 重点复习 BigDecimal 的创建方式、不可变性和比较方法

### 建议学习内容
1. **Java 集合框架**：List、Set、Map 的区别和使用
2. **泛型**：泛型的使用、通配符、类型擦除
3. **Java 8 新特性**：Lambda 表达式、Stream API

### 需要补充的知识点
1. **BigDecimal 的性能优化**：避免在循环中重复创建相同值的 BigDecimal
2. **BigDecimal 与数据库交互**：如何在 JDBC 中使用 BigDecimal
3. **BigDecimal 的其他方法**：`setScale()`、`abs()`、`max()`、`min()` 等
4. **BigDecimal 的精度控制**：如何处理不同精度的 BigDecimal 运算

## 五、学习心得

通过今天的学习，我对 Java 中 BigDecimal API 有了更深入的理解。以前只知道 BigDecimal 用于金融计算，现在清楚了它的核心原理、正确创建方式、不可变性、除法舍入和比较方法等重要知识点。特别是 BigDecimal 的不可变性和比较方法，这些都是实际开发中容易出错的地方。

费曼学习法的问答方式让我能够主动思考，而不是被动接受知识。虽然有些问题一开始回答不够完整，甚至有错误，但通过老师的详细讲解和纠正，我能够更好地理解和记忆这些知识点。记忆口诀的总结也让我能够快速回顾和巩固所学内容。

明天计划学习 Java 集合框架，继续深入理解 Java 的核心特性。