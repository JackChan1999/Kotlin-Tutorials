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

## 协程

协程提供了一种避免阻塞线程并用更廉价、更可控的操作替代线程阻塞的方法：协程挂起。

Coroutine是编译器级的，Process和Thread是操作系统级的。

@RestrictsSuspension 注解

buildSequence()

kotlinx.coroutines 框架

### 挂起函数

挂起 suspend，非阻塞

挂起函数suspend，挂起函数不能在普通函数中调用，必须在协程上下文中调用

### CoroutineContext

协程上下文，就是代码块执行在哪个场景。

- AbstractCoroutineContextElement

### ContinuationInterceptor

- interceptContinuation()

### Continuation

挂起的协程可以作为保持其挂起状态与局部变量的对象来存储和传递。这种对象的类型是 `Continuation`

| 方法声明                  | 功能描述                |
| --------------------- | ------------------- |
| context               | CoroutineContext上下文 |
| resume()              |                     |
| resumeWithException() |                     |

协程的基本操作

| 方法声明                         | 功能描述 |
| ---------------------------- | ---- |
| createCoroutine()            | 创建协程 |
| startCoroutine(Continuation) | 开始协程 |
| suspendCoroutine()           | 挂起协程 |

### 生成器 

| 方法声明            | 功能描述 |
| --------------- | ---- |
| buildSequence() |      |
| buildIterator() |      |
|                 |      |

### 状态机

对具有逻辑顺序或时序规律事件的一种描述方法。

- 有限状态机
- 两种状态机：Moore型和Mealy型

状态机的主要用途

- ​

## 混合开发

Anko 代码生成ui布局