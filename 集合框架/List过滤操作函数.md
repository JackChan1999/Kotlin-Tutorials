### 过滤操作函数

#### `take(n: Int): List<T>` 挑出该集合前n个元素的子集合

函数定义：

```
public fun <T> Iterable<T>.take(n: Int): List<T> {
    require(n >= 0) { "Requested element count $n is less than zero." }
    if (n == 0) return emptyList()
    if (this is Collection<T>) {
        if (n >= size) return toList()
        if (n == 1) return listOf(first())
    }
    var count = 0
    val list = ArrayList<T>(n)
    for (item in this) {
        if (count++ == n)
            break
        list.add(item)
    }
    return list.optimizeReadOnlyList()
}
```

如果n等于0，返回空集；如果n大于集合`size`，返回该集合。

代码示例：

```
>>> val list = listOf("a","b","c")
>>> list
[a, b, c]
>>> list.take(2)
[a, b]
>>> list.take(10)
[a, b, c]
>>> list.take(0)
[]

```

#### `takeWhile(predicate: (T) -> Boolean): List<T>` 挑出满足条件的元素的子集合

函数定义：

```
public inline fun <T> Iterable<T>.takeWhile(predicate: (T) -> Boolean): List<T> {
    val list = ArrayList<T>()
    for (item in this) {
        if (!predicate(item))
            break
        list.add(item)
    }
    return list
}
```

从第一个元素开始，判断是否满足`predicate`为true，如果满足条件的元素就丢到返回`ArrayList`中。只要遇到任何一个元素不满足条件，就结束循环，返回list 。

代码示例：

```
>>> val list = listOf(1,2,4,6,8,9)
>>> list.takeWhile({it%2==0})
[]
>>> list.takeWhile({it%2==1})
[1]

>>> val list = listOf(2,4,6,8,9,11,12,16)
>>> list.takeWhile({it%2==0})
[2, 4, 6, 8]

```

#### `takeLast` 挑出后n个元素的子集合

函数定义：

```
public fun <T> List<T>.takeLast(n: Int): List<T> {
    require(n >= 0) { "Requested element count $n is less than zero." }
    if (n == 0) return emptyList()
    val size = size
    if (n >= size) return toList()
    if (n == 1) return listOf(last())
    val list = ArrayList<T>(n)
    if (this is RandomAccess) {
        for (index in size - n .. size - 1)
            list.add(this[index])
    } else {
        for (item in listIterator(n))
            list.add(item)
    }
    return list
}
```

从集合倒数n个元素起，取出到最后一个元素的子集合。如果传入0，返回空集。如果传入n大于集合size，返回整个集合。如果传入负数，直接抛出IllegalArgumentException。

代码示例：

```
>>> val list = listOf(2,4,6,8,9,11,12,16)
>>> list.takeLast(0)
[]
>>> list.takeLast(3)
[11, 12, 16]
>>> list.takeLast(100)
[2, 4, 6, 8, 9, 11, 12, 16]
>>> list.takeLast(-1)
java.lang.IllegalArgumentException: Requested element count -1 is less than zero.
    at kotlin.collections.CollectionsKt___CollectionsKt.takeLast(_Collections.kt:734)

```

#### `takeLastWhile(predicate: (T) -> Boolean)` 从最后开始挑出满足条件元素的子集合

函数定义：

```
public inline fun <T> List<T>.takeLastWhile(predicate: (T) -> Boolean): List<T> {
    if (isEmpty())
        return emptyList()
    val iterator = listIterator(size)
    while (iterator.hasPrevious()) {
        if (!predicate(iterator.previous())) {
            iterator.next()
            val expectedSize = size - iterator.nextIndex()
            if (expectedSize == 0) return emptyList()
            return ArrayList<T>(expectedSize).apply {
                while (iterator.hasNext())
                    add(iterator.next())
            }
        }
    }
    return toList()
}
```

反方向取满足条件的元素，遇到不满足的元素，直接终止循环，并返回子集合。

代码示例：

```
>>> val list = listOf(2,4,6,8,9,11,12,16)
>>> list.takeLastWhile({it%2==0})
[12, 16]
```

#### `drop(n: Int)` 去除前n个元素返回剩下的元素的子集合

函数定义：

```
public fun <T> Iterable<T>.drop(n: Int): List<T> {
    require(n >= 0) { "Requested element count $n is less than zero." }
    if (n == 0) return toList()
    val list: ArrayList<T>
    if (this is Collection<*>) {
        val resultSize = size - n
        if (resultSize <= 0)
            return emptyList()
        if (resultSize == 1)
            return listOf(last())
        list = ArrayList<T>(resultSize)
        if (this is List<T>) {
            if (this is RandomAccess) {
                for (index in n..size - 1)
                    list.add(this[index])
            } else {
                for (item in listIterator(n))
                    list.add(item)
            }
            return list
        }
    }
    else {
        list = ArrayList<T>()
    }
    var count = 0
    for (item in this) {
        if (count++ >= n) list.add(item)
    }
    return list.optimizeReadOnlyList()
}
```

代码示例：

```
>>> val list = listOf(2,4,6,8,9,11,12,16)
>>> list.drop(5)
[11, 12, 16]
>>> list.drop(100)
[]
>>> list.drop(0)
[2, 4, 6, 8, 9, 11, 12, 16]
>>> list.drop(-1)
java.lang.IllegalArgumentException: Requested element count -1 is less than zero.
    at kotlin.collections.CollectionsKt___CollectionsKt.drop(_Collections.kt:538)
```

