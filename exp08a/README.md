# Experiment 8A: ReactJS - Routing with React Router

## 1. Experiment Information
- **Experiment Number**: 08A
- **Title**: Page Routing using React Router
- **Duration**: 45 minutes

## 2. Learning Objectives
- Understand React Router basics
- Implement route navigation
- Create nested routes
- Handle route parameters
- Protect routes
- Use route hooks

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- Visual Studio Code
- Web browser

### Knowledge Requirements
- React basics
- JavaScript ES6
- Previous experiments completion

## 4. Theory and Concepts
### React Router
- BrowserRouter
- Routes and Route
- Link and NavLink
- Route parameters
- Nested routes
- Protected routes

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create new React project
npx create-react-app router-demo
cd router-demo

# Install React Router
npm install react-router-dom

# Start development server
npm start
```

### 2. Create Components

Create src/components/Navigation.js:
```javascript
import React from 'react';
import { NavLink } from 'react-router-dom';

function Navigation() {
  return (
    <nav className="navigation">
      <ul>
        <li>
          <NavLink 
            to="/"
            className={({ isActive }) => isActive ? 'active' : ''}
          >
            Home
          </NavLink>
        </li>
        <li>
          <NavLink 
            to="/products"
            className={({ isActive }) => isActive ? 'active' : ''}
          >
            Products
          </NavLink>
        </li>
        <li>
          <NavLink 
            to="/about"
            className={({ isActive }) => isActive ? 'active' : ''}
          >
            About
          </NavLink>
        </li>
        <li>
          <NavLink 
            to="/dashboard"
            className={({ isActive }) => isActive ? 'active' : ''}
          >
            Dashboard
          </NavLink>
        </li>
      </ul>
    </nav>
  );
}

export default Navigation;
```

Create src/components/ProtectedRoute.js:
```javascript
import React from 'react';
import { Navigate, useLocation } from 'react-router-dom';

function ProtectedRoute({ children, isAuthenticated }) {
  const location = useLocation();

  if (!isAuthenticated) {
    return <Navigate to="/login" state={{ from: location }} replace />;
  }

  return children;
}

export default ProtectedRoute;
```

Create src/pages/Home.js:
```javascript
import React from 'react';

function Home() {
  return (
    <div className="page home">
      <h1>Welcome to Our Store</h1>
      <p>Discover amazing products and great deals!</p>
      <div className="featured">
        <h2>Featured Items</h2>
        <div className="featured-grid">
          <div className="featured-item">
            <h3>New Arrivals</h3>
            <p>Check out our latest products</p>
          </div>
          <div className="featured-item">
            <h3>Best Sellers</h3>
            <p>See what's popular</p>
          </div>
          <div className="featured-item">
            <h3>Special Offers</h3>
            <p>Limited time deals</p>
          </div>
        </div>
      </div>
    </div>
  );
}

export default Home;
```

Create src/pages/Products.js:
```javascript
import React from 'react';
import { Link, Routes, Route, useParams } from 'react-router-dom';

// Product List Component
function ProductList() {
  const products = [
    { id: 1, name: 'Laptop', price: 999.99 },
    { id: 2, name: 'Smartphone', price: 699.99 },
    { id: 3, name: 'Headphones', price: 199.99 },
    { id: 4, name: 'Tablet', price: 499.99 }
  ];

  return (
    <div className="products-list">
      <h2>Our Products</h2>
      <div className="products-grid">
        {products.map(product => (
          <div key={product.id} className="product-card">
            <h3>{product.name}</h3>
            <p>${product.price}</p>
            <Link to={`/products/${product.id}`}>View Details</Link>
          </div>
        ))}
      </div>
    </div>
  );
}

// Product Detail Component
function ProductDetail() {
  const { productId } = useParams();
  const products = {
    1: {
      name: 'Laptop',
      price: 999.99,
      description: 'High-performance laptop with latest features'
    },
    2: {
      name: 'Smartphone',
      price: 699.99,
      description: 'Latest smartphone with advanced camera'
    },
    3: {
      name: 'Headphones',
      price: 199.99,
      description: 'Wireless headphones with noise cancellation'
    },
    4: {
      name: 'Tablet',
      price: 499.99,
      description: 'Versatile tablet for work and entertainment'
    }
  };

  const product = products[productId];

  if (!product) {
    return <div>Product not found</div>;
  }

  return (
    <div className="product-detail">
      <h2>{product.name}</h2>
      <p className="price">${product.price}</p>
      <p className="description">{product.description}</p>
      <Link to="/products" className="back-link">
        Back to Products
      </Link>
    </div>
  );
}

