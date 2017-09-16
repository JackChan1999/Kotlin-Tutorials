# Android 必备技能：最有可能接替Java的语言——Kotlin

> 原文链接：http://bennyhuo.leanote.com/post/Android-A-Powerful-Substitution-Kotlin

# 1. Hello, Kotlin

## 1.1 Kotlin的身世

- 写了许久Java，有没有发现其实你写了太多冗余的代码？
- 后来你体验了一下Python，有没有觉得不写分号的感觉真是超级爽？
- 你虽然勤勤恳恳，可到头来却被NullPointerException折磨的死去活来，难道就没有受够这种日子么？
- 直到有一天你发现自己已经写了好几十万行代码，发现居然全是getter和setter！

哈哈，实际上你完全可以不用这么痛苦，用Kotlin替代Java开发你的程序，无论是Android还是Server，你都能像之前写Java一样思考，同时又能享受到新一代编程语言的特性，说到这里你是不是开始心动了呢？下面我就通过这篇文章来给大家介绍一下Kotlin究竟是何方神圣。

话说，Kotlin是JetBrain公司搞出来的，运行在JVM上的一门静态类型语言，它是用波罗的海的一个小岛的名字命名的。从外观上，乍一看还以为是Scala，我曾经琢磨着把Scala作为我的下一门语言，不过想想用Scala来干嘛呢，我又不做大数据，而它又太复杂了o(╯□╰)o

**用Kotlin创建一个数据类**

```
data class Mondai(var index: Int = 0,                  var title: String = "",                  val ans: ArrayList<String> = ArrayList(),                  var correct: Int = 0,                  var comment: String = "",                  var color: String = "",                  private var lives: Int = 50)
```

最初是在intelliJ的源码中看到Kotlin的，那时候Kotlin的版本还不太稳定，所以源码总是编译不过，真是要抓狂啊，还骂『什么破玩意儿！为什么又出来新语言了？Groovy还没怎么学会，又来个Kotlin！』话说，Kotlin，难道是『靠它灵』的意思？？

其实经过一年多的发展，Kotlin 1.0已经release，feature基本完善，api也趋于稳定，这时候尝试也不会有那种被坑的感觉了。过年期间也算清闲，于是用Kotlin做了个app，简单来说，就是几个感觉：

- 思路与写Java时一样，不过更简洁清爽
- 少了冗余代码的烦恼，更容易专注于功能的开发，整个过程轻松愉快
- 扩展功能使得代码写起来更有趣
- 空安全和不可变类型使得开发中对变量的定义和初始化倾注了更多关注
- 啊啊，我再也不用写那个findViewById了，真的爽爆有木有！

## 1.2 第一个Kotlin程序

Kotlin开发当然使用JetBrain系列的IDE，实际上intelliJ idea 15发布时就已经内置了Kotlin插件，更早的版本则需要到插件仓库中下载安装Kotlin插件——在安装时你还会看到有个**Kotlin Extensions for Android**，不要管他，已经过时了。安装好以后，我们就可以使用Kotlin进行开发了。

接下来我们用Android Studio创建一个Android工程，比如叫做HelloKotlin，在app目录下面的build.gradle文件中添加下面的配置：

```
apply plugin: 'kotlin-android'apply plugin: 'kotlin-android-extensions'ext.anko_version = '0.8.2'ext.kotlin_version = '1.0.0'……dependencies{……    compile "org.jetbrains.kotlin:kotlin-stdlib:$kotlin_version"    compile "org.jetbrains.anko:anko-sdk15:$anko_version"    compile "org.jetbrains.anko:anko-support-v4:$anko_version"    compile "org.jetbrains.kotlin:kotlin-reflect:$kotlin_version"……}buildscript {    repositories {        jcenter()    }    dependencies {        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"    }}……
```

这里添加了kotlin对android的扩展，同时也添加了kotlin的gradle插件。

接下来就可以编写kotlin代码了——等等，Android Studio会帮我们生成一个MainActivity，你可以直接在菜单

> Code -> Convert Java file to Kotlin file

