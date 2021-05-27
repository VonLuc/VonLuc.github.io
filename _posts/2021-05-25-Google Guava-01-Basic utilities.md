---
layout:     post   				    							
title:      Google Guava-01 				
subtitle:   Basic utilities
date:       2021-05-25 											
author:     Zhan 												
header-img: img/post-bg-2015.jpg 								
catalog: true 													
tags:														
    - Google Guava 
---

# Google Guava -01- Basic utilities

​	Guava工程包含很多google java项目广泛一来的核心库，如集合、缓存、原生类型支持、并发库、通用注解，字符串处理，i/o等。

guava api链接地址:http://docs.guava-libraries.googlecode.com/git-history/release/javadoc/com/google/common/base/Optional.html

## 使用和避免null

​		null在开发过程中会引发很多让人意外的问题，通过查看底层源码库，我们发现将近95%的集合类不接受null作为元素，因此我们认为，相比默默接受null，使用快速失败操作拒绝null对开发者更有帮助。

​		null的语义模糊，让人反感。它可以表示失败、成功或者几乎任何情况，使用null以外的特定值，会让我们的开发逻辑变得更加清晰。

​		null的优势在于在性能和速度方面是廉价的，而且对象数组中，出现null也是无法避免，但对于底层库来说，在应用级别代码中，null往往导致混乱、疑难问题和模糊语义的罪魁祸首。

​		综合上述，guava对null采取快速失败，除非工具类本身提供针对null的因变措施，此外guava提供很多工具类，更方便替换null值。

### 具体建议

·不要再set中使用null，也不要把null作为map的键值，使用特殊值代表null会让查找更清晰；

·若想把null作为map中某条目的值，更好办法是 不把这一条目放到map中，而是单独维护一个【值为null的键集合null keys】，map中对应某个键的值是null和map中没有对应某个键的值，是非常容易混淆的情况。最好把值为null的键分离开，请认真想一下，null值的键在项目中到底表达什么；

·若需在列表中使用null，且列表的数据是稀疏的，使用Map<Integer, E>会更有效果，并且更准确地符合你实际的潜在需求；

·使用自然的null对象-特殊值，如某个enum类型增加特殊的枚举值表示null，如java.math.RoundingMode就定义了一个枚举值UNNECESSARY，它表示一种不做任何舍入操作的模式，用这种模式做舍入操作会直接抛出异常。

·若真的使用null，但null不能喝guava的集合实现一起工作，只能选择其他实现，如jdk的collections.unmodifiablelist代替guava的immutablelist；

### Optional

null大多表示某种缺失情形：可能是已经有一个默认值，或者没有值，或找不到值。guava使用Optional<T>表示可能为null的T类型引用，一个optinal实例可能包含非null的引用（引用存在），也可能什么也不包括（引用缺失)。它从不说包含的是null值，而是用存在和缺失表示，但optional从不会包含null值引用；

Optional<Integer> possible = Optional.of(5);

possible.isPresent();

possible.get();

Optional无意直接模拟其他编程环境中的”可选” or “可能”语义，但它们的确有相似之处。

#### Optional常用操作：

##### 创建optional实例(静态方法)

| Optional.of(T)           | 创建指定引用的optional实例，若引用为null则快速失败 |
| ------------------------ | -------------------------------------------------- |
| Optional.absent()        | 创建引用缺失的optional实例                         |
| Optional.fromNullable(T) | 创建指定引用的optional实例，若引用为null则表示失败 |

##### 用optional实例查询引用(非静态方法)

| boolean isPresent() | 如果optional包含非null的引用（引用存在)，返回true            |
| ------------------- | ------------------------------------------------------------ |
| T get()             | 返回optional所包含的引用，若引用缺失，则抛java.lang.IllegalStateException |
| T or(T)             | 返回optinal所包含的引用，若引用缺失，返回指定的值            |
| T orNull()          | 返回optional所包含的引用，若引用缺失，返回null               |
| Set<T> asSet()      | 返回optional所包含引用的单例不可变集，如果引用存在，返回一个只有单一元素的集合，如果引用缺失，返回一个空集合 |

#### 使用optional意义

​		使用Optional除了赋予null语义，增加了可读性，最大的优点在于它是一种傻瓜式的防护，使开发者思考引用缺失的情况，因此必须显式的从optional获取引用。

​		如同输入参数，方法的返回值也可能是null，将方法的返回类型指定为optional，可迫使调用者思考返回的引用缺失的情形；

#### 其他处理null的便利方法

​		当需要用一个默认值替代可能的null，使用objects.firstNonNull(T,T)方法，如同两个值都为null，该方法跑出npe，optional也是一个比较好的替代方案，如optional.of(first).or(second)

​		还有一些其他专门处理null或空字符串的方法(主要用来与混淆null/空的api交互)：

​		emptyToNull(String)

​		nullToEmpty(String)

​		isNullOrEmpty(String)











### 建议

积极把null和空区分开，以表示不同含义；

## 前置条件

## 常用object方法

## 排序：guava强大的流畅风格比较器

## Throwables:简化异常和错误的传播与检查



