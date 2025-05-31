# Experiment 11A: MongoDB - Managing Databases and Collections

## 1. Experiment Information
- **Experiment Number**: 11A
- **Title**: Creating and Managing MongoDB Databases and Collections
- **Duration**: 45 minutes

## 2. Learning Objectives
- Create databases
- Create collections
- Drop databases
- Drop collections
- Manage database operations
- Understand MongoDB structure

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
- Previous experiments completion

## 4. Theory and Concepts
### MongoDB Structure
- Databases
- Collections
- Documents
- Indexes
- Views
- Capped collections

## 5. Step-by-step Instructions

### 1. Project Setup

Create a new directory and initialize:
```bash
mkdir mongodb-db-demo
cd mongodb-db-demo
npm init -y
npm install mongodb
```

### 2. Create Database Connection

Create `db.js`:
```javascript
const { MongoClient } = require('mongodb');

// Connection URI (replace with your connection string)
const uri = "mongodb+srv://fst2user:<password>@cluster0.xxxxx.mongodb.net/?retryWrites=true&w=majority";

// Create MongoDB client
const client = new MongoClient(uri);

// Connect to MongoDB
async function connect() {
  try {
    await client.connect();
    console.log('Connected to MongoDB');
    return client;
  } catch (err) {
    console.error('Error connecting to MongoDB:', err);
    throw err;
  }
}

// Export connection
module.exports = { connect, client };
```

### 3. Database Operations

Create `database-ops.js`:
```javascript
const { connect, client } = require('./db');

async function databaseOperations() {
  try {
    await connect();

    // List all databases
    const databasesList = await client.db().admin().listDatabases();
    console.log('Databases:', databasesList.databases);

    // Create new database by using it
    const db1 = client.db('university');
    await db1.createCollection('students');
    console.log('Created university database');

    // Create another database
    const db2 = client.db('library');
    await db2.createCollection('books');
    console.log('Created library database');

    // Drop database
    await client.db('library').dropDatabase();
    console.log('Dropped library database');

    // List databases after operations
    const updatedDatabasesList = await client.db().admin().listDatabases();
    console.log('Updated databases list:', updatedDatabasesList.databases);

  } catch (err) {
    console.error('Error in database operations:', err);
  } finally {
    await client.close();
  }
}

// Run database operations
databaseOperations();
```

### 4. Collection Operations

Create `collection-ops.js`:
```javascript
const { connect, client } = require('./db');

async function collectionOperations() {
  try {
    await connect();
    const db = client.db('university');

    // Create regular collection
    await db.createCollection('courses');
    console.log('Created courses collection');

    // Create capped collection
    await db.createCollection('logs', {
      capped: true,
      size: 1024 * 1024, // 1MB
      max: 100 // maximum 100 documents
    });
    console.log('Created capped logs collection');

    // Create collection with validation
    await db.createCollection('professors', {
      validator: {
        $jsonSchema: {
          bsonType: "object",
          required: ["name", "department", "email"],
          properties: {
            name: {
              bsonType: "string",
              description: "must be a string and is required"
            },
            department: {
              bsonType: "string",
              description: "must be a string and is required"
            },
            email: {
              bsonType: "string",
              pattern: "^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}$",
              description: "must be a valid email and is required"
            }
          }
        }
      },
      validationLevel: "strict"
    });
    console.log('Created professors collection with validation');

    // List all collections
    const collections = await db.listCollections().toArray();
    console.log('Collections:', collections);

    // Create time series collection
    await db.createCollection('sensorData', {
      timeseries: {
        timeField: "timestamp",
        metaField: "sensorId",
        granularity: "minutes"
      }
    });
    console.log('Created time series collection');

    // Drop collection
    await db.collection('logs').drop();
    console.log('Dropped logs collection');

    // Updated collections list
    const updatedCollections = await db.listCollections().toArray();
    console.log('Updated collections:', updatedCollections);

  } catch (err) {
    console.error('Error in collection operations:', err);
  } finally {
    await client.close();
  }
}

// Run collection operations
collectionOperations();
```

### 5. Collection Views

Create `collection-views.js`:
```javascript
const { connect, client } = require('./db');

async function viewOperations() {
  try {
    await connect();
    const db = client.db('university');

    // Insert sample data
    await db.collection('students').insertMany([
      {
        name: "John Doe",
        department: "Computer Science",
        gpa: 3.8,
        year: 2
      },
      {
        name: "Jane Smith",
        department: "Physics",
        gpa: 3.9,
        year: 3
      },
      {
        name: "Bob Wilson",
        department: "Computer Science",
        gpa: 3.5,
        year: 2
      }
    ]);

    // Create view for CS students
    await db.createCollection('csStudents', {
      viewOn: 'students',
      pipeline: [
        {
          $match: {
            department: "Computer Science"
          }
        },
        {
          $project: {
            _id: 0,
            name: 1,
            gpa: 1,
            year: 1
          }
        }
      ]
    });
    console.log('Created CS students view');

    // Query the view
    const csStudents = await db.collection('csStudents').find().toArray();
    console.log('CS Students:', csStudents);

    // Drop view
    await db.collection('csStudents').drop();
    console.log('Dropped CS students view');

  } catch (err) {
    console.error('Error in view operations:', err);
  } finally {
    await client.close();
  }
}

// Run view operations
viewOperations();
```

## 6. Expected Output

Run the scripts:
```bash
# Database operations
node database-ops.js
# Output:
# Connected to MongoDB
# Databases: [...]
# Created university database
# Created library database
# Dropped library database
# Updated databases list: [...]

# Collection operations
node collection-ops.js
# Output:
# Connected to MongoDB
# Created courses collection
# Created capped logs collection
# Created professors collection with validation
# Collections: [...]
# Created time series collection
# Dropped logs collection
# Updated collections: [...]

# View operations
node collection-views.js
# Output:
# Connected to MongoDB
# Created CS students view
# CS Students: [...]
# Dropped CS students view
```

## 7. Practice Exercises
1. Create different collection types
2. Add custom validation
3. Create complex views
4. Manage multiple databases
5. Use different collection options

## 8. Verification and Validation
- [ ] Databases created/dropped
- [ ] Collections created/dropped
- [ ] Views working
- [ ] Validation working
- [ ] Operations successful

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| Permission denied | Check user roles |
| Invalid collection | Verify name/options |
| View error | Check pipeline |
| Drop fails | Check dependencies |

## 10. Additional Resources
- [MongoDB Databases](https://docs.mongodb.com/manual/core/databases-and-collections/)
- [MongoDB Collections](https://docs.mongodb.com/manual/core/collections/)
- [MongoDB Views](https://docs.mongodb.com/manual/core/views/)
- [Collection Validation](https://docs.mongodb.com/manual/core/schema-validation/)
