<em>请勿进行商业转载，学习交流转载请注明出处</em>

# 类与对象 #
- 类定义
```scala
abstract class Colorable(var color: Int) 
class Point(protected var xc: Int, protected var yc: Int, color: Int) extends Colorable(color) { 
  def this() = this(Int.MaxValue, Int.MaxValue, Int.MaxValue) 
  def this(x: Int, y: Int) = this(x, y, Int.MaxValue)
  def step(dx: Int, dy: Int) { 
    this.xc = this.xc + dx 
    this.xc = this.xc + dy 
  } 
  def move(x: Int, y: Int) = { 
    this.xc = x 
    this.yc = y
    doSomething()
    this 
  } 
  private def doSomething() {
  
  }
  override def toString(): String = s"($xc, $yc, $color)" 
} 

val pt = new Point (1,2,3) 
pt.setColor(456) 
println(pt.getColor()) 

class Name(var value: String)
// 完全等价于
class Name(s: String) {
  private var _value: String = s
  def value: String = _value
  def value_=(v: String): Unit = { // convention that allow user drop the "_" in client code
    this._value = v
  }
}

val a = new Name("Cheng Hao")
println(a.value)
a.value_=("Hao Cheng")
println(a.value)
a.value = "Cheng"
println(a.value)

import scala.beans.BeanProperty // to follow the java Accessor pattern, getter/setter method
class Name(@BeanProperty var value: String)

class Name(val value: String)
// 完全等价于
class Name(s: String) {
  private var _value: String = s
  def value: String = _value
}

```

- Case Class
```scala
case class Point (x: Int, y: Int, color: Int) 
val pt = Point(1, 2, 3) 

abstract class Colorable { 
  def color: Int 
} 

case class Point(x: Int, y: Int, color: Int) extends Colorable
val pt = Point(1, 2, 3) 
println(pt.toString) 

// Case class is the subclass of Product
// Immutable
// equals/toString are implemented
// no need `new` when constructing the object
```

- Trait
```scala
trait Similarity[T] { 
  var threshold: Int = 100 
  def isSimilar(x: T): Boolean 
  def isNotSimilar(x: T): Boolean = !isSimilar(x) 
} 

trait Moveable[T] {
  def move(other: T): T 
} 

abstract class Colorable(val color: Int)
class Point(var x: Int, var y: Int, color: Int) extends Colorable(color) with Similarity[Point] with Moveable[Point] {
  def isSimilar(that: Point): Boolean = if (that == null) { 
    false 
  } else if (Math.abs(x - that.x) < threshold && Math.abs(y - that.y) < threshold) { 
    true 
  } else { 
    false 
  } 
  override def move(other: Point) = {
    this.x = other.x 
    this.y = other.y 
    this 
  }
} 
```

- 匿名类
```scala
trait SimpleTrait {
 def add1(x: Int) = { x + 1 }
 def add2(x: Int) 
} 

abstract class SimpleClass {
 def add3(x: Int) = { x + 3 }
 def add4(x: Int) 
} 

val a = new SimpleClass {
 def add4(x: Int) = x + 4 
} 

val a = new SimpleTrait {
 def add2(x: Int) = x + 2
} 

val a = new SimpleClass with SimpleTrait {
 def add2(x: Int) = x + 2
 def add4(x: Int) = x + 4 
} 
```

- 对象与内部类
```scala
object DBManager {
  class ResultSet()
  class Connection()
  
  def createConnection(url: String, user: String, pass: String): Connection = new Connection()
  def createResultSet(conn: Connection, sql: String): ResultSet = new ResultSet()
}

val conn = DBManager.createConnection("","","")
val rs = DBManager.createResultSet(conn, "")

object MySharp extends Colorable(12)
object Base extends Point(0, 0)

case object RED extends Colorable(0xFF0000)
case object GREEN extends Colorable(0xFF00)
case object BLUE extends Colorable(0xFF)
```

- Implicit Class
```scala
object Helpers {
 implicit class IntWithTimes(x: Int) {
   def times[A](f: => A): Unit = {
     def loop(current: Int): Unit = if(current > 0) {
       f 
       loop(current - 1)
     }
    
     loop(x)
   }
  }
} 

import Helpers._ 

5.times(println("HI")) 
```

