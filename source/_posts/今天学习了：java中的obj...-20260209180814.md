---
title: Java Object API 与 Objects 工具类 - 学习总结
date: 2026-02-09
tags: [java, 基础]
---

# Java Object API 与 Objects 工具类 - 学习总结

**学习日期**：2026-02-09  
**学习时长**：约 15 分钟  
**学习方法**：费曼学习法（问答互动）

---

## 一、今日学习内容汇总

### 1. Object 类的 equals() 方法

**知识点**：
- **位置**：`java.lang.Object` 类（所有类的父类）
- **默认实现**：`public boolean equals(Object obj) { return (this == obj); }`（比较引用是否相同）
- **重写目的**：根据对象的内容（而不是内存地址）判断两个对象是否“相等”
- **使用场景**：当需要定义“业务意义上的相等”时重写

**实际应用场景**：
- ✅ 比较两个对象的内容是否相同（如用户登录时比较用户名密码）
- ✅ 在集合（如 HashMap、HashSet）中判断元素是否存在

### 2. Objects 工具类的 equals() 方法

**知识点**：
- **位置**：`java.util.Objects` 工具类（注意是 Objects，带 s）
- **作用**：安全地比较两个对象是否相等，自动处理 null 值，避免空指针异常（NPE）
- **源码逻辑**：
  ```java
  public static boolean equals(Object a, Object b) {
      return (a == b) || (a != null && a.equals(b));
  }
  ```
- **优点**：
  - 如果 a 和 b 都是 null，返回 true
  - 如果其中一个为 null，另一个不是，返回 false
  - 如果都不为 null，则调用 a.equals(b)（也就是重写的那个 equals）

**实际应用场景**：
- ✅ 在重写 equals() 方法时使用，简化代码并避免 NPE
- ✅ 安全地比较两个可能为 null 的对象

### 3. 重写 equals() 的最佳实践

**知识点**：
- **必须遵守的契约**：自反性、对称性、传递性、一致性、非空性
- **最佳实践**：
  1. 优先使用 Objects.equals() 比较字段
  2. 同时重写 hashCode()（可用 Objects.hash(field1, field2, …)）
  3. 不要直接调用 obj.equals(other) 而不判 null，容易 NPE

**示例代码**：
```java
public class Person {
    private String name;
    private int age;

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        // 使用 Objects.equals 安全比较
        return age == person.age && Objects.equals(name, person.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}
```

### 4. 三者关系总结

| 名称 | 所属 | 作用 | 是否需要重写 | 是否处理 null |
|------|------|------|------------|--------------|
| Object.equals() | Object 类 | 默认按引用比较 | ✅ 通常要重写（按内容） | ❌ 不处理（会 NPE） |
| Objects.equals(a, b) | 工具类 Objects | 安全比较两个对象 | ❌ 不能重写（静态方法） | ✅ 自动处理 null |
| 你重写的 equals() | 你的类 | 按业务逻辑判断相等 | ✅ 是重写 Object.equals() | 取决于你怎么写（建议用 Objects.equals） |

## 二、今日记忆口诀汇总

> **Object equals 比引用，重写之后比内容**
> **Objects equals 工具强，自动处理 null 防 NPE**
> **重写 equals 要配对，hashCode 一起改**
> **最佳实践用 Objects，代码简洁又安全**

## 三、学习时长统计

- **开始时间**：2026-02-09 18:08
- **结束时间**：2026-02-09 18:23
- **总时长**：约 15 分钟

## 四、明日学习计划

### 建议复习内容
1. 复习今日学习的 Object API 和 Objects 工具类知识点，重点记忆口诀
2. 练习编写代码，正确重写 equals() 和 hashCode() 方法，使用 Objects 工具类
3. 重点复习 Objects.equals() 的使用场景和优势

### 建议学习内容
1. **Java 集合框架**：List、Set、Map 的区别和使用
2. **泛型**：泛型的使用、通配符、类型擦除
3. **Java 8 新特性**：Lambda 表达式、Stream API

### 需要补充的知识点
1. **Objects 工具类的其他方法**：`Objects.hash()`、`Objects.toString()`、`Objects.requireNonNull()` 等
2. **equals() 和 hashCode() 的契约**：详细了解违反契约的后果
3. **深拷贝和浅拷贝**：使用 Objects 工具类辅助实现

## 五、学习心得

通过今天的学习，我对 Java 中 Object 类的 equals() 方法和 Objects 工具类的 equals() 方法有了更清晰的认识。以前经常混淆这两个方法，现在明白了它们的区别和联系。特别是 Objects.equals() 方法可以自动处理 null 值，避免空指针异常，这在实际开发中非常有用。

费曼学习法的问答方式让我能够主动思考，而不是被动接受知识。通过老师的详细讲解，我能够更好地理解和记忆这些知识点。记忆口诀的总结也让我能够快速回顾和巩固所学内容。

明天计划学习 Java 集合框架，继续深入理解 Java 的核心特性。