// Main Products Component
function Products() {
  return (
    <div className="page products">
      <Routes>
        <Route index element={<ProductList />} />
        <Route path=":productId" element={<ProductDetail />} />
      </Routes>
    </div>
  );
}

export default Products;
```

Create src/pages/About.js:
```javascript
import React from 'react';

function About() {
  return (
    <div className="page about">
      <h1>About Us</h1>
      <div className="about-content">
        <section className="company-info">
          <h2>Our Story</h2>
          <p>We are dedicated to providing the best products and services.</p>
        </section>
        <section className="team">
          <h2>Our Team</h2>
          <div className="team-members">
            <div className="member">
              <h3>John Doe</h3>
              <p>CEO</p>
            </div>
            <div className="member">
              <h3>Jane Smith</h3>
              <p>CTO</p>
            </div>
          </div>
        </section>
        <section className="contact">
          <h2>Contact Us</h2>
          <p>Email: info@example.com</p>
          <p>Phone: (555) 123-4567</p>
        </section>
      </div>
    </div>
  );
}

export default About;
```

Create src/pages/Dashboard.js:
```javascript
import React from 'react';
import { useNavigate } from 'react-router-dom';

function Dashboard() {
  const navigate = useNavigate();

  const handleLogout = () => {
    // Simulate logout
    navigate('/login');
  };

  return (
    <div className="page dashboard">
      <h1>Dashboard</h1>
      <div className="dashboard-content">
        <div className="dashboard-stats">
          <div className="stat-card">
            <h3>Orders</h3>
            <p>15</p>
          </div>
          <div className="stat-card">
            <h3>Products</h3>
            <p>45</p>
          </div>
          <div className="stat-card">
            <h3>Revenue</h3>
            <p>$2,500</p>
          </div>
        </div>
        <button onClick={handleLogout} className="logout-btn">
          Logout
        </button>
      </div>
    </div>
  );
}

export default Dashboard;
```

Create src/pages/Login.js:
```javascript
import React, { useState } from 'react';
import { useNavigate, useLocation } from 'react-router-dom';

function Login({ onLogin }) {
  const [credentials, setCredentials] = useState({
    username: '',
    password: ''
  });
  const navigate = useNavigate();
  const location = useLocation();

  const from = location.state?.from?.pathname || '/dashboard';

  const handleSubmit = (e) => {
    e.preventDefault();
    // Simulate authentication
    onLogin();
    navigate(from, { replace: true });
  };

  return (
    <div className="page login">
      <h1>Login</h1>
      <form onSubmit={handleSubmit} className="login-form">
        <div className="form-group">
          <label htmlFor="username">Username:</label>
          <input
            type="text"
            id="username"
            value={credentials.username}
            onChange={(e) => setCredentials(prev => ({
              ...prev,
              username: e.target.value
            }))}
          />
        </div>
        <div className="form-group">
          <label htmlFor="password">Password:</label>
          <input
            type="password"
            id="password"
            value={credentials.password}
            onChange={(e) => setCredentials(prev => ({
              ...prev,
              password: e.target.value
            }))}
          />
        </div>
        <button type="submit">Login</button>
      </form>
    </div>
  );
}

export default Login;
```

Create src/pages/NotFound.js:
```javascript
import React from 'react';
import { Link } from 'react-router-dom';

function NotFound() {
  return (
    <div className="page not-found">
      <h1>404 - Page Not Found</h1>
      <p>The page you are looking for does not exist.</p>
      <Link to="/" className="home-link">
        Go to Home
      </Link>
    </div>
  );
}

export default NotFound;
```

### 3. Update App Component

Replace src/App.js:
```javascript
import React, { useState } from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import './App.css';

import Navigation from './components/Navigation';
import ProtectedRoute from './components/ProtectedRoute';
import Home from './pages/Home';
import Products from './pages/Products';
import About from './pages/About';
import Dashboard from './pages/Dashboard';
import Login from './pages/Login';
import NotFound from './pages/NotFound';

function App() {
  const [isAuthenticated, setIsAuthenticated] = useState(false);

  const handleLogin = () => {
    setIsAuthenticated(true);
  };

  return (
    <BrowserRouter>
      <div className="App">
        <Navigation />
        
        <main>
          <Routes>
            <Route path="/" element={<Home />} />
            <Route path="/products/*" element={<Products />} />
            <Route path="/about" element={<About />} />
            <Route
              path="/dashboard"
              element={
                <ProtectedRoute isAuthenticated={isAuthenticated}>
                  <Dashboard />
                </ProtectedRoute>
              }
            />
            <Route
              path="/login"
              element={<Login onLogin={handleLogin} />}
            />
            <Route path="*" element={<NotFound />} />
          </Routes>
        </main>
      </div>
    </BrowserRouter>
  );
}

