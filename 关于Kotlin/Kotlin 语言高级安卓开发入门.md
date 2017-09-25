> 原文链接：https://academy.realm.io/cn/posts/oredev-jake-wharton-kotlin-advancing-android-dev/

过去一年，使用 Kotlin 来为安卓开发的人越来越多。即使那些现在还没有使用这个语言的开发者，也会对这个语言的精髓产生共鸣，它给现在 Java 开发增加了简单并且强大的范式。Jake Wharton 在他的 Øredev 的讨论中，提到了 Kotlin 是如何通过提升安卓开发的语言特性和设计模式来解决这些严重的问题，通过这些方法你可以清除那些无用的 API 还有无效代码。充分利用扩展特性来解决你的开发中的模板性代码的问题！

Kotlin 代码的格式会有些奇怪，我们非常抱歉。我们的高亮系统还不支持这种语言！我们保证 Jake 的代码事实上是漂亮而且实用的。

### 为什么要推广这个语言？

好吧，大伙。欢迎来到这里。我们今天的主题是使用 Kotlin 语言的高级安卓开发 – 推广我们开发应用的语言。首先呢，我们需要弄清楚我们为什么要在安卓开发中更进一步？为什么我们需要一些新的东西来推进安卓开发？

许多人都会提到的一个经典的原因就是我们现在被 Java 6-ish 困住了，Java 7 也差不多是同样的状况。这被称作是一片荒地。

javax.time 是 Java 8 才引进的新库， JSR310。这个库我们在安卓上没有，所以我们被老的日期和日历的 API 困住了，它们都非常容易出错。

Java 8 的另外一个功能 streams，安卓也没有。我们还缺少其他的语言特性，比如 lambdas，method references 和 不引用 outer class 的 anonymous classes。最后，我们没有 try-with-resources。Try-with-resources 不在 Java 7 里面，而安卓在 API 19 中大部分都是 Java 7， 在 API 20 中又增加了点，但不是全部。所以只有当你的定义最小的 SDK 的时候，你才能用这些相关的功能。

### Java 6 不是个问题

尽管上面4个问题都看起来十分令人头疼，但是事实上它们不是。关于 javax.time，为 Java 8 SDK 编写的大部分代码大部分都能够以 ThreeTenBP 的形式向后兼容 Java 6，所以你可以这样使用。

