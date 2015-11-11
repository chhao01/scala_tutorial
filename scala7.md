<em>请勿进行商业转载，学习交流转载请注明出处</em>

# 模式匹配(Pattern Matching) #
- 值匹配
```java
String result = null;
switch choice {
  case 0:
     result = "no";
     break;
  case 1:
  case 2:
  case 3:
     result = "yes";
     break;
  default:
     result = "error";
     break;
}
```

```scala
def toYesOrNo(choice: Int): String = choice match {
  case 1 => "yes" 
  case 0 => "no" 
  case _ => "error" 
} 

def fib(n: Int): Int = n match {
  case 0 => 1
  case n => n * fib(n - 1)
}
  
def toYesOrNo(choice: Int): String = choice match {
  case 1 | 2 | 3 => "yes" 
  case 0 => "no" 
  case _ => "error" 
} 

def parseArgument(arg: String) = arg match {
  case "-h" | "--help" => displayHelp 
  case "-v" | "--version" => displayVerion 
  case whatever => unknownArgument(whatever) 
}

val companies = Seq(
  ("Apple", "Steve Jobs"),
  ("Pixar Animation Studios", "Steve Jobs"),
  ("Microsoft", "Bill Gates"),
  ("Baidu", "Robin Li"))
  
companies.find(_ match {
  case ("Apple", _) => true
  case (_, "Steve Jobs") => true
  case _ => false
})

val JobsRE = """(.*), (?i)(Steve Jobs)""".r //
val companies = Seq(
  "APPLE INC, Steve Jobs",
  "Pixar Animation Studios, STEVE JOBS",
  "MICROSOFT, Bill Gates"
)
companies.foreach(_ match {
  case JobsRE(company, founder) => println (s"Founder: $founder, Company: $company")
  case _ =>
})
```

- 类型匹配
```scala
def f(x: Any): String = x match {
  case i: Int => "integer: " + i 
  case _: Double => "a double" 
  case s: String => "I want to say " + s 
} 

def f(x: Any): String = x match {
  case i @ (1 | 2 | 3) => s"this is a special integer $i"
  case i: Int => "integer: " + i 
  case _: Double => "a double" 
  case s: String => "I want to say " + s
  case unexpected => s"I don't know what it is -> $unexpected"
}

// This is a warning cause type erasure
def meth[A](xs: List[A]) = xs match {
  case _: List[String] => "list of strings"
  case _: List[Int] => "list of integers"
}
meth(List("abc", "123"))
meth(List(1, 2, 3))

// This is correct
import scala.reflect.runtime.universe._

def meth[A : TypeTag](xs: List[A]) = typeOf[A] match {
  case t if t =:= typeOf[String] => "list of strings"
  case t if t <:< typeOf[Int] => "list of integers"
}
meth(List("abc", "123"))
meth(List(1, 2, 3))
```

- 值萃取
```scala
// Case Class 
case class Address(street: String, county: String, state: String, country: String) 

def extract(addr: Address) = addr match {
  case a @ Address(_, _, _, "US") => s"$a is in North America" 
  case a @ Address(_, _, _, country) => s"$a is not a US address, but $country" 
} 


println(extract(Address("ZhongShan RD No.1", "HuangPu", "ShangHai", "China"))) 
println(extract(Address("Main ST 1", "Sunnyvale", "CA", "US"))) 


def extract(addr: Address) = addr match {
  case a @ Address(_, _, _, "US") => s"$a is in North America" 
  case a @ Address(_, _, s, c) => s"$a is not a US address, but $c, $s" 
} 
```
```scala
// unapply function 
trait User { def name: String } 
class FreeUser(val name: String) extends User 
class PremiumUser(val name: String, val level: Int) extends User 
object FreeUser {
  def apply(name: String): FreeUser = new FreeUser(name)
  def unapply(user: FreeUser): Option[String] = Some(user.name) 
} 

object PremiumUser {
  def unapply(user: PremiumUser): Option[(String, Int)] = Some((user.name, user.level)) 
} 

val user1: User = new PremiumUser("Daniel", 3) 
val user2: User = FreeUser("Robert") 
def sayHi(user: User) = user match {
  case FreeUser(name) => println(s"Hello $name") // FreeUser.unapply called
  case PremiumUser(name, level) => println(s"Welcome back, Level $level user $name") // PremiumUser.unapply called 
} 

sayHi(user1) 
sayHi(user2) 

object User {
  def apply(name: String) = new FreeUser(name)
  def apply(name: String, level: Int) = new PremiumUser(name, level)
  def unapplySeq(line: String): Option[Seq[String]] = {
    if (line == null) None else Some(line.split(",").toSeq)
  }
}

val lines = Seq("Hao", "Hao Cheng,1", "Tom,2", "Invalid User Entry,,3")
lines.flatten(_ match {
  case User(name) => User(name) :: Nil
  case User(name, level) => User(name, level.toInt) :: Nil
  case _ => Nil
})
```

