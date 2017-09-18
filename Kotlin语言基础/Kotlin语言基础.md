# Kotlin语言基础

学习任何东西，都是一个由表及里的过程。学习一门编程语言也一样。对于一门编程语言来说，“表” 就是基本词汇（关键字、标识符等）、句子（表达式）和语法。

每一门编程语言的学习内容都会涉及： 运行环境、基础数据类型（数字、字符串、数组、集合、映射字典等） 、表达式、 流程控制 、类、方法（函数）
等等，不同的语言会借鉴其他的语言特性，同时也会有各自的特性。这样我们就可以通过对比学习来加深理解。另外，我们还通过大量实践深入理解，达到熟练使用。

所谓“纸上得来终觉浅，绝知此事要躬行”是也。下面让我们开始吧。

## 3.6 代码注释

正如 Java 和 JavaScript，Kotlin 支持行注释及块注释。

```
// 这是一个行注释

/* 这是一个多行的
   块注释。 */
```

与 Java 不同的是，Kotlin 的块注释可以嵌套。就是说，你可以这样注释：

```
/**
 * hhhh
 * /**
 *  fff
 *  /**
 *    ggggg
 *  */
 * */
 *
 * abc
 *
 */
fun main(args:Array<String>){
    val f = Functions()
    println(f.fvoid1())
    println(f.fvoid2())
    println(f.sum1(1,1))
    println(f.sum2(1,1))
}

```

## 3.7 语法与标识符

我们知道，任何一门编程语言都会有一些自己专用的关键字、符号以及规定的语法规则等等。程序员们使用这些基础词汇和语法规则来表达算法步骤，也就是写代码的过程。

词法分析是编译器对源码进行编译的基础步骤之一。词法分析是将源程序读入的字符序列，按照一定的规则转换成词法单元（Token）序列的过程。词法单元是语言中具有独立意义的最小单元，包括修饰符、关键字、常数、运算符、边界符等等。

## 3.7.1 修饰符

在Kotlin源码工程中的kotlin/grammar/src/modifiers.grm文件中，描述了Kotlin语言的修饰符，我们在此作简要注释说明：

```
/**
## Modifiers
*/

modifiers
  : (modifier | annotations)*
  ;

typeModifiers
  : (suspendModifier | annotations)*
  ;

modifier
  : classModifier
  : accessModifier
  : varianceAnnotation
  : memberModifier
  : parameterModifier
  : typeParameterModifier
  : functionModifier
  : propertyModifier
  ;

classModifier 类修饰符
  : "abstract" 抽象类
  : "final" 不可被继承final类
  : "enum" 枚举类
  : "open" 可继承open类
  : "annotation" 注解类
  : "sealed" 密封类
  : "data" 数据类
  ;

memberModifier
  : "override" 重写函数
  : "open" 可被重写
  : "final" 不可被重写
  : "abstract" 抽象函数
  : "lateinit" 后期初始化
  ;

accessModifier 访问权限控制, 默认是public
  : "private"
  : "protected"
  : "public"
  : "internal"  整个模块内（模块(module)是指一起编译的一组 Kotlin 源代码文件: 例如，一个 IntelliJ IDEA 模块，一个 Maven 工程, 或 Gradle 工程，通过 Ant 任务的一次调用编译的一组文件等）可访问
  ;

varianceAnnotation 泛型可变性
  : "in" 
  : "out"
  ;

parameterModifier
  : "noinline" 
  : "crossinline" 
  : "vararg" 变长参数
  ;

typeParameterModifier
  : "reified"
  ;

functionModifier
  : "tailrec" 尾递归
  : "operator"
  : "infix"
  : "inline"
  : "external"
  : suspendModifier
  ;

propertyModifier
  : "const" 
  ;

suspendModifier
  : "suspend"
  ;

```

这些修饰符的完整定义，在kotlin/compiler/frontend/src/org/jetbrains/kotlin/lexer/KtTokens.java源码中：

```
KtModifierKeywordToken[] MODIFIER_KEYWORDS_ARRAY =
            new KtModifierKeywordToken[] {
                    ABSTRACT_KEYWORD, ENUM_KEYWORD, OPEN_KEYWORD, INNER_KEYWORD, OVERRIDE_KEYWORD, PRIVATE_KEYWORD,
                    PUBLIC_KEYWORD, INTERNAL_KEYWORD, PROTECTED_KEYWORD, OUT_KEYWORD, IN_KEYWORD, FINAL_KEYWORD, VARARG_KEYWORD,
                    REIFIED_KEYWORD, COMPANION_KEYWORD, SEALED_KEYWORD, LATEINIT_KEYWORD,
                    DATA_KEYWORD, INLINE_KEYWORD, NOINLINE_KEYWORD, TAILREC_KEYWORD, EXTERNAL_KEYWORD, ANNOTATION_KEYWORD, CROSSINLINE_KEYWORD,
                    CONST_KEYWORD, OPERATOR_KEYWORD, INFIX_KEYWORD, SUSPEND_KEYWORD, HEADER_KEYWORD, IMPL_KEYWORD
            };

    TokenSet MODIFIER_KEYWORDS = TokenSet.create(MODIFIER_KEYWORDS_ARRAY);

    TokenSet TYPE_MODIFIER_KEYWORDS = TokenSet.create(SUSPEND_KEYWORD);
    TokenSet TYPE_ARGUMENT_MODIFIER_KEYWORDS = TokenSet.create(IN_KEYWORD, OUT_KEYWORD);
    TokenSet RESERVED_VALUE_PARAMETER_MODIFIER_KEYWORDS = TokenSet.create(OUT_KEYWORD, VARARG_KEYWORD);

    TokenSet VISIBILITY_MODIFIERS = TokenSet.create(PRIVATE_KEYWORD, PUBLIC_KEYWORD, INTERNAL_KEYWORD, PROTECTED_KEYWORD);
```

## 3.7.2 关键字(保留字)

