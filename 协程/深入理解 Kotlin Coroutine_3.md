# 深入理解 Kotlin coroutine (三)

前面有两篇文章介绍过协程，加上这篇，基本上介绍得差不多了。

- [深入理解 Kotlin coroutine (一)](深入理解 Kotlin Coroutine_1.md)
- [深入理解 Kotlin coroutine (二)](深入理解 Kotlin Coroutine_2.md)

上周在北京的活动上给大家分享了一下协程，发现大家对于协程最大的困惑是：我为什么要用它？

正好，前面一直想要再写篇协程的文章，这次让我们再来回顾一下其中的一些问题。

## 我们为什么要用协程？

这个问题对于新接触协程的朋友来说确实很容易让人困惑，那么我们就来看看协程给我们带来什么吧。

- **协程是一种语法糖** 协程的出现是来解决异步问题的，但它本身却不提供异步的能力，额这就很搞笑了，你来自于猴子与逗比吗？当然不是，协程某种意义上更像是一种语法糖，它为我们隐藏了异步调用和回调的细节，让我们更关注于业务逻辑的实现。

- **协程让代码更简洁** 协程可以允许我们用同步代码的方式写出异步代码的功能。

```kotlin
async{ 
    val bitmap = await{ loadImage(url) }
} 
```
这是一段 Android 的代码示例，请注意这个赋值操作，它实际上是切换到 UI 线程之后运行的，而 await 当中的 loadImage(url) 却是在 IO 线程中运行，所以我们一方面知道协程的异步功能是有线程在后面支持的，另一方面我们也知道异步线程回调可以用协程直接简化为一个简单的赋值。

- **协程让异步异常处理更方便** 如果你的异步代码出现异常，通常你会在你的回调中加入一个 onError 来传递这个异常信息：

```kotlin
interface Callback{ 
    fun onError(e: CertainException) 　 
    fun onSuccess(data: Data) 
} 

```

而在协程的支持下，我们只要按照同步代码的异常捕获方式进行捕获就可以了：

```kotlin
async{ 
    try{ 
        val bitmap = await{ loadImage(url) } 
    }catch(e: CertainException){ 
        ... 
    }
} 
```

- **协程更轻量级** 通常比较传统的服务器实现，一个用户请求接入后会给他开一个线程来等待和处理它的请求。这样是非常不经济的，因为这个用户有可能就是来逗你玩的，建立连接之后半天来一个字节，就这样你还得付出一个线程的代价来服务他，尴尬。如果用协程，那么就要轻量多了，因为协程只是一块儿内存，不像线程那样还要对应操作系统内核线程（印象中 Hotpot 的实现就是这样）。

用协程的理由我个人感觉也就这些了。一句话，协程是一种轻量级的方便操作异步代码的语法糖，而它本身不提供异步能力。

## 为什么说它是语法糖？

```kotlin
async{ 
   val bitmap = await{ loadImage(url) } 
} 
```

这样一句代码其实在编译完之后会生成一些新的类，async 后面的 Lambda 就会被编译成一个 CoroutineImpl 类的子类实例，大家只需要按照我经常提到的查看 Kotlin 字节码的方法去看看就知道了。

那么这个类究竟是个什么呢？

```kotlin
abstract class CoroutineImpl( 
        arity: Int, 
        @JvmField 
        protected var completion: Continuation<Any?>? 
) : Lambda(arity), Continuation<Any?> { 
　 
    @JvmField 
    protected var label: Int = if (completion != null) 0 else -1 
　 
    private val _context: CoroutineContext? = completion?.context 
　 
    override val context: CoroutineContext 
        get() = _context!! 
　 
    private var _facade: Continuation<Any?>? = null 
　 
    val facade: Continuation<Any?> get() { 
        if (_facade == null) _facade = interceptContinuationIfNeeded(_context!!, this) 
        return _facade!! 
    } 
　 
    override fun resume(value: Any?) { 
        processBareContinuationResume(completion!!) { 
            doResume(value, null) 
        } 
    } 
　 
    override fun resumeWithException(exception: Throwable) { 
        processBareContinuationResume(completion!!) { 
            doResume(null, exception) 
        } 
    } 
　 
    protected abstract fun doResume(data: Any?, exception: Throwable?): Any? 
　 
    open fun create(completion: Continuation<*>): Continuation<Unit> { 
        throw IllegalStateException("create(Continuation) has not been overridden") 
    } 
　 
    open fun create(value: Any?, completion: Continuation<*>): Continuation<Unit> { 
        throw IllegalStateException("create(Any?;Continuation) has not been overridden") 
    } 
} 
```

