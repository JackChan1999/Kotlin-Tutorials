## List

List接口继承于Collection接口，元素以线性方式存储，集合中可以存放重复对象。Kotlin的List分为：不可变集合类List（ReadOnly, Immutable）和可变集合类MutableList（Read&Write, Mutable）。

其类图结构如下：

![img](https://segmentfault.com/img/remote/1460000010313210)

其中，`Iterator`是所有容器类`Collection`的迭代器。迭代器（Iterator）模式，又叫做游标（Cursor）模式。GOF给出的定义为：提供一种方法访问一个容器对象中各个元素，而又不需暴露该对象的内部细节。 从定义可见，迭代器模式是为容器而生。

### 创建不可变List

我们可以使用`listOf`函数来构建一个不可变的List（read-only，只读的List）。它定义在`libraries/stdlib/src/kotlin/collections/Collections.kt` 里面。关于`listOf`这个构建函数有下面3个重载函数：

```
@kotlin.internal.InlineOnly
public inline fun <T> listOf(): List<T> = emptyList()

public fun <T> listOf(vararg elements: T): List<T> = if (elements.size > 0) elements.asList() else emptyList()

@JvmVersion
public fun <T> listOf(element: T): List<T> = java.util.Collections.singletonList(element)

```

这些函数创建的List都是是只读的（readonly，也就是不可变的immutable ）、可序列化的。

其中，

- `listOf()`用于创建没有元素的空List
- `listOf(vararg elements: T)`用于创建只有一个元素的List
- `listOf(element: T)`用于创建拥有多个元素的List

我们使用代码示例分别来演示其用法：

首先，我们使用`listOf()`来构建一个没有元素的空的List：

```kotlin
>>> val list:List<Int> = listOf()
>>> list
[]
>>> list::class
class kotlin.collections.EmptyList
```

注意，这里的变量的类型不能省略，否则会报错：

```kotlin
>>> val list = listOf()
error: type inference failed: Not enough information to infer parameter T in inline fun <T> listOf(): List<T>
Please specify it explicitly.

val list = listOf()
           ^
```

因为这是一个泛型函数。关于泛型，我们将在下一章中介绍。

其中，`EmptyList` 是一个 `internal object EmptyList`, 这是Kotlin内部定义的一个默认空的object List类。

下面，我们再来创建一个只有1个元素的List：

```kotlin
>>> val list = listOf(1)
>>> list::class
class java.util.Collections$SingletonList
```

我们可以看出，它实际上是调用Java的`java.util.Collections` 里面的`singletonList`方法：

```kotlin
public static <T> List<T> singletonList(T o) {
    return new SingletonList<>(o);
}
```

我们再来创建一个有多个元素的List:

```kotlin
>>> val list = listOf(0,1, 2, 3, 4, 5, 6,7,8,9)
>>> list
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> list::class
class java.util.Arrays$ArrayList
>>> list::class.java
```

它调用的是

```kotlin
fun <T> listOf(vararg elements: T): List<T> = if (elements.size > 0) elements.asList() else emptyList()
```

这个函数。其中，`asList`函数是`Array`的扩展函数：

```kotlin
public fun <T> Array<out T>.asList(): List<T> {
    return ArraysUtilJVM.asList(this)
}
```

而这个`ArraysUtilJVM`是一个Java类，里面实际上调用的是`java.util.Arrays`和`java.util.List` :

```kotlin
package kotlin.collections;

import java.util.Arrays;
import java.util.List;

class ArraysUtilJVM {
    static <T> List<T> asList(T[] array) {
        return Arrays.asList(array);
    }
}
```

另外，我们还可以直接使用`arrayListOf`函数来创建一个Java中的ArrayList对象实例：

```kotlin
>>> val list = arrayListOf(0,1,2,3)
>>> list
[0, 1, 2, 3]
>>> list::class
class java.util.ArrayList
>>> val list = listOf(0,1, 2, 3, 4, 5, 6,7,8,9) 
>>> list::class
class java.util.Arrays$ArrayList
```

这个函数定义在`libraries/stdlib/src/kotlin/collections/Collections.kt`类中：

```kotlin
@SinceKotlin("1.1")
@kotlin.internal.InlineOnly
public inline fun <T> arrayListOf(): ArrayList<T> = ArrayList()
```

同样的处理方式，这里的`ArrayList()`是Java中的` java.util.ArrayList`的类型别名：

```kotlin
@SinceKotlin("1.1") public typealias ArrayList<E> = java.util.ArrayList<E>
```

### 创建可变集合MutableList

在MutableList中，除了继承List中的那些函数外，另外新增了add/addAll、remove/removeAll/removeAt、set、clear、retainAll等更新修改的操作函数。

```kotlin
override fun add(element: E): Boolean
override fun remove(element: E): Boolean
override fun addAll(elements: Collection<E>): Boolean
fun addAll(index: Int, elements: Collection<E>): Boolean
override fun removeAll(elements: Collection<E>): Boolean
override fun retainAll(elements: Collection<E>): Boolean
override fun clear(): Unit
operator fun set(index: Int, element: E): E
fun add(index: Int, element: E): Unit
fun removeAt(index: Int): E
override fun listIterator(): MutableListIterator<E>
override fun listIterator(index: Int): MutableListIterator<E>
override fun subList(fromIndex: Int, toIndex: Int): MutableList<E>
```

创建一个MutableList的对象实例跟List类似，前面加上前缀`mutable`，代码示例如下：

```kotlin
>>> val list = mutableListOf(1, 2, 3)
>>> list
[1, 2, 3]
>>> list::class
class java.util.ArrayList
>>> val list2 = mutableListOf<Int>()
>>> list2
[]
>>> list2::class
class java.util.ArrayList
>>> val list3 = mutableListOf(1)
>>> list3
[1]
>>> list3::class
class java.util.ArrayList
```

我们可以看出，使用`mutableListOf`函数创建的可变集合类，实际上背后调用的是`java.util.ArrayList`类的相关方法。

另外，我们可以直接使用Kotlin封装的`arrayListOf`函数来创建一个ArrayList：

```kotlin
>>> val list4 = arrayListOf(1, 2, 3)
>>> list4::class
class java.util.ArrayList
```

关于Kotlin中的`ArrayList`类型别名定义在
`kotlin/collections/TypeAliases.kt` 文件中：

```kotlin
@file:kotlin.jvm.JvmVersion

package kotlin.collections

@SinceKotlin("1.1") public typealias RandomAccess = java.util.RandomAccess
@SinceKotlin("1.1") public typealias ArrayList<E> = java.util.ArrayList<E>
@SinceKotlin("1.1") public typealias LinkedHashMap<K, V> = java.util.LinkedHashMap<K, V>
@SinceKotlin("1.1") public typealias HashMap<K, V> = java.util.HashMap<K, V>
@SinceKotlin("1.1") public typealias LinkedHashSet<E> = java.util.LinkedHashSet<E>
@SinceKotlin("1.1") public typealias HashSet<E> = java.util.HashSet<E>

// also @SinceKotlin("1.1")
internal typealias SortedSet<E> = java.util.SortedSet<E>
internal typealias TreeSet<E> = java.util.TreeSet<E>
```

如果我们已经有了一个不可变的List，而我们现在想把他转换成可变的List，我们可以直接调用转换函数`toMutableList`：

```kotlin
val list = mutableListOf(1, 2, 3)
val mlist = list.toMutableList()
mlist.add(5)
```

### 遍历List元素

#### 使用Iterator迭代器

我们以集合 `val list = listOf(0,1, 2, 3, 4, 5, 6,7,8,9) `为例，使用Iterator迭代器遍历列表所有元素的操作：

```kotlin
>>> val list = listOf(0,1, 2, 3, 4, 5, 6,7,8,9) 
>>> list
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> val iterator = list.iterator()
>>> iterator
java.util.AbstractList$Itr@438bad7c
>>> while(iterator.hasNext()){
... println(iterator.next())
... }
0
1
2
3
4
5
6
7
8
9
```

迭代器是一种设计模式，它是一个对象，它可以遍历并选择序列中的对象，而开发人员不需要了解该序列的底层结构。迭代器通常被称为“轻量级”对象，因为创建它的代价小。

Kotlin中的Iterator功能比较简单，并且只能单向移动：

（1） 调用iterator()函数，容器返回一个Iterator实例。iterator()函数是`kotlin.collections.Iterable`中的函数, 被Collection继承。
（2）调用hasNext()函数检查序列中是否还有元素。
（3）第一次调用Iterator的next()函数时，它返回序列的第一个元素。依次向后递推，使用next()获得序列中的下一个元素。

当我们调用到最后一个元素，再次调用`next()`函数，会抛这个异常`java.util.NoSuchElementException`。代码示例：

```kotlin
>>> val list = listOf(1,2,3)
>>> val iter = list.iterator()
>>> iter
java.util.AbstractList$Itr@3abfe845
>>> iter.hasNext()
true
>>> iter.next()
1
>>> iter.hasNext()
true
>>> iter.next()
2
>>> iter.hasNext()
true
>>> iter.next()
3
>>> iter.hasNext()
false
>>> iter.next()
java.util.NoSuchElementException
    at java.util.AbstractList$Itr.next(AbstractList.java:364)
```

我们可以看出，这里的Iterator的实现是在`AbstractList`中的内部类`IteratorImpl`：

```kotlin
private open inner class IteratorImpl : Iterator<E> {
        protected var index = 0
        override fun hasNext(): Boolean = index < size
        override fun next(): E {
            if (!hasNext()) throw NoSuchElementException()
            return get(index++)
        }
    }
```

通过这个实现源码，我们可以更加清楚地明白Iterator的工作原理。

其中，`NoSuchElementException()`这个类是`java.util.NoSuchElementException`的类型别名：

```kotlin
@kotlin.SinceKotlin public typealias NoSuchElementException = java.util.NoSuchElementException
```

#### 使用`forEach`遍历List元素

这个`forEach`函数定义如下：

```kotlin
@kotlin.internal.HidesMembers
public inline fun <T> Iterable<T>.forEach(action: (T) -> Unit): Unit {
    for (element in this) action(element)
}
```

它是`package kotlin.collections`包下面的Iterable的扩展内联函数。它的入参是一个函数类型：

```kotlin
action: (T) -> Unit
```

关于函数式编程，我们将在后面章节中学习。

这里的`forEach`是一个语法糖。实际上`forEach`在遍历List对象的时候，仍然使用的是iterator迭代器来进行循环遍历的。

```kotlin
>>> val list = listOf(1,2,3)
>>> list
[1, 2, 3]
>>> list.forEach{
... println(it)
... }
1
2
3
```

当参数只有一个函数的时候，括号可以省略不写。

也就是说，这里面的forEach函数调用的写法，实际上跟下面的写法等价：

```kotlin
list.forEach({
    println(it)
})
```

我们甚至还可以直接这样写：

```kotlin
>>> list.forEach(::println)
```

其中，`::` 是函数引用符。