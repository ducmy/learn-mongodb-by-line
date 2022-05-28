## Learn MongoDB by short way

### Tools
- Docker
- MongoDB Image from Docker Hub
- mongosh

### One to One Relationship (Linking)

#### Schema
```
author
 - _id (auto generate)
 - name
 - age
 
 address
 - _id
 - user_id (fk)
 - street
 - city
 ```

#### Mongo SH

###### Connection:
```
$ mongosh mongodb://docker:mongopw@localhost:55000
```

###### Pipeline:
```js
show dbs;

use test;
db.author.drop();
db.address.drop();
db.createCollection('author');
db.createCollection('address');

/* Preparing data  for author */
db.author.insertMany([
    { name: String('Tran Van A'), age: NumberInt(20) },
    { name: String('Nguyen Van B'), age: NumberInt(30) },
    { name: String('Le Thi C'), age: NumberInt(40) }
]);

db.author.find({}).pretty();

db.author.findOne({ name: 'Tran Van A'}); // Get all field with name 
db.author.findOne({ name: 'Tran Van A'}, {_id: true}); // Get both Key and Value
db.author.findOne({ name: 'Tran Van A'},{_id: true})._id; // Get only Value

// Show address info before finding somethings
db.address.find({}).pretty();

// Add addresss for Auth name Tran Van A
db.address.insertOne(
    {
        user_id: db.author.findOne({ name: 'Tran Van A'},{_id: true})._id,
        street: 'Nguyen Hue',
        city: 'HCM'
    }
);

db.address.find({}).pretty();


// Update Address without Author
db.address.insertOne(
    {
        street: 'Hai Bà Trưng',
        city: 'HCM'
    }
);

db.address.find({}).pretty();

// Update Address to Author with Name
db.address.updateOne({
        street: 'Hai Bà Trưng'
    }, {
    $set: {
        user_id: db.author.findOne({ name: 'Nguyen Van B'},{_id: true})._id
    }
});

db.address.find({}).pretty();

// From Address get User
db.address.aggregate([
    {
        $lookup:
            {
                from: "author",          // Author table
                localField: "user_id",   // Local Field from Address
                foreignField: "_id",     // Author table, Col _id
                as: "users_list"        // Descrise User Detail
            }
    }
]).pretty();

// From author get the address
db.author.aggregate([{
    $lookup:
        {
            from: "address",          // Address table
            localField: "_id",   // Local Field from Address
            foreignField: "user_id",     // Author table, Col _id
            as: "address_info"        // Descrise User Detail
        }
}]);

db.author.find({});
db.address.find({});
```

