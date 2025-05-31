# Experiment 6A: ReactJS - Props and States

## 1. Experiment Information
- **Experiment Number**: 06A
- **Title**: Working with Props and States
- **Duration**: 45 minutes

## 2. Learning Objectives
- Understand Props vs States
- Pass data between components
- Manage component state
- Use state hooks
- Implement prop drilling
- Handle state updates

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- Visual Studio Code
- Web browser

### Knowledge Requirements
- JavaScript ES6+
- React basics
- Component concepts
- Previous experiments completion

## 4. Theory and Concepts
### Props and State
- Props: Immutable data passed to components
- State: Mutable data managed within components
- State updates and re-rendering
- Props vs State usage patterns
- State management patterns

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create new React project
npx create-react-app props-state-demo
cd props-state-demo

# Start development server
npm start
```

### 2. Create Components

Create src/components/ShoppingCart.js:
```javascript
import React, { useState } from 'react';
import ProductList from './ProductList';
import CartSummary from './CartSummary';

function ShoppingCart() {
  const [products] = useState([
    { id: 1, name: 'Laptop', price: 999.99, stock: 5 },
    { id: 2, name: 'Smartphone', price: 499.99, stock: 8 },
    { id: 3, name: 'Headphones', price: 99.99, stock: 12 },
    { id: 4, name: 'Tablet', price: 299.99, stock: 3 }
  ]);

  const [cart, setCart] = useState([]);

  const addToCart = (product) => {
    setCart(prevCart => {
      const existingItem = prevCart.find(item => item.id === product.id);
      
      if (existingItem) {
        return prevCart.map(item =>
          item.id === product.id
            ? { ...item, quantity: item.quantity + 1 }
            : item
        );
      }

      return [...prevCart, { ...product, quantity: 1 }];
    });
  };

  const removeFromCart = (productId) => {
    setCart(prevCart => prevCart.filter(item => item.id !== productId));
  };

  const updateQuantity = (productId, newQuantity) => {
    if (newQuantity < 1) return;

    const product = products.find(p => p.id === productId);
    if (newQuantity > product.stock) return;

    setCart(prevCart =>
      prevCart.map(item =>
        item.id === productId
          ? { ...item, quantity: newQuantity }
          : item
      )
    );
  };

  return (
    <div className="shopping-cart">
      <h2>Shopping Cart Demo</h2>
      <div className="cart-container">
        <ProductList
          products={products}
          cart={cart}
          onAddToCart={addToCart}
        />
        <CartSummary
          cart={cart}
          onUpdateQuantity={updateQuantity}
          onRemoveItem={removeFromCart}
        />
      </div>
    </div>
  );
}

export default ShoppingCart;
```

Create src/components/ProductList.js:
```javascript
import React from 'react';
import ProductItem from './ProductItem';

function ProductList({ products, cart, onAddToCart }) {
  return (
    <div className="product-list">
      <h3>Available Products</h3>
      <div className="products">
        {products.map(product => (
          <ProductItem
            key={product.id}
            product={product}
            cartItem={cart.find(item => item.id === product.id)}
            onAddToCart={onAddToCart}
          />
        ))}
      </div>
    </div>
  );
}

export default ProductList;
```

Create src/components/ProductItem.js:
```javascript
import React from 'react';

function ProductItem({ product, cartItem, onAddToCart }) {
  const inStock = product.stock > 0;
  const quantityInCart = cartItem?.quantity || 0;
  const remainingStock = product.stock - quantityInCart;

  return (
    <div className="product-item">
      <h4>{product.name}</h4>
      <p className="price">${product.price.toFixed(2)}</p>
      <p className="stock">
        In stock: {remainingStock}
      </p>
      <button
        onClick={() => onAddToCart(product)}
        disabled={!inStock || remainingStock === 0}
      >
        Add to Cart
      </button>
      {quantityInCart > 0 && (
        <p className="in-cart">
          In cart: {quantityInCart}
        </p>
      )}
    </div>
  );
}

export default ProductItem;
```

Create src/components/CartSummary.js:
```javascript
import React from 'react';
import CartItem from './CartItem';

function CartSummary({ cart, onUpdateQuantity, onRemoveItem }) {
  const total = cart.reduce((sum, item) => 
    sum + (item.price * item.quantity), 0
  );

  return (
    <div className="cart-summary">
      <h3>Shopping Cart</h3>
      {cart.length === 0 ? (
        <p>Your cart is empty</p>
      ) : (
        <>
          <div className="cart-items">
            {cart.map(item => (
              <CartItem
                key={item.id}
                item={item}
                onUpdateQuantity={onUpdateQuantity}
                onRemoveItem={onRemoveItem}
              />
            ))}
          </div>
          <div className="cart-total">
            <h4>Total: ${total.toFixed(2)}</h4>
          </div>
        </>
      )}
    </div>
  );
}

