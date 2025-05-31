# Experiment 4A: ExpressJS - MongoDB with Mongoose

## 1. Experiment Information
- **Experiment Number**: 04A
- **Title**: MongoDB Database Operations using Mongoose
- **Duration**: 1 hour

## 2. Learning Objectives
- Connect to MongoDB using Mongoose
- Create Mongoose schemas and models
- Implement CRUD operations
- Handle database errors
- Use Mongoose validation

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- MongoDB Community Server
- Visual Studio Code
- Postman

### Knowledge Requirements
- Basic JavaScript
- Express basics
- MongoDB concepts
- Async/await

## 4. Theory and Concepts
### MongoDB and Mongoose
- Document-based database
- Schema design
- Data modeling
- CRUD operations
- Validation rules

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create project directory
mkdir mongoose-crud-demo
cd mongoose-crud-demo

# Initialize project
npm init -y

# Install dependencies
npm install express mongoose morgan

# Create project structure
mkdir models
mkdir controllers
mkdir routes
```

### 2. Create Database Models

Create models/student.js:
```javascript
const mongoose = require('mongoose');

const studentSchema = new mongoose.Schema({
    rollNo: {
        type: String,
        required: true,
        unique: true,
        trim: true
    },
    name: {
        type: String,
        required: true,
        trim: true
    },
    email: {
        type: String,
        required: true,
        unique: true,
        trim: true,
        lowercase: true
    },
    course: {
        type: String,
        required: true,
        trim: true
    },
    semester: {
        type: Number,
        required: true,
        min: 1,
        max: 8
    },
    grades: [{
        subject: {
            type: String,
            required: true
        },
        score: {
            type: Number,
            required: true,
            min: 0,
            max: 100
        }
    }],
    active: {
        type: Boolean,
        default: true
    },
    createdAt: {
        type: Date,
        default: Date.now
    }
});

module.exports = mongoose.model('Student', studentSchema);
```

### 3. Create Controllers

Create controllers/studentController.js:
```javascript
const Student = require('../models/student');

// Get all students
exports.getAllStudents = async (req, res) => {
    try {
        const students = await Student.find({ active: true });
        res.json(students);
    } catch (error) {
        res.status(500).json({ message: error.message });
    }
};

// Get student by ID
exports.getStudentById = async (req, res) => {
    try {
        const student = await Student.findById(req.params.id);
        if (!student || !student.active) {
            return res.status(404).json({ message: 'Student not found' });
        }
        res.json(student);
    } catch (error) {
        res.status(500).json({ message: error.message });
    }
};

// Create new student
exports.createStudent = async (req, res) => {
    try {
        const student = new Student(req.body);
        const newStudent = await student.save();
        res.status(201).json(newStudent);
    } catch (error) {
        res.status(400).json({ message: error.message });
    }
};

// Update student
exports.updateStudent = async (req, res) => {
    try {
        const student = await Student.findById(req.params.id);
        if (!student || !student.active) {
            return res.status(404).json({ message: 'Student not found' });
        }

        Object.assign(student, req.body);
        const updatedStudent = await student.save();
        res.json(updatedStudent);
    } catch (error) {
        res.status(400).json({ message: error.message });
    }
};

// Delete student (soft delete)
exports.deleteStudent = async (req, res) => {
    try {
        const student = await Student.findById(req.params.id);
        if (!student || !student.active) {
            return res.status(404).json({ message: 'Student not found' });
        }

        student.active = false;
        await student.save();
        res.json({ message: 'Student deleted successfully' });
    } catch (error) {
        res.status(500).json({ message: error.message });
    }
};

// Add grade to student
exports.addGrade = async (req, res) => {
    try {
        const student = await Student.findById(req.params.id);
        if (!student || !student.active) {
            return res.status(404).json({ message: 'Student not found' });
        }

        student.grades.push(req.body);
        const updatedStudent = await student.save();
        res.json(updatedStudent);
    } catch (error) {
        res.status(400).json({ message: error.message });
    }
};

