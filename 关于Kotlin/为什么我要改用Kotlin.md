# 为什么我要改用Kotlin

> 原文链接：http://droidyue.com/blog/2017/05/18/why-do-i-turn-to-kotlin/

**写在前面的话，作为一个不熬夜的人，一觉醒来发现Kotlin成为了Android的官方语言，可谓是大喜过望。为了趁热打铁，我决定提前三天放出原定本周日Release的文章。希望能及时让大家了解一下Kotlin。**

相信很多开发人员，尤其是Android开发者都会或多或少听说过Kotlin，当然如果没有听过或者不熟悉也没有关系。因为本篇文章以及博客后期的内容会涉及到很多关于Kotlin的知识分享。

在写这篇文章前的一个多月，Flipboard中国的Android项目确定了正式将Kotlin作为项目开发语言，这就意味着新增的代码文件将以Kotlin代码格式出现，而且同时旧的Java代码也将会陆陆续续翻译成Kotlin代码。在使用Kotlin的这段时间，被它的简洁，高效，快捷等等特点震撼，所以有必要写一篇文章来谈一谈Kotlin的特性，如若能取得推广Kotlin的效果则倍感欣慰。

## Kotlin的“简历”

- 来自于著名的IDE IntelliJ IDEA(Android Studio基于此开发) 软件开发公司 JetBrains(位于东欧捷克)
- 起源来自JetBrains的圣彼得堡团队，名称取自圣彼得堡附近的一个小岛(Kotlin Island)
- 一种基于JVM的静态类型编程语言

来自知名的工具开发商JetBrains，也就决定了Kotlin的基因中必然包含实用与高效等特征。那我们接下来看一看Kotlin的特点，当然这也是我改用Kotlin的重要原因。

### 语法简单，不啰嗦

```kotlin
//variables and constants
var currentVersionCode = 1   //变量当前的版本号，类型Int可以根据值推断出来
var currentVersionName : String = "1.0" //显式标明类型
val APPNAME = "droidyue.com" //常量APPNAME 类型(String)可以根据值推断出来

//methods
fun main(args: Array<String>) {
    println(args)
}

// class
class MainActivity : AppCompatActivity() {
  
}

// data class  自动生成getter，setting，hashcode和equals等方法
data class Book(var name: String, val price: Float, var author: String)

//支持默认参数值，减少方法重载
fun Context.showToast(message: String, duration:Int = Toast.LENGTH_LONG) {
    Toast.makeText(this, message, duration).show()
}
```

- Kotlin支持类型推断，没有Java那样的啰嗦。
- 另外用`var`表示变量，`val`表示常量更加的简洁
- 方法也很简单，连function都缩写成了fun，平添了几分双关之意。
- 类的继承和实现很简单，使用:即可
- Kotlin每个句子都不需要加分号(;)

### 空指针安全

空指针（NullPointerException或NPE）是我们使用Java开发程序中最常见的崩溃了。因为在Java中我们不得不写很多防御性的代码，比如这样

```kotlin
public void test(String string) {
    if (string != null) {
        char[] chars = string.toCharArray();
        if (chars.length > 10) {
            System.out.println(((Character)chars[10]).hashCode());
        }
    }
}
```

在Kotlin中空指针异常得到了很好的解决。

- 在类型上的处理，即在类型后面加上?，即表示这个变量或参数以及返回值可以为null，否则不允许为变量参数赋值为null或者返回null
- 对于一个可能是null的变量或者参数，在调用对象方法或者属性之前，需要加上?，否则编译无法通过。

如下面的代码就是Kotlin实现空指针安全的一个例子，而且相对Java实现而言，简直是一行代码搞定的。

```kotlin
fun testNullSafeOperator(string: String?) {
    System.out.println(string?.toCharArray()?.getOrNull(10)?.hashCode())
}

testNullSafeOperator(null)
testNullSafeOperator("12345678901")
testNullSafeOperator("123")

//result
null
49
null
```

