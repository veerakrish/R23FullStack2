# Experiment 4B: ExpressJS - Single Page Application with RESTful APIs

## 1. Experiment Information
- **Experiment Number**: 04B
- **Title**: Building a Single Page Application using RESTful APIs
- **Duration**: 1 hour

## 2. Learning Objectives
- Create a RESTful API
- Build a single-page application
- Handle client-side routing
- Implement AJAX requests
- Manage application state

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- MongoDB Community Server
- Visual Studio Code
- Web browser

### Knowledge Requirements
- JavaScript ES6+
- DOM manipulation
- AJAX/Fetch API
- Express basics
- MongoDB (Experiment 4A)

## 4. Theory and Concepts
### Single Page Applications
- Client-side routing
- State management
- RESTful architecture
- AJAX communication
- Frontend rendering

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create project directory
mkdir spa-books-demo
cd spa-books-demo

# Initialize project
npm init -y

# Install dependencies
npm install express mongoose cors

# Create project structure
mkdir public
mkdir models
mkdir routes
```

### 2. Create Book Model

Create models/book.js:
```javascript
const mongoose = require('mongoose');

const bookSchema = new mongoose.Schema({
    title: {
        type: String,
        required: true,
        trim: true
    },
    author: {
        type: String,
        required: true,
        trim: true
    },
    isbn: {
        type: String,
        required: true,
        unique: true,
        trim: true
    },
    genre: {
        type: String,
        required: true,
        trim: true
    },
    publishedYear: {
        type: Number,
        required: true
    },
    available: {
        type: Boolean,
        default: true
    }
});

module.exports = mongoose.model('Book', bookSchema);
```

### 3. Create API Routes

Create routes/bookRoutes.js:
```javascript
const express = require('express');
const router = express.Router();
const Book = require('../models/book');

// Get all books
router.get('/books', async (req, res) => {
    try {
        const books = await Book.find();
        res.json(books);
    } catch (error) {
        res.status(500).json({ message: error.message });
    }
});

// Get book by ID
router.get('/books/:id', async (req, res) => {
    try {
        const book = await Book.findById(req.params.id);
        if (!book) {
            return res.status(404).json({ message: 'Book not found' });
        }
        res.json(book);
    } catch (error) {
        res.status(500).json({ message: error.message });
    }
});

// Create book
router.post('/books', async (req, res) => {
    try {
        const book = new Book(req.body);
        const newBook = await book.save();
        res.status(201).json(newBook);
    } catch (error) {
        res.status(400).json({ message: error.message });
    }
});

// Update book
router.put('/books/:id', async (req, res) => {
    try {
        const book = await Book.findById(req.params.id);
        if (!book) {
            return res.status(404).json({ message: 'Book not found' });
        }
        Object.assign(book, req.body);
        const updatedBook = await book.save();
        res.json(updatedBook);
    } catch (error) {
        res.status(400).json({ message: error.message });
    }
});

// Delete book
router.delete('/books/:id', async (req, res) => {
    try {
        const book = await Book.findById(req.params.id);
        if (!book) {
            return res.status(404).json({ message: 'Book not found' });
        }
        await book.remove();
        res.json({ message: 'Book deleted' });
    } catch (error) {
        res.status(500).json({ message: error.message });
    }
});

module.exports = router;
```

### 4. Create Server

Create index.js:
```javascript
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');
const bookRoutes = require('./routes/bookRoutes');

const app = express();
const port = 3000;

// Connect to MongoDB
mongoose.connect('mongodb://localhost/library_db', {
    useNewUrlParser: true,
    useUnifiedTopology: true
}).then(() => {
    console.log('Connected to MongoDB');
}).catch((error) => {
    console.error('MongoDB connection error:', error);
});

// Middleware
app.use(cors());
app.use(express.json());
app.use(express.static('public'));

// API Routes
app.use('/api', bookRoutes);

// Serve SPA
app.get('*', (req, res) => {
    res.sendFile(__dirname + '/public/index.html');
});

// Start server
app.listen(port, () => {
    console.log(`Server running at http://localhost:${port}`);
});
```

### 5. Create Single Page Application

Create public/index.html:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Library Management</title>
    <link rel="stylesheet" href="css/style.css">
</head>
<body>
    <nav>
        <div class="nav-brand">Library Management</div>
        <div class="nav-links">
            <a href="#" data-page="books">Books</a>
            <a href="#" data-page="add-book">Add Book</a>
        </div>
    </nav>

    <div id="app"></div>

    <script src="js/app.js" type="module"></script>
</body>
</html>
```

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

nav {
    background: #333;
    color: white;
    padding: 1rem;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.nav-brand {
    font-size: 1.5rem;
    font-weight: bold;
}

.nav-links a {
    color: white;
    text-decoration: none;
    margin-left: 1rem;
}

#app {
    max-width: 1200px;
    margin: 2rem auto;
    padding: 0 20px;
}

.book-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    gap: 20px;
    margin-top: 2rem;
}

