# Experiment 2B: ExpressJS - Form Data Handling

## 1. Experiment Information
- **Experiment Number**: 02B
- **Title**: Working with Form Data
- **Duration**: 1 hour

## 2. Learning Objectives
- Handle HTML form submissions
- Process different form data types
- Implement file uploads
- Validate form input
- Display form errors

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- Visual Studio Code
- Web browser

### Knowledge Requirements
- Basic JavaScript
- HTML forms
- Express basics
- EJS templating (Experiment 2A)

## 4. Theory and Concepts
### Form Handling
- Form methods (GET/POST)
- Form data parsing
- File uploads
- Input validation
- Error handling

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create project directory
mkdir express-form-demo
cd express-form-demo

# Initialize project
npm init -y

# Install dependencies
npm install express ejs multer express-validator

# Create directories
mkdir views
mkdir public
mkdir public/css
mkdir uploads
```

### 2. Create Server

Create index.js:
```javascript
const express = require('express');
const multer = require('multer');
const { body, validationResult } = require('express-validator');
const path = require('path');
const app = express();
const port = 3000;

// Configure multer for file uploads
const storage = multer.diskStorage({
    destination: './uploads/',
    filename: function(req, file, cb) {
        cb(null, file.fieldname + '-' + Date.now() + 
            path.extname(file.originalname));
    }
});

const upload = multer({
    storage: storage,
    limits: { fileSize: 1000000 }, // 1MB limit
    fileFilter: function(req, file, cb) {
        checkFileType(file, cb);
    }
});

// Check file type
function checkFileType(file, cb) {
    const filetypes = /jpeg|jpg|png|gif/;
    const extname = filetypes.test(path.extname(file.originalname).toLowerCase());
    const mimetype = filetypes.test(file.mimetype);

    if (mimetype && extname) {
        return cb(null, true);
    } else {
        cb('Error: Images Only!');
    }
}

// Middleware
app.use(express.urlencoded({ extended: true }));
app.use(express.static('public'));
app.use('/uploads', express.static('uploads'));

// Set EJS as templating engine
app.set('view engine', 'ejs');

// Routes
app.get('/', (req, res) => {
    res.render('index', { errors: [] });
});

// Basic form handling
app.post('/submit-basic', [
    body('name').notEmpty().trim().escape(),
    body('email').isEmail().normalizeEmail(),
    body('message').notEmpty().trim().escape()
], (req, res) => {
    const errors = validationResult(req);
    
    if (!errors.isEmpty()) {
        return res.render('index', { 
            errors: errors.array(),
            formData: req.body
        });
    }

    res.render('success', { 
        title: 'Basic Form',
        data: req.body 
    });
});

// Advanced form with file upload
app.post('/submit-advanced', upload.single('avatar'), [
    body('username').notEmpty().trim().escape(),
    body('password').isLength({ min: 6 }),
    body('birthdate').isDate(),
    body('interests').isArray()
], (req, res) => {
    const errors = validationResult(req);
    
    if (!errors.isEmpty()) {
        return res.render('index', { 
            errors: errors.array(),
            formData: req.body
        });
    }

    const formData = {
        ...req.body,
        avatar: req.file ? `/uploads/${req.file.filename}` : null
    };

    res.render('success', { 
        title: 'Advanced Form',
        data: formData 
    });
});

// Start server
app.listen(port, () => {
    console.log(`Server running at http://localhost:${port}`);
});
```

### 3. Create Views

Create views/index.ejs:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Form Handling Demo</title>
    <link rel="stylesheet" href="/css/style.css">
</head>
<body>
    <div class="container">
        <% if (errors.length > 0) { %>
            <div class="errors">
                <% errors.forEach(error => { %>
                    <p class="error"><%= error.msg %></p>
                <% }); %>
            </div>
        <% } %>

        <section class="form-section">
            <h2>Basic Contact Form</h2>
            <form action="/submit-basic" method="POST" class="basic-form">
                <div class="form-group">
                    <label for="name">Name:</label>
                    <input type="text" id="name" name="name" 
                           value="<%= locals.formData ? formData.name : '' %>">
                </div>
                
                <div class="form-group">
                    <label for="email">Email:</label>
                    <input type="email" id="email" name="email"
                           value="<%= locals.formData ? formData.email : '' %>">
                </div>
                
                <div class="form-group">
                    <label for="message">Message:</label>
                    <textarea id="message" name="message" rows="4"><%= locals.formData ? formData.message : '' %></textarea>
                </div>
                
                <button type="submit">Submit</button>
            </form>
        </section>

        <section class="form-section">
            <h2>Advanced Form with File Upload</h2>
            <form action="/submit-advanced" method="POST" 
                  enctype="multipart/form-data" class="advanced-form">
                <div class="form-group">
                    <label for="username">Username:</label>
                    <input type="text" id="username" name="username"
                           value="<%= locals.formData ? formData.username : '' %>">
                </div>
                
                <div class="form-group">
                    <label for="password">Password:</label>
                    <input type="password" id="password" name="password">
                </div>
                
                <div class="form-group">
                    <label for="birthdate">Birth Date:</label>
                    <input type="date" id="birthdate" name="birthdate"
                           value="<%= locals.formData ? formData.birthdate : '' %>">
                </div>
                
                <div class="form-group">
                    <label>Interests:</label>
                    <div class="checkbox-group">
                        <label>
                            <input type="checkbox" name="interests" value="coding"
                                <%= locals.formData && formData.interests?.includes('coding') ? 'checked' : '' %>>
                            Coding
                        </label>
                        <label>
                            <input type="checkbox" name="interests" value="reading"
                                <%= locals.formData && formData.interests?.includes('reading') ? 'checked' : '' %>>
                            Reading
                        </label>
                        <label>
                            <input type="checkbox" name="interests" value="gaming"
                                <%= locals.formData && formData.interests?.includes('gaming') ? 'checked' : '' %>>
                            Gaming
                        </label>
                    </div>
                </div>
                
                <div class="form-group">
                    <label for="avatar">Profile Picture:</label>
                    <input type="file" id="avatar" name="avatar" accept="image/*">
                </div>
                
                <button type="submit">Submit</button>
            </form>
        </section>
    </div>
</body>
</html>
```

