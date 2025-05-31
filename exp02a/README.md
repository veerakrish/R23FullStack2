# Experiment 2A: ExpressJS - Templating with EJS

## 1. Experiment Information
- **Experiment Number**: 02A
- **Title**: Working with EJS Templating Engine
- **Duration**: 1 hour

## 2. Learning Objectives
- Understand template engines in Express
- Implement EJS templating
- Create dynamic web pages
- Work with partials and layouts
- Pass data to templates

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- Visual Studio Code
- Web browser

### Knowledge Requirements
- Basic JavaScript
- HTML/CSS
- Express basics

## 4. Theory and Concepts
### Template Engines
- Server-side rendering
- Dynamic content generation
- Template syntax
- Layouts and partials
- Data passing

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create project directory
mkdir express-template-demo
cd express-template-demo

# Initialize project
npm init -y

# Install dependencies
npm install express ejs

# Create directories
mkdir views
mkdir views/partials
mkdir public
mkdir public/css
```

### 2. Create Basic Server

Create index.js:
```javascript
const express = require('express');
const app = express();
const port = 3000;

// Set EJS as templating engine
app.set('view engine', 'ejs');

// Serve static files
app.use(express.static('public'));

// Sample data
const products = [
    { id: 1, name: 'Laptop', price: 999.99 },
    { id: 2, name: 'Smartphone', price: 499.99 },
    { id: 3, name: 'Headphones', price: 99.99 }
];

// Routes
app.get('/', (req, res) => {
    res.render('home', {
        title: 'Home',
        message: 'Welcome to our store!'
    });
});

app.get('/products', (req, res) => {
    res.render('products', {
        title: 'Products',
        products: products
    });
});

app.get('/about', (req, res) => {
    res.render('about', {
        title: 'About Us',
        company: 'Tech Store',
        established: 2025
    });
});

// Start server
app.listen(port, () => {
    console.log(`Server running at http://localhost:${port}`);
});
```

### 3. Create Views

Create views/partials/header.ejs:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title><%= title %></title>
    <link rel="stylesheet" href="/css/style.css">
</head>
<body>
    <nav>
        <ul>
            <li><a href="/">Home</a></li>
            <li><a href="/products">Products</a></li>
            <li><a href="/about">About</a></li>
        </ul>
    </nav>
```

Create views/partials/footer.ejs:
```html
    <footer>
        <p>&copy; 2025 Tech Store. All rights reserved.</p>
    </footer>
</body>
</html>
```

Create views/home.ejs:
```html
<%- include('partials/header') %>

<div class="container">
    <h1><%= title %></h1>
    <p><%= message %></p>
    
    <div class="welcome-section">
        <h2>Featured Products</h2>
        <p>Check out our latest collection!</p>
        <a href="/products" class="btn">View Products</a>
    </div>
</div>

<%- include('partials/footer') %>
```

Create views/products.ejs:
```html
<%- include('partials/header') %>

<div class="container">
    <h1><%= title %></h1>
    
    <div class="products-grid">
        <% products.forEach(product => { %>
            <div class="product-card">
                <h3><%= product.name %></h3>
                <p class="price">$<%= product.price.toFixed(2) %></p>
                <button class="btn">Add to Cart</button>
            </div>
        <% }); %>
    </div>
</div>

<%- include('partials/footer') %>
```

Create views/about.ejs:
```html
<%- include('partials/header') %>

<div class="container">
    <h1><%= title %></h1>
    
    <div class="about-content">
        <h2><%= company %></h2>
        <p>Established in <%= established %></p>
        <p>We are committed to providing the best technology products to our customers.</p>
    </div>
</div>

<%- include('partials/footer') %>
```

### 4. Add Styling

Create public/css/style.css:
```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: Arial, sans-serif;
    line-height: 1.6;
}

.container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 20px;
}

nav {
    background: #333;
    padding: 1rem;
}

nav ul {
    list-style: none;
    display: flex;
    justify-content: center;
}

nav ul li {
    margin: 0 15px;
}

nav ul li a {
    color: white;
    text-decoration: none;
}

.welcome-section {
    text-align: center;
    margin: 2rem 0;
}

.products-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 20px;
    margin-top: 2rem;
}

.product-card {
    border: 1px solid #ddd;
    padding: 1rem;
    text-align: center;
    border-radius: 5px;
}

.price {
    color: #2ecc71;
    font-size: 1.2rem;
    margin: 1rem 0;
}

.btn {
    display: inline-block;
    padding: 0.5rem 1rem;
    background: #3498db;
    color: white;
    text-decoration: none;
    border: none;
    border-radius: 3px;
    cursor: pointer;
}

.about-content {
    text-align: center;
    margin: 2rem 0;
}

footer {
    text-align: center;
    padding: 1rem;
    background: #333;
    color: white;
    position: fixed;
    bottom: 0;
    width: 100%;
}
```

## 6. Expected Output

Start the server:
```bash
node index.js
```

Visit the following URLs in your browser:

1. Home page (http://localhost:3000/):
- Shows welcome message
- Displays featured products section
- Navigation menu visible

2. Products page (http://localhost:3000/products):
- Displays grid of products
- Shows product name and price
- Add to Cart button for each product

3. About page (http://localhost:3000/about):
- Shows company information
- Displays establishment year
- Consistent layout with other pages

## 7. Practice Exercises
1. Add a contact page with dynamic content
2. Create a product details page
3. Implement conditional rendering in templates
4. Add more partial templates
5. Create a custom 404 page

## 8. Verification and Validation
- [ ] All pages render correctly
- [ ] Dynamic data displays properly
- [ ] Partials are included correctly
- [ ] Styling is applied
- [ ] Navigation works

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| Template not found | Check file path and extension |
| Data not displaying | Verify variable passing to template |
| Partial not loading | Check include path |
| CSS not applying | Verify static middleware setup |

## 10. Additional Resources
- [EJS Documentation](https://ejs.co/)
- [Express Template Engines](https://expressjs.com/en/guide/using-template-engines.html)
- [EJS Partials](https://www.npmjs.com/package/ejs#includes)
- [Express Static Files](https://expressjs.com/en/starter/static-files.html)
