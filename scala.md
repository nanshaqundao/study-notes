# Sample Scala script

something similar to java functional interface..

```scala
package foo

object Example {
  def main(args: Array[String]): Unit = {
    println(hello())

    flexibleFizzBuzz(s => {})
    flexibleFizzBuzz(s => println(s))

    var i = 0
    val output = new Array[String](100)

    flexibleFizzBuzz { s =>
      output(i) = s
      i += 1
    }

    assert(output.take(15).sameElements(Array(
      "1",
      "2",
      "Fizz",
      "4",
      "Buzz",
      "Fizz",
      "7",
      "8",
      "Fizz",
      "Buzz",
      "11",
      "Fizz",
      "13",
      "14",
      "FizzBuzz"
    )))
  }

  def hello(): String = "Hello World"

  def flexibleFizzBuzz(callback: String => Unit): Unit = {
    println("Start")
    for (i <- Range.inclusive(1, 100)) {
      callback(
        if (i % 15 == 0) "FizzBuzz"
        else if (i % 3 == 0) "Fizz"
        else if (i % 5 == 0) "Buzz"
        else i.toString
      )
    }
    println("End")
  }
}
```

Sample practice with file read/write

```scala
import foo.Example.hello

object Example3 {
  def main(args: Array[String]): Unit = {
    println("hi")

    var outcome = withFileWrite("my.txt"){ writer =>
      writer.write("Hello\n")
      writer.write("world\n")
    }

    val result = withFileReader("my.txt"){reader =>
      reader.readLine() + "\n" + reader.readLine()
    }
    assert(result ==  "Hello\nworld")

  }

  def withFileWrite[T](fileName: String)(handler: java.io.BufferedWriter => T) = {
    val output = java.nio.file.Files.newBufferedWriter(java.nio.file.Paths.get(fileName))
    try handler(output)
    finally output.close()
  }

  def withFileReader[T](fileName: String)(handler: java.io.BufferedReader => T) = {
    val input = java.nio.file.Files.newBufferedReader(java.nio.file.Paths.get(fileName))
    try handler(input)
    finally input.close()
  }
}

```

Sample practice using recursive function

```scala
package foo

import foo.Example.hello

object Example2 {
  def main(args: Array[String]): Unit = {
    println(hello())
    printMessages2(Array(
      new Msg(0,None, "Hello"),
      new Msg(1,Some(0), "Hello"),
      new Msg(2,None, "Hello"),
      new Msg(3,None, "Hello"),
      new Msg(4,None, "Hello"),
      new Msg(7,Some(0), "Hello")
    ))

  }
  def printMessages2(messages: Array[Msg]): Unit = {
    def printWithIndent(parent: Option[Int], indent: String): Unit ={
      for (msg <- messages if msg.parent == parent){
        println(s"$indent#${msg.id} ${msg.txt}")
        printWithIndent(Some(msg.id),indent + "   ")
      }
    }
    printWithIndent(None,"")
  }

  def printMessages(messages: Array[Msg]): Unit = {
    def printFrag(parent: Option[Int], indent: String): Unit = {
      for (msg <- messages if msg.parent == parent) {
        println(s"$indent#${msg.id} ${msg.txt}")
        printFrag(Some(msg.id), indent + "    ")
      }
    }
    printFrag(None, "")
  }
  class Msg(val id: Int, val parent: Option[Int], val txt: String){

  }
}

```

