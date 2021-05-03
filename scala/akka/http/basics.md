# Akka HTTP basics

## SBT Build

```scala
name := "akka-http-sample"

version := "0.1"

scalaVersion := "2.13.5"

val akkaVersion = "2.6.14"
val akkaHttpVersion = "10.2.4"
val scalaTestVersion = "3.2.8"

libraryDependencies ++= Seq(
  // akka streams
  "com.typesafe.akka" %% "akka-stream" % akkaVersion,
  // akka http
  "com.typesafe.akka" %% "akka-http" % akkaHttpVersion,
  "com.typesafe.akka" %% "akka-http-spray-json" % akkaHttpVersion,
  "com.typesafe.akka" %% "akka-http-testkit" % akkaHttpVersion,
  // testing
  "com.typesafe.akka" %% "akka-testkit" % akkaVersion,
  "org.scalatest" %% "scalatest" % scalaTestVersion,

  // JWT
  "com.github.jwt-scala" %% "jwt-spray-json" % "7.1.3"
)
```

### Sync Server

```scala
package sample

import akka.actor.ActorSystem
import akka.http.javadsl.model.ContentType
import akka.http.scaladsl.Http
import akka.http.scaladsl.Http.IncomingConnection
import akka.http.scaladsl.model.{ContentTypes, HttpEntity, HttpMethods, HttpRequest, HttpResponse, StatusCodes}
import akka.stream.scaladsl.Sink
import scala.util.{Failure, Success}

object SyncServer extends App {

  implicit val system = ActorSystem("sync-server")
  import system.dispatcher
  
  val connSource = Http().newServerAt("localhost", 8080).connectionSource()
  
  val connSink = Sink.foreach[IncomingConnection]{ conn =>
    println(s"Accepted incoming connection from ${conn.remoteAddress}")
    conn.handleWithSyncHandler(handler)
  }

  val bindingFuture = connSource.to(connSink).run()

  bindingFuture.onComplete{
    case Success(_) => println("bind success")
    case Failure(ex) => println(s"bind failure: $ex")
  }

  val handler: HttpRequest => HttpResponse = {
    case HttpRequest(HttpMethods.GET, _, _, _, _) => HttpResponse(
      StatusCodes.OK,
      entity = HttpEntity(
        ContentTypes.`text/html(UTF-8)`,
        """
          |<html>
          |<body>
          | Hello form Akka HTTP
          |</body>
          |</html>
          |""".stripMargin
      )
    )
    case request:HttpRequest =>
      request.discardEntityBytes()
      HttpResponse(
      StatusCodes.NotFound,
      entity = HttpEntity(
        ContentTypes.`text/html(UTF-8)`,
        """
        |<html><body> Oops Not found </body></html>
        |""".stripMargin
      )
    )
  }
}

```
