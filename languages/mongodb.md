---
description: >-
  Disclaimer : Not complete. I've read the docs but not actually used MongoDB,
  so some of this stuff is probably false. C'est une base pour quand je
  tomberais desus.
---

# MongoDB

## Généralités

* Most popular NoSQL database
* Open-source _document oriented database_
* Written in C++
* Etant NoSQL et document oriented, sa structure ressemble énormément à un fichier JSON plutôt qu'à des tables & co des bdd relationnelles
* Schema less \(numbers of fields, content & size can vary from one document to another within the same collection\)

## Terminologie

* Database : Aggregation de collections
* Collections : Aggregation de plusieurs document, ceux-ci peuvent différé because no schema is enforced \(dynamic schema\). L'équivalent d'une table en SQL
* Document : Set of key-value pairs \(l'équivalent d'une ligne en SQL\) \(Ecrit entre des {}\)
* Field : L'équivalent d'une colonne en SQL

## Exemple

```text
{
   _id: ObjectId(7df78ad8902c)
   title: 'MongoDB Overview', 
   description: 'MongoDB is no sql database',
   by: 'tutorials point',
   url: 'http://www.tutorialspoint.com',
   tags: ['mongodb', 'database', 'NoSQL'],
   likes: 100, 
   comments: [    
      {
         user:'user1',
         message: 'My first comment',
         dateCreated: new Date(2011,1,20,2,15),
         like: 0 
      },
      {
         user:'user2',
         message: 'My second comments',
         dateCreated: new Date(2011,1,25,7,45),
         like: 5
      }
   ]
}
```

## Datatypes

| Type | Description |
| :--- | :--- |
| String | Must be UTF-8 valid |
| Integer | 32 or 64 bit depending of servers |
| Boolean |  |
| Double |  |
| Min/Max keys | Compare value against the lowest & highest BSON elements \(?\) |
| Arrays |  |
| Timestamp |  |
| Object | Embedded documents |
| Null |  |
| Symbol | Used like a string but reserved for languages that use a specific symbol type \(?\) |
| Date | UNIX time format |
| Object ID | Store a document ID |
| Binary data |  |
| Code | Stode Javascript code |
| Regular Expr |  |

## Commandes

### Général

* `use DB_NAME` Use or create db
  * A new database isn't actually created until you add one document to it
  * The default db is _test_
* `db` Shows currently selected db
* `show dbs` Shows all db
* `db.dropDatabase()` Drop selected database

### Collection

* `db.createCollection(name, options)` 
  * Crée une collection
  * _options_ est optionnel et peut prendre ces valeurs :
    * `capped` If true, will overwrites oldest entries instead of adding new ones when the size limit is reached
    * `autoIndexId` Automatically create `_id` field
    * `size` Maximum size for _capped_
    * `max` Maximum number of documents in a _capped_ collection
  * Exemple : `db.createCollection("colName", {capped : true, autoIndexId : true, size : 6142800, max : 10000 })`
  * MongoDB creates collection automatically when you insert some document
* `db.collection_name.drop()`
* `show collections`

### Add a new document

* `db.collection_name.insert(document)`

  ```text
  db.colName.insert({
   _id: ObjectId(7df78ad8902c),
   title: 'MongoDB Overview', 
   description: 'MongoDB is no sql database',
   by: 'tutorials point',
   url: 'http://www.tutorialspoint.com',
   tags: ['mongodb', 'database', 'NoSQL'],
   likes: 100
  })
  ```

* If the collection doesn't yet exist it will first create it.
* If we don't specify the `_id` parameter, it will assign a unique one
* `_id` are unique 12 bytes hex number divided as follow :
  * 4 bytes timestamp
  * 3 bytes machine id \(where does it comes from, the MAC ?\)
  * 3 bytes process id
  * 3 bytes incrementer
* Create multiple documents on one go by passing an array to the insert command : `db.collection_name.insert([{doc1 ...}, {doc2 ...}, ...])`

### Query

* `db.collection_name.find({filter}, {field1:bool, field2:bool})` 
  * Display all document in a non-structured way
  * Un filtre vide ressort tout les résultats
  * La seconde partie est optionnelle et permet de définir les champs qu'on veut avoir dans la réponse `db.colName.find({},{"title":1, _id:0})`
