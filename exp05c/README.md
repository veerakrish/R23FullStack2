# Experiment 5C: ReactJS - Creating and Nesting Components

## 1. Experiment Information
- **Experiment Number**: 05C
- **Title**: Creating Function and Class Components
- **Duration**: 45 minutes

## 2. Learning Objectives
- Create functional components
- Create class components
- Understand component nesting
- Pass props between components
- Handle component state
- Use component lifecycle methods

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
- Experiments 5A and 5B completion

## 4. Theory and Concepts
### React Components
- Function components
- Class components
- Props
- State
- Component lifecycle
- Component composition

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create new React project
npx create-react-app component-demo
cd component-demo

# Start development server
npm start
```

### 2. Create Components

Create src/components/Header.js (Function Component):
```javascript
import React from 'react';

function Header({ title, subtitle }) {
  return (
    <header className="header">
      <h1>{title}</h1>
      {subtitle && <p>{subtitle}</p>}
    </header>
  );
}

export default Header;
```

Create src/components/Counter.js (Class Component):
```javascript
import React, { Component } from 'react';

class Counter extends Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  componentDidMount() {
    console.log('Counter mounted');
  }

  componentDidUpdate(prevProps, prevState) {
    if (prevState.count !== this.state.count) {
      console.log('Count updated:', this.state.count);
    }
  }

  componentWillUnmount() {
    console.log('Counter will unmount');
  }

  increment = () => {
    this.setState(prevState => ({
      count: prevState.count + 1
    }));
  };

  decrement = () => {
    this.setState(prevState => ({
      count: prevState.count - 1
    }));
  };

  render() {
    const { count } = this.state;
    const { title } = this.props;

    return (
      <div className="counter">
        <h3>{title}</h3>
        <p>Count: {count}</p>
        <button onClick={this.decrement}>-</button>
        <button onClick={this.increment}>+</button>
      </div>
    );
  }
}

export default Counter;
```

Create src/components/TodoList.js (Function Component with Hooks):
```javascript
import React, { useState } from 'react';
import TodoItem from './TodoItem';

function TodoList() {
  const [todos, setTodos] = useState([]);
  const [input, setInput] = useState('');

  const addTodo = (e) => {
    e.preventDefault();
    if (!input.trim()) return;
    
    setTodos([...todos, { id: Date.now(), text: input, completed: false }]);
    setInput('');
  };

  const toggleTodo = (id) => {
    setTodos(todos.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    ));
  };

  const deleteTodo = (id) => {
    setTodos(todos.filter(todo => todo.id !== id));
  };

  return (
    <div className="todo-list">
      <h3>Todo List</h3>
      <form onSubmit={addTodo}>
        <input
          type="text"
          value={input}
          onChange={(e) => setInput(e.target.value)}
          placeholder="Add new todo"
        />
        <button type="submit">Add</button>
      </form>
      <div className="todos">
        {todos.map(todo => (
          <TodoItem
            key={todo.id}
            todo={todo}
            onToggle={toggleTodo}
            onDelete={deleteTodo}
          />
        ))}
      </div>
    </div>
  );
}

export default TodoList;
```

Create src/components/TodoItem.js (Function Component):
```javascript
import React from 'react';

function TodoItem({ todo, onToggle, onDelete }) {
  return (
    <div className="todo-item">
      <input
        type="checkbox"
        checked={todo.completed}
        onChange={() => onToggle(todo.id)}
      />
      <span style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}>
        {todo.text}
      </span>
      <button onClick={() => onDelete(todo.id)}>Delete</button>
    </div>
  );
}

export default TodoItem;
```

Create src/components/UserProfile.js (Class Component):
```javascript
import React, { Component } from 'react';

class UserProfile extends Component {
  state = {
    isEditing: false,
    userData: {
      name: this.props.name || '',
      email: this.props.email || '',
      bio: this.props.bio || ''
    }
  };

  toggleEdit = () => {
    this.setState(prevState => ({
      isEditing: !prevState.isEditing
    }));
  };

  handleChange = (e) => {
    const { name, value } = e.target;
    this.setState(prevState => ({
      userData: {
        ...prevState.userData,
        [name]: value
      }
    }));
  };

  handleSubmit = (e) => {
    e.preventDefault();
    this.toggleEdit();
    if (this.props.onUpdate) {
      this.props.onUpdate(this.state.userData);
    }
  };

