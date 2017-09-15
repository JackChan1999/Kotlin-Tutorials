### Iterable

```kotlin
public interface Iterable<out T> {
    /**
     * Returns an iterator over the elements of this object.
     */
    public operator fun iterator(): Iterator<T>
}
```

### Iterator

- next()
- hasNext() 

```kotlin
public interface Iterator<out T> {
    /**
     * Returns the next element in the iteration.
     */
    public operator fun next(): T

    /**
     * Returns `true` if the iteration has more elements.
     */
    public operator fun hasNext(): Boolean
}
```
```kotlin
/**
 * Given an [iterator] function constructs an [Iterable] instance that returns values through the [Iterator]
 * provided by that function.
 */
@kotlin.internal.InlineOnly
public inline fun <T> Iterable(crossinline iterator: () -> Iterator<T>): Iterable<T> = object : Iterable<T> {
    override fun iterator(): Iterator<T> = iterator()
}
```

```kotlin
fun main(args: Array<String>) {

    var x = 5
    while(x > 0){
        println(x)
        x--
    }

    do{
        println(x)
        x--
    }while (x > 0)

//    for (arg in args){
//        println(arg)
//    }
//
//    for((index, value) in args.withIndex()){
//        println("$index -> $value")
//    }
//
//    for(indexedValue in args.withIndex()){
//        println("${indexedValue.index} -> ${indexedValue.value}")
//    }
//
//    val list = MyIntList()
//    list.add(1)
//    list.add(2)
//    list.add(3)
//
//    for(i in list){
//        println(i)
//    }
}

class MyIterator(val iterator: Iterator<Int>){
    operator fun next(): Int{
        return iterator.next()
    }

    operator fun hasNext(): Boolean{
        return iterator.hasNext()
    }
}

class MyIntList{
    private val list = ArrayList<Int>()

    fun add(int : Int){
        list.add(int)
    }

    fun remove(int: Int){
        list.remove(int)
    }

    operator fun iterator(): MyIterator{
        return MyIterator(list.iterator())
    }
}
```