将这个java代码转换为kotlin代码。截止到现在，你什么都不用做，程序就已经可以跑起来了。

# 2. 完美为Java开发者打造

## 2.1 通用的集合框架

我们都知道Jvm上面的语言，像什么Java、Groovy、Jython啥的，都是要编成虚拟机的字节码的，一旦编成字节码，在一定程度上大家就都平等了。

> 英雄不问出身啊

有人做过一个非常形象的比喻：Java虚拟机语言就是打群架。Kotlin正是充分利用了这一点，它自己的标准库只是基于Java的语言框架做了许多扩展，你在Kotlin当中使用的集合框架仍然跟你在Java当中一样。

举个例子，如果你想要在Kotlin中使用ArrayList，很简单，Java的ArrayList你可以随意使用，这个感觉跟使用Java没有任何区别，请看：

```
//实际上就是创建一个ArrayList    val list = arrayListOf(1,2,3,4)    list.add(5)    list.remove(3)    for(item in list){        println(item)    }
```

当然，Kotlin标准库也对这些做了扩展，我们在享用Java世界的一切资源的同时，还能比原生Java代码更滋润，真是爽爆有木有：

```
   val list = arrayListOf(1, 2, 3, 4, 5)    //doubleList = [2,4,6,8,10]    val doubleList = list.map {         it * 2    }    //oddList = [1,3,5]    val oddList = list.filter{        it % 2 == 1    }    //将list挨个打印出来    list.forEach {         println(it)    }
```

## 2.2 与Java交互

Kotlin的标准库更多的是对Java库的扩展，基于这个设计思路，你丝毫不需要担心Kotlin对Java代码的引用，你甚至可以在Kotlin当中使用Java反射，反正只要是Java有的，Kotlin都有，于是有人做出这样的评价：

> Kotlin就是Java的一个扩展

这样说Kotlin显然是不公平的，但就像微信刚面世那会儿要为QQ接收离线消息一样，总得抱几天大腿嘛。

