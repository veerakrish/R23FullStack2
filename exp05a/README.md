# Experiment 5A: ReactJS - Rendering HTML

## 1. Experiment Information
- **Experiment Number**: 05A
- **Title**: Rendering HTML with React
- **Duration**: 45 minutes

## 2. Learning Objectives
- Set up a React development environment
- Understand React DOM rendering
- Learn basic HTML rendering in React
- Use different HTML elements in React
- Handle dynamic content rendering

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- Visual Studio Code
- Web browser

### Knowledge Requirements
- Basic HTML and CSS
- JavaScript fundamentals
- ES6+ features
- DOM concepts

## 4. Theory and Concepts
### React Rendering
- Virtual DOM
- React DOM
- Root element
- Render method
- React elements

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create new React project
npx create-react-app react-html-demo
cd react-html-demo

# Start development server
npm start
```

### 2. Basic HTML Rendering

Replace src/App.js:
```javascript
import React from 'react';
import './App.css';

function App() {
  return (
    <div className="App">
      <header>
        <h1>Welcome to React</h1>
        <p>Learning HTML rendering in React</p>
      </header>
      
      <main>
        {/* Basic HTML Elements */}
        <section className="basic-elements">
          <h2>Basic HTML Elements</h2>
          <p>This is a paragraph element</p>
          <button>This is a button</button>
          <br />
          <a href="https://reactjs.org">Visit React Website</a>
        </section>

        {/* Lists */}
        <section className="lists">
          <h2>Lists in React</h2>
          <h3>Unordered List:</h3>
          <ul>
            <li>Item 1</li>
            <li>Item 2</li>
            <li>Item 3</li>
          </ul>

          <h3>Ordered List:</h3>
          <ol>
            <li>First Item</li>
            <li>Second Item</li>
            <li>Third Item</li>
          </ol>
        </section>

        {/* Forms */}
        <section className="forms">
          <h2>Form Elements</h2>
          <form onSubmit={(e) => e.preventDefault()}>
            <div>
              <label htmlFor="name">Name:</label>
              <input type="text" id="name" name="name" />
            </div>
            <div>
              <label htmlFor="email">Email:</label>
              <input type="email" id="email" name="email" />
            </div>
            <div>
              <label htmlFor="message">Message:</label>
              <textarea id="message" name="message"></textarea>
            </div>
            <div>
              <label>
                <input type="checkbox" name="subscribe" />
                Subscribe to newsletter
              </label>
            </div>
            <button type="submit">Submit</button>
          </form>
        </section>

        {/* Tables */}
        <section className="tables">
          <h2>Table Example</h2>
          <table>
            <thead>
              <tr>
                <th>Header 1</th>
                <th>Header 2</th>
                <th>Header 3</th>
              </tr>
            </thead>
            <tbody>
              <tr>
                <td>Row 1, Cell 1</td>
                <td>Row 1, Cell 2</td>
                <td>Row 1, Cell 3</td>
              </tr>
              <tr>
                <td>Row 2, Cell 1</td>
                <td>Row 2, Cell 2</td>
                <td>Row 2, Cell 3</td>
              </tr>
            </tbody>
          </table>
        </section>

        {/* Media Elements */}
        <section className="media">
          <h2>Media Elements</h2>
          <img 
            src="https://via.placeholder.com/150" 
            alt="Placeholder"
          />
          <video width="320" height="240" controls>
            <source src="movie.mp4" type="video/mp4" />
            Your browser does not support the video tag.
          </video>
        </section>
      </main>

      <footer>
        <p>&copy; 2025 React HTML Demo</p>
      </footer>
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

h1 {
  color: #333;
}

h2 {
  color: #666;
  margin-bottom: 1rem;
}

/* Form Styles */
form {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

form div {
  display: flex;
  flex-direction: column;
}

label {
  margin-bottom: 0.5rem;
}

input[type="text"],
input[type="email"],
textarea {
  padding: 0.5rem;
  border: 1px solid #ddd;
  border-radius: 3px;
  width: 100%;
  max-width: 300px;
}

button {
  padding: 0.5rem 1rem;
  background-color: #0066cc;
  color: white;
  border: none;
  border-radius: 3px;
  cursor: pointer;
  width: fit-content;
}

button:hover {
  background-color: #0052a3;
}

/* Table Styles */
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 1rem;
}

th, td {
  border: 1px solid #ddd;
  padding: 0.5rem;
  text-align: left;
}

th {
  background-color: #f0f0f0;
}

/* Media Styles */
.media {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

img, video {
  max-width: 100%;
  height: auto;
}

footer {
  text-align: center;
  margin-top: 2rem;
  padding: 1rem;
  background-color: #f0f0f0;
  border-radius: 5px;
}
```

### 4. Update Index File

Update src/index.js:
```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

## 6. Expected Output

Start the development server:
```bash
npm start
```

Visit http://localhost:3000 to see:

1. Header section with title
2. Basic HTML elements section
3. List examples (ordered and unordered)
4. Form elements with styling
5. Table with sample data
6. Media elements section
7. Footer with copyright

## 7. Practice Exercises
1. Add more HTML5 semantic elements (article, aside, nav)
2. Create a responsive navigation menu
3. Add form validation
4. Include different input types
5. Create a photo gallery grid

## 8. Verification and Validation
- [ ] All HTML elements render correctly
- [ ] Styling is applied properly
- [ ] Form elements are interactive
- [ ] Media elements display correctly
- [ ] Layout is responsive

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| Elements not rendering | Check JSX syntax |
| Styles not applying | Verify CSS imports |
| Media not loading | Check source URLs |
| Form not working | Verify event handlers |

## 10. Additional Resources
- [React Documentation](https://reactjs.org/docs/getting-started.html)
- [HTML in JSX](https://reactjs.org/docs/dom-elements.html)
- [React Forms](https://reactjs.org/docs/forms.html)
- [Styling in React](https://reactjs.org/docs/faq-styling.html)
