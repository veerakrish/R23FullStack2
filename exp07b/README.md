# Experiment 7B: ReactJS - Rendering Lists

## 1. Experiment Information
- **Experiment Number**: 07B
- **Title**: Rendering Lists in React
- **Duration**: 45 minutes

## 2. Learning Objectives
- Understand list rendering patterns
- Use map() for rendering
- Handle keys properly
- Filter and sort lists
- Implement list operations
- Create dynamic lists

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- Visual Studio Code
- Web browser

### Knowledge Requirements
- JavaScript arrays
- Array methods
- React basics
- Previous experiments completion

## 4. Theory and Concepts
### List Rendering
- Array mapping
- Keys in lists
- List operations
- Filtering
- Sorting
- Dynamic updates

## 5. Step-by-step Instructions

### 1. Project Setup
```bash
# Create new React project
npx create-react-app list-demo
cd list-demo

# Start development server
npm start
```

### 2. Create Components

Create src/components/TaskManager.js:
```javascript
import React, { useState } from 'react';

function TaskManager() {
  const [tasks, setTasks] = useState([
    { id: 1, title: 'Learn React', completed: true, priority: 'high' },
    { id: 2, title: 'Build Projects', completed: false, priority: 'medium' },
    { id: 3, title: 'Write Documentation', completed: false, priority: 'low' }
  ]);

  const [newTask, setNewTask] = useState('');
  const [filter, setFilter] = useState('all');
  const [sortBy, setSortBy] = useState('id');

  // Add new task
  const addTask = (e) => {
    e.preventDefault();
    if (!newTask.trim()) return;

    setTasks(prev => [...prev, {
      id: Date.now(),
      title: newTask,
      completed: false,
      priority: 'medium'
    }]);
    setNewTask('');
  };

  // Toggle task completion
  const toggleTask = (id) => {
    setTasks(prev =>
      prev.map(task =>
        task.id === id
          ? { ...task, completed: !task.completed }
          : task
      )
    );
  };

  // Update task priority
  const updatePriority = (id, priority) => {
    setTasks(prev =>
      prev.map(task =>
        task.id === id
          ? { ...task, priority }
          : task
      )
    );
  };

  // Delete task
  const deleteTask = (id) => {
    setTasks(prev => prev.filter(task => task.id !== id));
  };

  // Filter tasks
  const getFilteredTasks = () => {
    let filtered = [...tasks];

    switch (filter) {
      case 'active':
        filtered = filtered.filter(task => !task.completed);
        break;
      case 'completed':
        filtered = filtered.filter(task => task.completed);
        break;
      default:
        break;
    }

    // Sort tasks
    switch (sortBy) {
      case 'priority':
        const priorityOrder = { high: 1, medium: 2, low: 3 };
        filtered.sort((a, b) => priorityOrder[a.priority] - priorityOrder[b.priority]);
        break;
      case 'title':
        filtered.sort((a, b) => a.title.localeCompare(b.title));
        break;
      default:
        filtered.sort((a, b) => a.id - b.id);
        break;
    }

    return filtered;
  };

  return (
    <div className="task-manager">
      <h2>Task Manager</h2>

      {/* Add Task Form */}
      <form onSubmit={addTask} className="add-task">
        <input
          type="text"
          value={newTask}
          onChange={(e) => setNewTask(e.target.value)}
          placeholder="Add new task..."
        />
        <button type="submit">Add</button>
      </form>

      {/* Controls */}
      <div className="controls">
        <div className="filters">
          <button
            className={filter === 'all' ? 'active' : ''}
            onClick={() => setFilter('all')}
          >
            All
          </button>
          <button
            className={filter === 'active' ? 'active' : ''}
            onClick={() => setFilter('active')}
          >
            Active
          </button>
          <button
            className={filter === 'completed' ? 'active' : ''}
            onClick={() => setFilter('completed')}
          >
            Completed
          </button>
        </div>

        <select
          value={sortBy}
          onChange={(e) => setSortBy(e.target.value)}
        >
          <option value="id">Sort by ID</option>
          <option value="title">Sort by Title</option>
          <option value="priority">Sort by Priority</option>
        </select>
      </div>

      {/* Task List */}
      <ul className="task-list">
        {getFilteredTasks().map(task => (
          <li key={task.id} className={`task-item ${task.completed ? 'completed' : ''}`}>
            <input
              type="checkbox"
              checked={task.completed}
              onChange={() => toggleTask(task.id)}
            />
            <span className="task-title">{task.title}</span>
            <select
              value={task.priority}
              onChange={(e) => updatePriority(task.id, e.target.value)}
              className={`priority ${task.priority}`}
            >
              <option value="high">High</option>
              <option value="medium">Medium</option>
              <option value="low">Low</option>
            </select>
            <button
              onClick={() => deleteTask(task.id)}
              className="delete"
            >
              Delete
            </button>
          </li>
        ))}
      </ul>

      {/* Stats */}
      <div className="stats">
        <p>Total: {tasks.length}</p>
        <p>Active: {tasks.filter(task => !task.completed).length}</p>
        <p>Completed: {tasks.filter(task => task.completed).length}</p>
      </div>
    </div>
  );
}

export default TaskManager;
```

