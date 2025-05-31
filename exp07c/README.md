# Experiment 7C: ReactJS - Working with Forms

## 1. Experiment Information
- **Experiment Number**: 07C
- **Title**: Working with Different Form Fields
- **Duration**: 45 minutes

## 2. Learning Objectives
- Understand form handling in React
- Work with different form fields
- Implement form validation
- Handle form submission
- Create dynamic forms
- Use form libraries

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- Visual Studio Code
- Web browser

### Knowledge Requirements
- HTML forms
- JavaScript events
- React basics
- Previous experiments completion

## 4. Theory and Concepts
### React Forms
- Controlled components
- Uncontrolled components
- Form validation
- Form submission
- Dynamic forms
- Form state management

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create new React project
npx create-react-app form-demo
cd form-demo

# Start development server
npm start
```

### 2. Create Components

Create src/components/RegistrationForm.js:
```javascript
import React, { useState } from 'react';

function RegistrationForm() {
  const [formData, setFormData] = useState({
    username: '',
    email: '',
    password: '',
    confirmPassword: '',
    age: '',
    gender: 'prefer-not-to-say',
    interests: [],
    bio: '',
    newsletter: false,
    role: 'student'
  });

  const [errors, setErrors] = useState({});
  const [submitted, setSubmitted] = useState(false);

  const interests = [
    'Programming',
    'Web Development',
    'Mobile Development',
    'AI/ML',
    'DevOps',
    'UI/UX Design'
  ];

  // Handle input changes
  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    
    setFormData(prev => ({
      ...prev,
      [name]: type === 'checkbox' 
        ? checked 
        : type === 'select-multiple'
          ? Array.from(e.target.selectedOptions, option => option.value)
          : value
    }));

    // Clear error when user starts typing
    if (errors[name]) {
      setErrors(prev => ({
        ...prev,
        [name]: ''
      }));
    }
  };

  // Validate form
  const validateForm = () => {
    const newErrors = {};

    // Username validation
    if (!formData.username.trim()) {
      newErrors.username = 'Username is required';
    } else if (formData.username.length < 3) {
      newErrors.username = 'Username must be at least 3 characters';
    }

    // Email validation
    if (!formData.email) {
      newErrors.email = 'Email is required';
    } else if (!/\S+@\S+\.\S+/.test(formData.email)) {
      newErrors.email = 'Email is invalid';
    }

    // Password validation
    if (!formData.password) {
      newErrors.password = 'Password is required';
    } else if (formData.password.length < 6) {
      newErrors.password = 'Password must be at least 6 characters';
    }

    // Confirm password
    if (formData.password !== formData.confirmPassword) {
      newErrors.confirmPassword = 'Passwords do not match';
    }

    // Age validation
    if (formData.age && (isNaN(formData.age) || +formData.age < 13)) {
      newErrors.age = 'Age must be at least 13';
    }

    // Interests validation
    if (formData.interests.length === 0) {
      newErrors.interests = 'Select at least one interest';
    }

    return newErrors;
  };

  // Handle form submission
  const handleSubmit = (e) => {
    e.preventDefault();
    
    const newErrors = validateForm();
    if (Object.keys(newErrors).length > 0) {
      setErrors(newErrors);
      return;
    }

    // Form is valid, process it
    console.log('Form submitted:', formData);
    setSubmitted(true);
  };

  if (submitted) {
    return (
      <div className="success-message">
        <h3>Registration Successful!</h3>
        <p>Thank you for registering, {formData.username}!</p>
        <button onClick={() => {
          setFormData({
            username: '',
            email: '',
            password: '',
            confirmPassword: '',
            age: '',
            gender: 'prefer-not-to-say',
            interests: [],
            bio: '',
            newsletter: false,
            role: 'student'
          });
          setSubmitted(false);
        }}>
          Register Another
        </button>
      </div>
    );
  }

  return (
    <div className="registration-form">
      <h2>Registration Form</h2>
      <form onSubmit={handleSubmit}>
        {/* Text Input */}
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

        {/* Email Input */}
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

        {/* Password Input */}
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

        {/* Confirm Password */}
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

        {/* Number Input */}
        <div className="form-group">
          <label htmlFor="age">Age:</label>
          <input
            type="number"
            id="age"
            name="age"
            value={formData.age}
            onChange={handleChange}
            className={errors.age ? 'error' : ''}
          />
          {errors.age && (
            <span className="error-message">{errors.age}</span>
          )}
        </div>

        {/* Radio Buttons */}
        <div className="form-group">
          <label>Gender:</label>
          <div className="radio-group">
            <label>
              <input
                type="radio"
                name="gender"
                value="male"
                checked={formData.gender === 'male'}
                onChange={handleChange}
              />
              Male
            </label>
            <label>
              <input
                type="radio"
                name="gender"
                value="female"
                checked={formData.gender === 'female'}
                onChange={handleChange}
              />
              Female
            </label>
            <label>
              <input
                type="radio"
                name="gender"
                value="prefer-not-to-say"
                checked={formData.gender === 'prefer-not-to-say'}
                onChange={handleChange}
              />
              Prefer not to say
            </label>
          </div>
        </div>

        {/* Multiple Select */}
        <div className="form-group">
          <label htmlFor="interests">Interests:</label>
          <select
            multiple
            id="interests"
            name="interests"
            value={formData.interests}
            onChange={handleChange}
            className={errors.interests ? 'error' : ''}
          >
            {interests.map(interest => (
              <option key={interest} value={interest}>
                {interest}
              </option>
            ))}
          </select>
          {errors.interests && (
            <span className="error-message">{errors.interests}</span>
          )}
          <small>Hold Ctrl/Cmd to select multiple</small>
        </div>

        {/* Textarea */}
        <div className="form-group">
          <label htmlFor="bio">Bio:</label>
          <textarea
            id="bio"
            name="bio"
            value={formData.bio}
            onChange={handleChange}
            rows="4"
          />
        </div>

        {/* Select Dropdown */}
        <div className="form-group">
          <label htmlFor="role">Role:</label>
          <select
            id="role"
            name="role"
            value={formData.role}
            onChange={handleChange}
          >
            <option value="student">Student</option>
            <option value="professional">Professional</option>
            <option value="educator">Educator</option>
          </select>
        </div>

        {/* Checkbox */}
        <div className="form-group checkbox">
          <label>
            <input
              type="checkbox"
              name="newsletter"
              checked={formData.newsletter}
              onChange={handleChange}
            />
            Subscribe to newsletter
          </label>
        </div>

        {/* Submit Button */}
        <div className="form-buttons">
          <button type="submit">Register</button>
          <button
            type="button"
            onClick={() => {
              setFormData({
                username: '',
                email: '',
                password: '',
                confirmPassword: '',
                age: '',
                gender: 'prefer-not-to-say',
                interests: [],
                bio: '',
                newsletter: false,
                role: 'student'
              });
              setErrors({});
            }}
          >
            Reset
          </button>
        </div>
      </form>
    </div>
  );
}

