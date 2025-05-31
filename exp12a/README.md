# Experiment 12A: Todo List Application with Node.js and Express.js

## 1. Experiment Information
- **Experiment Number**: 12A
- **Title**: Building a Todo List Application
- **Duration**: 90 minutes

## 2. Learning Objectives
- Build a full-stack application
- Implement RESTful APIs
- Create MongoDB schemas
- Handle user authentication
- Manage state in React
- Implement CRUD operations

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- MongoDB
- Visual Studio Code
- Postman (optional)

### Knowledge Requirements
- JavaScript/ES6
- React basics
- Express.js basics
- MongoDB basics
- Previous experiments completion

## 4. Theory and Concepts
- RESTful APIs
- CRUD operations
- JWT authentication
- State management
- Database design
- Frontend-backend integration

## 5. Step-by-step Instructions

### 1. Project Setup

Initialize backend:
```bash
mkdir todo-app
cd todo-app

# Initialize backend
mkdir backend
cd backend
npm init -y

# Install dependencies
npm install express mongoose dotenv cors bcryptjs jsonwebtoken

# Create basic folder structure
mkdir config controllers middleware models routes
```

### 2. Backend Implementation

1. Create `.env` file:
```plaintext
PORT=5000
MONGODB_URI=mongodb://localhost:27017/todo-app
JWT_SECRET=your_jwt_secret_key
```

2. Create `config/db.js`:
```javascript
const mongoose = require('mongoose');

const connectDB = async () => {
  try {
    const conn = await mongoose.connect(process.env.MONGODB_URI);
    console.log(`MongoDB Connected: ${conn.connection.host}`);
  } catch (error) {
    console.error(`Error: ${error.message}`);
    process.exit(1);
  }
};

module.exports = connectDB;
```

3. Create `models/Todo.js`:
```javascript
const mongoose = require('mongoose');

const todoSchema = new mongoose.Schema({
  user: {
    type: mongoose.Schema.Types.ObjectId,
    required: true,
    ref: 'User'
  },
  title: {
    type: String,
    required: true,
    trim: true
  },
  description: {
    type: String,
    trim: true
  },
  completed: {
    type: Boolean,
    default: false
  },
  priority: {
    type: String,
    enum: ['low', 'medium', 'high'],
    default: 'medium'
  },
  dueDate: {
    type: Date
  }
}, {
  timestamps: true
});

module.exports = mongoose.model('Todo', todoSchema);
```

4. Create `models/User.js`:
```javascript
const mongoose = require('mongoose');
const bcrypt = require('bcryptjs');

const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true
  },
  email: {
    type: String,
    required: true,
    unique: true
  },
  password: {
    type: String,
    required: true
  }
}, {
  timestamps: true
});

userSchema.pre('save', async function(next) {
  if (!this.isModified('password')) {
    next();
  }
  const salt = await bcrypt.genSalt(10);
  this.password = await bcrypt.hash(this.password, salt);
});

userSchema.methods.matchPassword = async function(enteredPassword) {
  return await bcrypt.compare(enteredPassword, this.password);
};

module.exports = mongoose.model('User', userSchema);
```

5. Create `middleware/auth.js`:
```javascript
const jwt = require('jsonwebtoken');
const User = require('../models/User');

const protect = async (req, res, next) => {
  let token;

  if (req.headers.authorization?.startsWith('Bearer')) {
    try {
      token = req.headers.authorization.split(' ')[1];
      const decoded = jwt.verify(token, process.env.JWT_SECRET);
      req.user = await User.findById(decoded.id).select('-password');
      next();
    } catch (error) {
      res.status(401).json({ message: 'Not authorized' });
    }
  }

  if (!token) {
    res.status(401).json({ message: 'Not authorized, no token' });
  }
};

module.exports = { protect };
```

6. Create `controllers/userController.js`:
```javascript
const jwt = require('jsonwebtoken');
const User = require('../models/User');

const generateToken = (id) => {
  return jwt.sign({ id }, process.env.JWT_SECRET, {
    expiresIn: '30d'
  });
};

// Register user
exports.registerUser = async (req, res) => {
  try {
    const { name, email, password } = req.body;

    const userExists = await User.findOne({ email });
    if (userExists) {
      return res.status(400).json({ message: 'User already exists' });
    }

    const user = await User.create({
      name,
      email,
      password
    });

    res.status(201).json({
      _id: user._id,
      name: user.name,
      email: user.email,
      token: generateToken(user._id)
    });
  } catch (error) {
    res.status(400).json({ message: error.message });
  }
};

// Login user
exports.loginUser = async (req, res) => {
  try {
    const { email, password } = req.body;

    const user = await User.findOne({ email });
    if (user && (await user.matchPassword(password))) {
      res.json({
        _id: user._id,
        name: user.name,
        email: user.email,
        token: generateToken(user._id)
      });
    } else {
      res.status(401).json({ message: 'Invalid email or password' });
    }
  } catch (error) {
    res.status(400).json({ message: error.message });
  }
};
```

