jaguarDb
================
The simplest in-process database in JavaScript that could possible work. 

This library provides the ability to store information in a Node.js application emulating a simple document-oriented database and without requiring an external process to be running.

The goal is to provide a quick and dirty way of storing information for small prototypes and unit testing. Do not use this to power your web site or the next eBay.

The API mimics MongoDB’s API to allow for easy migration of applications using jaguarDb to a real database. Also, the fact the MongoDB has a kickass API makes it an great model to follow.

This database does not support transactions or any of the ACID properties. In fact you should not even consider it a database. 

The API for jaguarDb is asynchronous so that the code using it can easily migrated to use a real database. I might provide a synchronous API later on to make its use much simpler, particularly for unit tests or batch processes.


Basic Samples
-------------
**demoAdd.js** shows how to add data to a brand new database. The basic structure is as follow.

    var jaguarDb = require('./jaguarDb').jaguarDb;
    var db = new jaguarDb();
    db.connect('./data', function(err) {
      if(err) {
        // handle error
        return;
      }
      var data = {title: 'hello', content: 'blah blah blah'};
      db.insert(data, function(err, insertedData) {
        // insertedData has the new document
      });
    });


**demoFind.js** shows how to query documents from the database. The basic structure is as follow: 

    var jaguarDb = require('./jaguarDb').jaguarDb;
    var db = new jaguarDb();
    db.connect('./data', function(err) {
      if(err) {
        // handle error
        return;
      }
      var query = {}; // all records
      var fields = {}; // all fields
      db.find(query, fields, function(err, documents) {
        // your data is in the documents array
      });
    });

You can filter the documents by providing a query object with the fields and attributes to use to filter: The following example will select documents where the title is 'hello'

    var query = { title: 'hello' };


Sample using Express
--------------------
**demoExpress.js** shows how to use jaguarDb with Express.js. You need to install Express.js on your system via **npm install express** before running this example: 

    var express = require('express');
    var app = express();

    var jaguarDb = require('./jaguarDb').jaguarDb;
    var db = new jaguarDb();

    db.connect('./data', function(err) {
      if(err) {
        console.log('Could not connect to database: ' + err);
        return;
      }
    });

    app.get('/', function(req, res){
      var query = {}; // all records
      var fields = {}; // all fields
      db.find(query, fields, function(err, docs) {
        if(err) {
          res.send('Error reading documents: ' + err);
          return;
        }
        // build HTML with your documents
        var html = "";
        res.send(html);
      });
    });


Storage
-------
Data is stored in one directory per database. A master file "index.json" contains the list of documents in the database plus other general information about the database. One "n.json" file is created for each document.

Each document is automatically assigned an _id field with a sequential value. 


Limitations (a lot)
-------------------
This library is meant to be used in a single-user environment as it has no multi-user provisions. 

Transactions are not supported.


Future enhancements
-------------------
Add support for complex queries. Currently only exact match queries are allowed.

    // This is currently supported
    // filter where fieldA == 'a' and fieldB == 'b'
    var query = {fieldA: 'a', fieldB: 'b'};

    // This is NOT currently supported
    // filter where fieldA == 'a' or fieldB == 'b'
    // filter where fieldA > 'a'


Add support for "indexes". Fields indexed will be kept in index.json file so that we don't have to reach out for the individual file for each record when searching indexed fields.  

Allow for sorting operations.


Questions, comments, thoughts?
------------------------------
This is a very rough work in progress. Feel free to contact me with 
questions or comments about this project.

