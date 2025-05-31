# Experiment 6C: ReactJS - Responding to Events

## 1. Experiment Information
- **Experiment Number**: 06C
- **Title**: Event Handling in React
- **Duration**: 45 minutes

## 2. Learning Objectives
- Handle user events in React
- Implement event handlers
- Use synthetic events
- Manage form events
- Create custom event handlers
- Understand event bubbling

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- Visual Studio Code
- Web browser

### Knowledge Requirements
- JavaScript events
- React basics
- Previous experiments completion

## 4. Theory and Concepts
### React Events
- Synthetic Events
- Event Handling
- Event Bubbling
- Event Delegation
- Form Events
- Custom Events

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create new React project
npx create-react-app event-handling-demo
cd event-handling-demo

# Start development server
npm start
```

### 2. Create Components

Create src/components/EventDemo.js:
```javascript
import React, { useState } from 'react';

function EventDemo() {
  const [mousePosition, setMousePosition] = useState({ x: 0, y: 0 });
  const [clickCount, setClickCount] = useState(0);
  const [keyPressed, setKeyPressed] = useState('');
  const [isDragging, setIsDragging] = useState(false);
  const [dragPosition, setDragPosition] = useState({ x: 0, y: 0 });

  // Mouse move handler
  const handleMouseMove = (e) => {
    setMousePosition({
      x: e.clientX,
      y: e.clientY
    });
  };

  // Click handler with event object
  const handleClick = (e) => {
    setClickCount(prev => prev + 1);
    console.log('Click event:', e);
  };

  // Key press handler
  const handleKeyPress = (e) => {
    setKeyPressed(e.key);
  };

  // Drag handlers
  const handleDragStart = (e) => {
    setIsDragging(true);
    e.target.style.opacity = '0.5';
  };

  const handleDrag = (e) => {
    setDragPosition({
      x: e.clientX,
      y: e.clientY
    });
  };

  const handleDragEnd = (e) => {
    setIsDragging(false);
    e.target.style.opacity = '1';
  };

  return (
    <div className="event-demo">
      <section className="mouse-events">
        <h2>Mouse Events</h2>
        <div 
          className="mouse-area"
          onMouseMove={handleMouseMove}
          onClick={handleClick}
        >
          <p>Move mouse or click here</p>
          <p>Mouse position: X: {mousePosition.x}, Y: {mousePosition.y}</p>
          <p>Click count: {clickCount}</p>
        </div>
      </section>

      <section className="keyboard-events">
        <h2>Keyboard Events</h2>
        <input
          type="text"
          onKeyPress={handleKeyPress}
          placeholder="Type something..."
        />
        <p>Last key pressed: {keyPressed}</p>
      </section>

      <section className="drag-events">
        <h2>Drag Events</h2>
        <div
          className="draggable"
          draggable="true"
          onDragStart={handleDragStart}
          onDrag={handleDrag}
          onDragEnd={handleDragEnd}
        >
          Drag me!
        </div>
        {isDragging && (
          <p>
            Dragging position: X: {dragPosition.x}, Y: {dragPosition.y}
          </p>
        )}
      </section>
    </div>
  );
}

export default EventDemo;
```

Create src/components/FormEvents.js:
```javascript
import React, { useState } from 'react';

function FormEvents() {
  const [formData, setFormData] = useState({
    username: '',
    email: '',
    password: '',
    confirmPassword: '',
    agreeToTerms: false
  });

  const [errors, setErrors] = useState({});

  // Handle input changes
  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: type === 'checkbox' ? checked : value
    }));
  };

  // Handle form submission
  const handleSubmit = (e) => {
    e.preventDefault();
    
    // Validate form
    const newErrors = {};
    
    if (!formData.username.trim()) {
      newErrors.username = 'Username is required';
    }

    if (!formData.email.trim()) {
      newErrors.email = 'Email is required';
    } else if (!/\S+@\S+\.\S+/.test(formData.email)) {
      newErrors.email = 'Email is invalid';
    }

    if (!formData.password) {
      newErrors.password = 'Password is required';
    } else if (formData.password.length < 6) {
      newErrors.password = 'Password must be at least 6 characters';
    }

    if (formData.password !== formData.confirmPassword) {
      newErrors.confirmPassword = 'Passwords do not match';
    }

    if (!formData.agreeToTerms) {
      newErrors.agreeToTerms = 'You must agree to the terms';
    }

    if (Object.keys(newErrors).length > 0) {
      setErrors(newErrors);
      return;
    }

    // Form is valid, process it
    console.log('Form submitted:', formData);
    alert('Form submitted successfully!');
  };

  // Handle form reset
  const handleReset = (e) => {
    e.preventDefault();
    setFormData({
      username: '',
      email: '',
      password: '',
      confirmPassword: '',
      agreeToTerms: false
    });
    setErrors({});
  };

  return (
    <div className="form-events">
      <h2>Form Events</h2>
      <form onSubmit={handleSubmit} onReset={handleReset}>
        <div className="form-group">
          <label htmlFor="username">Username:</label>
          <input
            type="text"
            id="username"
            name="username"
            value={formData.username}
            onChange={handleChange}
            className={errors.username ? 'error' : ''}
          />
          {errors.username && (
            <span className="error-message">{errors.username}</span>
          )}
        </div>

        <div className="form-group">
          <label htmlFor="email">Email:</label>
          <input
            type="email"
            id="email"
            name="email"
            value={formData.email}
            onChange={handleChange}
            className={errors.email ? 'error' : ''}
          />
          {errors.email && (
            <span className="error-message">{errors.email}</span>
          )}
        </div>

        <div className="form-group">
          <label htmlFor="password">Password:</label>
          <input
            type="password"
            id="password"
            name="password"
            value={formData.password}
            onChange={handleChange}
            className={errors.password ? 'error' : ''}
          />
          {errors.password && (
            <span className="error-message">{errors.password}</span>
          )}
        </div>

        <div className="form-group">
          <label htmlFor="confirmPassword">Confirm Password:</label>
          <input
            type="password"
            id="confirmPassword"
            name="confirmPassword"
            value={formData.confirmPassword}
            onChange={handleChange}
            className={errors.confirmPassword ? 'error' : ''}
          />
          {errors.confirmPassword && (
            <span className="error-message">{errors.confirmPassword}</span>
          )}
        </div>

        <div className="form-group checkbox">
          <label>
            <input
              type="checkbox"
              name="agreeToTerms"
              checked={formData.agreeToTerms}
              onChange={handleChange}
            />
            I agree to the terms and conditions
          </label>
          {errors.agreeToTerms && (
            <span className="error-message">{errors.agreeToTerms}</span>
          )}
        </div>

        <div className="form-buttons">
          <button type="submit">Submit</button>
          <button type="reset">Reset</button>
        </div>
      </form>
    </div>
  );
}

