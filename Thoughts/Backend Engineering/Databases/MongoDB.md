# MongoDB

> *Flexible document database*

---

## Why MongoDB?

- **Schema flexibility** - No fixed structure
- **JSON-like documents** - Natural for developers
- **Horizontal scaling** - Sharding built-in
- **Aggregation pipeline** - Powerful queries

---

## Basic Operations

### Insert

```javascript
// Insert one
db.users.insertOne({
  name: "John",
  email: "john@example.com",
  createdAt: new Date()
});

// Insert many
db.users.insertMany([
  { name: "Alice", email: "alice@example.com" },
  { name: "Bob", email: "bob@example.com" }
]);
```

### Find

```javascript
// Find all
db.users.find();

// Find with filter
db.users.find({ name: "John" });

// Projection
db.users.find({}, { name: 1, email: 1 });

// Operators
db.users.find({ age: { $gte: 18 } });
```

### Update

```javascript
// Update one
db.users.updateOne(
  { email: "john@example.com" },
  { $set: { name: "John Doe" } }
);

// Update many
db.users.updateMany(
  { status: "inactive" },
  { $set: { archived: true } }
);
```

### Delete

```javascript
db.users.deleteOne({ _id: ObjectId("...") });
db.users.deleteMany({ status: "deleted" });
```

---

## Aggregation Pipeline

```javascript
db.orders.aggregate([
  { $match: { status: "completed" } },
  { $group: {
      _id: "$userId",
      totalSpent: { $sum: "$total" },
      orderCount: { $sum: 1 }
  }},
  { $sort: { totalSpent: -1 } },
  { $limit: 10 }
]);
```

---

## Indexes

```javascript
// Single field
db.users.createIndex({ email: 1 });

// Compound
db.orders.createIndex({ userId: 1, createdAt: -1 });

// Unique
db.users.createIndex({ email: 1 }, { unique: true });
```

---

*Back to: [[Index|Databases Home]]*