- 更为复杂的例子
```scala
// example 1
class RDD(data: String) {
 def collect() = data 
 def compute() = println("processing..") 
} 
abstract class SQLContext {
 def env: String 
 def sql(strText: String): RDD = new RDD(s"[$env]result of executing $strText") 
} 
object LocalMode {
  implicit object sqlContext extends SQLContext {
    def env = "LocalMode" 
  } 
} 
object ClusterMode {
  implicit object sqlContext extends SQLContext {
    def env = "ClusterMode" 
  }
} 
object myApp {  // will search SQLContext instance from the scope 
  implicit class SqlExecutor(sql: String)(implicit context: SQLContext) {
    def run = {
      val rdd = context.sql(sql) 
      rdd.compute() 
      rdd.collect() 
    } 
  } 
} 

import myApp._ 
import ClusterMode._ // import LocalMode._ 
"select * from src".run 
```

- Operator
```scala
case class N(x: Int) {
 def +(that: N) = N(x + that.x)
 def add(that: N) = this + that
 def -(that: N) = N(x - that.x)
 def minus(that: N) = this - that
 def - = N(-x)
 def negate = this -
 def unary_- = this -
} 

-(N(2) + N(3)) // functional style
((N(2).+(N(3)))).- // OO style 
```

- 对象与伴生对象（Companion Object）
```scala
class Point (val x: Int, val y: Int, val color: Int) 
object ConstantShape {
 val defaultColor = Int.MinValue 
} // object is singleton 

object Point { 
// companion object with the same name of the class, must defined in the file of the class
  val defaultColor = ConstantShape.defaultColor 
// built-in function 
  def apply(x: Int, y: Int): Point = new Point(x, y, defaultColor) 
  def apply(x: Int, y: Int, color: Int): Point = new Point(x, y, color) 
} 

Point(1, 2) // Point.apply(1, 2), without the companion object we have to add the “new” e.g. val a = new Point(1,2,3) 
Point(1, 2, 3) // Point.apply(1, 2, 3) 
```

```scala
class Row(val values: Array[Any]) {
 def apply(ordinal: Int): Any = values(ordinal)
 def apply(ordinal: Int, defaultValue: Any): Any = {
   if (values(ordinal) == null) {
     defaultValue
   } else { 
     values(ordinal)
   }
  }
  def update(ordinal: Int, value: Any) {
    values(ordinal) = value
  }
}

val row = new Row(Array(1, "abc")) 
row(0) = null // update 
println(row(0)) // call the apply version 1 
println(row(0, "aaaa")) // call the apply version 2 
```

- Explicity Self-Type引用
```scala
abstract class ConnectionPool {
  def getConnection: java.sql.Connection
} 

abstract class MySQLConnectionPool extends ConnectionPool {
  def getConnection: java.sql.Connection = {
    println("getting MySQL connection")
    null
  }
} 

trait SQLServerConnectionPool extends ConnectionPool {
  def getConnection: java.sql.Connection = {
    println("getting SQL Server connection") 
    null 
  } 
} 

trait UserDao {
  self: ConnectionPool => 
  def authenticate(user: String, pass: String) = { 
    val conn = getConnection 
    println ("authenticating..") 
    true 
  } 
} 

trait BusinessDao { 
  self: ConnectionPool => 

  def makeDeal(fromUser: String, toUser: String, deal: Int) {
    val conn = getConnection 
    println(s"making deal $fromUser -> $toUser: deal: $deal")
  } 
} 

// Dependencies Injection 
val dao = new SQLServerConnectionPool with UserDao 
dao.authenticate("user1", "pass1") 

val dao2 = new MySQLConnectionPool with UserDao with BusinessDao 
dao2.authenticate("user1", "pass1") 
```

- 闭包(Clousre)
```java
public static void main(String[] args) {
  int x = 0; 
  new Thread(new Runnable() { 
    public void run() { 
      x = 1;  // compilation error 
    } 
  }).start(); 
} 
```

```scala
var constant=1 
def addX(x: Int) = x + constant 
def updateConstant(x: Int) {
  constant = x 
} 

println(addX(100)) // prints 101 
updateConstant(100) 
println(constant) // prints 100 
println(addX(100)) // prints 200 
constant=2 
println(addX(100)) // prints 200

```
