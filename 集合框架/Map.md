## Map

### Map概述

Map是一种把键对象Key和值对象Value映射的集合，它的每一个元素都包含一对键对象和值对象（K-V Pair）。 Key可以看成是Value 的索引，作为key的对象在集合中不可重复（uniq）。

如果我们从数据结构的本质上来看，其实List就是Key是Int类型下标的特殊的Map。而Set也是Key为Int，但是Value值不能重复的特殊Map。

Kotlin中的Map与List、Set一样，Map也分为只读Map和可变的MutableMap。

Map没有继承于Collection接口。其类图结构如下：

![map](images/map.png)

在接口`interface Map<K, out V>`中，K是键值的类型，V是对应的映射值的类型。这里的`out V`表示类型为V或V的子类。这是泛型的相关知识，我们将在下一章节中介绍。

其中，`Entry<out K, out V>`中保存的是Map的键值对。

### 创建Map

跟Java相比不同的是，在Kotlin中的Map区分了只读的Map和可编辑的Map（MutableMap、HashMap、LinkedHashMap）。

Kotlin没有自己重新去实现一套集合类，而是在Java的集合类基础上做了一些扩展。

我们知道在Java中，根据内部数据结构的不同，Map 接口通常有多种实现类。

其中常用的有：

- HashMap

HashMap是基于哈希表（hash table）的 Map 接口的实现，以key-value的形式存在。在HashMap中，key-value是一个整体，系统会根据hash算法来来计算key-value的存储位置，我们可以通过key快速地存取value。它允许使用 null 值和 null 键。

另外，HashMap中元素的顺序，随着时间的推移会发生变化。

- TreeMap

使用红黑二叉树（red-black tree）的 Map 接口的实现。

- LinkedHashMap

还有继承了HashMap，并使用链表实现的LinkedHashMap。LinkedHashMap保存了记录的插入顺序，在用Iterator遍历LinkedHashMap时，先得到的记录是先插入的记录。简单说，LinkedHashMap是有序的，它使用链表维护内部次序。

我们在使用Kotlin创建Map的时候，实际上大部分都是调用Java的Map的方法。

下面我们就来介绍Map的创建以及基本操作函数。

#### `mapOf()`

创建一个只读空Map。

```kotlin
>>> val map1 = mapOf<String, Int>()
>>> map1.size
0
>>> map1.isEmpty()
true
```

我们还可以用另外一个函数创建空Map：

```kotlin
>>> val map2 = emptyMap<String, Int>()
>>> map2.size
0
>>> map2.isEmpty()
true
```

空Map都是相等的：

```kotlin
>>> map2==map1
true
```

这个空Map是只读的，其属性和函数返回都是预定义好的。其代码如下：

```kotlin
private object EmptyMap : Map<Any?, Nothing>, Serializable {
    private const val serialVersionUID: Long = 8246714829545688274

    override fun equals(other: Any?): Boolean = other is Map<*,*> && other.isEmpty()
    override fun hashCode(): Int = 0
    override fun toString(): String = "{}"

    override val size: Int get() = 0
    override fun isEmpty(): Boolean = true

    override fun containsKey(key: Any?): Boolean = false
    override fun containsValue(value: Nothing): Boolean = false
    override fun get(key: Any?): Nothing? = null
    override val entries: Set<Map.Entry<Any?, Nothing>> get() = EmptySet
    override val keys: Set<Any?> get() = EmptySet
    override val values: Collection<Nothing> get() = EmptyList

    private fun readResolve(): Any = EmptyMap
}
```

#### `mapOf(pair: Pair<K, V>): Map<K, V>`

使用二元组Pair创建一个只读Map。

```kotlin
>>> val map = mapOf(1 to "x", 2 to "y", 3 to "z")
>>> map
{1=x, 2=y, 3=z}
>>> map.get(1)
x
>>> map.get(3)
z
>>> map.size
3
>>> map.entries
[1=x, 2=y, 3=z]
```

这个创建函数内部是调用的LinkedHashMap构造函数，其相关代码如下：