Create views/success.ejs:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Success</title>
    <link rel="stylesheet" href="/css/style.css">
</head>
<body>
    <div class="container">
        <div class="success-message">
            <h1>Form Submitted Successfully!</h1>
            <h2><%= title %> Data:</h2>
            
            <div class="data-display">
                <% Object.entries(data).forEach(([key, value]) => { %>
                    <% if (key === 'avatar' && value) { %>
                        <div class="data-row">
                            <strong>Profile Picture:</strong>
                            <img src="<%= value %>" alt="Profile" class="avatar-preview">
                        </div>
                    <% } else { %>
                        <div class="data-row">
                            <strong><%= key %>:</strong>
                            <span><%= Array.isArray(value) ? value.join(', ') : value %></span>
                        </div>
                    <% } %>
                <% }); %>
            </div>
            
            <a href="/" class="btn">Back to Forms</a>
        </div>
    </div>
</body>
</html>
```

### 4. Add Styling

Create public/css/style.css:
```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: Arial, sans-serif;
    line-height: 1.6;
    background: #f4f4f4;
}

.container {
    max-width: 800px;
    margin: 2rem auto;
    padding: 20px;
}

.form-section {
    background: white;
    padding: 2rem;
    margin-bottom: 2rem;
    border-radius: 5px;
    box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

.form-section h2 {
    margin-bottom: 1.5rem;
    color: #333;
}

.form-group {
    margin-bottom: 1rem;
}

label {
    display: block;
    margin-bottom: 0.5rem;
    color: #666;
}

input[type="text"],
input[type="email"],
input[type="password"],
input[type="date"],
textarea {
    width: 100%;
    padding: 0.5rem;
    border: 1px solid #ddd;
    border-radius: 3px;
}

.checkbox-group {
    display: flex;
    gap: 1rem;
}

.checkbox-group label {
    display: flex;
    align-items: center;
    gap: 0.5rem;
}

button {
    background: #3498db;
    color: white;
    padding: 0.7rem 1.5rem;
    border: none;
    border-radius: 3px;
    cursor: pointer;
}

button:hover {
    background: #2980b9;
}

.errors {
    background: #ff6b6b;
    color: white;
    padding: 1rem;
    margin-bottom: 1rem;
    border-radius: 3px;
}

.error {
    margin: 0.3rem 0;
}

.success-message {
    background: white;
    padding: 2rem;
    border-radius: 5px;
    box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

.data-display {
    margin: 2rem 0;
}

.data-row {
    display: flex;
    padding: 0.5rem 0;
    border-bottom: 1px solid #eee;
}

.data-row strong {
    width: 150px;
    color: #666;
}

.avatar-preview {
    max-width: 200px;
    border-radius: 5px;
}

.btn {
    display: inline-block;
    background: #3498db;
    color: white;
    padding: 0.7rem 1.5rem;
    text-decoration: none;
    border-radius: 3px;
}

.btn:hover {
    background: #2980b9;
}
```

## 6. Expected Output

Start the server:
```bash
node index.js
```

Visit http://localhost:3000 and test:

1. Basic Contact Form:
- Fill in name, email, and message
- Submit and see validation
- View submitted data on success page

2. Advanced Form:
- Enter username and password
- Select birth date
- Check multiple interests
- Upload an image
- Submit and view all data including image preview

## 7. Practice Exercises
1. Add custom validation rules
2. Implement multiple file uploads
3. Create a dynamic form builder
4. Add AJAX form submission
5. Implement form data persistence

## 8. Verification and Validation
- [ ] Basic form works correctly
- [ ] File upload functions properly
- [ ] Validation shows errors
- [ ] Data displays on success page
- [ ] Form retains data on error

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| File upload fails | Check multer configuration |
| Validation not working | Verify middleware order |
| Form data lost | Check body-parser setup |
| Image not displaying | Verify static file serving |

## 10. Additional Resources
- [Express-Validator](https://express-validator.github.io/docs/)
- [Multer Documentation](https://github.com/expressjs/multer)
- [HTML Forms](https://developer.mozilla.org/en-US/docs/Learn/Forms)
- [File Upload Best Practices](https://expressjs.com/en/advanced/best-practice-security.html#uploads)
