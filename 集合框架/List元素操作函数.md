### 5.3.4 List元素操作函数

#### `add` `remove` `set` `clear`

这两个添加、删除操作函数是MutableList里面的。跟Java中的集合类操作类似。

创建一个可变集合：

```
>>> val mutableList = mutableListOf(1,2,3)
>>> mutableList
[1, 2, 3]
```

向集合中添加一个元素：

```
>>> mutableList.add(4)
true
>>> mutableList
[1, 2, 3, 4]
```

在下标为0的位置添加元素0 ：

```
>>> mutableList.add(0,0)
>>> mutableList
[0, 1, 2, 3, 4]
```

删除元素1 ：

```
>>> mutableList.remove(1)
true
>>> mutableList
[0, 2, 3, 4]
>>> mutableList.remove(1)
false
```

删除下标为1的元素：

```
>>> mutableList.removeAt(1)
2
>>> mutableList
[0, 3, 4]
```

删除子集合：

```
>>> mutableList.removeAll(listOf(3,4))
true
>>> mutableList
[0]
```

添加子集合：

```
>>> mutableList.addAll(listOf(1,2,3))
true
>>> mutableList
[1, 2, 3]
```

更新设置下标0的元素值为100：

```
>>> mutableList.set(0,100)
0
>>> mutableList
[100]
```

清空集合：

```
>>> mutableList.clear()
>>> mutableList
[]

```

把可变集合转为不可变集合：

```
>>> mutableList.toList()
[1, 2, 3]
```

#### `retainAll`

取两个集合交集：

```
>>> val mlist1 = mutableListOf(1,2,3,4,5,6)
>>> val mlist2 = mutableListOf(3,4,5,6,7,8,9)
>>> mlist1.retainAll(mlist2)
true
>>> mlist1
[3, 4, 5, 6]

```

#### `contains(element: T): Boolean`

判断集合中是否有指定元素，有就返回true，否则返回false 。
代码示例：

```
>>> val list = listOf(1,2,3,4,5,6,7)
>>> list.contains(1)
true
```

#### `elementAt(index: Int): T`

查找下标对应的元素，如果下标越界会抛IndexOutOfBoundsException。
代码示例：

```
>>> val list = listOf(1,2,3,4,5,6,7)
>>> list.elementAt(6)
7
>>> list.elementAt(7)
java.lang.ArrayIndexOutOfBoundsException: 7
    at java.util.Arrays$ArrayList.get(Arrays.java:3841)


```

另外，针对越界的处理，还有下面两个函数：

`elementAtOrElse(index: Int, defaultValue: (Int) -> T): T` : 查找下标对应元素，如果越界会根据方法返回默认值。

```
>>> list.elementAtOrElse(7,{0})
0
>>> list.elementAtOrElse(7,{10})
10
```

`elementAtOrNull(index: Int): T?` : 查找下标对应元素，如果越界就返回null

```
>>> list.elementAtOrNull(7)
null
```

#### `first()`

返回集合第1个元素，如果是空集，抛出异常NoSuchElementException。

```
>>> val list = listOf(1,2,3)
>>> list.first()
1
>>> val emptyList = listOf<Int>()
>>> emptyList.first()
java.util.NoSuchElementException: List is empty.
    at kotlin.collections.CollectionsKt___CollectionsKt.first(_Collections.kt:178)


```

对应的有针对异常处理的函数`firstOrNull(): T?` :

```
>>> emptyList.firstOrNull()
null
```

#### `first(predicate: (T) -> Boolean): T`

返回符合条件的第一个元素，没有则抛异常NoSuchElementException 。

```
>>> val list = listOf(1,2,3)
>>> list.first({it%2==0})
2
>>> list.first({it>100})
java.util.NoSuchElementException: Collection contains no element matching the predicate.

```

对应的有针对异常处理的函数`firstOrNull(predicate: (T) -> Boolean): T?` ，返回符合条件的第一个元素，没有就返回null ：

```
>>> list.firstOrNull({it>100})
null
```

#### `indexOf(element: T): Int`

返回指定下标的元素，没有就返回-1

```
>>> val list = listOf("a","b","c")
>>> list.indexOf("c")
2
>>> list.indexOf("x")
-1
```

#### `indexOfFirst(predicate: (T) -> Boolean): Int`

返回第一个符合条件的元素下标，没有就返回-1 。

```
>>> val list = listOf("abc","xyz","xjk","pqk")
>>> list.indexOfFirst({it.contains("x")})
1
>>> list.indexOfFirst({it.contains("k")})
2
>>> list.indexOfFirst({it.contains("e")})
-1
```

#### `indexOfLast(predicate: (T) -> Boolean): Int`

返回最后一个符合条件的元素下标，没有就返回-1 。

```
>>> val list = listOf("abc","xyz","xjk","pqk")
>>> list.indexOfLast({it.contains("x")})
2
>>> list.indexOfLast({it.contains("k")})
3
>>> list.indexOfLast({it.contains("e")})
-1
```

#### `last()`

返回集合最后一个元素，空集则抛出异常NoSuchElementException。

```
>>> val list = listOf(1,2,3,4,7,5,6,7,8)
>>> list.last()
8
>>> val emptyList = listOf<Int>()
>>> emptyList.last()
java.util.NoSuchElementException: List is empty.
    at kotlin.collections.CollectionsKt___CollectionsKt.last(_Collections.kt:340)



```

#### `last(predicate: (T) -> Boolean): T`

返回符合条件的最后一个元素，没有就抛NoSuchElementException

```
>>> val list = listOf(1,2,3,4,7,5,6,7,8)
>>> list.last({it==7})
7
>>> list.last({it>10})
java.util.NoSuchElementException: List contains no element matching the predicate.

```

对应的针对越界处理的`lastOrNull `函数：返回符合条件的最后一个元素，没有则返回null :

```
>>> list.lastOrNull({it>10})
null
```

#### `lastIndexOf(element: T): Int`

返回符合条件的最后一个元素，没有就返回-1

```
>>> val list = listOf("abc","dfg","jkl","abc","bbc","wer")
>>> list.lastIndexOf("abc")
3
```

#### `single(): T`

该集合如果只有1个元素，则返回该元素。否则，抛异常。

```
>>> val list = listOf(1)
>>> list.single()
1

>>> val list = listOf(1,2)
>>> list.single()
java.lang.IllegalArgumentException: List has more than one element.
    at kotlin.collections.CollectionsKt___CollectionsKt.single(_Collections.kt:471)

>>> val list = listOf<Int>()

>>> list.single()
java.util.NoSuchElementException: List is empty.
    at kotlin.collections.CollectionsKt___CollectionsKt.single(_Collections.kt:469)



```

#### `single(predicate: (T) -> Boolean): T`

返回符合条件的单个元素，如有没有符合的抛异常NoSuchElementException，或超过一个的抛异常IllegalArgumentException。

```
>>> val list = listOf(1,2,3,4,7,5,6,7,8)
>>> list.single({it==1})
1
>>> list.single({it==7})
java.lang.IllegalArgumentException: Collection contains more than one matching element.

>>> list.single({it==10})
java.util.NoSuchElementException: Collection contains no element matching the predicate.

```

对应的针对异常处理的函数`singleOrNull `: 返回符合条件的单个元素，如有没有符合或超过一个，返回null

```
>>> list.singleOrNull({it==7})
null
>>> list.singleOrNull({it==10})
null
```