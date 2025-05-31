# Experiment 3B: ExpressJS - User Authentication

## 1. Experiment Information
- **Experiment Number**: 03B
- **Title**: Implementing User Authentication
- **Duration**: 1 hour

## 2. Learning Objectives
- Implement user registration and login
- Hash and verify passwords
- Create protected routes
- Manage user sessions
- Handle authentication errors

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- Visual Studio Code
- MongoDB

### Knowledge Requirements
- Basic JavaScript
- Express basics
- Session management (Experiment 3A)
- Basic MongoDB concepts

## 4. Theory and Concepts
### Authentication
- Password hashing
- Session-based auth
- Protected routes
- User management
- Security best practices

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create project directory
mkdir express-auth-demo
cd express-auth-demo

# Initialize project
npm init -y

# Install dependencies
npm install express express-session bcryptjs mongoose connect-mongo ejs

# Create directories
mkdir views
mkdir models
mkdir middleware
```

### 2. Create User Model

Create models/User.js:
```javascript
const mongoose = require('mongoose');
const bcrypt = require('bcryptjs');

const userSchema = new mongoose.Schema({
    username: {
        type: String,
        required: true,
        unique: true,
        trim: true
    },
    email: {
        type: String,
        required: true,
        unique: true,
        trim: true,
        lowercase: true
    },
    password: {
        type: String,
        required: true
    },
    createdAt: {
        type: Date,
        default: Date.now
    }
});

// Hash password before saving
userSchema.pre('save', async function(next) {
    if (!this.isModified('password')) return next();
    
    try {
        const salt = await bcrypt.genSalt(10);
        this.password = await bcrypt.hash(this.password, salt);
        next();
    } catch (error) {
        next(error);
    }
});

// Method to compare password
userSchema.methods.comparePassword = async function(candidatePassword) {
    return bcrypt.compare(candidatePassword, this.password);
};

module.exports = mongoose.model('User', userSchema);
```

### 3. Create Authentication Middleware

Create middleware/auth.js:
```javascript
const isAuthenticated = (req, res, next) => {
    if (req.session.userId) {
        return next();
    }
    res.redirect('/login');
};

const isGuest = (req, res, next) => {
    if (!req.session.userId) {
        return next();
    }
    res.redirect('/dashboard');
};

module.exports = { isAuthenticated, isGuest };
```

### 4. Create Server

Create index.js:
```javascript
const express = require('express');
const session = require('express-session');
const mongoose = require('mongoose');
const MongoStore = require('connect-mongo');
const User = require('./models/User');
const { isAuthenticated, isGuest } = require('./middleware/auth');

const app = express();
const port = 3000;

// Connect to MongoDB
mongoose.connect('mongodb://localhost/auth_demo', {
    useNewUrlParser: true,
    useUnifiedTopology: true
});

// Middleware
app.use(express.urlencoded({ extended: true }));
app.use(session({
    secret: 'your-secret-key',
    resave: false,
    saveUninitialized: false,
    store: MongoStore.create({
        mongoUrl: 'mongodb://localhost/auth_demo'
    }),
    cookie: {
        maxAge: 1000 * 60 * 60 * 24 // 1 day
    }
}));

// Set view engine
app.set('view engine', 'ejs');

// Make user data available to all templates
app.use(async (req, res, next) => {
    if (req.session.userId) {
        try {
            const user = await User.findById(req.session.userId);
            res.locals.user = user;
        } catch (error) {
            console.error('Error fetching user:', error);
        }
    }
    next();
});

// Routes
app.get('/', (req, res) => {
    res.render('home');
});

app.get('/register', isGuest, (req, res) => {
    res.render('register', { error: null });
});

app.post('/register', isGuest, async (req, res) => {
    try {
        const { username, email, password, confirmPassword } = req.body;

        // Basic validation
        if (password !== confirmPassword) {
            return res.render('register', { 
                error: 'Passwords do not match' 
            });
        }

        // Check if user exists
        const existingUser = await User.findOne({ 
            $or: [{ email }, { username }] 
        });
        
        if (existingUser) {
            return res.render('register', { 
                error: 'Username or email already exists' 
            });
        }

        // Create new user
        const user = new User({ username, email, password });
        await user.save();

        // Auto-login after registration
        req.session.userId = user._id;
        res.redirect('/dashboard');
    } catch (error) {
        res.render('register', { 
            error: 'Error creating user' 
        });
    }
});