关于 streams，Java 6 上也有一个向后兼容的移植或者一个常用的 Java 库，[RxJava](https://github.com/ReactiveX/RxJava) 实现了同样的概念，只不过用了一种略微不同的有争议性的更强大的方式。

也有一个工具叫做 [Retrolambda](https://github.com/orfjackal/retrolambda)，它事实上也可以运行 Java 8 bytecode 并且向后兼容了 lambdas 和 method references 到 Java 6。

最后，如我所说，如果你的最小的 SDK 是 19 的话，你可以使用 try-with-resources，而且 Retrolambda 也会允许你这样做。

这些关于 Java 6 是个问题的争论都有工具去解决它们。当然，如果我们能使用 Java 8 是最好的，但是这些替代方案都是经过实战测试而且能够很好地工作的方案。所以上面的理由就不太算数了。无论如何，有两个方面我想重点讨论一下，因为这两个方面没有类似的解决方案。

### Java 语言的限制和问题 

Java 语言的一些限制和所带来的一些问题，这些问题 Java 语言本身是不可避免的。例如，我们不能给不是我们自己写的 types、classes 或者 interfaces 增加新的方法。长时间以来，我们都会采用 util 类，杂乱无章地堆砌着我们代码或者或者揉在同一个 util package 里面。如果这是解决方案的话，它肯定不理想。

Java 语言的类型系统都有 null 的问题，在 android 上更为明显。因为它没有对可能是或不是 null 的类型完成 first-class representation。所以，被称作 “billion dollar mistake” 的 null 指针异常最后会毁了你的应用。

接下来， Java 肯定不是最简洁的语言。这件事本身不是件坏事，但是事实上存在太多的常见的冗余。这会带来潜在的错误和缺陷。在这之前，我们还要处理安卓 API 带来的问题。

### Android API 的设计问题

安卓是一个庞大的继承系统；他们为他们的继承系统而感到自豪，对他们来说这个系统工作的也非常的正常，但是这个系统倾向于把问题推向应用开发者。而且，空引用的问题也回来了，这个问题在安卓系统中十分明显，因为他们想让系统更加有效些。null 被用在许多地方来代表值的缺失，而不是封装成更高级的类型，比如一个类型或者可选项。

Receive news and updates from Realm straight to your inbox

订阅Comments 

同时，回到语言的冗余，安卓的 API 有着自己许多的范式。这也许是因为性能的原因。设计者们最后写出的这些 APIs 需要你，开发者，来做许多的事情来提高效率而不是用其他的方法抽象它们。

所以说 Java 语言和安卓 API 是驱动我们转向类似 Kotlin 语言的两个重要的因素。这不是说 Java 6 没有问题，因为 Kotlin 解决了 Java 6 的许多问题，但是我不认为那是 Kotlin 作为一个替代方案的充分原因，因为其他的方案也可以带来同样的好处。

### Kotlin 入门

[Kotlin](https://kotlinlang.org/) 是公司 [JetBrains](https://www.jetbrains.com/) 研发的语言。市面上为各种语言开发的 IDE 很多，但是 Intelli J 平台是 Android Studio 的基础。在他们的网站上，他们这样描述 Kotlin ：

> 为 JVM、Android 和浏览器而生的静态编程语言。

它的目标是 JVM、安卓和 Java 6 字节流。他们想在他们的语言里增加这些特性，而且持续支持 Java 6、JVM 和安卓系统市场。他们特别关注和 Java 的相互调用，这点接下来会讨论。

### Kotlin 语法速成

网站上有许多的很棒的指导来学习语法，但是我还是会很快地介绍一下，然后再解释为什么这些语法有利于安卓开发。我们以一个这样的方法定义的语法开始。

```kotlin
fun sum(a: Int, b: Int): Int {
  return a + b
}
```
我们有一个 “fun” 的定义，这代表着函数。函数名和第一个要注意的事情是这和 Java 有明显的不同。参数名的顺序和参数的类型保留了下来 – 名字后面跟着类型。返回值类型在函数末尾声明。没有分号。

另外一个有意思的事情是这个函数还可以有单行描述，我们事实上可以不用大括号和 return，定义函数和表达式一样。

```kotlin
fun sum(a: Int, b: Int) = a + b
```
我们接下来还会看到更简洁的语法。这里有另外一个例子，这个看起来像一个 main 函数，如果你写一个普通的 Java 应用的话：

```kotlin
fun main(args: Array<String>) {
  println("Args: $args")
}
```
数组的语法不太一样。但是处理得十分自然。 编译后的字节码会使用一个字符串的数组，但是在你的代码里却把它处理地像一个普通的数组。它也支持字符串的插入；我们可以写一个串，然后引用它其中的变量，并且可以自动的替换其中的变量。

最后，看看变量：

```kotlin
val name = "Jake"
val people: List<String> = ArrayList()
```
这里我用一个叫做 “name” 的变量给一个字符串命名，而且这里没有类型定义。语言会自动解释类型，因为它只可能是串。它有 “val” 的前缀而 “Val” 是它的值，并且是个不可以修改的值。如果我们想修改它，我们就需要用 “var” 作为前缀定义变量。

这个 `: List<String>` 是一个看起来像在 field 上的类型，它接在名字后面，像一个方法。最后，当我们调用构造函数的时候，我们不需要使用 “new” 关键字。其他的语法都是一样的，就是不要 “new”。

### Kotlin 语言特性

让我们看看那些语言本身的特性，看看他们是如何帮助我们构建安卓应用的。我指出过这些 util 类都是反设计模式的，而且它们会在你的应用里越来越不受控制……

#### 函数扩展

Kotlin 有扩展函数的概念。这不是 Kotlin 语言独有的，但是和其他语言里面我们看到的扩展又不太一样。如果我们在纯 Java 语言的环境下添加一个 date 的方法，我们需要写一个 utils 类或者 dates 类，然后增加一个静态方法。它接收一个实例，然后做些事情，可能会返回一个值。

```kotlin
static boolean isTuesday(Date date) {
  return date.getDay() == 2;
}

boolean tuesday = DateUtils.isTuesday(date);
```
这里我增加一个十分有用的 “isTuesday” 函数给我们的 date utils，然后我们用传统的静态方法调用它。在我展现 Kotlin 语法前，我想和 C# 做个比较。这是在 C# 中我们需要在 date 类中添加一个函数的实现， `DateTime`：

```kotlin
static boolean IsTuesday(this DateTime date)
{
  return date.DayOfWeek == DayOfWeek.Tuesday;
}
```
这里我们得到 date 的实例，然后调用这个方法，在任何 .NET 环境下这都能行得通，只要你在某处定义了该扩展方法，你能在你的整个项目中都能引用到 DateTime。我接下来会解释一个有意思的语言特征。这是 Kotlin 定义的方法：

```kotlin
fun Date.isTuesday(): Boolean {
  return getDay() == 2
}

val tuesday = date.isTuesday();
```
在 Kotlin 中，我们用我们想增加的函数的方法的类型来给原有的函数名增加了一个前缀。我们现在调用 `Date.IsTuesday`而不是 `isTuesday`。然后你能在最后得到返回值。我们最后能调用 “getDay” 并且这个扩展的方法能够被调到，尽管我们没有使用实例来调用它。我们的调用方式和该类原来就有有这个方法时调用的方式一样。我们也能够在 Date 上调用其他的方法。

Kotlin 的一个非常好的功能是，它会自动地转换有 getters 和 setters 综合属性的类型。所以我能够替换 `getDay()` 为 `day`，因为这个 day 的属性是存在的。它看起来像一个 field，但是实际上是个 property – getter 和 setter 的概念融合在了一起。

前面我指出的单行函数表达式会使得语法变得更简洁，所以我们可以把上面的代码修改成下面的样子，并且隐藏返回值：

```kotlin
fun Date.isTuesday() = day == 2
```
现在我们有一个非常漂亮的单行实现的并且在 date 上使用的扩展方法了。

不像 C#，如果我们不在同一个 package 里面的话，扩展函数需要显示引用。如果不是同一个文件里，我们需要非常清楚地描述这个函数从何而来： `import com.example.utils.isTuesday`

这和 Java 的静态方法 import 非常类似。我们需要显示声明我们调用的函数，这样函数的来源就不会模糊。因为当我看到这段代码时，作为一个 Java 开发者，我知道 date 没有 “isTuesday” 的函数，但是显式的 import 告诉我它来自于公共的某个 util package。而在 C# 中，我们不知道这个扩展函数从何而来。它可能来自一个库，你的源代码，或者其他的地方，你无法静态地知道，除非你到 IDE 里面找到它的定义。

当然，你可以 command B 然后进入这个函数，这看起来像是 date 类型的一个方法。这和我们在 Java 里面编写它，然后产生的二进制代码一模一样。而且，因为它关注 inter-op，你可以从 Java 侧使用一个自动生成类来调用它。

#### 类型系统中的 Null

我提过 null 会是个问题。Kotlin 事实上在它的类型系统里重新表述了 null。对于 get string 可能返回 null 的函数来说，我会返回 `String?` 来表述这可能是个 null 值。然后在 get string 函数中，我使用 double exclamation mark 语法来直接调用这个 null。这基本上是说，“我知道这可能是个 null，所以把它变成一个普通字符串。” 当它真是 null 的时候，它会发出一个检查的信号，然后抛出异常。

但是在消费者的代码里面，代码往往是直接调用该函数，类型系统会生成一个带有问号的字符串，并且传递到调用者的代码处。这意味着如果你不首先做 null 检查或者提前的默认处理机制，你就永远不能解引用。这样，它会最终解决消费者代码中导致 null 指针异常的问题。

#### 函数表达式入门 

另外，函数表达式也被称作 lambdas 或者 closures。这里有一个最简单的函数表达式： `{ it.toString() }`。它是一段代码在 “it” 变量上调用了 two-string 函数。“it” 是个 built-in 的名字。当你在写这些函数表达式的时候，如果你只有一个参数传入这段代码，你可以用 “it” 引用，这只是一个你不需要定义参数的方法。

但是当你需要定义参数的时候，或者不止一个参数要定义的时候，语法就是这样的： `{ x, y -> x + y }`。我们可以创建一段代码，一个函数表达式，输入两个参数，然后把它们相加。如果我们愿意，我们可以显示定义类型。

```kotlin
{ x, y -> x + y }

val sum: (Int, Int) -> Int = { x, y -> x + y }

val sum = { x: Int, y: Int -> x + y }
```
然后，在 fields 上存储它们。现在你可以看到在前二个例子中，类型是在 field 本上上定义的。这意味着函数表达式不需要任何类型信息。然后往下一个，类型信息在函数表达式里面自己包含了，所以我们不需要在变量定义时加入类型信息。

在最后一个例子中，返回值是推断出来的。两个整型相加，输出只能是整型。所以你不需要显示定义它们。

#### Higher-order 函数

这是个新奇的���语，它指的是函数可以接收函数，或者函数可以返回函数。这里是个例子：

```kotlin
func apply(one: Int, two: Int, func: (Int, Int) -> Int): Int {
  return func(one, two)
}

val sum = apply(1, 2, { x, y -> x + y })
val difference = apply(1, 2, { x, y -> x - y })
```
这里我们定义一个函数，接收两个整型作为参数。然后第三个参数是一个函数。这和我们之前看到的语法一样，那里我们定义了一个函数接收两个整型并返回一个整型。

然后，在函数体内部，我们调用了该函数，并且传入了两个参数。这就是我们如何使用它来计算和或者其他的东西的方法。我们把这段代码应用到了这两个数字上。回到之前所说的地方，我们说这段代码知道如何相加或者相减，而且我们把它运用到我们传入这个方法的数据上。然后这段代码在自己合适的上下文环境中自动运行。

Kotlin 给你提供了一个手动的方式来调整这段代码成为更好的语法表达。如果函数的最后一个参数是个表达式，你一直都不需要使用括号，你可以只用在所有参数初始化后添加一个。

```kotlin
val sum = apply(1, 2) { x, y -> x + y }
val difference = apply(1, 2) { x, y -> x - y }
```
这项技术允许你创建非常漂亮的 DSL 和 API。你可以像 Int 的扩展一样来编写它们。

#### 包含 higher-order 函数的应用

```kotlin
fun <T> List<T>.filter(predicate: (T) -> Boolean): List<T> {
  val newList = ArrayList<T>()
  for (item in this) {
    if (predicate(item)) {
      newList.add(item)
    }
  }
  return newList
}

val names = listOf("Jake", "Jesse", "Matt", "Alec")
val jakes = names.filter { it == "Jake" }
```
一个常见的操作是你有一个事情的列表，然后你需要基于一些条件来过滤它们。因为我们有 extension methods 和 higher-order functions，我们可以在列表上写一个这样的函数。这和你期望的实现一样，这个函数比较每个列表中的元素然后返回正确的结果。

现在如果你有一个包含数据的列表，只需用一行代码，如此简洁的函数就能过滤出我们想要的数据。谢天谢地，这个功能实际上已经在 Kotlin 标准库里面包含了，这样我们就不用扩展列表类了。

大部分 Kotlin 的库都已经实现了现有的 Java 类型中类似的高级函数来，这样可以统一在这些类型上的操作。

看看这个在 Kotlin 标准库里面不存在的应用：

```kotlin
fun Any.lock(func: () -> Unit) {
  synchronized (this) {
    func()
  }
}
```
除了同步阻塞代码块，我们可以用一个表达式来实现同步阻塞。 “Any” 是 Kotlin 的对象版本。所有的类型都是 “Any” 的子类型。我们可以对它增加一个方法，该方法输入是一个函数，然后执行这个函数的代码，这段代码会同步该实例。如果我们需要同样的锁，或者一些对象需要被锁住，我们可以把这段代码放在函数内部的锁起来。然后我们把它传给每个实例的这个方法中。这个方法简化了调用者的代码，而且清晰很多。

### 简单清晰的锁

另一个十分酷的应用存在于我们常用的锁的情景中，我们常常忘记在一些操作之前完成锁的操作。我们可以写这样的类，这个类只允许你访问你需要用锁才能操作的资源。

```kotlin
data class Lock<T>(private val obj: T) {
  public fun acquire(func: (T) -> Unit) {
    synchronized (obj) {
      func(obj)
    }
  }
}

val readerLock = Lock(JsonReader(stream))

// Later
readerLock.acquire {
  println(it.readString())
}
```
在这个例子里面，我们创建了依赖流的 JsonReader。设想不论什么原因我们需要多线程同时访问它，这样我们就必须使用锁，锁会帮助我们管理同步的问题。

然后在后面的代码中，我们调用了这个 acquire 的方法，它会在这个 JsonReader 的实例上实现同步，然后把它传给我们提供的函数。所以在这种情况下，我们会在这个 JsonReader 里面再次析构，于是我们需要使用锁。但现在我们的代码根本没有处理锁的问题。我们也没有显式的同步，也没有为 JsonReader 创建锁。可是访问 JsonReader 不使用锁是不可能的。

### 避免 Kotlin 带来的泄露

前面，我提到过关于 data 的代码，定义了一个这样的值：

```kotlin
val notEmpty: (String) -> Boolean { !it.isEmpty() }
```
Kotlin 实现函数表达式的方法和在 Java 里面使用类的方法是一样的。好处是 Kotlin 并没有创建对于外部范围的引用，因为没有这样的类。这样避免了可能的上下文泄漏。

我们需要关心的就是传入的数据。它会导致创建一个静态的单例实例而且没有引用。用这些函数表达式根本不可能产生上下文泄漏。但是在最上层，我们两个是一模一样的。

### 扩展函数表达式的例子 

- 扩展函数 – 给一个类型加入函数但是不修改原来的类型。
- 函数表达式 – 未定义的函数体被用作表达式（i.e.，date）
- Higher-Order 函数 – 一个参数是函数或者返回是函数的函数。

扩展函数表达式是上述三个概念的综合体，这是个强大的而且可以创建清晰的 API 的方法。为了说明这点，我将使用一个 databases 的 API 做为例子，把它改造成一个更加整洁的、没有无效代码的 API。

```kotlin
db.beginTransaction();
try {
  db.delete("users", "first_name = ?", new String[] { "Jake" });
  db.setTransactionSuccessful();
} finally {
  db.endTransaction();
}
```
如果你想在一个 transaction 里面执行一个 statement 的话，这是你必须写的六行代码。我们开始一个 transaction，我们把它放在 “try finally” 里面，然后我们标记这个 transaction 为成功。如果它抛出或者不在 “finally” 里面抛出异常，我们需要结束 transaction.

这是一个容易带来 bug 的代码，容易健忘的代码和应该重构的代码。任何事情都有可能出现错误，在这里你不小心交换了两个事情，然后突然，你就会有一个很难找到原因的 bug。 或者它会在运行时 crash。

扩展函数表达式允许我们解决这个问题。我们现在能给 database 自己增加一个方法， 这将给现有的代码构建一个防护墙。

```kotlin
fun SQLiteDatabase.inTransaction(func: (SQLiteDatabase) -> Unit) {
  beginTransaction()
  try {
    func(this)
    setTransactionSuccessful()
  } finally {
    endTransaction()
  }
}

db.inTransaction {
  it.db.delete("users", "first_name = ?", arrayOf("Jake"))
}
```
开始一个 transaction，调用 “try” 代码段，然后设置 transaction 为成功，最后，结束它。我们接收一个函数作为参数，在使用这个小技巧后，我们在 “try” 代码段里面执行该函数。在我们的消费者代码里，我们会调用这个我们称作 `inTransaction` 的方法，在这个方法里面我们写的代码都会在一个 transaction 里面执行。你没有别的办法来错误地使用它了。

#### 渠道化你的内部 SQLiteDatabase

一个有趣的事情是，在这样的实现方法下，你的代码里面还是会需要 database 的引用。这也是可能会出错的地方。如果你有两个数据库，你可能会在 transaction 里面引用了那个错误的数据库。

我们有多种方法来解决这个问题。一个方法就是我们可以让函数的参数包含需要发生 transaction 的数据库的引用。我们传了 this 给函数，然后我们就不需要调用 “db”，而是使用 “it” 了，它是传给表达式的第一个参数。

这个方法还是不太好。我们阻止了潜在问题的发生，但是现在我们每次访问 database 的时候都需要调用 “it”。这里有个有意思的事情是输入参数是个函数。我们可以把这个函数改写成 “SQLiteDatabase” 的一个扩展函数。

这将会使你有些迷惑，因为它是个疯狂的强大的概念。通过把 `func(this)` 替换成 `this.func()`，传入的函数参数变成了该对象的扩展函数，而且这个扩展函数已经存在 “SQLiteDatabase” 里了。因为我们并不是真正需要 this，所以我们抛弃了它。

这个函数会像在 `SQLiteDatabase` 里面定义的那样执行。如果我们调用删除方法，我们不需要用任何东西来证明自己有资格调用，因为我们就是个 `SQLiteDatabase`。现在我们可以不需要 it 了，所以每一个你放在这个代码段里面的表达式都会看起来像 `SQLiteDatabase` 内部的一个私有方法一样。你不需要证明自己是正确的，因为它总是能在正确的对象上执行自己。

这太棒了，它会把你的代码改编成：

```kotlin
db.inTransaction {
  it.db.delete("users", "first_name = ?", arrayOf("Jake"))
}
```
#### 避免额外的垃圾回收 

这仍然是安卓的一个大问题。等效的 Java 代码中，我们创建了一个函数的新的实例，然后把它传给了我们产生的静态方法。这非常不幸，因为在我们顺序执行代码之前，我们并没有真正地分配内存。

如果我们用 Java 来实现这个，我们需要在每次使用这些函数表达式的时候都分配一些极小的函数对象。这不好，因为这会触发许多的垃圾回收。

谢天谢地，我们有办法在 Kotlin 里面来避免这个问题。我们这里的函数仅仅是个定义好的函数表达式。它使用函数表达式作为入参，这会是个问题。那个函数表达式需要转成一个匿名类。

```kotlin
inline fun SQLiteDatabase.inTransaction(func: SQLiteDatabase.() -> Unit) { ... }
```
我们可以把它变成一个 in-line 的函数，这样我们就告诉了 Kotlin 编译器不要把它作为一个静态函数调用，我需要编译器仅仅把我的函数代码替换到需要调用的地方。虽然这样会产生很多的 bytecode，但是这样产生的 Java 类文件会和容易出错的 Java 代码编译出来的类文件一样的。

经验法则：函数表达式加上 in-line 函数和同样实现的 Java 代码一模一样。现在我们就能清理我们想清理的任何 API 了，找到你安卓里面最差的 API，这样的 API 肯定到处都是。这其中有很多是基于 transaction 的，所以我们可以为 fragments 或者为 shared preferences 使用同样的模式。这是一个增加功能的好方法，而且不会对自动产生的代码带来额外开销。

### JetBrains 提供的 Anko

JetBrains 的同事把这个思路用到了极致，并且创建了一个库叫做 [Anko](https://github.com/Kotlin/anko)。基本想法是 XML 描述性非常好，而且很适合定义 UI，因为它是分级的。当你创建 UI 的时候，它们也是具备分层特性的。

在这些函数表达式的帮助下，我们可以用同样分层式方式来编写代码。当然我们还能够引用其他所有的方法，其他的重构工具，和其他的 Java 代码的静态分析工具来展示 UI。

```kotlin
verticalLayout {
  padding = dip(30)
  editText {
    hint = "Name"
    textSize = 24f
  }
  editText {
    hint = "Password"
    textSize = 24f
  }
  button("Login") {
    textSize = 26f
  }
}
```
这仅仅是扩展函数表达式的一个发展分支。它们会创建这些类的实例，把它们加到它们的父亲那里，设置合适的属性。除了我们在 XML 里面使用的概念以外，比如 layout，你可以调用一个函数来返回这些 layout 和把这些东西组织在一起。

这一定是个有趣的概念。但不一定适合每一个人。它们也有定制的预览插件。使用 XML 的一个好处就是你可以预渲染视图，然后看到它在设备上的样子。他们也写了一个为 Java 代码的工具，这个工具可以解析 Kotlin 代码并且完成渲染。

这也是个 XML 也会令人烦恼的例子。Java 和 XML 这两个分离的系统，会带来一些一般的麻烦。而这个方法会把这两个分离的系统统一到一个 Koltin 的源文件里面。这会导致性能的提升，因为你减少了 XML 解析的开销，也会减少寻找 XML 中定义的类而发生的反射的开销。

所以虽然不是每个人都喜欢这个解决方法，但是这确是个解决他们碰到问题的新的方案。

### 结论

我想给你们介绍现在解决安卓系统中的问题过程中的最有用的一些概念。Koltin 语言还有许多其他的一般性的改进，但那都是为了 Java 语言的。

这就是今天我想讨论的让你意识到的一些安卓系统开发的问题和可能解决它们的具体途径。[Kotlin 网站](https://kotlinlang.org/) 有着非常多的好的资源。那有一个交互性编辑器，使用它你可以在你的浏览器里面创建和运行 Kotlin 代码。

在同样的编辑器里面，它们也有一系列的交互性教程来帮助你一步步学习语法。

语言还处在 1.0 beta 的状态，所以对于那些因为这个原因而持观望态度的人，你们很快就能加入了。我鼓励你们都试试 Kotlin 语言。