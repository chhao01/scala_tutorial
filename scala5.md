# 泛型 #
- 基本泛型
```scala
case class Reference[T] (value: T) {
  def isEmpty = value == null
  def get: T = value
}
```

```scala
val add = new scala.Function2[Int, Int, Int] {
  override def apply(a: Int, b: Int): Int = a + b
}
add(1, 2)
```

- 多态方法
```java
class Helper {
    public <T> String mkString(T o) {
        if (o == null) {
            return "null";
        } else {
            return o.toString();
        }
    }
    public <T extends Number> long add(T a, T b) {
        return a.longValue() + b.longValue();
    }
}

public class Q2 {
  public static void main(String[] args) {
    Helper helper1 = new Helper();
    System.out.println(helper1.mkString("abc"));
    System.out.println(helper1.mkString(new java.util.Date()));
    System.out.println(helper1.add(123, 234));
    System.out.println(helper1.add(123.123, 234.234));
  }
}

```
```scala
trait Helper {
  def mkString[T](o: T): String = {
    if (o == null) {
      "null"
    } else {
      o.toString()
    }
  }

  def sum[N <: Number](a: N, b: N): Long = {
    a.longValue() + b.longValue()
  }
}

object a extends Helper

a.mkString("D")
a.mkString(new java.util.Date())  
a.mkString(1) 
a.mkString(null) 

a.sum(new Integer(3), new Integer(5))
a.sum(new java.lang.Long(3), new java.lang.Long(5))
```

- 不变（Invariant）
```java
java.util.List<Number> slist = new java.util.ArrayList<Number>();
slist.add(1);
slist.add(1L);
slist.add(1.0f);
slist.add(null);

// bug in java type system
Object[] arr = new Integer[1];
arr[0] = "Hello, there!"; // RuntimeException 
```

```scala
val a = new Array[Int](2) 
val a: Array[Any] = new Array[Int](2) // compile error 
a(0) = 1 
a(1) = 2 
a(0) = “test” // compilation error 
```

```java
public class Q2 {
    public static class ListNode<T> {
        public static final ListNode EMPTY = new ListNode(null, null);

        private final T head;
        private final ListNode<T> tail;

        private ListNode(T head, ListNode<T> tail) {
            this.head = head;
            this.tail = tail;
        }

        public T head() {
            return this.head;
        }

        public ListNode<T> tail() {
            return this.tail;
        }

        public ListNode<T> prepend(T newHead) {
            return new ListNode(newHead, this);
        }
    }

    public static void main(String[] args) {
        ListNode<Integer> nil = ListNode.EMPTY;
        ListNode<Integer> node = nil.prepend(123).prepend(234).prepend(345);
        ListNode<Integer> it = node;
        while(it.tail() != null) {
            System.out.println(it.head());
            it = it.tail();
        }
        
        ListNode<String> e = ListNode.EMPTY;
        // e.prepend("abc").prepend(123);  // error
        ListNode<Object> e1 = ListNode.EMPTY;
        e1.prepend("abc").prepend(123); // OK
    }
}
```

```scala
case class ListNode[T](head: T, tail: ListNode[T]) {
  def prepend(elem: T): ListNode[T] = ListNode(elem, this) 
} 

object ListNode {
  def empty[T]() = new ListNode(null.asInstanceOf[T], null.asInstanceOf[ListNode[T]])
}

val node = ListNode.empty[Int].prepend(123).prepend(234).prepend(345) // ListNode[Int]

var it = node
while(it.tail != null) {
  println(it.head)
  it = it.tail
}
```

ListNode.empty[Int].prepend(123).prepend(234) // ListNode[Int]

ListNode.empty[Int] //  ListNode[Int]
  .prepend(123)     //  ListNode[Int]
  .prepend(123.123d)//  ListNode[AnyVal]  compilation error
  .prepend("abc")   //  ListNode[Any]     compilation error