```
TokenSet KEYWORDS = TokenSet.create(PACKAGE_KEYWORD, AS_KEYWORD, TYPE_ALIAS_KEYWORD, CLASS_KEYWORD, INTERFACE_KEYWORD,
                                        THIS_KEYWORD, SUPER_KEYWORD, VAL_KEYWORD, VAR_KEYWORD, FUN_KEYWORD, FOR_KEYWORD,
                                        NULL_KEYWORD,
                                        TRUE_KEYWORD, FALSE_KEYWORD, IS_KEYWORD,
                                        IN_KEYWORD, THROW_KEYWORD, RETURN_KEYWORD, BREAK_KEYWORD, CONTINUE_KEYWORD, OBJECT_KEYWORD, IF_KEYWORD,
                                        ELSE_KEYWORD, WHILE_KEYWORD, DO_KEYWORD, TRY_KEYWORD, WHEN_KEYWORD,
                                        NOT_IN, NOT_IS, AS_SAFE,
                                        TYPEOF_KEYWORD
    );

    TokenSet SOFT_KEYWORDS = TokenSet.create(FILE_KEYWORD, IMPORT_KEYWORD, WHERE_KEYWORD, BY_KEYWORD, GET_KEYWORD,
                                             SET_KEYWORD, ABSTRACT_KEYWORD, ENUM_KEYWORD, OPEN_KEYWORD, INNER_KEYWORD,
                                             OVERRIDE_KEYWORD, PRIVATE_KEYWORD, PUBLIC_KEYWORD, INTERNAL_KEYWORD, PROTECTED_KEYWORD,
                                             CATCH_KEYWORD, FINALLY_KEYWORD, OUT_KEYWORD, FINAL_KEYWORD, VARARG_KEYWORD, REIFIED_KEYWORD,
                                             DYNAMIC_KEYWORD, COMPANION_KEYWORD, CONSTRUCTOR_KEYWORD, INIT_KEYWORD, SEALED_KEYWORD,
                                             FIELD_KEYWORD, PROPERTY_KEYWORD, RECEIVER_KEYWORD, PARAM_KEYWORD, SETPARAM_KEYWORD,
                                             DELEGATE_KEYWORD,
                                             LATEINIT_KEYWORD,
                                             DATA_KEYWORD, INLINE_KEYWORD, NOINLINE_KEYWORD, TAILREC_KEYWORD, EXTERNAL_KEYWORD,
                                             ANNOTATION_KEYWORD, CROSSINLINE_KEYWORD, CONST_KEYWORD, OPERATOR_KEYWORD, INFIX_KEYWORD,
                                             SUSPEND_KEYWORD, HEADER_KEYWORD, IMPL_KEYWORD
    );
```

其中，对应的关键字如下：

```
    KtKeywordToken PACKAGE_KEYWORD          = KtKeywordToken.keyword("package");
    KtKeywordToken AS_KEYWORD               = KtKeywordToken.keyword("as");
    KtKeywordToken TYPE_ALIAS_KEYWORD       = KtKeywordToken.keyword("typealias");
    KtKeywordToken CLASS_KEYWORD            = KtKeywordToken.keyword("class");
    KtKeywordToken THIS_KEYWORD             = KtKeywordToken.keyword("this");
    KtKeywordToken SUPER_KEYWORD            = KtKeywordToken.keyword("super");
    KtKeywordToken VAL_KEYWORD              = KtKeywordToken.keyword("val");
    KtKeywordToken VAR_KEYWORD              = KtKeywordToken.keyword("var");
    KtKeywordToken FUN_KEYWORD              = KtKeywordToken.keyword("fun");
    KtKeywordToken FOR_KEYWORD              = KtKeywordToken.keyword("for");
    KtKeywordToken NULL_KEYWORD             = KtKeywordToken.keyword("null");
    KtKeywordToken TRUE_KEYWORD             = KtKeywordToken.keyword("true");
    KtKeywordToken FALSE_KEYWORD            = KtKeywordToken.keyword("false");
    KtKeywordToken IS_KEYWORD               = KtKeywordToken.keyword("is");
    KtModifierKeywordToken IN_KEYWORD       = KtModifierKeywordToken.keywordModifier("in");
    KtKeywordToken THROW_KEYWORD            = KtKeywordToken.keyword("throw");
    KtKeywordToken RETURN_KEYWORD           = KtKeywordToken.keyword("return");
    KtKeywordToken BREAK_KEYWORD            = KtKeywordToken.keyword("break");
    KtKeywordToken CONTINUE_KEYWORD         = KtKeywordToken.keyword("continue");
    KtKeywordToken OBJECT_KEYWORD           = KtKeywordToken.keyword("object");
    KtKeywordToken IF_KEYWORD               = KtKeywordToken.keyword("if");
    KtKeywordToken TRY_KEYWORD              = KtKeywordToken.keyword("try");
    KtKeywordToken ELSE_KEYWORD             = KtKeywordToken.keyword("else");
    KtKeywordToken WHILE_KEYWORD            = KtKeywordToken.keyword("while");
    KtKeywordToken DO_KEYWORD               = KtKeywordToken.keyword("do");
    KtKeywordToken WHEN_KEYWORD             = KtKeywordToken.keyword("when");
    KtKeywordToken INTERFACE_KEYWORD        = KtKeywordToken.keyword("interface");

    // Reserved for future use:
    KtKeywordToken TYPEOF_KEYWORD           = KtKeywordToken.keyword("typeof");
...
    KtKeywordToken FILE_KEYWORD    = KtKeywordToken.softKeyword("file");
    KtKeywordToken FIELD_KEYWORD     = KtKeywordToken.softKeyword("field");
    KtKeywordToken PROPERTY_KEYWORD     = KtKeywordToken.softKeyword("property");
    KtKeywordToken RECEIVER_KEYWORD     = KtKeywordToken.softKeyword("receiver");
    KtKeywordToken PARAM_KEYWORD     = KtKeywordToken.softKeyword("param");
    KtKeywordToken SETPARAM_KEYWORD  = KtKeywordToken.softKeyword("setparam");
    KtKeywordToken DELEGATE_KEYWORD  = KtKeywordToken.softKeyword("delegate");
    KtKeywordToken IMPORT_KEYWORD    = KtKeywordToken.softKeyword("import");
    KtKeywordToken WHERE_KEYWORD     = KtKeywordToken.softKeyword("where");
    KtKeywordToken BY_KEYWORD        = KtKeywordToken.softKeyword("by");
    KtKeywordToken GET_KEYWORD       = KtKeywordToken.softKeyword("get");
    KtKeywordToken SET_KEYWORD       = KtKeywordToken.softKeyword("set");
    KtKeywordToken CONSTRUCTOR_KEYWORD = KtKeywordToken.softKeyword("constructor");
    KtKeywordToken INIT_KEYWORD        = KtKeywordToken.softKeyword("init");

    KtModifierKeywordToken ABSTRACT_KEYWORD  = KtModifierKeywordToken.softKeywordModifier("abstract");
    KtModifierKeywordToken ENUM_KEYWORD      = KtModifierKeywordToken.softKeywordModifier("enum");
    KtModifierKeywordToken OPEN_KEYWORD      = KtModifierKeywordToken.softKeywordModifier("open");
    KtModifierKeywordToken INNER_KEYWORD     = KtModifierKeywordToken.softKeywordModifier("inner");
    KtModifierKeywordToken OVERRIDE_KEYWORD  = KtModifierKeywordToken.softKeywordModifier("override");
    KtModifierKeywordToken PRIVATE_KEYWORD   = KtModifierKeywordToken.softKeywordModifier("private");
    KtModifierKeywordToken PUBLIC_KEYWORD    = KtModifierKeywordToken.softKeywordModifier("public");
    KtModifierKeywordToken INTERNAL_KEYWORD  = KtModifierKeywordToken.softKeywordModifier("internal");
    KtModifierKeywordToken PROTECTED_KEYWORD = KtModifierKeywordToken.softKeywordModifier("protected");
    KtKeywordToken CATCH_KEYWORD     = KtKeywordToken.softKeyword("catch");
    KtModifierKeywordToken OUT_KEYWORD       = KtModifierKeywordToken.softKeywordModifier("out");
    KtModifierKeywordToken VARARG_KEYWORD    = KtModifierKeywordToken.softKeywordModifier("vararg");
    KtModifierKeywordToken REIFIED_KEYWORD   = KtModifierKeywordToken.softKeywordModifier("reified");
    KtKeywordToken DYNAMIC_KEYWORD   = KtKeywordToken.softKeyword("dynamic");
    KtModifierKeywordToken COMPANION_KEYWORD = KtModifierKeywordToken.softKeywordModifier("companion");
    KtModifierKeywordToken SEALED_KEYWORD    = KtModifierKeywordToken.softKeywordModifier("sealed");

    KtModifierKeywordToken DEFAULT_VISIBILITY_KEYWORD = PUBLIC_KEYWORD;

    KtKeywordToken FINALLY_KEYWORD   = KtKeywordToken.softKeyword("finally");
    KtModifierKeywordToken FINAL_KEYWORD     = KtModifierKeywordToken.softKeywordModifier("final");

    KtModifierKeywordToken LATEINIT_KEYWORD = KtModifierKeywordToken.softKeywordModifier("lateinit");

    KtModifierKeywordToken DATA_KEYWORD    = KtModifierKeywordToken.softKeywordModifier("data");
    KtModifierKeywordToken INLINE_KEYWORD    = KtModifierKeywordToken.softKeywordModifier("inline");
    KtModifierKeywordToken NOINLINE_KEYWORD    = KtModifierKeywordToken.softKeywordModifier("noinline");
    KtModifierKeywordToken TAILREC_KEYWORD    = KtModifierKeywordToken.softKeywordModifier("tailrec");
    KtModifierKeywordToken EXTERNAL_KEYWORD    = KtModifierKeywordToken.softKeywordModifier("external");
    KtModifierKeywordToken ANNOTATION_KEYWORD    = KtModifierKeywordToken.softKeywordModifier("annotation");
    KtModifierKeywordToken CROSSINLINE_KEYWORD    = KtModifierKeywordToken.softKeywordModifier("crossinline");
    KtModifierKeywordToken OPERATOR_KEYWORD = KtModifierKeywordToken.softKeywordModifier("operator");
    KtModifierKeywordToken INFIX_KEYWORD = KtModifierKeywordToken.softKeywordModifier("infix");

    KtModifierKeywordToken CONST_KEYWORD = KtModifierKeywordToken.softKeywordModifier("const");

    KtModifierKeywordToken SUSPEND_KEYWORD = KtModifierKeywordToken.softKeywordModifier("suspend");

    KtModifierKeywordToken HEADER_KEYWORD = KtModifierKeywordToken.softKeywordModifier("header");
    KtModifierKeywordToken IMPL_KEYWORD = KtModifierKeywordToken.softKeywordModifier("impl");
```

