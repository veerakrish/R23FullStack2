# Experiment 12B: Quiz Application with React.js

## 1. Experiment Information
- **Experiment Number**: 12B
- **Title**: Building a Quiz Application
- **Duration**: 90 minutes

## 2. Learning Objectives
- Create interactive React components
- Manage application state
- Handle user interactions
- Implement routing
- Create dynamic UI
- Track user progress

## 3. Prerequisites
### Software Requirements
- Node.js (v14+ LTS)
- npm (v6+)
- Visual Studio Code
- Web browser

### Knowledge Requirements
- JavaScript/ES6
- React basics
- React hooks
- Previous experiments completion

## 4. Theory and Concepts
- React components
- State management
- React Router
- User interface design
- Quiz logic
- Score calculation

## 5. Step-by-step Instructions

### 1. Project Setup

Create React project:
```bash
npx create-react-app quiz-app
cd quiz-app
npm install @mui/material @emotion/react @emotion/styled @mui/icons-material react-router-dom
```

### 2. Project Structure
```
quiz-app/
├── src/
│   ├── components/
│   │   ├── Quiz.js
│   │   ├── Question.js
│   │   ├── Results.js
│   │   ├── Timer.js
│   │   └── Progress.js
│   ├── data/
│   │   └── questions.js
│   ├── contexts/
│   │   └── QuizContext.js
│   ├── pages/
│   │   ├── Home.js
│   │   ├── QuizPage.js
│   │   └── ResultsPage.js
│   ├── App.js
│   └── index.js
└── package.json
```

### 3. Implementation

1. Create `src/data/questions.js`:
```javascript
export const questions = [
  {
    id: 1,
    question: "What is React.js?",
    options: [
      "A JavaScript library for building user interfaces",
      "A programming language",
      "A database management system",
      "An operating system"
    ],
    correctAnswer: 0,
    explanation: "React is a JavaScript library developed by Facebook for building user interfaces."
  },
  {
    id: 2,
    question: "What is JSX?",
    options: [
      "A JavaScript XML parser",
      "A syntax extension for JavaScript",
      "A new programming language",
      "A database query language"
    ],
    correctAnswer: 1,
    explanation: "JSX is a syntax extension for JavaScript that allows you to write HTML-like code in your JavaScript files."
  },
  {
    id: 3,
    question: "What is a React component?",
    options: [
      "A function or class that returns HTML elements",
      "A CSS framework",
      "A JavaScript module",
      "A type of database"
    ],
    correctAnswer: 0,
    explanation: "A React component is a reusable piece of code that returns React elements describing what should appear on the screen."
  },
  // Add more questions as needed
];
```

2. Create `src/contexts/QuizContext.js`:
```javascript
import React, { createContext, useContext, useReducer } from 'react';

const QuizContext = createContext();

const initialState = {
  currentQuestion: 0,
  score: 0,
  answers: [],
  quizStarted: false,
  quizFinished: false,
  timeRemaining: 300 // 5 minutes in seconds
};

function quizReducer(state, action) {
  switch (action.type) {
    case 'START_QUIZ':
      return {
        ...state,
        quizStarted: true,
        timeRemaining: 300
      };
    case 'ANSWER_QUESTION':
      return {
        ...state,
        answers: [...state.answers, action.payload],
        score: action.payload.correct ? state.score + 1 : state.score,
        currentQuestion: state.currentQuestion + 1
      };
    case 'FINISH_QUIZ':
      return {
        ...state,
        quizFinished: true
      };
    case 'UPDATE_TIMER':
      return {
        ...state,
        timeRemaining: state.timeRemaining - 1
      };
    case 'RESET_QUIZ':
      return initialState;
    default:
      return state;
  }
}

export function QuizProvider({ children }) {
  const [state, dispatch] = useReducer(quizReducer, initialState);

  return (
    <QuizContext.Provider value={{ state, dispatch }}>
      {children}
    </QuizContext.Provider>
  );
}

export function useQuiz() {
  const context = useContext(QuizContext);
  if (!context) {
    throw new Error('useQuiz must be used within a QuizProvider');
  }
  return context;
}
```