* `db.collection_name.findOne()` Return only 1 doc
* To format it, add .pretty\(\) : `db.collection_name.find().pretty()`
* To limit the number of result, add .limit\(nb\) : `db.collection_name.find().limit(10)`
* We can also skip the first x elements with `.skip(nb)`
* To sort, add .sort\({key : 1}\) \(or -1 for descending order\) : `db.collection_name.find().sort({"title":1})`

**Opérateurs**

| Symbol | Syntax | Example |
| :--- | :--- | :--- |
| = | {"key" : "value"} | `db.colName.find({"name" : "Lyssei"}).pretty()` |
| != | {"key" : {$ne : "value"}} | `db.colName.find({"name" : {$ne : "Lyssei"}}).pretty()` |
| &lt; | {"key" : {$lt : "value"}} | `db.colName.find({"age" : {$lt : 28 }}).pretty()` |
| &lt;= | {"key" : {$lte : "value"}} | `db.colName.find({"age" : {$lte : 28 }}).pretty()` |
| &gt; | {"key" : {$gt : "value"}} | `db.colName.find({"age" : {$gt : 28 }}).pretty()` |
| &gt;= | {"key" : {$gte : "value"}} | `db.colName.find({"age" : {$gte : 28 }}).pretty()` |

**AND**

```text
db.mycol.find(
   {
      $and: [ {key1: value1}, {key2:value2} ]
   }
).pretty()
```

**OR**

```text
db.mycol.find(
   {
      $or: [ {key1: value1}, {key2:value2} ]
   }
).pretty()
```

{% hint style="info" %}
Apparently SQL's `or 1=1 --` can be translated to `|| 1==1` which does not make sense from my doc but ok.
{% endhint %}

### Update

* `db.collection_name.update(filter, {$set : {new_data}}, {multi : true})`
  * Mets à jour les documents correspondant au filtre
  * Si _multi_ n'est pas spécifié, il ne modifiera qu'un seul doc
  * Exemple : `db.mycol.update({'title':'plop'}, {$set:{'title':'new_plop'}}, {multi:true})`
* `db.collection_name.save(document)` 
  * Même syntaxe que _insert_
  * Remplace les données du document par les nouvelles
  * Si `_id` n'est pas spécifié, crée le document

### Remove

* `db.collection_name.remove(filter, justOne)`
* filter est optionnel, tous les documents seront supprimés s'il n'est pas précisé \(faut quand même mettre `{}` entre les parenthèses\)
* justOne \(true ou 1\) est optionnel
* Exemple : `db.colName.remove({'title':'plop'}, 1)`

### Aggregation

`db.collection_name.aggregate(rules)`

Les expressions fonctionnent étrangement donc je rajouterais des exemples/explications si un jour je les utilise :

| Expression | Description |
| :--- | :--- |
| $sum | Si on lui donne un champ, fait la somme de celui-ci, si on lui donne une valeur, aditionne cette valeur à chaque occurence |
| $avg |  |
| $min |  |
| $max |  |
| $push | Inserts the value to an array in the resulting document |
| $addToSet | Same but without duplicates |
| $first |  |
| $last |  |
| $project | "Used to select specific fields from a collection |
| $match | Filtering operation |
| $group | Actual aggregation |
| $sort |  |
| $limit |  |
| $skip | Skip a given amount of documents |
| $unwind | "Unwind arrays" |

**Exemple**

Avec ces données :

```text
{
   _id: ObjectId(7df78ad8902c)
   title: 'MongoDB Overview', 
   description: 'MongoDB is no sql database',
   by_user: 'tutorials point',
   url: 'http://www.tutorialspoint.com',
   tags: ['mongodb', 'database', 'NoSQL'],
   likes: 100
},
{
   _id: ObjectId(7df78ad8902d)
   title: 'NoSQL Overview', 
   description: 'No sql database is very fast',
   by_user: 'tutorials point',
   url: 'http://www.tutorialspoint.com',
   tags: ['mongodb', 'database', 'NoSQL'],
   likes: 10
},
{
   _id: ObjectId(7df78ad8902e)
   title: 'Neo4j Overview', 
   description: 'Neo4j is no sql database',
   by_user: 'Neo4j',
   url: 'http://www.neo4j.com',
   tags: ['neo4j', 'database', 'NoSQL'],
   likes: 750
}
```