#### `this` 关键字

`this`关键字持有当前对象的引用。我们可以使用`this`来引用变量或者成员函数，也可以使用`return this`，来返回某个类的引用。

代码示例

```
class ThisDemo {
    val thisis = "THIS IS"

    fun whatIsThis(): ThisDemo {
        println(this.thisis) //引用变量
        this.howIsThis()// 引用成员函数
        return this // 返回此类的引用
    }

    fun howIsThis(){
        println("HOW IS THIS ?")
    }
}

```

测试代码

```
    @Test
    fun testThisDemo(){
        val demo = ThisDemo()
        println(demo.whatIsThis())
    }
```

输出

```
THIS IS
HOW IS THIS ?
com.easy.kotlin.ThisDemo@475232fc
```

在类的成员中，this 指向的是该类的当前对象。

在扩展函数或者带接收者的函数字面值中， this 表示在点左侧传递的 接收者参数。

代码示例：

```
>>> val sum = fun Int.(x:Int):Int = this + x
>>> sum
kotlin.Int.(kotlin.Int) -> kotlin.Int
>>> 1.sum(1)
2
>>> val concat = fun String.(x:Any) = this + x
>>> "abc".concat(123)
abc123
>>> "abc".concat(true)
abctrue

```

如果 this 没有限定符，它指的是最内层的包含它的作用域。如果我们想要引用其他作用域中的 this，可以使用 this@label 标签。

代码示例：

```
class Outer {
    val oh = "Oh!"

    inner class Inner {

        fun m() {
            val outer = this@Outer
            val inner = this@Inner
            val pthis = this
            println("outer=" + outer)
            println("inner=" + inner)
            println("pthis=" + pthis)
            println(this@Outer.oh)

            val fun1 = hello@ fun String.() {
                val d1 = this // fun1 的接收者
                println("d1" + d1)
            }

            val fun2 = { s: String ->
                val d2 = this
                println("d2=" + d2)
            }

            "abc".fun1()

            fun2

        }
    }


}
```

测试代码：

```
    @Test
    fun testThisKeyWord() {
        val outer = Outer()
        outer.Inner().m()
    }

```

输出

```
outer=com.easy.kotlin.Outer@5114e183
inner=com.easy.kotlin.Outer$Inner@5aa8ac7f
pthis=com.easy.kotlin.Outer$Inner@5aa8ac7f
Oh!
d1abc
```

#### super 关键字

super关键字持有指向其父类的引用。

代码示例：

