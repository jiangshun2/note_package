---
title: Java IO流学习总结
date: 2026-03-13
tags: [java, 基础]
---

# Java IO流学习总结

**日期：2026-03-13**

## 一、知识点

1. **字节流和字符流**
   - 字节流：处理任何文件（包括视频、音频），处理单位是字节
   - 字符流：处理文本内容，处理单位是char，底层是字节流+转换器
   - 适用场景：二进制文件用字节流，文本文件用字符流

2. **缓冲流**
   - 定义：装饰器模式的流，添加缓冲区提高I/O性能
   - 工作原理：使用byte[]或char[]数组预先存储数据，批量读写
   - 性能优势：减少I/O操作次数，提高数据传输效率
   - 常见类：BufferedInputStream、BufferedOutputStream、BufferedReader、BufferedWriter

3. **字符集**
   - 定义：字符与编码之间的映射规则
   - 常见字符集：ASCII、ISO-8859-1、GB2312、GBK、GB18030、Unicode（UTF-8）
   - 编码问题：字符集不匹配会导致乱码
   - 解决方案：指定字符集读取和写入文件

## 二、核心洞察

### 字节流和字符流的本质区别

**字节流**是Java IO的基础，它以**字节**（8位）为单位读写数据，直接操作二进制数据，适用于所有类型的文件。字节流不关心数据的含义，只是简单地传输字节。

**字符流**是专门为处理文本数据设计的流，它以**字符**（16位）为单位读写数据，自动处理字符编码转换。字符流的底层仍然是字节流，只是添加了字符编码转换器，将字节转换为字符。

**选择原则**：处理二进制文件（图片、视频、音频等）使用字节流，处理文本文件（.txt、.csv、.properties等）使用字符流。

### 缓冲流的工作原理

**缓冲流**是一种装饰器模式的流，它在普通流的基础上添加了缓冲区，用于提高I/O操作的性能。缓冲流的工作原理可以用"水井-水桶-饮水者"的比喻来理解：

- **水井**：底层流（FileInputStream、FileOutputStream等）
- **水桶**：缓冲区（byte[]或char[]数组）
- **饮水者**：应用程序

**读取过程**：缓冲流从底层流（水井）读取数据到缓冲区（水桶），应用程序（饮水者）从缓冲区（水桶）读取数据。当缓冲区为空时，再次从底层流读取数据到缓冲区。

**写入过程**：应用程序（饮水者）将数据写入缓冲区（水桶），当缓冲区满时，将数据一次性写入底层流（水井）。调用flush()方法可以强制将缓冲区数据写入底层流。

**性能优势**：缓冲流通过批量读写减少了I/O操作次数，显著提高了数据传输效率。缓冲区大小通常为8192字节（8KB），大数据量传输时性能提升明显。

### 字符集和字符编码

**字符集**是字符与编码之间的映射规则，它定义了如何将字符转换为字节，以及如何将字节转换回字符。字符编码是字符集的具体实现方式：

- **字符集**：定义了字符到数字的映射（如：'A' → 65）
- **字符编码**：定义了数字到字节的转换（如：65 → 0x41）

**常见字符集**：
- **ASCII**：7位编码，共128个字符，适用于纯英文文本
- **ISO-8859-1**：8位编码，共256个字符，适用于西欧语言文本
- **GB2312**：双字节编码，共6763个汉字，适用于简体中文文本
- **GBK**：双字节编码，共21886个汉字，适用于中文文本（Windows默认）
- **GB18030**：双字节/四字节编码，共27484个汉字，适用于中文文本（国家标准）
- **Unicode**：统一编码，支持所有语言，适用于国际化文本
- **UTF-8**：变长编码（1-4字节），兼容ASCII，适用于国际化文本（推荐）

**编码问题**：字符集不匹配会导致乱码。常见场景包括：
- 读取时编码与写入时编码不一致
- 平台默认编码不同（Windows：GBK，Linux/Mac：UTF-8）
- 文件编码声明错误

**解决方案**：指定字符集读取和写入文件，确保编码一致。

## 三、记忆口诀

