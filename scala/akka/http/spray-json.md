# JSON with spray-json

```scala
package sample

import spray.json._



case class Pet(id: Int, name: String, category: String)

trait PetStoreProtocol extends DefaultJsonProtocol {
  implicit val petFormat = jsonFormat3(Pet)
}



object PetsJSON extends App with PetStoreProtocol {
  // Marshalling
  val aDog = Pet(1, "Rex", "Dog")
  println(aDog.toJson.prettyPrint)

  val myPets = List(
    Pet(1, "Rex", "Dog"),
    Pet(2, "Ruth", "Cat"),
    Pet(3, "Ron", "Dog"),
    Pet(4, "Silver", "Sparrow")
  )

  println(myPets.toJson.prettyPrint)

  // Unmarshalling
  val petJson =
    """
      |{
      |  "category": "Cat",
      |  "id": 101,
      |  "name": "flex"
      |}
      |""".stripMargin

  println(petJson.parseJson.convertTo[Pet])

  val petsJson =
    """
      |[{
      |  "category": "Dog",
      |  "id": 1,
      |  "name": "Rex"
      |}, {
      |  "category": "Cat",
      |  "id": 2,
      |  "name": "Ruth"
      |}, {
      |  "category": "Dog",
      |  "id": 3,
      |  "name": "Ron"
      |}, {
      |  "category": "Sparrow",
      |  "id": 4,
      |  "name": "silver"
      |}]
      |""".stripMargin

  println(petsJson.parseJson.convertTo[List[Pet]])
}

```