- 集合元素匹配
```scala
def length[A](list : List[A]) : Int = list match {
  case _ :: tail => 1 + length(tail) 
  case Nil => 0 
} 
val seq = Seq(1, 2, 3, 4) 
seq match {
  case a :: b :: tail => println (s"first two [$a] [$b]") 
  case _ => sys.error("not found") 
} 

seq match { 
  case a :: _ :: _ :: last :: Nil => println (s"first and last [$a] [$last]") 
  case _ => sys.error("not found") 
} 

val seq1 = Seq(1, 2, 3, 4) 
val seq2 = Seq(2, 3, 4) 
def matchFirst2(seq: Seq[Int]) = seq match {
  case 1 :: 2 :: tail => println ("first two element is 1 and 2") 
  case _ => println ("first two element is not 1 and 2") 
} 

matchFirst2(seq1) 
matchFirst2(seq2)
```

```scala
object UserParser {
  def unapply(line: String): Option[User] = {
    line match {
      case null => None
      case s => s.split(",") match {
        case Array(name) => Some(new FreeUser(name))
        case Array(name, level) if level.nonEmpty => Some(new PremiumUser(name, level.toInt))
        case _ => None
      }
    }
  }
}
lines.foreach( line => line match {
  case UserParser(FreeUser(name)) => println(s"Hello $name")
  case UserParser(PremiumUser(name, level)) => println(s"Welcome back, Level $level user $name")
  case _ => println (s"Invalid format for $line")
})
```

```scala
val map = Map("a" -> 1, "ab" -> 2, "abc" ->3)

map.get("a") match {
  case Some(value) => println (value)
  case None => println("Not Found")
}

```

- Pattern Guard
```scala
case class User(name: String, age: Int) 
val user1: User = new User("Daniel", 3) 
val user2: User = new User("Robert", 24) 
def permit(user: User) = user match {
  case User(name, age) if (age >= 18 && age <= 100) => println(s"Hello $name, welcome!") 
  case User(name, _) => println(s"Sorry $name, you must be older than 18") 
} 

permit(user1) 
permit(user2) 
```

- Exception & Exception Catching
```scala
case class TooYoungException(msg: String) extends Exception(msg)
case class User(name: String, age: Int)

def buyAlcohol(user: User) {
  if (user.age >= 18) {
     println (s"Enjoy it ${user.name}")
  } else {
     throw TooYoungException(s"${user.name} is too young to buy alcohol")
  }
}

try {
  buyAlcohol(User("Tom", 20))
  buyAlcohol(User("Kate", 15))
} catch {
  case TooYoungException(msg) => println (msg)
}
```

```scala
import java.io.FileNotFoundException
import java.io.IOException
import java.io.FileInputStream
import scala.io.Source

def printFile1(file: String): Unit = {
  try {
    val fr = new FileInputStream(file)
    Source.fromInputStream(fr).getLines().foreach(println)
  } catch {
    case ex: FileNotFoundException => println("File Not Found")
      throw ex
    case ex: IOException => println("IO Exception")
      throw ex
  } finally {
  }
}

import scala.util.Success
import scala.util.Failure
import scala.util.Try

def printFile2(file: String): Unit = {
  Try(new FileInputStream(file)).flatMap(fr =>
    Try(Source.fromInputStream(fr))).flatMap(bs =>
      Try(bs.getLines())) match {
    case Success(lines) => lines.foreach(println)
    case Failure(ex: FileNotFoundException) => println("File Not Found")
    case Failure(ex: IOException) => println("Error in reading")
    case Failure(ex) => println(ex.getMessage)
  }
}
```

- 例子：四则运算
```scala
abstract class Expr 
case class Number(num: Double) extends Expr 
case class UnOp(operator: String, arg: Expr) extends Expr 
case class BinOp(operator: String, left: Expr, right: Expr) extends Expr 
def simplifyAll(expr: Expr): Expr = expr match {
  case UnOp("-", UnOp("-", e)) => simplifyAll(e) // `-' is its own inverse 
  case BinOp("/", e1, e2 @ Number(d)) if d == 0=> sys.error("cannot divide 0") 
  case BinOp("+", Number(0), e)=> simplifyAll(e) // `0' is a neutral element for `+' 
  case BinOp("*", Number(1), e) => simplifyAll(e) // `1' is a neutral element for `*' 
  case UnOp(op, e) => UnOp(op, simplifyAll(e)) 
  case BinOp(op, l, r) => BinOp(op, simplifyAll(l), simplifyAll(r)) 
  case _ => expr 
} 

// -(-(0+2)) 
val expr = UnOp("-", UnOp("-", BinOp("+", Number(0), Number(2)))) 
simplifyAll(expr) 

// -(-(2/0)) 
val expr = UnOp("-", UnOp("-", BinOp("/", Number(2), Number(0)))) 
simplifyAll(expr) 
```