export default RegistrationForm;
```

### 3. Update App Component

Replace src/App.js:
```javascript
import React from 'react';
import './App.css';
import RegistrationForm from './components/RegistrationForm';

function App() {
  return (
    <div className="App">
      <header>
        <h1>React Forms</h1>
        <p>Working with different form fields</p>
      </header>

      <main>
        <RegistrationForm />
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

/* Registration Form Styles */
.registration-form {
  background: white;
  padding: 2rem;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.form-group {
  margin-bottom: 1.5rem;
}

.form-group label {
  display: block;
  margin-bottom: 0.5rem;
  font-weight: bold;
}

.form-group input[type="text"],
.form-group input[type="email"],
.form-group input[type="password"],
.form-group input[type="number"],
.form-group select,
.form-group textarea {
  width: 100%;
  padding: 0.5rem;
  border: 1px solid #ddd;
  border-radius: 3px;
  font-size: 1rem;
}

.form-group textarea {
  resize: vertical;
  min-height: 100px;
}

.form-group select[multiple] {
  height: 150px;
}

.radio-group {
  display: flex;
  gap: 1rem;
}

.radio-group label {
  font-weight: normal;
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.checkbox label {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  font-weight: normal;
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
  margin-top: 2rem;
}

button {
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 3px;
  cursor: pointer;
  font-size: 1rem;
}

button[type="submit"] {
  background-color: #0066cc;
  color: white;
}

button[type="button"] {
  background-color: #6c757d;
  color: white;
}

button:hover {
  opacity: 0.9;
}

small {
  color: #6c757d;
  display: block;
  margin-top: 0.25rem;
}

/* Success Message Styles */
.success-message {
  text-align: center;
  padding: 2rem;
  background-color: #d4edda;
  border-radius: 5px;
  margin-bottom: 2rem;
}

.success-message h3 {
  color: #155724;
  margin-bottom: 1rem;
}

.success-message button {
  background-color: #28a745;
  color: white;
  margin-top: 1rem;
}
```

## 6. Expected Output

Start the development server:
```bash
npm start
```

Visit http://localhost:3000 to see:

1. Registration form with:
   - Text inputs
   - Email input
   - Password fields
   - Number input
   - Radio buttons
   - Multiple select
   - Textarea
   - Select dropdown
   - Checkbox
   - Submit/Reset buttons

2. Features:
   - Form validation
   - Error messages
   - Success message
   - Form reset
   - Multiple field types
   - Responsive design

## 7. Practice Exercises
1. Add file upload
2. Create dynamic form fields
3. Implement form wizard
4. Add form persistence
5. Create custom form controls

## 8. Verification and Validation
- [ ] All fields work correctly
- [ ] Validation works
- [ ] Error messages show
- [ ] Form submits properly
- [ ] Reset works correctly

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| Form not submitting | Check event handler |
| Validation not working | Verify rules |
| Fields not updating | Check onChange |
| Reset not working | Verify state reset |

## 10. Additional Resources
- [React Forms](https://reactjs.org/docs/forms.html)
- [Form Validation](https://reactjs.org/docs/forms.html#validation)
- [Formik](https://formik.org/)
- [React Hook Form](https://react-hook-form.com/)