Cette opération nous donnera ces résultats :

```text
> db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$sum : 1}}}])
{
   "result" : [
      {
         "_id" : "tutorials point",
         "num_tutorial" : 2
      },
      {
         "_id" : "Neo4j",
         "num_tutorial" : 1
      }
   ],
   "ok" : 1
}
```

## Index

Indexes make queries more efficient. They're "a special data structure that store a small portion of the data set in an easy to traverse form".

* To create an index, use `db.collection_name.ensureIndex({key : 1})`
* The 1 is for ascending or descending \(-1\) order
* It can be done on multiple fields : `db.mycol.ensureIndex({"title":1,"description":-1})`

There's a bunch of optional options :

| Name | Description | Default value |
| :--- | :--- | :--- |
| background | Runs in backgrounds in order not to interfere with other db activities | false |
| unique | Enforce unique index \(i think it translate to not accepting documents with duplicate | false |
| name | Name of the index | Concatenate the names of the indexed fields and sort order |
| dropDups | Create a unique index by deleting any duplicates | false |
| sparse | Only reference documents with the spedified fields | false |
| expireAfterSeconds | Time to Live in seconds of documents within this collection | false |
| v | Index version | Depends on MongoDB version |
| weights | \(in document format\), a number between 1 - 99'999 denoting the importance of a field |  |
| default\_language | "For a text index, determines the list of stop words and the rules for the stemmer and tokenizer" | english |
| language\_override | For a text index, specificy the name of the field that override the default language | language |

_Covered Queries_ are queries where all the fields are part of the same index. Sinces indexes are in the RAM, theses are answered without even looking at the document, and as such much faster.

## Relationships

When different documents referenced each other you can either : 1. Embed one in the other \(which make queries simpler but doesn't scale very well\) 2. Reference the objectID of the other document inside the first one. Scale better but you need to use multiple queries to query the different documents \(there's no _join_ etc\)

If the document is in another collection, you can use DBRef, which are document composed of 3 fields :

* $ref : The collection of the referenced document
* $id : It's object id
* $db : It's db \(optionnal\)

Exemple of data :

```text
{
   "_id":ObjectId("53402597d852426020000002"),
   "address": 
   {
       "$ref": "address_home",
       "$id": ObjectId("534009e4d852427820000002",
       "$db": "tutorialspoint"
   },
   "contact": "987654321",
   "name": "Tom Benzamin"
}
```

Example of query :

```text
>var user = db.users.findOne({"name":"Tom Benzamin"})
>var dbRef = user.address
>db[dbRef.$ref].findOne({"_id":(dbRef.$id)})
`
```

## Replication

* Replication has many advantages : failure proof, constant availability, etc
* In MongoDB, it is done via _Replica Set_
* These are a group of _mongod_ instances dealing with the same dataset
* One primary node receive all writing instructions and those are replicated onto secondary node
* On disconnection of the primary node, an election is performed to choose the new one. When the old one is up again, it returns to the group as a secondary node
* Maximum of 12 nodes
* To create a replica set :
  * Use `mongod --port 20000 -dbpath "D:..." --replSet name` \(chose any port you want\)
  * Connect to the mongod instance and run `rs.initiate()`
  * Check config with `rs.conf()` & status with `rs.status()`
  * To add new members, start mongod instances on multiple machines. Then, on a mongo client, use `rs.add(hostname:port)`
  * To check if you're connection to the primary node, use `db.isMaster()` in a mongo client

## Sharding

* Process of storing data across multiple machines to deal with space & latency issue when scaling
* Each _shard_ is a separate replica set
* _Config Servers_ contains a mapping of the cluster's data set to the shards. There's 3 of them.
* _Query Routers_ use the config server to know which shard to query for which info

## Dump & Restore

* `mongodump`
* Some options :
  * --host ...
  * --port ...
  * --dbpath ...
  * --out ...
  * --collection ...
  * --db ...
* `mongorestore`

## Stat

* In the bin directory, use `mongostat`. It showcase counters of queries, insert, etc as well as page faults & lock percentage
* In the bin directory, use `mongotop`. Details read & write by collection. By default update each second, give a number of sec as argument to increase that

