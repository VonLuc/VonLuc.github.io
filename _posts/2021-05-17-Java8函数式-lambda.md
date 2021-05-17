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

