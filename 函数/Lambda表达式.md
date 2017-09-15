## Lambda表达式

函数式接口

函数式编程

匿名函数

Lambda表达式的类型

用()调用Lambda表达式，等价于invoke()方法

- 参数只有一个的时候可省略，它的名字就是it
- 如果最后一个参数是Lambda表达式，可以把{}移到()的外面
- 当()中什么也没有的时候，()是可选的
- 传入的函数和Lambda表达式类型相同，可以使用函数引用::简化

```kotlin
args.forEach{println(it)}
args.forEach({println(it)})
args.forEach({it -> println(it)})
args.forEach(::println)
```