```
open class Father {
    open val firstName = "Chen"
    open val lastName = "Jason"

    fun ff() {
        println("FFF")
    }
}

class Son : Father {
    override var firstName = super.firstName
    override var lastName = "Jack"

    constructor(lastName: String) {
        this.lastName = lastName
    }

    fun love() {
        super.ff() // 调用父类方法
        println(super.firstName + " " + super.lastName + " Love " + this.firstName + " " + this.lastName)
    }
}
```

测试代码

```
    @Test
    fun testSuperKeyWord() {
        val son = Son("Harry")
        son.love()
    }
```

输出

```
FFF
Chen Jason Love Chen Harry
```

### 3.7.3 操作符和操作符的重载

Kotlin 允许我们为自己的类型提供预定义的一组操作符的实现。这些操作符具有固定的符号表示（如 `+` 或 `*`）和固定的优先级。这些操作符的符号定义如下：

```
    KtSingleValueToken LBRACKET    = new KtSingleValueToken("LBRACKET", "[");
    KtSingleValueToken RBRACKET    = new KtSingleValueToken("RBRACKET", "]");
    KtSingleValueToken LBRACE      = new KtSingleValueToken("LBRACE", "{");
    KtSingleValueToken RBRACE      = new KtSingleValueToken("RBRACE", "}");
    KtSingleValueToken LPAR        = new KtSingleValueToken("LPAR", "(");
    KtSingleValueToken RPAR        = new KtSingleValueToken("RPAR", ")");
    KtSingleValueToken DOT         = new KtSingleValueToken("DOT", ".");
    KtSingleValueToken PLUSPLUS    = new KtSingleValueToken("PLUSPLUS", "++");
    KtSingleValueToken MINUSMINUS  = new KtSingleValueToken("MINUSMINUS", "--");
    KtSingleValueToken MUL         = new KtSingleValueToken("MUL", "*");
    KtSingleValueToken PLUS        = new KtSingleValueToken("PLUS", "+");
    KtSingleValueToken MINUS       = new KtSingleValueToken("MINUS", "-");
    KtSingleValueToken EXCL        = new KtSingleValueToken("EXCL", "!");
    KtSingleValueToken DIV         = new KtSingleValueToken("DIV", "/");
    KtSingleValueToken PERC        = new KtSingleValueToken("PERC", "%");
    KtSingleValueToken LT          = new KtSingleValueToken("LT", "<");
    KtSingleValueToken GT          = new KtSingleValueToken("GT", ">");
    KtSingleValueToken LTEQ        = new KtSingleValueToken("LTEQ", "<=");
    KtSingleValueToken GTEQ        = new KtSingleValueToken("GTEQ", ">=");
    KtSingleValueToken EQEQEQ      = new KtSingleValueToken("EQEQEQ", "===");
    KtSingleValueToken ARROW       = new KtSingleValueToken("ARROW", "->");
    KtSingleValueToken DOUBLE_ARROW       = new KtSingleValueToken("DOUBLE_ARROW", "=>");
    KtSingleValueToken EXCLEQEQEQ  = new KtSingleValueToken("EXCLEQEQEQ", "!==");
    KtSingleValueToken EQEQ        = new KtSingleValueToken("EQEQ", "==");
    KtSingleValueToken EXCLEQ      = new KtSingleValueToken("EXCLEQ", "!=");
    KtSingleValueToken EXCLEXCL    = new KtSingleValueToken("EXCLEXCL", "!!");
    KtSingleValueToken ANDAND      = new KtSingleValueToken("ANDAND", "&&");
    KtSingleValueToken OROR        = new KtSingleValueToken("OROR", "||");
    KtSingleValueToken SAFE_ACCESS = new KtSingleValueToken("SAFE_ACCESS", "?.");
    KtSingleValueToken ELVIS       = new KtSingleValueToken("ELVIS", "?:");
    KtSingleValueToken QUEST       = new KtSingleValueToken("QUEST", "?");
    KtSingleValueToken COLONCOLON  = new KtSingleValueToken("COLONCOLON", "::");
    KtSingleValueToken COLON       = new KtSingleValueToken("COLON", ":");
    KtSingleValueToken SEMICOLON   = new KtSingleValueToken("SEMICOLON", ";");
    KtSingleValueToken DOUBLE_SEMICOLON   = new KtSingleValueToken("DOUBLE_SEMICOLON", ";;");
    KtSingleValueToken RANGE       = new KtSingleValueToken("RANGE", "..");
    KtSingleValueToken EQ          = new KtSingleValueToken("EQ", "=");
    KtSingleValueToken MULTEQ      = new KtSingleValueToken("MULTEQ", "*=");
    KtSingleValueToken DIVEQ       = new KtSingleValueToken("DIVEQ", "/=");
    KtSingleValueToken PERCEQ      = new KtSingleValueToken("PERCEQ", "%=");
    KtSingleValueToken PLUSEQ      = new KtSingleValueToken("PLUSEQ", "+=");
    KtSingleValueToken MINUSEQ     = new KtSingleValueToken("MINUSEQ", "-=");
    KtKeywordToken NOT_IN      = KtKeywordToken.keyword("NOT_IN", "!in");
    KtKeywordToken NOT_IS      = KtKeywordToken.keyword("NOT_IS", "!is");
    KtSingleValueToken HASH        = new KtSingleValueToken("HASH", "#");
    KtSingleValueToken AT          = new KtSingleValueToken("AT", "@");

    KtSingleValueToken COMMA       = new KtSingleValueToken("COMMA", ",");
```

### 3.7.4 操作符优先级（Precedence)

| 优先级  | 标题                                       | 符号                                       |
| ---- | ---------------------------------------- | ---------------------------------------- |
| 最高   | 后缀（Postfix ）                             | `++`, `--`, `.`, `?.`, `?`               |
|      | 前缀（Prefix）                               | `-`, `+`, `++`, `--`, `!`, [`labelDefinition`](https://segmentfault.com/a/1190000009734338#IDENTIFIER)`@` |
|      | 右手类型运算（Type RHS，right-hand side class type (RHS) ） | `:`, `as`, `as?`                         |
|      | 乘除取余（Multiplicative）                     | `*`, `/`, `%`                            |
|      | 加减（Additive ）                            | `+`, `-`                                 |
|      | 区间范围（Range）                              | `..`                                     |
|      | Infix函数                                  | 例如，给` Int `定义扩展 `infix fun Int.shl(x: Int): Int {...}`,这样调用 `1 shl 2`，等同于`1.shl(2)` |
|      | Elvis操作符                                 | `?:`                                     |
|      | 命名检查符（Named checks）                      | `in`, `!in`, `is`, `!is`                 |
|      | 比较大小（Comparison）                         | `<`, `>`, `<=`, `>=`                     |
|      | 相等性判断（Equality)                          | `==`, `\!==`                             |
|      | 与 （Conjunction）                          | `&&`                                     |
|      | 或 （Disjunction）                          | `ll`                                     |
| 最低   | 赋值（Assignment）                           | `=`, `+=`, `-=`, `*=`, `/=`, `%=`        |

