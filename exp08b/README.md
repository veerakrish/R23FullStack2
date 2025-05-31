# Experiment 8B: ReactJS - Updating the Screen

## 1. Experiment Information
- **Experiment Number**: 08B
- **Title**: Screen Updates in React
- **Duration**: 45 minutes

## 2. Learning Objectives
- Understand React's update mechanism
- Implement different update patterns
- Use state and effect hooks
- Handle async updates
- Optimize performance
- Manage component lifecycle

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
### Screen Updates
- Component lifecycle
- State updates
- Effect hooks
- Async operations
- Performance optimization
- Update patterns

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create new React project
npx create-react-app update-demo
cd update-demo

# Start development server
npm start
```

### 2. Create Components

Create src/components/Counter.js:
```javascript
import React, { useState, useCallback } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  const [step, setStep] = useState(1);

  // Memoized increment function
  const increment = useCallback(() => {
    setCount(prev => prev + step);
  }, [step]);

  // Memoized decrement function
  const decrement = useCallback(() => {
    setCount(prev => prev - step);
  }, [step]);

  return (
    <div className="counter">
      <h2>Counter</h2>
      <div className="counter-display">
        <button onClick={decrement}>-</button>
        <span className="count">{count}</span>
        <button onClick={increment}>+</button>
      </div>
      <div className="step-control">
        <label>
          Step:
          <select
            value={step}
            onChange={(e) => setStep(Number(e.target.value))}
          >
            <option value={1}>1</option>
            <option value={5}>5</option>
            <option value={10}>10</option>
          </select>
        </label>
      </div>
    </div>
  );
}

export default Counter;
```

Create src/components/Timer.js:
```javascript
import React, { useState, useEffect, useRef } from 'react';

function Timer() {
  const [time, setTime] = useState(0);
  const [isRunning, setIsRunning] = useState(false);
  const timerRef = useRef(null);

  useEffect(() => {
    if (isRunning) {
      timerRef.current = setInterval(() => {
        setTime(prev => prev + 1);
      }, 1000);
    }

    return () => {
      if (timerRef.current) {
        clearInterval(timerRef.current);
      }
    };
  }, [isRunning]);

  const toggleTimer = () => {
    setIsRunning(prev => !prev);
  };

  const resetTimer = () => {
    setIsRunning(false);
    setTime(0);
  };

  const formatTime = (seconds) => {
    const minutes = Math.floor(seconds / 60);
    const remainingSeconds = seconds % 60;
    return `${minutes}:${remainingSeconds.toString().padStart(2, '0')}`;
  };

  return (
    <div className="timer">
      <h2>Timer</h2>
      <div className="timer-display">
        {formatTime(time)}
      </div>
      <div className="timer-controls">
        <button onClick={toggleTimer}>
          {isRunning ? 'Pause' : 'Start'}
        </button>
        <button onClick={resetTimer}>Reset</button>
      </div>
    </div>
  );
}

export default Timer;
```

Create src/components/TodoList.js:
```javascript
import React, { useState, useReducer } from 'react';

// Action types
const ADD_TODO = 'ADD_TODO';
const TOGGLE_TODO = 'TOGGLE_TODO';
const DELETE_TODO = 'DELETE_TODO';

// Reducer function
function todoReducer(state, action) {
  switch (action.type) {
    case ADD_TODO:
      return [
        ...state,
        {
          id: Date.now(),
          text: action.payload,
          completed: false
        }
      ];
    case TOGGLE_TODO:
      return state.map(todo =>
        todo.id === action.payload
          ? { ...todo, completed: !todo.completed }
          : todo
      );
    case DELETE_TODO:
      return state.filter(todo => todo.id !== action.payload);
    default:
      return state;
  }
}

function TodoList() {
  const [todos, dispatch] = useReducer(todoReducer, []);
  const [input, setInput] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!input.trim()) return;

    dispatch({ type: ADD_TODO, payload: input });
    setInput('');
  };

  return (
    <div className="todo-list">
      <h2>Todo List</h2>
      
      <form onSubmit={handleSubmit} className="todo-form">
        <input
          type="text"
          value={input}
          onChange={(e) => setInput(e.target.value)}
          placeholder="Add new todo..."
        />
        <button type="submit">Add</button>
      </form>

      <ul className="todos">
        {todos.map(todo => (
          <li
            key={todo.id}
            className={`todo-item ${todo.completed ? 'completed' : ''}`}
          >
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => dispatch({
                type: TOGGLE_TODO,
                payload: todo.id
              })}
            />
            <span className="todo-text">{todo.text}</span>
            <button
              onClick={() => dispatch({
                type: DELETE_TODO,
                payload: todo.id
              })}
              className="delete-btn"
            >
              Delete
            </button>
          </li>
        ))}
      </ul>

      <div className="todo-stats">
        <p>Total: {todos.length}</p>
        <p>
          Completed: {todos.filter(todo => todo.completed).length}
        </p>
      </div>
    </div>
  );
}

export default TodoList;
```

Create src/components/DataFetcher.js:
```javascript
import React, { useState, useEffect } from 'react';