1. **字节流vs字符流**：字节流处理二进制，字符流处理文本；字节流单位是字节，字符流单位是char；字节流更快无转换，字符流自动转编码；二进制文件用字节，文本文件用字符！

2. **缓冲流**：缓冲流加缓冲区，减少I/O操作次；底层数组来存储，批量读写效率高；字节字符都能用，Buffered前缀要记牢；readLine和newLine，额外功能很实用！

3. **字符集**：字符集是映射规则，字符编号到字节；ASCII英文范围窄，GBK中文GB2312；Unicode万国码，UTF-8变长编码；编码一致不乱码，指定字符集要牢记！

## 四、实战锦囊

### 1. 字节流和字符流的使用

**字节流复制二进制文件**：
```java
// 字节流复制图片
try (
    FileInputStream fis = new FileInputStream("source.jpg");
    FileOutputStream fos = new FileOutputStream("target.jpg")
) {
    byte[] buffer = new byte[1024];
    int bytesRead;
    while ((bytesRead = fis.read(buffer)) != -1) {
        fos.write(buffer, 0, bytesRead);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

**字符流读取文本文件**：
```java
// 字符流读取文本
try (
    FileReader fr = new FileReader("input.txt");
    BufferedReader br = new BufferedReader(fr)
) {
    String line;
    while ((line = br.readLine()) != null) {
        System.out.println(line);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

### 2. 缓冲流的使用

**字节缓冲流复制文件**：
```java
// 字节缓冲流复制文件
try (
    FileInputStream fis = new FileInputStream("source.mp4");
    BufferedInputStream bis = new BufferedInputStream(fis);  // 默认8KB缓冲区
    FileOutputStream fos = new FileOutputStream("target.mp4");
    BufferedOutputStream bos = new BufferedOutputStream(fos)  // 默认8KB缓冲区
) {
    byte[] buffer = new byte[4096]; // 4KB临时缓冲区
    int bytesRead;
    while ((bytesRead = bis.read(buffer)) != -1) {
        bos.write(buffer, 0, bytesRead);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

**字符缓冲流读写文本**：
```java
// 字符缓冲流读写文本
try (
    FileReader fr = new FileReader("input.txt");
    BufferedReader br = new BufferedReader(fr);  // 默认8KB缓冲区
    FileWriter fw = new FileWriter("output.txt");
    BufferedWriter bw = new BufferedWriter(fw)    // 默认8KB缓冲区
) {
    String line;
    while ((line = br.readLine()) != null) {  // 读取整行
        bw.write(line);
        bw.newLine();  // 写入换行符
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

### 3. 字符集的处理

**写入UTF-8文件**：
```java
// 写入UTF-8文件
public static void writeUTF8(String filePath, String content) throws IOException {
    try (
        FileWriter fw = new FileWriter(filePath, StandardCharsets.UTF_8, true);  // 追加模式
        BufferedWriter bw = new BufferedWriter(fw)
    ) {
        bw.write(content);
        bw.newLine();
        // 自动flush和close
    }
}

// 使用示例
writeUTF8("output.txt", "你好，Java！");
```

**读取UTF-8文件**：
```java
// 读取UTF-8文件
public static void readUTF8(String filePath) throws IOException {
    try (
        FileReader fr = new FileReader(filePath, StandardCharsets.UTF_8);
        BufferedReader br = new BufferedReader(fr)
    ) {
        String line;
        while ((line = br.readLine()) != null) {
            System.out.println(line);
        }
    }
}

// 使用示例
readUTF8("input.txt");
```

**转换文件编码（GBK → UTF-8）**：
```java
// 转换文件编码（GBK → UTF-8）
public static void convertEncoding(String sourcePath, String targetPath) throws IOException {
    try (
        InputStreamReader isr = new InputStreamReader(
            new FileInputStream(sourcePath),
            StandardCharsets.GBK  // 源文件编码
        );
        BufferedReader br = new BufferedReader(isr);
        OutputStreamWriter osw = new OutputStreamWriter(
            new FileOutputStream(targetPath),
            StandardCharsets.UTF_8  // 目标文件编码
        );
        BufferedWriter bw = new BufferedWriter(osw)
    ) {
        String line;
        while ((line = br.readLine()) != null) {
            bw.write(line);
            bw.newLine();
        }
        // 自动flush和close
    }
}

// 使用示例
convertEncoding("gbk.txt", "utf8.txt");
```

### 4. 实际应用场景

**读取配置文件（字符流+缓冲流）**：
```java
// 使用字符流读取配置文件
try (
    FileReader fr = new FileReader("config.properties");
    BufferedReader br = new BufferedReader(fr)
) {
    String line;
    while ((line = br.readLine()) != null) {
        if (!line.startsWith("#") && !line.trim().isEmpty()) {
            String[] parts = line.split("=");
            String key = parts[0].trim();
            String value = parts[1].trim();
            System.out.println(key + " = " + value);
        }
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

**复制二进制文件（字节流+缓冲流）**：
```java
// 使用字节流复制二进制文件
public static void copyBinaryFile(String sourcePath, String targetPath) throws IOException {
    try (
        FileInputStream fis = new FileInputStream(sourcePath);
        BufferedInputStream bis = new BufferedInputStream(fis);
        FileOutputStream fos = new FileOutputStream(targetPath);
        BufferedOutputStream bos = new BufferedOutputStream(fos)
    ) {
        byte[] buffer = new byte[4096]; // 4KB缓冲区
        int bytesRead;
        while ((bytesRead = bis.read(buffer)) != -1) {
            bos.write(buffer, 0, bytesRead);
        }
    }
}
```

**写入日志文件（字符流+缓冲流+UTF-8）**：
```java
// 写入UTF-8日志文件
public static void writeLog(String logFile, String message) throws IOException {
    try (
        FileWriter fw = new FileWriter(logFile, StandardCharsets.UTF_8, true);  // 追加模式
        BufferedWriter bw = new BufferedWriter(fw)
    ) {
        bw.write(new Date().toString() + " - " + message);
        bw.newLine();
        // 自动flush和close
    }
}

// 使用示例
writeLog("app.log", "系统启动成功");
```

## 五、用户回答内容

### 问题1：什么是字节流和字符流？它们有什么区别？

**用户回答：**
- 字节流是任何文件都能处理，包括视频和音频，处理单位是字节，它是先准换成字节。
- 字符流是只能处理和字符相关的文件，也就是更加擅长处理文本内容，它的处理基本单位是char，其中它的底层也是字节流，加上缓冲流和一个转换器。

### 问题2：缓冲流的作用是什么？它与普通流有什么区别？

**用户回答：**
- 缓冲流可以提高代码的读取性能，其中原因是缓冲流底层会调用一个byte[]数组预先存贮，只要达标后才会进行处理，打个比方：就是要处理的数据，缓冲流和编辑者的关系就是水井-水桶-饮水者的关系。

**代码实例：**
```java
FileReader fr=new FileReader(s);
BufferedReader br=new BufferedReader(fr,4);
int read = br.read();
while(read!=-1){
    System.out.print((char)read);
    break;
}
```

### 问题3：什么是字符集？如何处理字符编码问题？

**用户回答：**
- 字符集：给每一个字符安排编号
- 常见：ASCll英文+电脑上的字符，适用范围较窄
- GBK:中文，使用范围窄
- unicode:万国码（utf-8）:可变长度，作为通用的编码。
- 其中字符集不匹配会导致乱码。

**代码实例：**
```java
public class FIleIoDemo02 {
    public static void main(String[] args) throws IOException {
        String s="D:\\新建 文本文档.txt";
        FileWriter fw=new FileWriter(s, StandardCharsets.UTF_8,true);
        fw.write("你好java");
        fw.close();
    }
}
```

## 六、源码/扩展阅读链接

1. **Java IO流官方文档**
   - Java 8 InputStream：https://docs.oracle.com/javase/8/docs/api/java/io/InputStream.html
   - Java 8 OutputStream：https://docs.oracle.com/javase/8/docs/api/java/io/OutputStream.html
   - Java 8 Reader：https://docs.oracle.com/javase/8/docs/api/java/io/Reader.html
   - Java 8 Writer：https://docs.oracle.com/javase/8/docs/api/java/io/Writer.html

2. **缓冲流官方文档**
   - Java 8 BufferedInputStream：https://docs.oracle.com/javase/8/docs/api/java/io/BufferedInputStream.html
   - Java 8 BufferedOutputStream：https://docs.oracle.com/javase/8/docs/api/java/io/BufferedOutputStream.html
   - Java 8 BufferedReader：https://docs.oracle.com/javase/8/docs/api/java/io/BufferedReader.html
   - Java 8 BufferedWriter：https://docs.oracle.com/javase/8/docs/api/java/io/BufferedWriter.html

3. **字符集官方文档**
   - Java 8 StandardCharsets：https://docs.oracle.com/javase/8/docs/api/java/nio/charset/StandardCharsets.html
   - Java 8 Charset：https://docs.oracle.com/javase/8/docs/api/java/nio/charset/Charset.html
   - 字符编码详解：https://docs.oracle.com/javase/tutorial/i18n/text/encoding.html

4. **Java IO教程**
   - Java IO官方教程：https://docs.oracle.com/javase/tutorial/essential/io/
   - Java NIO.2教程：https://docs.oracle.com/javase/tutorial/essential/io/fileio.html

## 七、明日学习计划

### 复习任务（强关联）

1. **复习字节流和字符流**
   - 打开 `D:\桌面\笔记文件夹\Note.md`，重读【2026-03-13 Java IO流】章节中关于'字节流和字符流'的内容
   - 尝试口头复述字节流和字符流的记忆口诀："字节流处理二进制，字符流处理文本"

2. **复习缓冲流**
   - 重读【2026-03-13 Java IO流】章节中关于'缓冲流'的内容
   - 尝试口头复述缓冲流的记忆口诀："缓冲流加缓冲区，减少I/O操作次"

3. **复习字符集**
   - 重读【2026-03-13 Java IO流】章节中关于'字符集'的内容
   - 尝试口头复述字符集的记忆口诀："字符集是映射规则，字符编号到字节"

### 新学任务

1. **学习Java NIO（非阻塞IO）**
   - 学习NIO的核心概念：Channel、Buffer、Selector
   - 学习NIO的文件操作：FileChannel、ByteBuffer
   - 理解NIO与传统IO的区别和优势

2. **探索网络编程基础**
   - 学习Socket和ServerSocket的基本使用
   - 学习TCP协议的网络编程
   - 理解网络编程中的IO流应用

**知识串联预告：**
- NIO是对传统IO的增强，提供了更高效的I/O操作方式
- 网络编程中的Socket通信也使用IO流进行数据传输
- 理解这些知识点将为后续学习Netty、Spring Boot等框架打下基础

## 八、补充说明

1. **字节流和字符流的注意事项**
   - 字节流处理二进制数据，不关心数据的含义
   - 字符流处理文本数据，自动进行字符编码转换
   - 字符流底层仍然是字节流，只是添加了字符编码转换器
   - 选择流类型时，根据文件类型选择合适的流

2. **缓冲流的注意事项**
   - 缓冲流会提高性能，但会增加内存使用
   - 缓冲区大小通常为8192字节（8KB），可以自定义
   - 写入数据后调用flush()可以强制将缓冲区数据写入底层流
   - 使用try-with-resources语法自动关闭流，确保资源释放

3. **字符集的注意事项**
   - 读取和写入文件时使用相同的字符集，避免乱码
   - Windows平台默认使用GBK，Linux/Mac平台默认使用UTF-8
   - 推荐使用UTF-8编码，支持所有语言字符
   - 使用StandardCharsets类指定字符集，避免拼写错误

4. **学习建议**
   - 结合实际项目练习IO流的使用，掌握常见操作
   - 注意异常处理，确保流的正确关闭
   - 使用try-with-resources语法自动关闭流，避免资源泄漏
   - 理解IO流的装饰器模式，掌握流的组合使用