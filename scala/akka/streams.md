# Akka Streams

## Sample build.sbt file

```sbt
name := "sample-project"

version := "0.1"

scalaVersion := "2.13.5"

lazy val akkaVersion = "2.6.14"
lazy val scalaTestVersion = "3.2.8"

libraryDependencies ++= Seq(
  "com.typesafe.akka" %% "akka-stream" % akkaVersion,
  "com.typesafe.akka" %% "akka-stream-testkit" % akkaVersion,
  "com.typesafe.akka" %% "akka-testkit" % akkaVersion,
  "org.scalatest" %% "scalatest" % scalaTestVersion % Test
)
```

---

## Example streams app

```scala
package samples

import akka.actor.ActorSystem
import akka.stream.scaladsl.{Flow, Sink, Source}

object SimpleStreams extends App {
  implicit val system = ActorSystem("simple-streams")
  // Source (Producer)
  val source = Source(1 to 100)

  // Sink (Consumer)
  val sink = Sink.foreach[Int](println)

  // Flow (transform elements)
  val flow = Flow[Int].map( x => x + 1 )

  // graph
  val graph = source.via(flow).to(sink)

  // run the graph
  graph.run()
}
```
## Sources, Sinks and Flows
### Attach a flow to a source 

```scala
 val aSource: Source[Int, NotUsed] = Source(1 to 10).via(Flow[Int].map(x =>  2*x))
```

### Attach flow to a sink

```scala
 val aSink: Sink[Int, NotUsed] = Flow[Int].map(x => 3 * x).to(Sink.reduce[Int](_ + _))
```

### Kinds of sources

```scala
val singleSource = Source.single[Int](1)
val emptySource = Source.empty
val sourceFromList = Source(List(1, 2, 3))
val infiniteSource = Source(LazyList.from(1))
val cycledSource = Source.cycle(() => List(1, 2, 3, 4).iterator)
val futureSource = Source.future(Future(42))
```

### Kinds of sinks

```scala
val nopSink = Sink.ignore               // does nothing
val feSink = Sink.foreach(println)
val headSink = Sink.head
val foldingSink = Sink.fold[Int, Int](0)((a, b) => a + b)
```


### Kinds of flows

```scala
val mapFlow = Flow[Int].map(a => 2 * a)
val takeFlow = Flow[Int].take(5)
val dropFlow = Flow[Int].drop(10)
val filterFlow = Flow[Int].filter(x => x % 2 == 0)
```

### Syntactic Sugar

```scala
val mapSource = Source(1 to 10).map(x => x * 2) 
mapSource.runForeach(println)
```
this is equivalent to `Source(1 to 10).via(Flow[Int].map(x => x + 2))`

### Example

```scala
val personSource = Source(List("mary", "jane", "richard", "jamie", "tom", "robert"))
  
personSource.via(
  Flow[String].filter(n => n.length > 4)
).via(
  Flow[String].take(2)
).to(
  Sink.foreach(println)
).run()
```

#### Output
```
richard
jamie
```

---
## Materialized Values

```scala
val simpleGraph = Source(1 to 10).to(Sink.foreach(println))
val simpleMatValue: NotUsed = simpleGraph.run()
```

### Using materialized values

```scala
import system.dispatcher
val source = Source(1 to 10)

val sink = Sink.reduce[Int]( (a,b) => a + b)

val matValue: Future[Int] = source.runWith(sink)

matValue.onComplete{
  case Success(value) => println(s"success with $value")
  case Failure(ex) => println(s"error: $ex")
}
```

### Choosing materialized values

```scala
val source = Source(1 to 10)

val flow = Flow[Int].map(x => x + 1)

val sink = Sink.reduce[Int]((a,b) => a + b)

val graph = source.viaMat(flow)(Keep.right).toMat(sink)(Keep.right)

graph.run().onComplete{
  case Success(value) => println(s"stream processing complete value = $value")
  case Failure(ex) => println(s"failed with exception $ex")
}
```
### Syntactic sugars

```scala
  val sumFuture = Source(1 to 10).runWith(Sink.reduce[Int](_ + _))
  val multFuture = Source(1 to 10).runWith(Sink.reduce[Int](_ * _))

```

### backwards, starting from sink

```scala
Sink.reduce[Int](_ + _).runWith(Source(1 to 10))
```

### both ways, starting from flow

```scala
val result: (NotUsed, Future[String]) = Flow[String].map(_.toUpperCase()).runWith(
  Source(List("one", "two")),
  Sink.head
)
result._2.onComplete{
  case Success(s) => println(s"got ${s}")
  case Failure(ex) => println(s"ERROR: $ex")
}
```

### Examples

return last element out of a source

```scala
val lastValue: Future[Int] = Source(1 to 10).toMat(Sink.last)(Keep.right).run()
```

compute the total word count out of a stream of sentences

```scala
val sentenceSource = Source(List(
  "Logic is the beginning of wisdom not the end",
  "Highly illogical",
  "Live long, and prosper",
  "Things are only impossible until they're not",
  "Insufficient facts always invite danger"
))

val f: Future[Int] = sentenceSource
  .map(s => s.split(" ").length)
  .runWith(
    Sink.reduce[Int]((a, b) => a + b )
  )

f.onComplete{
  case Success(value) => println(s"total word count $value")
  case _ =>
}
```

