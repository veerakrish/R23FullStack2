# Experiment 11B: MongoDB - Working with Records

## 1. Experiment Information
- **Experiment Number**: 11B
- **Title**: Advanced MongoDB Record Operations
- **Duration**: 45 minutes

## 2. Learning Objectives
- Use find() operations
- Apply limit() and sort()
- Create and use indexes
- Perform aggregations
- Handle complex queries
- Optimize query performance

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
### MongoDB Operations
- Query operators
- Cursor methods
- Indexes
- Aggregation pipeline
- Query optimization
- Projection operators

## 5. Step-by-step Instructions

### 1. Project Setup

Create a new directory and initialize:
```bash
mkdir mongodb-records-demo
cd mongodb-records-demo
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
    return client.db('university').collection('students');
  } catch (err) {
    console.error('Error connecting to MongoDB:', err);
    throw err;
  }
}

// Export connection
module.exports = { connect, client };
```

### 3. Sample Data Setup

Create `setup-data.js`:
```javascript
const { connect, client } = require('./db');

async function setupData() {
  const collection = await connect();

  try {
    // Drop existing data
    await collection.drop();

    // Insert sample data
    await collection.insertMany([
      {
        name: "John Doe",
        age: 20,
        department: "Computer Science",
        gpa: 3.8,
        courses: ["Web Development", "Database", "AI"],
        grades: { midterm: 90, final: 95 },
        joinDate: new Date("2023-09-01"),
        isActive: true
      },
      {
        name: "Jane Smith",
        age: 22,
        department: "Physics",
        gpa: 3.9,
        courses: ["Quantum Mechanics", "Mathematics", "Lab Research"],
        grades: { midterm: 88, final: 92 },
        joinDate: new Date("2023-09-01"),
        isActive: true
      },
      {
        name: "Bob Wilson",
        age: 21,
        department: "Computer Science",
        gpa: 3.5,
        courses: ["Networking", "Security", "Web Development"],
        grades: { midterm: 85, final: 88 },
        joinDate: new Date("2023-09-01"),
        isActive: true
      },
      {
        name: "Alice Brown",
        age: 23,
        department: "Mathematics",
        gpa: 4.0,
        courses: ["Calculus", "Linear Algebra", "Statistics"],
        grades: { midterm: 98, final: 99 },
        joinDate: new Date("2023-09-01"),
        isActive: true
      },
      {
        name: "Charlie Davis",
        age: 20,
        department: "Computer Science",
        gpa: 3.2,
        courses: ["Programming", "Database", "Web Development"],
        grades: { midterm: 82, final: 85 },
        joinDate: new Date("2023-09-01"),
        isActive: false
      }
    ]);

    console.log('Sample data inserted');

  } catch (err) {
    console.error('Error setting up data:', err);
  } finally {
    await client.close();
  }
}

// Run setup
setupData();
```

### 4. Find Operations

Create `find-ops.js`:
```javascript
const { connect, client } = require('./db');

async function findOperations() {
  const collection = await connect();

  try {
    // Basic find
    const allStudents = await collection.find().toArray();
    console.log('All students:', allStudents);

    // Find with criteria
    const csStudents = await collection.find({
      department: "Computer Science"
    }).toArray();
    console.log('CS students:', csStudents);

    // Find with projection
    const namesOnly = await collection.find({}, {
      projection: { name: 1, department: 1, _id: 0 }
    }).toArray();
    console.log('Names only:', namesOnly);

    // Find one
    const oneStudent = await collection.findOne({ gpa: { $gte: 3.9 } });
    console.log('High GPA student:', oneStudent);

    // Complex find
    const complexQuery = await collection.find({
      $and: [
        { age: { $lt: 22 } },
        { department: "Computer Science" },
        { "grades.midterm": { $gte: 85 } }
      ]
    }).toArray();
    console.log('Complex query results:', complexQuery);

  } catch (err) {
    console.error('Error in find operations:', err);
  } finally {
    await client.close();
  }
}

// Run find operations
findOperations();
```

### 5. Sort and Limit Operations

Create `sort-limit-ops.js`:
```javascript
const { connect, client } = require('./db');

async function sortLimitOperations() {
  const collection = await connect();

  try {
    // Sort by GPA descending
    const topStudents = await collection
      .find()
      .sort({ gpa: -1 })
      .limit(3)
      .toArray();
    console.log('Top 3 students by GPA:', topStudents);

    // Sort by multiple fields
    const sortedStudents = await collection
      .find()
      .sort({ department: 1, gpa: -1 })
      .toArray();
    console.log('Students sorted by department and GPA:', sortedStudents);

    // Skip and limit (pagination)
    const page2 = await collection
      .find()
      .skip(2)
      .limit(2)
      .toArray();
    console.log('Page 2 (2 students):', page2);

  } catch (err) {
    console.error('Error in sort/limit operations:', err);
  } finally {
    await client.close();
  }
}

// Run sort and limit operations
sortLimitOperations();
```

