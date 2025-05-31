# Experiment 1A: ExpressJS - Routes and Parameters

## 1. Experiment Information
- **Experiment Number**: 01A
- **Title**: Routes, Route Parameters, Query Parameters, and URL Building
- **Duration**: 1 hour

## 2. Learning Objectives
- Define and handle different types of routes in Express
- Work with route parameters
- Handle query parameters
- Implement URL building
- Understand route organization

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- Visual Studio Code
- Postman (for testing)

### Knowledge Requirements
- Basic JavaScript
- HTTP methods
- URL structure

## 4. Theory and Concepts
### Routes in Express
- Basic routing
- Route methods (GET, POST, etc.)
- Route paths
- Route parameters
- Query strings
- Route handlers

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create project directory
mkdir express-routing-demo
cd express-routing-demo

# Initialize project
npm init -y

# Install dependencies
npm install express

# Create main file
touch index.js
```

### 2. Basic Routes Implementation

Create index.js with the following content:
```javascript
const express = require('express');
const app = express();
const port = 3000;

// Basic route
app.get('/', (req, res) => {
    res.send('Welcome to Express Routing Demo!');
});

// Route with parameters
app.get('/user/:id', (req, res) => {
    res.send(`User ID: ${req.params.id}`);
});

// Route with multiple parameters
app.get('/users/:userId/books/:bookId', (req, res) => {
    const { userId, bookId } = req.params;
    res.send(`User ID: ${userId}, Book ID: ${bookId}`);
});

// Route with query parameters
app.get('/search', (req, res) => {
    const { q, category } = req.query;
    res.send(`Search Query: ${q}, Category: ${category}`);
});

// Optional parameters
app.get('/products/:category?', (req, res) => {
    const category = req.params.category || 'all';
    res.send(`Products Category: ${category}`);
});

// Regular expression in routes
app.get(/.*fly$/, (req, res) => {
    res.send('URL ends with "fly"');
});

// Start server
app.listen(port, () => {
    console.log(`Server running at http://localhost:${port}`);
});
```

### 3. Route Organization

Create a separate routes directory and file for better organization:

```bash
# Create routes directory
mkdir routes
```

Create routes/userRoutes.js:
```javascript
const express = require('express');
const router = express.Router();

// User routes
router.get('/', (req, res) => {
    res.send('Users list');
});

router.get('/profile/:username', (req, res) => {
    res.send(`Profile of ${req.params.username}`);
});

router.get('/settings', (req, res) => {
    const { theme, notifications } = req.query;
    res.send(`Settings - Theme: ${theme}, Notifications: ${notifications}`);
});

module.exports = router;
```

Update index.js to use the router:
```javascript
const express = require('express');
const userRoutes = require('./routes/userRoutes');
const app = express();
const port = 3000;

// Use user routes
app.use('/users', userRoutes);

// ... previous routes ...

app.listen(port, () => {
    console.log(`Server running at http://localhost:${port}`);
});
```

## 6. Expected Output

Start the server:
```bash
node index.js
```

Test the following URLs in Postman:

1. Basic route:
   - GET http://localhost:3000/
   ```
   Welcome to Express Routing Demo!
   ```

2. Route with parameters:
   - GET http://localhost:3000/user/123
   ```
   User ID: 123
   ```

3. Multiple parameters:
   - GET http://localhost:3000/users/123/books/456
   ```
   User ID: 123, Book ID: 456
   ```

4. Query parameters:
   - GET http://localhost:3000/search?q=express&category=tutorial
   ```
   Search Query: express, Category: tutorial
   ```

5. Optional parameters:
   - GET http://localhost:3000/products
   ```
   Products Category: all
   ```
   - GET http://localhost:3000/products/electronics
   ```
   Products Category: electronics
   ```

## 7. Practice Exercises
1. Create a route that accepts multiple optional parameters
2. Implement nested routes for a blog (posts/comments)
3. Add route handlers with array callbacks
4. Create routes with regular expression patterns
5. Implement URL building with query strings

## 8. Verification and Validation
- [ ] All routes respond correctly
- [ ] Parameters are properly extracted
- [ ] Query strings are handled
- [ ] Route organization is clean
- [ ] Error handling is implemented

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| 404 errors | Check route path spelling |
| Parameter undefined | Verify parameter name in URL |
| Query string issues | Check URL encoding |
| Route conflicts | Review route order |

## 10. Additional Resources
- [Express Routing Guide](https://expressjs.com/en/guide/routing.html)
- [Route Parameters](https://expressjs.com/en/guide/routing.html#route-parameters)
- [Query Strings](https://expressjs.com/en/api.html#req.query)
- [Express Router](https://expressjs.com/en/guide/routing.html#express-router)