Create src/components/DataTable.js:
```javascript
import React, { useState } from 'react';

function DataTable() {
  const [data] = useState([
    { id: 1, name: 'John Doe', age: 25, city: 'New York', role: 'Developer' },
    { id: 2, name: 'Jane Smith', age: 30, city: 'London', role: 'Designer' },
    { id: 3, name: 'Bob Johnson', age: 35, city: 'Paris', role: 'Manager' },
    { id: 4, name: 'Alice Brown', age: 28, city: 'Tokyo', role: 'Developer' },
    { id: 5, name: 'Charlie Wilson', age: 32, city: 'Berlin', role: 'Designer' }
  ]);

  const [sortConfig, setSortConfig] = useState({
    key: null,
    direction: 'ascending'
  });

  // Sort data
  const sortedData = React.useMemo(() => {
    let sortableData = [...data];
    if (sortConfig.key !== null) {
      sortableData.sort((a, b) => {
        if (a[sortConfig.key] < b[sortConfig.key]) {
          return sortConfig.direction === 'ascending' ? -1 : 1;
        }
        if (a[sortConfig.key] > b[sortConfig.key]) {
          return sortConfig.direction === 'ascending' ? 1 : -1;
        }
        return 0;
      });
    }
    return sortableData;
  }, [data, sortConfig]);

  // Request sort
  const requestSort = (key) => {
    let direction = 'ascending';
    if (
      sortConfig.key === key &&
      sortConfig.direction === 'ascending'
    ) {
      direction = 'descending';
    }
    setSortConfig({ key, direction });
  };

  return (
    <div className="data-table">
      <h2>Data Table</h2>
      <table>
        <thead>
          <tr>
            <th onClick={() => requestSort('id')}>
              ID {sortConfig.key === 'id' && (
                <span>{sortConfig.direction === 'ascending' ? '↑' : '↓'}</span>
              )}
            </th>
            <th onClick={() => requestSort('name')}>
              Name {sortConfig.key === 'name' && (
                <span>{sortConfig.direction === 'ascending' ? '↑' : '↓'}</span>
              )}
            </th>
            <th onClick={() => requestSort('age')}>
              Age {sortConfig.key === 'age' && (
                <span>{sortConfig.direction === 'ascending' ? '↑' : '↓'}</span>
              )}
            </th>
            <th onClick={() => requestSort('city')}>
              City {sortConfig.key === 'city' && (
                <span>{sortConfig.direction === 'ascending' ? '↑' : '↓'}</span>
              )}
            </th>
            <th onClick={() => requestSort('role')}>
              Role {sortConfig.key === 'role' && (
                <span>{sortConfig.direction === 'ascending' ? '↑' : '↓'}</span>
              )}
            </th>
          </tr>
        </thead>
        <tbody>
          {sortedData.map(item => (
            <tr key={item.id}>
              <td>{item.id}</td>
              <td>{item.name}</td>
              <td>{item.age}</td>
              <td>{item.city}</td>
              <td>{item.role}</td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
}

export default DataTable;
```