.book-card {
    background: white;
    padding: 1rem;
    border-radius: 5px;
    box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

.book-form {
    background: white;
    padding: 2rem;
    border-radius: 5px;
    box-shadow: 0 2px 5px rgba(0,0,0,0.1);
    max-width: 600px;
    margin: 0 auto;
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
    border: 1px solid #ddd;
    border-radius: 3px;
}

.btn {
    background: #3498db;
    color: white;
    padding: 0.7rem 1.5rem;
    border: none;
    border-radius: 3px;
    cursor: pointer;
}

.btn-danger {
    background: #e74c3c;
}

.message {
    padding: 1rem;
    margin-bottom: 1rem;
    border-radius: 3px;
}

.success {
    background: #2ecc71;
    color: white;
}

.error {
    background: #e74c3c;
    color: white;
}
```

Create public/js/app.js:
```javascript
// Router
const router = {
    navigate: function(page) {
        switch(page) {
            case 'books':
                this.showBooks();
                break;
            case 'add-book':
                this.showAddBook();
                break;
            default:
                this.showBooks();
        }
    },

    init: function() {
        document.querySelectorAll('[data-page]').forEach(link => {
            link.addEventListener('click', (e) => {
                e.preventDefault();
                this.navigate(e.target.dataset.page);
            });
        });

        // Initial route
        this.navigate('books');
    }
};

// API Service
const api = {
    baseUrl: '/api',

    async getBooks() {
        const response = await fetch(`${this.baseUrl}/books`);
        return response.json();
    },

    async getBook(id) {
        const response = await fetch(`${this.baseUrl}/books/${id}`);
        return response.json();
    },

    async createBook(book) {
        const response = await fetch(`${this.baseUrl}/books`, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(book)
        });
        return response.json();
    },

    async updateBook(id, book) {
        const response = await fetch(`${this.baseUrl}/books/${id}`, {
            method: 'PUT',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(book)
        });
        return response.json();
    },

    async deleteBook(id) {
        const response = await fetch(`${this.baseUrl}/books/${id}`, {
            method: 'DELETE'
        });
        return response.json();
    }
};

// Views
const views = {
    showBooks: async function() {
        const app = document.getElementById('app');
        try {
            const books = await api.getBooks();
            app.innerHTML = `
                <h1>Books</h1>
                <div class="book-grid">
                    ${books.map(book => `
                        <div class="book-card">
                            <h3>${book.title}</h3>
                            <p><strong>Author:</strong> ${book.author}</p>
                            <p><strong>Genre:</strong> ${book.genre}</p>
                            <p><strong>ISBN:</strong> ${book.isbn}</p>
                            <p><strong>Year:</strong> ${book.publishedYear}</p>
                            <p><strong>Status:</strong> ${book.available ? 'Available' : 'Not Available'}</p>
                            <button class="btn btn-danger" onclick="deleteBook('${book._id}')">Delete</button>
                        </div>
                    `).join('')}
                </div>
            `;
        } catch (error) {
            app.innerHTML = `<div class="message error">${error.message}</div>`;
        }
    },

    showAddBook: function() {
        const app = document.getElementById('app');
        app.innerHTML = `
            <h1>Add New Book</h1>
            <form class="book-form" id="addBookForm">
                <div class="form-group">
                    <label for="title">Title:</label>
                    <input type="text" id="title" required>
                </div>
                <div class="form-group">
                    <label for="author">Author:</label>
                    <input type="text" id="author" required>
                </div>
                <div class="form-group">
                    <label for="isbn">ISBN:</label>
                    <input type="text" id="isbn" required>
                </div>
                <div class="form-group">
                    <label for="genre">Genre:</label>
                    <input type="text" id="genre" required>
                </div>
                <div class="form-group">
                    <label for="publishedYear">Published Year:</label>
                    <input type="number" id="publishedYear" required>
                </div>
                <button type="submit" class="btn">Add Book</button>
            </form>
        `;

        document.getElementById('addBookForm').addEventListener('submit', async (e) => {
            e.preventDefault();
            try {
                const book = {
                    title: document.getElementById('title').value,
                    author: document.getElementById('author').value,
                    isbn: document.getElementById('isbn').value,
                    genre: document.getElementById('genre').value,
                    publishedYear: parseInt(document.getElementById('publishedYear').value)
                };

                await api.createBook(book);
                router.navigate('books');
            } catch (error) {
                app.insertAdjacentHTML('afterbegin', 
                    `<div class="message error">${error.message}</div>`
                );
            }
        });
    }
};

// Initialize router
router.init();

// Global functions
window.deleteBook = async function(id) {
    if (confirm('Are you sure you want to delete this book?')) {
        try {
            await api.deleteBook(id);
            router.navigate('books');
        } catch (error) {
            alert(error.message);
        }
    }
};
```

## 6. Expected Output

Start MongoDB and the server:
```bash
# Start MongoDB (if not running)
mongod

# Start the application
node index.js
```

Visit http://localhost:3000 and test:

1. View Books:
- See list of all books in grid layout
- Each book shows complete information
- Delete functionality works

2. Add Book:
- Fill in book details
- Submit form
- See new book in list
- Validation works

3. Navigation:
- Switch between views
- No page reload
- State persistence

## 7. Practice Exercises
1. Add book editing functionality
2. Implement search and filtering
3. Add book categories
4. Create user reviews system
5. Add pagination to book list

## 8. Verification and Validation
- [ ] API endpoints work correctly
- [ ] SPA navigation functions
- [ ] CRUD operations work
- [ ] Form validation works
- [ ] Error handling implemented

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| API 404 errors | Check route paths |
| CORS errors | Verify CORS setup |
| Form submission fails | Check validation |
| Navigation issues | Debug router code |

## 10. Additional Resources
- [Single Page Applications](https://developer.mozilla.org/en-US/docs/Glossary/SPA)
- [REST API Design](https://restfulapi.net/)
- [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
- [Client-side Routing](https://developer.mozilla.org/en-US/docs/Web/API/History_API)
