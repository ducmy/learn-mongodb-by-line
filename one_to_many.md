```javascript
use test2;
// Embedded 
// One to Many
db.employee.insertMany([
    {
        "name": "Joe Bookreader",
        "addresses": [
            {
                "street": "123 Fake Street",
                "city": "Faketon",
                "state": "MA",
                "zip": "12345"
            },
            {
                "street": "1 Some Other Street",
                "city": "Boston",
                "state": "MA",
                "zip": "12345"
            }
        ]
    },
]);

db.employee.find({ name: { $regex: "Bookreader" } }, { addresses: 1 });
db.employee.find({ name: { $regex: "Bookreader" } }, { addresses: 1 }).toArray()[0].addresses;
db.employee.find({ "addresses.street": "123 Fake Street" }).toArray();
db.employee.find({ name: "Joe Bookreader" });

//insert more in embedded item
db.employee.findOneAndUpdate(
    { _id: ObjectId("62935b4ab7aac0f352484825") },
    {
        $push: {
            addresses: {
                "street": "123 Fake Street",
                "city": "Faketon",
                "state": "MA",
                "zip": "12345"
            }
        }
    }
);

db.employee.findOneAndUpdate(
    { _id: ObjectId("62935b4ab7aac0f352484825") },
    {
        $pop: {
            addresses: 1
        }
    }
);

// Find duplicate value in nested collection

db.employee.aggregate([
    {
        "$project": {
            "name": 1,
            "addresses": { "$setUnion": ["$addresses", []] }
        }
    }
]).pretty();

db.employee.find({});

// Reference one to many


// Insert employee
db.employee_ref.insertMany([
    { "name": "Jony", "card_id": 123, "addressIDs": [] },
    { "name": "Katana", "card_id": 123 }
]
);

db.employee_ref.find();

// Insert Address
db.address.insertMany(
    [
        {
            "street": "Street 101",
            "city": "Ho Chi Minh",
            "state": "JAK",
            "zip": "70000"
        },
        {
            "street": "Street 200",
            "city": "Ho Chi Minh",
            "state": "SG",
            "zip": "8000"
        }

    ]
);
db.address.find({});
var addressIDs = [];
db.address.find({}, { _id: 1 }).forEach(function (doc) {
    addressIDs.push(doc._id);
}
);
printjson(addressIDs);

db.employee_ref.findOne({ "_id": ObjectId("62938d7eb7aac0f352484826") });
db.employee_ref.findOneAndUpdate({ "_id": ObjectId("62938d7eb7aac0f352484826") },

    {
        $set: {
            "addressIDs": addressIDs,
        }
    }
);

db.employee_ref.findOneAndUpdate({ "_id": ObjectId("62938d7eb7aac0f352484827") },

    {
        $pop: {
            "addressIDs": 1,
        }
    }
);

db.employee_ref.findOneAndUpdate({ "_id": ObjectId("62938d7eb7aac0f352484827") },

    {
        $push: {
            "addressIDs": addressIDs[1],
        }
    }
);

db.employee_ref.find({});
db.address.find({});

// Many to Many
db.address.findOneAndUpdate({ "_id": ObjectId("62938e8ab7aac0f352484828") },

    {
        $push: {
            "employIDs": ObjectId("62938d7eb7aac0f352484827"),
        }
    }
);

var employIDs = db.employee_ref.findOne({"name":"Jony"},{"addressIDs":1});
db.address.find({"_id":{"$in":employIDs["addressIDs"]}});


```
