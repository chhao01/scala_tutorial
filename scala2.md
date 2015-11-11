<em>请勿进行商业转载，学习交流转载请注明出处</em>

# Scala基础语法 #
## 定义类型和创建对象实例 ##
```java
final String[] a = new String[100];
final double a = 2 + 1.2d;
double c = a;
c=1.2d;
```
```scala 
val a: Array[String] = new Array[String](100) // val == final （定义不可变引用）
val a = new Array[String](100) // 不需要；结尾
val a = 2 + 1.2d // 自动类型推导
var c = a // var 定义可变引用
c = 1.2d // 赋值
lazy val a = b + c // 惰值计算（延迟计算），当第一次被使用时才会触发计算
var a = 123
val (a, b, c) = (123, 345, 456) // 从元组中提取值
var (a, b) = Pair(345, 456)
a += 1 // OK
a++ // error 没有++操作符
++a // error 没有++操作符
```

## // ;和变量名转义 ##
```java
System.out.println("Hello World"); // ";"结尾
int for = 123 // Keyword Error
```
```scala
println("Hello World")
val a = "Hello World"; println(a)
val `for` = 123
```

## 各种Literal的定义 ##
```scala
0x12
0.12f
31.2d
.123f
'a'
'\u0012'
"abc"
"a\nb\nc"
"0\"abc\""
"""0"abc"d"""
"""
This is 
Multiple
Line
"""
```

## String interpolation ##
```scala
val a = 123
val b = (123, 456)
s"This is a ${a}"
s"This is a $a"
s"This is first element of b ${b._1}"

"""

  val c1 = '\u0042'
  val s1 = "\n\\\t"
  val s2 =
    """
      this
      is
      a
      test
      this line includes \\
      this line include \n
    """
  val s3 =
    """
      |this
      |is
      |a
      |test
      |this line includes \\
    """.stripMargin


  println("this is a:" + a + " and this is b:" + b)
  println(s"this is a:$a and this is b:$b")
  println(s"this is a:${a + 1} and this is the second value of b:${b._2}")
  println(s"""
  | This is 
  | second element of b ${b._2}""".stripMargin)
  
```

## 注释 ##
 和Java是一样的，分多行和单行都支持
```scala
/*
 * This is multiple line comments
 */

// This is single line comment
/* Another Single line comment */
```

## 导入依赖的类 ##
```scala
import java.util.Array                     // import
import java.util._                         // wildcard
import java.util.{ArrayList, HashSet}      // multiple import
import java.util.{ArrayList => JArrayList} // renaming
Object a {
    import java.util.ArrayList             // limit the import scope
    def calc: ArrayList = {…}
    def showMe(what: String): Unit = {
       println("show me " + what)
    }
}

import java.util._
import a._
showMe("the money")

scala.Predef.* / java.lang.* / scala.* // 默认已经导入
                                           
```

## 类获取与类型转换 ##
```scala
    val a: AnyRef = "abc"
    val b: String = a.asInstanceOf[String] // String b = (String)a;
    val clazz: Class[_] = classOf[String]  // Class<?> clazz = String.class;
    val clazz: Class[_] = b.getClass       // Class<?> clazz = b.getClass();
```

## Scala类型族谱 ##

![](https://camo.githubusercontent.com/d394abcbb3142dc30ede7d8ca49fafa5013ab7e5/687474703a2f2f7777772e7363616c612d6c616e672e6f72672f6f6c642f73697465732f64656661756c742f66696c65732f696d616765732f636c6173736869657261726368792e706e67)


- 特殊类型：
  - Unit 理解为没有值的类型，也就是函数执行体类型
  - Nothing 是所有的类型的子类型
  - Null null或者empty引用
  - Any 所有类型的基类类型
  - AnyRef 所有的可以被引用类型的基类

## 分支与循环 ##
- 条件分支(if)
```scala
val a = 123
val b = 456
val c = if (a > b) a else b

if (a > b) {
   println ("a is greater than b")
} else if (a == b) {
   println ("a equals b")
else {
   println ("b is greater than a")
}
```
- For Loop
```scala
for (idx <- 0 to 10) println(idx) // 0, 1, 2 ... 10
for (idx <- 0 until 10) println(idx) // 0, 1, 2 ... 9
for (idx <- 0 until 10 if idx % 3 == 0; if idx % 2 == 0) println(idx) // 0, 6

for (idx <- 0 until 10 
   if idx % 3 == 0; 
   if idx % 2 == 0) {
  println(idx)
}

// For循环嵌套
val a = Array(1, 2, 3, 4, 5, 6)
val b = Array(11, 22, 33, 44, 55, 66)
for (idx1 <- a 
   if idx1 % 2 == 0; 
   idx2 <- b;
   idx3 <- 1 to 3) {
  println(s"$idx1 <-> $idx2 <-> $idx3")
}
// 没有break 或者 continue
```
- While Loop
```scala
var idx = 0
while (idx < 10) {
  println (idx)
  idx += 1
}

var idx2 = 0
do {
   println (idx2)
   idx2 += 2
} while(idx2 <= 10)

// 没有break 或者 continue
var i = 0
var foundIt = false
val args = Array("-aa", "-bb", "mytest.scala", "next")
while (i < args.length && !foundIt) {
  if (!args(i).startsWith("-")) {
    if (args(i).endsWith(".scala"))
      foundIt = true
  }
  i = i + 1
}
```

- Range
```scala
val a = 1 to 10
val b = 1 until 10
val c = 1 to 10 by 3
val d = 10 to 1 by -3
val e = 1.2f to 3.4f by 0.31f
```

## 等价性 ##

```scala

// 相当于java中的 .equal(..)
val a = "String 1"
val b = "String 2"
a == b // a.equals(b)
a != b // !a.equals(b)

// 相当于java中的 ==
val a = "String 1"
val b = "String 2"
a eq b // 相当于 java中的 a == b
a ne b // 相当于 java中的 a != b
```

