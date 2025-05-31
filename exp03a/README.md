# Experiment 3A: ExpressJS - Session Management

## 1. Experiment Information
- **Experiment Number**: 03A
- **Title**: Working with Cookies and Sessions
- **Duration**: 1 hour

## 2. Learning Objectives
- Understand cookies and sessions
- Implement cookie management
- Create session-based features
- Handle session storage
- Manage session lifecycle

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- Visual Studio Code
- Web browser

### Knowledge Requirements
- Basic JavaScript
- Express basics
- HTTP protocol understanding

## 4. Theory and Concepts
### Cookies and Sessions
- HTTP stateless nature
- Cookie types and attributes
- Session management
- Session storage options
- Security considerations

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create project directory
mkdir express-session-demo
cd express-session-demo

# Initialize project
npm init -y

# Install dependencies
npm install express express-session cookie-parser ejs

# Create directories
mkdir views
```

### 2. Create Server

Create index.js:
```javascript
const express = require('express');
const session = require('express-session');
const cookieParser = require('cookie-parser');
const app = express();
const port = 3000;

// Middleware
app.use(express.urlencoded({ extended: true }));
app.use(cookieParser());
app.use(session({
    secret: 'your-secret-key',
    resave: false,
    saveUninitialized: true,
    cookie: { 
        secure: false, // Set to true in production with HTTPS
        maxAge: 1000 * 60 * 60 // 1 hour
    }
}));

// Set view engine
app.set('view engine', 'ejs');

// Routes for cookie management
app.get('/set-cookie', (req, res) => {
    // Set a regular cookie
    res.cookie('user_preference', 'dark_theme', {
        maxAge: 1000 * 60 * 60 * 24 * 7, // 1 week
        httpOnly: true
    });
    
    // Set a signed cookie
    res.cookie('user_id', '12345', {
        signed: true
    });
    
    res.redirect('/cookies');
});

app.get('/clear-cookie', (req, res) => {
    res.clearCookie('user_preference');
    res.clearCookie('user_id');
    res.redirect('/cookies');
});

app.get('/cookies', (req, res) => {
    res.render('cookies', {
        cookies: req.cookies,
        signedCookies: req.signedCookies
    });
});

// Routes for session management
app.get('/', (req, res) => {
    res.render('index', {
        viewCount: req.session.viewCount || 0,
        lastVisit: req.session.lastVisit || 'First visit'
    });
});

app.post('/preferences', (req, res) => {
    const { theme, language } = req.body;
    
    // Store in session
    req.session.userPreferences = {
        theme,
        language
    };
    
    res.redirect('/preferences');
});

app.get('/preferences', (req, res) => {
    res.render('preferences', {
        preferences: req.session.userPreferences || {}
    });
});

// Session counter middleware
app.use((req, res, next) => {
    req.session.viewCount = (req.session.viewCount || 0) + 1;
    req.session.lastVisit = new Date().toLocaleString();
    next();
});

// Shopping cart example
app.get('/cart', (req, res) => {
    const cart = req.session.cart || [];
    res.render('cart', { cart });
});

app.post('/cart/add', (req, res) => {
    const { item, price } = req.body;
    req.session.cart = req.session.cart || [];
    req.session.cart.push({ item, price: parseFloat(price) });
    res.redirect('/cart');
});

app.post('/cart/clear', (req, res) => {
    req.session.cart = [];
    res.redirect('/cart');
});

// Start server
app.listen(port, () => {
    console.log(`Server running at http://localhost:${port}`);
});
```

### 3. Create Views

Create views/index.ejs:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Session Demo</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
        }
        nav {
            margin-bottom: 20px;
        }
        nav a {
            margin-right: 10px;
        }
        .info-box {
            background: #f4f4f4;
            padding: 20px;
            border-radius: 5px;
            margin-bottom: 20px;
        }
    </style>
</head>
<body>
    <nav>
        <a href="/">Home</a>
        <a href="/cookies">Cookies</a>
        <a href="/preferences">Preferences</a>
        <a href="/cart">Cart</a>
    </nav>

    <h1>Session Management Demo</h1>
    
    <div class="info-box">
        <h2>Session Information</h2>
        <p>Page Views: <%= viewCount %></p>
        <p>Last Visit: <%= lastVisit %></p>
    </div>
</body>
</html>
```

Create views/cookies.ejs:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cookie Management</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
        }
        .cookie-box {
            background: #f4f4f4;
            padding: 20px;
            border-radius: 5px;
            margin-bottom: 20px;
        }
        .actions {
            margin: 20px 0;
        }
    </style>