```

- 协变（Covariant）
```scala
trait Base[+T]
case class Child[+T](v: T) extends Base[T]

val a = Child(1) // A[Int] 
val b: Child[AnyVal] = a
val c: Child[Any] = a
val d: Base[Int] = a
val e: Base[AnyVal] = a
```

- 逆变（contra-variant）
```scala
// T   =>  class C[T], even if A <: B, C[A] <> C[B]
// +R  =>  class C[+T], if A <: B then C[A] <: C[B]
// -P  =>  class C[-T], if A <: B then C[A] >: C[B]

trait Function1[-P, +R] { 
  def apply(p: P): R 
}

def whatever(b: java.lang.Number): String = b.toString

val c: String = whatever(123)
val d: AnyRef = whatever(123.123d)
val a = whatever _

val b: (java.lang.Integer) => String = a
val c: (java.lang.Integer) => AnyRef = b
val e: (java.lang.Double) => AnyRef = a

trait Function2[-P1, -P2, +R] { 
  def apply(p1: P1, p2: P2): R 
}
import java.util.HashMap
val map = new java.util.HashMap[Number, Number]()
map.put(1L, 1.111d)
map.put(2, 2.222d)
map.put(2, 3.333d)
val a = map.put _ // (Number, Number) => Number
val b: (Integer, Integer) => Number = a
val c: (Integer, Integer) => Any = b
val d: (Integer, Integer) => Any = a
```

```scala
class C[+T](var value: T)
 // ==>
 
class C[+T] {
  private var value: T = _
  def value(): T = value
  def value_=(x: T): Unit = { // error
    this.value = x
  }
}

class C[-T](var value: T)
 // ==>
class C[-T](var value: T) {
  private var value: T = _
  def value(): T = value // error
  def value_=(x: T): Unit = {
    this.value = x
  }
}
```
- 上界（Upper Bound）
```java
public class A {
    public <T extends java.util.List<String>> T combine(T a, T b) {
        a.addAll(b);
        return a;
    }
}
public static void main(String[] args) {
    ArrayList<String> a = new ArrayList<String>();
    LinkedList<String> b = new LinkedList<String>();

    a.add("a");
    a.add("b");
    b.add("c");
    b.add("d");
    new A().combine(a, b);
}
```
```scala
import java.util.{List => JList, ArrayList => JArrayList} 
object CollectionTest { 
  def combine[T <:JList[String]](left: T, right: T): T = {
    left.addAll(right) 
    left 
  } 
} 
val a = new JArrayList[String]() 
val b = new JArrayList[String]() 
a.add("a") 
a.add("b") 
b.add("c") 
b.add("d") 
val c = CollectionTest.combine(a, b) 
```

- 下界（Lower Bound）
```scala
// Example 1
object Counter {
  def getNullCount[T >: Null](array: Array[T]) = {
    var count = 0
    for(v <- array) {
      if (v == null) {
        count += 1
      }
    }

    count
  }
}

val a = Array(1,2,3,4,5,6)
val b = Array("abc", null, "cba")
val c = Array(1, 2, 3.0f)
val d = Array(1, 2, "abc")

Counter.getNullCount(a) // error!
Counter.getNullCount(b)
Counter.getNullCount(c) // error!
Counter.getNullCount(d)

// Example 2
import java.util.{List => JList, ArrayList => JArrayList, LinkedList=>JLinkedList}
object CollectionTest {
  def combine[T >: JArrayList[String] <:JList[String]](left: T, right: T): T = {
    left.addAll(right)
    left
  }
}
val a = new JArrayList[String]()
val b = new JArrayList[String]()

a.add("a")
a.add("b")
b.add("c")
b.add("d")
val c = CollectionTest.combine(a, b)

val d = new JLinkedList[String]()
d.add("e")
d.add("f")
CollectionTest.combine(a, d)

