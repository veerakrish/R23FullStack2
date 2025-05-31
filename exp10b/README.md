# Experiment 10B: MongoDB - CRUD Operations

## 1. Experiment Information
- **Experiment Number**: 10B
- **Title**: MongoDB CRUD Operations
- **Duration**: 45 minutes

## 2. Learning Objectives
- Understand CRUD operations
- Use MongoDB shell commands
- Write Node.js MongoDB queries
- Handle MongoDB documents
- Perform complex queries
- Manage data operations

## 3. Prerequisites
### Software Requirements
- MongoDB installed
- MongoDB Compass
- Node.js (v14+ LTS)
- npm (v6+)
- Text editor

### Knowledge Requirements
- MongoDB basics
- JavaScript
- JSON format
- Previous experiments completion

## 4. Theory and Concepts
### MongoDB Operations
- Create (insert)
- Read (find)
- Update
- Delete (remove)
- Query operators
- Projections
- Sort and limit

## 5. Step-by-step Instructions

### 1. Project Setup

Create a new directory and initialize:
```bash
mkdir mongodb-crud-demo
cd mongodb-crud-demo
npm init -y
npm install mongodb
```

### 2. Create Database Connection

Create `db.js`:
```javascript
const { MongoClient } = require('mongodb');

// Connection URI (replace with your connection string)
const uri = "mongodb+srv://fst2user:<password>@cluster0.xxxxx.mongodb.net/?retryWrites=true&w=majority";

// Database Name
const dbName = 'fst2db';

// Collection Name
const collectionName = 'students';

// Create MongoDB client
const client = new MongoClient(uri);

// Connect to database
async function connect() {
  try {
    await client.connect();
    console.log('Connected to MongoDB');
    return client.db(dbName).collection(collectionName);
  } catch (err) {
    console.error('Error connecting to MongoDB:', err);
    throw err;
  }
}

// Export connection
module.exports = { connect, client };
```

### 3. Create Operations

Create `create.js`:
```javascript
const { connect, client } = require('./db');

async function createOperations() {
  const collection = await connect();

  try {
    // Insert One Document
    const singleResult = await collection.insertOne({
      name: "John Doe",
      email: "john@example.com",
      age: 20,
      courses: ["Web Development", "Database"],
      active: true,
      createdAt: new Date()
    });
    console.log('Inserted single document:', singleResult.insertedId);

    // Insert Multiple Documents
    const multipleResult = await collection.insertMany([
      {
        name: "Jane Smith",
        email: "jane@example.com",
        age: 22,
        courses: ["AI", "Machine Learning"],
        active: true,
        createdAt: new Date()
      },
      {
        name: "Bob Wilson",
        email: "bob@example.com",
        age: 21,
        courses: ["Cloud Computing"],
        active: false,
        createdAt: new Date()
      }
    ]);
    console.log('Inserted multiple documents:', multipleResult.insertedIds);

  } catch (err) {
    console.error('Error in create operations:', err);
  } finally {
    await client.close();
  }
}

// Run create operations
createOperations();
```

### 4. Read Operations

Create `read.js`:
```javascript
const { connect, client } = require('./db');

async function readOperations() {
  const collection = await connect();

  try {
    // Find One Document
    const singleDoc = await collection.findOne({ active: true });
    console.log('Found single document:', singleDoc);

    // Find Multiple Documents
    const cursor = collection.find({ active: true });
    const documents = await cursor.toArray();
    console.log('Found documents:', documents);

    // Find with Query
    const youngStudents = await collection
      .find({ age: { $lt: 22 } })
      .toArray();
    console.log('Young students:', youngStudents);

    // Find with Projection
    const namesOnly = await collection
      .find({}, { projection: { name: 1, email: 1, _id: 0 } })
      .toArray();
    console.log('Names only:', namesOnly);

    // Find with Sort and Limit
    const sortedDocs = await collection
      .find()
      .sort({ age: -1 })
      .limit(2)
      .toArray();
    console.log('Sorted and limited docs:', sortedDocs);

  } catch (err) {
    console.error('Error in read operations:', err);
  } finally {
    await client.close();
  }
}

// Run read operations
readOperations();
```

### 5. Update Operations

