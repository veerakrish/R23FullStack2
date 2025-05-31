# Experiment 5B: ReactJS - Writing Markup with JSX

## 1. Experiment Information
- **Experiment Number**: 05B
- **Title**: Writing Markup with JSX
- **Duration**: 45 minutes

## 2. Learning Objectives
- Understand JSX syntax
- Learn JSX expressions
- Work with conditional rendering
- Use JSX with loops
- Handle JSX attributes

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- Visual Studio Code
- Web browser

### Knowledge Requirements
- HTML and CSS
- JavaScript ES6+
- React basics
- Experiment 5A completion

## 4. Theory and Concepts
### JSX
- JavaScript XML
- Expression syntax
- Conditional rendering
- List rendering
- Props and attributes
- JSX compilation

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create new React project
npx create-react-app jsx-demo
cd jsx-demo

# Start development server
npm start
```

### 2. JSX Examples

Replace src/App.js:
```javascript
import React from 'react';
import './App.css';

function App() {
  // Sample data for demonstrations
  const user = {
    name: 'John Doe',
    age: 25,
    isAdmin: true,
    hobbies: ['reading', 'gaming', 'coding']
  };

  const products = [
    { id: 1, name: 'Laptop', price: 999.99, inStock: true },
    { id: 2, name: 'Phone', price: 699.99, inStock: false },
    { id: 3, name: 'Tablet', price: 499.99, inStock: true }
  ];

  // Helper function for price formatting
  const formatPrice = (price) => {
    return new Intl.NumberFormat('en-US', {
      style: 'currency',
      currency: 'USD'
    }).format(price);
  };

  return (
    <div className="App">
      <header>
        <h1>JSX Examples</h1>
      </header>

      <main>
        {/* Basic Expressions */}
        <section>
          <h2>Basic JSX Expressions</h2>
          <p>Hello, {user.name}!</p>
          <p>You are {user.age} years old.</p>
          <p>2 + 2 = {2 + 2}</p>
          <p>Current time: {new Date().toLocaleTimeString()}</p>
        </section>

        {/* Conditional Rendering */}
        <section>
          <h2>Conditional Rendering</h2>
          
          {/* Using ternary operator */}
          <p>
            Status: {user.isAdmin ? 'Administrator' : 'Regular User'}
          </p>

          {/* Using logical && */}
          {user.isAdmin && (
            <p className="admin-message">
              Welcome, Admin! You have full access.
            </p>
          )}

          {/* Using if-else with immediate function */}
          <p>
            {(() => {
              if (user.age < 18) return 'Minor';
              if (user.age < 65) return 'Adult';
              return 'Senior';
            })()}
          </p>
        </section>

        {/* List Rendering */}
        <section>
          <h2>List Rendering</h2>
          
          {/* Simple array mapping */}
          <h3>Hobbies:</h3>
          <ul>
            {user.hobbies.map((hobby, index) => (
              <li key={index}>{hobby}</li>
            ))}
          </ul>

          {/* Complex object mapping */}
          <h3>Products:</h3>
          <div className="product-grid">
            {products.map(product => (
              <div key={product.id} className="product-card">
                <h4>{product.name}</h4>
                <p>Price: {formatPrice(product.price)}</p>
                <p className={product.inStock ? 'in-stock' : 'out-of-stock'}>
                  {product.inStock ? 'In Stock' : 'Out of Stock'}
                </p>
              </div>
            ))}
          </div>
        </section>

        {/* JSX Attributes */}
        <section>
          <h2>JSX Attributes</h2>
          
          {/* Class and Style attributes */}
          <div 
            className="custom-div"
            style={{
              backgroundColor: '#f0f0f0',
              padding: '1rem',
              borderRadius: '5px'
            }}
          >
            <p>Custom styled div</p>
          </div>

          {/* Dynamic attributes */}
          <button
            disabled={!user.isAdmin}
            onClick={() => alert('Button clicked!')}
            className={user.isAdmin ? 'admin-button' : 'user-button'}
          >
            {user.isAdmin ? 'Admin Action' : 'Regular Action'}
          </button>
        </section>

        {/* JSX Fragments */}
        <section>
          <h2>JSX Fragments</h2>
          <>
            <p>This is inside a fragment</p>
            <p>No extra div needed!</p>
          </>
        </section>

        {/* JSX Comments and Spacing */}
        <section>
          <h2>Comments and Spacing</h2>
          {/* This is a JSX comment */}
          <div>
            This is{' '}
            <strong>spaced</strong>{' '}
            text
          </div>
        </section>
      </main>
    </div>
  );
}

export default App;
```

### 3. Add Styling

Update src/App.css:
```css
.App {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

header {
  text-align: center;
  margin-bottom: 2rem;
}

section {
  margin-bottom: 2rem;
  padding: 1rem;
  border: 1px solid #ddd;
  border-radius: 5px;
}

h2 {
  color: #333;
  margin-bottom: 1rem;
}

/* Product Grid Styles */
.product-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 1rem;
  margin-top: 1rem;
}

.product-card {
  padding: 1rem;
  border: 1px solid #ddd;
  border-radius: 5px;
  background-color: white;
}

.in-stock {
  color: green;
}

.out-of-stock {
  color: red;
}

/* Button Styles */
button {
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 3px;
  cursor: pointer;
  margin: 0.5rem 0;
}

button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.admin-button {
  background-color: #0066cc;
  color: white;
}

.user-button {
  background-color: #666;
  color: white;
}

/* Admin Message */
.admin-message {
  background-color: #e6f3ff;
  padding: 0.5rem;
  border-radius: 3px;
  color: #0066cc;
}

/* Custom Div */
.custom-div {
  margin: 1rem 0;
}
```

## 6. Expected Output

Start the development server:
```bash
npm start
```

Visit http://localhost:3000 to see:

1. Basic JSX expressions
2. Conditional rendering examples
3. List rendering with arrays and objects
4. Dynamic attributes and styling
5. JSX fragments usage
6. Comments and spacing examples

## 7. Practice Exercises
1. Create a dynamic form using JSX
2. Implement a toggle component
3. Create a filtered list
4. Build a tabbed interface
5. Create a dynamic table

## 8. Verification and Validation
- [ ] All expressions evaluate correctly
- [ ] Conditional rendering works
- [ ] Lists render properly
- [ ] Styling is applied correctly
- [ ] Events work as expected

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| JSX syntax errors | Check closing tags |
| Expression issues | Verify {} usage |
| Key warnings | Add unique keys |
| Style problems | Check style object syntax |

## 10. Additional Resources
- [JSX in Depth](https://reactjs.org/docs/jsx-in-depth.html)
- [Lists and Keys](https://reactjs.org/docs/lists-and-keys.html)
- [Conditional Rendering](https://reactjs.org/docs/conditional-rendering.html)
- [JSX Gotchas](https://reactjs.org/docs/jsx-in-depth.html#user-defined-components-must-be-capitalized)