7. Create `controllers/todoController.js`:
```javascript
const Todo = require('../models/Todo');

// Get todos
exports.getTodos = async (req, res) => {
  try {
    const todos = await Todo.find({ user: req.user._id });
    res.json(todos);
  } catch (error) {
    res.status(500).json({ message: error.message });
  }
};

// Create todo
exports.createTodo = async (req, res) => {
  try {
    const todo = new Todo({
      user: req.user._id,
      title: req.body.title,
      description: req.body.description,
      priority: req.body.priority,
      dueDate: req.body.dueDate
    });

    const createdTodo = await todo.save();
    res.status(201).json(createdTodo);
  } catch (error) {
    res.status(400).json({ message: error.message });
  }
};

// Update todo
exports.updateTodo = async (req, res) => {
  try {
    const todo = await Todo.findById(req.params.id);

    if (!todo) {
      return res.status(404).json({ message: 'Todo not found' });
    }

    if (todo.user.toString() !== req.user._id.toString()) {
      return res.status(401).json({ message: 'Not authorized' });
    }

    const updatedTodo = await Todo.findByIdAndUpdate(
      req.params.id,
      req.body,
      { new: true }
    );

    res.json(updatedTodo);
  } catch (error) {
    res.status(400).json({ message: error.message });
  }
};

// Delete todo
exports.deleteTodo = async (req, res) => {
  try {
    const todo = await Todo.findById(req.params.id);

    if (!todo) {
      return res.status(404).json({ message: 'Todo not found' });
    }

    if (todo.user.toString() !== req.user._id.toString()) {
      return res.status(401).json({ message: 'Not authorized' });
    }

    await todo.remove();
    res.json({ message: 'Todo removed' });
  } catch (error) {
    res.status(500).json({ message: error.message });
  }
};
```

8. Create `routes/userRoutes.js`:
```javascript
const express = require('express');
const router = express.Router();
const { registerUser, loginUser } = require('../controllers/userController');

router.post('/register', registerUser);
router.post('/login', loginUser);

module.exports = router;
```

9. Create `routes/todoRoutes.js`:
```javascript
const express = require('express');
const router = express.Router();
const { protect } = require('../middleware/auth');
const {
  getTodos,
  createTodo,
  updateTodo,
  deleteTodo
} = require('../controllers/todoController');

router.route('/')
  .get(protect, getTodos)
  .post(protect, createTodo);

router.route('/:id')
  .put(protect, updateTodo)
  .delete(protect, deleteTodo);

module.exports = router;
```

10. Create `server.js`:
```javascript
require('dotenv').config();
const express = require('express');
const cors = require('cors');
const connectDB = require('./config/db');
const userRoutes = require('./routes/userRoutes');
const todoRoutes = require('./routes/todoRoutes');

// Connect to database
connectDB();

const app = express();

// Middleware
app.use(cors());
app.use(express.json());

// Routes
app.use('/api/users', userRoutes);
app.use('/api/todos', todoRoutes);

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

### 3. Frontend Implementation

1. Create React frontend:
```bash
cd ..
npx create-react-app frontend
cd frontend
npm install @mui/material @emotion/react @emotion/styled @mui/icons-material axios react-router-dom date-fns
```

2. Create `src/services/api.js`:
```javascript
import axios from 'axios';

const API_URL = 'http://localhost:5000/api';

// Create axios instance
const api = axios.create({
  baseURL: API_URL,
  headers: {
    'Content-Type': 'application/json'
  }
});

// Add token to requests
api.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => {
    return Promise.reject(error);
  }
);

export const login = (email, password) => 
  api.post('/users/login', { email, password });

export const register = (name, email, password) =>
  api.post('/users/register', { name, email, password });

export const getTodos = () => 
  api.get('/todos');

export const createTodo = (todo) =>
  api.post('/todos', todo);

export const updateTodo = (id, todo) =>
  api.put(`/todos/${id}`, todo);

export const deleteTodo = (id) =>
  api.delete(`/todos/${id}`);

export default api;
```

3. Create components and implement the frontend interface (check the complete code in the repository).

## 6. Expected Output

1. Start MongoDB:
```bash
mongod
```

2. Start backend server:
```bash
cd backend
npm start
```

3. Start frontend development server:
```bash
cd frontend
npm start
```

Visit http://localhost:3000 to see:
- User registration/login
- Todo list display
- Add/edit/delete todos
- Mark todos as complete
- Set priorities and due dates
- Filter and sort todos

## 7. Practice Exercises
1. Add categories/tags
2. Implement search
3. Add bulk actions
4. Create task statistics
5. Add file attachments

## 8. Verification and Validation
- [ ] Backend API works
- [ ] Frontend renders
- [ ] CRUD operations work
- [ ] Authentication works
- [ ] Data persists

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| MongoDB not running | Start MongoDB service |
| CORS error | Check CORS configuration |
| Auth token invalid | Clear localStorage |
| API errors | Check server logs |

## 10. Additional Resources
- [Express.js Documentation](https://expressjs.com/)
- [React Documentation](https://reactjs.org/)
- [MongoDB Documentation](https://docs.mongodb.com/)
- [Material-UI Documentation](https://mui.com/)
