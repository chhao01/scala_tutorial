<em>请勿进行商业转载，学习交流转载请注明出处</em>

# Scala函数 #
- 函数定义
```scala
// In java: 
// public int sum(int a, int b) { 
//   return a + b; 
// } 
def sum(a: Int, b: Int): Int = { return a + b }
def abs(a: Int): Int = {
  if (a >= 0) {
    return a // return可以提前返回
  }

  return -a
}
def sum(a: Int, b: Int) = { a + b } 
def max(a: Int, b: Int) = {
  println(a)
  println(b)
  if (a > b) {
     a
  } else {
     b
  }
}

def fac(n: Int) = if (n == 0) 1 else n * fac(n - 1) // can not compile 
def sum(a: Int, b: Int = 2) = { 
  a + b 
} 

def sum(a: Int, b: Int) = a + b 
def sum(a: Int)(b: Int) = a + b 
def sum(a: Int, b: Int) = ??? 
def process(a: Int): Unit = {} // process(2) 
def process(a: Int) {} // process(2) function without return value 
def start = {2} // val a = start 
def start() = {2} // val a = start()

- 函数调用
```scala
println (process _) // outputs <function1>| 
println (start _) // outputs <function0>
```
```scala
scala> val f = (_: Int) + (_: Int)
f: (Int, Int) => Int = <function>
scala> f(5, 4)
res11: Int = 9
```

- 默认参数与按名字传递参数
```scala
def fun(a: Int, b: Boolean = true, c: Boolean = false): Unit = {
  println (s"a:$a, b: $b, c: $c") 
}

fun(2, true, true)
fun(2, false)
fun(2, c=true)
```

- 函数作为参数
```scala
def oncePerSecond(callback: (Int) => Unit) { 
  var i = 0 
  while (i < 5) { 
    callback(i)
    Thread.sleep(1000)
    i += 1 
  } 
} 

def timeFlies(i: Int) {
 println("time flies like an arrow... " + i) 
} 

oncePerSecond(timeFlies) 
```

- 匿名函数
```scala
def oncePerSecond(callback: (Int) => Unit) {
 var i = 0 
 while (i < 5) {
   callback(i) 
   Thread sleep 1000
   i += 1
 }
} 

def timeFlies(i: Int) {
 println("time flies like an arrow... " + i)
} 

oncePerSecond(timeFlies) 
oncePerSecond((i: Int) => { 
  println ("I am an anonymous function." +i) 
}) 

val d = (i: Int) => { println("test2.." + i) } 

oncePerSecond(d) 
```

- 嵌套函数
```scala
def sumDoubles(n: Int): Int = {
 def dbl(a: Int) = 2 * a 
 if(n > 0) {
   dbl(n) + sumDoubles(n - 1)
 } else { 
   0 
 } 
} 
```

- 隐式转换函数
```scala
def repeat(str: String, n: Int): String = {
  var i = 0
  val sb = new StringBuffer(str.length * n)
  while (i < n) {
    sb.append(str)
    i += 1
  }

  sb.toString
}

println(repeat("abc", 3))
println(repeat(123.toString, 3))
println(repeat(123, 3)) // error, found: Int(123), required: String

implicit def int2String(i: Int) = i.toString
for (i <- 1 to 4) {
  println(repeat(i, i))
}
```

- 部分应用函数(Partial Apply Function)
```scala
def sum(a: Int, b: Int) = a + b 
val add5: Int=>Int = sum(_, 5) 
println (add5(20)) // outputs 25 
```

- 咖喱化函数(Curry Function)
```scala
def sum(a: Int)(b: Int) = a + b 
val add5 = sum(5) _ 
println (add5(20)) // outputs 25 

def sum(a: Int, b: Int) = a + b
sum(1, 5)
val curried = (sum _).curried // curried
curried(1)(5)

// define a curried function object
val curried: Int => Int => Int = (a: Int) => (b: Int) => a + b
curried(1)(5)
```

```scala
def query(conn: Connection)(c: Criteria) : Seq[Person] = {
 ctx.sql(sql).collect.map(…..) 
} 

val queryBackup= query(backupConnection) _ 
val queryOnline = query(onlineConnection) _ 

for (criteria <- criterias) {
 assert(queryBackup(critera) == queryOnline(critera))
} 
```

- 部分函数/分段函数（Partial Function）
```scala
val f = new Function2[Int, Double, Double] {
  def apply(a: Int, b: Double): Double = a + b
}
f(3, 3.0d)

val fraction = new PartialFunction[Int, Int] {
 def apply(d: Int) = 42 / d 
 def isDefinedAt(d: Int) = d != 0 
} 

fraction.isDefinedAt(42) // true
fraction.isDefinedAt(0)  // false
fraction(42)   // 1
fraction(0)     // java.lang.ArithmeticException: / by zero

def fprint(p: PartialFunction[Int, Int])(a: Int) {
   if (p.isDefinedAt(a)) {
     println(p(a))
   } else {
     println(s"$a is invalid")
   }
}
```
```scala
def fraction: PartialFunction[Int, Int] = {
 case d: Int if d != 0 ⇒ 42 / d 
} 

fraction(42) // 1
fraction(0)   // scala.MatchError: 0 (of class java.lang.Integer)
```

- 高阶函数（Higher Order Function）
```scala
def safeStringOp(s: String, f: String => String) = {
 if (s != null) f(s) else s 
} 

def reverser(s: String) = s.reverse 
safeStringOp(null, reverser)           // null
safeStringOp("Ready", reverser)   // dyaeR
```

```scala
// closure
var factor = 2
val multiply: (Int) => Int = (n: Int) => n * factor
multiply(3) // 6

factor = 4
multiply(3) // 12

// concrete example
def whileLoop(cond: => Boolean)(body: => Unit): Unit = if (cond) { 
  body 
  whileLoop(cond)(body)
} 

var i = 10 // closure
whileLoop (i > 0) {
 println(i) 
  i -= 1 
} 

class LoopUnlessCond(body: => Unit) {
 def unless(cond: => Boolean) {
   body 
   if (!cond) unless(cond)
 }
}

def loop(body: => Unit): LoopUnlessCond = new LoopUnlessCond(body) 

var i = 10 
loop {
 println("i = " + i) 
 i -= 1 
} unless (i == 0) 
```

- 递归函数调用与尾递归
```
def fab(n: Int): Int = if (n == 0 || n == 1) 1 else fab(n - 1) + fab(n - 2)

import scala.annotation.tailrec
@tailrec
def fac(n: Int): Int = if (n == 0) 1 else n * fac(n - 1) // not tail recursive

@tailrec
def fac(n: Int, accu: Int): Int = if (n <= 1) accu else fac(n - 1, n * accu)

@tailrec
def whileLoop(cond: => Boolean)(body: => Unit): Unit = if (cond) { 
  body 
  whileLoop(cond)(body)
} 

class LoopUnlessCond(body: => Unit) {
 @tailrec
 final def unless(cond: => Boolean) { // Must add `final`
   body 
   if (!cond) unless(cond)
 }
}
```