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

## Attach a flow to a source 

```scala
 val aSource: Source[Int, NotUsed] = Source(1 to 10).via(Flow[Int].map(x =>  2*x))
```

## Attach flow to a sink

```scala
 val aSink: Sink[Int, NotUsed] = Flow[Int].map(x => 3 * x).to(Sink.reduce[Int](_ + _))
```