关于空指针安全的原理，可以参考这篇文章[研究学习Kotlin的一些方法](http://droidyue.com/newtab.html?url=http%3A%2F%2Fdroidyue.com%2Fblog%2F2017%2F05%2F08%2Fhow-to-study-kotlin%2F)

### 支持方法扩展

很多时候，Framework提供给我们的API往往都时比较原子的，调用时需要我们进行组合处理，因为就会产生了一些Util类，一个简单的例子，我们想要更快捷的展示Toast信息，在Java中我们可以这样做。

```kotlin
public static void longToast(Context context, String message) {
    Toast.makeText(context, message, Toast.LENGTH_LONG).show();
}
```

但是Kotlin的实现却让人惊奇，我们只需要重写扩展方法就可以了，比如这个longToast方法扩展到所有的Context对象中，如果不去追根溯源，可能无法区分是Framework提供的还是自行扩展的。

```kotlin
fun Context.longToast(message: String) {
    Toast.makeText(this, message, Toast.LENGTH_LONG).show()
}
applicationContext.longToast("hello world")
```

注意：Kotlin的方法扩展并不是真正修改了对应的类文件，而是在编译器和IDE方面做得处理。使我们看起来像是扩展了方法。

### Lambda, 高阶函数，Streams API, 函数式编程支持

所谓的Lambda表达式是匿名函数，这使得我们的代码会更加的简单。比如下面的代码就是lambda的应用。

```kotlin
findViewById(R.id.content).setOnClickListener {
    Log.d("MainActivity", "$it was clicked")
}
```

所谓的高阶函数就是

- 可以接受函数作为参数
- 也可以返回函数作为结果

举一个接受函数作为参数的例子。在Android开发中，我们经常使用SharedPreference来存储数据，如果忘记调用apply或者commit则数据修改不能应用。利用Kotlin中的高阶函数的功能，我们能更好的解决这个问题

```kotlin
fun SharedPreferences.editor(f: (SharedPreferences.Editor) -> Unit) {
    val editor = edit()
    f(editor)
    editor.apply()
}

//实际调用
PreferenceManager.getDefaultSharedPreferences(this).editor {
    it.putBoolean("installed", true)
}
```

当然这上面的例子中我们也同时使用了方法扩展这个特性。

Kotlin支持了Streams API和方法引用，这样函数式编程更加方便。比如下面的代码就是我们结合Jsoup，来抓取某个proxy网站的数据，代码更加简单，实现起来也快速。

```kotlin
fun parse(url: String): Unit {
    Jsoup.parse(URL(url), PARSE_URL_TIMEOUT).getElementsByClass("table table-sm")
        .first().children()
        .filter { "tbody".equals(it.tagName().toLowerCase()) }
        .flatMap(Element::children).forEach {
            trElement ->
            ProxyItem().apply {
                trElement.children().forEachIndexed { index, element ->
                    when (index) {
                        0 -> {
                            host = element.text().split(":")[0]
                            port = element.text().split(":")[1].toInt()
                        }
                        1 -> protocol = element.text()
                        5 -> country = element.text()
                    }
                }
            }.let(::println)
        }
}
```

### 字符串模板

无论是Java还是Android开发，我们都会用到字符串拼接，比如进行日志输出等等。在Kotlin中，字符串模板是支持的，我们可以很轻松的完成一个字符串数组的组成

```kotlin
val book = Book("Thinking In Java", 59.0f, "Unknown")
val extraValue = "extra"
Log.d("MainActivity", "book.name = ${book.name}; book.price=${book.price};extraValue=$extraValue")
```

注意：关于字符串拼接可以参考这篇文章[Java细节：字符串的拼接](http://droidyue.com/newtab.html?url=http%3A%2F%2Fdroidyue.com%2Fblog%2F2014%2F08%2F30%2Fjava-details-string-concatenation%2F)

### 与Java交互性好

Kotlin和Java都属于基于JVM的编程语言。Kotlin和Java的交互性很好，可以说是无缝连接。这表现在

- Kotlin可以自由的引用Java的代码，反之亦然。
- Kotlin可以现有的全部的Java框架和库
- Java文件可以很轻松的借助IntelliJ的插件转成kotlin

### Kotlin应用广泛

Kotlin对Android应用开发支持广泛，诸多工具，比如kotterknife(ButterKnife Kotlin版)，RxKotlin,Anko等等，当然还有已经存在的很多Java的库都是可以使用的。

除此之外，Kotlin也可以编译成Javascript。最近使用Kotlin写了一段抓取proxy的代码，实现起来非常快捷。甚至比纯JavaScript实现起来要快很多。

```kotlin
fun handle(): Unit {
        window.onload = {
            document.getElementsByClassName("table table-sm").asList().first()
                    .children.asList().filter { "TBODY".equals(it.tagName.toUpperCase()) }
                    .flatMap { it.children.asList() }.forEach {
                var proxyItem = ProxyItem()
                it.children.asList().forEachIndexed { index, element ->
                    when (index) {
                        0 -> {
                            proxyItem.host = element.trimedTextContent()?.split(":")?.get(0) ?: ""
                            proxyItem.port = element.trimedTextContent()?.split(":")?.get(1)?.trim()?.toInt() ?: -1
                        }
                        1 -> proxyItem.protocol = element.trimedTextContent() ?: ""
                        5 -> proxyItem.country = element.trimedTextContent() ?: ""
                    }
                }.run {
                    console.info("proxyItem $proxyItem")
                }

            }
        }
    }
```

### 关于性能

Kotlin的执行效率和Java代码的执行效率理论上一致的。有时候Kotlin可能会显得高一些，比如Kotlin提供了方法的inline设置，可以设置某些高频方法进行inline操作，减少了运行时的进栈出栈和保存状态的开销。

读到这里，是不是想要尝试一下Kotlin呢，它简洁的语法，汇集诸多特性，高效率实现等等，已经在国外风生水起，国外的Pintereset, Square, Flipboard等公司已经开始应用到生产中。

### 关于转向Kotlin

其实，我在做决定之前（当时Kotlin还没有被钦定）也曾有过考虑，是不是选择了Kotlin就意味着放弃Java呢，冷静下来想一想，其实并不是那么回事，因为Kotlin与Java语法太相近，以及在Kotlin中无时无刻不在和Java相关的东西打交道，所以这点顾虑不是问题的。

对于个人的项目来转向Kotlin，通常不是很难的选择，毕竟Kotlin是那么优秀的语言，相信很多人还是愿意尝试并使用这个事半功倍的语言的。

而比较难抉择的情况是如果如何让团队转用Kotlin，个人认为团队难以转用的原因有很多，比如学习成本，历史包袱等等。但其实根本原因还是思维方式的问题，歪果仁喜欢用工具来提升开发效率，因为人力成本很高。而国内团队提高效率的办法通常是增加成员。好在Flipboard 美国团队自2015年（可能更早）就引入了Kotlin，因此中国团队这边选用Kotlin也更加顺水推舟。当然更主要的是目前团队规模不大，成员一致认可Kotlin的优点。

关于团队转用Kotlin的方法，一般比较行得通的办法是自上而下的推行。这就意味着要么直接的技术负责人比较开明要么就是需要有人来不断推介来影响团队。

做个比较现实的比拟，Java就像是一趟从我的家乡保定开往北京西的耗时将近2个小时甚至更长的普通列车，而Kotlin则是那趟仅需40分钟就能到达的高铁。通常的人都会选择高铁，因为它节省了时间和提高了体验。这个时间和体验对应编程中的，我想应该是高效率和高可读性，可维护性的代码。

现在好了，有了Google的支持，Kotlin转Android相信在不久的将来就会全面展开。篡改Python的一句名言“人生苦短，我用Kotlin”，这样一个高效实用的语言应该会被越来越多的团队所接受，并应用到开发生产中。当然也希望在国内环境下大放异彩。