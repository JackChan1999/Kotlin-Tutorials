## Kotlin 的类特性(下)

前面三章的内容是写给希望快速了解 Kotlin 语言的大忙人的。
而从本章开始，才会真正讲述 Kotlin 语言的神奇之处。

### 1. 类的扩展

在 Java 开发的时候，经常会写一大堆的 Utils 类，甚至经常写一些common包，比如著名的 `apache-commons`系列、`Guava`等等。
如果每个类在想要用这些工具类的时候，他们自己就已经具备了这些工具方法多好，`Kotlin`的类扩展方法就是这个作用。

#### 1.1 扩展方法

在之前的文章中我就讲过扩展方法了，这里就不再多赘述，只回顾一下扩展方法的格式：

```kotlin
fun Activity.toast(message: CharSequence, duration: Int = Toast.LENGTH_SHORT) {
    Toast.makeText(this, message, duration).show()
}
```

首先是一个`fun`关键字，紧接着是要扩展哪个类的类名，点方法名，然后是方法的声明和返回值以及方法体。

#### 1.2 小心有坑

需要注意的是扩展方法是静态解析的，而并不是真正给类添加了这个方法。
举个例子：

```kotlin
open class Animal{

}
class Dog : Animal()

object Main {
    fun Animal.bark() = "animal"

    fun Dog.bark() = "dog"

    fun Animal.printBark(anim: Animal){
        println(anim.bark())
    }

    @JvmStatic fun main(args: Array<String>) {
        Animal().printBark(Dog())
    }
}
```

最终的输出是 `animal`，而不是`dog`。
因为扩展方法是静态解析的，在添加扩展方法的时候类型为`Animal`，那么即便运行时传入了子类对象，也依旧会执行参数中声明时类型的方法。

#### 1.3 强转与智能转换

在 `Kotlin` 中，用 is 来判断一个对象是否是某个类的实例，用 as 来做强转。

Kotlin 有一个很好的特性，叫 `智能转换(smart cast)`，在我之前的文章中也提到过。就是当已经确定一个对象的类型后，可以自动识别为这个类的对象，而不用再手动强转。

```kotlin
fun main(args: Array<String>) {
	var animal: Animal? = xxx
    if (animal is Dog) {
    	//在这里 animal 被当做 Dog 的对象来处理
       animal.bark()
    }
}
```

#### 1.4 总有例外

如果智能转换的对象是一个全局变量，这个变量可能在别的地方被改变赋值，所以你必须手动判断与转换它的类型。

```kotlin
open class Animal {
}

class Dog : Animal() {
    fun bark() {
        println("animal")
    }
}

var animal: Animal? = null

fun main(args: Array<String>) {
    if (animal is Dog) {
    	//在这里你必须手动强转为Dog的对象
       (animal as Dog).bark()
    }
}
```

### 2. 伴生对象

在上一篇 **Kotlin 与 Java 互转** 中 我们提到这样一段工具类代码

```kotlin
class StringUtils {
    companion object {
       fun isEmpty(str: String): Boolean {
	        return "" == str
	    }
    }
}
```

由于 `Kotlin` 没有静态方法。在大多数情况下，官方建议是简单地使用 包级 函数。如果你需要写一个可以无需用一个类的实例来调用、但需要访问类内部的函数(例如,工厂方法或单利),你可以把它写成一个用 `companion`修饰的对象内的方法。我们称`companion`修饰的对象为伴生对象。

将上面的代码编译后查看，实际上是编译器生成了一个`public`的内部对象。

```kotlin
public final class StringUtils public constructor() {
    public companion object {
        public final fun isEmpty(str: kotlin.String): kotlin.Boolean { 
        /* compiled code */
        }
    }
}
```

### 3. 单例类设计

伴生对象更多的用途是用来创建一个单例类。如果只是简单的写，直接用伴生对象返回一个 `val` 修饰的外部类对象就可以了，但是更多的时候我们希望在类被调用的时候才去初始化他的对象。以下代码将线程安全问题交给虚拟机在静态内部类加载时处理，是一种推荐的写法：

```kotlin
class Single private constructor() {
    companion object {
        fun get():Single{
            return Holder.instance
        }
    }

    private object Holder {
        val instance = Single()
    }
}
```

### 4. 动态代理

写多继承还是要根据场景来，正好今天跟朋友聊到他们项目重构的问题，我当时就说了一句：果然还是`Kotlin`好，原生支持动态代理。

朋友的一个 Android 项目，所有网络请求包括回调和参数全部封装在了一个 BaseActivity 中，然后随着项目越来越大，这一些网络请求方法想要抽出来，但又害怕牵连到线上的改动，我就推荐他用个动态代理来做，但是 Java 的动态代理又得要反射，又得要额外多写很多的代码方法，又是一个大改动。

反之看`Kotlin`的动态代理：

```kotlin
interface Animal{
    fun bark()
}

class Dog :Animal {
    override fun bark() {
        println("Wang Wang")
    }
}

class Cat(animal: Animal) : Animal by animal {
}

fun main(args: Array<String>) {
   Cat(Dog()).bark()
}
```

这样，我们就很成功的让一只猫的叫声用狗去代理掉了，于是上面的`main`方法执行完后就变成了 Wang Wang。

### 5. 伪多继承

Kotlin 的动态代理更多的是用在一种需要多继承的场景。
例如，还是之前我举的我朋友那个项目的例子，他们的问题在于，每个 `BaseActivity` 的子类，都会要请求不同的网络，可能A需要获取用户信息，B需要获取活动列表，C既需要活动列表也需要获取用户信息，D却只需要获取图片列表。

这样一个场景，使用一个代理类实现所有需要获取信息的接口方法。然后让不同的子类去实现所需的接口，请求统一交给代理类完成。这样不仅维护网络请求信息方便，而且每个类不会有额外多出来的方法防止新人接触项目的时候调用错请求方法。

还是用猫狗来举例：

```kotlin
interface Animal{
    fun bark()
}

interface Food{
    fun eat()
}

class Delegate : Animal, Food {
    override fun eat() {
        println("mouse")
    }

    override fun bark() {
        println("Miao")
    }
}

class Cat(animal: Animal, food: Food) : Animal by animal, Food by food {
}

@JvmStatic fun main(args: Array<String>) {
    val delegate: Delegate = Delegate()
    Cat(delegate, delegate).bark()
}
```

Kotlin 的类就介绍这么多，下一章我们讲：**闭包**