// Get student grades
exports.getStudentGrades = async (req, res) => {
    try {
        const student = await Student.findById(req.params.id);
        if (!student || !student.active) {
            return res.status(404).json({ message: 'Student not found' });
        }

        res.json(student.grades);
    } catch (error) {
        res.status(500).json({ message: error.message });
    }
};
```

### 4. Create Routes

Create routes/studentRoutes.js:
```javascript
const express = require('express');
const router = express.Router();
const studentController = require('../controllers/studentController');

// CRUD routes
router.get('/', studentController.getAllStudents);
router.get('/:id', studentController.getStudentById);
router.post('/', studentController.createStudent);
router.put('/:id', studentController.updateStudent);
router.delete('/:id', studentController.deleteStudent);

// Grade routes
router.post('/:id/grades', studentController.addGrade);
router.get('/:id/grades', studentController.getStudentGrades);

module.exports = router;
```

### 5. Create Main Server File

Create index.js:
```javascript
const express = require('express');
const mongoose = require('mongoose');
const morgan = require('morgan');
const studentRoutes = require('./routes/studentRoutes');

const app = express();
const port = 3000;

// Connect to MongoDB
mongoose.connect('mongodb://localhost/student_db', {
    useNewUrlParser: true,
    useUnifiedTopology: true
}).then(() => {
    console.log('Connected to MongoDB');
}).catch((error) => {
    console.error('MongoDB connection error:', error);
});

// Middleware
app.use(express.json());
app.use(morgan('dev'));

// Routes
app.use('/api/students', studentRoutes);

// Error handling middleware
app.use((err, req, res, next) => {
    console.error(err.stack);
    res.status(500).json({ message: 'Something went wrong!' });
});

// Start server
app.listen(port, () => {
    console.log(`Server running at http://localhost:${port}`);
});
```

## 6. Expected Output

Start MongoDB and the server:
```bash
# Start MongoDB (if not running)
mongod

# Start the application
node index.js
```

Test the following API endpoints using Postman:

1. Create Student:
```http
POST http://localhost:3000/api/students
Content-Type: application/json

{
    "rollNo": "CS001",
    "name": "John Doe",
    "email": "john@example.com",
    "course": "Computer Science",
    "semester": 3,
    "grades": [
        {
            "subject": "Mathematics",
            "score": 85
        }
    ]
}
```

2. Get All Students:
```http
GET http://localhost:3000/api/students
```

3. Get Student by ID:
```http
GET http://localhost:3000/api/students/:id
```

4. Update Student:
```http
PUT http://localhost:3000/api/students/:id
Content-Type: application/json

{
    "name": "John Smith",
    "semester": 4
}
```

5. Add Grade:
```http
POST http://localhost:3000/api/students/:id/grades
Content-Type: application/json

{
    "subject": "Physics",
    "score": 90
}
```

6. Get Student Grades:
```http
GET http://localhost:3000/api/students/:id/grades
```

7. Delete Student:
```http
DELETE http://localhost:3000/api/students/:id
```

## 7. Practice Exercises
1. Add pagination to getAllStudents
2. Implement sorting and filtering
3. Add grade average calculation
4. Create bulk operations
5. Implement data validation middleware

## 8. Verification and Validation
- [ ] MongoDB connection works
- [ ] CRUD operations function correctly
- [ ] Error handling is implemented
- [ ] Data validation works
- [ ] Soft delete functions properly

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| Connection failed | Check MongoDB service |
| Validation error | Review schema rules |
| Duplicate key | Check unique constraints |
| Query not working | Verify MongoDB syntax |

## 10. Additional Resources
- [Mongoose Documentation](https://mongoosejs.com/docs/)
- [MongoDB CRUD Operations](https://docs.mongodb.com/manual/crud/)
- [Mongoose Validation](https://mongoosejs.com/docs/validation.html)
- [MongoDB Best Practices](https://www.mongodb.com/developer/products/mongodb/mongodb-schema-design-best-practices/)
