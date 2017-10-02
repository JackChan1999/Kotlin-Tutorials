## 为什么需要kotlin

- 类型安全
- Lambda表达式
- 扩展方法
- 类型推导
- 胜任Java所做的任何事情，还要更简单
- 没有分号

## Java与Kotlin的区别

- Kotlin语句结束处没有分号
- Kotlin创建对象不用new关键字
- Kotlin定义方法使用fun关键字，参数名在前，参数类型在后，中间用分号:隔开
- 定义常量用val
- 定义变量用var，可以类型推导
- 继承使用分号:，不是extends关键字
- data class

## 数据类型

- Boolean
- Number
  - Double
  - Float
  - Long
  - Int
  - Short
  - Byte（-128~127）
  - 拆箱装箱
- Char 字符
  - 转义字符
- 基本类型的转换
  - 不可隐式转换
- String
  - charArrayOf()
  - == 与equals()等价
  - === 比较是否是同一个对象
  - 字符串模板$
  - ""原始字符串""
- 类型别名 typealias
- 类型推导

```kotlin
toLong()
arrayOf() // 创建数组
arrayOfNulls() // 创建空数组
listOf()
setOf()
charArrayOf();
joinToString();
slice(); // 切片
until
readLine()
withIndex()
```

智能类型转换

- is
- as 强转，转换失败抛异常
- as? 安全的类型转换，转换失败返回null
- 智能类型转换，如果已经判断出来是某种类型，则不需要转换

```kotlin
fun main(args: Array<String>) {
    val parent: Parent = Parent()
    val child: Child? = parent as? Child
    println(child)

    val string: String? = "Hello"
    if(string != null)
        println(string.length)
}
```

## Array

- IntArray
- CharArray
- XxxArray
- xxArrayOf()
- Array&lt;String>

## 程序结构

- java 中的final 编译期常量，在编译期会把引用final的地方全部替换
- val 虽然也是常量，但是不是编译期常量，const val 编译期常量 等价于 final
- Unit ==> void

## 泛型

- 协变 ？extends Object ==> out Object  向下通配符
- 逆变 ？super String ==> in String 向上通配符
- let
- with
- apply

## List

创建

| 方法声明            | 功能描述                    |
| --------------- | ----------------------- |
| listOf()        | 创建不可变List               |
| arrayListOf()   | 创建一个Java中的ArrayList对象实例 |
| mutableListOf() | 创建一个MutableList的对象实例    |
| toMutableList() | 把不可变集合转为可变集合            |
| toList()        | 把可变集合转为不可变集合            |
| iterator()      | 获取迭代器                   |
| forEach()       | 遍历集合                    |
|                 |                         |
|                 |                         |
|                 |                         |

| 方法声明          | 功能描述                       |
| ------------- | -------------------------- |
| first()       |                            |
| firstOrNull() |                            |
| indexOf       |                            |
| indexOfFirst  | 返回第一个符合条件的元素下标             |
| indexOfLast   | 返回最后一个符合条件的元素下标            |
| last()        |                            |
| lastIndexOf   | 返回符合条件的最后一个元素              |
| single()      | 该集合如果只有1个元素，则返回该元素。否则，抛异常。 |
|               |                            |

基本运算函数

| 方法声明              | 功能描述                      |
| ----------------- | ------------------------- |
| any()             | 判断集合至少有一个元素               |
| all()             | 判断集合中的元素是否都满足条件           |
| none()            | 判断集合无元素                   |
| count()           | 计算集合中元素的个数                |
| reduce()          | 从第一项到最后一项进行累计运算           |
| reduceRight       | 从最后一项到第一项进行累计运算           |
| fold()            | 带初始值的reduce               |
| foldRight()       | 和 reduceRight 类似，有初始值。    |
| forEach()         | 循环遍历元素，元素是it              |
| forEachIndexed()  | 带index(下标) 的元素遍历          |
| max() , min()     | 查询最大、最小的元素                |
| maxBy() , minBy() | 获取函数映射结果的最大值、最小值对应的那个元素的值 |
| sumBy()           | 获取函数映射值的总和                |
|                   |                           |

过滤操作函数

| 方法声明            | 功能描述                      |
| --------------- | ------------------------- |
| filter()        | 过滤出满足条件的元素组成的子集合          |
| filterTo()      | 过滤出满足条件的元素并赋值给destination |
| take()          | 挑出该集合前n个元素的子集合            |
| takeWhile()     | 挑出满足条件的元素的子集合             |
| takeLast()      | 挑出后n个元素的子集合               |
| takeLastWhile() | 从最后开始挑出满足条件元素的子集合         |
| drop()          | 去除前n个元素返回剩下的元素的子集合        |
| dropWhile()     | 去除满足条件的元素返回剩下的元素的子集合      |
| dropLast()      | 从最后去除n个元素                 |
| dropLastWh()    | 从最后满足条件的元素                |
| slice()         | 取开始下标至结束下标元素子集合           |
| filterNot()     | 用来过滤所有不满足条件的元素            |
| filterNotNull() | 过滤掉null元素                 |
|                 |                           |
|                 |                           |



## 混合开发

Anko 代码生成ui布局