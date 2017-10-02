## MainKt

```kotlin
package cn.kotliner.coroutine.ui

import cn.kotliner.coroutine.async.DownloadContext
import cn.kotliner.coroutine.async.我要开始加载图片啦
import cn.kotliner.coroutine.async.我要开始协程啦
import cn.kotliner.coroutine.async.我要开始耗时操作了
import cn.kotliner.coroutine.common.log
import javax.swing.JFrame.EXIT_ON_CLOSE

const val LOGO_URL = "http://www.imooc.com/static/img/index/logo.png?t=1.1"

fun main(args: Array<String>) {
    val frame = MainWindow()
    frame.title = "Coroutine@Bennyhuo"
    frame.setSize(200, 150)
    frame.isResizable = true
    frame.defaultCloseOperation = EXIT_ON_CLOSE
    frame.init()
    frame.isVisible = true

    frame.onButtonClick {
        log("协程之前")
        我要开始协程啦(DownloadContext(LOGO_URL)) {
            log("协程开始")
            try {
                val imageData = 我要开始耗时操作了 {
                    我要开始加载图片啦(this[DownloadContext]!!.url)
                }
                log("拿到图片")
                frame.setLogo(imageData)
            } catch(e: Exception) {
                e.printStackTrace()
            }
        }
        log("协程之后")
    }

//    frame.onButtonClick {
//        HttpService.service.getLogo(LOGO_URL)
//                .enqueue(object : Callback<ResponseBody> {
//                    override fun onResponse(
//                            call: Call<ResponseBody>,
//                            response: Response<ResponseBody>) {
//                        if (response.isSuccessful) {
//                            val imageData = response.body()?.byteStream()?.readBytes()
//                            if (imageData == null) {
//                                throw HttpException(HttpError.HTTP_ERROR_NO_DATA)
//                            } else {
//                                SwingUtilities.invokeLater {
//                                    frame.setLogo(imageData)
//                                }
//                            }
//                        } else {
//                            throw HttpException(response.code())
//                        }
//                    }
//
//                    override fun onFailure(call: Call<ResponseBody>, t: Throwable) {
//                        throw HttpException(HttpError.HTTP_ERROR_UNKNOWN)
//                    }
//
//                })
//    }
}
```

## BaseContinuation

```kotlin
class BaseContinuation: Continuation<Unit> {
    override val context: CoroutineContext = EmptyCoroutineContext

    override fun resume(value: Unit) {

    }

    override fun resumeWithException(exception: Throwable) {

    }

}
```

## ContextContinuation.kt

```kotlin
class ContextContinuation(override val context: CoroutineContext = EmptyCoroutineContext): Continuation<Unit> {

    override fun resume(value: Unit) {

    }

    override fun resumeWithException(exception: Throwable) {

    }

}
```

## UiCotinuationWrapper

```kotlin
class UiCotinuationWrapper<T>(val continuation: Continuation<T>): Continuation<T>{
    override val context = continuation.context

    override fun resume(value: T) {
        SwingUtilities.invokeLater { continuation.resume(value) }
    }

    override fun resumeWithException(exception: Throwable) {
        SwingUtilities.invokeLater { continuation.resumeWithException(exception) }
    }

}
```

## AsyncTask.kt

```kotlin
private val pool by lazy {
    Executors.newCachedThreadPool()
}

class AsyncTask(val block: ()-> Unit){
    fun execute() = pool.execute(block)
}
```

## AsyncContext

```kotlin
class AsyncContext: AbstractCoroutineContextElement(ContinuationInterceptor), ContinuationInterceptor{

    override fun <T> interceptContinuation(continuation: Continuation<T>): Continuation<T> {
        return UiCotinuationWrapper(continuation.context.fold(continuation){
            continuation, element ->
            if(element != this && element is ContinuationInterceptor){
                element.interceptContinuation(continuation)
            }else continuation
        })
    }
}
```

## Coroutines.kt

```kotlin
fun 我要开始协程啦(context: CoroutineContext = EmptyCoroutineContext, block: suspend () -> Unit) {
    block.startCoroutine(ContextContinuation( context + AsyncContext()))
}

suspend fun <T> 我要开始耗时操作了(block: CoroutineContext.() -> T)
        = suspendCoroutine<T> {
    continuation ->
    log("异步任务开始前")
    AsyncTask {
        try {
            continuation.resume(block(continuation.context))
        } catch(e: Exception) {
            continuation.resumeWithException(e)
        }
    }.execute()
}

fun 我要开始加载图片啦(url: String): ByteArray {
    log("异步任务开始前")
    log("耗时操作，下载图片")
    val responseBody = HttpService.service.getLogo(url).execute()
    if (responseBody.isSuccessful) {
        responseBody.body()?.byteStream()?.readBytes()?.let {
            return it
        }
        throw HttpException(HttpError.HTTP_ERROR_NO_DATA)
    } else {
        throw HttpException(responseBody.code())
    }
}
```

