- ? 可空
- A ?: B ==> A非空则返回A，否则B
- !! 确认非空

```kotlin
fun getName(): String?{
    return null
}

fun main(args: Array<String>) {
//    val name: String = getName() ?: return
//    println(name.length)

    val value: String? = "HelloWorld"
    println(value!!.length)
}
```
