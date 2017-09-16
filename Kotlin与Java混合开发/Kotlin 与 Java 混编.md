## Kotlin 与 Java 混编

虽然 Kotlin 的开发很方便，但当你与他人协作时，总会碰到 Java 与 Kotlin 代码共存的代码项目。
本章就教你如何优雅的实现 Kotlin 与 Java 混合编程。

### 1. 直接转换

#### 1.1 将 Java 转换为 Kotlin

如果你之前使用 Java 语言而没有 Kotlin 开发经验，不用担心，Intellij IDEA 会帮你一键转换，将 Java 代码转换成 Kotlin 代码(但是反过来就不行了)。

在 Mac 上，系统默认的快捷键为`control+shift+command+K`，这个组合键实在有点反人类，建议你自定义一个你觉得舒服的快捷键。

快捷键可以通过你的编译器 *keymap* 中修改：`command+,` -> 搜索`keymap` -> 右侧搜索`kotlin`，可以查看到`Convert Java File to Kotlin File`项。

![图3-1-1](http://cdn.kymjs.com/kotlin/3-1-1.png)

#### 1.2 注意 Class 调用

在 Java 或 Android 开发中，经常会直接调用一个类的 Class 文件。但是当你用上文介绍的转换方法去转换 `XXX.class`这样的代码时，是无法直接转换的(也许未来会修复这个问题，但目前你扔需要手动修改)。在 M13 之前，Java 中的`XXX.class`对应 Kotlin 代码中的`JavaClass<XXX>`，而 M13 之后写法已被改为`XXX::class.java`。

#### 1.3 Android proguard 的坑

注：我们团队遇到过这样的一个坑，在 Android 开发的时候，如下代码会在混淆以后，发生异常

```kotlin
var str = some?.s?.d ?: ""
```

这段代码在正常debug模式编译运行完全正常，但是一旦执行混淆，就会发生所在函数被移除的现象。
但是如果改写为以下写法就能正常运行：

```kotlin
var str = some?.s?.d ?: String()
```

猜想应该是 proguard 不知道如何处理这段代码，无法识别出最后两个引号是一个`String`，最后直接将整个函数移除掉了。

同样的代码还有：

```kotlin
var list = some?.data?.list:mutableListof()
```

但是如下代码即使混淆后也是可以完全正常执行的

```kotlin
var s = some?.s ?: ""  
var s = some.d ?: ""
var list = some?.data?.list:klist  
var data = some?.data ?: return
```

#### 1.4 开发 Android library 的建议

如果你是开发 Android library 程序，建议你不要使用 Kotlin 代码。因为作为 library，如果使用它的工程是纯 Java 完成的，引入后会额外增大 200k 左右大小，同时它有可能会造成某些情况下编译异常。

### 2. 在 Kotlin 中调用 Java 代码

#### 2.1 返回 void 的方法

如果一个 Java 方法返回 void，对应的在 Kotlin 代码中它将返回 Unit。关于 Unit，本书将在 第五章`函数`部分着重讲解。 
现在你只需要知道在Java 中返回为 void 的函数，在 Kotlin 中可以省略这个返回类型。

#### 2.2 与 Kotlin 关键字冲突的处理

Java 有 static 关键字，在 Kotlin 中没有这个关键字，你需要使用`@JvmStatic`替代这个关键字。
同样，在 Kotlin 中也有很多的关键字是 Java 中是没有的。例如 `in`,`is`,`data`等。如果 Java 中使用了这些关键字，需要加上反引号(`)转义来避免冲突。例如

```kotlin
// Java 代码中有个方法叫 is()
public void is(){
	//...
}

// 转换为 Kotlin 代码需要加反引号转义
fun `is`() {
   //...
}
```

### 3 在 Java 中调用 Kotlin 代码

#### 3.1 static 方法

上文已经提到过，在 Kotlin 中没有 `static`关键字,那么如果在 Java 代码中想要通过类名调用一个 Kotlin 类的方法，你需要给这个方法加入`@JvmStatic`注解（这个注解只在 jvm 平台有用）。否则你必须通过对象调用这个方法。

```kotlin
StringUtils.isEmpty("hello");  
StringUtils.INSTANCE.isEmpty2("hello");

object StringUtils {
    @JvmStatic fun isEmpty(str: String): Boolean {
        return "" == str
    }

    fun isEmpty2(str: String): Boolean {
        return "" == str
    }
}
```

如果你阅读 Kotlin 代码，应该经常看到这样一种写法。

```kotlin
class StringUtils {
    companion object {
       fun isEmpty(str: String): Boolean {
	        return "" == str
	    }
    }
}
```

`companion object`表示外部类的一个伴生对象，你可以把他理解为外部类自动创建了一个对象作为自己的`field`。
与上面的类似，Java 在调用时，可以这样写：`StringUtils.Companion.isEmpty();`(1.1以后可以省略中间的 Companion，写作 StringUtils.isEmpty())

关于伴生对象，我们将在下一章 `类与对象` 详细讲解。

#### 3.2 包级别函数

与 Java 不同，Kotlin 允许函数独立存在，而不必依赖于某个类，这类函数我们称之为**包级别函数**(Package-Level Functions)。

为了兼容 Java，Kotlin 默认会将所有的包级别函数放在一个自动生成的叫`ExampleKt`的类中， 在 Java 中想要调用包级别函数时，需要通过这个类来调用。 

当然，也是可以自定义的，你只需要通过注解`@file:JvmName("Example")`即可将当前文件中的所有包级别函数放到一个自动生成的名为 Example 的类中。

#### 3.3 空安全性

在 Java 中，如果你调用的 kotlin 方法参数声明了非空类型，如果你在 Java 代码中传入一个空值，将在运行时抛出`NullPointerException`。其内部原因在于 Kotlin 为每个非空类型加了断言，如果传入空值则会立刻抛出异常。 
同样，如果你使用 null 对象去调用一个 kotlin 方法，将会立刻抛出`NullPointerException`（就算是调用普通 java 方法也是一样会抛出 NullPointerException ）