3. Create `src/components/Timer.js`:
```javascript
import React, { useEffect } from 'react';
import { useQuiz } from '../contexts/QuizContext';

function Timer() {
  const { state, dispatch } = useQuiz();

  useEffect(() => {
    if (state.quizStarted && !state.quizFinished) {
      const timer = setInterval(() => {
        if (state.timeRemaining > 0) {
          dispatch({ type: 'UPDATE_TIMER' });
        } else {
          dispatch({ type: 'FINISH_QUIZ' });
          clearInterval(timer);
        }
      }, 1000);

      return () => clearInterval(timer);
    }
  }, [state.quizStarted, state.quizFinished, state.timeRemaining, dispatch]);

  const minutes = Math.floor(state.timeRemaining / 60);
  const seconds = state.timeRemaining % 60;

  return (
    <div className="timer">
      Time Remaining: {minutes}:{seconds < 10 ? `0${seconds}` : seconds}
    </div>
  );
}

export default Timer;
```

4. Create `src/components/Progress.js`:
```javascript
import React from 'react';
import { LinearProgress, Typography, Box } from '@mui/material';
import { useQuiz } from '../contexts/QuizContext';
import { questions } from '../data/questions';

function Progress() {
  const { state } = useQuiz();
  const progress = (state.currentQuestion / questions.length) * 100;

  return (
    <Box sx={{ width: '100%', mb: 2 }}>
      <Box sx={{ display: 'flex', justifyContent: 'space-between', mb: 1 }}>
        <Typography variant="body2" color="text.secondary">
          Question {state.currentQuestion + 1} of {questions.length}
        </Typography>
        <Typography variant="body2" color="text.secondary">
          Score: {state.score}
        </Typography>
      </Box>
      <LinearProgress variant="determinate" value={progress} />
    </Box>
  );
}

export default Progress;
```

5. Create `src/components/Question.js`:
```javascript
import React from 'react';
import {
  Card,
  CardContent,
  Typography,
  RadioGroup,
  FormControlLabel,
  Radio,
  Button
} from '@mui/material';
import { useQuiz } from '../contexts/QuizContext';
import { questions } from '../data/questions';

function Question() {
  const { state, dispatch } = useQuiz();
  const [selectedAnswer, setSelectedAnswer] = React.useState(null);
  const currentQuestion = questions[state.currentQuestion];

  const handleAnswer = () => {
    if (selectedAnswer !== null) {
      dispatch({
        type: 'ANSWER_QUESTION',
        payload: {
          questionId: currentQuestion.id,
          answer: selectedAnswer,
          correct: selectedAnswer === currentQuestion.correctAnswer
        }
      });

      setSelectedAnswer(null);

      if (state.currentQuestion === questions.length - 1) {
        dispatch({ type: 'FINISH_QUIZ' });
      }
    }
  };

  return (
    <Card sx={{ minWidth: 275, maxWidth: 600, mx: 'auto', my: 2 }}>
      <CardContent>
        <Typography variant="h6" gutterBottom>
          {currentQuestion.question}
        </Typography>
        <RadioGroup
          value={selectedAnswer}
          onChange={(e) => setSelectedAnswer(Number(e.target.value))}
        >
          {currentQuestion.options.map((option, index) => (
            <FormControlLabel
              key={index}
              value={index}
              control={<Radio />}
              label={option}
            />
          ))}
        </RadioGroup>
        <Button
          variant="contained"
          color="primary"
          fullWidth
          onClick={handleAnswer}
          disabled={selectedAnswer === null}
          sx={{ mt: 2 }}
        >
          Next Question
        </Button>
      </CardContent>
    </Card>
  );
}

export default Question;
```

6. Create `src/components/Results.js`:
```javascript
import React from 'react';
import {
  Card,
  CardContent,
  Typography,
  Button,
  List,
  ListItem,
  ListItemText
} from '@mui/material';
import { useQuiz } from '../contexts/QuizContext';
import { questions } from '../data/questions';

function Results() {
  const { state, dispatch } = useQuiz();
  const percentage = (state.score / questions.length) * 100;

  const handleRetry = () => {
    dispatch({ type: 'RESET_QUIZ' });
  };

  return (
    <Card sx={{ minWidth: 275, maxWidth: 600, mx: 'auto', my: 2 }}>
      <CardContent>
        <Typography variant="h5" gutterBottom>
          Quiz Results
        </Typography>
        <Typography variant="h6" color="primary" gutterBottom>
          Score: {state.score} out of {questions.length} ({percentage.toFixed(1)}%)
        </Typography>

        <List>
          {state.answers.map((answer, index) => (
            <ListItem key={index}>
              <ListItemText
                primary={questions[index].question}
                secondary={
                  <>
                    Your answer: {questions[index].options[answer.answer]}
                    <br />
                    {answer.correct ? (
                      <Typography component="span" color="success.main">
                        Correct!
                      </Typography>
                    ) : (
                      <Typography component="span" color="error.main">
                        Incorrect. Correct answer: {
                          questions[index].options[questions[index].correctAnswer]
                        }
                      </Typography>
                    )}
                  </>
                }
              />
            </ListItem>
          ))}
        </List>

        <Button
          variant="contained"
          color="primary"
          fullWidth
          onClick={handleRetry}
          sx={{ mt: 2 }}
        >
          Try Again
        </Button>
      </CardContent>
    </Card>
  );
}

export default Results;
```