注：Markdown表格语法：`ll`是`||`。

为实现这些的操作符，Kotlin为二元操作符左侧的类型和一元操作符的参数类型，提供了相应的函数或扩展函数。

例如在kotlin/core/builtins/native/kotlin/Primitives.kt代码中，对基本类型Int的操作符的实现代码如下

```
public class Int private constructor() : Number(), Comparable<Int> {
    ...

    /**
     * Compares this value with the specified value for order.
     * Returns zero if this value is equal to the specified other value, a negative number if it's less than other,
     * or a positive number if it's greater than other.
     */
    public operator fun compareTo(other: Byte): Int

    /**
     * Compares this value with the specified value for order.
     * Returns zero if this value is equal to the specified other value, a negative number if it's less than other,
     * or a positive number if it's greater than other.
     */
    public operator fun compareTo(other: Short): Int

    /**
     * Compares this value with the specified value for order.
     * Returns zero if this value is equal to the specified other value, a negative number if it's less than other,
     * or a positive number if it's greater than other.
     */
    public override operator fun compareTo(other: Int): Int

    /**
     * Compares this value with the specified value for order.
     * Returns zero if this value is equal to the specified other value, a negative number if it's less than other,
     * or a positive number if it's greater than other.
     */
    public operator fun compareTo(other: Long): Int

    /**
     * Compares this value with the specified value for order.
     * Returns zero if this value is equal to the specified other value, a negative number if it's less than other,
     * or a positive number if it's greater than other.
     */
    public operator fun compareTo(other: Float): Int

    /**
     * Compares this value with the specified value for order.
     * Returns zero if this value is equal to the specified other value, a negative number if it's less than other,
     * or a positive number if it's greater than other.
     */
    public operator fun compareTo(other: Double): Int

    /** Adds the other value to this value. */
    public operator fun plus(other: Byte): Int
    /** Adds the other value to this value. */
    public operator fun plus(other: Short): Int
    /** Adds the other value to this value. */
    public operator fun plus(other: Int): Int
    /** Adds the other value to this value. */
    public operator fun plus(other: Long): Long
    /** Adds the other value to this value. */
    public operator fun plus(other: Float): Float
    /** Adds the other value to this value. */
    public operator fun plus(other: Double): Double

    /** Subtracts the other value from this value. */
    public operator fun minus(other: Byte): Int
    /** Subtracts the other value from this value. */
    public operator fun minus(other: Short): Int
    /** Subtracts the other value from this value. */
    public operator fun minus(other: Int): Int
    /** Subtracts the other value from this value. */
    public operator fun minus(other: Long): Long
    /** Subtracts the other value from this value. */
    public operator fun minus(other: Float): Float
    /** Subtracts the other value from this value. */
    public operator fun minus(other: Double): Double

    /** Multiplies this value by the other value. */
    public operator fun times(other: Byte): Int
    /** Multiplies this value by the other value. */
    public operator fun times(other: Short): Int
    /** Multiplies this value by the other value. */
    public operator fun times(other: Int): Int
    /** Multiplies this value by the other value. */
    public operator fun times(other: Long): Long
    /** Multiplies this value by the other value. */
    public operator fun times(other: Float): Float
    /** Multiplies this value by the other value. */
    public operator fun times(other: Double): Double

    /** Divides this value by the other value. */
    public operator fun div(other: Byte): Int
    /** Divides this value by the other value. */
    public operator fun div(other: Short): Int
    /** Divides this value by the other value. */
    public operator fun div(other: Int): Int
    /** Divides this value by the other value. */
    public operator fun div(other: Long): Long
    /** Divides this value by the other value. */
    public operator fun div(other: Float): Float
    /** Divides this value by the other value. */
    public operator fun div(other: Double): Double

    /** Calculates the remainder of dividing this value by the other value. */
    @Deprecated("Use rem(other) instead", ReplaceWith("rem(other)"), DeprecationLevel.WARNING)
    public operator fun mod(other: Byte): Int
    /** Calculates the remainder of dividing this value by the other value. */
    @Deprecated("Use rem(other) instead", ReplaceWith("rem(other)"), DeprecationLevel.WARNING)
    public operator fun mod(other: Short): Int
    /** Calculates the remainder of dividing this value by the other value. */
    @Deprecated("Use rem(other) instead", ReplaceWith("rem(other)"), DeprecationLevel.WARNING)
    public operator fun mod(other: Int): Int
    /** Calculates the remainder of dividing this value by the other value. */
    @Deprecated("Use rem(other) instead", ReplaceWith("rem(other)"), DeprecationLevel.WARNING)
    public operator fun mod(other: Long): Long
    /** Calculates the remainder of dividing this value by the other value. */
    @Deprecated("Use rem(other) instead", ReplaceWith("rem(other)"), DeprecationLevel.WARNING)
    public operator fun mod(other: Float): Float
    /** Calculates the remainder of dividing this value by the other value. */
    @Deprecated("Use rem(other) instead", ReplaceWith("rem(other)"), DeprecationLevel.WARNING)
    public operator fun mod(other: Double): Double

    /** Calculates the remainder of dividing this value by the other value. */
    @SinceKotlin("1.1")
    public operator fun rem(other: Byte): Int
    /** Calculates the remainder of dividing this value by the other value. */
    @SinceKotlin("1.1")
    public operator fun rem(other: Short): Int
    /** Calculates the remainder of dividing this value by the other value. */
    @SinceKotlin("1.1")
    public operator fun rem(other: Int): Int
    /** Calculates the remainder of dividing this value by the other value. */
    @SinceKotlin("1.1")
    public operator fun rem(other: Long): Long
    /** Calculates the remainder of dividing this value by the other value. */
    @SinceKotlin("1.1")
    public operator fun rem(other: Float): Float
    /** Calculates the remainder of dividing this value by the other value. */
    @SinceKotlin("1.1")
    public operator fun rem(other: Double): Double

    /** Increments this value. */
    public operator fun inc(): Int
    /** Decrements this value. */
    public operator fun dec(): Int
    /** Returns this value. */
    public operator fun unaryPlus(): Int
    /** Returns the negative of this value. */
    public operator fun unaryMinus(): Int

     /** Creates a range from this value to the specified [other] value. */
    public operator fun rangeTo(other: Byte): IntRange
     /** Creates a range from this value to the specified [other] value. */
    public operator fun rangeTo(other: Short): IntRange
     /** Creates a range from this value to the specified [other] value. */
    public operator fun rangeTo(other: Int): IntRange
     /** Creates a range from this value to the specified [other] value. */
    public operator fun rangeTo(other: Long): LongRange

    /** Shifts this value left by [bits]. */
    public infix fun shl(bitCount: Int): Int
    /** Shifts this value right by [bits], filling the leftmost bits with copies of the sign bit. */
    public infix fun shr(bitCount: Int): Int
    /** Shifts this value right by [bits], filling the leftmost bits with zeros. */
    public infix fun ushr(bitCount: Int): Int
    /** Performs a bitwise AND operation between the two values. */
    public infix fun and(other: Int): Int
    /** Performs a bitwise OR operation between the two values. */
    public infix fun or(other: Int): Int
    /** Performs a bitwise XOR operation between the two values. */
    public infix fun xor(other: Int): Int
    /** Inverts the bits in this value. */
    public fun inv(): Int

    public override fun toByte(): Byte
    public override fun toChar(): Char
    public override fun toShort(): Short
    public override fun toInt(): Int
    public override fun toLong(): Long
    public override fun toFloat(): Float
    public override fun toDouble(): Double
}
```