### 3. Update App Component

Replace src/App.js:
```javascript
import React from 'react';
import './App.css';
import TaskManager from './components/TaskManager';
import DataTable from './components/DataTable';

function App() {
  return (
    <div className="App">
      <header>
        <h1>List Rendering in React</h1>
        <p>Exploring different list rendering patterns</p>
      </header>

      <main>
        <TaskManager />
        <DataTable />
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

/* Task Manager Styles */
.task-manager {
  margin-bottom: 2rem;
  padding: 1rem;
  border: 1px solid #ddd;
  border-radius: 5px;
}

.add-task {
  display: flex;
  gap: 1rem;
  margin-bottom: 1rem;
}

.add-task input {
  flex: 1;
  padding: 0.5rem;
  border: 1px solid #ddd;
  border-radius: 3px;
}

.controls {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}

.filters {
  display: flex;
  gap: 0.5rem;
}

.filters button {
  padding: 0.5rem 1rem;
  border: 1px solid #ddd;
  background: white;
  border-radius: 3px;
  cursor: pointer;
}

.filters button.active {
  background-color: #0066cc;
  color: white;
  border-color: #0066cc;
}

.task-list {
  list-style: none;
  padding: 0;
}

.task-item {
  display: flex;
  align-items: center;
  gap: 1rem;
  padding: 0.5rem;
  border-bottom: 1px solid #eee;
}

.task-item.completed .task-title {
  text-decoration: line-through;
  color: #999;
}

.task-title {
  flex: 1;
}

.priority {
  padding: 0.25rem;
  border-radius: 3px;
}

.priority.high {
  color: #e74c3c;
}

.priority.medium {
  color: #f39c12;
}

.priority.low {
  color: #27ae60;
}

.delete {
  padding: 0.25rem 0.5rem;
  background-color: #e74c3c;
  color: white;
  border: none;
  border-radius: 3px;
  cursor: pointer;
}

.stats {
  display: flex;
  justify-content: space-around;
  margin-top: 1rem;
  padding-top: 1rem;
  border-top: 1px solid #eee;
}

/* Data Table Styles */
.data-table {
  margin-bottom: 2rem;
  padding: 1rem;
  border: 1px solid #ddd;
  border-radius: 5px;
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 1rem;
}

th, td {
  padding: 0.75rem;
  text-align: left;
  border-bottom: 1px solid #ddd;
}

th {
  background-color: #f8f9fa;
  cursor: pointer;
}

th:hover {
  background-color: #e9ecef;
}

tr:hover {
  background-color: #f8f9fa;
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

select {
  padding: 0.5rem;
  border: 1px solid #ddd;
  border-radius: 3px;
}

h2 {
  margin-bottom: 1rem;
}
```

## 6. Expected Output

Start the development server:
```bash
npm start
```

Visit http://localhost:3000 to see:

1. Task Manager:
   - Add new tasks
   - Toggle completion
   - Set priorities
   - Filter tasks
   - Sort tasks
   - Delete tasks
   - View statistics

2. Data Table:
   - Sortable columns
   - Row highlighting
   - Responsive design
   - Visual indicators

## 7. Practice Exercises
1. Add pagination
2. Implement search
3. Add row selection
4. Create nested lists
5. Add drag and drop

## 8. Verification and Validation
- [ ] List rendering works
- [ ] Keys are unique
- [ ] Sorting works
- [ ] Filtering works
- [ ] Updates are correct

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| Key warnings | Add unique keys |
| Sort not working | Check sort logic |
| Updates not showing | Verify state updates |
| Performance issues | Use memo/callback |

## 10. Additional Resources
- [Lists and Keys](https://reactjs.org/docs/lists-and-keys.html)
- [Array Methods](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
- [React Performance](https://reactjs.org/docs/optimizing-performance.html)
- [React Patterns](https://reactpatterns.com/)
