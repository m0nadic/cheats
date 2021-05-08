# Akka HTTP Low Level Server API


## Sync Server

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
  import system.dispatcher. // Don't use in production code
  
  // Source 
  val connSource = Http().newServerAt("localhost", 8080).connectionSource()
  
  // Sink
  val connSink = Sink.foreach[IncomingConnection]{ conn =>
    println(s"Accepted incoming connection from ${conn.remoteAddress}")
    conn.handleWithSyncHandler(handler) // Sync server
  }

  // Connect source to a sink and run the graph
  val bindingFuture = connSource.to(connSink).run()

  bindingFuture.onComplete{
    case Success(_) => println("bind success")
    case Failure(ex) => println(s"bind failure: $ex")
  }

  // partial function
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

## Async Server

```scala
package sample

import akka.actor.ActorSystem
import akka.http.scaladsl.Http
import akka.http.scaladsl.Http.IncomingConnection
import akka.http.scaladsl.model.{ContentTypes, HttpEntity, HttpMethods, HttpRequest, HttpResponse, StatusCodes, Uri}
import akka.stream.scaladsl.Sink

import scala.concurrent.Future
import scala.util.{Failure, Success}

object AsyncServer extends App {
  implicit val system = ActorSystem("async-server")
  import system.dispatcher

  val connSource = Http().newServerAt("localhost", 8080).connectionSource()

  val connSink = Sink.foreach[IncomingConnection]{ conn =>
    conn.handleWithAsyncHandler(handler)
  }

  connSource.to(connSink).run().onComplete{
    case Success(_) => println("bind successful")
    case Failure(exception) => println(s"bind failed: $exception")
  }

  val handler: HttpRequest => Future[HttpResponse] = {
    case HttpRequest(HttpMethods.GET, Uri.Path("/home"), _, _, _) => Future(HttpResponse(StatusCodes.OK, entity = rootPageEntity))
    case request: HttpRequest =>
      request.discardEntityBytes()
      Future(HttpResponse(StatusCodes.NotFound, entity = notFoundEntity))
  }

  val rootPageEntity = HttpEntity(
    ContentTypes.`text/html(UTF-8)`,
    """
    |<html><body>Hello from Akka Http</body></html>
    |""".stripMargin
  )

  val notFoundEntity = HttpEntity(
    ContentTypes.`text/html(UTF-8)`,
    """
      |<html><body>Oops page not found </body></html>
      |""".stripMargin
  )
}
```

## Flow Server

```scala
package sample

import akka.actor.ActorSystem
import akka.http.scaladsl.Http
import akka.http.scaladsl.Http.IncomingConnection
import akka.http.scaladsl.model.{ContentTypes, HttpEntity, HttpMethods, HttpRequest, HttpResponse, StatusCodes, Uri}
import akka.stream.scaladsl.{Flow, Sink}

import scala.util.{Failure, Success}

object FlowServer extends App {
  implicit val system = ActorSystem("flow-server")
  import system.dispatcher

  val connSource = Http().newServerAt("localhost", 8081).connectionSource()
  val connSink = Sink.foreach[IncomingConnection]{ conn =>
    conn.handleWith(handlerFlow)
  }

  val bindingFuture = connSource.to(connSink).run()

  bindingFuture.onComplete{
    case Success(value) => println("binding successful")
    case Failure(ex) => println(s"binding failure with $ex")
  }

  val handlerFlow = Flow[HttpRequest].map{
    case HttpRequest(HttpMethods.GET, Uri.Path("/home"), _, _, _) => HttpResponse(StatusCodes.OK, entity = pageEntity)
    case request:HttpRequest =>
      request.discardEntityBytes()
      HttpResponse(StatusCodes.NotFound, entity = notFoundEntity)
  }

  val pageEntity = HttpEntity(
    ContentTypes.`text/html(UTF-8)`,
    """
      |<html><body>Hello from Akka</body></html>
      |""".stripMargin
  )

  val notFoundEntity = HttpEntity(
    ContentTypes.`text/html(UTF-8)`,
    """
      |<html><body>Oops page not found</body></html>
      |""".stripMargin
  )
}
```



