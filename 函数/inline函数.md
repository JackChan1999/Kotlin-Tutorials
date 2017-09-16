## inline函数

inline内联函数，最终编译时是要编译到调用它的代码块中，这时候T的类型实际上是确定的，因而Kotlin通过reified这个关键字告诉编译器，T这个参数可不只是个摆设，我要把它当实际类型来用呢。

```kotlin
inline fun <reified T: Any> Gson.fromJson(json: String): T{
    return fromJson(json, T::class.java)
}

interface Api {
    fun getSingerFromJson(json: String): BaseResult<Singer>
}

object ApiFactory {
    val api: Api by lazy {
        Proxy.newProxyInstance(ApiFactory.javaClass.classLoader, arrayOf(Api::class.java)) {
            proxy, method, args ->
            val responseType = method.genericReturnType
            val adapter = Gson().getAdapter(TypeToken.get(responseType))
            adapter.fromJson(args[0].toString())
        } as Api
    }
}

fun main(args: Array<String>) {
    val json = File("result_singer_field_loss.json").readText()
    val result : BaseResult<Singer> = ApiFactory.api.getSingerFromJson(json)
    println(result.content.name.isEmpty())
}
```

```kotlin
package com.benny.utils
import android.util.Log
inline fun <reified T> T.debug(log: Any){
	Log.d(T::class.simpleName, log.toString())
}

debug(whatever)
```