// Example 3
case class Wrapper[A](value: A) {
  def isEmpty: Boolean = if (value == null) true else false

  def getOrElse[B >: A](default: => B): B =
    if (isEmpty) default else value
}
val v1 = 1
val v2 = "abc"
val v3 = null
val v4 = new java.math.BigInteger("123123123123")
val v5 = Array(1,2,3)

val a = Wrapper(v1).getOrElse("abc")
val b = Wrapper(v2).getOrElse("abc")
val c = Wrapper(v3).getOrElse("abc")
val d = Wrapper(v4).getOrElse("abc")
val e = Wrapper(v5).getOrElse("abc")
```

```scala
abstract class Node[+T] {
  def head: T
  def tail: Node[T]
  def prepend[U >: T](e: U): Node[U] = ListNode(e, this) 
}

case class ListNode[+T](head: T, tail: Node[T]) extends Node[T]

case object EmptyNode extends Node[Nothing] {
  def head: Nothing = ???
  def tail: ListNode[Nothing] = ???
}
val a = EmptyNode.prepend(234)
val b = a.prepend(345.123d)
val c = b.prepend("abc")

EmptyNode.prepend(234).prepend(345.123d).prepend("abc")
```

- 上下文边界（Context Bound）
```scala
// In package scala.math._
// implicit object BigIntIsIntegral .. extends Numeric[BigInt]
// implicit object DoubleIsFractional .. extends Numeric[Double]
// implicit object IntIsIntegral .. extends Numeric[Int]
// implicit object Char .. extends Ordering[Char]
// implicit object Int .. extends Ordering[Int]

def add[A](a: A, b: A)(implicit numeric: Numeric[A]) = numeric.plus(a, b) // Normal Implicit
add(123, 234) // --> OK, but how do we hide the (implicit numeric: Numeric[A])

// scala.Predef.implicitly function, which helpful for retrieving the implicit object by type
def implicitly[T](implicit e: T) = e // define an extra function

def implicitly[T](implicit e: T) = e // scala.Predef.implicitly
val orderingInt = implicitly[Ordering[Int]]
val compareInt = orderingInt.compare
val maxOfInt = orderingInt.max

def add[A] = implicitly[Numeric[A]].plus _   // Error
val func = add[Int]                          // Error
add[Int]()(123, 234)                         // Error

def add[A: Numeric] = implicitly[Numeric[A]].plus _ // Context Bound
val func = add[Int]
func(123, 234)

def add[A: Numeric](a: A, b: A) = implicitly[Numeric[A]].plus(a, b) // Context Bound
add(123, 234)
```
- 视图边界（View Bound）
```scala
object Formatter {
  case class XMLFormatter(value: Any) {
    def output(os: java.io.PrintStream) {
      os.println(s"<value> $value </value>")
    }
  }
  implicit def createIntPrinter(v: Int) = XMLFormatter(v)
  implicit def createStringPrinter(v: String) = XMLFormatter(v)
}

object ConsolePrinter {
  import Formatter._
  def print[A <% XMLFormatter](v: A): Unit = {
    v.output(System.out)
  }
}

ConsolePrinter.print(123)
ConsolePrinter.print("abc")
```

- ClassTag & TypeTag
```scala
def createArray[T](length: Int): Array[T] = new Array[T](length) // ERROR

import scala.reflect.ClassTag
def getType[T: ClassTag](v: T): ClassTag[T] = implicitly[ClassTag[T]]
getType("a")
getType(1)

def createArray[T: ClassTag](length: Int): Array[T] = implicitly[ClassTag[T]].newArray(length)
def createArray[T: ClassTag](length: Int): Array[T] = new Array[T](length)

def mkArray[T: ClassTag](v: T*): Array[T] = {
  val result = new Array[T](v.length) // Array[T](v: _*)
  var i = 0
  while(i < v.length) {
    result(i) = v(i)
    i += 1
  }
  result
}
mkArray(1,2,3)
mkArray("a", "bc", "d")
```
// more concrete example
```java
public static class Transform<FROM, TO> {
    public TO[] transform(FROM[] raw) {
        TO[] values = new TO[raw.length]; // compilation error; type eliminated by erasure
        for(int i = 0; i < raw.length; ++i) {
            values[i] = map(raw[i]);
        }
        return values;
    }
        