app.get('/login', isGuest, (req, res) => {
    res.render('login', { error: null });
});

app.post('/login', isGuest, async (req, res) => {
    try {
        const { username, password } = req.body;

        // Find user
        const user = await User.findOne({ username });
        if (!user) {
            return res.render('login', { 
                error: 'Invalid credentials' 
            });
        }

        // Verify password
        const isMatch = await user.comparePassword(password);
        if (!isMatch) {
            return res.render('login', { 
                error: 'Invalid credentials' 
            });
        }

        // Set session
        req.session.userId = user._id;
        res.redirect('/dashboard');
    } catch (error) {
        res.render('login', { 
            error: 'Error during login' 
        });
    }
});

app.get('/dashboard', isAuthenticated, (req, res) => {
    res.render('dashboard');
});

app.get('/profile', isAuthenticated, (req, res) => {
    res.render('profile');
});

app.post('/logout', isAuthenticated, (req, res) => {
    req.session.destroy(err => {
        if (err) {
            return res.redirect('/dashboard');
        }
        res.redirect('/login');
    });
});

// Start server
app.listen(port, () => {
    console.log(`Server running at http://localhost:${port}`);
});
```

### 5. Create Views

Create views/home.ejs:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Home</title>
    <%- include('partials/styles') %>
</head>
<body>
    <%- include('partials/nav') %>
    
    <div class="container">
        <h1>Welcome to Auth Demo</h1>
        <% if (!locals.user) { %>
            <div class="auth-buttons">
                <a href="/login" class="btn">Login</a>
                <a href="/register" class="btn">Register</a>
            </div>
        <% } %>
    </div>
</body>
</html>
```

Create views/register.ejs:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Register</title>
    <%- include('partials/styles') %>
</head>
<body>
    <%- include('partials/nav') %>
    
    <div class="container">
        <h1>Register</h1>
        
        <% if (error) { %>
            <div class="error"><%= error %></div>
        <% } %>

        <form action="/register" method="POST" class="auth-form">
            <div class="form-group">
                <label for="username">Username:</label>
                <input type="text" id="username" name="username" required>
            </div>

            <div class="form-group">
                <label for="email">Email:</label>
                <input type="email" id="email" name="email" required>
            </div>

            <div class="form-group">
                <label for="password">Password:</label>
                <input type="password" id="password" name="password" required>
            </div>

            <div class="form-group">
                <label for="confirmPassword">Confirm Password:</label>
                <input type="password" id="confirmPassword" name="confirmPassword" required>
            </div>

            <button type="submit" class="btn">Register</button>
        </form>

        <p>Already have an account? <a href="/login">Login</a></p>
    </div>
</body>
</html>
```

Create views/login.ejs:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Login</title>
    <%- include('partials/styles') %>
</head>
<body>
    <%- include('partials/nav') %>
    
    <div class="container">
        <h1>Login</h1>
        
        <% if (error) { %>
            <div class="error"><%= error %></div>
        <% } %>

        <form action="/login" method="POST" class="auth-form">
            <div class="form-group">
                <label for="username">Username:</label>
                <input type="text" id="username" name="username" required>
            </div>

            <div class="form-group">
                <label for="password">Password:</label>
                <input type="password" id="password" name="password" required>
            </div>

            <button type="submit" class="btn">Login</button>
        </form>

        <p>Don't have an account? <a href="/register">Register</a></p>
    </div>
</body>
</html>
```

Create views/dashboard.ejs:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard</title>
    <%- include('partials/styles') %>
</head>
<body>
    <%- include('partials/nav') %>
    
    <div class="container">
        <h1>Dashboard</h1>
        <p>Welcome, <%= user.username %>!</p>
        
        <div class="dashboard-content">
            <h2>Your Account Information</h2>
            <p>Email: <%= user.email %></p>
            <p>Member since: <%= user.createdAt.toLocaleDateString() %></p>
        </div>
    </div>
