### List集合类的基本运算函数

#### `any() `判断集合至少有一个元素

这个函数定义如下：

```kotlin
public fun <T> Iterable<T>.any(): Boolean {
    for (element in this) return true
    return false
}
```

如果该集合至少有一个元素，返回`true`，否则返回`false`。

代码示例：

```kotlin
>>> val emptyList = listOf<Int>()
>>> emptyList.any()
false
>>> val list1 = listOf(1)
>>> list1.any()
true
```

#### `any(predicate: (T) -> Boolean)` 判断集合中是否有满足条件的元素

这个函数定义如下：

```kotlin
public inline fun <T> Iterable<T>.any(predicate: (T) -> Boolean): Boolean {
    for (element in this) if (predicate(element)) return true
    return false
}
```

如果该集合中至少有一个元素匹配谓词函数参数`predicate: (T) -> Boolean`，返回true，否则返回false。

代码示例：

```kotlin
>>> val list = listOf(1, 2, 3)
>>> list.any() // 至少有1个元素
true
>>> list.any({it%2==0}) // 元素2满足{it%2==0}
true
>>> list.any({it>4}) // 没有元素满足{it>4}
false
```

#### `all(predicate: (T) -> Boolean)` 判断集合中的元素是否都满足条件

函数定义：

```kotlin
public inline fun <T> Iterable<T>.all(predicate: (T) -> Boolean): Boolean {
    for (element in this) if (!predicate(element)) return false
    return true
}
```

当且仅当该集合中所有元素都满足条件时，返回`true`；否则都返回`false`。

代码示例：

```kotlin
>>> val list = listOf(0,2,4,6,8)
>>> list.all({it%2==0})
true
>>> list.all({it>2})
false
```

#### `none() `判断集合无元素

函数定义：

```kotlin
public fun <T> Iterable<T>.none(): Boolean {
    for (element in this) return false
    return true
}
```

如果该集合没有任何元素，返回`true`，否则返回`false`。

代码示例：

```kotlin
>>> val list = listOf<Int>()
>>> list.none()
true
```

#### `none(predicate: (T) -> Boolean)`判断集合中所有元素都不满足条件

函数定义：

```kotlin
public inline fun <T> Iterable<T>.none(predicate: (T) -> Boolean): Boolean {
    for (element in this) if (predicate(element)) return false
    return true
}
```

当且仅当集合中所有元素都不满足条件时返回`true`，否则返回`false`。

代码示例：

```kotlin
>>> val list = listOf(0,2,4,6,8)
>>> list.none({it%2==1})
true
>>> list.none({it>0})
false
```

#### `count()` 计算集合中元素的个数

函数定义：

```kotlin
public fun <T> Iterable<T>.count(): Int {
    var count = 0
    for (element in this) count++
    return count
}
```

代码示例：

```kotlin
>>> val list = listOf(0,2,4,6,8,9)
>>> list.count()
6
```

#### `count(predicate: (T) -> Boolean)` 计算集合中满足条件的元素的个数

函数定义：

```kotlin
public inline fun <T> Iterable<T>.count(predicate: (T) -> Boolean): Int {
    var count = 0
    for (element in this) if (predicate(element)) count++
    return count
}
```

代码示例：

```kotlin
>>> val list = listOf(0,2,4,6,8,9)
>>> list.count()
6
>>> list.count({it%2==0})
5
```

#### `reduce`从第一项到最后一项进行累计运算

函数定义：

```kotlin
public inline fun <S, T: S> Iterable<T>.reduce(operation: (acc: S, T) -> S): S {
    val iterator = this.iterator()
    if (!iterator.hasNext()) throw UnsupportedOperationException("Empty collection can't be reduced.")
    var accumulator: S = iterator.next()
    while (iterator.hasNext()) {
        accumulator = operation(accumulator, iterator.next())
    }
    return accumulator
}
```

