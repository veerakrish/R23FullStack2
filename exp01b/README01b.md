# Experiment 1B: ExpressJS - HTTP Methods and Data Handling

## 1. Experiment Information
- **Experiment Number**: 01B
- **Title**: Working with HTTP Methods (GET, POST, PUT, DELETE)
- **Duration**: 1 hour

## 2. Learning Objectives
- Implement different HTTP methods
- Handle data submission through forms
- Create RESTful API endpoints
- Manage data using HTTP methods
- Test API endpoints using Postman

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- Visual Studio Code
- Postman (for testing)

### Knowledge Requirements
- Basic JavaScript
- HTTP methods understanding
- JSON data format
- Express basics (Experiment 1A)

## 4. Theory and Concepts
### HTTP Methods
- GET: Retrieve data
- POST: Create new data
- PUT: Update existing data
- DELETE: Remove data

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create project directory
mkdir express-http-methods
cd express-http-methods

# Initialize project
npm init -y

# Install dependencies
npm install express body-parser

# Create main file
touch index.js
```

### 2. Basic Server Setup

Create index.js with the following content:
```javascript
const express = require('express');
const bodyParser = require('body-parser');
const app = express();
const port = 3000;

// Middleware for parsing JSON bodies
app.use(bodyParser.json());
// Middleware for parsing URL-encoded bodies
app.use(bodyParser.urlencoded({ extended: true }));

// In-memory data store
let users = [
    { id: 1, name: 'John Doe', email: 'john@example.com' },
    { id: 2, name: 'Jane Smith', email: 'jane@example.com' }
];

// GET - Retrieve all users
app.get('/users', (req, res) => {
    res.json(users);
});

// GET - Retrieve specific user
app.get('/users/:id', (req, res) => {
    const user = users.find(u => u.id === parseInt(req.params.id));
    if (!user) {
        return res.status(404).json({ message: 'User not found' });
    }
    res.json(user);
});

// POST - Create new user
app.post('/users', (req, res) => {
    const { name, email } = req.body;
    
    // Validate input
    if (!name || !email) {
        return res.status(400).json({ message: 'Name and email are required' });
    }

    const newUser = {
        id: users.length + 1,
        name,
        email
    };
    
    users.push(newUser);
    res.status(201).json(newUser);
});

// PUT - Update user
app.put('/users/:id', (req, res) => {
    const { name, email } = req.body;
    const id = parseInt(req.params.id);
    
    // Find user
    const userIndex = users.findIndex(u => u.id === id);
    if (userIndex === -1) {
        return res.status(404).json({ message: 'User not found' });
    }

    // Update user
    users[userIndex] = {
        ...users[userIndex],
        name: name || users[userIndex].name,
        email: email || users[userIndex].email
    };

    res.json(users[userIndex]);
});

// DELETE - Remove user
app.delete('/users/:id', (req, res) => {
    const id = parseInt(req.params.id);
    
    // Find user
    const userIndex = users.findIndex(u => u.id === id);
    if (userIndex === -1) {
        return res.status(404).json({ message: 'User not found' });
    }

    // Remove user
    const deletedUser = users[userIndex];
    users = users.filter(u => u.id !== id);

    res.json({ message: 'User deleted', user: deletedUser });
});

// Start server
app.listen(port, () => {
    console.log(`Server running at http://localhost:${port}`);
});
```

## 6. Expected Output

Start the server:
```bash
node index.js
```

Test the following endpoints in Postman:

1. Get all users:
```http
GET http://localhost:3000/users
```
Response:
```json
[
    {"id": 1, "name": "John Doe", "email": "john@example.com"},
    {"id": 2, "name": "Jane Smith", "email": "jane@example.com"}
]
```

2. Get specific user:
```http
GET http://localhost:3000/users/1
```
Response:
```json
{
    "id": 1,
    "name": "John Doe",
    "email": "john@example.com"
}
```

3. Create new user:
```http
POST http://localhost:3000/users
Content-Type: application/json

{
    "name": "Bob Wilson",
    "email": "bob@example.com"
}
```
Response:
```json
{
    "id": 3,
    "name": "Bob Wilson",
    "email": "bob@example.com"
}
```

4. Update user:
```http
PUT http://localhost:3000/users/1
Content-Type: application/json

{
    "name": "John Smith"
}
```
Response:
```json
{
    "id": 1,
    "name": "John Smith",
    "email": "john@example.com"
}
```

5. Delete user:
```http
DELETE http://localhost:3000/users/2
```
Response:
```json
{
    "message": "User deleted",
    "user": {
        "id": 2,
        "name": "Jane Smith",
        "email": "jane@example.com"
    }
}
```

## 7. Practice Exercises
1. Add data validation for email format
2. Implement search functionality
3. Add pagination to GET /users
4. Implement bulk delete operation
5. Add filtering capabilities

## 8. Verification and Validation
- [ ] All HTTP methods work correctly
- [ ] Data validation is implemented
- [ ] Error handling is in place
- [ ] Status codes are appropriate
- [ ] JSON responses are properly formatted

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| Body undefined | Check body-parser middleware |
| Invalid JSON | Verify request body format |
| 404 errors | Check route paths and methods |
| Validation errors | Review request data |

## 10. Additional Resources
- [Express Request Methods](https://expressjs.com/en/4x/api.html#req)
- [Express Response Methods](https://expressjs.com/en/4x/api.html#res)
- [HTTP Status Codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
- [REST API Design Best Practices](https://restfulapi.net/)
