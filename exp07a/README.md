# Experiment 7A: ReactJS - Conditional Rendering

## 1. Experiment Information
- **Experiment Number**: 07A
- **Title**: Conditional Rendering in React
- **Duration**: 45 minutes

## 2. Learning Objectives
- Understand conditional rendering patterns
- Use if statements with JSX
- Implement ternary operators
- Work with logical && operator
- Handle multiple conditions
- Switch between components

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- Visual Studio Code
- Web browser

### Knowledge Requirements
- JavaScript fundamentals
- React basics
- Previous experiments completion

## 4. Theory and Concepts
### Conditional Rendering
- If statements
- Ternary operators
- Logical && operator
- Switch statements
- Component switching
- Element variables

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create new React project
npx create-react-app conditional-demo
cd conditional-demo

# Start development server
npm start
```

### 2. Create Components

Create src/components/AuthDemo.js:
```javascript
import React, { useState } from 'react';

function AuthDemo() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(false);

  const handleLogin = () => {
    setLoading(true);
    // Simulate API call
    setTimeout(() => {
      setUser({
        name: 'John Doe',
        role: 'admin',
        email: 'john@example.com'
      });
      setIsLoggedIn(true);
      setLoading(false);
    }, 1500);
  };

  const handleLogout = () => {
    setUser(null);
    setIsLoggedIn(false);
  };

  // Using if statement for loading state
  if (loading) {
    return (
      <div className="auth-demo loading">
        <h2>Loading...</h2>
        <div className="spinner"></div>
      </div>
    );
  }

  return (
    <div className="auth-demo">
      <h2>Authentication Demo</h2>

      {/* Using ternary operator */}
      {isLoggedIn ? (
        <div className="user-profile">
          <h3>Welcome, {user.name}!</h3>
          <p>Email: {user.email}</p>
          
          {/* Using logical && operator */}
          {user.role === 'admin' && (
            <div className="admin-panel">
              <h4>Admin Panel</h4>
              <p>You have special privileges</p>
            </div>
          )}
          
          <button onClick={handleLogout}>Logout</button>
        </div>
      ) : (
        <div className="login-prompt">
          <p>Please log in to continue</p>
          <button onClick={handleLogin}>Login</button>
        </div>
      )}
    </div>
  );
}

export default AuthDemo;
```

Create src/components/FeatureFlags.js:
```javascript
import React, { useState } from 'react';

// Feature components
const OldFeature = () => (
  <div className="feature">
    <h3>Old Feature</h3>
    <p>This is the old version of the feature.</p>
  </div>
);

const NewFeature = () => (
  <div className="feature">
    <h3>New Feature</h3>
    <p>This is the new and improved version!</p>
  </div>
);

const BetaFeature = () => (
  <div className="feature beta">
    <h3>Beta Feature</h3>
    <p>This feature is in beta testing.</p>
    <span className="beta-badge">BETA</span>
  </div>
);

function FeatureFlags() {
  const [flags, setFlags] = useState({
    useNewFeature: false,
    showBetaFeatures: false,
    isExperiment: true
  });

  const toggleFlag = (flag) => {
    setFlags(prev => ({
      ...prev,
      [flag]: !prev[flag]
    }));
  };

  return (
    <div className="feature-flags">
      <h2>Feature Flags Demo</h2>

      <div className="controls">
        <label>
          <input
            type="checkbox"
            checked={flags.useNewFeature}
            onChange={() => toggleFlag('useNewFeature')}
          />
          Use New Feature
        </label>

        <label>
          <input
            type="checkbox"
            checked={flags.showBetaFeatures}
            onChange={() => toggleFlag('showBetaFeatures')}
          />
          Show Beta Features
        </label>

        <label>
          <input
            type="checkbox"
            checked={flags.isExperiment}
            onChange={() => toggleFlag('isExperiment')}
          />
          Enable Experiments
        </label>
      </div>

      <div className="features">
        {/* Using ternary operator */}
        {flags.useNewFeature ? <NewFeature /> : <OldFeature />}

        {/* Using logical && operator */}
        {flags.showBetaFeatures && <BetaFeature />}

        {/* Using element variables */}
        {(() => {
          if (flags.isExperiment) {
            return (
              <div className="experiment">
                <h3>Experimental Feature</h3>
                <p>This is an experimental feature.</p>
                <button className="experiment-btn">Try it out</button>
              </div>
            );
          }
          return null;
        })()}
      </div>
    </div>
  );
}

export default FeatureFlags;
```

Create src/components/ContentRenderer.js:
```javascript
import React, { useState } from 'react';

// Content type components
const TextContent = ({ text }) => (
  <div className="content text">
    <p>{text}</p>
  </div>
);

const ImageContent = ({ src, alt }) => (
  <div className="content image">
    <img src={src} alt={alt} />
  </div>
);

const VideoContent = ({ src }) => (
  <div className="content video">
    <video controls>
      <source src={src} type="video/mp4" />
      Your browser does not support video playback.
    </video>
  </div>
);

const ErrorContent = ({ message }) => (
  <div className="content error">
    <p className="error-message">{message}</p>
  </div>
);