```kotlin
pairs.toMap(LinkedHashMap(mapCapacity(pairs.size)))
```

如果我们想编辑这个Map， 编译器会直接报错

```kotlin
>>> map[1]="a"
error: unresolved reference. None of the following candidates is applicable because of receiver type mismatch: 
@InlineOnly public operator inline fun <K, V> MutableMap<Int, String>.set(key: Int, value: String): Unit defined in kotlin.collections
@InlineOnly public operator inline fun kotlin.text.StringBuilder /* = java.lang.StringBuilder */.set(index: Int, value: Char): Unit defined in kotlin.text
map[1]="a"
^
error: no set method providing array access
map[1]="a"
   ^
```

因为在不可变（Immutable）Map中，根本就没有提供set函数。

#### `mutableMapOf()`

创建一个空的可变的Map。

```kotlin
>>> val map = mutableMapOf<Int, Any?>()
>>> map.isEmpty()
true
>>> map[1] = "x"
>>> map[2] = 1
>>> map
{1=x, 2=1}
```

该函数直接是调用的LinkedHashMap()构造函数。

#### `mutableMapOf(vararg pairs: Pair<K, V>): MutableMap<K, V>`

创建一个可编辑的MutableMap对象。

```kotlin
>>> val map = mutableMapOf(1 to "x", 2 to "y", 3 to "z")
>>> map
{1=x, 2=y, 3=z}
>>> map[1]="a"
>>> map
{1=a, 2=y, 3=z}
```

另外，如果Map中有重复的key键，后面的会直接覆盖掉前面的：

```kotlin
>>> val map = mutableMapOf(1 to "x", 2 to "y", 1 to "z")
>>> map
{1=z, 2=y}
```

后面的`1 to "z"`直接把前面的`1 to "x"`覆盖掉了。

#### `hashMapOf(): HashMap<K, V>`

创建HashMap对象。Kotlin直接使用的是Java的HashMap。

```kotlin
>>> val map: HashMap<Int, String> = hashMapOf(1 to "x", 2 to "y", 3 to "z")
>>> map
{1=x, 2=y, 3=z}
```

#### `linkedMapOf(): LinkedHashMap<K, V>`

创建空对象LinkedHashMap。直接使用的是Java中的LinkedHashMap。

```kotlin
>>> val map: LinkedHashMap<Int, String> = linkedMapOf()
>>> map
{}
>>> map[1]="x"
>>> map
{1=x}
```

#### `linkedMapOf(vararg pairs: Pair<K, V>): LinkedHashMap<K, V>`

创建带二元组Pair元素的LinkedHashMap对象。直接使用的是Java中的LinkedHashMap。

```kotlin
>>> val map: LinkedHashMap<Int, String> = linkedMapOf(1 to "x", 2 to "y", 3 to "z")
>>> map
{1=x, 2=y, 3=z}
>>> map[1]="a"
>>> map
{1=a, 2=y, 3=z}
```

#### `sortedMapOf(vararg pairs: Pair<K, V>): SortedMap<K, V>`

创建一个根据Key升序排序好的TreeMap。对应的是使用Java中的SortedMap。

```kotlin
>>> val map = sortedMapOf(Pair("c", 3), Pair("b", 2), Pair("d", 1))
>>> map
{b=2, c=3, d=1}
```

### 访问Map的元素

#### entries属性

我们可以直接访问entries属性

```kotlin
val entries: Set<Entry<K, V>>
```

获取该Map中的所有键/值对的Set。这个Entry类型定义如下：

```kotlin
public interface Entry<out K, out V> {
    public val key: K
    public val value: V
}
```

代码示例

```kotlin
>>> val map = mapOf("x" to 1, "y" to 2, "z" to 3)
>>> map
{x=1, y=2, z=3}
>>> map.entries
[x=1, y=2, z=3]
```

这样，我们就可以遍历这个Entry的Set了：

```kotlin
>>> map.entries.forEach({println("key="+ it.key + " value=" + it.value)})
key=x value=1
key=y value=2
key=z value=3
```