从源代码我们可以看出，重载操作符的函数需要用 `operator` 修饰符标记。中缀操作符的函数使用`infix`修饰符标记。

### 3.7.5 一元操作符（unary operation）

#### 前缀操作符

| 表达式  | 翻译为              |
| ---- | ---------------- |
| `+a` | `a.unaryPlus()`  |
| `-a` | `a.unaryMinus()` |
| `!a` | `a.not()`        |

例如，当编译器处理表达式 `+a` 时，它将执行以下步骤：

- 确定 `a` 的类型，令其为 `T`。
- 为接收者 `T` 查找一个带有 `operator` 修饰符的无参函数 `unaryPlus（）`，即成员函数或扩展函数。
- 如果函数不存在或不明确，则导致编译错误。
- 如果函数存在且其返回类型为 `R`，那就表达式 `+a` 具有类型 `R`。

编译器对这些操作以及所有其他操作都针对基本类型做了优化，不会引入函数调用的开销。

以下是如何重载一元减运算符的示例：

```
package com.easy.kotlin

/**
 * Created by jack on 2017/6/10.
 */

class OperatorDemo {

}


data class Point(val x: Int, val y: Int)

operator fun Point.unaryMinus() = Point(-x, -y)


```

测试代码：

```
package com.easy.kotlin

import org.junit.Test
import org.junit.runner.RunWith
import org.junit.runners.JUnit4

/**
 * Created by jack on 2017/6/10.
 */
@RunWith(JUnit4::class)
class OperatorDemoTest {

    @Test
    fun testPointUnaryMinus() {
        val p = Point(1, 1)
        val np = -p
        println(np) //Point(x=-1, y=-1)
    }
}

```

#### 递增和递减

| 表达式   | 翻译为                 |
| ----- | ------------------- |
| `a++` | `a.inc()` 返回值是`a`   |
| `a--` | `a.dec()` 返回值是`a`   |
| `++a` | `a.inc()` 返回值是`a+1` |
| `--a` | `a.dec()` 返回值是`a-1` |

`inc()` 和 `dec()` 函数必须返回一个值，它用于赋值给使用
`++` 或 `--` 操作的变量。

编译器执行以下步骤来解析*后缀*形式的操作符，例如 `a++`：

- 确定 `a` 的类型，令其为 `T`。
- 查找一个适用于类型为 `T` 的接收者的、带有 `operator` 修饰符的无参数函数 `inc()`。
- 检查函数的返回类型是 `T` 的子类型。

计算表达式的步骤是：

- 把 `a` 的初始值存储到临时存储 `a_` 中
- 把 `a.inc()` 结果赋值给 `a`
- 把 `a_` 作为表达式的结果返回