它首先是个 Lambda ，这没毛病。其次，它是一个 Continuation，了解协程的朋友似乎要知道什么了，没错，这货与我们自己在启动协程时传入的 Continuation 实例是同样的东西，而且我们可以注意到构造方法当中有一个叫做 completion 的字段，不要惊讶，那就是我们传入的 Continuation。

实际上，我们通过编译器编译出来的字节码发现，create 方法当中会通过我们的这个 Lambda 表达式创建一个新的 CoroutineImpl 实例，而 doResume 这个抽象方法其实就是我们的 Lambda 表达式的内容了。

在这里我们还看到了 facade：

```kotlin
val facade: Continuation<Any?> get() { 
    if (_facade == null) _facade = interceptContinuationIfNeeded(_context!!, this) 
    return _facade!! 
} 
```

其中 `interceptContinuationIfNeeded` 当中就会处理各个拦截器，来完成线程调度或者其他操作，也就是说 facade 返回的 Continuation 实例就是经过类似下面这样的拦截器返回的实例了：

```kotlin
class UIContext : 
AbstractCoroutineContextElement(ContinuationInterceptor), 
ContinuationInterceptor { 
    override fun <T> interceptContinuation(continuation: Continuation<T>) 
            = ... 
} 
```

## 协程是如何启动的？

我们再来看看协程是如何启动的。我们启动协程的时候通常会调用 startCoroutine 或者 createCoroutine，它们都会调用到一个方法：

```kotlin
public fun <T> (suspend () -> T).createCoroutineUnchecked( 
        completion: Continuation<T> 
): Continuation<Unit> = 
        if (this !is CoroutineImpl) 
           ... 
        else 
            (this.create(completion) as CoroutineImpl).facade 
```

我们已经知道我们的调用者实际上就是一个 CoroutineImpl 的实例，我们只需要关注 else 分支即可，这时候调用我们前面已经见到的 create 方法再创建一个 CoroutineImpl 实例，并把 completion 这个对象传给它，而 facade 实际上就会触发拦截器的操作，最终返回的就是经过拦截器处理之后的 Continuation 实例了。

创建完协程之后，就是启动的逻辑：

```kotlin
public fun <T> (suspend  () -> T).startCoroutine( 
        completion: Continuation<T> 
) { 
    createCoroutineUnchecked(completion).resume(Unit) 
} 
```

直接调用 `resume` 方法，结果怎样呢？由于拦截器都是我们自己提供的，比较直观，我们暂且不提，通常情况下，这个 resume 方法最终本质上调用的还是 CoroutineImpl 的 `resume` 方法：

```kotlin
override fun resume(value: Any?) { 
    processBareContinuationResume(completion!!) { 
        doResume(value, null) 
    } 
} 
```

```kotlin
internal inline fun processBareContinuationResume(completion: Continuation<*>, block: () -> Any?) { 
    try { 
        val result = block() 
        if (result !== COROUTINE_SUSPENDED) { 
            (completion as Continuation<Any?>).resume(result) 
        } 
    } catch (t: Throwable) { 
        completion.resumeWithException(t) 
    } 
} 
```

`processBareContinuationResume` 会首先触发一次 doResume 的调用，这个调用也就是我们自己的协程代码了，直到遇到第一个 suspend 调用，那么这时候协程就会被挂起，等待异步操作执行完成之后再来调用我们的 resume/resumeWithException 方法来通知我们数据回来了或者异常发生了。这个过程直到整个协程执行流程结束。

我们稍微关注一下 Continuation 接口：

```kotlin
public interface Continuation<in T> { 
    public val context: CoroutineContext 
　 
    public fun resume(value: T) 
　 
    public fun resumeWithException(exception: Throwable) 
} 
```

再来看看我们通常的回调版本：

```kotlin
interface Callback<T>{ 
   fun onError(e: Exception) 
　 
   fun onSuccess(data: T) 
} 
```

除了协程上下文之外，剩下的两个方法与我们的回调又有什么区别呢？

## 小结

协程是什么？它就是用来简化你的异步回调代码的语法糖！