### 6. Index Operations

Create `index-ops.js`:
```javascript
const { connect, client } = require('./db');

async function indexOperations() {
  const collection = await connect();

  try {
    // Create single index
    await collection.createIndex({ department: 1 });
    console.log('Created department index');

    // Create compound index
    await collection.createIndex({ department: 1, gpa: -1 });
    console.log('Created compound index');

    // Create text index
    await collection.createIndex({ name: "text" });
    console.log('Created text index');

    // List indexes
    const indexes = await collection.listIndexes().toArray();
    console.log('Indexes:', indexes);

    // Use text index
    const textSearchResults = await collection
      .find({ $text: { $search: "John" } })
      .toArray();
    console.log('Text search results:', textSearchResults);

    // Drop index
    await collection.dropIndex("name_text");
    console.log('Dropped text index');

  } catch (err) {
    console.error('Error in index operations:', err);
  } finally {
    await client.close();
  }
}

// Run index operations
indexOperations();
```

### 7. Aggregation Operations

Create `aggregate-ops.js`:
```javascript
const { connect, client } = require('./db');

async function aggregateOperations() {
  const collection = await connect();

  try {
    // Simple aggregation (group by department)
    const departmentStats = await collection.aggregate([
      {
        $group: {
          _id: "$department",
          averageGPA: { $avg: "$gpa" },
          totalStudents: { $sum: 1 }
        }
      }
    ]).toArray();
    console.log('Department statistics:', departmentStats);

    // Complex aggregation
    const courseAnalysis = await collection.aggregate([
      // Unwind courses array
      { $unwind: "$courses" },
      // Group by course
      {
        $group: {
          _id: "$courses",
          studentCount: { $sum: 1 },
          averageGPA: { $avg: "$gpa" },
          students: { $push: "$name" }
        }
      },
      // Sort by student count
      { $sort: { studentCount: -1 } }
    ]).toArray();
    console.log('Course analysis:', courseAnalysis);

    // Advanced aggregation with multiple stages
    const gradeAnalysis = await collection.aggregate([
      // Match active students
      { $match: { isActive: true } },
      // Add calculated fields
      {
        $addFields: {
          averageGrade: {
            $avg: ["$grades.midterm", "$grades.final"]
          }
        }
      },
      // Group by department
      {
        $group: {
          _id: "$department",
          avgGPA: { $avg: "$gpa" },
          avgGrade: { $avg: "$averageGrade" },
          highestGPA: { $max: "$gpa" },
          students: {
            $push: {
              name: "$name",
              gpa: "$gpa",
              averageGrade: "$averageGrade"
            }
          }
        }
      },
      // Sort by average GPA
      { $sort: { avgGPA: -1 } }
    ]).toArray();
    console.log('Grade analysis:', gradeAnalysis);

  } catch (err) {
    console.error('Error in aggregation operations:', err);
  } finally {
    await client.close();
  }
}

// Run aggregation operations
aggregateOperations();
```

## 6. Expected Output

Run the scripts:
```bash
# Setup data
node setup-data.js
# Output:
# Connected to MongoDB
# Sample data inserted

# Find operations
node find-ops.js
# Output:
# Connected to MongoDB
# All students: [...]
# CS students: [...]
# Names only: [...]
# High GPA student: {...}
# Complex query results: [...]

# Sort and limit operations
node sort-limit-ops.js
# Output:
# Connected to MongoDB
# Top 3 students by GPA: [...]
# Students sorted by department and GPA: [...]
# Page 2 (2 students): [...]

# Index operations
node index-ops.js
# Output:
# Connected to MongoDB
# Created department index
# Created compound index
# Created text index
# Indexes: [...]
# Text search results: [...]
# Dropped text index

# Aggregation operations
node aggregate-ops.js
# Output:
# Connected to MongoDB
# Department statistics: [...]
# Course analysis: [...]
# Grade analysis: [...]
```

## 7. Practice Exercises
1. Create complex queries
2. Use different indexes
3. Write aggregation pipelines
4. Implement pagination
5. Optimize queries

## 8. Verification and Validation
- [ ] Find operations work
- [ ] Sort/limit works
- [ ] Indexes created/used
- [ ] Aggregations correct
- [ ] Performance good

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| Slow queries | Check indexes |
| Memory issues | Use cursor methods |
| Wrong results | Verify query logic |
| Index not used | Check query planner |

## 10. Additional Resources
- [MongoDB Query Operations](https://docs.mongodb.com/manual/tutorial/query-documents/)
- [MongoDB Indexes](https://docs.mongodb.com/manual/indexes/)
- [MongoDB Aggregation](https://docs.mongodb.com/manual/aggregation/)
- [Query Optimization](https://docs.mongodb.com/manual/core/query-optimization/)