function ContentRenderer() {
  const [content, setContent] = useState({
    type: 'text',
    data: {
      text: 'This is a sample text content.',
      imageSrc: 'https://via.placeholder.com/300',
      videoSrc: 'sample.mp4'
    }
  });

  // Switch between content types
  const renderContent = () => {
    switch (content.type) {
      case 'text':
        return <TextContent text={content.data.text} />;
      case 'image':
        return <ImageContent 
          src={content.data.imageSrc} 
          alt="Sample" 
        />;
      case 'video':
        return <VideoContent src={content.data.videoSrc} />;
      default:
        return <ErrorContent message="Unknown content type" />;
    }
  };

  return (
    <div className="content-renderer">
      <h2>Content Renderer</h2>

      <div className="content-controls">
        <button 
          onClick={() => setContent(prev => ({ ...prev, type: 'text' }))}
          className={content.type === 'text' ? 'active' : ''}
        >
          Text
        </button>
        <button 
          onClick={() => setContent(prev => ({ ...prev, type: 'image' }))}
          className={content.type === 'image' ? 'active' : ''}
        >
          Image
        </button>
        <button 
          onClick={() => setContent(prev => ({ ...prev, type: 'video' }))}
          className={content.type === 'video' ? 'active' : ''}
        >
          Video
        </button>
      </div>

      <div className="content-display">
        {renderContent()}
      </div>
    </div>
  );
}

export default ContentRenderer;
```

### 3. Update App Component

Replace src/App.js:
```javascript
import React from 'react';
import './App.css';
import AuthDemo from './components/AuthDemo';
import FeatureFlags from './components/FeatureFlags';
import ContentRenderer from './components/ContentRenderer';

function App() {
  return (
    <div className="App">
      <header>
        <h1>Conditional Rendering in React</h1>
        <p>Exploring different conditional rendering patterns</p>
      </header>

      <main>
        <AuthDemo />
        <FeatureFlags />
        <ContentRenderer />
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

/* Auth Demo Styles */
.auth-demo {
  margin-bottom: 2rem;
  padding: 1rem;
  border: 1px solid #ddd;
  border-radius: 5px;
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

.admin-panel {
  margin-top: 1rem;
  padding: 1rem;
  background-color: #f8f9fa;
  border-radius: 5px;
}

/* Feature Flags Styles */
.feature-flags {
  margin-bottom: 2rem;
  padding: 1rem;
  border: 1px solid #ddd;
  border-radius: 5px;
}

.controls {
  display: flex;
  gap: 1rem;
  margin-bottom: 1rem;
}

.feature {
  padding: 1rem;
  margin-bottom: 1rem;
  background-color: #f8f9fa;
  border-radius: 5px;
}

.beta {
  border: 2px solid #f1c40f;
  position: relative;
}

.beta-badge {
  position: absolute;
  top: -10px;
  right: -10px;
  background-color: #f1c40f;
  color: white;
  padding: 0.25rem 0.5rem;
  border-radius: 3px;
  font-size: 0.8rem;
}

.experiment {
  padding: 1rem;
  background-color: #e8f4fd;
  border-radius: 5px;
  margin-top: 1rem;
}

/* Content Renderer Styles */
.content-renderer {
  margin-bottom: 2rem;
  padding: 1rem;
  border: 1px solid #ddd;
  border-radius: 5px;
}

.content-controls {
  display: flex;
  gap: 1rem;
  margin-bottom: 1rem;
}

.content-controls button {
  padding: 0.5rem 1rem;
  border: 1px solid #ddd;
  background: white;
  border-radius: 3px;
  cursor: pointer;
}

.content-controls button.active {
  background-color: #0066cc;
  color: white;
  border-color: #0066cc;
}

.content {
  padding: 1rem;
  background-color: #f8f9fa;
  border-radius: 5px;
}

.content.error {
  background-color: #fee;
  color: #e74c3c;
}

.content img,
.content video {
  max-width: 100%;
  height: auto;
}

/* Common Styles */
button {
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 3px;
  background-color: #0066cc;
  color: white;
  cursor: pointer;
}

button:hover {
  background-color: #0052a3;
}

h2 {
  margin-bottom: 1rem;
}

h3 {
  margin-bottom: 0.5rem;
}
```

## 6. Expected Output

Start the development server:
```bash
npm start
```

Visit http://localhost:3000 to see:

1. Authentication Demo:
   - Login/Logout states
   - Loading spinner
   - Admin panel (conditional)

2. Feature Flags:
   - Toggle between features
   - Beta features
   - Experimental features

3. Content Renderer:
   - Switch between content types
   - Different content displays
   - Error handling

## 7. Practice Exercises
1. Add more authentication states
2. Create nested conditions
3. Implement role-based rendering
4. Add loading states
5. Create a theme switcher

## 8. Verification and Validation
- [ ] All conditions work
- [ ] Components render correctly
- [ ] State updates properly
- [ ] Loading states work
- [ ] Error handling works

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| Component not rendering | Check conditions |
| Multiple renders | Verify state updates |
| Infinite loops | Check dependencies |
| State not updating | Verify setState calls |

## 10. Additional Resources
- [Conditional Rendering](https://reactjs.org/docs/conditional-rendering.html)
- [React State and Lifecycle](https://reactjs.org/docs/state-and-lifecycle.html)
- [Handling Events](https://reactjs.org/docs/handling-events.html)
- [React Patterns](https://reactpatterns.com/)