export default FormEvents;
```

### 3. Update App Component

Replace src/App.js:
```javascript
import React from 'react';
import './App.css';
import EventDemo from './components/EventDemo';
import FormEvents from './components/FormEvents';

function App() {
  return (
    <div className="App">
      <header>
        <h1>React Event Handling</h1>
        <p>Exploring different types of events</p>
      </header>

      <main>
        <EventDemo />
        <FormEvents />
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

/* Event Demo Styles */
.event-demo {
  margin-bottom: 2rem;
}

.mouse-area {
  padding: 2rem;
  background-color: #f8f9fa;
  border: 1px solid #dee2e6;
  border-radius: 5px;
  text-align: center;
  cursor: pointer;
}

.draggable {
  width: 100px;
  height: 100px;
  background-color: #0066cc;
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: move;
  border-radius: 5px;
}

/* Form Styles */
.form-events {
  max-width: 500px;
  margin: 0 auto;
  padding: 2rem;
  background-color: white;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.form-group {
  margin-bottom: 1rem;
}

.form-group label {
  display: block;
  margin-bottom: 0.5rem;
}

.form-group input[type="text"],
.form-group input[type="email"],
.form-group input[type="password"] {
  width: 100%;
  padding: 0.5rem;
  border: 1px solid #dee2e6;
  border-radius: 3px;
}

.form-group.checkbox {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.form-group.checkbox label {
  margin: 0;
}

.error {
  border-color: #dc3545 !important;
}

.error-message {
  color: #dc3545;
  font-size: 0.875rem;
  margin-top: 0.25rem;
  display: block;
}

.form-buttons {
  display: flex;
  gap: 1rem;
  margin-top: 1rem;
}

button {
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 3px;
  cursor: pointer;
}

button[type="submit"] {
  background-color: #0066cc;
  color: white;
}

button[type="reset"] {
  background-color: #6c757d;
  color: white;
}

button:hover {
  opacity: 0.9;
}

section {
  margin-bottom: 2rem;
  padding: 1rem;
  border: 1px solid #dee2e6;
  border-radius: 5px;
}

h2 {
  margin-bottom: 1rem;
  color: #333;
}
```

## 6. Expected Output

Start the development server:
```bash
npm start
```

Visit http://localhost:3000 to see:

1. Mouse Events:
   - Mouse position tracking
   - Click counter
   - Event object logging

2. Keyboard Events:
   - Key press detection
   - Input handling

3. Drag Events:
   - Draggable element
   - Position tracking
   - Visual feedback

4. Form Events:
   - Input validation
   - Form submission
   - Error handling
   - Form reset

## 7. Practice Exercises
1. Add double-click handling
2. Implement touch events
3. Create custom event handlers
4. Add form auto-save
5. Implement event delegation

## 8. Verification and Validation
- [ ] Mouse events work
- [ ] Keyboard events work
- [ ] Drag events work
- [ ] Form validation works
- [ ] Error handling works

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| Events not firing | Check event binding |
| Form not validating | Verify validation logic |
| Event bubbling issues | Use stopPropagation |
| State not updating | Check setState usage |

## 10. Additional Resources
- [React Events](https://reactjs.org/docs/handling-events.html)
- [SyntheticEvent](https://reactjs.org/docs/events.html)
- [Form Handling](https://reactjs.org/docs/forms.html)
- [Event Bubbling and Capturing](https://reactjs.org/docs/events.html#event-bubbling)