首先把第一个元素赋值给累加子`accumulator`，然后逐次向后取元素累加，新值继续赋值给累加子`accumulator = operation(accumulator, iterator.next())`，以此类推。最后返回累加子的值。

代码示例：

```kotlin
>>> val list = listOf(1,2,3,4,5,6,7,8,9)
>>> list.reduce({sum, next->sum+next})
45
>>> list.reduce({sum, next->sum*next})
362880
>>> val list = listOf("a","b","c")
>>> list.reduce({total, s->total+s})
abc

```

#### `reduceRight`从最后一项到第一项进行累计运算

函数定义：

```kotlin
public inline fun <S, T: S> List<T>.reduceRight(operation: (T, acc: S) -> S): S {
    val iterator = listIterator(size)
    if (!iterator.hasPrevious())
        throw UnsupportedOperationException("Empty list can't be reduced.")
    var accumulator: S = iterator.previous()
    while (iterator.hasPrevious()) {
        accumulator = operation(iterator.previous(), accumulator)
    }
    return accumulator
}

```

从函数的定义`accumulator = operation(iterator.previous(), accumulator)`, 我们可以看出，从右边累计运算的累加子是放在后面的。

代码示例：

```kotlin
>>> val list = listOf("a","b","c")
>>> list.reduceRight({total, s -> s+total})
cba
```

如果我们位置放错了，会输出下面的结果：

```kotlin
>>> list.reduceRight({total, s -> total+s})
abc
```

#### `fold(initial: R, operation: (acc: R, T) -> R): R` 带初始值的reduce

函数定义：

```kotlin
public inline fun <T, R> Iterable<T>.fold(initial: R, operation: (acc: R, T) -> R): R {
    var accumulator = initial
    for (element in this) accumulator = operation(accumulator, element)
    return accumulator
}
```

从函数的定义，我们可以看出，fold函数给累加子赋了初始值`initial`。

代码示例：

```kotlin
>>> val list=listOf(1,2,3,4)
>>> list.fold(100,{total, next -> next + total})
110
```

`foldRight`和`reduceRight`类似，有初始值。

函数定义：

```kotlin
public inline fun <T, R> List<T>.foldRight(initial: R, operation: (T, acc: R) -> R): R {
    var accumulator = initial
    if (!isEmpty()) {
        val iterator = listIterator(size)
        while (iterator.hasPrevious()) {
            accumulator = operation(iterator.previous(), accumulator)
        }
    }
    return accumulator
}
```

代码示例：

```kotlin
>>> val list = listOf("a","b","c")
>>> list.foldRight("xyz",{s, pre -> pre + s})
xyzcba
```

#### `forEach(action: (T) -> Unit): Unit` 循环遍历元素，元素是it

我们在前文已经讲述，参看5.3.4。

再写个代码示例：

```kotlin
>>> val list = listOf(0, 1, 2, 3, 4, 5, 6, 7, 8, 9) 
>>> list.forEach { value -> if (value > 7) println(value) } 
8
9
```

#### `forEachIndexed` 带index(下标) 的元素遍历

函数定义：

```kotlin
public inline fun <T> Iterable<T>.forEachIndexed(action: (index: Int, T) -> Unit): Unit {
    var index = 0
    for (item in this) action(index++, item)
}
```

代码示例：

```kotlin
>>> val list = listOf(0, 1, 2, 3, 4, 5, 6, 7, 8, 9) 
>>> list.forEachIndexed { index, value -> if (value > 8) println("value of index $index is $value, greater than 8") } 

value of index 9 is 9, greater than 8
```

#### max 、min查询最大、最小的元素，空集则返回null

`max`函数定义：

```kotlin
public fun <T : Comparable<T>> Iterable<T>.max(): T? {
    val iterator = iterator()
    if (!iterator.hasNext()) return null
    var max = iterator.next()
    while (iterator.hasNext()) {
        val e = iterator.next()
        if (max < e) max = e
    }
    return max
}
```

