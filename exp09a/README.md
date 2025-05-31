# Experiment 9A: ReactJS - Understanding Hooks

## 1. Experiment Information
- **Experiment Number**: 09A
- **Title**: Understanding React Hooks
- **Duration**: 45 minutes

## 2. Learning Objectives
- Understand React Hooks
- Use built-in hooks
- Create custom hooks
- Handle side effects
- Optimize performance
- Manage complex state

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
### React Hooks
- useState
- useEffect
- useContext
- useReducer
- useMemo
- useCallback
- useRef
- Custom hooks

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create new React project
npx create-react-app hooks-demo
cd hooks-demo

# Start development server
npm start
```

### 2. Create Components

Create src/hooks/useLocalStorage.js:
```javascript
import { useState, useEffect } from 'react';

function useLocalStorage(key, initialValue) {
  // Get initial value from localStorage or use provided initialValue
  const [value, setValue] = useState(() => {
    const storedValue = localStorage.getItem(key);
    return storedValue ? JSON.parse(storedValue) : initialValue;
  });

  // Update localStorage when value changes
  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
}

export default useLocalStorage;
```

Create src/hooks/useWindowSize.js:
```javascript
import { useState, useEffect } from 'react';

function useWindowSize() {
  const [windowSize, setWindowSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight
  });

  useEffect(() => {
    // Handler to call on window resize
    const handleResize = () => {
      setWindowSize({
        width: window.innerWidth,
        height: window.innerHeight
      });
    };

    // Add event listener
    window.addEventListener('resize', handleResize);

    // Remove event listener on cleanup
    return () => window.removeEventListener('resize', handleResize);
  }, []);

  return windowSize;
}

export default useWindowSize;
```

Create src/hooks/useDebounce.js:
```javascript
import { useState, useEffect } from 'react';

function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    // Set debouncedValue after delay
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    // Cancel the timeout if value changes or component unmounts
    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);

  return debouncedValue;
}

export default useDebounce;
```

Create src/components/StateDemo.js:
```javascript
import React, { useState } from 'react';
import useLocalStorage from '../hooks/useLocalStorage';

function StateDemo() {
  // Regular useState
  const [count, setCount] = useState(0);
  
  // Custom hook using localStorage
  const [savedCount, setSavedCount] = useLocalStorage('savedCount', 0);

  return (
    <div className="state-demo">
      <h2>useState Demo</h2>
      
      <div className="counter regular">
        <h3>Regular Counter</h3>
        <p>Count: {count}</p>
        <button onClick={() => setCount(prev => prev + 1)}>
          Increment
        </button>
      </div>

      <div className="counter persistent">
        <h3>Persistent Counter</h3>
        <p>Saved Count: {savedCount}</p>
        <button onClick={() => setSavedCount(prev => prev + 1)}>
          Increment (Persists on Reload)
        </button>
      </div>
    </div>
  );
}

export default StateDemo;
```

Create src/components/EffectDemo.js:
```javascript
import React, { useState, useEffect } from 'react';
import useWindowSize from '../hooks/useWindowSize';

function EffectDemo() {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);
  const windowSize = useWindowSize();

  useEffect(() => {
    // Simulated API call
    const fetchPosts = async () => {
      try {
        // Simulate delay
        await new Promise(resolve => setTimeout(resolve, 1000));
        
        // Simulate data
        const data = [
          { id: 1, title: 'Post 1' },
          { id: 2, title: 'Post 2' },
          { id: 3, title: 'Post 3' }
        ];
        
        setPosts(data);
        setLoading(false);
      } catch (error) {
        console.error('Error fetching posts:', error);
        setLoading(false);
      }
    };

    fetchPosts();
  }, []);

  return (
    <div className="effect-demo">
      <h2>useEffect Demo</h2>

      <div className="window-size">
        <h3>Window Size</h3>
        <p>Width: {windowSize.width}px</p>
        <p>Height: {windowSize.height}px</p>
      </div>

      <div className="posts">
        <h3>Posts</h3>
        {loading ? (
          <p>Loading...</p>
        ) : (
          <ul>
            {posts.map(post => (
              <li key={post.id}>{post.title}</li>
            ))}
          </ul>
        )}
      </div>
    </div>
  );
}

export default EffectDemo;
```

Create src/components/MemoDemo.js:
```javascript
import React, { useState, useMemo, useCallback } from 'react';

function ExpensiveComponent({ value, onClick }) {
  console.log('ExpensiveComponent rendered');
  return (
    <div className="expensive">
      <p>Expensive Calculation Result: {value}</p>
      <button onClick={onClick}>Recalculate</button>
    </div>
  );
}

// Memoize the component
const MemoizedExpensiveComponent = React.memo(ExpensiveComponent);

