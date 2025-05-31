# Experiment 9B: ReactJS - Sharing Data Between Components

## 1. Experiment Information
- **Experiment Number**: 09B
- **Title**: Data Sharing in React Components
- **Duration**: 45 minutes

## 2. Learning Objectives
- Understand component communication
- Use Context API
- Implement prop drilling
- Create global state
- Share data between siblings
- Handle state updates

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- Visual Studio Code
- Web browser

### Knowledge Requirements
- React basics
- React hooks
- Previous experiments completion

## 4. Theory and Concepts
### Data Sharing
- Props
- Context API
- State lifting
- Global state
- Event handling
- Component composition

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create new React project
npx create-react-app data-sharing-demo
cd data-sharing-demo

# Start development server
npm start
```

### 2. Create Components

Create src/context/ThemeContext.js:
```javascript
import React, { createContext, useContext, useState } from 'react';

const ThemeContext = createContext();

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within a ThemeProvider');
  }
  return context;
}
```

Create src/context/UserContext.js:
```javascript
import React, { createContext, useContext, useState } from 'react';

const UserContext = createContext();

export function UserProvider({ children }) {
  const [user, setUser] = useState(null);

  const login = (userData) => {
    setUser(userData);
  };

  const logout = () => {
    setUser(null);
  };

  return (
    <UserContext.Provider value={{ user, login, logout }}>
      {children}
    </UserContext.Provider>
  );
}

export function useUser() {
  const context = useContext(UserContext);
  if (!context) {
    throw new Error('useUser must be used within a UserProvider');
  }
  return context;
}
```

Create src/components/Header.js:
```javascript
import React from 'react';
import { useTheme } from '../context/ThemeContext';
import { useUser } from '../context/UserContext';

function Header() {
  const { theme, toggleTheme } = useTheme();
  const { user, logout } = useUser();

  return (
    <header className={`header ${theme}`}>
      <div className="header-content">
        <h1>Data Sharing Demo</h1>
        <div className="header-controls">
          <button onClick={toggleTheme}>
            Toggle {theme === 'light' ? 'Dark' : 'Light'} Mode
          </button>
          {user && (
            <button onClick={logout} className="logout">
              Logout
            </button>
          )}
        </div>
      </div>
    </header>
  );
}

export default Header;
```

Create src/components/LoginForm.js:
```javascript
import React, { useState } from 'react';
import { useUser } from '../context/UserContext';
import { useTheme } from '../context/ThemeContext';

function LoginForm() {
  const { login } = useUser();
  const { theme } = useTheme();
  const [formData, setFormData] = useState({
    username: '',
    password: ''
  });

  const handleSubmit = (e) => {
    e.preventDefault();
    login({
      username: formData.username,
      role: 'user'
    });
  };

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: value
    }));
  };

  return (
    <div className={`login-form ${theme}`}>
      <h2>Login</h2>
      <form onSubmit={handleSubmit}>
        <div className="form-group">
          <label htmlFor="username">Username:</label>
          <input
            type="text"
            id="username"
            name="username"
            value={formData.username}
            onChange={handleChange}
          />
        </div>
        <div className="form-group">
          <label htmlFor="password">Password:</label>
          <input
            type="password"
            id="password"
            name="password"
            value={formData.password}
            onChange={handleChange}
          />
        </div>
        <button type="submit">Login</button>
      </form>
    </div>
  );
}

export default LoginForm;
```

Create src/components/Dashboard.js:
```javascript
import React from 'react';
import { useUser } from '../context/UserContext';
import { useTheme } from '../context/ThemeContext';
import UserProfile from './UserProfile';
import Settings from './Settings';

function Dashboard() {
  const { user } = useUser();
  const { theme } = useTheme();

  if (!user) {
    return null;
  }

  return (
    <div className={`dashboard ${theme}`}>
      <h2>Welcome, {user.username}!</h2>
      <div className="dashboard-content">
        <UserProfile />
        <Settings />
      </div>
    </div>
  );
}

export default Dashboard;
```

Create src/components/UserProfile.js:
```javascript
import React from 'react';
import { useUser } from '../context/UserContext';
import { useTheme } from '../context/ThemeContext';

function UserProfile() {
  const { user } = useUser();
  const { theme } = useTheme();

  return (
    <div className={`user-profile ${theme}`}>
      <h3>User Profile</h3>
      <div className="profile-info">
        <p>Username: {user.username}</p>
        <p>Role: {user.role}</p>
      </div>
    </div>
  );
}

export default UserProfile;
```

Create src/components/Settings.js:
```javascript
import React from 'react';
import { useTheme } from '../context/ThemeContext';

function Settings() {
  const { theme, toggleTheme } = useTheme();

  return (
    <div className={`settings ${theme}`}>
      <h3>Settings</h3>
      <div className="settings-content">
        <div className="setting-item">
          <label>Theme:</label>
          <button onClick={toggleTheme}>
            {theme === 'light' ? 'Switch to Dark Mode' : 'Switch to Light Mode'}
          </button>
        </div>
      </div>
    </div>
  );
}