( `a--` 同理分析）。

对于*前缀*形式 `++a` 和 `--a` 解析步骤类似，但是返回值是取的新值来返回：

- 把 `a.inc()` 结果赋值给 `a`
- 把 `a` 的新值`a+1`作为表达式结果返回

( `--a` 同理分析）。

### 3.7.6 二元操作符

#### 算术运算符

| 表达式     | 翻译为                    |
| ------- | ---------------------- |
| `a + b` | `a.plus(b)`            |
| `a - b` | `a.minus(b)`           |
| `a * b` | `a.times(b)`           |
| `a / b` | `a.div(b)`             |
| `a % b` | `a.rem(b)`、 `a.mod(b)` |
| `a..b`  | `a.rangeTo(b)`         |

代码示例

```
>>> val a=10
>>> val b=3
>>> a+b
13
>>> a-b
7
>>> a/b
3
>>> a%b
1
>>> a..b
10..3
>>> b..a
3..10
```

#### 字符串的`+`运算符重载

先用代码举个例子：

```
>>> ""+1
1
>>> 1+""
error: none of the following functions can be called with the arguments supplied: 
public final operator fun plus(other: Byte): Int defined in kotlin.Int
public final operator fun plus(other: Double): Double defined in kotlin.Int
public final operator fun plus(other: Float): Float defined in kotlin.Int
public final operator fun plus(other: Int): Int defined in kotlin.Int
public final operator fun plus(other: Long): Long defined in kotlin.Int
public final operator fun plus(other: Short): Int defined in kotlin.Int
1+""
 ^

```

从上面的示例，我们可以看出，在Kotlin中`1+""`是不允许的(这地方，相比Scala，写这样的Kotlin代码就显得不大友好)，只能显式调用`toString`来相加：

```
>>> 1.toString()+""
1
```

#### 自定义重载的 `+` 运算符

下面我们使用一个计数类 Counter 重载的 `+` 运算符来增加index的计数值。

代码示例

```
data class Counter(var index: Int)

operator fun Counter.plus(increment: Int): Counter {
    return Counter(index + increment)
}
```

测试类

```
package com.easy.kotlin

import org.junit.Test
import org.junit.runner.RunWith
import org.junit.runners.JUnit4

/**
 * Created by jack on 2017/6/10.
 */
@RunWith(JUnit4::class)
class OperatorDemoTest 
    @Test
    fun testCounterIndexPlus() {
        val c = Counter(1)
        val cplus = c + 10
        println(cplus) //Counter(index=11)
    }
}

```

#### `in`操作符

| 表达式       | 翻译为              |
| --------- | ---------------- |
| `a in b`  | `b.contains(a)`  |
| `a !in b` | `!b.contains(a)` |

#### 索引访问操作符

| 表达式        | 翻译为           |
| ---------- | ------------- |
| `a[i]`     | `a.get(i)`    |
| `a[i] = b` | `a.set(i, b)` |

方括号转换为调用带有适当数量参数的 `get` 和 `set`。

#### 调用操作符

| 表达式    | 翻译为           |
| ------ | ------------- |
| `a()`  | `a.invoke()`  |
| `a(i)` | `a.invoke(i)` |

圆括号转换为调用带有适当数量参数的 `invoke`。

#### 计算并赋值

| 表达式      | 翻译为                |
| -------- | ------------------ |
| `a += b` | `a.plusAssign(b)`  |
| `a -= b` | `a.minusAssign(b)` |
| `a *= b` | `a.timesAssign(b)` |
| `a /= b` | `a.divAssign(b)`   |
| `a %= b` | `a.modAssign(b)`   |

对于赋值操作，例如 `a += b`，编译器会试着生成 `a = a + b` 的代码（这里包含类型检查：`a + b` 的类型必须是 `a` 的子类型）。

#### 相等与不等操作符

Kotlin 中有两种类型的相等性：

- 引用相等 `===` `!==`（两个引用指向同一对象）
- 结构相等 `==` `!=`（ 使用`equals()` 判断）

| 表达式      | 翻译为                               |
| -------- | --------------------------------- |
| `a == b` | `a?.equals(b) ?: (b === null)`    |
| `a != b` | `!(a?.equals(b) ?: (b === null))` |

这个 `==` 操作符有些特殊：它被翻译成一个复杂的表达式，用于筛选 `null` 值。

意思是：如果 a 不是 null 则调用 `equals(Any?)` 函数并返回其值；否则（即 `a === null`）就计算 `b === null` 的值并返回。

当与 null 显式比较时，`a == null` 会被自动转换为 `a=== null`

*注意*：`===` 和 `!==`不可重载。

#### Elvis 操作符 `?:`

在Kotin中，Elvis操作符特定是跟null比较。也就是说

```
y = x?:0
```

等价于

```
val y = if(x!==null) x else 0
```

主要用来作`null`安全性检查。

Elvis操作符 `?:` 是一个二元运算符，如果第一个操作数为真，则返回第一个操作数，否则将计算并返回其第二个操作数。它是三元条件运算符的变体。命名灵感来自猫王的发型风格。

Kotlin中没有这样的三元运算符 `true?1:0`，取而代之的是`if(true) 1 else 0`。而Elvis操作符算是精简版的三元运算符。

我们在Java中使用的三元运算符的语法，你通常要重复变量两次， 示例：

```
String name = "Elvis Presley";
String displayName = (name != null) ? name : "Unknown";
```

取而代之，你可以使用Elvis操作符。

```
String name = "Elvis Presley";
String displayName = name?:"Unknown"
```

我们可以看出，用Elvis操作符（?:）可以把带有默认值的if/else结构写的及其短小。用Elvis操作符不用检查null（避免了`NullPointerException`），也不用重复变量。

这个Elvis操作符功能在Spring 表达式语言 (SpEL)中提供。

在Kotlin中当然就没有理由不支持这个特性。

代码示例：

```
>>> val x = null
>>> val y = x?:0
>>> y
0
>>> val x = false
>>> val y = x?:0
>>> y
false
>>> val x = ""
>>> val y = x?:0
>>> y

>>> val x = "abc"
>>> val y = x?:0
>>> y
abc
```

#### 比较操作符

| 表达式      | 翻译为                   |
| -------- | --------------------- |
| `a > b`  | `a.compareTo(b) > 0`  |
| `a < b`  | `a.compareTo(b) < 0`  |
| `a >= b` | `a.compareTo(b) >= 0` |
| `a <= b` | `a.compareTo(b) <= 0` |

所有的比较都转换为对 `compareTo` 的调用，这个函数需要返回 `Int` 值

#### 用infix函数自定义中缀操作符

我们可以通过自定义infix函数来实现中缀操作符。

代码示例

```
data class Person(val name: String, val age: Int)

infix fun Person.grow(years: Int): Person {
    return Person(name, age + years)
}
```

测试代码

```
package com.easy.kotlin

import org.junit.Test
import org.junit.runner.RunWith
import org.junit.runners.JUnit4

/**
 * Created by jack on 2017/6/11.
 */
@RunWith(JUnit4::class)
class InfixFunctionDemoTest {

    @Test fun testInfixFuntion() {
        val person = Person("Jack", 20)

        println(person.grow(2))

        println(person grow 2)
    }
}

```

输出

```
Person(name=Jack, age=22)
Person(name=Jack, age=22)
```

## 3.8 函数扩展和属性扩展(Extensions)

Kotlin 支持 扩展函数 和 扩展属性。其能够扩展一个类的新功能而无需继承该类或使用像装饰者这样的设计模式等。

大多数时候我们在顶层定义扩展，即直接在包里：

```
package com.easy.kotlin

/**
 * Created by jack on 2017/6/11.
 */

val <T> List<T>.lastIndex: Int get() = size - 1

fun String.notEmpty(): Boolean {
    return !this.isEmpty()
}

```

这样我们就可以在整个包里使用这些扩展。

要使用其他包的扩展，我们需要在调用方导入它：

```
package com.example.usage

import foo.bar.goo // 导入所有名为“goo”的扩展
                   // 或者
import foo.bar.*   // 从“foo.bar”导入一切

fun usage(baz: Baz) {
    baz.goo()
}

```

### 3.8.1 扩展函数

声明一个扩展函数，我们需要用_被扩展的类型_来作为前缀。

比如说，我们不喜欢类似下面的_双重否定_式的逻辑判断（绕脑子）：

```
>>> !"123".isEmpty()
true
```

我们就可以为`String`类型扩展一个`notEmpty()`函数：

```
>>> fun String.notEmpty():Boolean{
... return !this.isEmpty()
... }

>>> "".notEmpty()
false

>>> "123".notEmpty()
true
```

下面代码为 `MutableList<Int>` 添加一个`swap` 函数：

```
fun MutableList<Int>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // this对应该列表
    this[index1] = this[index2]
    this[index2] = tmp
}
```

这个 `this` 关键字在扩展函数内部对应到接收者对象（传过来的在点`.`符号前的对象）
现在，我们对任意 `MutableList<Int>` 调用该函数了。

当然，这个函数对任何 `MutableList<T>` 起作用，我们可以泛化它：

```
    fun <T> MutableList<T>.mswap(index1: Int, index2: Int) {
        val tmp = this[index1] // “this”对应该列表
        this[index1] = this[index2]
        this[index2] = tmp
    }
```

为了在接收者类型表达式中使用泛型，我们要在函数名前声明泛型参数。

完整代码示例

```
package com.easy.kotlin

/**
 * Created by jack on 2017/6/11.
 */

val <T> List<T>.lastIndex: Int get() = size - 1

fun String.notEmpty(): Boolean {
    return !this.isEmpty()
}

fun MutableList<Int>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // this对应该列表m
    this[index1] = this[index2]
    this[index2] = tmp
}

fun <T> MutableList<T>.mswap(index1: Int, index2: Int) {
    val tmp = this[index1] // “this”对应该列表
    this[index1] = this[index2]
    this[index2] = tmp
}


class ExtensionsDemo {


    fun useExtensions() {
        val a = "abc"
        println(a.notEmpty())//true

        val mList = mutableListOf<Int>(1, 2, 3, 4, 5)
        println("Before Swap:")
        println(mList)//[1, 2, 3, 4, 5]
        mList.swap(0, mList.size - 1)
        println("After Swap:")
        println(mList)//[5, 2, 3, 4, 1]


        val mmList = mutableListOf<String>("a12", "b34", "c56", "d78")
        println("Before Swap:")
        println(mmList)//[a12, b34, c56, d78]
        mmList.mswap(1, 2)
        println("After Swap:")
        println(mmList)//[a12, c56, b34, d78]


        val mmmList = mutableListOf<Int>(100, 200, 300, 400, 500)
        println("Before Swap:")
        println(mmmList)
        mmmList.mswap(0, mmmList.lastIndex)
        println("After Swap:")
        println(mmmList)
    }


    class Inner {
        fun useExtensions() {
            val mmmList = mutableListOf<Int>(100, 200, 300, 400, 500)
            println(mmmList.lastIndex)
        }
    }
}





```

测试代码

```
package com.easy.kotlin

import org.junit.Test
import org.junit.runner.RunWith
import org.junit.runners.JUnit4

/**
 * Created by jack on 2017/6/11.
 */

@RunWith(JUnit4::class)
class ExtensionsDemoTest {
    @Test fun testExtensionsDemo() {
        val demo = ExtensionsDemo()
        demo.useExtensions()
    }
}

```

扩展不是真正的修改他们所扩展的类。我们定义一个扩展，其实并没有在一个类中插入新函数，仅仅是通过该类型的变量，用点`.`表达式去调用这个新函数。

### 3.8.2 扩展属性

和函数类似，Kotlin 支持扩展属性：

```
val <T> List<T>.lastIndex: Int
    get() = size - 1
```

注意：由于扩展没有实际的将成员插入类中，因此对扩展的属性来说，它的行为只能由显式提供的 getters/setters 定义。

代码示例：

```
package com.easy.kotlin

/**
 * Created by jack on 2017/6/11.
 */

val <T> List<T>.lastIndex: Int get() = size - 1




```

我们可以直接使用包`com.easy.kotlin`中扩展的属性`lastIndex` :

![img](https://segmentfault.com/img/remote/1460000009734344?w=765&h=199)

## 3.9 空指针安全(Null-safety)

我们写代码的时候知道，在Java中NPE（NullPointerExceptions）是一件成程序员几近崩溃的事情。很多时候，虽然费尽体力脑力，仍然防不胜防。

以前，当我们不确定一个DTO类中的字段是否已初始化时，可以使用@Nullable和@NotNull注解来声明，但功能很有限。

现在好了，Kotlin在编译器级别，把你之前在Java中需要写的null check代码完成了。

但是，当我们的代码

- 显式调用 `throw NullPointerException()`
- 使用了 `!!` 操作符
- 调用的外部 Java 代码有NPE
- 对于初始化，有一些数据不一致（如一个未初始化的 `this` 用于构造函数的某个地方）

也可能会发生NPE。

在Kotlin中`null`等同于空指针。我们来通过代码来看一下`null`的有趣的特性：

首先，一个非空引用不能直接赋值为`null` :

```
>>> var a="abc"
>>> a=null
error: null can not be a value of a non-null type String
a=null
  ^

>>> var one=1
>>> one=null
error: null can not be a value of a non-null type Int
one=null
    ^

>>> var arrayInts = intArrayOf(1,2,3)
>>> arrayInts=null
error: null can not be a value of a non-null type IntArray
arrayInts=null
          ^

```

这样，我们就可以放心地调用 `a` 的方法或者访问它的属性，不会导致 `NPE`：

```
>>> val a="abc"
>>> a.length
3
```

如果要允许为空，我们可以在变量的类型后面加个问号`?`声明一个变量为可空的：

```
>>> var a:String?="abc"
>>> a=null
>>> var one:Int?=1
>>> one=null
>>> var arrayInts:IntArray?=intArrayOf(1,2,3)
>>> arrayInts=null
>>> arrayInts
null
```

如果我们声明了一个可空`String?`类型变量`na` ，然后直接调用`length`属性，这将是不安全的。编译器会直接报错：

```
>>> var na:String?="abc"
>>> na=null
>>> na.length
error: only safe (?.) or non-null asserted (!!.) calls are allowed on a nullable receiver of type String?
na.length
  ^

```

我们使用安全调用`?.` 和 非空断言调用 `!!.`

```
>>> na?.length
null
>>> na!!.length
kotlin.KotlinNullPointerException

```

我们可以看出，代码返回了`null` 和 `kotlin.KotlinNullPointerException`。

安全调用在链式调用中很有用。在调用链中如果任意一个属性（环节）为空，这个链式调用就会安全返回 null。

如果要只对非空值执行某个操作，安全调用操作符可以与 `let` （以调用者的值作为参数来执行指定的函数块，并返回其结果）一起使用：

```
>>> val listWithNulls: List<String?> = listOf("A", "B",null)
>>> listWithNulls
[A, B, null]

>>> listWithNulls.forEach{
... it?.let{println(it)}
... }
A
B
```