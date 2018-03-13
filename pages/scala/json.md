
## Code for parsing a JSON document

These auxiliary classes help retrieving the values of the different types of JSON objects:

```
// This common class is the base of all classes parsed from the JSON
class CC[T] { def unapply(a:Any):Option[T] = Some(a.asInstanceOf[T]) }

// Now, for every type that we might recover, we have a specialized class
// M (map) and L (list) will be used to recover embedded objects from the JSON
object M extends CC[Map[String, Any]]
object L extends CC[List[Any]]
object S extends CC[String]
object D extends CC[Double]
object B extends CC[Boolean]

```

Now, after parsing the JSON structure, we crawl along it and extract the needed fields

```
def parseJSONData(data: String) : List[UseCase] = {

// We crawl along the JSON structure, retrieving the columns that will form our table structure
val resultList = for {
  Some(M(json)) <- List(JSON.parseFull(data))
  // TODO

} yield {
  // TODO
}
```