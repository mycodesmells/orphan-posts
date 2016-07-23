# Backing Up MongoDB

Any time you store some important information in your database, you should consider backing it up. This is just so obvious, yet so many people just don't to it, not to mention do it regularly. Let's look on the ways you can back up your data stored in MongoDB.

First of all, we need some database to be backed up. Let's create a small, two-collection database:

    $ mongo
    ...
    connecting to: test
    > db.createCollection('first')
    { "ok" : 1 }
    > db.createCollection('second')
    { "ok" : 1 }
    > db.first.insert({name: 'Dennis'})
    > db.first.insert({name: 'Patrick'})
    > db.first.insert({name: 'Simon'})
    > db.second.insert({name: 'Ann'})
    > db.second.insert({name: 'Taylor'})
    > db.second.insert({name: 'Jessica'})

Now imagine we want to have this data backed up in case of any unexpected events that might happen at some point.

There are two ways to do that: with binary and human-readable files. The first approach is much faster, but there is no easy way to edit the files in the process. Readable JSON files are good not only for manual edits, but also for a situation when you need to insert your data into another system, in a form of one-time data integration.

### mongodump

To create a binary dump of your database, all you need to do is:

    $ mongodump --db test --out bck
    connected to: 127.0.0.1
    Tue May 31 21:34:32.312 DATABASE: test	 to 	bck/test
    Tue May 31 21:34:32.312 	test.system.indexes to bck/test/system.indexes.bson
    Tue May 31 21:34:32.326 		 2 objects
    Tue May 31 21:34:32.326 	test.first to bck/test/first.bson
    Tue May 31 21:34:32.326 		 3 objects
    Tue May 31 21:34:32.326 	Metadata for test.first to bck/test/first.metadata.json
    Tue May 31 21:34:32.327 	test.second to bck/test/second.bson
    Tue May 31 21:34:32.327 		 3 objects
    Tue May 31 21:34:32.327 	Metadata for test.second to bck/test/second.metadata.json

The `--out` parameter defines the location where dump files will be stored.

This command creates a specific structure of files, in which each `.bson` file represent binary data of a collection, and `.json` file defining some metadata, like indexes:

    bck/test
     - first.bson
     - first.metadata.json
     - second.bson
     - second.metadata.json
     - system.indexes.bson

### mongorestore

A reverse action to dumping data is restoring them using just as simple command:

    $ mongorestore bck/
    connected to: 127.0.0.1
    Tue May 31 21:40:32.684 bck/test/second.bson
    Tue May 31 21:40:32.684 	going into namespace [test.second]
    Tue May 31 21:40:32.698 	Created collection test.second with options: { "create" : "second" }
    3 objects found
    Tue May 31 21:40:32.698 	Creating index: { key: { _id: 1 }, ns: "test.second", name: "_id_" }
    Tue May 31 21:40:32.698 bck/test/first.bson
    Tue May 31 21:40:32.698 	going into namespace [test.first]
    Tue May 31 21:40:32.699 	Created collection test.first with options: { "create" : "first" }
    3 objects found
    Tue May 31 21:40:32.699 	Creating index: { key: { _id: 1 }, ns: "test.first", name: "_id_" }

Note that passing just a parameter defining a location of dump files suggests that we want to create a database with the same name as the original (in our case it would be _test_). If you want to rename it, you can always use `--db <db-name>` parameter.

### mongoexport

Exporting data to human-readable files requires a bit more effort. You explicitly need to define which collection will land in a specific JSON file:

    $ mongoexport --db test --collection first --out first.json
    connected to: 127.0.0.1
    exported 3 records
    $ mongoexport --db test --collection second --out second.json
    connected to: 127.0.0.1
    exported 3 records

After this operation you can easily read the output files:

    $ cat first.json
    { "_id" : { "$oid" : "574de5881d7ecde6545aa084" }, "name" : "Dennis" }
    { "_id" : { "$oid" : "574de5921d7ecde6545aa085" }, "name" : "Patrick" }
    { "_id" : { "$oid" : "574de59d1d7ecde6545aa086" }, "name" : "Simon" }
    $ cat second.json
    { "_id" : { "$oid" : "574de5b21d7ecde6545aa087" }, "name" : "Ann" }
    { "_id" : { "$oid" : "574de5bf1d7ecde6545aa088" }, "name" : "Taylor" }
    { "_id" : { "$oid" : "574de5c41d7ecde6545aa089" }, "name" : "Jessica" }

### mongoimport

Importing JSON files back to Mongo takes exactly the same effort - choose a file containing data, select database, collection and run:

    $ mongoimport --db test --collection first --file first.json
    connected to: 127.0.0.1
    Tue May 31 21:56:32.267 imported 3 objects
    $ mongoimport --db test --collection second --file second.json
    connected to: 127.0.0.1
    Tue May 31 21:56:43.365 imported 3 objects

**Note** that exporting/importing specific collections manually might cause you backup to be inconsistent! You may have some kind of relations between items in collections, and forget to import one of them. It is very important to check your backup if you choose `mongoexport` , not to be unpleasantly surprised when you need to restore database state after some data loss.
