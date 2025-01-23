# React Hooks

### **1. Basic State Management**

### **`useState`**

- **Purpose**: Manages simple state values (e.g., numbers, strings, booleans, arrays, objects).
- **How it works**: React gives you two things:
    - The current value of the state (e.g., `count`).
    - A function to update the state (e.g., `setCount`).
- **Rules**:
    - Always call `setState` (like `setCount`) to update the state.
    - State updates are **asynchronous**, meaning you won't see changes immediately.
- **Enhanced Example**: Form with multiple inputs.
    
    ```jsx
    import React, { useState } from "react";
    
    function Form() {
      const [formData, setFormData] = useState({ name: "", email: "" });
    
      const handleChange = (e) => {
        const { name, value } = e.target;
        setFormData({ ...formData, [name]: value }); // Merge new input into the object
      };
    
      return (
        <form>
          <input
            type="text"
            name="name"
            placeholder="Name"
            value={formData.name}
            onChange={handleChange}
          />
          <input
            type="email"
            name="email"
            placeholder="Email"
            value={formData.email}
            onChange={handleChange}
          />
          <p>{JSON.stringify(formData)}</p>
        </form>
      );
    }
    ```
    

---

### **`useReducer`**

- **Purpose**: Manages state logic with complex rules or multiple actions.
- **How it works**: You define a **reducer function** that tells React how to update the state based on an "action."
- **Advantages**:
    - Clearer logic for managing state changes.
    - Useful for larger components with multiple state transitions.
- **Enhanced Example**: A to-do list.
    
    ```jsx
    import React, { useReducer } from "react";
    
    const initialState = [];
    const reducer = (state, action) => {
      switch (action.type) {
        case "add":
          return [...state, { id: Date.now(), text: action.payload }];
        case "remove":
          return state.filter((item) => item.id !== action.payload);
        default:
          return state;
      }
    };
    
    function TodoApp() {
      const [todos, dispatch] = useReducer(reducer, initialState);
      const [input, setInput] = React.useState("");
    
      const addTodo = () => {
        if (input) {
          dispatch({ type: "add", payload: input });
          setInput("");
        }
      };
    
      return (
        <div>
          <input value={input} onChange={(e) => setInput(e.target.value)} />
          <button onClick={addTodo}>Add</button>
          <ul>
            {todos.map((todo) => (
              <li key={todo.id}>
                {todo.text}{" "}
                <button onClick={() => dispatch({ type: "remove", payload: todo.id })}>Delete</button>
              </li>
            ))}
          </ul>
        </div>
      );
    }
    ```
    

---

### **2. Side Effects**

### **`useEffect`**

- **Purpose**: Allows you to "do something" after your component renders or when certain data changes.
- **How it works**:
    - You provide a function that React runs after rendering.
    - You can control when it runs using a dependency array (`[]`).
    - Common use cases: Fetching data, adding event listeners, timers, or syncing external data.
- **Rules**:
    - Place dependencies in the dependency array to avoid unnecessary re-runs.
    - Return a **cleanup function** if your effect sets up something (e.g., event listeners).
- **Enhanced Example**: Fetch data from an API.
    
    ```jsx
    import React, { useState, useEffect } from "react";
    
    function DataFetcher() {
      const [data, setData] = useState([]);
      const [loading, setLoading] = useState(true);
    
      useEffect(() => {
        fetch("https://jsonplaceholder.typicode.com/posts")
          .then((response) => response.json())
          .then((data) => {
            setData(data);
            setLoading(false);
          });
      }, []); // Empty array means this runs only once (on mount)
    
      return (
        <div>
          {loading ? <p>Loading...</p> : data.map((item) => <p key={item.id}>{item.title}</p>)}
        </div>
      );
    }
    ```
    

---

### **`useLayoutEffect`**

- **Purpose**: Runs code before the browser updates the screen, allowing you to measure or modify the DOM.
- **Difference from `useEffect`**:
    - `useEffect` runs **after** the screen updates.
    - `useLayoutEffect` runs **before** the screen updates.
