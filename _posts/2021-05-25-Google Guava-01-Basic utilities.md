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

​	null在开发过程中会引发很多让人意外的问题，通过查看底层源码库，我们发现将近95%的集合类不接受null作为元素，因此我们认为，相比默默接受null，使用快速失败操作拒绝null对开发者更有帮助。

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

​	guava在preditions类中添加了若干前置条件判断的实用方法，强烈推荐将这些放大加入项目中使用，每个方法都有三个变种：

​	·没有额外参数：

​	·有一个object对象作为额外参数：抛出的异常使用object.tostring作为错位错误信息；

​	·有一个string作为额外参数，并且有一组任意数量的附加object对象：这个变种处理异常消息类似printf，但考虑gwt的兼容性和效率，只支持%s指示符；

checkArgument(i>=0, "Argument was s% but expected nonegative", i);

checkArgument(i<j, "Expected i < j, but s% > s%", i, j);

| 方法声明                                           | 描述                                                         | 检查失败时抛出的异常      |
| -------------------------------------------------- | ------------------------------------------------------------ | ------------------------- |
| checkArgument(bolean)                              | 检查boolean是否为true，用来检查传递给方法的参数。            | IllegalArgumentException  |
| checkNotNull(T)                                    | 检查value是否为null，该方法直接返回value，因此可以内嵌使用checkNotNull`。` | NullPointerException      |
| checkState(boolean)                                | 用来检查对象的某些状态。                                     | IllegalStateException     |
| checkElementIndex(int index,int size)              | 检查index作为索引值对某个列表、字符串或数组是否有效。index>=0 && index<size * | IndexOutOfBoundsException |
| checkPositionIndex(int index, int size)            | 检查index作为位置值对某个列表、字符串或数组是否有效。index>=0 && index<=size * | IndexOutOfBoundsException |
| checkPositionIndexes(int start, int end, int size) | 检查[start, end]表示的位置范围对某个列表、字符串或数组是否有效* | IndexOutOfBoundsException |

### 相比apache commons的类似方法，我们建议把guava的Preconditions作为首选，理由如下：

·静态导入后，guava的方法清晰，每个check*方法清晰描述所做处理，让你在构造函数过程中保持字段的单行赋值风格；

·简单的、参数可变的printf风格异常信息；

在编码时，若某值有多重的前置条件，建议把他们放到不同行，有助于调试时定位，也有助于编写清晰和有用的错误消息；

## 常用object方法

## equals

当一个对象中的字段可以为null时，实现object.equals方法会抛出npe，所以不得不进行null检查，使用objects.equal可以帮助执行null敏感的equals判断，避免npe；

## hashCode

guava的objects.hashcode（object...）会对传入的字段序列计算出合理的、顺序敏感的散列值，可以使用objects.hashcode(field1, field2, ... , fieldn)来代替手工计算散列值；

## toString

Objects.toStringHelper:

//Returns "ClassName{x=1}"

Objects.toStringHelper(this).add("x",1).toString();



## compare/compareTo

旧版本使用比较器：实现comparator或实现comparable接口，比较繁琐，如：

class person implements Comparable<Person>{

​	private Srting lastName;

​	private Srting firstName;	

​	private int zipCode;

public int compareTo(Person other){

​	int cmp = lastName.compareTo(other.lastName);

​	if(cmp != 0)

​		return cmp;

​    cmp = firstName.compareTo(other.firstName);

​	if(cmp != 0)

​		return cpm;

​	return Integer.compare(zipCode, other.zipCode);

 }

}

该用guava提供地ComparisonChain,它执行一种懒比较，执行比较操作直至发现非零的结果，在那之后的比较输入将被忽略

public int compareTo(Foo that) {

​	return ComparisonChain.start().compare(this.aString, that.aString).compare(this.anInt, that.anInt).compare(this.anEnum, that.anEnum,Ordering.natural().nullsLast()).result();

}

## 排序：guava强大的流畅风格比较器-Ordering

​	ordering实际为Comparator的特殊实例，把很多基于comparator的静态方法包装为自己的实例方法但非静态方法，且提供了链式调用方法，来定制和增强现有的比较器。

创建排序器:

​	natrual()：对可排序类型做自然排序，数字按大笑，日期按先后

​	usingToString()：按对象的字符串形式，做字典排序lexicographical ordering

​	from(Comparator)：把给定的comparator转化为排序器，为了实现自定义排序器，也可以不是用from，直接继承ordering

​		Ordering<String> byLengthOrdering = new Ordering<String>{

​				public int compare(String left，String right) {

​					return Ints.compare(left.length(), right.length());

​				}

​		};

