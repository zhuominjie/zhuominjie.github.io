---
title: JAVA：流
tags:
  - InputStream
  - OutputStream
  - java
keywords:
  - InputStream
  - OutputStream
  - java
categories: JAVA
abbrlink: 9c74640c
date: 2018-06-09 21:26:45
---

## 流
**输入流：**可以从其中读入字节序列的对象；
**输出流：**可以向其中写入字节序列的对象；

抽象类`InputStream`和`OutputStream`构成输入/输出(I/O)类层次结构基础。

`Unicode`中每个字符都使用多个字节表示，以上面向字节的流不便处理，因此，从抽象类`Reader`和`Writer`中继承出专用于处理`Unicode`字符的类层次结构，且读写操作都基于**两字节**的Unicode码元。
<!--more-->
### 读写字节
`adstract int read()`

`InputStream`类的抽象方法，作用是：读入一个字节，并返回读入的字节，或者在遇到输入源结尾时返回`-1`。具体的输入流类必须覆盖此方法以提供适用的功能。

`abstract void write(int b)`

`OutputStream`的抽象方法，用于向某个输出位置写出一个字节。

`read`和`write`方法在执行时都将阻塞，直至字节确实被读入或写出。

`available`方法用于检查当前可读入的字节数量。以下的代码写法就不可能被阻塞，因为**字节数组的长度被确定**。

```java
int bytesAvailable = in.available();
if (bytesAvailable > 0) {
	byte[] data = new byte[bytesAvailable];
	in.read(data);
}
```
完成对流的读写后，应调用`close`方法关闭它，释放系统资源。关闭**输出流**的同时，还会冲刷用于该输出流的缓冲区（buffer）：字节输出时，先被置于临时缓冲区中，再以更大的包（block）的形式写出，当关闭输出流时，会将缓冲区内容输出。如果不关闭，那么写出字节的最后一个包可能永远也无法输出。当然，也可以用`flush`方法强制输出。

#### 组合流过滤器
FileInputStream和FileOutputStream可以通过文件路径读取磁盘文件上的输入/输出流，并且只能从`fin`对象中读入字节和字节数组。

```java
FileInputStream fin = new FileInputStream("employee.dat")

byte b = (byte)fin.read();
```

提示：java.io的相对路径以用户工作目录开始，调用`System.getProperty("user.dir")`来获取。

DataInputStream只能读入数值类型，而不能从文件获取数据。

```
DataInputStream din = ...;
double s = din.readDouble();
```

如果希望从文件或者其他外部位置获取字节，再将其组装到更有用的数据类型中（如数字类型），则必须对二者进行组合。

```
FileInputStream fin = new FileInputStream("employee.dat");
DataInputStream din = new DataInputStream(fin);
double s = din.readDouble();
```

再例如，默认情况下流的读取时不被缓冲区缓存的，每调用一次`read`会请求操作系统再分发一个字节。要想使用**缓冲机制**，并从文件获取数据，就需要如下的构造器序列。

```
DataInputStream din = new DataInputStream(
	new BufferInputStream(
		new FileInputStream("employee.dat")));
```
### 文本输入与输出
保存数据时，可以选择二进制格式或者文本格式。尽管二进制格式的I/O高效且高速，但是难以阅读。

在存储文本字符串时，需要考虑字符编码（character encoding）方式，如UTF-16、ISO 8859-1等。

`OutputStreamWriter`类使用选定的字符编码方式，把Unicode**字符流**转换为**字节流**。而`InputStreamReader`类将字节输入流转为可以产生Unicode码元的读入器。

例如，从控制台读入键盘敲击信息，并转为Unicode：

```java
InputStreamReader in = new InputStreamReader(System.in);
```

这个`InputStreamReader`使用系统默认字符编码方式，如需指定不同的编码方式，则可用以下方式：

```java
InputStreamReader in = new InputStreamReader(
	new FileInputStream("kremlin.dat"), "ISO8859_5");
```

#### 如何写出文本输出
`PrintWriter`类拥有以文本格式打印字符串和数字的方法，再使用与`System.out`相同的`print`、`println`和`printf`方法打印数字、字符、布尔值、字符串和对象到文件中。例如：

```java
PrintWriter out = new PrintWriter("employee.txt");

String name = "Harry Hacker";
double salary = 75000;
out.print(name);
out.print(' ');
out.println(salary);
```
输出以下内容到 employee.txt 中：

```
Harry Hacker 75000.0
```

#### 如何读入文本输入
在 Java SE 5.0 之前，通过`BufferedReader`类的`readLine`方法读入一行文本。`readLine`在没有输入时返回 **null**。以下示例集成缓冲功能：

```java
BufferedReader in = new BufferedReader(
	new InputStreamReader(new FileInputStream("employee.txt"), "UTF-8"));
	
String line;
while ((line - in.readLine()) != null) 
{
	do something with line
}
```
然而，`BufferedReader`不能读入数字，建议使用`Scanner`来处理。





