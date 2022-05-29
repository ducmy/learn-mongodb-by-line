```javascript
use test;

// Embedded (1-1, kết nối với bảng ko có quan hệ)

// Each employee just have one the address

db.employee.insertMany([
    {
        firstName: "John",
        lastName: "King",
        email: "john.king@abc.com",
        salary: "33000",
        DoB: new Date('Mar 24, 2011'),
        address: {
            street: "Upper Street",
            house: "No 1",
            city: "New York",
            country: "USA"
        }
    },
    {
        firstName: "Tomy",
        lastName: "Dang",
        email: "tomy.dang@abc.com",
        salary: "22000",
        DoB: new Date('Mar 24, 2005'),
        address: {
            street: "Down Street",
            house: "No 2",
            city: "Shanghai",
            country: "Vietnam"
        }
    },
]);

db.employee.find({ "email": "john.king@abc.com" });

//db.employee.deleteOne({_id: ObjectId("6293381bcc2eda7d1ae2db90")});
db.employee.update(
    { _id: ObjectId("6293381bcc2eda7d1ae2db91") },
    {
        $set: {
            "address.street": "Upper Street",
        }
    }
);

// Search All employ with street
/**************************************************************************************/

var employIds = [];
db.employee.find({ "address.street": "Upper Street" }).forEach(function (doc) {
    employIds.push(doc._id);
}
);
print(employIds);

db.employee.find({ _id: { $in: employIds } });

/**************************************************************************************/

// Reference
db.address.insertOne({
    _id: 101,
    street: "Upper Street",
    house: "No 1",
    city: "New York",
    country: "USA"
});

db.employee_ref.insertOne({
    firstName: "John",
    lastName: "King",
    email: "john.king@abc.com",
    salary: "33000",
    DoB: new Date('Mar 24, 2011'),
    address: 101
});

db.employee_ref.findOne({});
db.address.findOne();

// Option 1
var addrId = db.employee_ref.findOne({ firstName: 'John' }).address;
db.address.findOne({ _id: addrId });

// Option 2
db.employee_ref.aggregate([{ $lookup: { from: 'address', localField: 'address', foreignField: "_id", as: 'addr' } }]);
```
