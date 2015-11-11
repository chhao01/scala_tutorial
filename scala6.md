# 集合操作 #
- 不可变集合与可变集合（Immutable V.S Mutable）
```scala
import scala.collection.mutable
import scala.collection // default imported
import scala.collection.immutable
```
- 集合类图
1. 集合基类接口
![](https://camo.githubusercontent.com/32e9285bc62a80e8030351de6333d9ef688aa0cd/687474703a2f2f692e737461636b2e696d6775722e636f6d2f62535679412e706e67)
2. 不可变集合类图
![](https://camo.githubusercontent.com/3866e60cebe33f8bf06c1cb5aabe970b0529b2a0/687474703a2f2f692e737461636b2e696d6775722e636f6d2f32666a6f412e706e67)
3. 可变集合类图
![](https://camo.githubusercontent.com/8f5b6495059f0fe00e2903f1c3e02e213a1ea1d1/687474703a2f2f692e737461636b2e696d6775722e636f6d2f447370746c2e706e67)

http://docs.scala-lang.org/overviews/collections/performance-characteristics.html

- 举例
1. 创建不可变集合
```scala
Traversable()             // An empty traversable object
List()                    // The empty list
List(1.0, 2.0)            // A list with elements 1.0, 2.0
Vector(1.0, 2.0)          // A vector with elements 1.0, 2.0
Range(1, 10)			  // Range
(1 to 10)                 // Range
('a' -> 1)                // Tuple
('a', 1)                  // Tuple∂
Iterator(1, 2, 3)         // An iterator returning three integers.
Set("dog", "cat", "bird") // A set of three animals
Map('a' -> 7, 'b' -> 0)     // A map from characters to integers


// 并集
Set("dog", "cat", "bird") | Set("tiger", "cat")
Set("dog", "cat", "bird") union Set("tiger", "cat")
Set("dog", "cat", "bird") ++ Set("tiger", "cat")

// 交集
Set("dog", "cat", "bird") & Set("tiger", "cat")
Set("dog", "cat", "bird") intersect Set("tiger", "cat")

// 差集
Set("dog", "cat", "bird") -- Set("tiger", "cat")
Set("dog", "cat", "bird") diff Set("tiger", "cat")


val seq = Seq("a", "b", "c")
val seq = Nil.::("c").::("b").::("a") // Nil a object of List[Nothing]() // Contra-variant & Co-variant
val seq = "a" :: "b" :: "c" :: Nil 
val seq2 = seq :+ "d" // add to the end
val seq3 = "0" +: seq // add to the begin

val queue = scala.collection.immutable.Queue(1, 2, 3)
val queue2 = queue.enqueue(1.2f) //  scala.collection.immutable.Queue[AnyVal] = Queue(1, 2, 3, 1.2)
val queue3 = queue.enqueue("a") // scala.collection.immutable.Queue[Val] = Queue(1, 2, 3, a)

val stack = scala.collection.immutable.Stack(1,2,3) // scala.collection.immutable.Stack[Int] = Stack(1, 2, 3)
val stack1 = stack.push(4) // scala.collection.immutable.Stack[Int] = Stack(4, 1, 2, 3)
val stack2 = stack1.pop // scala.collection.immutable.Stack[Int] = Stack(1, 2, 3)
val stack3 = stack1.pop // scala.collection.immutable.Stack[Int] = Stack(1, 2, 3)
```

2. 创建可变集合
```scala
val buf = scala.collection.mutable.ListBuffer.empty[Int] // Invariant
buf += 1
buf += 10
buf.insert(1, 2)
buf.insert(2, 3, 4, 5, 6, 7, 8, 9)
val a = buf.apply(0)
val a = buf(0)
buf.update(0, -100)
buf(0) = -100

buf ++ (Seq(11, 12, 13))
val buffer = new scala.collection.mutable.ArrayBuffer[Int]()
val list = new scala.collection.mutable.LinkedList[String]()

val hashSet = new scala.collection.mutable.HashSet[String]()
hashSet.add("dog")
hashSet + ("bird")
hashSet + ("spark")
hashSet.remove("spark")
hashSet - ("spark")
hashSet.add("Spark")
hashSet.contains("Spark")
hashSet.contains("spark")
hashSet("spark")
hashSet ++ (Set("dog", "cat", "bird"))
hashSet ++ (Seq("dog", "cat", "bird"))
val hashSet2 = hashSet += ("tiger")
val hashSet3 = hashSet -= ("bird")

val hashMap = new scala.collection.mutable.HashMap[String, Int]()
hashMap.put("dog", 3)
hashMap.put("bird", 4)
hashMap.put("spark", 5)
hashMap + (("elephant", 6))
val hashMap2 = hashMap += (("elephant", 6))
hashMap - ("elephant")
val hashMap2 = hashMap -= ("elephant")
hashMap ++ (Seq(("snake", 7), ("mouse", 8)))

val v1 = hashMap.get("spark")
val v2 = hashMap.apply("spark")
val v2 = hashMap("spark")
val v3 = hashMap.getOrElse("sparK", s"this is the default value")
hashMap.remove("spark")
hashMap.update("spark", 5)
hashMap("spark") = 5
hashMap.keySet
hashMap.keys
hashMap.keysIterator
hashMap.values
hashMap.valuesIterator

val queue = scala.collection.mutable.Queue(1, 2, 3)
queue.enqueue("a") // exception java.lang.NumberFormatException: For input string: "a"
queue.enqueue(4)
queue.enqueue(5)
queue.dequeue // 1
queue.dequeue // 2
queue.dequeue // 3
queue.dequeue // 4
queue.dequeue // 5
queue.dequeue // java.util.NoSuchElementException: queue empty
```

3. 数组
```scala
val a1 = Array(1, 2, 3)
val a2 = a1 map (_ * 3) // Array(3, 6, 9)

val array1 = new Array[Int](5)
for (i <- 1 to 5) array1(i - 1) = i
val array2 = Array(1, 2, 3, 4, 5)
val array3 = Array.tabulate(4) { i => i * 10 } // 0, 10, 20, 30

def printArray(arrs: Int*) {
   println(s"Length:${arrs.length}")
   println(s"""values:${arrs.mkString(",")}""")
}
printArray(1, 2, 3)
printArray(array2: _*)
printArray()

def printArray2(first: Int, arrs: Int*) {
   println(s"Length:${arrs.length + 1}")
   println(s"""values:$first and ${arrs.mkString(",")}""")
}
```

4. 集合操作
```scala
for (i <- 1 to 5) yield i // 1, 2, 3, 4, 5
for (i <- 1 until 5) yield i // 1, 2, 3, 4
for (i <- 1 to 5) yield i % 2
for (i <- 0 to 5; // nested loop
      j <- i to 5)
    yield Pair(i, j)

val a = Array(1, 2, 3, 4, 5)

// for loop yield over Collection
for (e <- a) yield e * 2

// for loop, yield, and guards (for loop 'if' conditions)
for (e <- a if e > 2) yield e

// def foreach[U](f: A =>  U): Unit
a.foreach(mbr => println(mbr)) // iterate all of the elements in the collection

// def map[B, That](f: A => B): That
val transformed = a.map(_ + 20) // _ + 20 is a anonymous functions

// def flatMap[B, That](f: A => GenTraversableOnce[B]): That
List(List(1, 2), List(3, 4)).flatMap(x => x.map(_ * 2))

// flatten
List(List(1, 2), List(3, 4)).flatten

// def filter(p: A => Boolean): Traversable[A]
val result = a.filter((i: Int) => i % 2 == 0) 
val result = a.filter(_ % 2 == 0)

// zip
List(1, 2, 3).zip(List("a", "b", "c")).map { case (a, b) => 
    b + a
}

// zipWithIndex
val pairs = List('a', 'b', 'c', 'd','e').zipWithIndex
pairs.foreach { pair => println (s"idx: ${pair._2}, elements:${pair._1}") }

// def slice(from: Int, until: Int): Traversable[A]
List('a', 'b', 'c', 'd', 'e').slice(2, 3)

// def count(p: A => Boolean): Int
List('a', 'b', 'c', 'a', 'd', 'e').count(_ == 'a')

// def size: Int
List('a', 'b').size
Seq(1, 2, 3).size
(1 :: 2 :: 3 :: Nil).size

// def isEmpty: Boolean
List[Int]().isEmpty
List(1, 2, 3).isEmpty

// def head: A
List(1, 2, 3).head
List[Int]().head

// def headOption: Option[A]
List(1, 2, 3).headOption
List[Int]().headOption

// def last: A
List(1, 2, 3).last
// def lastOption: Option[A]
List(1, 2, 3).lastOption
List[Int]().lastOption

// def tail: Traversable[A]
List(1, 2, 3).tail
List[Int]().tail // Exception
Nil.tail         // Exception

// def forall(p: A => Boolean): Boolean
List(1, 2, 3).forall(_ > 0)

// def exists(p: A => Boolean): Boolean
List(1, 2, 3).exists(_ == 2)

// def count(p: A => Boolean): Int
List(1, 2, 3, 2).count(_ == 2)

// def find(p: A => Boolean): Option[A]
List(1, 2, 3, 2).find(_ == 2)
List(1, 2, 3, 2).find(_ == 4)

// def ++ [B >: A, That](xs: GenTraversableOnce[B]): That
List(1, 2, 3) ++ List(4, 5, 6)
Seq(1, 2, 3) ++ List(4, 5, 6)

// def take(n: Int): Traversable[A]
List(1, 2, 3, 4, 5, 6, 7, 8).take(4)

// def partition(p: A => Boolean): (Traversable[A], Traversable[A])
val (odds, evens) = Array(1,2,3,4,5,6,7).partition(_ % 2 == 1)

// def groupBy[K](f: A => K): Map[K, Traversable[A]]
val result: Map[Int,Array[Int]] = Array(1,2,3,4,5,6,7).groupBy(_ % 3)

// find
Array(1,2,3,4,5).find(_ > 3) // Some(4)

// exists
Array(1,2,3,4,5).exists(_ > 3) // true

// def drop(n: Int): Traversable[A]
Array(1,2,3,4,5,6,7,8,9).drop(5)

// def takeWhile(p: A => Boolean): Traversable[A]
Array(1,2,3,1,1,1,2,2,9).takeWhile(_ < 3) // first N match the predicate

// def dropWhile(p: A => Boolean): Traversable[A]
Array(1,2,3,1,1,6,7,8,9).dropWhile(_ < 3)

// def span(p: A => Boolean): (Traversable[A], Traversable[A])
Array(1,2,3,1,1,6,7,8,9).span(_ < 3)

// def splitAt(n: Int): (Traversable[A], Traversable[A])
Array(1,2,3,1,1,6,7,8,9).splitAt(4)
Seq(1,2,3,1,1,6,7,8,9).splitAt(4)

// def sortWith(lt : (A,A) => Boolean): Traversable[A]
Array(1,2,3,1,1,1,2,2,9).sortWith( _ > _)

// def sorted[B >: A](implicit ord: Ordering[B])
Array(1,2,3,1,1,1,2,2,9).sorted

// def sortBy[B](f: A => B)(implicit ord: Ordering[B])
Array(1,2,3,1,1,1,2,2,9).sortBy(_ % 5)

// def foldLeft[B](z: B)(op: (B, A) => B): B
Array[Int]().foldLeft(0) { (sum: Int, e: Int) => println(s"sum:$sum e:$e"); sum + e }
Array(1).foldLeft(0) { (sum: Int, e: Int) => println(s"sum:$sum e:$e"); sum + e }
Array(1,2,3,4,5,6,7,8,9).foldLeft(0) { (sum: Int, e: Int) => println(s"sum:$sum e:$e"); sum + e }

// def /: [B](z: B)(op: (B, A) => B): B
(0 /: Array(1,2,3,4,5,6,7,8,9)) { (sum: Int, e: Int) => println(s"sum:$sum e:$e"); sum + e }

// def foldRight[B](z: B)(op: (A, B) => B): B
Array[Int]().foldRight(0) { (e: Int, sum: Int) =>println(s"sum:$sum e:$e"); sum + e }
Array(1).foldRight(0) { (e: Int, sum: Int) =>println(s"sum:$sum e:$e"); sum + e }
Array(1,2,3,4,5,6,7,8,9).foldRight(0) { (e: Int, sum: Int) =>println(s"sum:$sum e:$e"); sum + e }

// def :\ [B](z: B)(op: (A, B) => B): B
(Array(1,2,3,4,5,6,7,8,9) :\ 0) { (e: Int, sum: Int) =>println(s"sum:$sum e:$e"); sum + e }

// override def reduceLeft[B >: A](op: (B, A) => B): B
Array(1).reduceLeft( (prev: Int, cur: Int) => {println(s"prev:$prev curr:$cur"); prev + cur })
Array(1,2).reduceLeft( (prev: Int, cur: Int) => {println(s"prev:$prev curr:$cur"); prev + cur })
Array(1,2,3,4,5,6,7,8,9).reduceLeft( (prev: Int, cur: Int) => {println(s"prev:$prev curr:$cur"); prev + cur })
Array[Int]().reduceLeft( (prev: Int, cur: Int) => {println(s"prev:$prev curr:$cur"); prev + cur })

// override def reduceLeftOption[B >: A](op: (B, A) => B): Option[B]
Array[Int]().reduceLeftOption( (prev: Int, cur: Int) => {println(s"prev:$prev curr:$cur"); prev + cur })
Array(1).reduceLeftOption( (prev: Int, cur: Int) => {println(s"prev:$prev curr:$cur"); prev + cur })
Array(1, 2, 3, 4, 5, 6, 7, 8, 9).reduceLeftOption( (prev: Int, cur: Int) => {println(s"prev:$prev curr:$cur"); prev + cur })

// override def reduceRight[B >: A](op: (A, B) => B): B
// override def reduceRightOption[B >: A](op: (A, B) => B): Option[B]

// def mkString(start: String, sep: String, end: String): String
Array(1, 2, 3, 4, 5).mkString("Seq(", ", ", ")")
Array(1).mkString("Seq(", ", ", ")")
Array[Int]().mkString("Seq(", ", ", ")")

// def mkString(sep: String): String
Array[Int]().mkString(",")
Array(1).mkString(",")
Array(1, 2, 3, 4, 5).mkString(",")

// def mkString: String
Array(1, 2, 3, 4, 5).mkString

// def init: Traversable[A] Select all of the element except the last one
Array(1, 2, 3, 4, 5).init
Array(1).init
Array[Int]().init

// collect
val mixedList = List("a", 1, 2, "b", 19, 42.0) //this is a List[Any]
val results = mixedList collect {
  case s: String => "String:" + s
  case i: Int => "Int:" + i.toString 
  // 42.0 doesn't match any of the partial function, will be ignored
}
```

5. Option
Option Trait 有两个子类：
Some[T] & None 
```scala
trait Option[T] {
  def isDefined: Boolean
  def isEmpty: Boolean
  def get: T
  def getOrElse(t: T): T
  final def map[B](f: A => B): Option[B]
  final def orNull[A1 >: A](implicit ev: Null <:< A1): A1
}

val numbers = Map(1 -> "one", 2 -> "two", 3 -> null)
numbers.get(2) // Some("two")
numbers.get(3) // Some(null)
numbers.get(4) // None

val m = Map(2 -> "Two", 1 -> "One")
val result = m.getOrElse(1, "Unknow")

val m2 = new collection.mutable.HashMap[Int, String]()
m2.getOrElseUpdate(1, "One")
m2.getOrElseUpdate(2, "Two")

println(m2(1))
```

6. 集合类型转化
```scala
val a = List(1, 2, 3, 4)
val b = Array(1, 2, 3, 4)

// def copyToBuffer[B >: A](dest: Buffer[B])
import scala.collection.mutable.ArrayBuffer
val a = List(1, 2, 3, 4, 5)
val buffer = new ArrayBuffer[Int]()
a.copyToBuffer(buffer)
buffer.foreach(println)

// def copyToArray[B >: A](xs: Array[B], start: Int, len: Int)
val a = List(1, 2, 3, 4)
val c = new Array[Int](10)
a.copyToArray(c, 1, a.length)
c.foreach(println)

// def copyToArray[B >: A](xs: Array[B], start: Int)

// def toArray[B >: A : ClassTag]: Array[B]
a.toArray

// def toList: List[A]
b.toList

// def toSeq: Seq[A]
a.toSeq
b.toSeq

// def toStream: Stream[A]
a.toStream.map(_ + 3).foreach(println)

Seq((1, "a"), (2, "b"), (3, "c")).toMap
```
  
7. Java Collection & Scala Collection
```scala
import scala.collection.JavaConversions._ // implicit function

scala.collection.Seq         => java.util.List
scala.collection.mutable.Seq => java.util.List
scala.collection.Set         => java.util.Set
scala.collection.Map         => java.util.Map
java.util.Properties         => scala.collection.mutable.Map[String, String]

Iterator               <=>     java.util.Iterator
Iterator               <=>     java.util.Enumeration
Iterable               <=>     java.lang.Iterable
Iterable               <=>     java.util.Collection
mutable.Buffer         <=>     java.util.List
mutable.Set            <=>     java.util.Set
mutable.Map            <=>     java.util.Map
mutable.ConcurrentMap  <=>     java.util.concurrent.ConcurrentMap

val a = Seq(1,2,3,4,5) // a: Seq[Int] = List(1, 2, 3, 4, 5)
val b: java.util.List[Int] = a // b: java.util.List[Int] = [1, 2, 3, 4, 5]

val a = new java.util.LinkedList[String]()
a.add("aaa")
a.add("bbb")
a.add("ccc")

a.foreach(println)
a.mkString(",")
val b = a.toList
b.takeRight(1)

val c: java.util.List[String] = b
```

8. 等价(Equality)
```scala
Array(1, 2, 3) == Array(1, 2, 3) // false
Array(1, 2, 3) sameElements Array(1, 2, 3) // true
Set(1, 2, 3) == List(1, 2, 3) // false
List(1, 2, 3) == Array(1, 2, 3) // false
List(1, 2, 3) == Vector(1, 2, 3) // true
List(1, 2, 3) == collection.mutable.LinkedList(1, 2, 3) // true
import collection.mutable.{HashSet, TreeSet}
HashSet(1, 2) == TreeSet(2, 1) // true
import collection.mutable.{HashMap, ArrayBuffer}
Map(1 -> "a") == HashMap(1 -> "a") // true
```

9. 迭代器(iterators)
定义：
```scala
abstract class Iterator[+A] {
  def hasNext: Boolean
  def next: A
}

// how we iterate the iterator
while (it.hasNext) 
  println(it.next())
```
例子：
```scala
val it = Iterator("a", "b", "c")
val it1 = Iterator("a", "b", "c")
val it2 = Iterator(1, 2, 3)
val it = it1 ++ it2
it.foreach(println)

val it3 = Iterator(4,5,6)
val (it1, it2) = it3.duplicate // it3 is invalid
it1.foreach(println)
it2.foreach(println)

```

```scala
  val newIt = (1 to 10).toIterator
  val it4 = newIt.map { i =>
    println(s"This is looping it4 $i")
    i
  }

  it4.contains(3)
  it4.contains(5)
  it4.foreach(println)
```

```scala
  val newIt2 = (1 to 10).toIterator
  val newIt3 = newIt2.map { idx =>
    println(s"add 1 for $idx")
    idx + 1
  }
  val newIt4 = newIt3.map { idx =>
    println(s"multiply for $idx")
    2 * idx
  }
  
  val new2 = (1 to 10)
  val new3 = new2.map { idx =>
    println(s"add 1 for $idx")
    idx + 1
  }
  val new4 = new3.map { idx =>
    println(s"multiply for $idx")
    2 * idx
  }
  
```
```scala
// def view
(1 to 1000000000).map(_ + 3).take(3) // takes long time
(1 to 1000000000).view.map(_ + 3).take(3) // lazy transformation & execution
(1 to 1000000000).view.map(_ + 3).take(3).force // takes less time

// def view(from: Int, until: Int): TraversableView[A, Traversable[A]]
(1 to 1000000000).view(5, 10).map(_ + 3).take(3) // lazy transformation & execution
(1 to 1000000000).view(5, 10).map(_ + 3).take(3).force // takes less time
```

```scala
Stream(1, 2, 3).map(x => {println(s"this is $x"); x}).take(1).foreach(println)
Stream.range(1,1000000000).map(_ + 3).takeWhile(_ < 10).force
```

10. 并行操作
(1 to 10000000).par.count(_ % 2 == 0)
(1 to 10000000).par.sum
(1 to 10000000).par.iterator.map(_ + 3).sum

11. 元组（Tuple）
从scala.Tuple1, scala.Tuple2 ... scala.Tuple22派生
```scala
val stuff = (42, "fish") // stuff: (Int, String) = (42,fish)
println(stuff._1) // 42
println(stuff._2) // fish
val stuff3 = (54, 21.3, "fish") // stuff3: (Int, Double, String) = (54,21.3,fish)
println(stuff._1) // 54
println(stuff._2) // 21.3
println(stuff._3) // fish
```

12. Product
Product是一个trait，Sub trait 有scala.Product1, scala.Product2 ... scala.Product22
```scala
trait Product extends Any with Equals {
  def productElement(n: Int): Any

  /** The size of this product   */
  def productArity: Int

  /** An iterator over all the elements of this product.   */
  def productIterator: Iterator[Any] = new scala.collection.AbstractIterator[Any]
}

trait RecordType {
  self: Product =>
  def records: String = (
    self.productIterator.map({ arg =>
      arg.getClass.getSimpleName + ":" + arg
    }).toSeq.mkString("\n"))
}
// case class天然就是Product
case class X(i: Int, c: Char, s: String) extends RecordType

// Tuple也天然就是Product
val stuff3 = (54, 21.3, "fish")
stuff3.productIterator.map { arg =>
  arg.getClass.getSimpleName + ":" + arg
}.toSeq.mkString("\n")

val x = X(5, 'd', "test")
println(x.records)
