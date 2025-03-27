# Project Architecture & Workflow

## Architecture Overview
This project is a React Todo App that uses Context API for state management. It consists of:

### Components (UI Elements)
- **TodoForm.jsx** → Handles adding new todos
- **TodoItem.jsx** → Displays and manages individual todos
- **index.jsx** → Centralizes exports for easy imports

### Context API for Global State Management
- **TodoContext.js** → Defines and provides the global todo state

### Main Application
- **App.jsx** → Manages todos, provides state via TodoContext, and renders components

## Workflow
### Adding a Todo
1. User types a task in **TodoForm.jsx** and submits.
2. The todo is added to the global todos state via `addTodo`.

### Updating a Todo
- **TodoItem.jsx** allows editing and updates the state via `updateTodo`.

### Deleting a Todo
- Clicking the ❌ button removes the todo from `todos` via `deleteTodo`.

### Toggling Completion
- Checking the checkbox updates completed status using `toggleComplete`.

### Persistent Storage
- `useEffect` syncs todos with `localStorage`.

## Line-by-Line Explanation
### 1. TodoContext.js (Context API for Global State)
This file creates and manages the global todo state.
```jsx
import { createContext, useContext } from "react";
```
- `createContext` is used to create a new React Context for the todo state.
- `useContext` allows consuming the context inside components.

```jsx
export const TodoContext = createContext({
    todos: [
        {
            id: 1,
            todo: 'Todo Msg',
            completed: false,
        }
    ],
    addTodo: (todo) => { },
    updateTodo: (id, todo) => { },
    deleteTodo: (id) => { },
    toggleComplete: (id) => { },
});
```
- Defines a default state for the `todos` array with a sample todo.
- `addTodo`, `updateTodo`, `deleteTodo`, and `toggleComplete` are placeholder functions.

```jsx
export const useTodo = () => {
    return useContext(TodoContext);
};
```
- `useTodo` is a custom hook to consume the `TodoContext` easily.

```jsx
export const Todoprovider = TodoContext.Provider;
```
- `Todoprovider` acts as a provider to wrap the components needing access to `TodoContext`.

### 2. index.js (Centralizing Context Exports)
```jsx
export { TodoContext, Todoprovider, useTodo } from "./TodoContext";
```
- This re-exports everything from `TodoContext.js` for easy imports.

### 3. TodoForm.jsx (Adding New Todos)
Handles the form where users enter new tasks.

```jsx
import React, { useState } from 'react';
import { useTodo } from '../Contexts';
```
- Imports `useState` for managing input field state.
- Imports `useTodo` to access the `addTodo` function from context.

```jsx
function TodoForm() {
    const [todo, setTodo] = useState("");
    const { addTodo } = useTodo();
```
- `todo` stores the input text.
- `addTodo` is fetched from context to add new tasks.

```jsx
    const add = (e) => {
        e.preventDefault();
        if (!todo) return;

        addTodo({
            todo,
            completed: false
        });

        setTodo("");
    };
```
- Prevents form submission from reloading the page.
- Calls `addTodo` with the input task and resets the field.

### 4. TodoItem.jsx (Displaying & Managing Todos)
Each todo item is displayed here.

```jsx
import React, { useState } from 'react';
import { useTodo } from '../Contexts';
```
- `useState` manages editable state.
- `useTodo` is used to access global actions like `updateTodo`.

```jsx
function TodoItem({ todo }) {
    const { updateTodo, deleteTodo, toggleComplete } = useTodo();
    const [todoMsg, setTodoMsg] = useState(todo.todo);
    const [isTodoEditable, setIsTodoEditable] = useState(false);
```
- `todoMsg` stores the current text of the todo.
- `isTodoEditable` tracks if the todo is in edit mode.

### 5. App.jsx (Main File)
Manages the entire app and provides context.

```jsx
import { useEffect, useState } from 'react';
import { Todoprovider } from './Contexts';
import { TodoForm, TodoItem } from './Components';
```
- Imports `Todoprovider` to wrap the app in `TodoContext`.
- Imports `TodoForm` and `TodoItem`.

```jsx
const [todos, setTodos] = useState([]);
```
- Stores the list of todos.

```jsx
useEffect(() => {
    const todos = JSON.parse(localStorage.getItem("todos"));
    if (todos) setTodos(todos);
}, []);
```
- Loads todos from `localStorage` on startup.

```jsx
useEffect(() => {
    localStorage.setItem("todos", JSON.stringify(todos));
}, [todos]);
```
- Saves todos whenever they change.

---

## Diagram: Todo Flow in Files
```
User Interaction (UI)
       ⬇️
-------------------------------------
|           Components               |
-------------------------------------
|  TodoForm.jsx   |  TodoItem.jsx    |
-------------------------------------
      ⬇️                 ⬇️
-------------------------------------
|      useTodo() from Contexts       |
-------------------------------------
      ⬇️
-------------------------------------
|      TodoContext.js (Global State)  |
-------------------------------------
      ⬇️
-------------------------------------
|      App.jsx (Manages State)       |
-------------------------------------
      ⬇️
-------------------------------------
|    LocalStorage (Persistence)      |
-------------------------------------
```

### Flow Explanation:
#### User Interaction
- The user adds, edits, deletes, or completes todos through the UI (**TodoForm.jsx, TodoItem.jsx**).

#### Component Level
- **TodoForm.jsx** handles new todo submissions.
- **TodoItem.jsx** displays todos, allows updates, and toggles completion.

#### Context API (**TodoContext.js**)
- `useTodo()` provides access to functions (`addTodo`, `updateTodo`, `deleteTodo`, `toggleComplete`).
- The context maintains the global state of todos.

#### State Management (**App.jsx**)
- `App.jsx` manages the `todos` array and updates it using `setTodos()`.
- The context provider (`Todoprovider`) passes state and functions to all components.

#### Local Storage (Persistence Layer)
- Todos are loaded from `localStorage` when the app starts.
- Todos are saved in `localStorage` every time they change.