  render() {
    const { isEditing, userData } = this.state;

    return (
      <div className="user-profile">
        <h3>User Profile</h3>
        {isEditing ? (
          <form onSubmit={this.handleSubmit}>
            <div>
              <label>Name:</label>
              <input
                type="text"
                name="name"
                value={userData.name}
                onChange={this.handleChange}
              />
            </div>
            <div>
              <label>Email:</label>
              <input
                type="email"
                name="email"
                value={userData.email}
                onChange={this.handleChange}
              />
            </div>
            <div>
              <label>Bio:</label>
              <textarea
                name="bio"
                value={userData.bio}
                onChange={this.handleChange}
              />
            </div>
            <button type="submit">Save</button>
            <button type="button" onClick={this.toggleEdit}>Cancel</button>
          </form>
        ) : (
          <div>
            <p><strong>Name:</strong> {userData.name}</p>
            <p><strong>Email:</strong> {userData.email}</p>
            <p><strong>Bio:</strong> {userData.bio}</p>
            <button onClick={this.toggleEdit}>Edit</button>
          </div>
        )}
      </div>
    );
  }
}

export default UserProfile;
```

### 3. Update App Component

Replace src/App.js:
```javascript
import React, { useState } from 'react';
import './App.css';
import Header from './components/Header';
import Counter from './components/Counter';
import TodoList from './components/TodoList';
import UserProfile from './components/UserProfile';

function App() {
  const [showCounter, setShowCounter] = useState(true);
  const [userData, setUserData] = useState({
    name: 'John Doe',
    email: 'john@example.com',
    bio: 'React developer'
  });

  const handleUserUpdate = (newData) => {
    setUserData(newData);
    alert('Profile updated!');
  };

  return (
    <div className="App">
      <Header 
        title="React Components Demo" 
        subtitle="Learning Function and Class Components" 
      />

      <main>
        <section>
          <h2>Class Component Example</h2>
          <button onClick={() => setShowCounter(!showCounter)}>
            {showCounter ? 'Hide' : 'Show'} Counter
          </button>
          {showCounter && <Counter title="My Counter" />}
        </section>

        <section>
          <h2>Function Component with Hooks</h2>
          <TodoList />
        </section>

        <section>
          <h2>Class Component with Forms</h2>
          <UserProfile 
            {...userData}
            onUpdate={handleUserUpdate}
          />
        </section>
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

.header {
  text-align: center;
  margin-bottom: 2rem;
  padding: 1rem;
  background-color: #f0f0f0;
  border-radius: 5px;
}

section {
  margin-bottom: 2rem;
  padding: 1rem;
  border: 1px solid #ddd;
  border-radius: 5px;
}

/* Counter Styles */
.counter {
  text-align: center;
  padding: 1rem;
}

.counter button {
  margin: 0 0.5rem;
  padding: 0.5rem 1rem;
  font-size: 1.2rem;
}

/* Todo List Styles */
.todo-list {
  max-width: 500px;
  margin: 0 auto;
}

.todo-list form {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

.todo-list input[type="text"] {
  flex: 1;
  padding: 0.5rem;
  border: 1px solid #ddd;
  border-radius: 3px;
}

.todo-item {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.5rem;
  border-bottom: 1px solid #eee;
}

.todo-item button {
  margin-left: auto;
}

/* User Profile Styles */
.user-profile {
  max-width: 500px;
  margin: 0 auto;
}

.user-profile form {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.user-profile form div {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.user-profile input,
.user-profile textarea {
  padding: 0.5rem;
  border: 1px solid #ddd;
  border-radius: 3px;
}

.user-profile button {
  padding: 0.5rem 1rem;
  background-color: #0066cc;
  color: white;
  border: none;
  border-radius: 3px;
  cursor: pointer;
  margin-right: 0.5rem;
}

.user-profile button:hover {
  background-color: #0052a3;
}
```

## 6. Expected Output

Start the development server:
```bash
npm start
```

Visit http://localhost:3000 to see:

1. Header component with title and subtitle
2. Counter component with state management
3. Todo list with add/toggle/delete functionality
4. User profile with edit form
5. Component mounting/unmounting demo

## 7. Practice Exercises
1. Add prop type validation
2. Create a reusable button component
3. Implement error boundaries
4. Add more lifecycle methods
5. Create a theme switcher component

## 8. Verification and Validation
- [ ] Function components work
- [ ] Class components work
- [ ] Props pass correctly
- [ ] State updates properly
- [ ] Components nest correctly
- [ ] Forms function properly

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| Props undefined | Check prop passing |
| State not updating | Use setState correctly |
| Component not rendering | Check export/import |
| Event handlers not working | Check binding |

## 10. Additional Resources
- [Components and Props](https://reactjs.org/docs/components-and-props.html)
- [State and Lifecycle](https://reactjs.org/docs/state-and-lifecycle.html)
- [Hooks Overview](https://reactjs.org/docs/hooks-overview.html)
- [React Component Patterns](https://reactpatterns.com/)