export default App;
```

### 4. Add Styling

Update src/App.css:
```css
.App {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

/* Navigation Styles */
.navigation {
  background-color: #f8f9fa;
  padding: 1rem;
  margin-bottom: 2rem;
  border-radius: 5px;
}

.navigation ul {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  gap: 1rem;
}

.navigation a {
  text-decoration: none;
  color: #333;
  padding: 0.5rem 1rem;
  border-radius: 3px;
}

.navigation a:hover {
  background-color: #e9ecef;
}

.navigation a.active {
  background-color: #0066cc;
  color: white;
}

/* Page Styles */
.page {
  padding: 2rem;
  background-color: white;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

/* Home Page */
.featured-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1rem;
  margin-top: 2rem;
}

.featured-item {
  padding: 1.5rem;
  background-color: #f8f9fa;
  border-radius: 5px;
}

/* Products Page */
.products-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 1.5rem;
  margin-top: 2rem;
}

.product-card {
  padding: 1rem;
  background-color: #f8f9fa;
  border-radius: 5px;
  text-align: center;
}

.product-card a {
  display: inline-block;
  margin-top: 1rem;
  padding: 0.5rem 1rem;
  background-color: #0066cc;
  color: white;
  text-decoration: none;
  border-radius: 3px;
}

.product-detail {
  max-width: 600px;
  margin: 0 auto;
}

.back-link {
  display: inline-block;
  margin-top: 1rem;
  color: #0066cc;
  text-decoration: none;
}

/* About Page */
.about-content {
  max-width: 800px;
  margin: 0 auto;
}

.team-members {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 1.5rem;
  margin-top: 1.5rem;
}

.member {
  padding: 1rem;
  background-color: #f8f9fa;
  border-radius: 5px;
  text-align: center;
}

/* Dashboard Page */
.dashboard-stats {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  gap: 1.5rem;
  margin-bottom: 2rem;
}

.stat-card {
  padding: 1.5rem;
  background-color: #f8f9fa;
  border-radius: 5px;
  text-align: center;
}

.logout-btn {
  padding: 0.5rem 1rem;
  background-color: #dc3545;
  color: white;
  border: none;
  border-radius: 3px;
  cursor: pointer;
}

/* Login Page */
.login-form {
  max-width: 400px;
  margin: 0 auto;
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

/* Not Found Page */
.not-found {
  text-align: center;
}

.home-link {
  display: inline-block;
  margin-top: 1rem;
  padding: 0.5rem 1rem;
  background-color: #0066cc;
  color: white;
  text-decoration: none;
  border-radius: 3px;
}

/* Common Styles */
h1 {
  margin-bottom: 1.5rem;
}

h2 {
  margin: 1.5rem 0 1rem;
}

button {
  padding: 0.5rem 1rem;
  background-color: #0066cc;
  color: white;
  border: none;
  border-radius: 3px;
  cursor: pointer;
}

button:hover {
  background-color: #0052a3;
}
```

## 6. Expected Output

Start the development server:
```bash
npm start
```

Visit http://localhost:3000 to see:

1. Navigation:
   - Home
   - Products
   - About
   - Dashboard

2. Features:
   - Route navigation
   - Nested routes
   - Protected routes
   - Route parameters
   - 404 handling

3. Pages:
   - Home page
   - Products list and detail
   - About page
   - Protected dashboard
   - Login page
   - Not found page

## 7. Practice Exercises
1. Add more nested routes
2. Implement route transitions
3. Add query parameters
4. Create breadcrumb navigation
5. Add route guards

## 8. Verification and Validation
- [ ] All routes work
- [ ] Navigation works
- [ ] Protected routes work
- [ ] Nested routes work
- [ ] 404 handling works

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| Routes not working | Check router setup |
| Protected route fails | Verify auth state |
| Nested routes not showing | Check route paths |
| Navigation not updating | Check component updates |

## 10. Additional Resources
- [React Router Documentation](https://reactrouter.com/)
- [Protected Routes](https://reactrouter.com/docs/en/v6/examples/auth)
- [Route Parameters](https://reactrouter.com/docs/en/v6/getting-started/concepts#dynamic-segments)
- [Navigation Guides](https://reactrouter.com/docs/en/v6/getting-started/overview)
