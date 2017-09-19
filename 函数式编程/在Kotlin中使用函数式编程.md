## 在Kotlin中使用函数式编程

好了亲，前文中我们在函数式编程的世界里遨游了一番，现在我们把思绪收回来，放到在Kotlin中的函数式编程中来。

严格的面向对象的观点，使得很多问题的解决方案变得较为笨拙。为了将一行有用的代码包装到Runnable或者Callable 这两个Java中最流行的函数式示例中，我们不得不去写五六行模板范例代码。为了让事情简单化（在Java 8中，增加Lambda表达式的支持），我们在Kotlin中使用普通的函数来替代函数式接口。事实上，函数式编程中的函数，比C语言中的函数或者Java中的方法都要强大的多。

在Kotlin中，支持函数作为一等公民。它支持高阶函数、Lambda表达式等。我们不仅可以把函数当做普通变量一样传递、返回，还可以把它分配给变量、放进数据结构或者进行一般性的操作。它们可以是未经命名的，也就是匿名函数。我们也可以直接把一段代码丢到 `{}`中，这就是闭包。

在前面的章节中，其实我们已经涉及到一些关于函数的地方，我们将在这里系统地学习一下Kotlin的函数式编程。

### Kotlin中的函数

首先，我们来看下Kotlin中函数的概念。

#### 函数声明

Kotlin 中的函数使用 fun 关键字声明

```kotlin
fun double(x: Int): Int {
    return 2*x
}
```

#### 函数用法

调用函数使用传统的方法

```kotlin
fun test() {
    val doubleTwo = double(2)
    println("double(2) = $doubleTwo")
}
```

输出：double(2) = 4

调用成员函数使用点表示法

```kotlin
object FPBasics {

    fun double(x: Int): Int {
        return 2 * x
    }

    fun test() {
        val doubleTwo = double(2)
        println("double(2) = $doubleTwo")
    }
}

fun main(args: Array<String>) {
    FPBasics.test()
}
```

我们这里直接用object对象FPBasics来演示。

### 扩展函数

通过 扩展 声明完成一个类的新功能 扩展 ，而无需继承该类或使用设计模式(例如，装饰者模式)。

一个扩展String类的swap函数的例子：

```kotlin
fun String.swap(index1: Int, index2: Int): String {
    val charArray = this.toCharArray()
    val tmp = charArray[index1]
    charArray[index1] = charArray[index2]
    charArray[index2] = tmp

    return charArrayToString(charArray)
}

fun charArrayToString(charArray: CharArray): String {
    var result = ""
    charArray.forEach { it -> result = result + it }
    return result
}
```

这个 this 关键字在扩展函数内部对应到接收者对象（传过来的在点符号前的对象）。 现在，我们对任意 String 调用该函数了：

```kotlin
val str = "abcd"
val swapStr = str.swap(0, str.lastIndex)
println("str.swap(0, str.lastIndex) = $swapStr")
```

输出： str.swap(0, str.lastIndex) = dbca

### 中缀函数

在以下场景中，函数还可以用中缀表示法调用：

- 成员函数或扩展函数
- 只有一个参数
- 用 `infix` 关键字标注

例如，给 Int 定义扩展

```kotlin
infix fun Int.shl(x: Int): Int {
 ...
}
```

用中缀表示法调用扩展函数：

```kotlin
1 shl 2
```

等同于这样

```kotlin
1.shl(2)
```

### 函数参数

函数参数使用 Pascal 表示法定义，即 name: type。参数用逗号隔开。每个参数必须显式指定其类型。

```kotlin
fun powerOf(number: Int, exponent: Int): Int {
    return Math.pow(number.toDouble(), exponent.toDouble()).toInt()
}
```

测试代码：

```kotlin
val eight = powerOf(2, 3)
println("powerOf(2,3) = $eight")
```

输出：powerOf(2,3) = 8

#### 默认参数

函数参数可以有默认值，当省略相应的参数时使用默认值。这可以减少重载数量。

```kotlin
fun add(x: Int = 0, y: Int = 0): Int {
    return x + y
}
```

默认值通过类型后面的 **=** 及给出的值来定义。

测试代码：