7. Create `src/pages/Home.js`:
```javascript
import React from 'react';
import {
  Container,
  Typography,
  Button,
  Paper,
  List,
  ListItem,
  ListItemIcon,
  ListItemText
} from '@mui/material';
import {
  Timer as TimerIcon,
  QuestionAnswer as QuestionIcon,
  Score as ScoreIcon
} from '@mui/icons-material';
import { useQuiz } from '../contexts/QuizContext';
import { questions } from '../data/questions';

function Home() {
  const { dispatch } = useQuiz();

  const handleStart = () => {
    dispatch({ type: 'START_QUIZ' });
  };

  return (
    <Container maxWidth="sm">
      <Paper sx={{ p: 3, mt: 4 }}>
        <Typography variant="h4" gutterBottom>
          React Quiz
        </Typography>
        <Typography variant="body1" paragraph>
          Test your knowledge of React.js with this interactive quiz!
        </Typography>

        <List>
          <ListItem>
            <ListItemIcon>
              <QuestionIcon />
            </ListItemIcon>
            <ListItemText
              primary={`${questions.length} Questions`}
              secondary="Multiple choice questions"
            />
          </ListItem>
          <ListItem>
            <ListItemIcon>
              <TimerIcon />
            </ListItemIcon>
            <ListItemText
              primary="5 Minutes"
              secondary="Time limit for the quiz"
            />
          </ListItem>
          <ListItem>
            <ListItemIcon>
              <ScoreIcon />
            </ListItemIcon>
            <ListItemText
              primary="Instant Results"
              secondary="Get your score immediately"
            />
          </ListItem>
        </List>

        <Button
          variant="contained"
          color="primary"
          size="large"
          fullWidth
          onClick={handleStart}
          sx={{ mt: 2 }}
        >
          Start Quiz
        </Button>
      </Paper>
    </Container>
  );
}

export default Home;
```

8. Create `src/pages/QuizPage.js`:
```javascript
import React from 'react';
import { Container } from '@mui/material';
import Question from '../components/Question';
import Progress from '../components/Progress';
import Timer from '../components/Timer';
import { useQuiz } from '../contexts/QuizContext';
import Results from '../components/Results';

function QuizPage() {
  const { state } = useQuiz();

  if (!state.quizStarted) {
    return null;
  }

  if (state.quizFinished) {
    return <Results />;
  }

  return (
    <Container maxWidth="md">
      <Timer />
      <Progress />
      <Question />
    </Container>
  );
}

export default QuizPage;
```

9. Update `src/App.js`:
```javascript
import React from 'react';
import { CssBaseline, Container } from '@mui/material';
import { QuizProvider } from './contexts/QuizContext';
import Home from './pages/Home';
import QuizPage from './pages/QuizPage';

function App() {
  return (
    <QuizProvider>
      <CssBaseline />
      <Container>
        <Home />
        <QuizPage />
      </Container>
    </QuizProvider>
  );
}

export default App;
```

## 6. Expected Output

Start the development server:
```bash
npm start
```

Visit http://localhost:3000 to see:
1. Home page with quiz instructions
2. Interactive quiz interface
3. Timer countdown
4. Progress tracking
5. Results page with score

## 7. Practice Exercises
1. Add more questions
2. Implement different question types
3. Add difficulty levels
4. Create a leaderboard
5. Add animations

## 8. Verification and Validation
- [ ] Quiz starts correctly
- [ ] Questions display properly
- [ ] Timer works
- [ ] Answers are tracked
- [ ] Results show correctly

## 9. Common Issues and Troubleshooting
| Issue | Solution |
|-------|----------|
| Timer not updating | Check useEffect |
| Score incorrect | Verify answer logic |
| State not updating | Check dispatch calls |
| Components not rendering | Check context |

## 10. Additional Resources
- [React Documentation](https://reactjs.org/)
- [Material-UI Documentation](https://mui.com/)
- [React Context API](https://reactjs.org/docs/context.html)
- [React Hooks](https://reactjs.org/docs/hooks-intro.html)