    private TO map(FROM v) {
        // do something
    }
}
```
```scala
object Traversal {
  import scala.reflect.ClassTag 
  def map[FROM, TO: ClassTag](input: Array[FROM], f: FROM => TO): Array[TO] = {
    val dest = new Array[TO](input.length) 
    var i = 0 
    while (i < input.length) { 
      dest(i) = f(input(i)) 
      i += 1 
    } 
    dest 
  } 
} 

val a = Array(1, 2, 3, 4, 5) 
val b = Traversal.map(a, (x: Int) => x.toDouble)
```
// TypeTag
```scala
import scala.reflect.runtime.universe._

def getType[T](t: T)(implicit tag: TypeTag[T]): Type = tag.tpe 
def getType[T : TypeTag](t: T): Type = typeOf[T]


getType((i: Double) => i.toString)
getType(1)
getType("abc")
getType(new java.util.ArrayList[java.util.LinkedList[String]])

typeOf[Int] <:< typeOf[AnyVal]
typeOf[AnyVal] <:< typeOf[Any]
typeOf[Int] =:= getType(1)
```

- Box & UnBox
a.scala
```scala
class Container[@specialized(Int,Double) T](value: T) {
  def apply(): T = value
}
```
scalac -print a.scala

- 抽象类型成员（Abstract Type Member）
```scala
// Abstract Type Memeber Version 
trait Getter { 
  type A 
  var x: A 
  def getX: A = x 
} 

trait Setter { 
  type A 
  var x: A 
  def setX(x: A) { 
    this.x = x 
  } 
} 

class IntFoo extends Getter with Setter { 
  type A = Int 
  var x = 123 
} 

class StringFoo extends Getter with Setter { 
  type A = String 
  var x = "hey" 
} 

// Or the Parameterized Type Version 
trait Getter[A] { 
  var x: A 
  def getX: A = x 
} 

trait Setter[A] { 
  var x: 
  A def setX(x: A) { 
    this.x = x 
  } 
} 

class IntFoo(var x: Int) extends Getter[Int] with Setter[Int] 
class StringFoo(var x: String) extends Getter[String] with Setter[String] 

// Difference Parameterized type V.S. Abstract Types
case class ParameterizedSome[A](value: A) { ...}    // Win
case class AbstractTypedSome(value: ???) {
  type A ...
}

// 1. Type alias
trait ConverterRegistry {
  type Converter = String => String
  def register(name: String, converter: Converter)
}

// 2. this.type
abstract class Wrapper[T] {
  def value: T
  def copy(): this.type
}
case class StringWrapper(value: String) extends Wrapper[String] {
  def copy(): this.type = { 
    StringWrapper(value.concat("")).asInstanceOf[this.type]
  }
}
val a = StringWrapper("abc").copy() // still the StringWrapper instance

// 3. Define the constraint among the types 
trait ConverterRegistry {
  type Name // no constraint
  type FROM <: TO // TO is the super type of FROM
  type TO >: Null // TO is the super type of Null
  type Converter = FROM => Array[TO] // type alias
  def register(name: String, converter: Converter)
  
  def name: Name
  def converter: Converter
}

object IntToNumberArrayRegistry extends ConverterRegistry {
  type Name = String
  type FROM = java.lang.Integer
  type TO = java.lang.Number
  
  var name: Name = _
  var converter: Converter = _
  def register(name: Name, converter: Converter) = {
    println(s"register... function $name")
    this.converter = converter
    this.name = name
  }
}

IntToNumberArrayRegistry.register("triple", (n: java.lang.Integer) => Array(n, n * 1.0f, n * 1.0d))

IntToNumberArrayRegistry.converter(2)
IntToNumberArrayRegistry.converter(3)



```