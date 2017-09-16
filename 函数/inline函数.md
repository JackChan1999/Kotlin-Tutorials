## inline函数

reified

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