```kotlin
val zero = add()
val one = add(1)
val two = add(1, 1)
println("add() = $zero")
println("add(1) = $one")
println("add(1, 1) = $two")
```

输出：

```
add() = 0
add(1) = 1
add(1, 1) = 2
```

另外，覆盖带默认参数的函数时，总是使用与基类型方法相同的默认参数值。
当覆盖一个带有默认参数值的方法时，签名中不带默认参数值：

```kotlin
open class DefaultParamBase {
    open fun add(x: Int = 0, y: Int = 0): Int {
        return x + y
    }
}

class DefaultParam : DefaultParamBase() {
    override fun add(x: Int, y: Int): Int { // 不能有默认值
        return super.add(x, y)
    }
}
```

#### 命名参数

可以在调用函数时使用命名的函数参数。当一个函数有大量的参数或默认参数时这会非常方便。

给定以下函数

```kotlin
fun reformat(str: String,
	normalizeCase: Boolean = true,
	upperCaseFirstLetter: Boolean = true,
	divideByCamelHumps: Boolean = false,
	wordSeparator: Char = ' ') {
}
```

我们可以使用默认参数来调用它

```kotlin
reformat(str)
```

然而，当使用非默认参数调用它时，该调用看起来就像

```kotlin
reformat(str, true, true, false, '_')
```

使用命名参数我们可以使代码更具有可读性

```kotlin
reformat(str,
    normalizeCase = true,
    upperCaseFirstLetter = true,
    divideByCamelHumps = false,
    wordSeparator = '_'
)
```

并且如果我们不需要所有的参数

```kotlin
reformat(str, wordSeparator = '_')
```

#### 可变数量的参数（Varargs）

函数的参数（通常是最后一个）可以用 `vararg` 修饰符标记：

```kotlin
fun <T> asList(vararg ts: T): List<T> {
    val result = ArrayList<T>()
    for (t in ts) // ts is an Array
        result.add(t)
    return result
}
```

允许将可变数量的参数传递给函数：

```kotlin
val list = asList(1, 2, 3)
```

### 函数返回类型

#### 函数返回类型需要显式声明

具有块代码体的函数必须始终显式指定返回类型，除非他们旨在返回 `Unit`。

Kotlin 不推断具有块代码体的函数的返回类型，因为这样的函数在代码体中可能有复杂的控制流，并且返回类型对于读者（有时对于编译器）也是不明显的。

#### 返回 Unit 的函数

如果一个函数不返回任何有用的值，它的返回类型是 `Unit`。`Unit` 是一种只有一个`Unit` 值的类型。这个值不需要显式返回:

```kotlin
fun printHello(name: String?): Unit {
    if (name != null)
        println("Hello ${name}")
    else
        println("Hi there!")
    // `return Unit` 或者 `return` 是可选的
}
```

`Unit` 返回类型声明也是可选的。上面的代码等同于

```kotlin
fun printHello(name: String?) {
    .....
}
```

### 单表达式函数

当函数返回单个表达式时，可以省略花括号并且在 **=** 符号之后指定代码体即可

```kotlin
fun double(x: Int): Int = x * 2
```

当返回值类型可由编译器推断时，显式声明返回类型是可选的:

```
fun double(x: Int) = x * 2
```

### 函数作用域

在 Kotlin 中函数可以在文件顶层声明，这意味着你不需要像一些语言如 Java、C# 或 Scala 那样创建一个类来保存一个函数。此外除了顶层函数，Kotlin 中函数也可以声明在局部作用域、作为成员函数以及扩展函数。

#### 局部函数（嵌套函数）

Kotlin 支持局部函数，即一个函数在另一个函数内部

```kotlin
fun sum(x: Int, y: Int, z: Int): Int {
   val delta = 0;
   fun add(a: Int, b: Int): Int {
       return a + b + delta
   }
   return add(x + add(y, z))
}
```

局部函数可以访问外部函数（即闭包）中的局部变量delta。

```kotlin
println("sum(1,2,3) = ${sum(0, 1, 2, 3)}")
```

输出：sum(1,2,3) = 6

#### 成员函数

成员函数是在类或对象内部定义的函数

```kotlin
class Sample() {
    fun foo() { print("Foo") }
}
```

成员函数以点表示法调用

