---
title: 'JAVA:正则表达式用法'
tags:
  - JAVA
  - regex
  - pattern
categories: JAVA
abbrlink: 5ddc6624
date: 2018-08-28 09:43:45
keywords:
description:
---
## java的regex库
java里预留了一个regex库，方便于我们在java里操作正则表达式，或者用它来匹配字符串。

其中比较常用的就是 Pattern 和 Matcher ,pattern是一个编译好的正则表达式，而Matcher是一个正则表达式适配器，Matcher的功能很强大，所以我们一般用pattern 来获取一个Matcher对象，然后用Matcher来操作正则表达式。先看一下这两个类的用法吧.

## Pattern
创建`pattern`的对象是很简单的，但是由于`pattern`的构造方法是用`private`声明的，所以我们仅能通过工厂模式的`compile`方法来返回一个`Pattern`的对象。

```java
Pattern pattern = Pattern.compile("[abc]");
```
compile可以接收一个正则表达式作为参数。

接下来我们创建一个`Matcher`对象。`Matcher`的构造方法也是一个`private`方法，但是我们可以通过`Pattern`的`Matcher`方法来返回一个`Matcher`对象。

```java
Matcher matcher = pattern.matcher("hello abc");
```
<!--more-->
这里`matcher`可以接收一个字符串作为参数，准确的说这里所接收的参数类型是`CharSequences`接口类型的参数，但是`String`、`StringBuffer`、`StringBuilder`，还有`CharBuffer`都实现了`CharSequence`接口，因此我们向里面传入这四种任何我们需要的参数。

与此同时`Pattern`还提供了一个`matches`静态方法，它允许我们传入一个`String`类型的正则表达式和一个`String`类型的需要匹配的字符串，并返回一个`boolean`类型的值，这个方法的好处在于我们可以不用创建`pattern`对象和`matcher`对象就可以知道所传入的正则表达式能不能匹配所传入的字符串。

```java
boolean bool = Pattern.matches("\\w+","hello abc");
```

## Matcher
说到`Matcher`，这个东西就很强大了，我们比较常用的方法有:

**find();**

**group();**

（1）先来说一下`find()`和`group()`这两个方法。

* `find`有点像一个迭代器，它能通过正则表达式向前迭代。下来看一个例子

```java
public class Main {
    public static void main(String[] args){
        Pattern pattern = Pattern.compile("\\w+");
        Matcher matcher = pattern.matcher("hello abc bbc cbc ccc");
        //find向前迭代
        while(matcher.find()){
            System.out.println(matcher.group());
        }
    }
}
```

我们先来看看 jdk 给出的 api 怎么定义`find`的:

```java
boolean find();
```
可以知道`find`返回的是一个`boolean`值，当前方没有内容的时候，`find`会返回`false`，所以我们这里可以直接用`while`来写，这句代码打印出的内容是

```
hello
abc
bbc
cbc
ccc
```

可以看到其实我们的正则表达式"\\w+"只匹配到了第一个单词hello ，但是因为find迭代的关系，把后面的单词全部都打印出来了，参照的正是我们给出的正则表达式。

（2）`group`: 说到`find`就不得不说`group`。
下面看个式子

```
(a(b)(c(d)))
```

* 这里的话我们把整个式子称为第0组,
* 第一组是 a(b)(c(d))
* 第二组是 子式 b 和 子式c(d)
* 第三组是 d

看一下几个`group`方法

```java
int groupCount()
```

返回此匹配器模式中的捕获组数，这个方法也就是返回所匹配的字符串的组数。

```java
public class Main {
    public static void main(String[] args){
        Pattern pattern = Pattern.compile("(\\w+)\\d+");
        Matcher matcher = pattern.matcher("hello123 abc bbc cbc ccc");
        matcher.find();
        System.out.println(matcher.groupCount());
    }
}
```
这里匹配到的是`hello123`， 当然不加()也能得到，这里只是为了方便演示。
打印出来的数值是1，这是因为我们只有一个组

那`group()`呢?

```java
String group()
```
返回由以前匹配操作所匹配的输入子序列。

也就是说`group`是返回所匹配到的`第0组`的值，返回值是一个`String`。这也能解释我们刚刚用`find`进行迭代的那个例子了。

```java
public class Main {
    public static void main(String[] args){
        Pattern pattern = Pattern.compile("\\w+");
        Matcher matcher = pattern.matcher("hello abc bbc cbc ccc");
        //find向前迭代
        while(matcher.find()){
            System.out.println(matcher.group());
        }
    }
}
```
这里没有分组所以直接将匹配到的`String`打印出来，其实也就是`第0组`。

另外，`group`还有个重载的方法，可以接收一个`int`类型的参数

```java
String group(int group)
```
返回在以前匹配操作期间由给定组捕获的输入子序列。
传入的参数正是组数.

```java
public class Main {
    public static void main(String[] args){
        Pattern pattern = Pattern.compile("(\\w+)\\s\\d+");
        Matcher matcher = pattern.matcher("hello 123 abc bbc cbc ccc");
        matcher.find();
        System.out.println(matcher.group(1));
    }
}
```
打印出来的结果正在我们的意料之中是`hello`。

除此之外还有两个可以返回匹配当前字符串的索引的方法。

```java
int start();
int end();
```

其中`start`是返回匹配成功的子串的第一个字母的索引，而`end`是返回子串最后一个索引的位置`+1`。

```java
String input = "hello abc BBc Cbc ccc";
Matcher matcher = Pattern.compile("[A-Z][A-Z]\\w").matcher(input);
matcher.find();
System.out.println(input.charAt(matcher.start()));
```
这里打印出来的值是B。但是如果我们换成end就不一样了。

```java
System.out.println(input.charAt(matcher.end()));
```
这里打印出来的值却是`" "`是一个空字符,也就是c的索引加了1，所以我们这里只需稍作修改便可以打印出`c`了。

```
System.out.println(input.charAt(matcher.end()-1));
```