</body>
</html>
```

Create views/profile.ejs:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Profile</title>
    <%- include('partials/styles') %>
</head>
<body>
    <%- include('partials/nav') %>
    
    <div class="container">
        <h1>Profile</h1>
        
        <div class="profile-info">
            <h2>User Information</h2>
            <p><strong>Username:</strong> <%= user.username %></p>
            <p><strong>Email:</strong> <%= user.email %></p>
            <p><strong>Member since:</strong> <%= user.createdAt.toLocaleDateString() %></p>
        </div>
    </div>
</body>
</html>
```

Create views/partials/nav.ejs:
```html
<nav>
    <div class="nav-brand">
        <a href="/">Auth Demo</a>
    </div>
    
    <div class="nav-links">
        <% if (locals.user) { %>
            <a href="/dashboard">Dashboard</a>
            <a href="/profile">Profile</a>
            <form action="/logout" method="POST" class="logout-form">
                <button type="submit">Logout</button>
            </form>
        <% } else { %>
            <a href="/login">Login</a>
            <a href="/register">Register</a>
        <% } %>
    </div>
</nav>
```

Create views/partials/styles.ejs:
```html
<style>
    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }

    body {
        font-family: Arial, sans-serif;
        line-height: 1.6;
        background: #f4f4f4;
    }

    .container {
        max-width: 800px;
        margin: 2rem auto;
        padding: 0 20px;
    }

    nav {
        background: #333;
        color: white;
        padding: 1rem;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }

    nav a {
        color: white;
        text-decoration: none;
        margin: 0 10px;
    }

    .nav-links {
        display: flex;
        align-items: center;
    }

    .auth-form {
        background: white;
        padding: 2rem;
        border-radius: 5px;
        box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        margin-top: 2rem;
    }

    .form-group {
        margin-bottom: 1rem;
    }

    .form-group label {
        display: block;
        margin-bottom: 0.5rem;
    }

    .form-group input {
        width: 100%;
        padding: 0.5rem;
        border: 1px solid #ddd;
        border-radius: 3px;
    }

    .btn {
        background: #3498db;
        color: white;
        padding: 0.7rem 1.5rem;
        border: none;
        border-radius: 3px;
        cursor: pointer;
        text-decoration: none;
    }

    .error {
        background: #ff6b6b;
        color: white;
        padding: 1rem;
        border-radius: 3px;
        margin-bottom: 1rem;
    }

    .logout-form {
        display: inline;
    }

    .logout-form button {
        background: none;
        border: none;
        color: white;
        cursor: pointer;
    }

    .dashboard-content,
    .profile-info {
        background: white;
        padding: 2rem;
        border-radius: 5px;
        box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        margin-top: 2rem;
    }
</style>
```

## 6. Expected Output

Start MongoDB and the server:
```bash
# Start MongoDB (if not running)
mongod

# Start the application
node index.js
```

Test the following features:

1. Registration:
- Visit http://localhost:3000/register
- Create a new account
- Verify automatic login after registration

2. Login:
- Visit http://localhost:3000/login
- Enter credentials
- Access dashboard after successful login

3. Protected Routes:
- Try accessing /dashboard without login
- Verify redirect to login page
- Access dashboard after authentication

4. Profile and Logout:
- View profile information
- Test logout functionality
- Verify session termination

## 7. Practice Exercises
1. Add password reset functionality
2. Implement email verification
3. Add OAuth authentication
4. Create user roles and permissions
5. Add account deletion feature

## 8. Verification and Validation
- [ ] Registration works correctly
- [ ] Login authentication works
- [ ] Protected routes are secure
- [ ] Session management works
- [ ] Password hashing is implemented

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| MongoDB connection fails | Check MongoDB service |
| Password hash error | Verify bcrypt implementation |
| Session not persisting | Check session configuration |
| Auth bypass attempts | Verify middleware order |

## 10. Additional Resources
- [Passport.js Documentation](http://www.passportjs.org/)
- [bcrypt Documentation](https://www.npmjs.com/package/bcryptjs)
- [Express Session](https://www.npmjs.com/package/express-session)
- [Authentication Best Practices](https://expressjs.com/en/advanced/best-practice-security.html)