- **When to use**: Only for things like animations, measuring DOM size/position, or making adjustments before the user sees the UI.
- **Example**: Measure the size of a box.
    
    ```jsx
    import React, { useLayoutEffect, useRef, useState } from "react";
    
    function MeasureBox() {
      const boxRef = useRef();
      const [boxSize, setBoxSize] = useState({ width: 0, height: 0 });
    
      useLayoutEffect(() => {
        const { width, height } = boxRef.current.getBoundingClientRect();
        setBoxSize({ width, height });
      });
    
      return (
        <div>
          <div ref={boxRef} style={{ width: "200px", height: "100px", background: "blue" }} />
          <p>Box size: {JSON.stringify(boxSize)}</p>
        </div>
      );
    }
    ```
    

---

### **3. Contextual Data**

### **`useContext`**

- **Purpose**: Avoids "prop drilling" by sharing data across deeply nested components without manually passing it down.
- **How it works**:
    - Create a **context**.
    - Wrap the parent component with a **provider** that gives a value to the context.
    - Use `useContext` in child components to get the value.
- **Enhanced Example**: User authentication.
    
    ```jsx
    import React, { createContext, useContext, useState } from "react";
    
    const AuthContext = createContext();
    
    function AuthProvider({ children }) {
      const [user, setUser] = useState(null);
      return (
        <AuthContext.Provider value={{ user, login: () => setUser("Fares"), logout: () => setUser(null) }}>
          {children}
        </AuthContext.Provider>
      );
    }
    
    function LoginButton() {
      const { user, login, logout } = useContext(AuthContext);
      return (
        <button onClick={user ? logout : login}>
          {user ? `Logout (${user})` : "Login"}
        </button>
      );
    }
    
    function App() {
      return (
        <AuthProvider>
          <LoginButton />
        </AuthProvider>
      );
    }
    ```
    

---

### **4. DOM Interaction**

### **`useRef`**

- **Purpose**: Access DOM elements or keep a mutable value that doesn’t cause re-renders.
- **Example**: Track previous state value.
    
    ```jsx
    import React, { useState, useRef, useEffect } from "react";
    
    function PreviousValueTracker() {
      const [value, setValue] = useState("");
      const previousValue = useRef("");
    
      useEffect(() => {
        previousValue.current = value; // Update the previous value
      }, [value]);
    
      return (
        <div>
          <input
            value={value}
            onChange={(e) => setValue(e.target.value)}
            placeholder="Type something"
          />
          <p>Current: {value}</p>
          <p>Previous: {previousValue.current}</p>
        </div>
      );
    }
    
    ```
    

---

### **5. Performance Optimization**

### **`useMemo`**

- **Purpose**: Memorizes expensive calculations to avoid re-running them unnecessarily.
- **Example**: Filtering data.
    
    ```jsx
    import React, { useState, useMemo } from "react";
    
    function FilterList() {
      const [query, setQuery] = useState("");
      const items = ["apple", "banana", "orange", "grape"];
    
      const filteredItems = useMemo(() => {
        console.log("Filtering...");
        return items.filter((item) => item.includes(query));
      }, [query]);
    
      return (
        <div>
          <input value={query} onChange={(e) => setQuery(e.target.value)} placeholder="Search..." />
          <ul>
            {filteredItems.map((item, index) => (
              <li key={index}>{item}</li>
            ))}
          </ul>
        </div>
      );
    }
    
    ```
    

### **`useCallback`**

- **Purpose**: Memorizes functions to prevent them from being recreated unnecessarily.
- **Example**: Passing stable callbacks to child components.
    
    ```jsx
    jsx
    C
    import React, { useState, useCallback } from "react";
    
    function Button({ onClick, label }) {
      return <button onClick={onClick}>{label}</button>;
    }
    
    function Counter() {
      const [count, setCount] = useState(0);
    
      const increment = useCallback(() => setCount((c) => c + 1), []);
      const decrement = useCallback(() => setCount((c) => c - 1), []);
    
      return (
        <div>
          <p>Count: {count}</p>
          <Button onClick={increment} label="Add 1" />
          <Button onClick={decrement} label="Subtract 1" />
        </div>
      );
    }
    
    ```