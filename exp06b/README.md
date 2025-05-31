# Experiment 6B: ReactJS - CSS and Sass Styling

## 1. Experiment Information
- **Experiment Number**: 06B
- **Title**: Adding Styles with CSS and Sass
- **Duration**: 45 minutes

## 2. Learning Objectives
- Use different styling approaches in React
- Implement CSS modules
- Work with Sass/SCSS
- Create responsive layouts
- Use CSS-in-JS
- Apply styled-components

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- Visual Studio Code
- Web browser

### Knowledge Requirements
- CSS fundamentals
- Sass basics
- React components
- Previous experiments completion

## 4. Theory and Concepts
### Styling in React
- CSS Modules
- Sass/SCSS
- Styled Components
- CSS-in-JS
- Responsive design
- Theme management

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create new React project
npx create-react-app styling-demo
cd styling-demo

# Install dependencies
npm install node-sass styled-components

# Start development server
npm start
```

### 2. Create Components and Styles

Create src/components/Card/Card.module.scss:
```scss
$primary-color: #0066cc;
$secondary-color: #f0f0f0;
$border-radius: 5px;

.card {
  background: white;
  border-radius: $border-radius;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  padding: 1rem;
  margin: 1rem;
  transition: transform 0.2s;

  &:hover {
    transform: translateY(-5px);
  }

  .header {
    border-bottom: 1px solid $secondary-color;
    margin-bottom: 1rem;
    padding-bottom: 1rem;

    h3 {
      margin: 0;
      color: $primary-color;
    }
  }

  .content {
    color: #333;
    line-height: 1.6;
  }

  .footer {
    margin-top: 1rem;
    padding-top: 1rem;
    border-top: 1px solid $secondary-color;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
}
```

Create src/components/Card/Card.js:
```javascript
import React from 'react';
import styles from './Card.module.scss';

function Card({ title, content, footer }) {
  return (
    <div className={styles.card}>
      <div className={styles.header}>
        <h3>{title}</h3>
      </div>
      <div className={styles.content}>
        {content}
      </div>
      {footer && (
        <div className={styles.footer}>
          {footer}
        </div>
      )}
    </div>
  );
}

export default Card;
```

Create src/components/Button/Button.js:
```javascript
import styled from 'styled-components';

const Button = styled.button`
  padding: ${props => props.small ? '0.5rem 1rem' : '0.75rem 1.5rem'};
  background-color: ${props => props.variant === 'secondary' ? '#666' : '#0066cc'};
  color: white;
  border: none;
  border-radius: 3px;
  cursor: pointer;
  font-size: ${props => props.small ? '0.9rem' : '1rem'};
  transition: background-color 0.2s;

  &:hover {
    background-color: ${props => props.variant === 'secondary' ? '#555' : '#0052a3'};
  }

  &:disabled {
    background-color: #ccc;
    cursor: not-allowed;
  }
`;

export default Button;
```

Create src/components/Grid/Grid.scss:
```scss
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 1rem;
  padding: 1rem;

  @media (max-width: 768px) {
    grid-template-columns: 1fr;
  }
}
```

Create src/components/Grid/Grid.js:
```javascript
import React from 'react';
import './Grid.scss';

function Grid({ children }) {
  return (
    <div className="grid">
      {children}
    </div>
  );
}

export default Grid;
```

Create src/styles/theme.js:
```javascript
export const theme = {
  colors: {
    primary: '#0066cc',
    secondary: '#666666',
    success: '#2ecc71',
    danger: '#e74c3c',
    warning: '#f1c40f',
    info: '#3498db',
    light: '#f8f9fa',
    dark: '#343a40'
  },
  spacing: {
    xs: '0.25rem',
    sm: '0.5rem',
    md: '1rem',
    lg: '1.5rem',
    xl: '2rem'
  },
  breakpoints: {
    xs: '0px',
    sm: '576px',
    md: '768px',
    lg: '992px',
    xl: '1200px'
  }
};
```

Create src/styles/global.scss:
```scss
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;500;700&display=swap');

:root {
  --primary-color: #0066cc;
  --secondary-color: #666666;
  --success-color: #2ecc71;
  --danger-color: #e74c3c;
  --warning-color: #f1c40f;
  --info-color: #3498db;
  --light-color: #f8f9fa;
  --dark-color: #343a40;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Roboto', sans-serif;
  line-height: 1.6;
  background-color: var(--light-color);
  color: var(--dark-color);
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 1rem;
}

@media (max-width: 768px) {
  .container {
    padding: 0 0.5rem;
  }
}
```

### 3. Update App Component

Replace src/App.js:
```javascript
import React from 'react';
import { ThemeProvider } from 'styled-components';
import Card from './components/Card/Card';
import Button from './components/Button/Button';
import Grid from './components/Grid/Grid';
import { theme } from './styles/theme';
import './styles/global.scss';

function App() {
  return (
    <ThemeProvider theme={theme}>
      <div className="App">
        <header className="header">
          <h1>React Styling Demo</h1>
          <p>Exploring different styling approaches</p>
        </header>

        <main className="container">
          <Grid>
            {/* CSS Modules Example */}
            <Card
              title="CSS Modules"
              content="This card is styled using CSS Modules with Sass"
              footer={
                <Button>Learn More</Button>
              }
            />

            {/* Styled Components Example */}
            <Card
              title="Styled Components"
              content="This card uses styled-components for the button"
              footer={
                <Button variant="secondary" small>
                  Details
                </Button>
              }
            />

            {/* Sass Example */}
            <Card
              title="Sass Styling"
              content="This grid layout is styled using Sass"
              footer={
                <div>
                  <Button small>Accept</Button>
                  <Button variant="secondary" small>
                    Decline
                  </Button>
                </div>
              }
            />
          </Grid>
        </main>
      </div>
    </ThemeProvider>
  );
}

export default App;
```

### 4. Add App Styling

Create src/App.scss:
```scss
.App {
  min-height: 100vh;
  
  .header {
    text-align: center;
    padding: 2rem 1rem;
    background: linear-gradient(to right, var(--primary-color), var(--info-color));
    color: white;
    margin-bottom: 2rem;

    h1 {
      margin-bottom: 0.5rem;
      font-size: 2.5rem;

      @media (max-width: 768px) {
        font-size: 2rem;
      }
    }

    p {
      font-size: 1.2rem;
      opacity: 0.9;
    }
  }
}
```

## 6. Expected Output

Start the development server:
```bash
npm start
```

Visit http://localhost:3000 to see:

1. Styled header with gradient background
2. Grid of cards with:
   - CSS Modules styling
   - Styled Components buttons
   - Sass-based layout
3. Responsive design
4. Hover effects
5. Theme consistency

## 7. Practice Exercises
1. Add dark/light theme toggle
2. Create animated components
3. Implement CSS Grid layouts
4. Add custom form styling
5. Create a responsive navbar

## 8. Verification and Validation
- [ ] Styles apply correctly
- [ ] Responsive design works
- [ ] Theme is consistent
- [ ] Animations work
- [ ] CSS Modules scope properly

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| Styles not applying | Check import paths |
| SCSS not compiling | Verify node-sass |
| CSS Modules not working | Check webpack config |
| Theme not working | Check provider setup |

## 10. Additional Resources
- [CSS Modules](https://github.com/css-modules/css-modules)
- [Styled Components](https://styled-components.com/)
- [Sass Documentation](https://sass-lang.com/documentation)
- [React Styling Best Practices](https://reactjs.org/docs/faq-styling.html)
