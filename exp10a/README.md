# Experiment 10A: MongoDB - Installation and Atlas Configuration

## 1. Experiment Information
- **Experiment Number**: 10A
- **Title**: MongoDB Installation and Atlas Setup
- **Duration**: 45 minutes

## 2. Learning Objectives
- Install MongoDB locally
- Set up MongoDB Atlas
- Configure database access
- Create clusters
- Connect to databases
- Understand MongoDB basics

## 3. Prerequisites
### Software Requirements
- Web browser
- MongoDB Community Server
- MongoDB Compass
- Node.js (v14+ LTS)
- npm (v6+)

### Knowledge Requirements
- Basic database concepts
- JSON format
- Previous experiments completion

## 4. Theory and Concepts
### MongoDB Basics
- Document databases
- Collections
- Documents
- BSON format
- MongoDB Atlas
- Connection strings

## 5. Step-by-step Instructions

### 1. Install MongoDB Community Server

1. Visit the MongoDB Download Center:
   ```
   https://www.mongodb.com/try/download/community
   ```

2. Download MongoDB Community Server for Windows:
   - Select "Windows" as platform
   - Choose "msi" package
   - Click "Download"

3. Run the installer:
   - Accept the license agreement
   - Choose "Complete" installation
   - Install MongoDB Compass (GUI tool)
   - Complete the installation

4. Verify installation:
   ```bash
   # Check MongoDB version
   mongod --version

   # Check MongoDB service status
   services.msc
   # Look for "MongoDB" service - should be running
   ```

### 2. Set Up MongoDB Atlas

1. Create MongoDB Atlas Account:
   - Visit: https://www.mongodb.com/cloud/atlas
   - Click "Try Free"
   - Fill in registration details
   - Verify email

2. Create New Project:
   ```
   1. Click "New Project"
   2. Name: "fst2-lab"
   3. Click "Create Project"
   ```

3. Create Free Cluster:
   ```
   1. Click "Build a Database"
   2. Choose "FREE" tier
   3. Select:
      - Cloud Provider: AWS
      - Region: Mumbai (ap-south-1)
   4. Click "Create"
   ```

4. Configure Security:
   ```
   1. Create Database User:
      - Username: "fst2user"
      - Password: Generate secure password
      - Role: "Read and write to any database"
      
   2. Configure Network Access:
      - Click "Network Access"
      - Click "Add IP Address"
      - Click "Allow Access from Anywhere"
      - Confirm "0.0.0.0/0"
   ```

5. Get Connection String:
   ```
   1. Click "Connect"
   2. Choose "Connect your application"
   3. Select:
      - Driver: "Node.js"
      - Version: "4.1 or later"
   4. Copy connection string:
      mongodb+srv://fst2user:<password>@cluster0.xxxxx.mongodb.net/?retryWrites=true&w=majority
   ```

### 3. Connect Using MongoDB Compass

1. Open MongoDB Compass

2. Create New Connection:
   ```
   1. Click "New Connection"
   2. Paste Atlas connection string
   3. Replace <password> with your password
   4. Click "Connect"
   ```

3. Create Database and Collection:
   ```
   1. Click "Create Database"
   2. Database Name: "fst2db"
   3. Collection Name: "students"
   4. Click "Create"
   ```

### 4. Test Connection with Node.js

Create a new file `test-connection.js`:
```javascript
const { MongoClient } = require('mongodb');

// Connection URI (replace with your connection string)
const uri = "mongodb+srv://fst2user:<password>@cluster0.xxxxx.mongodb.net/?retryWrites=true&w=majority";

async function testConnection() {
  const client = new MongoClient(uri);

  try {
    // Connect to MongoDB
    await client.connect();
    console.log("Successfully connected to MongoDB Atlas!");

    // Get database and collection
    const database = client.db("fst2db");
    const collection = database.collection("students");

    // Test insert
    const testDoc = {
      name: "Test Student",
      email: "test@example.com",
      createdAt: new Date()
    };

    const result = await collection.insertOne(testDoc);
    console.log("Inserted test document:", result.insertedId);

  } catch (err) {
    console.error("Error connecting to MongoDB:", err);
  } finally {
    // Close the connection
    await client.close();
  }
}

// Run the test
testConnection();
```

Run the test:
```bash
# Install MongoDB driver
npm init -y
npm install mongodb

# Run test script
node test-connection.js
```

## 6. Expected Output

1. MongoDB Installation:
   - MongoDB service running
   - Version check successful
   - Compass installed

2. Atlas Setup:
   - Account created
   - Project created
   - Cluster running
   - Security configured

3. Compass Connection:
   - Successfully connected
   - Database created
   - Collection created

4. Node.js Test:
   ```
   Successfully connected to MongoDB Atlas!
   Inserted test document: ObjectId("...")
   ```

## 7. Practice Exercises
1. Create multiple databases
2. Add sample collections
3. Try different connection methods
4. Configure replica sets
5. Set up monitoring

## 8. Verification and Validation
- [ ] MongoDB installed
- [ ] Atlas configured
- [ ] Security set up
- [ ] Connection works
- [ ] Test successful

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| Connection fails | Check network/firewall |
| Auth error | Verify credentials |
| Service not running | Check MongoDB service |
| Compass not connecting | Check connection string |

## 10. Additional Resources
- [MongoDB Installation Guide](https://docs.mongodb.com/manual/installation/)
- [Atlas Documentation](https://docs.atlas.mongodb.com/)
- [MongoDB Node.js Driver](https://docs.mongodb.com/drivers/node/)
- [MongoDB Compass Guide](https://docs.mongodb.com/compass/current/)