返回集合中最大的元素。

代码示例：

```kotlin
>>> val list = listOf(1,2,3)
>>> list.max()
3
>>> val list = listOf("a","b","c")
>>> list.max()
c

```

`min`函数定义：

```kotlin
public fun <T : Comparable<T>> Iterable<T>.min(): T? {
    val iterator = iterator()
    if (!iterator.hasNext()) return null
    var min = iterator.next()
    while (iterator.hasNext()) {
        val e = iterator.next()
        if (min > e) min = e
    }
    return min
}
```

返回集合中的最小元素。

代码示例：

```kotlin
>>> val list = listOf(1,2,3)
>>> list.min()
1
>>> val list = listOf("a","b","c")
>>> list.min()
a

```

在Kotlin中，字符串的大小比较比较有意思的，我们直接通过代码示例来学习一下：

```kotlin
>>> "c" > "a"
true
>>> "abd" > "abc"
true
>>> "abd" > "abcd"
true
>>> "abd" > "abcdefg"
true
```

我们可以看出，字符串的大小比较是按照对应的下标的字符进行比较的。
另外，布尔值的比较是`true`大于`false`：

```kotlin
>>> true > false
true
```

#### `maxBy(selector: (T) -> R): T?` 、 `minBy(selector: (T) -> R): T?`获取函数映射结果的最大值、最小值对应的那个元素的值，如果没有则返回null

函数定义：

```kotlin
public inline fun <T, R : Comparable<R>> Iterable<T>.maxBy(selector: (T) -> R): T? {
    val iterator = iterator()
    if (!iterator.hasNext()) return null
    var maxElem = iterator.next()
    var maxValue = selector(maxElem)
    while (iterator.hasNext()) {
        val e = iterator.next()
        val v = selector(e)
        if (maxValue < v) {
            maxElem = e
            maxValue = v
        }
    }
    return maxElem
}
```

也就是说，不直接比较集合元素的大小，而是以集合元素为入参的函数`selector: (T) -> R` 返回值来比较大小，最后返回此元素的值（注意，不是对应的`selector`函数的返回值）。有点像数学里的求函数最值问题：

给定函数 `y = f(x)` ， 求` max f(x) `的`x`的值。

代码示例：

```kotlin
>>> val list = listOf(100,-500,300,200)
>>> list.maxBy({it})
300
>>> list.maxBy({it*(1-it)})
100
>>> list.maxBy({it*it})
-500
```

对应的 `minBy` 是获取函数映射后返回结果的最小值所对应那个元素的值，如果没有则返回null。

代码示例：

```kotlin
>>> val list = listOf(100,-500,300,200)
>>> list.minBy({it})
-500
>>> list.minBy({it*(1-it)})
-500
>>> list.minBy({it*it})
100
```

#### `sumBy(selector: (T) -> Int): Int` 获取函数映射值的总和

函数定义：

```kotlin
public inline fun <T> Iterable<T>.sumBy(selector: (T) -> Int): Int {
    var sum: Int = 0
    for (element in this) {
        sum += selector(element)
    }
    return sum
}

```

可以看出，这个`sumBy`函数算子，累加器`sum`初始值为0，返回值是`Int`。它的入参`selector`是一个函数类型`(T) -> Int`，也就是说这个selector也是返回Int类型的函数。

代码示例：

```kotlin
>>> val list = listOf(1,2,3,4)
>>> list.sumBy({it})
10
>>> list.sumBy({it*it})
30
```

类型错误反例：

```kotlin
>>> val list = listOf("a","b","c")
>>> list.sumBy({it})
error: type inference failed: inline fun <T> Iterable<T>.sumBy(selector: (T) -> Int): Int
cannot be applied to
receiver: List<String>  arguments: ((String) -> String)

list.sumBy({it})
     ^
error: type mismatch: inferred type is (String) -> String but (String) -> Int was expected
list.sumBy({it})
           ^
```