export default CartSummary;
```

Create src/components/CartItem.js:
```javascript
import React from 'react';

function CartItem({ item, onUpdateQuantity, onRemoveItem }) {
  return (
    <div className="cart-item">
      <div className="item-info">
        <h4>{item.name}</h4>
        <p>${item.price.toFixed(2)} each</p>
      </div>
      <div className="item-controls">
        <button
          onClick={() => onUpdateQuantity(item.id, item.quantity - 1)}
          className="quantity-btn"
        >
          -
        </button>
        <span className="quantity">{item.quantity}</span>
        <button
          onClick={() => onUpdateQuantity(item.id, item.quantity + 1)}
          className="quantity-btn"
        >
          +
        </button>
        <button
          onClick={() => onRemoveItem(item.id)}
          className="remove-btn"
        >
          Remove
        </button>
      </div>
      <div className="item-total">
        ${(item.price * item.quantity).toFixed(2)}
      </div>
    </div>
  );
}

export default CartItem;
```

### 3. Update App Component

Replace src/App.js:
```javascript
import React from 'react';
import './App.css';
import ShoppingCart from './components/ShoppingCart';

function App() {
  return (
    <div className="App">
      <header>
        <h1>Props and State Demo</h1>
        <p>Shopping Cart Implementation</p>
      </header>
      <main>
        <ShoppingCart />
      </main>
    </div>
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

header {
  text-align: center;
  margin-bottom: 2rem;
  padding: 1rem;
  background-color: #f0f0f0;
  border-radius: 5px;
}

.cart-container {
  display: grid;
  grid-template-columns: 2fr 1fr;
  gap: 2rem;
}

/* Product List Styles */
.product-list {
  padding: 1rem;
  background: white;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.products {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 1rem;
}

.product-item {
  padding: 1rem;
  border: 1px solid #ddd;
  border-radius: 5px;
  text-align: center;
}

.product-item .price {
  font-size: 1.2rem;
  color: #0066cc;
  font-weight: bold;
}

.product-item .stock {
  color: #666;
  margin: 0.5rem 0;
}

.product-item .in-cart {
  color: #2ecc71;
  font-weight: bold;
}

/* Cart Summary Styles */
.cart-summary {
  padding: 1rem;
  background: white;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.cart-item {
  display: grid;
  grid-template-columns: 2fr 2fr 1fr;
  align-items: center;
  padding: 1rem;
  border-bottom: 1px solid #ddd;
}

.item-controls {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.quantity-btn {
  padding: 0.25rem 0.5rem;
  border: 1px solid #ddd;
  background: white;
  cursor: pointer;
}

.remove-btn {
  padding: 0.25rem 0.5rem;
  background: #e74c3c;
  color: white;
  border: none;
  border-radius: 3px;
  cursor: pointer;
}

.cart-total {
  margin-top: 1rem;
  padding-top: 1rem;
  border-top: 2px solid #ddd;
  text-align: right;
}

button {
  padding: 0.5rem 1rem;
  background: #0066cc;
  color: white;
  border: none;
  border-radius: 3px;
  cursor: pointer;
}

button:disabled {
  background: #ccc;
  cursor: not-allowed;
}

button:hover:not(:disabled) {
  background: #0052a3;
}
```

## 6. Expected Output

Start the development server:
```bash
npm start
```

Visit http://localhost:3000 to see:

1. Product list with:
   - Product details
   - Stock information
   - Add to cart button
   - In-cart quantity

2. Shopping cart with:
   - Added items
   - Quantity controls
   - Remove button
   - Total calculation

3. Features to test:
   - Adding products to cart
   - Updating quantities
   - Removing items
   - Stock limitations
   - Total updates

## 7. Practice Exercises
1. Add product categories
2. Implement a wishlist
3. Add a checkout form
4. Create a product search
5. Add sorting options

## 8. Verification and Validation
- [ ] Props pass correctly
- [ ] State updates properly
- [ ] Cart calculations work
- [ ] Stock management works
- [ ] UI updates correctly

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| State not updating | Check setState usage |
| Props undefined | Verify prop passing |
| Cart total incorrect | Check calculations |
| Stock not updating | Verify state logic |

## 10. Additional Resources
- [React State and Lifecycle](https://reactjs.org/docs/state-and-lifecycle.html)
- [Handling Events](https://reactjs.org/docs/handling-events.html)
- [Lists and Keys](https://reactjs.org/docs/lists-and-keys.html)
- [Lifting State Up](https://reactjs.org/docs/lifting-state-up.html)
