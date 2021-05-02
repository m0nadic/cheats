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
object SimpleStreams extends App {
  implicit val system = ActorSystem("first-principles")
  // Source (Producer)
  val source = Source(1 to 100)

  // Sink (Consumer)
  val sink = Sink.foreach[Int](println)
  
  // Flow (transform elements)
  val flow = Flow[Int].map( x => x + 1 )

  // graph
  val graph = source.via(flow).to(sink)
  
  // 
  graph.run()
}
```

