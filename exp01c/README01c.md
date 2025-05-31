# Experiment 1C: ExpressJS - Middleware Implementation

## 1. Experiment Information
- **Experiment Number**: 01C
- **Title**: Working with Express Middleware
- **Duration**: 1 hour

## 2. Learning Objectives
- Understand middleware concept in Express
- Implement custom middleware functions
- Use built-in middleware
- Apply middleware at different levels
- Handle errors using middleware

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- Visual Studio Code
- Postman (for testing)

### Knowledge Requirements
- Basic JavaScript
- Express basics
- HTTP request/response cycle

## 4. Theory and Concepts
### Middleware in Express
- Request processing pipeline
- Types of middleware
  - Application-level
  - Router-level
  - Error-handling
  - Built-in
  - Third-party

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create project directory
mkdir express-middleware-demo
cd express-middleware-demo

# Initialize project
npm init -y

# Install dependencies
npm install express morgan

# Create main file
touch index.js
```

### 2. Basic Server Setup with Middleware

Create index.js with the following content:
```javascript
const express = require('express');
const morgan = require('morgan');
const app = express();
const port = 3000;

// Built-in middleware for parsing JSON
app.use(express.json());

// Third-party middleware for logging
app.use(morgan('dev'));

// Custom middleware for request timestamp
const timestampMiddleware = (req, res, next) => {
    req.timestamp = new Date().toISOString();
    console.log(`Request received at: ${req.timestamp}`);
    next();
};

// Custom middleware for authentication
const authMiddleware = (req, res, next) => {
    const authHeader = req.headers.authorization;
    
    if (!authHeader || authHeader !== 'Bearer secret-token') {
        return res.status(401).json({ message: 'Unauthorized' });
    }
    
    req.user = { id: 1, name: 'Test User' };
    next();
};

// Custom middleware for request validation
const validateUserInput = (req, res, next) => {
    const { name, email } = req.body;
    
    if (!name || !email) {
        return res.status(400).json({ 
            message: 'Name and email are required' 
        });
    }
    
    if (typeof name !== 'string' || typeof email !== 'string') {
        return res.status(400).json({ 
            message: 'Invalid input types' 
        });
    }
    
    next();
};

// Apply timestamp middleware to all routes
app.use(timestampMiddleware);

// Public route
app.get('/public', (req, res) => {
    res.json({ 
        message: 'Public route', 
        timestamp: req.timestamp 
    });
});

// Protected routes using auth middleware
app.use('/api', authMiddleware);

// Protected API routes
app.get('/api/profile', (req, res) => {
    res.json({ 
        message: 'Protected profile route', 
        user: req.user,
        timestamp: req.timestamp 
    });
});

// Route with validation middleware
app.post('/api/users', validateUserInput, (req, res) => {
    res.status(201).json({ 
        message: 'User created', 
        data: req.body,
        timestamp: req.timestamp 
    });
});

// Error handling middleware
app.use((err, req, res, next) => {
    console.error(err.stack);
    res.status(500).json({ 
        message: 'Something went wrong!',
        error: err.message
    });
});

// 404 middleware
app.use((req, res) => {
    res.status(404).json({ 
        message: 'Route not found' 
    });
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

1. Public route (no auth required):
```http
GET http://localhost:3000/public
```
Response:
```json
{
    "message": "Public route",
    "timestamp": "2025-05-31T05:00:00.000Z"
}
```

2. Protected route without auth:
```http
GET http://localhost:3000/api/profile
```
Response:
```json
{
    "message": "Unauthorized"
}
```

3. Protected route with auth:
```http
GET http://localhost:3000/api/profile
Authorization: Bearer secret-token
```
Response:
```json
{
    "message": "Protected profile route",
    "user": {
        "id": 1,
        "name": "Test User"
    },
    "timestamp": "2025-05-31T05:00:00.000Z"
}
```

4. Create user with invalid data:
```http
POST http://localhost:3000/api/users
Authorization: Bearer secret-token
Content-Type: application/json

{
    "name": "John"
}
```
Response:
```json
{
    "message": "Name and email are required"
}
```

5. Create user with valid data:
```http
POST http://localhost:3000/api/users
Authorization: Bearer secret-token
Content-Type: application/json

{
    "name": "John Doe",
    "email": "john@example.com"
}
```
Response:
```json
{
    "message": "User created",
    "data": {
        "name": "John Doe",
        "email": "john@example.com"
    },
    "timestamp": "2025-05-31T05:00:00.000Z"
}
```

## 7. Practice Exercises
1. Create a rate limiting middleware
2. Implement request body sanitization
3. Add response compression middleware
4. Create a logging middleware that saves to file
5. Implement CORS middleware

## 8. Verification and Validation
- [ ] All middleware executes in correct order
- [ ] Authentication works properly
- [ ] Input validation is working
- [ ] Error handling catches all cases
- [ ] Logging is functional

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| Middleware not executing | Check order of app.use() |
| Auth token not working | Verify header format |
| Validation bypassed | Check middleware order |
| Errors not caught | Verify error middleware position |

## 10. Additional Resources
- [Express Middleware](https://expressjs.com/en/guide/using-middleware.html)
- [Writing Middleware](https://expressjs.com/en/guide/writing-middleware.html)
- [Error Handling](https://expressjs.com/en/guide/error-handling.html)
- [Popular Express Middleware](https://expressjs.com/en/resources/middleware.html)