#### keys属性

访问keys属性：

```kotlin
val keys: Set<K>
```

获取Map中的所有键的Set。

```kotlin
>>> map.keys
[x, y, z]
```

#### values属性

访问` val values: Collection<V>`获取Map中的所有值的Collection。这个值的集合可能包含重复值。

```kotlin
>>> map.values
[1, 2, 3]
```

#### size属性

访问`val size: Int`获取map键/值对的数目。

```kotlin
>>> map.size
3
```

#### get(key: K)

我们使用get函数来通过key来获取value的值。

```kotlin
operator fun get(key: K): V?
```

对应的操作符是`[]`：

```kotlin
>>> map["x"]
1
>>> map.get("x")
1

```

如果这个key不在Map中，就返回null。

```kotlin
>>> map["k"]
null
```

如果不想返回null，可以使用getOrDefault函数

```kotlin
getOrDefault(key: K, defaultValue: @UnsafeVariance V): V
```

当为null时，不返回null，而是返回设置的一个默认值：

```kotlin
>>> map.getOrDefault("k",0)
0
```

这个默认值的类型，要和V对应。类型不匹配会报错：

```kotlin
>>> map.getOrDefault("k","a")
error: type mismatch: inferred type is String but Int was expected
map.getOrDefault("k","a")
                     ^
```

### Map操作符函数

#### `containsKey(key: K): Boolean`

是否包含该key。

```kotlin
>>> val map = mapOf("x" to 1, "y" to 2, "z" to 3)
>>> map.containsKey("x")
true
>>> map.containsKey("j")
false
```

#### `containsValue(value: V): Boolean`

是否包含该value。

```kotlin
>>> val map = mapOf("x" to 1, "y" to 2, "z" to 3)
>>> map.containsValue(2)
true
>>> map.containsValue(20)
false
```

#### `component1()` `component2()`

`Map.Entry<K, V>`的操作符函数，分别用来直接访问key和value。

```kotlin
>>> val map = mapOf("x" to 1, "y" to 2, "z" to 3)
>>> map.entries.forEach({println("key="+ it.component1() + " value=" + it.component2())})
key=x value=1
key=y value=2
key=z value=3
```

这两个函数的定义如下：

```kotlin
@kotlin.internal.InlineOnly
public inline operator fun <K, V> Map.Entry<K, V>.component1(): K = key

@kotlin.internal.InlineOnly
public inline operator fun <K, V> Map.Entry<K, V>.component2(): V = value
```

#### `Map.Entry<K, V>.toPair(): Pair<K, V>`

把Map的Entry转换为Pair。

```kotlin
>>> map.entries
[x=1, y=2, z=3]
>>> map.entries.forEach({println(it.toPair())})
(x, 1)
(y, 2)
(z, 3)
```

#### `getOrElse(key: K, defaultValue: () -> V): V`

通过key获取值，当没有值可以设置默认值。

```kotlin
>>> val map = mutableMapOf<String, Int?>()
>>> map.getOrElse("x", { 1 })
1
>>> map["x"] = 3
>>> map.getOrElse("x", { 1 })
3
```

#### `getValue(key: K): V`

当Map中不存在这个key，调用get函数，如果不想返回null，直接抛出异常，可调用此方法。

```kotlin
val map = mutableMapOf<String, Int?>()
>>> map.get("v")
null
>>> map.getValue("v")
java.util.NoSuchElementException: Key v is missing in the map.
    at kotlin.collections.MapsKt__MapWithDefaultKt.getOrImplicitDefaultNullable(MapWithDefault.kt:19)
    at kotlin.collections.MapsKt__MapsKt.getValue(Maps.kt:252)
```

#### `getOrPut(key: K, defaultValue: () -> V): V`

如果不存在这个key，就添加这个key到Map中，对应的value是defaultValue。

```kotlin
>>> val map = mutableMapOf<String, Int?>()
>>> map.getOrPut("x", { 2 })
2
>>> map
{x=2}
```