export default Settings;
```

### 3. Update App Component

Replace src/App.js:
```javascript
import React from 'react';
import './App.css';
import { ThemeProvider } from './context/ThemeContext';
import { UserProvider } from './context/UserContext';
import Header from './components/Header';
import LoginForm from './components/LoginForm';
import Dashboard from './components/Dashboard';

function App() {
  return (
    <ThemeProvider>
      <UserProvider>
        <div className="App">
          <Header />
          <main>
            <LoginForm />
            <Dashboard />
          </main>
        </div>
      </UserProvider>
    </ThemeProvider>
  );
}

export default App;
```

### 4. Add Styling

Update src/App.css:
```css
/* Theme Variables */
:root {
  --light-bg: #ffffff;
  --light-text: #333333;
  --light-border: #dddddd;
  --dark-bg: #1a1a1a;
  --dark-text: #ffffff;
  --dark-border: #444444;
}

/* Base Styles */
.App {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

/* Theme Styles */
.light {
  background-color: var(--light-bg);
  color: var(--light-text);
}

.dark {
  background-color: var(--dark-bg);
  color: var(--dark-text);
}

/* Header Styles */
.header {
  padding: 1rem;
  margin-bottom: 2rem;
  border-radius: 5px;
}

.header.light {
  background-color: #f8f9fa;
  border: 1px solid var(--light-border);
}

.header.dark {
  background-color: #2d2d2d;
  border: 1px solid var(--dark-border);
}

.header-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.header-controls {
  display: flex;
  gap: 1rem;
}

/* Login Form Styles */
.login-form {
  max-width: 400px;
  margin: 0 auto;
  padding: 2rem;
  border-radius: 5px;
}

.login-form.light {
  background-color: #ffffff;
  border: 1px solid var(--light-border);
}

.login-form.dark {
  background-color: #2d2d2d;
  border: 1px solid var(--dark-border);
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
  border-radius: 3px;
}

.form-group input.light {
  background-color: var(--light-bg);
  border: 1px solid var(--light-border);
  color: var(--light-text);
}

.form-group input.dark {
  background-color: var(--dark-bg);
  border: 1px solid var(--dark-border);
  color: var(--dark-text);
}

/* Dashboard Styles */
.dashboard {
  padding: 2rem;
  border-radius: 5px;
  margin-top: 2rem;
}

.dashboard.light {
  background-color: #ffffff;
  border: 1px solid var(--light-border);
}

.dashboard.dark {
  background-color: #2d2d2d;
  border: 1px solid var(--dark-border);
}

.dashboard-content {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 2rem;
  margin-top: 2rem;
}

/* User Profile Styles */
.user-profile {
  padding: 1.5rem;
  border-radius: 5px;
}

.user-profile.light {
  background-color: #f8f9fa;
  border: 1px solid var(--light-border);
}

.user-profile.dark {
  background-color: #2d2d2d;
  border: 1px solid var(--dark-border);
}

/* Settings Styles */
.settings {
  padding: 1.5rem;
  border-radius: 5px;
}

.settings.light {
  background-color: #f8f9fa;
  border: 1px solid var(--light-border);
}

.settings.dark {
  background-color: #2d2d2d;
  border: 1px solid var(--dark-border);
}

.setting-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}

/* Button Styles */
button {
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 3px;
  cursor: pointer;
  background-color: #0066cc;
  color: white;
}

button:hover {
  background-color: #0052a3;
}

button.logout {
  background-color: #dc3545;
}

button.logout:hover {
  background-color: #c82333;
}

/* Typography */
h1, h2, h3 {
  margin-bottom: 1rem;
}

/* Responsive Design */
@media (max-width: 600px) {
  .dashboard-content {
    grid-template-columns: 1fr;
  }

  .header-content {
    flex-direction: column;
    gap: 1rem;
    text-align: center;
  }
}
```

## 6. Expected Output

Start the development server:
```bash
npm start
```

Visit http://localhost:3000 to see:

1. Theme Context:
   - Light/dark mode toggle
   - Theme persistence
   - Styled components

2. User Context:
   - Login functionality
   - User data sharing
   - Protected content

3. Components:
   - Header with controls
   - Login form
   - Dashboard
   - User profile
   - Settings

4. Features:
   - Context providers
   - Custom hooks
   - Theme switching
   - User authentication
   - Responsive design

## 7. Practice Exercises
1. Add more context providers
2. Implement nested contexts
3. Add local storage
4. Create complex state
5. Add animations

## 8. Verification and Validation
- [ ] Context works
- [ ] Theme switches
- [ ] Login works
- [ ] Data sharing works
- [ ] Components update

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| Context not available | Check provider |
| Updates not showing | Verify context |
| Theme not applying | Check classes |
| State not sharing | Check scope |

## 10. Additional Resources
- [Context API](https://reactjs.org/docs/context.html)
- [useContext Hook](https://reactjs.org/docs/hooks-reference.html#usecontext)
- [Component Composition](https://reactjs.org/docs/composition-vs-inheritance.html)
- [State Management](https://reactjs.org/docs/lifting-state-up.html)