function DataFetcher() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  const [refreshKey, setRefreshKey] = useState(0);

  useEffect(() => {
    let mounted = true;

    const fetchData = async () => {
      try {
        setLoading(true);
        setError(null);
        
        // Simulated API call
        const response = await new Promise((resolve) => {
          setTimeout(() => {
            resolve({
              posts: [
                { id: 1, title: 'Post 1', content: 'Content 1' },
                { id: 2, title: 'Post 2', content: 'Content 2' },
                { id: 3, title: 'Post 3', content: 'Content 3' }
              ]
            });
          }, 1500);
        });

        if (mounted) {
          setData(response.posts);
          setLoading(false);
        }
      } catch (err) {
        if (mounted) {
          setError('Failed to fetch data');
          setLoading(false);
        }
      }
    };

    fetchData();

    return () => {
      mounted = false;
    };
  }, [refreshKey]);

  const handleRefresh = () => {
    setRefreshKey(prev => prev + 1);
  };

  if (loading) {
    return (
      <div className="data-fetcher loading">
        <h2>Loading...</h2>
        <div className="spinner"></div>
      </div>
    );
  }

  if (error) {
    return (
      <div className="data-fetcher error">
        <h2>Error</h2>
        <p>{error}</p>
        <button onClick={handleRefresh}>Retry</button>
      </div>
    );
  }

  return (
    <div className="data-fetcher">
      <h2>Posts</h2>
      <button onClick={handleRefresh} className="refresh-btn">
        Refresh
      </button>
      <div className="posts">
        {data.map(post => (
          <div key={post.id} className="post">
            <h3>{post.title}</h3>
            <p>{post.content}</p>
          </div>
        ))}
      </div>
    </div>
  );
}

export default DataFetcher;
```

### 3. Update App Component

Replace src/App.js:
```javascript
import React from 'react';
import './App.css';
import Counter from './components/Counter';
import Timer from './components/Timer';
import TodoList from './components/TodoList';
import DataFetcher from './components/DataFetcher';

function App() {
  return (
    <div className="App">
      <header>
        <h1>Screen Updates in React</h1>
        <p>Exploring different update patterns</p>
      </header>

      <main>
        <Counter />
        <Timer />
        <TodoList />
        <DataFetcher />
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

/* Counter Styles */
.counter {
  margin-bottom: 2rem;
  padding: 1.5rem;
  background-color: white;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.counter-display {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 1rem;
  margin: 1rem 0;
}

.count {
  font-size: 2rem;
  font-weight: bold;
  min-width: 60px;
  text-align: center;
}

.step-control {
  text-align: center;
}

/* Timer Styles */
.timer {
  margin-bottom: 2rem;
  padding: 1.5rem;
  background-color: white;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.timer-display {
  font-size: 2.5rem;
  font-weight: bold;
  text-align: center;
  margin: 1rem 0;
}

.timer-controls {
  display: flex;
  justify-content: center;
  gap: 1rem;
}

/* Todo List Styles */
.todo-list {
  margin-bottom: 2rem;
  padding: 1.5rem;
  background-color: white;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.todo-form {
  display: flex;
  gap: 1rem;
  margin-bottom: 1rem;
}

.todo-form input {
  flex: 1;
  padding: 0.5rem;
  border: 1px solid #ddd;
  border-radius: 3px;
}

.todos {
  list-style: none;
  padding: 0;
}

.todo-item {
  display: flex;
  align-items: center;
  gap: 1rem;
  padding: 0.5rem;
  border-bottom: 1px solid #eee;
}

.todo-item.completed .todo-text {
  text-decoration: line-through;
  color: #999;
}

.todo-text {
  flex: 1;
}

.delete-btn {
  padding: 0.25rem 0.5rem;
  background-color: #dc3545;
}

.todo-stats {
  display: flex;
  justify-content: space-around;
  margin-top: 1rem;
  padding-top: 1rem;
  border-top: 1px solid #eee;
}

/* Data Fetcher Styles */
.data-fetcher {
  margin-bottom: 2rem;
  padding: 1.5rem;
  background-color: white;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.loading {
  text-align: center;
}

.spinner {
  width: 40px;
  height: 40px;
  margin: 1rem auto;
  border: 4px solid #f3f3f3;
  border-top: 4px solid #3498db;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.error {
  color: #dc3545;
  text-align: center;
}

.refresh-btn {
  margin-bottom: 1rem;
}

.posts {
  display: grid;
  gap: 1rem;
}

.post {
  padding: 1rem;
  background-color: #f8f9fa;
  border-radius: 5px;
}

/* Common Styles */
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

select {
  padding: 0.5rem;
  border: 1px solid #ddd;
  border-radius: 3px;
}

h2 {
  margin-bottom: 1rem;
}
```

## 6. Expected Output

Start the development server:
```bash
npm start
```

Visit http://localhost:3000 to see:

1. Counter:
   - Increment/decrement
   - Step control
   - State updates

2. Timer:
   - Start/pause
   - Reset
   - Effect cleanup

3. Todo List:
   - Add todos
   - Toggle completion
   - Delete todos
   - Reducer pattern

4. Data Fetcher:
   - Loading state
   - Error handling
   - Data display
   - Refresh functionality

## 7. Practice Exercises
1. Add animations
2. Implement debouncing
3. Add batch updates
4. Create custom hooks
5. Add error boundaries

## 8. Verification and Validation
- [ ] Counter works
- [ ] Timer works
- [ ] Todo list works
- [ ] Data fetching works
- [ ] Updates are smooth

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| State not updating | Check setState calls |
| Memory leaks | Verify cleanup |
| Performance issues | Use memoization |
| Update loops | Check dependencies |

## 10. Additional Resources
- [React State Updates](https://reactjs.org/docs/state-and-lifecycle.html)
- [useEffect Hook](https://reactjs.org/docs/hooks-effect.html)
- [useReducer Hook](https://reactjs.org/docs/hooks-reference.html#usereducer)
- [Performance Optimization](https://reactjs.org/docs/optimizing-performance.html)