#### `iterator(): Iterator<Map.Entry<K, V>>`

这个函数返回的是 `entries.iterator()`。这样我们就可以像下面这样使用for循环来遍历Map：

```kotlin
>>> val map = mapOf("x" to 1, "y" to 2, "z" to 3 )
>>> for((k,v) in map){println("key=$k, value=$v")}
key=x, value=1
key=y, value=2
key=z, value=3
```

#### `mapKeys(transform: (Map.Entry<K, V>) -> R): Map<R, V>`

把Map的Key设置为通过转换函数transform映射之后的值。

```kotlin
>>> val map:Map<Int,String> = mapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> val mmap = map.mapKeys{it.key * 10}
>>> mmap
{10=a, 20=b, 30=c, -10=z}
```

注意，这里的it是Map的Entry。
如果不巧，有任意两个key通过映射之后相等了，那么后面的key将会覆盖掉前面的key。

```kotlin
>>> val mmap = map.mapKeys{it.key * it.key}
>>> mmap
{1=z, 4=b, 9=c}
```

我们可以看出，`1 to "a"`被`-1 to "z"`覆盖掉了。

#### `mapValues(transform: (Map.Entry<K, V>) -> R): Map<K, R>`

对应的这个函数是把Map的value设置为通过转换函数transform转换之后的新值。

```kotlin
>>> val map:Map<Int,String> = mapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> val mmap = map.mapValues({it.value + "$"})
>>> mmap
{1=a$, 2=b$, 3=c$, -1=z$}
```

#### `filterKeys(predicate: (K) -> Boolean): Map<K, V>`

返回过滤出满足key判断条件的元素组成的新Map。

```kotlin
>>> val map:Map<Int,String> = mapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> map.filterKeys({it>0})
{1=a, 2=b, 3=c}
```

注意，这里的it元素是Key。

#### `filterValues(predicate: (V) -> Boolean): Map<K, V>`

返回过滤出满足value判断条件的元素组成的新Map。

```kotlin
>>> val map:Map<Int,String> = mapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> map.filterValues({it>"b"})
{3=c, -1=z}
```

注意，这里的it元素是value。

#### `filter(predicate: (Map.Entry<K, V>) -> Boolean): Map<K, V>`

返回过滤出满足Entry判断条件的元素组成的新Map。

```kotlin
>>> val map:Map<Int,String> = mapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> map.filter({it.key>0 && it.value > "b"})
{3=c}
```

#### `Iterable<Pair<K, V>>.toMap(destination: M): M`

把持有Pair的Iterable集合转换为Map。

```kotlin
>>> val pairList = listOf(Pair(1,"a"),Pair(2,"b"),Pair(3,"c"))
>>> pairList
[(1, a), (2, b), (3, c)]
>>> pairList.toMap()
{1=a, 2=b, 3=c}
```

#### `Map<out K, V>.toMutableMap(): MutableMap<K, V>`

把一个只读的Map转换为可编辑的MutableMap。

```kotlin
>>> val map = mapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> map[1]="x"
error: unresolved reference. None of the following candidates is applicable ...
error: no set method providing array access
map[1]="x"
   ^

>>> val mutableMap = map.toMutableMap()
>>> mutableMap
{1=a, 2=b, 3=c, -1=z}
>>> mutableMap[1]="x"
>>> mutableMap
{1=x, 2=b, 3=c, -1=z}
```

#### `plus` `minus`

Map的加法运算符函数如下：

```kotlin
operator fun <K, V> Map<out K, V>.plus(pair: Pair<K, V>): Map<K, V>
operator fun <K, V> Map<out K, V>.plus(pairs: Iterable<Pair<K, V>>): Map<K, V>
operator fun <K, V> Map<out K, V>.plus(pairs: Array<out Pair<K, V>>): Map<K, V>
operator fun <K, V> Map<out K, V>.plus(pairs: Sequence<Pair<K, V>>): Map<K, V>
operator fun <K, V> Map<out K, V>.plus(map: Map<out K, V>): Map<K, V>
```

代码示例：