</head>
<body>
    <h1>Cookie Management</h1>

    <div class="actions">
        <a href="/set-cookie">Set Cookies</a> |
        <a href="/clear-cookie">Clear Cookies</a> |
        <a href="/">Home</a>
    </div>

    <div class="cookie-box">
        <h2>Regular Cookies</h2>
        <pre><%= JSON.stringify(cookies, null, 2) %></pre>
    </div>

    <div class="cookie-box">
        <h2>Signed Cookies</h2>
        <pre><%= JSON.stringify(signedCookies, null, 2) %></pre>
    </div>
</body>
</html>
```

Create views/preferences.ejs:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>User Preferences</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
        }
        form {
            background: #f4f4f4;
            padding: 20px;
            border-radius: 5px;
        }
        .form-group {
            margin-bottom: 15px;
        }
        .current-prefs {
            margin-top: 20px;
            padding: 20px;
            background: #e9ecef;
            border-radius: 5px;
        }
    </style>
</head>
<body>
    <h1>User Preferences</h1>
    
    <form action="/preferences" method="POST">
        <div class="form-group">
            <label for="theme">Theme:</label>
            <select name="theme" id="theme">
                <option value="light" <%= preferences.theme === 'light' ? 'selected' : '' %>>Light</option>
                <option value="dark" <%= preferences.theme === 'dark' ? 'selected' : '' %>>Dark</option>
            </select>
        </div>

        <div class="form-group">
            <label for="language">Language:</label>
            <select name="language" id="language">
                <option value="en" <%= preferences.language === 'en' ? 'selected' : '' %>>English</option>
                <option value="es" <%= preferences.language === 'es' ? 'selected' : '' %>>Spanish</option>
                <option value="fr" <%= preferences.language === 'fr' ? 'selected' : '' %>>French</option>
            </select>
        </div>

        <button type="submit">Save Preferences</button>
    </form>

    <div class="current-prefs">
        <h2>Current Preferences</h2>
        <pre><%= JSON.stringify(preferences, null, 2) %></pre>
    </div>

    <p><a href="/">Back to Home</a></p>
</body>
</html>
```

Create views/cart.ejs:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Shopping Cart</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
        }
        .cart-item {
            background: #f4f4f4;
            padding: 10px;
            margin-bottom: 10px;
            border-radius: 5px;
        }
        .total {
            font-weight: bold;
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <h1>Shopping Cart</h1>

    <form action="/cart/add" method="POST">
        <div>
            <label for="item">Item:</label>
            <input type="text" name="item" required>
        </div>
        <div>
            <label for="price">Price:</label>
            <input type="number" name="price" step="0.01" required>
        </div>
        <button type="submit">Add to Cart</button>
    </form>

    <h2>Cart Items</h2>
    <% if (cart.length === 0) { %>
        <p>Cart is empty</p>
    <% } else { %>
        <% cart.forEach(item => { %>
            <div class="cart-item">
                <p><%= item.item %> - $<%= item.price.toFixed(2) %></p>
            </div>
        <% }); %>
        
        <div class="total">
            Total: $<%= cart.reduce((sum, item) => sum + item.price, 0).toFixed(2) %>
        </div>

        <form action="/cart/clear" method="POST">
            <button type="submit">Clear Cart</button>
        </form>
    <% } %>

    <p><a href="/">Back to Home</a></p>
</body>
</html>
```

## 6. Expected Output

Start the server:
```bash
node index.js
```

Test the following features:

1. Session Counter:
- Visit http://localhost:3000 multiple times
- View count should increment
- Last visit time should update

2. Cookie Management:
- Visit http://localhost:3000/cookies
- Click "Set Cookies" to create cookies
- View both regular and signed cookies
- Click "Clear Cookies" to remove them

3. User Preferences:
- Visit http://localhost:3000/preferences
- Set theme and language
- Preferences persist across page reloads
- View stored preferences in JSON format

4. Shopping Cart:
- Visit http://localhost:3000/cart
- Add items with prices
- View cart total
- Clear cart functionality

## 7. Practice Exercises
1. Add session timeout handling
2. Implement remember me functionality
3. Create a session-based theme switcher
4. Add session persistence to database
5. Implement cart item quantity management

## 8. Verification and Validation
- [ ] Session counter works
- [ ] Cookies are properly set and cleared
- [ ] Preferences are saved in session
- [ ] Shopping cart maintains state
- [ ] Navigation works between pages

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| Session not persisting | Check session configuration |
| Cookies not setting | Verify cookie options |
| Session data lost | Check session store setup |
| Memory leaks | Implement proper session cleanup |

## 10. Additional Resources
- [Express Session](https://www.npmjs.com/package/express-session)
- [Cookie Parser](https://www.npmjs.com/package/cookie-parser)
- [Session Security Best Practices](https://expressjs.com/en/advanced/best-practice-security.html)
- [Cookie Security](https://expressjs.com/en/advanced/best-practice-security.html#cookies)