有关从Kotlin中调用Java的官方文档在此[Calling Java code from Kotlin ](https://kotlinlang.org/docs/reference/java-interop.html#static-methods-and-fields)，其中最常见的就是Getter/Setter方法对应到Kotlin属性的调用，举个例子：

**准备一个Java类**

```
public class JavaClass {    private int anInt = 0;    public int getAnInt() {        return anInt;    }    public void setAnInt(int anInt) {        this.anInt = anInt;    }}
```

**下面是Kotlin代码**

```
    val javaClass = JavaClass()    javaClass.anInt = 5    print(javaClass.anInt)
```

所以我们在Android开发时，就可以这样：

```
view.background = ...textView.text = ...
```

反过来在Java中调用Kotlin也毫无压力，官方文档[Calling Kotlin from Java](https://kotlinlang.org/docs/reference/java-to-kotlin-interop.html)对于常见的情况作了比较详细的阐述，这里就不再赘述。

# 3. 简洁，可靠，有趣

## 3.1 数据类

最初学Java的时候，学到一个概念叫JavaBean，当时就要被这个概念给折磨死了。明明很简单的一个东西，结果搞得很复杂的样子，而且由于当时对于这些数据类的设计概念不是很清晰，因而也并不懂得去覆写诸如equals和hashcode这样重要的方法，一旦用到HashMap这样的集合框架，总是出了问题都不知道找谁。

Kotlin提供了一种非常简单的方式来创建这样的数据类，例如：

```
data class Coordinate(val x: Double, val y: Double)
```

仅仅一行代码，Kotlin就会创建出一个完整的数据类，并自动生成相应的equals、hashcode、toString方法。是不是早就受够了getter和setter？反正我是受够了。

## 3.2 空安全与属性代理

第一次见到空类型安全的设计是在Swift当中，那时候还觉得这个东西有点儿意思哈，一旦要求变量不能为空以后，因它而导致的空指针异常的可能性就直接没有了。想想每次QA提的bug吧，说少了都得有三分之一是空指针吧。

Kotlin的空安全设计，主要是在类型后面加？表示可空，否则就不能为null。

```
val anInt: Int = null // 错误val anotherInt: Int? = null // 正确
```

使用时，则：

```
val nullable: Int? = 0val nonNullable: Int = 2nullable.toFloat() // 编译错误nullable?.toFloat() // 如果null，什么都不做，否则调用toFloatnullable!!.toFloat() // 强制转换为非空对象，并调用toFloat；如果nullable为null，抛空指针异常nonNullable.toFloat() // 正确
```

而对于Java代码，比如我们在覆写Activity的onCreate方法时，有个参数savedInstanceState：

```
override fun onCreate(savedInstanceState: Bundle!) 
```

这表示编译器不再强制savedInstanceState是否可null，开发者在覆写时可以自己决定是否可null。当然，对于本例，onCreate的参数是可能为null的，因此覆写以后的方法应为：

```
override fun onCreate(savedInstanceState: Bundle?) 
```

通常来讲，教科书式的讲法，到这里就该结束了。然而直到我真正用Kotlin开始写代码时，发现，有些需求实现起来真的有些奇怪。

还是举个例子，我需要在Activity当中创建一个View的引用，通常我们在Java代码中这么写：

```
public class DemoActivity extends Activity{    private TextView aTextView;    public void onCreate(Bundle savedInstanceState){        super.OnCreate(savedInstanceState);        setContentView(R.layout.main);        aTextView = (TextView) findViewById(R.id.a_textview);        aTextView.setText("Hello");        aTextView.setTextSize(20);        ...    }}
```

在Kotlin当中呢？

```
class DemoActivity : Activity(){    private var aTextView: TextView? = null    override fun onCreate(savedInstanceState: Bundle?){        super.onCreate(savedInstanceState)        setContentView(R.layout.main)        //当然有更好用的方式，暂且先这么写        aTextView = findViewById(R.id.a_textview) as TextView        aTextView!!.text = "Hello"        aTextView!!.textSize = 20        ...    }}
```

每次用aTextView都要加俩!，不然编译器不能确定它究竟是不是null，于是不让你使用。。这尼玛。。。到底是为了方便还是为了麻烦？？

所以后来我又决定这么写：

```
class DemoActivity : Activity(){    private var aTextView: TextView // 编译错误，必须初始化！！！    ...}
```

这可如何是好？？

其实Kotlin肯定是有办法解决这个问题哒！比如上面的场景，我们这么写就可以咯：

```
class DemoActivity : Activity(){    private val aTextView: TextView by lazy{        findViewById(R.id.a_textview) as TextView    }    override fun onCreate(savedInstanceState: Bundle?){        super.onCreate(savedInstanceState)        setContentView(R.layout.main)        aTextView.text = "Hello"        aTextView.textSize = 20        ...    }}
```

lazy是Kotlin的属性代理的一个实例，它提供了延迟加载的机制。换句话说，这里的lazy提供了初始化aTextView的方法，不过真正初始化这个动作发生的时机却是在aTextView第一次被使用时了。lazy默认是线程安全的，你当然也可以关掉这个配置，只需要加个参数即可：

```
private val aTextView: TextView by lazy(LazyThreadSafetyMode.NONE){     findViewById(R.id.a_textview) as TextView}
```

好，这时候肯定有人要扔西红柿过来了（再扔点儿鸡蛋呗），你这lazy只能初始化val啊，万一我要定义一个var成语，又需要延迟初始化，关键还不为null，怎么办？？

```
class Demo {    lateinit var anJsonObject: JsonObject    fun initDemo(){        anJsonObject = JsonObject("{...}")    }}
```

lateinit的使用还是有很多限制的，比如只能在不可null的对象上使用，比须为var，不能为primitives（Int、Float之类）等等，不过这样逼迫你一定要初始化这个变量的做法，确实能减少我们在开发中的遗漏，从而提高开发效率。

至于lazy技术，实际上是Delegate Properties的一个应用，也就是属性代理了。在Kotlin当中，声明成员属性，除了直接赋值，还可以用Delegate的方式来声明，这个Delegate需要根据成员的类型（val或者var）来提供相应的getValue和setValue方法，比如一个可读写的Delegate，需要提供下面的方法：

```
public interface ReadWriteProperty<in R, T> {    /**     * Returns the value of the property for the given object.     * @param thisRef the object for which the value is requested.     * @param property the metadata for the property.     * @return the property value.     */    public operator fun getValue(thisRef: R, property: KProperty<*>): T    /**     * Sets the value of the property for the given object.     * @param thisRef the object for which the value is requested.     * @param property the metadata for the property.     * @param value the value to set.     */    public operator fun setValue(thisRef: R, property: KProperty<*>, value: T)}
```

好嘴皮不如来个栗子，下面我们就看一个自定义Delegate，用来访问SharedPreference：

```
class Preference<T>(val context: Context, val name: String, val default: T) : ReadWriteProperty<Any?, T> {    val prefs by lazy { context.getSharedPreferences("default", Context.MODE_PRIVATE) }    override fun getValue(thisRef: Any?, property: KProperty<*>): T {        return findPreference(name, default)    }    override fun setValue(thisRef: Any?, property: KProperty<*>, value: T) {        putPreference(name, value)    }    private fun <U> findPreference(name: String, default: U): U = with(prefs) {        val res: Any = when (default) {            is Long -> getLong(name, default)            is String -> getString(name, default)            is Int -> getInt(name, default)            is Boolean -> getBoolean(name, default)            is Float -> getFloat(name, default)            else -> throw IllegalArgumentException("This type can be saved into Preferences")        }        res as U    }    private fun <U> putPreference(name: String, value: U) = with(prefs.edit()) {        when (value) {            is Long -> putLong(name, value)            is String -> putString(name, value)            is Int -> putInt(name, value)            is Boolean -> putBoolean(name, value)            is Float -> putFloat(name, value)            else -> throw IllegalArgumentException("This type can be saved into Preferences")        }.apply()    }}
```

需要说明的是，这段代码是我从《Kotlin for Android Developer》的示例中摘出来的。有了这个Delegate类，我们就可以完全不需要关心SharedPreference了，下面给出使用的示例代码：

```
class WhateverActivity : Activity(){    var aInt: Int by Preference(this, "aInt", 0)    fun whatever(){        println(aInt)//会从SharedPreference取这个数据        aInt = 9 //会将这个数据写入SharedPreference    }}
```

于是我们再也不需要重复写那些getSharedPreference，也不用edit、commit，再见那些edit之后忘了commit的日子。有没有觉得非常赞！

## 3.3 扩展类

扩展类，就是在现有类的基础上，添加一些属性或者方法，当然扩展的这些成员需要导入当前扩展成员所在的包才可以访问到。下面给出一个例子：

```
data class Coordinate(val x: Double, val y: Double)val Coordinate.theta: Double    get() {        return Math.atan(y/x)    }fun Coordinate.R():Double{    return Math.hypot(x, y)}
```

我们已经介绍过data class，Coordinate有两个成员分别是x和y，我们知道通常表示一个二维平面，有这俩够了；然而我们在图形学当中经常会需要求得其极坐标，所以我们扩展了Coordinate，增加了一个属性theta表示角度（反正切的值域为-π/2 ~ π/2，所以这个式子不适用于二三象限，不过这不是重点了），增加了一个R方法来获得点的半径，于是我们在main方法中就可以这么用：

```
fun main(args: Array<String>) {    val coord = Coordinate(3.0,4.0)    println(coord.theta)    println(coord.R())}
```

那么这个扩展有什么限制呢？

- 在扩展成员当中，只能访问被扩展类在当前作用域内可见的成员，本例中的x和y都是public的（Kotlin默认public，这个我们后面会提到），所以可以在扩展方法和属性中直接访问。
- 扩展成员与被扩展类的内部成员名称相同时，扩展成员将无法被访问到

好的，基本知识就是这些了，下面我们再给出一个实际的例子。

通常我们在Java中会自定义一些LogUtils类来打日志，或者直接用android.util.log来输出日志，不知道大家是什么感受，我反正每次因为要输入Log.d还要输入个tag简直烦的要死，而且有时候恰好这个类还没有tag这个成员，实践中我们通常会把当前类名作为TAG，但每个类都要做这么个工作，是在是没有什么趣味可言（之前我是用LiveTemplates帮我的，即便如此也没有那种流畅的感觉）。

有了Kotlin的这个扩展功能，日子就会好过得多了，下面我创建的一个打日志的方法：

```
package com.benny.utilsimport android.util.Loginline fun <reified T> T.debug(log: Any){    Log.d(T::class.simpleName, log.toString())}
```

有了这个方法，你可以在任何类的方法体中直接写：

```
debug(whatever)
```

然后就会输出以这个类名为TAG的日志。

嗯，这里需要简单介绍Kotlin在泛型中的一个比较重要的增强，这个在Java中无论如何也是做不到的：inline、reified。我们再来回头看一下debug这个方法，我们发现它可以通过泛型参数T来获取到T的具体类型，并且拿到它的类名——当然，如果你愿意，你甚至可以调用它的构造方法来构造一个对象出来——为什么Kotlin可以做到呢？因为这段代码是inline的，最终编译时是要编译到调用它的代码块中，这时候T的类型实际上是确定的，因而Kotlin通过reified这个关键字告诉编译器，T这个参数可不只是个摆设，我要把它当实际类型来用呢。

为了让大家印象深刻，我下面给出类似功能的Java的代码实现：

```
public static void debug(Class<?> clazz, Object log){    Log.d(clazz.getSimpleName(), log.toString());}
```

而你如果说希望在Java中也希望像下面这样拿到这个泛型参数的类型，是不可以的：

```
public static <T> void debug(Object log){    Log.d(T.getSimpleName(), log.toString());//错误，T是泛型参数，无法直接使用}
```

就算我们在调用处会写道 debug < Date >(“blabla”)，但这个Date在编译之后还是会被擦除。

## 3.4 函数式支持（Lambdas）

Java 8已经开始可以支持Lambda表达式了，这种东西对于Java这样一个『根红苗正』的面向对象编程语言来说还真是显得不自然，不过对于Kotlin来说，就没那么多顾忌了。

通常我们需要执行一段异步的代码，我们会构造一个Runnable对象，然后交给executor，比如这段java代码：

```
executor.submit(new Runnable(){    @Override    public void run(){        //todo    }});
```

用Kotlin怎么写呢？

```
executor.submit({    //todo})
```

一下子省了很多代码。

那么实际当中我们可能更常见到下面的例子，这是一段很常见的Java代码，在Android的UI初始化会见到：

```
textView.setOnClickListener(new OnClickListener(){    @Override    public void onClick(View view){        //todo    }});handle.post(new Runnable(){    @Override    public void run(){        //todo    }});
```

那么我们用Kotlin怎么写呢？

```
textView.setOnClickListener{ /*todo*/ }handler.post{ /*todo*/ }
```

在Anko这个Android库的帮助下，我们甚至可以继续简化OnClickListener的设置方式：

```
textView.onClick{ /*todo*/ }
```

当然，好玩的不止这些，如果结合上一节我们提到的扩展方法，我们就很容易看到Kotlin的标准库提供的类似with和apply这样的方法是怎么工作的了：

```
public inline fun <T, R> with(receiver: T, block: T.() -> R): R = receiver.block()public inline fun <T> T.apply(block: T.() -> Unit): T { block(); return this }
```

我们通常会在某个方法体内创建一个对象并返回它，可我们除了调用它的构造方法之外还需要做一些其他的操作，于是就要创建一个局部变量。。。有了apply这个扩展方法，我们就可以这么写：

```
fun getStringBuilder: StringBuilder{    return StringBuilder().apply{        append("whatever")    }}
```

这样返回的StringBuilder对象实际上是包含”whatever”这个字符串的。

至于说Kotlin对于RxJava的友好性，使得我突然有点儿相信缘分这种东西了：

```
       Observable.create<ArrayList<Dummy>> {            it.onStart()            try {                it.onNext(dummyObjs)            } catch(e: Exception) {                it.onError(e)            } finally {                it.onCompleted()            }        }.subscribe(object : Subscriber<ArrayList<Dummy>>() {            override fun onCompleted() {            }            override fun onNext(t: ArrayList<Dummy>?) {            }            override fun onError(e: Throwable?) {            }        })
```

## 3.5 Pattern Matching

记得之前在浏览Scala的特性时，看到：

```
object HelloScala{  // do something}
```

觉得很新鲜，这时候有个朋友不屑的说了句，Scala的模式匹配才真正犀利——Kotlin当中也有这样的特性，我们下面就来看个例子：

```
val x = 7when (x) {  in 1..10 -> print("x is in the range")  in validNumbers -> print("x is valid")  !in 10..20 -> print("x is outside the range")  else -> print("none of the above")}
```

咋一看感觉when表达式就是一个增强版的switch——Java 7以前的switch实际上支持的类型非常有限，Java 7当中增加的对String的支持也是基于int类型的——我们可以看到when不再像switch那样只匹配一个数值，它的子式可以是各种返回Boolean的表达式。

when表达式还有一种写法更革命：

```
when {  x.isOdd() -> print("x is odd")  x.isEven() -> print("x is even")  else -> print("x is funny")}
```

只要是返回Boolean的表达式就可以作为when的子式，这样when表达式的灵活性可见一斑。当然，与Scala相比，**Kotlin还是要保守一些的，下面给出一个Scala类似的例子，大家感受一下，这实际上也可以体现出Kotlin在增加Java的同时也尽量保持简单的设计哲学**（大家都知道，毕竟Scala需要智商o(╯□╰)o）。

```
object Hello {  def main(args: Array[String]) {    easyMatch((1, 3))    easyMatch(Array(1,3,4))    easyMatch(Bean(3.0, 4.0))  }  def easyMatch(value : Any) = value match {      case int :Int => {        println("This is an Int.")      }      case (a, b) =>{        println(s"a tuple with : $a , $b")      }      case Bean(x, y)  => {        println(s"$x, $y")    }      case whatever => println(whatever)    }}case class Bean(val x: Double, val y: Double)
```

运行结果如下：

```
a tuple with : 1 , 3[I@2d5548253.0, 4.0
```

## 3.6 如果你是一个SDK开发者

我曾经做过一段时间的SDK开发，SDK的内部有很多类其实是需要互相有访问权限的，但一旦类及其成员是public的，那么调用方也就可以看到它们了；而protected或者default这样的可见性对于子包却是不可见的。

用了这么久Java，这简直是我唯一强烈感到不满的地方了，甚至于我突然明白了C++的friend是多么的有用。

Kotlin虽然没有提供对于子包可见的修饰符，不过它提供了internal：即模块内可见。换句话说，internal在模块内相当于public，而对于模块外就是private了——于是乎我们如果开发SDK，那么可以减少api层的编写，那些用户不可见的部分直接用internal岂不更好。当然有人会说我们应当有proguard做混淆，我想说的是，proguard自然是要用到的，不过那是SDK这个产品加工的下一个环节了，我们为什么不能在代码级别把这个事情做好呢？

关于Kotlin的默认可见性究竟是哪个还有人做出过讨论，有兴趣的可以参考这里：[Kotlin’s default visibility should be internal](https://discuss.kotlinlang.org/t/kotlins-default-visibility-should-be-internal/1400)。

## 3.7 DSL

其实我们对DSL肯定不会陌生，gradle的脚本就是基于groovy的DSL，而Kotlin的函数特性显然也是可以支持DSL的。比如，我们最终要生成下面的xml数据：

```
<project version="4">  <component name="Encoding">    <file url="PROJECT" charset="UTF-8" />  </component></project>
```

我们可以构建下面的类：

```
class Project {    var version: String? = null        get() =        if (field == null) ""        else {            " version=\"${field}\""        }    lateinit private var component: Component    fun component(op: Component.() -> Unit) {        component = Component().apply {            op()        }    }    override fun toString(): String {        return "<project${version}>${component}<project>"    }}fun project(op: Project.() -> Unit): Project {    return Project().apply {        op()    }}class Component {    var name: String? = null        get() =        if (field == null) ""        else {            " name=\"${field}\""        }    lateinit private var file: File    fun file(op: File.() -> Unit) {        file = File().apply {            op()        }    }    override fun toString(): String {        return "<component${name}>${file}<component>"    }}class File {    var url: String? = null        get() =        if (field == null) ""        else {            " url=\"${field}\""        }    var charset: String? = null        get() =        if (field == null) ""        else {            " charset=\"${field}\""        }    override fun toString(): String {        return "<file${url}${charset}/>"    }}fun main(args: Array<String>) {    val xml = project {        version = "4"        component {            name = "Encoding"            file {                url = "PROJECT"                charset = "UTF-8"            }        }    }    println(xml)}
```

我们看到在main方法当中，我们用kotlin定义的dsl写出了一个Project对象，它有这与xml描述的一致的结构和含义，如果你愿意，可以构造相应的方法来输出这样的xml，运行之后的结果：

```
<project version="4"><component name="Encoding"><file url="PROJECT" charset="UTF-8"/><component><project>
```

当然，这个例子做的足够的简陋，如果你有兴趣也可以抽象出”Element”，并为之添加”Attributes”，实际上这也不是很难。

## 3.7 Kotlin与Android的另一些有趣的东西

写了很多代码，却发现它们干不了多少事情，终究还是会苦恼的。比如我一直比较痛苦的一件事儿就是：

```
Button button = (Button) findViewById(R.id.btn);
```

如果我需要很多个按钮和图片，那么我们要写一大片这样的findViewById。。妈呀。。。这活我干不了啦。。

不过用Kotlin的Android扩展插件，我们就可以这样：

先上布局文件：

**main.xml**

```
<?xml version="1.0" encoding="utf-8"?><RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"                android:layout_width="match_parent"                android:layout_height="match_parent"                android:background="@drawable/open_bj"                android:orientation="vertical">        <TextView            android:id="@+id/textView"                    android:text="Hello"            android:textSize="50sp"            android:layout_width="wrap_content"            android:layout_height="wrap_content"/>        <Button            android:id="@+id/start"            android:clickable="false"            android:layout_gravity="center_horizontal"            android:background="@drawable/start_selector"            android:textSize="50sp"            android:layout_marginTop="20dp"            android:layout_marginBottom="200dp"            android:layout_width="wrap_content"            android:layout_height="wrap_content"/></RelativeLayout>
```

在Activity中：

```
package com.benny……import kotlinx.android.synthetic.main.load_activity.*import org.jetbrains.anko.onClickimport org.jetbrains.anko.startActivityimport org.jetbrains.anko.toast……class LoadActivity : Activity() {    override fun onCreate(savedInstanceState: Bundle?) {        super.onCreate(savedInstanceState)        setContentView(R.layout.main)        start.onClick {            toast("开始")            startActivity<AnotherActivity>()        }        textView.text = "你好"    }}
```

注意到：

```
import kotlinx.android.synthetic.main.load_activity.*
```

导入这一句之后，我们就可以直接在代码中使用start、textView，他们分别对应于main.xml中的id为start的按钮和id为textView的TextView。

于是你就发现你再也不用findViewById了，多么愉快的一件事！！！当然，你还会发现Toast的调用也变得简单了，那其实就是一个扩展方法toast()；而startActivity呢，其实就是一个inline加reified的应用——这我们前面都提到过了。

还有一个恶心的东西就是UI线程和非UI线程的切换问题。也许你会用handler不断的post，不过说真的，用Handler的时候难道你不颤抖么，那可是一个很容易内存泄露的魔鬼呀~哈哈，好吧其实我不是说这个，主要是用handler写出来的代码 实在 太 丑 了 ！！

原来在java当中，我们这么写：

```
handler.post(new Runnable(){    @Override    public void run(){        //todo      }});MainActivity.this.runOnUiThread(    public void run(){        //todo    }});
```

而在Kotlin当中呢，我们只需要这么写：

```
async() {    //do something asynchronously    uiThread {        //do something on UI thread    }}
```

自己感受一下吧。

下面我们再来提一个有意思的东西，我们从做Android开发一开始就要编写xml，印象中这个对于我来说真的是一件痛苦的事情，因为它的工作机制并不如代码那样直接（以至于我现在很多时候居然喜欢用Java代码直接写布局）——当然，最主要的问题并不是这个，而是解析xml需要耗费CPU。Kotlin有办法可以解决这个问题，那就是DSL了。下面给出一个例子：

```
linearLayout {    button("Login") {        textSize = 26f    }.lparams(width = wrapContent) {        horizontalMargin = dip(5)        topMargin = dip(10)    }}
```

一个LinearLayout包含了一个Button，这段代码你可以直接写到你的代码中灵活复用，就像这样：

```
    override fun onCreate(savedInstanceState: Bundle?) {        super.onCreate(savedInstanceState)        setContentView(linearLayout {            button("This is a button") {                onClick {                    toast("clicked!")                }            }.lparams {                width = matchParent                verticalMargin = dip(5)            }        })    }
```

这样做的好处真是不少：

- 比起xml的繁琐来，这真是要清爽很多
- 布局本身也是代码，可以灵活复用
- 再也不用findViewById了，难道你不觉得在这个上面浪费的生命已经足够多吗
- 事件监听很方便的嵌到布局当中
- DSL方式的布局没有运行时的解析的负担，你的逻辑代码怎么运行它就怎么运行
- Anko还增加了更多好玩的特性，有兴趣的可以参考：[Anko@Github](https://github.com/Kotlin/anko)

## 3.8 方法数之痛

我曾经尝试用Scala写了个Android的HelloWorld，一切都配置好以后，仅仅引入了Scala常见的几个库，加上support-v4以及appcompat这样常见的库，结果还是报错了。是的，65K。。。而且用Scala开发Android的话，基于gradle的构建会让整个app的build过程异常漫长，有时候你会觉得自己悟出了广义相对论的奥义，哦不，你一定是晕了，时间并没有变慢。

相比之下，Kotlin的标准库只有7000个方法，比support-v4还要小，这正反映了Kotlin的设计理念：100% interoperable with Java。其实我们之前就提到，Java有的Kotlin就直接拿来用，而Scala的标准库要有5W多个方法，想想就还是想想算了。

# 4. 小结

目前Kotlin 1.0已经release，尽管像0xffffffff识别成Long类型这样的bug仍然没有解[详情](https://youtrack.jetbrains.com/issue/KT-4749)：

```
val int: Int = 0xffffffff // errorval anotherInt: Int = 0xffffffff.toInt() // correct
```

不过，Kotlin的教学资源和社区建设也已经相对成熟，按照官方的说法，Kotlin可以作为生产工具投入开发，详情可以参考：[Kotlin 1.0 Released: Pragmatic Language for JVM and Android](http://blog.jetbrains.com/kotlin/2016/02/kotlin-1-0-released-pragmatic-language-for-jvm-and-android/)。

敢于吃螃蟹，多少有些浪漫主义色彩，我们这些程序员多少可以有些浪漫主义特质，不过在生成环境中，稳定高于一切仍然是不二法则。追求新技术，一方面会给团队带来开发和维护上的学习成本，另一方面也要承担未来某些情况下因为对新技术不熟悉而产生未知问题的风险——老板们最怕风险了~~

基于这一点，毫无疑问，Kotlin可以作为小工具、测试用例等的开发工具，这是考虑到这些代码通常体量较小，维护人数较少较集中，对项目整体的影响也较小；而对于核心代码，则视情况而定吧。

就我个人而言，长期下去，Kotlin很大可能会成为我的主要语言，短期内则仍然采用温和的改革方式慢慢将Kotlin渗透进来。

一句话，**Kotlin是用来提升效率的，如果在你的场景中它做不到，甚至成了拖累，请放开它**。