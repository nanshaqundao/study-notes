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