```kotlin
>>> val map = mapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> map+Pair(10,"g")
{1=a, 2=b, 3=c, -1=z, 10=g}
>>> map + listOf(Pair(9,"s"),Pair(10,"w"))
{1=a, 2=b, 3=c, -1=z, 9=s, 10=w}
>>> map + arrayOf(Pair(9,"s"),Pair(10,"w"))
{1=a, 2=b, 3=c, -1=z, 9=s, 10=w}
>>> map + sequenceOf(Pair(9,"s"),Pair(10,"w"))
{1=a, 2=b, 3=c, -1=z, 9=s, 10=w}
>>> map + mapOf(9 to "s", 10 to "w")
{1=a, 2=b, 3=c, -1=z, 9=s, 10=w}
```

加并赋值函数：

```kotlin
inline operator fun <K, V> MutableMap<in K, in V>.plusAssign(pair: Pair<K, V>)
inline operator fun <K, V> MutableMap<in K, in V>.plusAssign(pairs: Iterable<Pair<K, V>>)
inline operator fun <K, V> MutableMap<in K, in V>.plusAssign(pairs: Array<out Pair<K, V>>)
inline operator fun <K, V> MutableMap<in K, in V>.plusAssign(pairs: Sequence<Pair<K, V>>)
inline operator fun <K, V> MutableMap<in K, in V>.plusAssign(map: Map<K, V>)

```

代码示例：

```kotlin
>>> val map = mutableMapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> map+=Pair(10,"g")
>>> map
{1=a, 2=b, 3=c, -1=z, 10=g}
>>> map += listOf(Pair(9,"s"),Pair(11,"w"))
>>> map
{1=a, 2=b, 3=c, -1=z, 10=g, 9=s, 11=w}
>>> map += mapOf(20 to "qq", 30 to "tt")
>>> map
{1=a, 2=b, 3=c, -1=z, 10=g, 9=s, 11=w, 20=qq, 30=tt}
```

减法跟加法类似。

#### `put(key: K, value: V): V?`

根据key设置元素的value。如果该key存在就更新value；不存在就添加，但是put的返回值是null。

```kotlin
>>> val map = mutableMapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> map
{1=a, 2=b, 3=c, -1=z}
>>> map.put(10,"q")
null
>>> map
{1=a, 2=b, 3=c, -1=z, 10=q}
>>> map.put(1,"f")
a
>>> map
{1=f, 2=b, 3=c, -1=z, 10=q}
```

#### `putAll(from: Map<out K, V>): Unit`

把一个Map全部添加到一个MutableMap中。

```kotlin
>>> val map = mutableMapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> val map2 = mapOf(99 to "aa", 100 to "bb")
>>> map.putAll(map2)
>>> map
{1=a, 2=b, 3=c, -1=z, 99=aa, 100=bb}
```

如果有key重复的，后面的值会覆盖掉前面的值：

```kotlin
>>> map
{1=a, 2=b, 3=c, -1=z, 99=aa, 100=bb}
>>> map.putAll(mapOf(1 to "www",2 to "tttt"))
>>> map
{1=www, 2=tttt, 3=c, -1=z, 99=aa, 100=bb}
```

#### `MutableMap<out K, V>.remove(key: K): V?`

根据键值key来删除元素。

```kotlin
>>> val map = mutableMapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> map.remove(-1)
z
>>> map
{1=a, 2=b, 3=c}
>>> map.remove(100)
null
>>> map
{1=a, 2=b, 3=c}
```

#### `MutableMap<K, V>.clear(): Unit`

清空MutableMap。

```kotlin
>>> val map = mutableMapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> map
{1=a, 2=b, 3=c, -1=z}
>>> map.clear()
>>> map
{}
```

## 本章小结

本章我们介绍了Kotlin标准库中的集合类List、Set、Map，以及它们扩展的丰富的操作函数，这些函数使得我们使用这些集合类更加简单容易。

集合类持有的是对象，而怎样的放入正确的对象类型则是我们写代码过程中需要注意的。下一章节中我们将学习泛型。