Create `update.js`:
```javascript
const { connect, client } = require('./db');

async function updateOperations() {
  const collection = await connect();

  try {
    // Update One Document
    const updateOneResult = await collection.updateOne(
      { name: "John Doe" },
      {
        $set: { age: 21 },
        $push: { courses: "Mobile Development" }
      }
    );
    console.log('Updated single document:', updateOneResult.modifiedCount);

    // Update Multiple Documents
    const updateManyResult = await collection.updateMany(
      { active: true },
      {
        $set: { lastUpdated: new Date() },
        $inc: { age: 1 }
      }
    );
    console.log('Updated multiple documents:', updateManyResult.modifiedCount);

    // Find One and Update
    const findAndUpdateResult = await collection.findOneAndUpdate(
      { name: "Jane Smith" },
      {
        $set: { email: "jane.smith@example.com" }
      },
      { returnDocument: 'after' }
    );
    console.log('Found and updated document:', findAndUpdateResult.value);

  } catch (err) {
    console.error('Error in update operations:', err);
  } finally {
    await client.close();
  }
}

// Run update operations
updateOperations();
```

### 6. Delete Operations

Create `delete.js`:
```javascript
const { connect, client } = require('./db');

async function deleteOperations() {
  const collection = await connect();

  try {
    // Delete One Document
    const deleteOneResult = await collection.deleteOne(
      { active: false }
    );
    console.log('Deleted single document:', deleteOneResult.deletedCount);

    // Delete Multiple Documents
    const deleteManyResult = await collection.deleteMany(
      { age: { $gt: 25 } }
    );
    console.log('Deleted multiple documents:', deleteManyResult.deletedCount);

    // Find One and Delete
    const findAndDeleteResult = await collection.findOneAndDelete(
      { name: "John Doe" }
    );
    console.log('Found and deleted document:', findAndDeleteResult.value);

  } catch (err) {
    console.error('Error in delete operations:', err);
  } finally {
    await client.close();
  }
}

// Run delete operations
deleteOperations();
```

### 7. Complex Queries

Create `complex-queries.js`:
```javascript
const { connect, client } = require('./db');

async function complexQueries() {
  const collection = await connect();

  try {
    // Aggregation Pipeline
    const aggregateResult = await collection.aggregate([
      { $match: { active: true } },
      { $group: {
          _id: null,
          avgAge: { $avg: "$age" },
          totalStudents: { $sum: 1 }
        }
      }
    ]).toArray();
    console.log('Aggregation result:', aggregateResult);

    // Complex Find Query
    const complexResult = await collection.find({
      $and: [
        { age: { $gte: 20, $lte: 25 } },
        { courses: { $in: ["Web Development", "Database"] } },
        { active: true }
      ]
    }).toArray();
    console.log('Complex query result:', complexResult);

    // Text Search (requires text index)
    await collection.createIndex({ name: "text", email: "text" });
    const searchResult = await collection.find({
      $text: { $search: "john smith" }
    }).toArray();
    console.log('Text search result:', searchResult);

  } catch (err) {
    console.error('Error in complex queries:', err);
  } finally {
    await client.close();
  }
}

// Run complex queries
complexQueries();
```

## 6. Expected Output

Run the scripts:
```bash
# Create operations
node create.js
# Output:
# Inserted single document: ObjectId("...")
# Inserted multiple documents: { '0': ObjectId("..."), '1': ObjectId("...") }

# Read operations
node read.js
# Output:
# Found single document: { ... }
# Found documents: [ ... ]
# Young students: [ ... ]
# Names only: [ ... ]
# Sorted and limited docs: [ ... ]

# Update operations
node update.js
# Output:
# Updated single document: 1
# Updated multiple documents: 2
# Found and updated document: { ... }

# Delete operations
node delete.js
# Output:
# Deleted single document: 1
# Deleted multiple documents: 0
# Found and deleted document: { ... }

# Complex queries
node complex-queries.js
# Output:
# Aggregation result: [ ... ]
# Complex query result: [ ... ]
# Text search result: [ ... ]
```

## 7. Practice Exercises
1. Create custom queries
2. Use different operators
3. Try bulk operations
4. Implement transactions
5. Create indexes

## 8. Verification and Validation
- [ ] All CRUD operations work
- [ ] Queries return expected results
- [ ] Error handling works
- [ ] Complex queries work
- [ ] Indexes created

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| Query not working | Check syntax |
| No results | Verify data exists |
| Connection error | Check connection string |
| Index error | Verify index exists |

## 10. Additional Resources
- [MongoDB CRUD Operations](https://docs.mongodb.com/manual/crud/)
- [MongoDB Query Operators](https://docs.mongodb.com/manual/reference/operator/)
- [MongoDB Aggregation](https://docs.mongodb.com/manual/aggregation/)
- [MongoDB Indexes](https://docs.mongodb.com/manual/indexes/)
