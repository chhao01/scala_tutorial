# 安装Scala #
- Download Scala （http://www.scala-lang.org/download/）
- Java Runtime (1.6 or Above)
- 设置好SCALA_HOME和PATH
- IDE: Scala-IDE(Eclipse) / IntelliJ + Scala Plugin

# 第一个程序 （Hello World）#
- 编译&运行
```scala
1.scala: 
object HelloWorld { 
  def main(args: Array[String]) { 
  println("Hello, world!") 
  } 
} 

hcheng@chenghao:~/home/hcheng/scalac 1.scala 
hcheng@chenghao:~/home/hcheng/scala HelloWorld 
```
- 在Scala Shell中执行
```scala
hcheng@chenghao:~/SCALA_HOME/bin/scala

scala>println("Hello, world!")
Hello, world！
scala>
```
- 当作普通脚本运行
```shell
2.scala：
println("Hello, world!")

$/tmp>scala 2.scala
Hello, world！
```
- 当作Shell脚本运行
```shell
file: script.sh
#!/bin/sh
exec scala "$0" "$@"
!#
object HelloWorld {
  def main(args: Array[String]) {
    println("Hello, world! " + args.toList)
  }
}
HelloWorld.main(args)
$/tmp>chmod +x script.sh
$/tmp>./script.sh
Hello, world！
```