```kotlin
Sample().foo() // 创建类 Sample 实例并调用 foo
```

### 泛型函数

函数可以有泛型参数，通过在函数名前使用尖括号指定。

例如Iterable的map函数：

```kotlin
public inline fun <T, R> Iterable<T>.map(transform: (T) -> R): List<R> {
    return mapTo(ArrayList<R>(collectionSizeOrDefault(10)), transform)
}
```

### 高阶函数

高阶函数是将函数用作参数或返回值的函数。例如，Iterable的filter函数：

```kotlin
public inline fun <T> Iterable<T>.filter(predicate: (T) -> Boolean): List<T> {
    return filterTo(ArrayList<T>(), predicate)
}
```

它的输入参数`predicate: (T) -> Boolean`就是一个函数。其中，函数类型声明的语法是：

```kotlin
(X)->Y
```

表示这个函数是从类型X到类型Y的映射。即这个函数输入X类型，输出Y类型。

这个函数我们这样调用：

```kotlin
fun isOdd(x: Int): Boolean {
    return x % 2 == 1
}

val list = listOf(1, 2, 3, 4, 5)
list.filter(::isOdd)
```

其中，`::`用来引用一个函数。

### 匿名函数

我们也可以使用匿名函数来实现这个predicate函数：

```kotlin
list.filter((fun(x: Int): Boolean {
    return x % 2 == 1
}))
```

### Lambda 表达式

我们也可以直接使用更简单的Lambda表达式来实现一个predicate函数：

```kotlin
list.filter {
    it % 2 == 1
}
```

- lambda 表达式总是被大括号 `{}` 括着
- 其参数（如果有的话）在 `->` 之前声明（参数类型可以省略）
- 函数体（如果存在的话）在 `->` 后面

上面的写法跟：

```kotlin
list.filter({
    it % 2 == 1
})
```

等价，如果 lambda 是该调用的唯一参数，则调用中的圆括号可以省略。

使用Lambda表达式定义一个函数字面值：

```kotlin
>>> val sum = { x: Int, y: Int -> x + y }
>>> sum(1,1)
2
```

我们在使用嵌套的Lambda表达式来定义一个柯里化的sum函数：

```kotlin
>>> val sum = {x:Int ->  {y:Int -> x+y }}
>>> sum
(kotlin.Int) -> (kotlin.Int) -> kotlin.Int
>>> sum(1)(1)
2
```

### `it`：单个参数的隐式名称

Kotlin中另一个有用的约定是，如果函数字面值只有一个参数，那么它的声明可以省略（连同 `->`），其名称是 `it`。

代码示例：

```kotlin
>>> val list = listOf(1,2,3,4,5)
>>> list.map { it * 2 }
[2, 4, 6, 8, 10]
```

### 闭包（Closure）

Lambda 表达式或者匿名函数，以及局部函数和对象表达式（object declarations）可以访问其 闭包 ，即在外部作用域中声明的变量。 与 Java 不同的是可以修改闭包中捕获的变量：

```kotlin
fun sumGTZero(c: Iterable<Int>): Int {
    var sum = 0
    c.filter { it > 0 }.forEach {
        sum += it
    }
    return sum
}

val list = listOf(1, 2, 3, 4, 5)
sumGTZero(list) // 输出 15
```

我们再使用闭包来写一个使用Java中的Thread接口的例子：

```kotlin
fun closureDemo() {
    Thread({
        for (i in 1..10) {
            println("I = $i")
            Thread.sleep(1000)
        }

    }).start()

    Thread({
        for (j in 10..20) {
            println("J = $j")
            Thread.sleep(2000)
        }
        Thread.sleep(1000)
    }).start()
}
```

一个输出：

```
I = 1
J = 10
I = 2
I = 3
...
J = 20
```

### 带接收者的函数字面值

Kotlin 提供了使用指定的 接收者对象 调用函数字面值的功能。

使用匿名函数的语法，我们可以直接指定函数字面值的接收者类型。

下面我们使用带接收者的函数类型声明一个变量，并在之后使用它。代码示例：

```kotlin
>>> val sum = fun Int.(other: Int): Int = this + other
>>> 1.sum(1)
2
```