function MemoDemo() {
  const [count, setCount] = useState(0);
  const [input, setInput] = useState('');

  // Expensive calculation
  const expensiveValue = useMemo(() => {
    console.log('Expensive calculation running...');
    let result = 0;
    for (let i = 0; i < count * 1000; i++) {
      result += Math.random();
    }
    return result.toFixed(2);
  }, [count]);

  // Memoized callback
  const handleClick = useCallback(() => {
    setCount(prev => prev + 1);
  }, []);

  return (
    <div className="memo-demo">
      <h2>useMemo & useCallback Demo</h2>

      <div className="controls">
        <input
          type="text"
          value={input}
          onChange={(e) => setInput(e.target.value)}
          placeholder="Type something..."
        />
        <p>Input changes won't trigger expensive calculation</p>
      </div>

      <MemoizedExpensiveComponent
        value={expensiveValue}
        onClick={handleClick}
      />
    </div>
  );
}

export default MemoDemo;
```

Create src/components/RefDemo.js:
```javascript
import React, { useRef, useState, useEffect } from 'react';
import useDebounce from '../hooks/useDebounce';

function RefDemo() {
  const [text, setText] = useState('');
  const debouncedText = useDebounce(text, 500);
  const inputRef = useRef(null);
  const renderCount = useRef(0);

  // Track component renders
  useEffect(() => {
    renderCount.current += 1;
  });

  // Focus input on mount
  useEffect(() => {
    inputRef.current.focus();
  }, []);

  return (
    <div className="ref-demo">
      <h2>useRef & Custom Hook Demo</h2>

      <div className="input-section">
        <input
          ref={inputRef}
          type="text"
          value={text}
          onChange={(e) => setText(e.target.value)}
          placeholder="Type something..."
        />
      </div>

      <div className="info">
        <p>Current text: {text}</p>
        <p>Debounced text: {debouncedText}</p>
        <p>Render count: {renderCount.current}</p>
      </div>

      <button onClick={() => inputRef.current.focus()}>
        Focus Input
      </button>
    </div>
  );
}

export default RefDemo;
```

### 3. Update App Component

Replace src/App.js:
```javascript
import React from 'react';
import './App.css';
import StateDemo from './components/StateDemo';
import EffectDemo from './components/EffectDemo';
import MemoDemo from './components/MemoDemo';
import RefDemo from './components/RefDemo';

function App() {
  return (
    <div className="App">
      <header>
        <h1>React Hooks</h1>
        <p>Understanding different types of hooks</p>
      </header>

      <main>
        <StateDemo />
        <EffectDemo />
        <MemoDemo />
        <RefDemo />
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
  max-width: 800px;
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

/* Common Component Styles */
.state-demo,
.effect-demo,
.memo-demo,
.ref-demo {
  margin-bottom: 2rem;
  padding: 1.5rem;
  background-color: white;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

/* State Demo Styles */
.counter {
  margin-bottom: 1.5rem;
  padding: 1rem;
  background-color: #f8f9fa;
  border-radius: 5px;
}

.counter.persistent {
  background-color: #e8f4fd;
}

/* Effect Demo Styles */
.window-size {
  margin-bottom: 1.5rem;
  padding: 1rem;
  background-color: #f8f9fa;
  border-radius: 5px;
}

.posts ul {
  list-style: none;
  padding: 0;
}

.posts li {
  padding: 0.5rem;
  margin-bottom: 0.5rem;
  background-color: #f8f9fa;
  border-radius: 3px;
}

/* Memo Demo Styles */
.controls {
  margin-bottom: 1.5rem;
}

.expensive {
  padding: 1rem;
  background-color: #f8f9fa;
  border-radius: 5px;
}

/* Ref Demo Styles */
.input-section {
  margin-bottom: 1rem;
}

.info {
  margin: 1rem 0;
  padding: 1rem;
  background-color: #f8f9fa;
  border-radius: 5px;
}

/* Common Form Styles */
input {
  width: 100%;
  padding: 0.5rem;
  margin-bottom: 0.5rem;
  border: 1px solid #ddd;
  border-radius: 3px;
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

h2 {
  margin-bottom: 1.5rem;
}

h3 {
  margin-bottom: 1rem;
}
```

## 6. Expected Output

Start the development server:
```bash
npm start
```

Visit http://localhost:3000 to see:

1. State Demo:
   - Regular counter
   - Persistent counter with localStorage

2. Effect Demo:
   - Window size tracking
   - Data fetching

3. Memo Demo:
   - Expensive calculations
   - Memoized components
   - Performance optimization

4. Ref Demo:
   - Input focus
   - Render counting
   - Debounced input

## 7. Practice Exercises
1. Create more custom hooks
2. Add error handling
3. Implement useContext
4. Add useReducer
5. Create complex state management

## 8. Verification and Validation
- [ ] All hooks work correctly
- [ ] Custom hooks work
- [ ] Side effects work
- [ ] Performance is optimized
- [ ] State persists correctly

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| Infinite loops | Check dependencies |
| Memory leaks | Verify cleanup |
| Stale closures | Use proper deps |
| Performance issues | Use memoization |

## 10. Additional Resources
- [React Hooks](https://reactjs.org/docs/hooks-intro.html)
- [Rules of Hooks](https://reactjs.org/docs/hooks-rules.html)
- [Custom Hooks](https://reactjs.org/docs/hooks-custom.html)
- [Hooks FAQ](https://reactjs.org/docs/hooks-faq.html)
