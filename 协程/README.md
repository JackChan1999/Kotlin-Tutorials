## 协程

* [轻量级线程：协程1](轻量级线程：协程1.md)
* [轻量级线程：协程2](轻量级线程：协程2.md)
* [深入理解 Kotlin Coroutine_1](深入理解 Kotlin Coroutine.md)
* [深入理解 Kotlin Coroutine_2](深入理解 Kotlin coroutine_2.md)

协程提供了一种避免阻塞线程并用更廉价、更可控的操作替代线程阻塞的方法：协程挂起。

Coroutine是编译器级的，Process和Thread是操作系统级的。

@RestrictsSuspension 注解

buildSequence()

kotlinx.coroutines 框架

----

run()

produce()

| 方法声明                | 功能描述                      |
| ------------------- | ------------------------- |
| Job launch()        | 启动协程                      |
| Deferred async()    | 异步，可取消                    |
| runBlocking()       | 启动一个协程，由它创建的协程会直接运行在当前线程上 |
| delay()             | 非阻塞的sleep                 |
| repeat()            |                           |
| withTimeout()       |                           |
| yield()             |                           |
| measureTimeMillis() | 计算执行时间                    |
|                     |                           |

### CoroutineStart

协程启动选项

- DEFAULT
- LAZY

### 挂起函数

挂起 suspend，非阻塞

挂起函数suspend，挂起函数不能在普通函数中调用，必须在协程上下文中调用

### CoroutineContext

协程上下文，就是代码块执行在哪个场景。

- CoroutineContext.Key
- AbstractCoroutineContextElement
- EmptyCoroutineContext

### CoroutineScope

- isActive
- context

### CoroutineDispatcher

调度器

- CommonPool 共享线程池（一个有线程池的上下文）
- HandlerContext
- Unconfined
- dispatch(context: CoroutineContext, block: Runnable)

### Job

Job launch()

Deferred async()

| 方法声明        | 功能描述 |
| ----------- | ---- |
| isActive    |      |
| isCompleted |      |
| cancel()    |      |
| join()      |      |

Deferred(Job的子接口)，携带返回值

- DeferredCoroutine
- LazyDeferredCoroutine

| 方法声明    | 功能描述 |
| ------- | ---- |
| await() | 获取结果 |

### Continuation

挂起的协程可以作为保持其挂起状态与局部变量的对象来存储和传递。这种对象的类型是 `Continuation`

| 方法声明                  | 功能描述                |
| --------------------- | ------------------- |
| context               | CoroutineContext上下文 |
| resume()              |                     |
| resumeWithException() |                     |

### ContinuationInterceptor

拦截器，可以把协程的操作拦截

- interceptContinuation()

### 协程的基本操作

包级函数

| 方法声明                         | 功能描述 |
| ---------------------------- | ---- |
| createCoroutine()            | 创建协程 |
| startCoroutine(Continuation) | 开始协程 |
| suspendCoroutine()           | 挂起协程 |

```kotlin
public inline suspend fun <T> suspendCoroutine(crossinline block: (Continuation<T>) -> Unit): T =
        suspendCoroutineOrReturn { c: Continuation<T> ->
            val safe = SafeContinuation(c)
            block(safe)
            safe.getResult()
        }
```
### Channel

- SendChannel
- ReceiveChannel
- Channel

| 方法声明               | 功能描述 |
| ------------------ | ---- |
| isClosedForSend    |      |
| isClosedForReceive |      |
| send()             |      |
| receive()          |      |
| close()            | 关闭通道 |

### 生成器

| 方法声明                     | 功能描述 |
| ------------------------ | ---- |
| Sequence buildSequence() |      |
| buildIterator()          |      |
| yield                    |      |
| yieldAll                 |      |

### 状态机

对具有逻辑顺序或时序规律事件的一种描述方法。

- 有限状态机
- 两种状态机：Moore型和Mealy型

状态机的主要用途
