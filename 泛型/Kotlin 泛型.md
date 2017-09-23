> 文章昨天发出之后收到了大家的反馈：
>
> - 感谢 @萌夜雀 指出的关于 Star Projection 的问题，有些疏漏，文中已修正，啊求大佬放过，不要让我退群；
> - 也感谢@冻雨 指出的"到底是型变还是协变" 的问题，为了不引起混淆，文中调整了措辞；
> - 另外，也感谢 @0u0 @Snk，"搞基函数"明显是个笔误好不啦
>
> 成文仓促，略有疏漏，非常抱歉，原文已删，文章重发，谢谢诸位，请不吝赐教！

## 0. 引子

Kotlin 100% 与 Java 兼容，所以抛开语言表面上面的种种特质之外，背后的语言逻辑或者说“灵魂”与 Java 总是想通的。本文只涉及 Kotlin Jvm，Kotlin Js、Kotlin Native 的具体实现可能有差异。

最近一段时间在慕课网上发了一套 Kotlin 的入门视频，涵盖了基础语法、面向对象、高阶函数、DSL、协程等比较有特色的知识点，不过有朋友提出了疑问：这门课为什么不专门讲讲泛型、反射和注解呢？

我最早听到这个问题的时候，反应比较懵逼，因为我居然没有感觉到 Kotlin 的反射、泛型特别是注解有专门学习的必要，因为他们跟 Java 实在是太像了。

实际上，从社区里面学习 Kotlin 的朋友的反应来看，大家大多对于函数式的写法，DSL，协程这些内容比较困惑，或者说不太适应，这与大家的知识结构是密切相关的，面向对象的东西大家很容易理解，因为就那么点儿内容，你懂了 C++ 的面向对象，Java 的也很容易理解，Kotlin 的也就不在话下了；而你没有接触过 Lua 的状态机，没有接触过 Python 的推导式，自然对于协程也就会觉得比较陌生。

所以我想说的是，泛型这东西，只要你对 Java 泛型有一定的认识，Kotlin 的泛型基本可以直接用。那我们这篇文章要干嘛呢？只是做一个简单的介绍啦，都很好理解的。

## 1. 真·泛型和伪·泛型

Java 的泛型大家肯定都知道了，1.5 之后才加入的，可以为类和方法分别定义泛型参数，就像下面这样：

```java
public class Generics<T>{ 
   private T t; 
   ... 
　 
   public <R> R getResult(){ 
       ... 
   } 
} 
```

Kotlin 的写法呢？完全一样：

```kotlin
class Generics<T>{ 
   private val t: T 
   ... 
　 
   fun <R> getResult(): R{ 
       ... 
   } 
} 
```

Java/Kotlin 的泛型实现采用了类型擦除的方式，这与 C# 的实现不同，后者是真·泛型，前者是伪·泛型。当然这么说是从运行时的角度来看的，在编译期，Java 的泛型对于语法的约束也是真实存在的，所以你愿意的话，也可以管 Java 的泛型叫做编译期真·泛型。

那么什么是真·泛型呢？我们给大家看一段 C# 的代码：

```kotlin
using System; 
　 
public class Program{ 
   public static void Main(String[] args){ 
       testGeneric<string>(); 
   } 
　 
    public static void testGeneric<T>(){ 
        Console.WriteLine(typeof(T)); 
    } 
} 
```

`testGeneric` 的泛型参数 string 可以在运行时获取到，俨然一个真实可用的类型啊。下面是输出的结果：

```
System.String 
```

那伪·泛型呢？如果同样的代码放到 Java 或者 Kotlin 当中，结果会怎样呢？

```java
public static <T> void testGenerics(){ 
    System.out.println(T.class); 
} 
```

这段代码无法编译，因为 T 是个泛型参数，你不能用它去获取 class 对象。为了更清楚地说明问题，我们看下下面的代码：

```java
public static <T> T testGenerics(){ 
    T t = null; 
    return t; 
} 
```

编译后的字节码：

```
public static testGenerics()Ljava/lang/Object; 
 L0 
  LINENUMBER 13 L0 
  ACONST_NULL 
  ASTORE 0 
 L1 
  LINENUMBER 14 L1 
  ALOAD 0 
  ARETURN 
 L2 
  LOCALVARIABLE t Ljava/lang/Object; L1 L2 0 
  // signature TT; 
  // declaration: T 
  MAXSTACK = 1 
  MAXLOCALS = 1 
```

我们看到，编译之后 T 变成了 Object，简单来说就相当于：

```java
public static Object testGenerics(){ 
    Object t = null; 
    return t; 
} 
```

这就是传说中的类型擦除了。而 Kotlin 在 JVM 之上，编译之后也是字节码，机制与 Java 是一样的。也正是因为这个原因，我们在使用 Gson 反序列化对象的时候除了制定泛型参数，还需要传入一个 class ：

