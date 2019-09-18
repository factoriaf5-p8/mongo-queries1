# MongoDB | Querying

### Create | Inserting Documents

Create commands allow us to insert new documents into a collection. If the collection doesn't exist, the operation will create it.

| Insert Command | Summary
|----------------|--------------------
| [db.collection.insertOne(doc)](https://docs.mongodb.com/v3.2/reference/method/db.collection.insertOne/)     | Adds a document to the collection
| [db.collection.insertMany([docs])]((https://docs.mongodb.com/v3.2/reference/method/db.collection.insertMany/)) | Adds one or more documents to the collection


### Read | Finding/Listing Documents

The read operations allow us to retrieve `documents` from the database. The basic method to find documents is [db.collection.find()](https://docs.mongodb.com/v3.2/reference/method/db.collection.find/) where you can specify two optional arguments:

:::info
**db.collection.find**( query_filter, projection )

 `>` **query_filter** specifies which documents to return
 `>` **projection** allows us to return only certain fields from those documents
:::


```javascript
db.users.find({ age: 18 }, { name: 1, _id: 0} )
```

:::info
In the mongo shell, the primary method for the read operation is the `db.collection.find()` method. This method queries a collection and returns a cursor to the returning documents.
:::

:::success
**Cursors**
A cursor is a pointer to the result set of a query. Clients can iterate through a cursor to retrieve results.

In other words, when you run a query looking for documents, what gets returned is a pointer to the results of that query rather than all of the results in one shot (as you can imagine, this might be impractical and slow for very large result sets).  Essentially, the cursor is the conduit you will use to extract all the results of a query from the database.

Now, returning just a pointer with no initial results would be a little inefficient - the first thing you would need to do each time is fetched the first batch of results.  At the same time, if that initial set of results it too large it might take a long time to assemble and transfer to the client.
:::

### Update

The update command allows us to update some fields in the document or replace the entire document:

| Mongo Method | Description
|--------------|---------------------
| [db.collection.updateOne()](https://docs.mongodb.com/v3.2/reference/method/db.collection.updateOne/) | Updates a single document within the collection
| [db.collection.updateMany()](https://docs.mongodb.com/v3.2/reference/method/db.collection.updateMany/) | Updates multiple documents within the collection
| [db.collection.replaceOne()](https://docs.mongodb.com/v3.2/reference/method/db.collection.replaceOne/) | Replaces a single document within the collection

All these methods accept two parameters:
- `<filter>`  finds what documents to update
- `<update>` specifies what fields to update in the document



### Delete

The delete commands remove documents from our collections.

| Mongo Method | Description
|--------------|---------------------
| [db.collection.deleteOne()](https://docs.mongodb.com/v3.2/reference/method/db.collection.deleteOne/) | Remove a single document that match the filter.
| [db.collection.deleteMany()](https://docs.mongodb.com/v3.2/reference/method/db.collection.deleteMany/) | Removes all documents that match the filter.


## Sample Collection Setup

In order to start the lesson with the same data, let's download and import the following JSON file:

:::warning
primer-dataset.json
:::

:bulb: In the terminal, use `mongoimport` to import documents into collection named `restaurants`.

```
$ mongoimport --db restaurants --collection restaurants --drop --file ~/downloads/primer-dataset.json
```

This is an example document from the `restaurants` collection.

```javascript
{
  "address": {
     "building": "1007",
     "coord": [ -73.856077, 40.848447 ],
     "street": "Morris Park Ave",
     "zipcode": "10462"
  },
  "borough": "Bronx",
  "cuisine": "Bakery",
  "grades": [
     { "date": { "$date": 1393804800000 }, "grade": "A", "score": 2 },
     { "date": { "$date": 1378857600000 }, "grade": "A", "score": 6 },
     { "date": { "$date": 1358985600000 }, "grade": "A", "score": 10 },
     { "date": { "$date": 1322006400000 }, "grade": "A", "score": 9 },
     { "date": { "$date": 1299715200000 }, "grade": "B", "score": 14 }
  ],
  "name": "Morris Park Bake Shop",
  "restaurant_id": "30075445"
}
```
## Query Operators

Query operators allow us to find data within the database.

### List all documents

To list all documents in a collection we use `find`.

```javascript
> db.restaurants.find({})
```

:::info
:bulb: Find will return at most 20 results. Use the `it`command to retrieve 20 more at a time.
:::

:::info
:bulb: We can use the modifier `pretty()` to print all documents in a more readable format:

```
> db.restaurants.find({}).pretty()
```
:::

### Equality Conditions

Performs queries that match a condition.

Let's find all restaurants for which **cuisine** is **Hamburgers**

```javascript
> db.restaurants.find({ cuisine: "Hamburgers" }).pretty()
```

To perform queries in **embedded documents** we use dot notation and the full path must to be inside double quotes `""`:

Find all documents where **zipcode** is **11225**

```javascript
> db.restaurants.find({ "address.zipcode": "11225"}).pretty()
```

We can do the same to access fields inside arrays:

Select all restaurants with **grade C**

```javascript
> db.restaurants.find({ "grades.grade": "C" }).pretty()
```

### Specifying Conditions

To use any conditions that are not an equality MongoDB provides query operators, like [comparision operators](http://docs.mongodb.com/manual/reference/operator/query-comparison/) or [logical operator](https://docs.mongodb.com/manual/reference/operator/query-logical/).

Query operators usually have the following syntax:

```javascript
> db.collection.find({ <field>: { <operator>: <value> } })
```

We are gonna find all the restaurants that have a **score greater or equal than 10**

```javascript
> db.restaurants.find({ "grades.score": { $gte: 10 }}).pretty()
```
Mongo Comparison Operators | Description
:----------------:|-----------------
[`$eq`](https://docs.mongodb.com/manual/reference/operator/query/eq/) | equal to
[`$ne`](https://docs.mongodb.com/manual/reference/operator/query/ne/) | not equal to
[`$gt`](https://docs.mongodb.com/manual/reference/operator/query/gt/) | greater than
[`$gte`](https://docs.mongodb.com/manual/reference/operator/query/gte/) | greater than or equal
[`$lt`](https://docs.mongodb.com/manual/reference/operator/query/lt/) | less than
[`$lte`](https://docs.mongodb.com/manual/reference/operator/query/lte/) | less than or equal

:::info
:bulb: The `$eq` operator can be omitted. These two queries are identical:

```javascript
> db.users.find({ age: 30 })
> db.users.find({ age: { $eq: 30 } })
```
:::

### Combining Conditions

We can combine multiple query conditions using logical operators.

Mongo Logical Operators | Description
:----------------:|-----------------
[`$or`](https://docs.mongodb.com/manual/reference/operator/query/or/#op._S_or) | OR
[`$and`](https://docs.mongodb.com/manual/reference/operator/query/and/#op._S_and) | AND
[`$not`](https://docs.mongodb.com/manual/reference/operator/query/not/#op._S_not) | NOT
[`$nor`](https://docs.mongodb.com/manual/reference/operator/query/nor/#op._S_nor) | NOR

:::info
:bulb: The `$and` operator is implicit. These two queries are identical:
```javascript
db.restaurants.find({
  "cuisine": "Italian",
  "address.zipcode": "10075"
})

db.restaurants.find({
  $and: [
    {"cuisine": "Italian"},
    {"address.zipcode": "10075" }
  ]
})
```
:::

:::info
:bulb: The only reason to use the `$and` operator is to apply more than one condition (that isn't equality) over the same field, for example:
```javascript
db.users.find({
  $and: [
    {"age": {$gt: 18}},
    {"age": {$lt: 30}}
  ]
})
```
:::

If we want to find restaurants with either **italian** or **hamburgers cuisine**

```javascript
db.restaurants.find({
  $or: [
    {"cuisine": "Italian"},
    {"cuisine": "Hamburgers"}
  ]
})
```

### Sorting Query Results

We can append the sort function to a query to sort the results by one or more fields. For each field we can specify if we want the results ascending or descending order:

Value | Order | Example
:------:|-------|-------------------------------
1  | Ascending order  | `A,B, ... Y,Z`
-1 | Descending order | `9,8, ... 2,1`

```javascript
> db.restaurants.find().sort( { "borough": 1, "address.zipcode": -1 } )
```

:::info
:link: [Other interesting methods](https://docs.mongodb.com/manual/reference/method/js-cursor/)
:::

## Independent Practice

Practice the following queries:

- Find the restaurant with id `30112340`.
- Find `May May Kitchen`.
- Find the restaurants where their cuisine is `Tapas`.
- Find the restaurants in postal code `11208`.
- Find all restaurants that have a score greater or equal than `70`.
- Find all restaurants in `Brooklyn`that have a score greater than `80`
- All restaurants with `Chilean` or `Czech` cuisine.
- All restaurants with grade `A` in **second** position of the array.
- All restaurants with grades `A` or `B`.
- All restaurants that have a review made in `2014-09-16`.
- All restaurant their cuisine is `Tapas` ordered by `name` in ascending (normal) order.
- How many restaurants have been graded after `2015-01-01`.