#### `dropWhile(predicate: (T) -> Boolean)` 去除满足条件的元素返回剩下的元素的子集合

函数定义：

```
public inline fun <T> Iterable<T>.dropWhile(predicate: (T) -> Boolean): List<T> {
    var yielding = false
    val list = ArrayList<T>()
    for (item in this)
        if (yielding)
            list.add(item)
        else if (!predicate(item)) {
            list.add(item)
            yielding = true
        }
    return list
}
```

去除满足条件的元素，当遇到一个不满足条件的元素时，中止操作，返回剩下的元素子集合。

代码示例：

```
>>> val list = listOf(2,4,6,8,9,11,12,16)
>>> list.dropWhile({it%2==0})
[9, 11, 12, 16]

```

#### `dropLast(n: Int)` 从最后去除n个元素

函数定义：

```
public fun <T> List<T>.dropLast(n: Int): List<T> {
    require(n >= 0) { "Requested element count $n is less than zero." }
    return take((size - n).coerceAtLeast(0))
}
```

代码示例：

```
>>> val list = listOf(2,4,6,8,9,11,12,16)
>>> list.dropLast(3)
[2, 4, 6, 8, 9]
>>> list.dropLast(100)
[]
>>> list.dropLast(0)
[2, 4, 6, 8, 9, 11, 12, 16]
>>> list.dropLast(-1)
java.lang.IllegalArgumentException: Requested element count -1 is less than zero.
    at kotlin.collections.CollectionsKt___CollectionsKt.dropLast(_Collections.kt:573)


```

#### `dropLastWhile(predicate: (T) -> Boolean)` 从最后满足条件的元素

函数定义：

```
public inline fun <T> List<T>.dropLastWhile(predicate: (T) -> Boolean): List<T> {
    if (!isEmpty()) {
        val iterator = listIterator(size)
        while (iterator.hasPrevious()) {
            if (!predicate(iterator.previous())) {
                return take(iterator.nextIndex() + 1)
            }
        }
    }
    return emptyList()
}
```

代码示例：

```
>>> val list = listOf(2,4,6,8,9,11,12,16)
>>> list.dropLastWhile({it%2==0})
[2, 4, 6, 8, 9, 11]
```

#### `slice(indices: IntRange)` 取开始下标至结束下标元素子集合

函数定义：

```
public fun <T> List<T>.slice(indices: IntRange): List<T> {
    if (indices.isEmpty()) return listOf()
    return this.subList(indices.start, indices.endInclusive + 1).toList()
}
```

代码示例：

```
val list = listOf(2,4,6,8,9,11,12,16)
>>> list
[2, 4, 6, 8, 9, 11, 12, 16]
>>> list.slice(1..3)
[4, 6, 8]
>>> list.slice(2..7)
[6, 8, 9, 11, 12, 16]
>>> list
[2, 4, 6, 8, 9, 11, 12, 16]
>>> list.slice(1..3)
[4, 6, 8]
>>> list.slice(2..7)
[6, 8, 9, 11, 12, 16]
```

#### `slice(indices: Iterable<Int>) `返回指定下标的元素子集合

函数定义：

```
public fun <T> List<T>.slice(indices: Iterable<Int>): List<T> {
    val size = indices.collectionSizeOrDefault(10)
    if (size == 0) return emptyList()
    val list = ArrayList<T>(size)
    for (index in indices) {
        list.add(get(index))
    }
    return list
}
```

这个函数从签名上看，不是那么简单直接。从函数的定义看，这里的indices是当做原来集合的下标来使用的。

代码示例：

```
>>> list
[2, 4, 6, 8, 9, 11, 12, 16]
>>> list.slice(listOf(2,4,6))
[6, 9, 12]
```

我们可以看出，这里是取出下标为2，4，6的元素。而不是直观理解上的，去掉元素2，4，6。

#### `filterTo(destination: C, predicate: (T) -> Boolean)` 过滤出满足条件的元素并赋值给destination

函数定义：

```
public inline fun <T, C : MutableCollection<in T>> Iterable<T>.filterTo(destination: C, predicate: (T) -> Boolean): C {
    for (element in this) if (predicate(element)) destination.add(element)
    return destination
}
```

把满足过滤条件的元素组成的子集合赋值给入参destination。

代码示例：

```
>>> val list = listOf(1,2,3,4,5,6,7)
>>> val dest = mutableListOf<Int>()
>>> list.filterTo(dest,{it>3})
[4, 5, 6, 7]
>>> dest
[4, 5, 6, 7]
```

#### `filter(predicate: (T) -> Boolean)`过滤出满足条件的元素组成的子集合

函数定义：

```
public inline fun <T> Iterable<T>.filter(predicate: (T) -> Boolean): List<T> {
    return filterTo(ArrayList<T>(), predicate)
}
```

相对于filterTo函数，filter函数更加简单易用。从源码我们可以看出，filter函数直接调用的`filterTo(ArrayList<T>(), predicate)`， 其中入参destination被直接默认赋值为`ArrayList<T>()`。

代码示例：

```
>>> val list = listOf(1,2,3,4,5,6,7)
>>> list.filter({it>3})
[4, 5, 6, 7]
```

另外，还有下面常用的过滤函数：

`filterNot(predicate: (T) -> Boolean)`, 用来过滤所有不满足条件的元素 ；
`filterNotNull()` 过滤掉`null`元素。