```kotlin
public <T> T fromJson(String json, Class<T> classOfT) throws JsonSyntaxException { 
   ... 
} 
```

显然 Gson 没有办法根据 T 直接去反序列化。

下面我们说一点儿不太一样的。在 Kotlin 当中有一个关键字叫做 `reified`，还有一个叫做 `inline`，后者可以将函数定义为内联函数，前者可以将内联函数的泛型参数当做真实类型使用，我们先来看例子：

```kotlin
inline fun <reified T> Gson.fromJson(json: String): T{ 
    return fromJson(json, T::class.java) 
} 
```

这是一个 Gson 的扩展方法，有了这个之后我们就无须在 Kotlin 当中显式的传入一个 class 对象就可以直接反序列化 json 了。

这个会让人感觉到有点儿迷惑，实际上由于是内联的方法调用，T 的类型在编译时就可以确定的：

```kotlin
class Person(var id: Int, var name: String) 
　 
fun test(){ 
    val person: Person = Gson().fromJson("""{"id": 0, "name": "Jack" }""") 
} 
```

反编译之后：

```kotlin
public static final void test() { 
  Gson $receiver$iv = new Gson(); 
  String json$iv = "{\"id\": 0, \"name\": \"Jack\" }"; 
  Person person = (Person)$receiver$iv.fromJson(json$iv, Person.class); 
} 
```

**注意，在这里，inline 是必须的。**

## 2. 型变

### 2.1 Java 的型变

如果 Parent 是 Child 的父类，那么 `List<Parent>` 和 `List<Child>` 的关系是什么呢？对于 Java 来说，没有关系。

也就是说下面的代码是无法编译的：

```java
List<Number> numbers = new ArrayList<Integer>(); //ERROR! 
```

不过 numbers 中可以添加 Number 类型的对象，所以我添加个 Integer 可以不呢？可以的：

```java
numbers.add(1); 
```

那么我要想添加一堆 Integer 呢？用 addAll 是吧？注意看下 addAll 的签名：

```java
boolean addAll(Collection<? extends E> c); 
```

这个泛型参数又是什么鬼？如果我把这个签名写成下面这样：

```java
boolean addAll(Collection<E> c); 
```

我想要在 numbers 当中 addAll 一个 `ArrayList<Integer>`，那就不可能了，因为我们说过，`ArrayList<Number>` 和 `ArrayList<Integer>` 是两个不同的类型，毛关系都没有。

`? extends E` 其实就是使用点协变，允许传入的参数可以是泛型参数类型为 Number 子类的任意类型。

当然，也有 `? super E` 的用法，这表示元素类型为 E 及其父类，这个通常也叫作逆变。

### 2.2 Kotlin 的型变

> 型变包括协变、逆变、不变三种。

下面我们看看 Kotlin 是怎么支持这个特性的。Kotlin 支持声明点型变，我们直接看 Collection 接口的定义：

```kotlin
public interface Collection<out E> : Iterable<E> { 
   ... 
} 
```

`out E` 就是型变的定义，表明 Collection 的元素类型是协变的，即 `Collection<Number>` 也是 `Collection<Int>` 的父类。

而对于 MutableList 来说，它的元素类型就是不变的：

```kotlin
public interface MutableCollection<E> : Collection<E>, MutableIterable<E> { 
   ... 
   public fun addAll(elements: Collection<E>): Boolean 
   ... 
} 
```

换言之，`MutableCollection<Number>` 与

`MutableCollection<Int>` 没有什么关系。

那么请注意看 `addAll` 的声明，参数是 `Collection<E>`，而 Collection 是协变的，所以传入的参数可以是任意 E 或者其子类的集合。

逆变的写法也简单一些： `Collection<in E>`。

那么 Kotlin 是否支持使用点型变呢？当然支持。

我们刚才说 `MutableCollection` 是不变的，那么如果下面的参数改成这样：

```kotlin
public fun addAll(elements: MutableCollection<E>): Boolean 
```

结果就是，当 E 为 Number 时，addAll 无法接类受似 `ArrayList<Int>` 的参数。而为了接受这样的参数，我们可以修改一下签名：

```kotlin
public fun addAll(elements: MutableCollection<out E>): Boolean 
```

这其实就与 Java 的型变完全一致了。

### 2.3 @UnsafeVariance

型变是一个让人费解的话题，很多人接触这东西的时候一开始都会比较晕，我们来看看下面的例子：

```kotlin
class MyCollection<out T>{ 
    fun add(t: T){ // ERROR! 
       ... 
    } 
} 
```

为什么会报错呢？因为 T 是协变的，所以外部传入的参数类型如果是 T 的话，会出问题，不信你看：

```kotlin
var myList: MyCollection<Number> = MyCollection<Int>() 
myList.add(3.0) 
```

上面的代码毫无疑问可以编译，但运行时就会比较尴尬，因为 `MyCollection<Int>` 希望接受的是 Int，没想到来了一个 Double。。