排序器ordering的链式调用：

​	reverse()：获取语意相反的排序器

​	nullFirst(): 使用当前排序器，但额外把null值排到最前面

​	nullLast(): 使用当前排序器，但额外把null值排到最后面

​	compound(Comparator): 合成另一个比较器，以处理当前排序器中的相等情况

​	lexicographical():基于处理类型T的排序器，返回该类型的可迭代对象Iterable<T>的排序器

​	onResultOf(Function):对集合中元素调用Function，再按返回值用当前排序器排序

​	示例:

​		需要排序器的类：

​			class Foo{

​				@Nullable String sortedBy;

​				int noSortedBy;

​			}

​         使用下面的链式调用来合成排序器:阅读链式调用产生的排序器时，应该从后往前读,排序器首先调用apply方法获取sortedBy值，并把sortedBy为null的元素都放到最前面，然后把剩下的元素按sortedBy进行自然排序,从后往前读，是因为每次链式调用都是用后面的方法包装了前面的排序器,在使用compound方法时，不应遵循从后往前读的规则，需要注意这点，为了避免混淆，请不要在一长串链式调用中间使用compound，可以另起一行，在链中最先或最后调用。

​         Ordering<Foo> ordering = Ordering.natural().nullFirst().OnResultOf(new Function<Foo, String>){

​				public String apply(Foo foo) {

​						return foo.sortedBy;

​                }

​         });

运用排序器：Guava的排序器实现有若干操纵集合或元素值的方法

​	greatestOf(Itrable iterable, int k)：获取可迭代对象中最大的k个元素。

​	isOrdered(Iterable)：判断可迭代对象是否已按排序器排序：允许有排序值相等的元素

​	sortedCopy(Iterable)：判断可迭代对象是否已严格按排序器排序：不允许排序值相等的元素

​	min(E,E)：返回两个参数中最小的那个。如果相等，则返回第一个参数

​	min(E,E,E,E,E...)：返回多个参数中最小的那个。如果有超过一个参数都最小，则返回第一个最小的参数

​	min(Iterable): 返回迭代器中最小的元素。如果可迭代对象中没有元素，则抛出NoSuchElementException

## Throwables:简化异常和错误的传播与检查

异常传播：

把捕获到的异常再次抛出，这种情况通常发生在Error或RuntimeException被捕获的时候，开发时没想捕获它们，但是声明捕获Throwable和Exception的时候，也包括了了Error或RuntimeException，Guava提供了若干方法，来判断异常类型并且重新传播异常：

try {
    someMethodThatCouldThrowAnything();
} catch (IKnowWhatToDoWithThisException e) {
    handle(e);
} catch (Throwable t) {
    Throwables.propagateIfInstanceOf(t, IOException.class);
    Throwables.propagateIfInstanceOf(t, SQLException.class);
    throw Throwables.propagate(t);
}

​	所有这些方法都会自己决定是否要抛出异常，但也能直接抛出方法返回的结果——例如，throw Throwables.propagate(t);—— 这样可以向编译器声明这里一定会抛出异常

Guava中异常传播方法简要列举：

RuntimeException propagate(Throwable)：如果Throwable是Error或RuntimeException，直接抛出；否则把Throwable包装成RuntimeException抛出，返回类型是RuntimeException。

void propagateIfInstanceOf( Throwable, Class<X extends   Exception>) throws X: Throwable类型为X才抛出。

void propagateIfPossible( Throwable)：Throwable类型为Error或RuntimeException才抛出。

void   propagateIfPossible( Throwable, Class<X extends Throwable>) throws X：Throwable类型为X, Error或RuntimeException抛出

## Throwables.propagate的用法

​	通常来说，如果调用者想让异常传播到栈顶，他不需要写任何catch代码块。因为他不打算从异常中恢复，他可能就不应该记录异常，或者有其他的动作。他可能是想做一些清理工作，但通常来说，无论操作是否成功，清理工作都要进行，所以清理工作可能会放在finallly代码块中。但有时候，捕获异常然后再抛出也是有用的：也许调用者想要在异常传播之前统计失败的次数，或者有条件地传播异常

Java7用多重捕获解决了多种异常需要处理的问题：

...

} catch (RuntimeException | Error e) {
    failures.increment();
    throw e;
}

Throwables.propagate的有争议用法：

争议一：把受检异常转化为非受检异常

争议二：异常穿隧

争议三：重新抛出其他线程产生的异常

异常原因链：

| Throwable   getRootCause(Throwable)         |
| ------------------------------------------- |
| List<Throwable>   getCausalChain(Throwable) |
| String   getStackTraceAsString(Throwable)   |