当接收者类型可以从上下文推断时，lambda 表达式可以用作带接收者的函数字面值。

```kotlin
class HTML {
    fun body() {
        println("HTML BODY")
    }
}

fun html(init: HTML.() -> Unit): HTML { // HTML.()中的HTML是接受者类型
    val html = HTML()  // 创建接收者对象
    html.init()        // 将该接收者对象传给该 lambda
    return html
}
```

测试代码：

```kotlin
html {
    body()
}
```

输出：HTML BODY

使用这个特性，我们可以构建一个HTML的DSL语言。

### 具体化的类型参数

有时候我们需要访问一个参数类型：

```kotlin
fun <T> TreeNode.findParentOfType(clazz: Class<T>): T? {
    var p = parent
    while (p != null && !clazz.isInstance(p)) {
        p = p.parent
    }
    @Suppress("UNCHECKED_CAST")
    return p as T?
}
```

在这里我们向上遍历一棵树并且检查每个节点是不是特定的类型。
这都没有问题，但是调用处不是很优雅：

```kotlin
treeNode.findParentOfType(MyTreeNode::class.java)
```

我们真正想要的只是传一个类型给该函数，即像这样调用它：

```kotlin
treeNode.findParentOfType<MyTreeNode>()
```

为能够这么做，内联函数支持*具体化的类型参数*，于是我们可以这样写：

```kotlin
inline fun <reified T> TreeNode.findParentOfType(): T? {
    var p = parent
    while (p != null && p !is T) {
        p = p.parent
    }
    return p as T?
}
```

我们使用 `reified` 修饰符来限定类型参数，现在可以在函数内部访问它了，
几乎就像是一个普通的类一样。由于函数是内联的，不需要反射，正常的操作符如 `!is` 和 `as` 现在都能用了。

虽然在许多情况下可能不需要反射，但我们仍然可以对一个具体化的类型参数使用它：

```kotlin
inline fun <reified T> membersOf() = T::class.members

fun main(s: Array<String>) {
    println(membersOf<StringBuilder>().joinToString("\n"))
}
```

普通的函数（未标记为内联函数的）没有具体化参数。

### 尾递归tailrec

Kotlin 支持一种称为尾递归的函数式编程风格。 这允许一些通常用循环写的算法改用递归函数来写，而无堆栈溢出的风险。 当一个函数用 tailrec 修饰符标记并满足所需的形式时，编译器会优化该递归，生成一个快速而高效的基于循环的版本。

```kotlin
tailrec fun findFixPoint(x: Double = 1.0): Double
        = if (x == Math.cos(x)) x else findFixPoint(Math.cos(x)) // 函数必须将其自身调用作为它执行的最后一个操作

```

这段代码计算余弦的不动点（fixpoint of cosine），这是一个数学常数。 它只是重复地从 1.0 开始调用 Math.cos，直到结果不再改变，产生0.7390851332151607的结果。最终代码相当于这种更传统风格的代码：

```kotlin
private fun findFixPoint(): Double {
    var x = 1.0
    while (true) {
        val y = Math.cos(x)
        if (x == y) return y
        x = y
    }
}
```

要符合 tailrec 修饰符的条件的话，函数必须将其自身调用作为它执行的最后一个操作。在递归调用后有更多代码时，不能使用尾递归，并且不能用在 try/catch/finally 块中。尾部递归在 JVM 后端中支持。

Kotlin 还为集合类引入了许多扩展函数。例如，使用 map() 和 filter() 函数可以流畅地操纵数据，具体的函数的使用以及示例我们已经在 集合类 章节中介绍。

## 本章小结

本章我们一起学习了函数式编程的简史、Lambda演算、Y组合子与递归等核心函数式的编程思想等相关内容。然后重点介绍了在Kotlin中如何使用函数式风格编程，其中重点介绍了Kotlin中函数的相关知识，以及高阶函数、Lambda表达式、闭包等核心语法，并给出相应的实例说明。

我们将在下一章 中介绍Kotlin的 轻量级线程：协程（Coroutines）的相关知识，我们将看到在Kotlin中，程序的逻辑可以在协程中顺序地表达，而底层库会为我们解决其异步性。

本章示例代码工程：https://github.com/EasyKotlin/chapter8_fp