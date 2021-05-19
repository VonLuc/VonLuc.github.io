---
layout:     post   				    							
title:      基于java8的函数式编程系列-Lambda
subtitle:   函数式编程系列
date:       2021-05-17 											
author:     Zhan 												
header-img: img/post-bg-2015.jpg 								
catalog: true 													
tags:														
    - 函数式 lambda java8
---

# Java8函数式(一)-Lambda

​	现阶段各大厂、公司均在积极推行java函数式编程，抛开跟风因素之外，java的函数式处理可将javaer在各个业务场景下从数据结构间的奇葩变换中轻易地解脱出来。我采用了示例驱动的写作风格，介绍概念定义后会跟随代码，之后是讲解，最后每章的末尾还有联系。我强烈建议读者读完一章后完成这些练习，熟能生巧。每个务实的程序员都知道，自欺欺人很容易，你觉得读懂一段代码了，其实还是遗漏了一些细节。

**`练习题答案github地址 https://github.com/RichardWarburton/java-8-Lambdas-exercises`**

## 为何引入Lambda

​	为编写处理批量数据的并行类库，需要修改java，添加Lambda表达式；

## 举例领域模型结构

Artist

​	name

​	members

​	origin

Track

​	name

Album

​	name

​	tracks

​	musicians

## Lambda表达式

### 第一个Lambda表达式

​	Swing是与平台无关的java类，常用来写图形用户界面(GUI)，做捕获界面元素触发的事件，实例代码（采用内部匿名类实现）：

```java
button.addActionListener(new ActionListener() {
    public void actionPerformed(ActionEvent event) {
        System.out.println("button clicked");
    }
});//该例为代码即数据，给按钮传递了一个代表某种行为的对象
```

上述匿名类的设计目的，即为方便javaer将代码作为数据传递，但内部类不足够简便且难读；

在java8中改写为：

```java
/**
 * event为参数名，与上述匿名类中为同一参数。->将参数与lambda的主体分开，主体为执行的逻辑代码
 * 匿名类需要显式声明参数类型，lambda无需指定类型，程序仍可编译(javac根据程序上下文方法的签名可以推断出参数类型)
 * java8仍是一种静态类型语言，为增加可读性迁就了我们额习惯，声明参数时可以包括类型，而且有时编译器不一定能根据上下文能推断出参数的类型
 */
 button.addActionListener(event -> System.out.println("button clicked"));
```

### 如何识别Lambda

```java
 /**
  * 不包含参数，使用空括号表示没参数，该Lambda表达式实现了runnable接口,接口返回类型为void
  * */
Runnable noArguments = () -> System.out.println("hello world");
/**
 * lambda只有一个参数时，可以省略参数括号
 * lambda的主体不仅可以是表达式，也可以是代码块，使用{}括起来
 * */
ActionListener oneArgument = event -> System.out.println("button clicked");
/**
 * 可以用返回和抛出异常退出
 * */
Runnable multiStatement = () -> {
    System.out.println("hello");
    System.out.println("world");
};
/**
 * 变量 add 的类型是 BinaryOperator<Long>，它不是两个数字的和， 而是将两个数字相加的那行代码
 * */
BinaryOperator<Long> add = (x, y) -> x + y;
/**
 * Lambda 表达式的类型依赖于上下文环境，是由编译器 推断出来的
 * */
BinaryOperator<Long> addExplicit = (Long x, Long y) -> x + y;
```

### 引用值，而不是变量

​	过匿名内部类引用它所在方法里的变量，需要将变量声明为 final，实际上是在使用赋给该变量的一个特定的值。Java 8 虽然放松了这一限制，可以引用非 final 变量，但是该变量在既成事实上必须是final。虽然无需将变量声明为 final，但在 Lambda 表达式中，也无法用作非终态变量。如果坚持用作非终态变量，编译器就会报错。既成事实上的 final 是指只能给该变量赋值一次。换句话说，Lambda 表达式引用的是值，而不是变量。Lambda 表达式也被称为闭包，未赋值的变量与周边环境隔离起来，进而被绑定到一个特定的值。

### 函数接口

```java
/**
 * ActionListener接口:接受ActionEvent类型参数，返回空;该接口也继承自一个不具有任何方法的父接口：EventListener;
 * ActionListener只有一个抽象方法：actionPerformed,表示的行为:接受一个参数，返回空;
 * 由于actionPerformed定义在一个接口里，因此abstract关键字不是必须的;
 * 该接口为函数式接口，接口中单一方法的命名并不重要，只要方法签名和Lambda表达是的类型匹配即可，可在函数接口中为参数起一个有意义的名字，增加易读性。
 * 
 *
 * */
public interface ActionListener  extends EventListener {
    public void actionPerformed(ActionEvent event);
}
```