对于协变的类型，通常我们是不允许将泛型类型作为传入参数的类型的，或者说，对于协变类型，我们通常是不允许其涉及泛型参数的部分被改变的。这也很容易解释为什么 MutableCollection 是不变的，而 Collection 是协变的，因为在 Kotlin 当中，前者是可被修改的，后者是不可被修改的。

逆变的情形正好相反，即不可以将泛型参数作为方法的返回值。

但实际上有些情况下，我们不得已需要在协变的情况下使用泛型参数类型作为方法参数的类型：

```kotlin
public interface Collection<out E> : Iterable<E> { 
   ... 
    public operator fun contains(element: @UnsafeVariance E): Boolean 
   ... 
} 
```

比如这种情形，为了让编译器放过一马，我们就可以用 @UnsafeVariance 来告诉编译器：“我知道我在干啥，保证不会出错，你不用担心”。

最后再给大家提一个点，现在你们知道为什么 in 表示逆变，out 表示协变了吗？

## 3. 通配符

在Java 中，当我们不知道泛型具体类型的时候可以用 ？来代替具体的类型来使用，比如下面的写法：

```java
Class<?> cls = numbers.getClass(); 
```

Kotlin 也可以有类似的写法：

```kotlin
val cls: Class<*> = list.javaClass 
val cls2: Class<*> = List::class.java 
```

Kotlin 可以根据 * 所指代的泛型参数进行相应的映射，下面是官方的说法：

- 对于 `Foo <out T>`，其中 T 是一个具有上界 TUpper 的协变类型参数，`Foo <*>` 等价于 `Foo <out TUpper>`。 这意味着当 T 未知时，你可以安全地从 `Foo <*>` 读取 TUpper 的值。
- 对于 `Foo <in T>`，其中 T 是一个逆变类型参数，`Foo <*>` 等价于 `Foo <in Nothing>`。 这意味着当 T 未知时，没有什么可以以安全的方式写入 `Foo <*>`。
- 对于 `Foo <T>`，其中 T 是一个具有上界 TUpper 的不型变类型参数，`Foo<*>` 对于读取值时等价于 `Foo<out TUpper>` 而对于写值时等价于 `Foo<in Nothing>`。

那么 * 在哪些场合下可以或者不可以使用呢？

我们来看几个例子：

```kotlin
val list = ArrayList<*>()// ERROR! 
```

`*` 不允许作为函数和变量的类型的泛型参数！

```kotlin
fun <T> hello(args: Array<T>){ 
    ... 
} 
　 
... 
hello<*>(args) // ERROR!! 
```

`*` 不允许作为函数和变量的类型的泛型参数！

```kotlin
interface Foo<T> 
　 
class Bar : Foo<*> // ERROR! 
```

`*` 不能直接作为父类的泛型参数传入！

```kotlin
interface Foo<T> 
　 
class Bar : Foo<Foo<*>> 
```

这是正确的。注意，尽管 `*` 不能直接作为类的泛型参数，`Foo<*>` 却可以，按照前面官方给出的说法，它在读时等价于`Foo<out Any>` 写时等价于 `Foo<in Nothing>`

```kotlin
fun hello(args: Array<*>){ 
    ... 
} 
```

同样，这表示接受的参数的类型在读写时分别等价于`Array<out Any>` 和 `Array<in Nothing>`

## 4. 其他

### 4.1 Raw 类型

Raw 类型就是对于定义时有泛型参数要求，但在使用时指定泛型参数的情况，这个只在 Java 中有，显然也是为了前向兼容而已。

例如：

```java
List list = new ArrayList(); 
```

这类用法在 Kotlin 当中是不被允许的。上面的代码大致相当于：

```kotlin
val list = ArrayList<Any?>() 
```

不过，在 Java 中，raw 类型可以有这种写法：

```java
List<Integer> integers = new ArrayList<>(); 
List list = new ArrayList(); 
list = integers; 
```

但 Kotlin 中，单纯的 `ArrayList<Any?>` 并不是协变的，所以下面的写法是错误的：

```kotlin
var list = ArrayList<Any?>() 
val integers = ArrayList<Int>() 
list = integers // ERROR! 
```

Java，你这样做很危险呀。

### 4.2 泛型边界

在 Java 中，我们同样可以用 extends 为泛型参数指定上限：

```java
class NumberFormatter<T extends Number>{ 
   ... 
} 
```

这表示使用时，泛型参数必须是 Number 及其子类的一种。

而在 Kotlin 中，写法与继承类似：

```kotlin
class NumberFormatter<T: Number>{ 
   ... 
} 
```

如果有多个上界，那么：

```kotlin
class NumberFormatter<T> where T: Number, T: Cloneable{ 
   ... 
} 
```

## 5. 小结

通过上面的讨论，其实大家会发现 Kotlin 的泛型相比 Java 有了更严格的约束，更简洁的表述，更灵活的配置，但背后的思路和具体的实现总体来说是一致的。