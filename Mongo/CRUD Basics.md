# Mongo Course M001

# Sanbox Cluster Info

### Sandbox connection string
```
mongo "mongodb+srv://sandbox.ypeyd.mongodb.net/test" --username m001-student --password m001-mongodb-basics
```

## CRUD Operations

### db.collectionName.insertMany()

* A function over the collection class.
* `ordered` parameters allows to insert one by one into an array, exits at exception like duplicate key, and stops.
* `ordered: false` carries on inserting remaining elements the input array even if exception occurs.
* `_id` is by default auto-generated monotonically increasing value, unqiue to each `document` in a `collection`, it can be applied to `sort()`.

### Example:
```
use('video');
db.myMovies.insertMany(
  [
    {
      "_id" : "tt0084726",
      "title" : "Star Trek II: The Wrath of Khan",
      "year" : 1982,
      "type" : "movie"
    },
    {
      "_id" : "tt0796366",
      "title" : "Star Trek",
      "year" : 2009,
      "type" : "movie"
    },
    {
      "_id" : "tt0084726",
      "title" : "Star Trek II: The Wrath of Khan",
      "year" : 1982,
      "type" : "movie"
    },
    {
      "_id" : "tt1408101",
      "title" : "Star Trek Into Darkness",
      "year" : 2013,
      "type" : "movie"
    },
    {
      "_id" : "tt0117731",
      "title" : "Star Trek: First Contact",
      "year" : 1996,
      "type" : "movie"
    }
  ],
  {
    ordered: false
  }
);
```

### db.collectionName.find()

* Used on collection class
* Use `.pretty()` method at end of find to see a pretty print output when using shell.
```
// keep adding filters
db.movieDetails.find({'awards.wins':2, 'awards.nominations':2}).count()
db.movieDetails.find({'rated':'PG-13', 'awards.nominations':10}).count()
```
* Array Search Examples
```  
// Array Search: Find all movies containing 'Jeff Bridges'
db.movieDetails.find({'actors': 'Jeff Bridges'})
// Array Search: Find all movies containing 'Jeff Bridges' at exactly 1st postion
db.movieDetails.find({'actors.0': 'Jeff Bridges'})
// Find all movies containing 'Western' as it's second 'genres'
db.movieDetails.find({'genres.1': 'Western'})
```
* find method returns a `cursor` basically a pointer to the current location in the result set.
* use `it` in mongo shell to iterate more, by default cursor iterates for `20` times. 
* A cursor is exhausted when the result set ends.
* Use `projection` arguments in find method to limit the fields of the returned result set. It helps in reducing network delays by applying like a boolean map on the fields to return to. by default `_id` field is always returned

```
// Only return the title field, exclude all by default, _id is explicit though
db.movieDetails.find({'genres.1': 'Western'}, {'title':1, '_id': 0})
```

### db.collectionName.updateOne() and updateMany()

* `updateOne({...filters...}, {...$update_operator{ ..fields}...})`
* Update operators are used `To specify how one or more fields should be modified in matching documents.`
* Update operators
  [https://docs.mongodb.com/manual/reference/operator/update/]
* `upsert:true` as a parameter upserts the document, meaning, insert if one does not exist, updates the matching filter if it exists
* updateMany() Example:
```
db.movieDetails.updateMany({
  rated: null
}, {
  $unset: {
    rated: ""
  }
})
```
Result:
``` 
{
  acknowleged: 1,
  matchedCount: 1555,
  modifiedCount: 1555,
  upsertedCount: 0,
  insertedId: null
}
```
* `replaceOne(filter, {..doc..})` method will apply changes to only one document, the first found in the server that matches the filter expression, using the `$natural` order of documents in the collection.
* the replacement document *cannot* contain update operators.
* `replaceOne` is used when you want to update the whole doc, `updateOne` is a preicse function with control over individual fields and the manner in which to update them.
* Example: `Replace one`
```
let filter = {title: "House, M.D., Season Four: New Beginnings"}
let doc = db.movieDetails.findOne(filter);
doc.poster;
doc.poster = "https://www.imdb.com/title/tt1329164/mediaviewer/rm2619416576";
doc.genres;
doc.genres.push("TV Series");
db.movieDetails.replaceOne(filter, doc);
```

### db.collectionName.deleteOne() and deleteMany()

* [https://docs.mongodb.com/manual/reference/method/db.collection.deleteOne/#db.collection.deleteOne]
* [https://docs.mongodb.com/manual/reference/method/db.collection.deleteMany/#db.collection.deleteMany]