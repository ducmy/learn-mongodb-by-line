One to One (Embedded Demo)

Original Schema
```
author
- name
- age

address
- street
- city


```
address
```js
// Connect to Database 
use test;
show collections;

db.createCollection('author');

// Create Index & Unique
db.author.createIndex( { name : 1 } , { unique : true } );
db.author.dropIndex("name_1");
db.author.createIndex( { name : 1, "address.street": 1 } , { unique : true} );
db.author.getIndexes();


// Insert Data
db.author.insert({ 
    "name" : "Peter Wilkinson", 
    "age" : 27.0, 
    "address" : {
        "street" : "Paster", 
        "city" : "New Yeak"
    }
});

// Repeat that insert iwth unique(name, address.street)

db.author.find({});

// Check if field contain string
db.author.find({ name: {$regex : "Wilkinson"} });

//Query on Embedded Document
db.author.find({ "address.city": "Nevermore"});
db.author.find({ "address.street": "Nguyen Thi Minh Khai"});

```
