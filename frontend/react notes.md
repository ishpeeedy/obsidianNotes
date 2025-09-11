Here are the comprehensive study notes for you, incorporating all information from the provided sources and our conversation history, presented in Markdown format.

---

# React Study Notes: `useState`, `useEffect`, and State Management Best Practices

## I. React Fundamentals

### A. What is React?

- React is a **JavaScript library** (not a framework) used for easily building and arranging **user interfaces (UIs)** for web applications.
- It focuses on creating web applications using **components**, which are self-contained, reusable blocks of code.
- React uses a **Virtual DOM**, a lightweight version of the real DOM, to efficiently update web pages by only applying specific changes to the real DOM, reducing rendering performance overhead. This means you won't see full page reloads for dynamic updates.
- A solid understanding of JavaScript, including ES6+ features like arrow functions, objects, classes, and arrays, along with HTML and CSS, is necessary to learn React effectively.

### B. Components

- **Components are reusable building blocks** that can include a combination of JavaScript and HTML-like code (JSX).
- React components are typically **function-based components** (also known as functional components), which are the recommended way for new projects. Class components are older and less common in new projects.

#### Syntax: Function-based Component

```
// components/Header.jsx
import React from 'react'; // Not always explicitly needed for newer React versions but good practice

function Header() {
  // Component logic
  return (
    <header>
      <h1>My Website</h1>
      <nav>
        <ul>
          <li><a href="#">Home</a></li>
          <li><a href="#">About</a></li>
        </ul>
      </nav>
    </header>
  );
}

export default Header; // Export to be used elsewhere
```

- A component function must **return a single element**, but it can contain multiple children elements within that single parent.
    - If you need to return multiple top-level elements without adding an extra `<div>` to the DOM, you can wrap them in a **React Fragment** (`<>...</>`).

#### Reusability

- The core idea of React is **reusing components**. You write code once and use it multiple times, eliminating repetition.
- Example: Importing and using a `Header` component in `App.jsx`:

```
// App.jsx
import Header from './components/Header.jsx'; //
import Footer from './components/Footer.jsx'; //

function App() {
  return (
    <> {/* Using a Fragment to return multiple top-level components */}
      <Header />
      <main>
        {/* Main content goes here */}
        <p>Welcome to our application!</p>
      </main>
      <Footer />
    </>
  );
}

export default App;
```

- Components are isolated; each instance has its own state.

### C. JSX (JavaScript XML)

- JSX is a **syntax extension for JavaScript** that allows you to write HTML-like markup directly within your JavaScript files.
- It keeps rendering logic and content together in the same place (components).

#### Rules of JSX

1. **Return a single root element**: Components must return a single parent tag. You can use a `<div>` or a `Fragment` (`<>...</>`) if you don't want an extra DOM node.
    - **Example:**
        
        ```
        // Valid JSX: Wrapped in a div
        <div>
          <h1>Title</h1>
          <p>Paragraph</p>
        </div>
        
        // Valid JSX: Wrapped in a Fragment (invisible in DOM)
        <>
          <h1>Title</h1>
          <p>Paragraph</p>
        </>
        
        // Invalid JSX: Multiple root elements without a wrapper
        // <h1>Title</h1>
        // <p>Paragraph</p>
        ```
        
2. **Close all tags**: JSX requires all tags to be explicitly closed. Self-closing tags (like `<img>`, `<input>`, `<br>`) must end with `/>`, and wrapping tags (like `<li>`) must have an opening and closing tag.
    - **Example:**
        
        ```
        <img src="path/to/image.jpg" alt="Description" />
        <input type="text" />
        <li>Item text</li>
        <br />
        ```
        
3. **`camelCase` for most attributes**: HTML attributes in JSX are written in `camelCase`. The most common is `className` instead of `class`. `aria-*` and `data-*` attributes are exceptions and use dashes.
    - **Example:**
        
        ```
        <div className="my-class" tabIndex={0}> {/* `className` instead of `class` */}
          <label htmlFor="myInput">My Input:</label>
          <input id="myInput" onChange={handleChange} />
        </div>
        ```
        

#### Embedding JavaScript with Curly Braces `{}`

- You can embed any JavaScript expression directly into JSX by enclosing it in **curly braces `{}`**. This acts as a "window into JavaScript".
- This works for:
    - **Variables**: Displaying dynamic content.
        
        ```
        const name = 'Alice';
        return <h1>Hello, {name}!</h1>; //
        ```
        
    - **Function calls**: Executing JavaScript functions and displaying their return values.
        
        ```
        function formatGreeting() { return 'Good morning!'; }
        return <p>{formatGreeting()}</p>;
        ```
        
    - **Objects (e.g., for inline styles)**: When passing a JavaScript object as a prop, especially for inline styles, you use a double set of curly braces: `style={{ property: 'value' }}`. The outer `{}` indicates JavaScript, and the inner `{}` creates the JavaScript object.
        
        - Inline `style` properties are also written in `camelCase` (e.g., `backgroundColor` not `background-color`).
        
        ```
        return (
          <ul style={{ backgroundColor: 'black', color: 'pink' }}> {/* */}
            <li>Item 1</li>
          </ul>
        );
        ```
        

### D. Virtual DOM

- The Virtual DOM is a **lightweight, in-memory representation of the actual browser DOM**. It acts like a simplified map of your webpage.
- When state or props change, React first updates the Virtual DOM. It then **compares the new Virtual DOM with the previous one** (diffing algorithm) to identify only the changes.
- Only the **changed parts are efficiently updated in the real browser DOM**, rather than re-rendering the entire page. This makes React fast and performant.

### E. Rendering Process (Purity)

- React's rendering process involves two main phases:
    1. **Render Phase**: React calls your component functions. During this phase, components should act as **pure functions**.
        - **Purity Rules:**
            - **Same inputs, same output**: Given the same props and state, a component should always return the same JSX.
            - **No side effects**: Components should not change any objects or variables that existed _before_ rendering. This means no modifying global objects, no network requests, no modifying the DOM directly (outside of JSX), and no `useState` setters outside event handlers or effects.
            - It is fine to change variables and objects that you've _just_ **created while rendering**.
    2. **Commit Phase**: React applies the changes to the DOM (updates the browser's UI). Effects run after this phase.
- React's **Strict Mode** (in development) helps identify purity violations by intentionally re-rendering components and re-running effects (setup -> cleanup -> setup) once after their initial mount. This is a stress test to ensure proper cleanup and prevent bugs, and is not how effects run in production.

## II. State Management with `useState`

### A. Introduction to `useState`

- `useState` is a **React Hook** that allows functional components to "remember" component-specific data (called **state**). It's essentially a component's memory.
- It enables components to change what's on the screen in response to interactions.
- The `useState` hook returns an array with **exactly two items**:
    1. The **current stateful variable** (the remembered value).
    2. A **setter function** to update that state variable.

#### Syntax: `useState`

```
import { useState } from 'react'; //

function MyCounter() {
  // Declare a state variable 'count' and its setter 'setCount'
  const [count, setCount] = useState(0); // Initial state is 0

  function increment() {
    setCount(count + 1); // Update the state and triggers a re-render
  }

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>Increment</button>
    </div>
  );
}
```

- **Initial state**: You pass the initial value of the state variable as an argument to `useState()`. This can be any JavaScript value: number, string, boolean, object, or array.
- **State as a snapshot**: When you set state, it does not immediately change the state variable in the current render. Instead, it **triggers a re-render** of the component with the _new_ state value. The event handler itself still "sees" the state value from the render when it was created (a "snapshot").
- **Re-rendering**: Updating state schedules a new render. The component function will run again, and React will calculate the new UI based on the updated state.
- State is **isolated** between components; each component instance maintains its own state.

### B. Common Mistakes with `useState` (and their solutions)

#### 1. Incorrectly updating multiple times in a row

- **Problem:** If you call a setter function multiple times in the same event handler using the current state value (e.g., `setCount(count + 1); setCount(count + 1);`), React's batching of state updates means all calls in that single event will use the _same snapshot_ of `count` from the start of the render. So, `count` will only increase by 1, not 2.
    - **Example (Incorrect):**
        
        ```
        // Assuming count is 0
        function handleClick() {
          setCount(count + 1); // Schedules count to be 1 (uses count from current render, which is 0)
          setCount(count + 1); // Schedules count to be 1 (again, using initial count of 0)
        }
        // Result: count becomes 1
        ```
        
- **Solution: Use an Updater Function:** Pass a function to the setter instead of a value. This function receives the `previousState` (often named `prevCount`, `n`, `c`, or `f` by convention) as an argument, which is guaranteed to be the most up-to-date _pending_ state value. This allows for **safe updates based on the previous state**. All updater functions are added to a queue and run during the next render.
    - **Example (Correct):**
        
        ```
        // Assuming count is 0
        function handleClick() {
          setCount(prevCount => prevCount + 1); // Schedules count to be 1
          setCount(prevCount => prevCount + 1); // Schedules count to be 2 (using prevCount = 1)
        }
        // Result: count becomes 2
        ```
        
- **When to use Updater Functions:** They are crucial when you have multiple state updates in a row, or when updates are asynchronous, to ensure safe updates based on the previous state. In other scenarios, direct value updates (`setCount(count + 1)`) are generally fine.

#### 2. Incorrectly updating object state

- **Problem:** Objects in JavaScript are mutable, but **React state should be treated as immutable**. Directly modifying an object (e.g., `user.name = e.target.value`) or replacing it without copying all existing properties will not trigger a re-render or will lose data.
    - **Example (Incorrect):**
        
        ```
        const [user, setUser] = useState({ name: 'Tim', age: 30, city: 'London' });
        
        function handleNameChange(e) {
          // 🔴 Direct mutation - will not re-render and can lead to bugs
          // user.name = e.target.value;
          // 🔴 Overwrites the whole object, losing 'age' and 'city'
          setUser({ name: e.target.value });
        }
        ```
        
- **Solution: Create a new object with spread syntax `...object`:** Copy all properties from the previous object into a new one, then override only the properties you want to change.
    - **Example (Correct):**
        
        ```
        const [user, setUser] = useState({ name: 'Tim', age: 30, city: 'London' });
        
        function handleNameChange(e) {
          setUser(prevUser => ({ // Using updater function and returning an object wrapped in parentheses
            ...prevUser, // Copy all existing properties from 'prevUser'
            name: e.target.value, // Override the 'name' property
          }));
        }
        ```
        
- **Single event handler for multiple fields:** You can use a dynamic property name with square brackets `[e.target.name]` to update different fields with one handler. This requires giving your input elements `name` attributes that match the state object's keys.
    - **Example:**
        
        ```
        const [form, setForm] = useState({ firstName: '', lastName: '', email: '' }); //
        
        function handleChange(e) {
          setForm(prevForm => ({ //
            ...prevForm,
            [e.target.name]: e.target.value, // e.target.name will be 'firstName', 'lastName', or 'email'
          }));
        }
        
        return (
          <input name="firstName" value={form.firstName} onChange={handleChange} placeholder="First Name" /> //
          // ... and so on for other inputs
        );
        ```
        
- **Updating deeply nested objects:** If you need to update a property within a nested object, you must copy all objects in the path from the property you're changing up to the top-level state object. The spread syntax is "shallow".
    - **Example:**
        
        ```
        const [person, setPerson] = useState({
          name: 'Niki',
          artwork: { title: 'Blue Nana', city: 'Hamburg' }
        });
        
        function handleCityChange(e) {
          setPerson(prevPerson => ({
            ...prevPerson, // Copy the top-level 'person' object
            artwork: { // Create a new 'artwork' object
              ...prevPerson.artwork, // Copy existing properties of 'artwork'
              city: e.target.value // Override 'city'
            }
          }));
        }
        ```
        
- **`Immer` for concise updates:** For deeply nested state or frequent object updates, Immer is a popular library that allows you to write mutating-style code on a special `draft` object, and it automatically produces the immutable copies for you behind the scenes.
    - **Example with Immer:**
        
        ```
        import { useImmer } from 'use-immer'; //
        
        function MyComponent() {
          const [person, updatePerson] = useImmer({ //
            name: 'Niki',
            artwork: { title: 'Blue Nana', city: 'Hamburg' }
          });
        
          function handleCityChange(e) {
            updatePerson(draft => { //
              draft.artwork.city = e.target.value; // Looks like mutation, but Immer handles immutability
            });
          }
          // ...
        }
        ```
        

#### 3. Incorrectly updating array state

- **Problem:** Similar to objects, arrays in React state should be treated as immutable. Directly mutating an array (e.g., `foods.push()`, `foods.splice()`) will not trigger a re-render or will cause shared state bugs. You should only mutate objects you have just created, not ones already in state.
- **Solution: Create a new array with spread syntax and non-mutating methods:** Use methods like `map()`, `filter()`, `slice()`, or the spread syntax `[...array]` to create a new array with the desired changes, then pass the new array to the setter. For methods like `reverse()` or `sort()` which are mutating, you must first copy the array.
    - **Example (Adding an item):**
        
        ```
        const [foods, setFoods] = useState(['Apple', 'Orange']);
        const newFood = 'Banana';
        setFoods(prevFoods => [...prevFoods, newFood]); // Creates a new array with newFood appended
        ```
        
    - **Example (Removing an item):**
        
        ```
        const [tasks, setTasks] = useState(['Eat', 'Sleep', 'Repeat']);
        const indexToDelete = 1; // Delete 'Sleep'
        setTasks(prevTasks => prevTasks.filter((_, i) => i !== indexToDelete)); // Filters out the item at index
        ```
        
    - **Example (Updating an object inside an array):** When an array contains objects, and you need to update a property of one of those objects, you must create a _new_ object with changes, and map over the array to return a _new_ array with the updated object.
        
        ```
        const [list, setList] = useState([
          { id: 0, title: 'Big Bellies', seen: false },
          { id: 1, title: 'Lunar Landscape', seen: false }
        ]);
        
        function handleToggleSeen(artworkId, nextSeen) {
          setList(list.map(artwork => {
            if (artwork.id === artworkId) {
              // Create a *new* object with changes
              return { ...artwork, seen: nextSeen };
            } else {
              // No changes to this artwork
              return artwork;
            }
          }));
        }
        ```
        
- **`Immer` for concise updates:** For deeply nested state or frequent array/object updates, Immer is a popular library that allows you to write mutating-style code on a special `draft` object, and it automatically produces the immutable copies for you behind the scenes.
    - **Example with Immer:**
        
        ```
        import { useImmer } from 'use-immer'; //
        
        function MyComponent() {
          const [list, updateList] = useImmer([ /* ... initialList */ ]); //
        
          function handleToggleSeen(artworkId, nextSeen) {
            updateList(draft => { //
              const artwork = draft.find(a => a.id === artworkId);
              artwork.seen = nextSeen; // Looks like mutation, but Immer handles immutability
            });
          }
          // ...
        }
        ```
        

#### 4. Initializing state with an object that can be `undefined` or `null`

- **Problem:** If you initialize an object state with `undefined` or leave it empty (`useState()`), and then attempt to access properties on it (e.g., `post.title`) before it's populated with actual data (e.g., from a fetch request), you will get an "cannot read properties of undefined" error. This is common with data fetching, as `useEffect` runs _after_ the initial render.
- **Solution:**
    - **Initialize with `null`**: It's better to explicitly initialize the state with `null` to indicate no data. `undefined` is more accidental, while `null` is deliberate.
    - **Optional chaining**: Use optional chaining (`post?.title`) to prevent errors if the object might be `null` or `undefined`.
    - **Loading state and Conditional Rendering**: Introduce a separate `isLoading` state variable to explicitly manage the loading phase and use conditional rendering to only display parts of the UI that depend on the data _after_ it has loaded.
    - **Example:**
        
        ```
        import { useState, useEffect } from 'react';
        
        function BlogPost({ postId }) {
          const [post, setPost] = useState(null); // Explicitly null
          const [isLoading, setIsLoading] = useState(true); //
          const [error, setError] = useState(null); //
        
          useEffect(() => {
            setIsLoading(true); //
            setError(null); //
            fetch(`/api/posts/${postId}`)
              .then(response => {
                if (!response.ok) throw new Error('Failed to fetch post');
                return response.json();
              })
              .then(data => setPost(data)) //
              .catch(err => setError(err.message)) //
              .finally(() => setIsLoading(false)); //
          }, [postId]);
        
          if (isLoading) {
            return <p>Loading...</p>; //
          }
          if (error) {
            return <p style={{ color: 'red' }}>Error: {error}</p>; //
          }
          if (!post) { // Should ideally not happen with proper loading/error handling
            return <p>No post found.</p>;
          }
        
          return (
            <div>
              <h2>{post.title}</h2>
              <p>{post.body}</p>
            </div>
          );
        }
        ```
        
- **Using TypeScript with `useState` and objects:** TypeScript can infer types for primitive values (boolean, string, number) from their initial values (e.g., `useState(true)` infers `boolean`). However, for objects that are initially `null` but later become a specific type, you need to explicitly define the type using generic angled brackets (`useState<Type | null>(null)`).
    - **Example (TypeScript):**
        
        ```
        type Post = { //
          title: string;
          body: string;
          tags?: string[]; //
        };
        
        function BlogPost({ postId }: { postId: number }) {
          // Explicitly define that 'post' can be 'Post' or 'null'
          const [post, setPost] = useState<Post | null>(null);
          // ... rest of the component
        }
        ```
        

#### 5. Creating separate state variables for many inputs

- **Problem:** For forms with many inputs (e.g., first name, last name, email, password), creating a separate `useState` variable for each input can lead to a lot of boilerplate code.
- **Solution: Use one object for the whole form state:** Consolidate all related form data into a single object within one `useState` hook. This makes the state structure cleaner and easier to manage with a single change handler (as shown in point 2.c above).
    - **Example:**
        
        ```
        function MyForm() {
          const [formData, setFormData] = useState({ //
            firstName: '',
            lastName: '',
            email: '',
            password: '',
          });
        
          const handleChange = (e) => { //
            setFormData(prevFormData => ({ //
              ...prevFormData,
              [e.target.name]: e.target.value, // Dynamic key based on input's 'name' attribute
            }));
          };
        
          return (
            <form>
              <input name="firstName" value={formData.firstName} onChange={handleChange} placeholder="First Name" /> //
              <input name="lastName" value={formData.lastName} onChange={handleChange} placeholder="Last Name" />
              {/* ... other inputs */}
            </form>
          );
        }
        ```
        

#### 6. Redundant state (Derivable state)

- **Problem:** Storing state that can be **calculated or derived from existing props or other state variables** is unnecessary and can lead to bugs if the redundant state gets out of sync. This is also known as "mirroring props in state" when a state variable is initialized from a prop but then doesn't update if the prop changes.
- **Solution: Derive the value during rendering:** Perform the calculation directly within the component's function body. This code will automatically re-run on every render when its dependencies (props or other state) change.
    - **Example (Incorrect: Redundant `fullName` state):**
        
        ```
        function UserForm() {
          const [firstName, setFirstName] = useState('');
          const [lastName, setLastName] = useState('');
          const [fullName, setFullName] = useState(''); // 🔴 Redundant state
        
          function handleFirstNameChange(e) {
            setFirstName(e.target.value);
            setFullName(e.target.value + ' ' + lastName); // Difficult to keep in sync
          }
          // ...
        }
        ```
        
    - **Example (Correct: Derive `fullName` in render):**
        
        ```
        function UserForm() {
          const [firstName, setFirstName] = useState(''); //
          const [lastName, setLastName] = useState(''); //
        
          // ✅ Derived in render - automatically updates when firstName or lastName changes
          const fullName = firstName + ' ' + lastName; //
        
          function handleFirstNameChange(e) {
            setFirstName(e.target.value); //
          }
          function handleLastNameChange(e) {
            setLastName(e.target.value); //
          }
        
          return <p>Your name: <b>{fullName}</b></p>;
        }
        ```
        
- **Avoid mirroring props in state**: If a component receives a prop and you initialize a state variable with it, subsequent changes to that prop from the parent will _not_ update your component's internal state. Instead, use the prop directly or create a constant for it. If you _intend_ to ignore updates to a prop, name it with `initial` or `default` (e.g., `initialColor`) to make your intent clear.
- **Caching expensive computations**: If the derivation is computationally expensive (e.g., filtering/looping thousands of items), you can use `useMemo` to cache the result, preventing re-computation on every render unless its dependencies change.

#### 7. Avoiding contradictions in state

- **Problem:** Using multiple boolean state variables (e.g., `isSending`, `isSent`) can lead to "impossible" states where logical contradictions occur (e.g., both `isSending` and `isSent` are `true` simultaneously). This makes components harder to debug and prone to bugs.
- **Solution: Consolidate into a single, well-defined `status` state variable:** Replace multiple booleans with a single state variable that can take on a limited set of valid, mutually exclusive string values (e.g., `'typing'`, `'sending'`, `'sent'`). Derive booleans from this `status` variable if needed for rendering or logic.
    - **Example (Incorrect: Contradictory booleans):**
        
        ```
        const [isSending, setIsSending] = useState(false); //
        const [isSent, setIsSent] = useState(false);     //
        // Problem: What if both are true?
        ```
        
    - **Example (Correct: Single `status` string):**
        
        ```
        const [status, setStatus] = useState('typing'); // 'typing', 'sending', 'sent'
        // Derive booleans from status
        const isSending = status === 'sending';
        const isSent = status === 'sent';
        ```
        

#### 8. Duplication in state

- **Problem:** Storing the same data in multiple state variables, or within nested objects, makes it difficult to keep them synchronized. For example, storing a `selectedItem` object when that object already exists within an `items` array.
- **Solution: Store only the essential identifier and derive the full object:** If an object is already part of another array or object in state, store only its ID (or another unique identifier) in the state variable, and then find/derive the full object during rendering.
    - **Example (Incorrect: Duplicated `selectedItem` object):**
        
        ```
        const initialItems = [{ title: 'pretzels', id: 0 }, { title: 'granola bar', id: 2 }];
        function Menu() {
          const [items, setItems] = useState(initialItems);
          const [selectedItem, setSelectedItem] = useState(items); // 🔴 selectedItem duplicates data
          // ... if an item's title changes in 'items', 'selectedItem.title' won't update
        }
        ```
        
    - **Example (Correct: Store `selectedId` and derive `selectedItem`):**
        
        ```
        const initialItems = [{ title: 'pretzels', id: 0 }, { title: 'granola bar', id: 2 }];
        function Menu() {
          const [items, setItems] = useState(initialItems); //
          const [selectedId, setSelectedId] = useState(0); // ✅ Store only the ID
        
          // Derive selectedItem during render
          const selectedItem = items.find(item => item.id === selectedId);
        
          return <p>You picked {selectedItem?.title} .</p>; // Add optional chaining to derived item
        }
        ```
        

#### 9. Deeply nested state

- **Problem:** Deeply hierarchical state (objects within objects, arrays within arrays) is verbose and inconvenient to update, as it requires copying many levels of objects/arrays.
- **Solution: Flatten (normalize) the state:** Restructure the data to be "flat", similar to a database table. Instead of nested objects containing their children, each object stores an array of its children's IDs, and then you have a mapping from ID to the full object. This makes updates easier and avoids duplication.
    - **Example (Incorrect: Deeply nested travel plan):**
        
        ```
        export const initialTravelPlan = { //
          id: 0, title: '(Root)',
          childPlaces: [{
            id: 1, title: 'Earth',
            childPlaces: [{
              id: 2, title: 'Africa',
              childPlaces: [{ id: 3, title: 'Botswana', childPlaces: [] }]
            }]
          }]
        };
        // Updating 'Botswana' title would require copying 'Africa', 'Earth', and '(Root)'
        ```
        
    - **Example (Correct: Flat/Normalized travel plan):**
        
        ```
        export const initialTravelPlan = { //
          0: { id: 0, title: '(Root)', childIds: },
          1: { id: 1, title: 'Earth', childIds: },
          // ... and so on, with each place referenced by ID
        };
        // Updating 'Botswana' now only requires updating the 'Botswana' object and its parent's childIds array.
        ```
        

### C. `useState` vs `useReducer`

- `useReducer` is another React Hook for state management, particularly useful for more complex scenarios.

#### When to use `useReducer`:

- **Complex state update logic**: When state updates involve multiple values, or multiple event handlers modify state in similar ways.
- **Decoupling logic**: It allows you to **consolidate all state update logic outside of your component** in a single function called a `reducer`, making the component cleaner.
- **Better debugging**: With `useReducer`, you can log every dispatched action and state update, providing a clear history of _why_ state changed.
- **Testability**: Reducer functions are pure and don't depend on the component, making them easier to test in isolation.
- **Avoiding "impossible" states**: Reducers can help model state more precisely, preventing invalid combinations of state variables.

#### `useReducer` Syntax

1. **Define a `reducer` function**: This function takes the `current state` and an `action` object as arguments and returns the `new state`. It should be a **pure function** (same inputs, same output, no side effects).
    - A common pattern is to use a `switch` statement based on `action.type`.
    - Each `case` should typically end with a `return` statement and be wrapped in curly braces to avoid variable clashes.
    - The `action` object can have any shape, but conventionally includes a string `type` and any additional data in other fields.
    - **Example `tasksReducer`:**
        
        ```
        function tasksReducer(tasks, action) { //
          switch (action.type) { //
            case 'added': { //
              return [
                ...tasks,
                { id: action.id, text: action.text, done: false }
              ];
            }
            case 'changed': { //
              return tasks.map(t => (t.id === action.task.id ? action.task : t));
            }
            case 'deleted': { //
              return tasks.filter(t => t.id !== action.id);
            }
            default: { // Good practice to handle unknown actions
              throw Error('Unknown action: ' + action.type);
            }
          }
        }
        ```
        
2. **Use the `useReducer` hook in your component**:
    
    - It takes a reducer function and an initial state as arguments.
    - It returns the current state and a `dispatch` function.
    - Event handlers now "dispatch" actions instead of directly setting state.
    
    ```
    import { useReducer } from 'react'; //
    
    const initialTasks = [ /* ... */ ]; // Initial state for the reducer
    
    function TaskApp() {
      // useReducer returns the current state and a 'dispatch' function
      const [tasks, dispatch] = useReducer(tasksReducer, initialTasks); //
    
      // Event handlers now 'dispatch' actions instead of directly setting state
      function handleAddTask(text) {
        dispatch({ type: 'added', id: nextId++, text: text }); // Action object
      }
      function handleChangeTask(task) {
        dispatch({ type: 'changed', task: task }); //
      }
      function handleDeleteTask(taskId) {
        dispatch({ type: 'deleted', id: taskId }); //
      }
    
      return (
        <>
          <h1>Prague itinerary</h1>
          {/* ... UI elements that call handleAddTask, handleChangeTask, handleDeleteTask */}
        </>
      );
    }
    ```
    

- You can move the reducer function outside the component or into a separate file for better organization.
- **`Immer` with `useReducer`:** The `useImmerReducer` Hook (from the `use-immer` package) lets you write reducer logic as if you're mutating the state, but it handles creating the immutable copies behind the scenes, making reducers more concise.

#### Combining `useReducer` with Context

- For global state that many deeply nested components need access to, you can combine `useReducer` with React Context.
- This avoids **prop drilling** (passing props through many intermediate components).

1. **Create two Contexts**: One for the state (`TasksContext`) and one for the dispatch function (`TasksDispatchContext`).
    
    ```
    // TasksContext.js
    import { createContext } from 'react'; //
    export const TasksContext = createContext(null); //
    export const TasksDispatchContext = createContext(null); //
    ```
    
2. **Provide both contexts from the top-level component**: Wrap child components with `Context.Provider` components, passing the `tasks` state and `dispatch` function as their `value` props.
    
    ```
    // TaskApp.js
    import { useReducer } from 'react'; //
    import { TasksContext, TasksDispatchContext } from './TasksContext.js'; //
    // ... other imports
    
    function TaskApp() {
      const [tasks, dispatch] = useReducer(tasksReducer, initialTasks); //
    
      return (
        <TasksContext.Provider value={tasks}> //
          <TasksDispatchContext.Provider value={dispatch}> //
            {/* Child components, no need to pass tasks or dispatch as props */}
            <AddTask />
            <TaskList />
          </TasksDispatchContext.Provider>
        </TasksContext.Provider>
      );
    }
    ```
    
3. **Consume contexts in any child component**: Use `useContext` to read the state or the dispatch function directly.
    
    ```
    // TaskList.js (deeply nested)
    import { useContext } from 'react'; //
    import { TasksContext } from './TasksContext.js'; //
    
    function TaskList() {
      const tasks = useContext(TasksContext); // Access tasks state
      // ...
    }
    
    function Task({ task }) {
      const dispatch = useContext(TasksDispatchContext); // Access dispatch function
      // ... call dispatch({ type: 'changed', task: new_task })
    }
    ```
    

- You can further **organize this by moving the reducer and context declarations into a single file** (e.g., `TasksContext.js`) and creating a `TasksProvider` component to wrap all the logic and provide children. This keeps components clean, focusing on display rather than data source.

### D. Immutability Principle

- **The core principle of state management in React is immutability.** When you update state that holds objects or arrays, you should **never mutate the original object or array** directly.
- Instead, always:
    1. Create a **new copy** of the object or array.
    2. Make your desired changes to the **new copy**.
    3. Pass the **new copy** to the state setter function.
- Failing to do so (i.e., mutating the original state object/array) can lead to:
    - React not detecting the change and thus **not re-rendering** the component.
    - **Accidental sharing of state** between different components or parts of your application, leading to unexpected bugs.
- **Libraries like `Immer`** can simplify writing immutable updates by letting you write mutating-style code on a special "draft" object, which then creates an immutable copy behind the scenes.

### E. Lifting State Up

- **Lifting state up** is a common pattern in React for **sharing state between components**.
- **Principle**: For each unique piece of state, there should be a **single source of truth**. If two components need to share or react to the same state, that state should "live" in their **closest common parent component**.
- **Process**:
    1. Identify the state that needs to be shared.
    2. Remove that state from the child components.
    3. Move the state (and its setter function) to their closest common parent component.
    4. Pass the state and its setter function down to the child components as props.
- **Example:** If multiple `Panel` components in an `Accordion` need to ensure only one is active at a time, the `activeIndex` state is lifted to the `Accordion` parent, which then passes `isActive` and `onShow` props to each `Panel`. This creates "controlled components".
- It is common to move state up or down during development as the application evolves.

## III. Side Effects with `useEffect`

### A. Introduction to `useEffect`

- `useEffect` is a React Hook that allows you to perform **side effects** in functional components.
- **Side effects** are operations that affect systems outside of React, such as:
    - Fetching data from an API.
    - Interacting with the browser DOM (e.g., directly manipulating elements, setting document title).
    - Setting up event listeners (e.g., `window` resize).
    - Starting timers (`setInterval`, `setTimeout`).
    - Sending analytics logs.
- Effects run **after rendering** (the commit phase). They are not pure calculations and should not run during rendering.
- Effects have a different lifecycle than components; they start synchronizing something and later stop synchronizing it.

#### Syntax: `useEffect`

```
import { useEffect, useState } from 'react'; //

function MyComponent({ someProp }) {
  const [data, setData] = useState(null);

  useEffect(() => { //
    // This function contains the effect logic
    console.log('Effect ran!');

    // Optional: Return a cleanup function
    return () => {
      console.log('Cleanup ran!');
      // E.g., remove event listeners, clear timers
    };
  }, [someProp]); // Dependency array
  // ...
}
```

### B. Dependencies Array

- The second argument to `useEffect` is an **optional array of dependencies**. This array tells React when to re-run the effect.
- There are three main scenarios for the dependency array:
    1. **Empty array `[]`**: The effect will run **only once** after the initial render (when the component "mounts") and its cleanup function will run only when the component "unmounts".
        - Useful for setting up one-time subscriptions or initial data fetches that don't depend on changing props/state.
        - **Example:**
            
            ```
            useEffect(() => {
              // Add a global event listener only once on mount
              window.addEventListener('resize', handleResize);
              return () => {
                // Remove event listener on unmount
                window.removeEventListener('resize', handleResize);
              };
            }, []); // Empty dependency array
            ```
            
    2. **No array (omitted)**: The effect will run **after _every_ render** of the component. This is rarely what you want and can lead to performance issues or infinite loops if the effect itself triggers state updates.
        - **Example (Insecure):**
            
            ```
            useEffect(() => { //
              // 🔴 Runs after every render, even unrelated state changes (e.g., typing in a message box)
              console.log('Component rendered or updated, effect ran!');
            }); // No dependency array
            ```
            
    3. **Array with values `[value1, value2]`**: The effect will run after the initial render (mount) and **only re-run when any of the values in the dependency array change** between renders.
        - **Reactive values** (props, state, and variables declared within the component's body) **must be included** in the dependency array if they are used inside the effect. This ensures the effect stays "synchronized" with the latest data.
        - **Example:**
            
            ```
            function MyComponent({ userId }) {
              const [data, setData] = useState(null);
              const [searchTerm, setSearchTerm] = useState('');
            
              useEffect(() => {
                // Fetches data when userId or searchTerm changes
                fetchData(userId, searchTerm).then(setData);
              }, [userId, searchTerm]); // Effect re-runs if userId or searchTerm changes
            }
            ```
            
- **Linter assistance:** React provides a linter rule (`react-hooks/exhaustive-deps`) that will automatically suggest correct dependencies if you omit them or miss some. **Always follow the linter's suggestions**. Suppressing the linter is dangerous and should be avoided.
- To change the dependency list, you must first change the code within the effect.

### C. Cleanup Function

- Many effects need a way to **stop, undo, or clean up** whatever they started.
- You can specify a cleanup function by **returning a function from inside your effect**.
- The cleanup function runs in two main scenarios:
    1. **Before the effect re-runs** due to a dependency change, to clean up the _previous_ effect's execution.
    2. **When the component unmounts** (is removed from the DOM).
- **Purpose of Cleanup**: To prevent memory leaks, unintended behavior, or resource accumulation.
- **Common cleanup tasks**:
    - Removing event listeners (`window.removeEventListener()`).
    - Clearing timers (`clearInterval()`, `clearTimeout()`).
    - Aborting ongoing network requests (`controller.abort()`) or ignoring stale responses.

### D. How React Verifies Effects in Development (Strict Mode)

- In **development mode**, especially with React's Strict Mode enabled, React intentionally **remounts components once after their initial mount** (setup -> cleanup -> setup sequence). It also remounts effects whenever you save a file.
- **Purpose**: This is a **stress test** to help developers find bugs in effects that need proper cleanup, such as memory leaks or race conditions.
- **Expected Behavior**: If an effect is implemented correctly with a proper cleanup function, there should be **no user-visible difference** between it running once (as in production) and running through the setup-cleanup-setup cycle (as in development).
- **Production**: In production, effects will typically run only once on mount and clean up on unmount.
- **Recommendation**: Keep Strict Mode on. The right question is not "how to run an Effect once", but "how to fix my Effect so that it works after remounting". You can remove the Strict Mode wrapper in `main.jsx` to stop this behavior in development.

### E. Common Mistakes with `useEffect`

#### 1. Fetching data without cleanup (Race Conditions)

- **Problem:** When fetching data, especially when the dependencies change rapidly (e.g., typing in a search box), multiple fetch requests can be fired. There's no guarantee of the order in which responses will return. A slower, older request might return _after_ a faster, newer one, causing the UI to display stale data. This is known as a **race condition**.
- **Solution: AbortController for network requests:**
    - Create a new `AbortController` instance.
    - Pass `controller.signal` as an option to your `fetch` call.
    - In the `useEffect` cleanup function, call `controller.abort()` to cancel any pending requests from the previous render.
    - **Example:**
        
        ```
        import { useState, useEffect } from 'react';
        
        function SearchResults({ query }) {
          const [data, setData] = useState(null);
        
          useEffect(() => {
            const controller = new AbortController(); //
            fetch(`/api/search?q=${query}`, { signal: controller.signal }) //
              .then(response => response.json())
              .then(data => setData(data));
        
            return () => {
              controller.abort(); // Cleanup: abort the previous fetch call if dependencies change
            };
          }, [query]); // Re-run effect when query changes
          // ...
        }
        ```
        
- **Another approach for race conditions (ignore stale responses):** Use a boolean flag `let ignore = false;` in the effect, set it to `true` in the cleanup function, and then check `if (!ignore)` before calling the state setter. This doesn't cancel the request but prevents setting state with stale data.

#### 2. Stale Closures with Intervals/Timers

- **Problem:** If you set up an `setInterval` (or `setTimeout`) in an effect that runs once, and the interval's callback uses a state variable that changes, the callback will close over the _initial_ value of that state variable (a **stale closure**), leading to incorrect or non-updating behavior. If you add the state variable to dependencies, it will create _multiple_ intervals without clearing old ones.
- **Solution:**
    
    1. **Use a cleanup function** to clear the interval (`clearInterval`) when the component unmounts or the effect re-runs.
    2. If the interval needs to access the latest state, use the **updater function** form of `setCount` (`setCount(prevCount => prevCount + 1)`) within the interval's callback, and usually keep the dependency array empty.
    
    - **Example (Correct):**
        
        ```
        import { useState, useEffect } from 'react';
        
        function Counter() {
          const [count, setCount] = useState(0);
        
          useEffect(() => {
            const id = setInterval(() => { //
              setCount(prevCount => prevCount + 1); // Use updater function for latest state
            }, 1000);
        
            return () => clearInterval(id); // Cleanup function: clear interval
          }, []); // Empty dependencies: run once on mount, clean up on unmount
          // ...
        }
        ```
        
- **Using `useRef` for timer IDs**: You can store the `timeoutID` in a `useRef` to ensure it persists across renders without causing re-renders, and then clear it in the cleanup.

#### 3. Fetching data in Effects (General Considerations)

- While fetching data with `useEffect` is common, it has several **downsides**, especially for professional applications:
    - **No server-side rendering (SSR)**: Effects don't run on the server, so initial HTML will only show loading states.
    - **Network waterfalls**: Parent components fetch, then children fetch, leading to sequential and slower data loading.
    - **No preloading/caching**: Data might be re-fetched unnecessarily if the component re-mounts.
    - **Boilerplate**: Requires manual handling of loading, error, and race conditions.
- **Alternatives:**
    - **Frameworks' built-in data fetching**: Modern React frameworks like Next.js offer optimized data fetching mechanisms (e.g., in server components).
    - **Client-side data fetching libraries**: Libraries like **React Query** (TanStack Query) or **SWR** by Vercel handle caching, race conditions, re-fetching, loading states, and error states more efficiently. They also deduplicate requests and cache responses.
    - **Custom Hooks**: Encapsulate data fetching logic into a custom hook (like `useData`), making it reusable and easier to manage, even if it uses `useEffect` internally. This also makes it easier to migrate to newer data fetching approaches in the future.
    - **`use` API for Promises (React 19 - Experimental)**: Future React versions might allow reading data in render by passing a Promise to `use()` within `Suspense`.
- **When it's still appropriate to fetch in Effects:** When you want to keep a part of your UI (e.g., search results) synchronized with data from the network based on `page` and `query` parameters, regardless of how those parameters originated (e.g., user typing, URL change). However, even then, consider solutions to race conditions and caching.
- **Environment Variables**: API keys should be stored in environment variables (e.g., in `.env.local` files for Vite projects), prefixed with `VITE_` (e.g., `VITE_TMDB_API_KEY`), and accessed via `import.meta.env` in the code.

#### 4. Object and Function Dependencies

- **Problem**: If an object or function is created inside the component's render function, it will be a _new_ object/function on _every_ render, even if its contents haven't logically changed. If this object/function is included in `useEffect`'s dependency array, it will cause the effect to **re-run unnecessarily on every render**, leading to performance issues or bugs (e.g., chat reconnecting on every keystroke).
- **Solution**: Try to avoid objects and functions as dependencies whenever possible.
    1. **Move static objects/functions outside the component**: If the object or function doesn't depend on any props or state, declare it outside the component function. This makes it non-reactive, and it won't trigger effect re-runs.
    2. **Move dynamic objects/functions inside the Effect**: If the object or function _does_ depend on reactive values (props, state), but its creation itself is not the synchronization logic, move its definition _inside_ the effect. This way, the inner object/function is recreated on each effect run, but it's not a dependency of the `useEffect` itself. The effect only depends on the primitive values that actually change.
        - **Example:**
            
            ```
            function ChatRoom({ roomId }) {
              const [message, setMessage] = useState('');
              useEffect(() => {
                const options = { // Created inside the effect
                  serverUrl: 'https://localhost:1234',
                  roomId: roomId
                };
                const connection = createConnection(options);
                connection.connect();
                return () => connection.disconnect();
              }, [roomId]); // Only 'roomId' is a dependency
              // ...
            }
            ```
            
    3. **Read primitive values from objects/functions**: If you receive an object or function via props, extract the primitive values you _actually_ need from it outside the effect, and use those primitive values as dependencies. This makes it explicit what the effect depends on.
    4. **Use Effect Events (`experimental_useEffectEvent`) for non-reactive functions**: For functions that need to read reactive values but _shouldn't_ cause the effect to re-run (e.g., a callback that uses `isMuted` state but shouldn't reconnect the chat when `isMuted` changes), use the experimental `useEffectEvent` Hook.
        - **`useEffectEvent` Syntax**: `const myEffectEvent = useEffectEvent((param) => { /* logic */ });`.
        - Effect Events are **not reactive** and must be omitted from dependencies. They always "see" the latest values of props and state.
        - They should only be called from _inside_ effects.
        - **Example:**
            
            ```
            import { useState, useEffect, experimental_useEffectEvent as useEffectEvent } from 'react'; //
            
            function ChatRoom({ roomId, theme }) {
              const onConnected = useEffectEvent(() => { // Effect Event, non-reactive
                showNotification('Connected!', theme); // Reads 'theme' but doesn't react to its changes
              });
            
              useEffect(() => {
                const connection = createConnection(serverUrl, roomId);
                connection.on('connected', () => {
                  onConnected(); // Call Effect Event from inside Effect
                });
                connection.connect();
                return () => connection.disconnect();
              }, [roomId]); // 'theme' is no longer a dependency, 'onConnected' is omitted
            }
            ```
            

#### 5. Effect Doing Several Unrelated Things

- **Problem**: A single `useEffect` that synchronizes multiple, independent processes (e.g., fetching cities based on country _and_ fetching areas based on city within the same effect) can lead to unexpected re-runs if only one of the underlying dependencies changes.
- **Solution**: **Each `useEffect` in your code should represent a separate and independent synchronization process**. If deleting one effect wouldn't break the logic of another, they should be split into separate effects with their own dependency arrays.
    - **Example (Incorrect: One effect for cities and areas):**
        
        ```
        function ShippingForm({ country, city }) {
          useEffect(() => {
            // 🔴 Avoid: A single Effect synchronizes two independent processes
            fetchCities(country); // Depends on country
            if (city) {
              fetchAreas(city); // Depends on city
            }
          }, [country, city]); // Re-runs if either changes, even if only one is relevant
        }
        ```
        
    - **Example (Correct: Two separate effects):**
        
        ```
        function ShippingForm({ country, city }) {
          useEffect(() => {
            // ✅ Good: One effect for cities, depends only on country
            fetchCities(country);
          }, [country]);
        
          useEffect(() => {
            // ✅ Good: Another effect for areas, depends only on city
            if (city) {
              fetchAreas(city);
            }
          }, [city]);
        }
        ```
        

#### 6. Reading State to Calculate Next State in Effects

- **Problem**: If an effect needs to update a state variable based on its _previous_ value (e.g., `setMessages([...messages, newMessage])`) and `messages` is also a dependency of the effect, then every update to `messages` will cause the effect to re-run, potentially leading to infinite loops or unwanted re-synchronizations (e.g., chat reconnecting on every message).
- **Solution**: Pass an **updater function** to the state setter instead of reading the state variable directly within the effect. This way, the effect doesn't need to depend on the state variable itself.
    - **Example (Incorrect: `messages` as dependency):**
        
        ```
        function ChatRoom({ roomId }) {
          const [messages, setMessages] = useState([]);
          useEffect(() => {
            const connection = createConnection();
            connection.on('message', (receivedMessage) => {
              setMessages([...messages, receivedMessage]); // Reads 'messages'
            });
            return () => connection.disconnect();
          }, [roomId, messages]); // 🔴 'messages' is a dependency, causes re-connect on every message
        }
        ```
        
    - **Example (Correct: Updater function for `setMessages`):**
        
        ```
        function ChatRoom({ roomId }) {
          const [messages, setMessages] = useState([]);
          useEffect(() => {
            const connection = createConnection();
            connection.on('message', (receivedMessage) => {
              setMessages(msgs => [...msgs, receivedMessage]); // ✅ Uses updater function
            });
            return () => connection.disconnect();
          }, [roomId]); // 'messages' is no longer a dependency
        }
        ```
        

#### 7. Event-Specific Logic in Effects

- **Problem**: Using effects for logic that should only run in response to a **specific user interaction** (e.g., sending a POST request when a button is clicked, showing a notification when an item is added to cart) can lead to bugs. Effects run when components are displayed or dependencies change, not just on specific interactions. This can cause the logic to run at unintended times (e.g., on page refresh, when navigating back).
- **Solution**: **Event-specific logic belongs in event handlers**. Event handlers are designed to respond to particular user interactions and are not reactive to state/prop changes.
    - **Example (Incorrect: POST request in Effect):**
        
        ```
        function Form() {
          const [jsonToSubmit, setJsonToSubmit] = useState(null);
          useEffect(() => {
            if (jsonToSubmit !== null) {
              // 🔴 Avoid: Event-specific logic inside an Effect
              post('/api/register', jsonToSubmit);
            }
          }, [jsonToSubmit]);
          function handleSubmit(e) { e.preventDefault(); setJsonToSubmit({ /* ... */ }); }
        }
        ```
        
    - **Example (Correct: POST request in Event Handler):**
        
        ```
        function Form() {
          const [firstName, setFirstName] = useState('');
          function handleSubmit(e) {
            e.preventDefault();
            // ✅ Good: Event-specific logic is in the event handler
            post('/api/register', { firstName, lastName });
          }
        }
        ```
        
- **Distinction**: Ask yourself _why_ the code needs to run:
    - **Event handlers** run _because_ of a particular interaction (e.g., a button click). Their logic is _not reactive_.
    - **Effects** run _because_ the component needs to stay synchronized with an external system as long as it's visible, regardless of specific user interactions. Their logic _is reactive_.

#### 8. Redundant Effects (Chains of Computations)

- **Problem**: Creating multiple effects that each adjust a piece of state based on other state, solely to trigger each other, is often unnecessary and leads to complex, hard-to-follow logic.
- **Solution**: Calculate as much as possible **during rendering** (derived state) and handle state updates for related logic **within a single event handler**.

#### 9. Subscribing to External Stores (e.g., `navigator.onLine`)

- **Problem**: Using `useEffect` to manually subscribe to browser APIs or other external data stores (like `navigator.onLine` for online status) often involves complex cleanup and can be error-prone, especially with server-side rendering considerations.
- **Solution**: Use the purpose-built **`useSyncExternalStore`** Hook (not explicitly detailed in provided sources, but mentioned as the modern alternative for the problem `useOnlineStatus` solves which is shown using `useEffect`). This Hook is designed specifically for subscribing to external mutable data sources and handles server rendering gracefully.
    - **Example (`useOnlineStatus` with `useEffect`):**
        
        ```
        import { useState, useEffect } from 'react'; //
        
        export function useOnlineStatus() {
          const [isOnline, setIsOnline] = useState(true); //
          useEffect(() => { //
            function handleOnline() { setIsOnline(true); } //
            function handleOffline() { setIsOnline(false); } //
            window.addEventListener('online', handleOnline); //
            window.addEventListener('offline', handleOffline); //
            return () => { //
              window.removeEventListener('online', handleOnline);
              window.removeEventListener('offline', handleOffline);
            };
          }, []); //
          return isOnline; //
        }
        ```
        

#### 10. Sending analytics (Double Logging in Development)

- **Problem**: In development, `useEffect` (and cleanup) often runs twice due to React's Strict Mode. This can cause analytics events to be logged twice.
- **Solution**: **Keep the code as is**. There's no user-visible behavior difference. In production, it typically won't log twice. Focus on configuring your analytics to ignore development logs rather than trying to prevent the effect from running twice.

#### 11. Debouncing Input

- **Problem**: For inputs that trigger data fetching (like search bars), making an API request on every keystroke can lead to excessive API calls, server overload, and rate limiting issues.
- **Solution**: Implement **input debouncing**. This technique delays the request until the user has stopped typing for a predefined amount of time, reducing the frequency of API calls.
    - This can be done using a pre-made `useDebounce` Hook from libraries like `react-use`.
    - **Example with `useDebounce`:**
        
        ```
        import { useState, useEffect } from 'react';
        import { useDebounce } from 'react-use'; //
        
        function SearchComponent() {
          const [searchTerm, setSearchTerm] = useState('');
          const [debouncedSearchTerm, setDebouncedSearchTerm] = useState(''); //
        
          useDebounce( //
            () => {
              setDebouncedSearchTerm(searchTerm);
            },
            500, // Wait 500 milliseconds (half a second) after typing stops
            [searchTerm] // Re-run debounce logic if searchTerm changes
          );
        
          useEffect(() => {
            if (debouncedSearchTerm) {
              // Only fetch when debouncedSearchTerm is not empty
              fetchMovies(debouncedSearchTerm); // Fetch based on debounced term
            }
          }, [debouncedSearchTerm]); // Effect depends on the debounced term
        
          return (
            <input
              type="text"
              value={searchTerm}
              onChange={e => setSearchTerm(e.target.value)}
              placeholder="Search movies..."
            />
          );
        }
        ```
        

### F. Event Handlers vs. Effects: The Reactivity Distinction

- Choosing whether to put logic in an event handler or an effect depends on **why** the code needs to run.
- **Event Handlers**:
    - Run in response to **specific user interactions** (e.g., clicks, key presses).
    - Their logic is **not reactive**. They won't run again unless the user performs the interaction again.
    - They can read reactive values (state, props) without "reacting" to their changes.
    - They are the **best place for side effects** that are a direct result of user interaction (e.g., sending a POST request to buy something, updating input field based on typing).
- **Effects**:
    - Run whenever **synchronization with an external system** is needed.
    - Their logic **is reactive**. If an effect reads a reactive value (prop, state), it must be specified as a dependency, and the effect will re-run when that value changes.
    - They are used to keep a component **synchronized** with something outside React, regardless of specific user interactions.

## IV. Reusing Logic with Custom Hooks

### A. What are Custom Hooks?

- Custom Hooks are a mechanism to **reuse stateful logic** (logic involving React Hooks like `useState`, `useEffect`, `useContext`) across different components.
- They allow you to **extract complex implementation details** (especially for synchronizing with external systems like browser APIs, network, or third-party libraries) out of your components, making the components cleaner and more focused on _what they do_ rather than _how they do it_.

#### Naming Conventions:

- Custom Hook names **must start with `use`** followed by a capital letter (e.g., `useWindowSize`, `useOnlineStatus`). This convention tells React that it's a Hook and follows the Rules of Hooks (e.g., can only be called at the top level of a React function).
- Functions that don't call any Hooks internally **should not** start with `use`.

#### Syntax: Custom Hook

```
// hooks/useWindowSize.js
import { useState, useEffect } from 'react'; // Custom hooks can use other hooks

function useWindowSize() { // Name starts with 'use' and a capital letter
  const [windowSize, setWindowSize] = useState({ //
    width: window.innerWidth,
    height: window.innerHeight,
  });

  useEffect(() => { //
    function handleResize() {
      setWindowSize({
        width: window.innerWidth,
        height: window.innerHeight,
      });
    }

    window.addEventListener('resize', handleResize); //
    return () => window.removeEventListener('resize', handleResize); //
  }, []); // Empty dependency array: runs once on mount, cleans up on unmount

  return windowSize; // Custom hooks can return any value
}

export default useWindowSize;
```

#### Using a Custom Hook

```
// components/MyComponent.jsx
import useWindowSize from '../hooks/useWindowSize.js'; // Import your custom hook

function MyComponent() {
  const { width, height } = useWindowSize(); // Call the custom hook

  return (
    <div>
      <p>Window width: {width}</p>
      <p>Window height: {height}</p>
    </div>
  );
}
```

### B. When to use Custom Hooks:

- When you have **stateful logic (especially effects)** that needs to be shared across multiple components.
- When you are synchronizing with an **external system** (e.g., browser API, network, third-party library).
- To make your component's code **more declarative** and focused on intent.
- To hide complex implementation details within the hook (e.g., how to subscribe to `online`/`offline` events).
- **Good custom hooks are focused on concrete, high-level use cases** (e.g., `useData`, `useChatRoom`, `useOnlineStatus`) rather than abstract "lifecycle" wrappers for `useEffect`.

### C. Custom Hooks Share Logic, Not State

- When multiple components use the same custom hook, they each get their **own independent state and effects**.
- For example, two components using `useOnlineStatus` will each have their own `isOnline` state variable. They happen to have the same value because they are synchronizing with the same external system (the browser's network status).

### D. Passing Event Handlers to Custom Hooks

- When a custom hook accepts an event handler as a prop, adding that event handler directly to the dependency array of an internal `useEffect` can cause the effect to re-run unnecessarily every time the parent component re-renders (because functions are recreated on each render).
- **Solution**: Wrap the event handler in an **Effect Event** (`useEffectEvent`, an experimental API). Effect Events are not reactive and therefore don't need to be included in the dependency array, preventing unnecessary re-synchronization.
    - **Example (Passing `onReceiveMessage` to `useChatRoom`):**
        
        ```
        import { useEffect, experimental_useEffectEvent as useEffectEvent } from 'react'; //
        import { createConnection } from './chat.js';
        import { showNotification } from './notifications.js';
        
        export function useChatRoom({ serverUrl, roomId, onReceiveMessage }) {
          const onMessage = useEffectEvent(onReceiveMessage); // Wrap event handler in Effect Event
        
          useEffect(() => {
            const options = { serverUrl, roomId };
            const connection = createConnection(options);
            connection.connect();
            connection.on('message', (msg) => {
              onMessage(msg); // Call the Effect Event
            });
            return () => connection.disconnect();
          }, [roomId, serverUrl]); // 'onReceiveMessage' (and thus 'onMessage') is not a dependency
        }
        ```
        

### E. Avoid "Lifecycle" Custom Hooks

- **Problem**: Custom Hooks that merely wrap standard lifecycle patterns (e.g., `useMount`, `useEffectOnce`, `useUpdateEffect`) are generally discouraged. They often abstract away the dependency array, making it harder for the linter to detect missing dependencies and leading to subtle bugs.
- **Solution**: When writing effects, start by using the raw `useEffect` API directly. This allows the React linter to provide full checks. If you find yourself duplicating logic, extract it into a custom hook that solves a concrete problem (like `useData` or `useOnlineStatus`), not just a generic lifecycle event.

## V. Other Important React Concepts

### A. Props (Properties)

- **Props are read-only properties** that allow parent components to **pass data down to their child components**. They are also known as "properties".
- Think of props as **arguments you pass to a function** or settings you pass to a component.
- Props can be **any JavaScript value**: strings, numbers, booleans, objects, arrays, or functions.
- Child components receive props as the first argument to their function (an object).
- A component may receive **different props over time** (props are not static).

#### Syntax: Passing and Receiving Props

```
// ParentComponent.jsx
import ChildComponent from './ChildComponent.jsx';

function ParentComponent() {
  const userName = 'Bro Code';
  const userAge = 30;
  const isStudent = true;

  return (
    <>
      {/* Passing props as key-value pairs */}
      <ChildComponent
        name={userName} //
        age={userAge}   //
        isStudent={isStudent} //
        greeting="Hello from Parent!"
      />
    </>
  );
}

// ChildComponent.jsx
function ChildComponent(props) { // Or destructure directly: function ChildComponent({ name, age, isStudent, greeting })
  return (
    <div>
      <p>Name: {props.name}</p> {/* */}
      <p>Age: {props.age}</p>   {/* */}
      <p>Is Student: {props.isStudent ? 'Yes' : 'No'}</p> {/* Conditional rendering based on prop */}
      <p>Message: {props.greeting}</p>
    </div>
  );
}
```

- **Destructuring Props**: You can destructure props directly in the component's function signature for easier access.
    
    ```
    function ChildComponent({ name, age, isStudent }) {
      return (
        <div>
          <p>Name: {name}</p> {/* Use 'name' directly */}
          {/* ... */}
        </div>
      );
    }
    ```
    
- **`children` Prop**: When you nest content inside a JSX tag, the parent component receives that content in a prop called `children`. This allows for flexible content composition.
    
    ```
    <Card>
      <Avatar />
    </Card>
    // Card component receives { children: <Avatar /> }
    ```
    
- **Prop Drilling:** This is a problem that occurs when data needs to be passed from a parent component through many intermediate components to a deeply nested child component, even if the intermediate components don't use that data themselves. It makes code verbose and hard to maintain.
    - **Alternatives to Prop Drilling**:
        1. **Context**: Use React Context to "teleport" data directly to any component in the tree below the provider, without explicitly passing props through every level.
        2. **Extracting Components / `children` prop**: Refactor your component hierarchy so that data is passed to components that actually need it, often by using the `children` prop to render content passed from the parent.
        3. **Lifting State Up**: Move the state to a common ancestor, then pass it down as props only to the components that need it.
- **Prop Types**: A mechanism to ensure that the passed value for a prop is of the correct data type (e.g., `age` is a `number`). If not, React emits a warning in the console for debugging purposes.

### B. Conditional Rendering

- Conditional rendering allows components to **display different things depending on different conditions**. You use standard JavaScript syntax within your JSX.

#### 1. `if` statements

- You can use `if` statements (or `if/else`) inside your component function to return different JSX based on a condition. The `return` statement immediately exits the function.
    - **Example:**
        
        ```
        function Item({ name, isPacked }) {
          if (isPacked) { //
            return <li className="item"> {name} ✅ </li>;
          }
          return <li className="item"> {name} </li>;
        }
        
        // Or for displaying nothing if a condition is met
        function Item({ name, isPacked }) {
          if (isPacked) { //
            return null; // Return null to render nothing
          }
          return <li className="item"> {name} </li>;
        }
        
        // Or for displaying different forms based on status
        function Form({ status = 'empty' }) {
          if (status === 'success') {
            return <h1>That's right!</h1>; //
          }
          // ... return form for other statuses
        }
        ```
        

#### 2. Ternary operator (`condition ? <A /> : <B />`)

- Useful for simple conditions where you need to render one of two things (an "if-else" in JSX).
    - **Example:**
        
        ```
        // Display 'Yes' or 'No' based on a boolean
        <p>Is Employed: {isEmployed ? 'Yes' : 'No'}</p>
        
        // Or render different text/JSX inside an element
        return (
          <li className="item">
            {isPacked ? ( <del>{name + ' ✅'}</del> ) : ( name )} {/* */}
          </li>
        );
        ```
        

#### 3. Logical AND operator (`&&`)

- Used when you want to render something **only if a condition is true, otherwise render nothing**. In JavaScript, `true && expression` evaluates to `expression`, while `false && expression` evaluates to `false` (which React doesn't render).
    - **Example:**
        
        ```
        // Display loading message only if isLoading is true
        {isLoading && <p>Loading...</p>}
        
        // Display checkmark only if isPacked is true
        function Item({ name, isPacked }) {
          return (
            <li className="item">
              {name} {isPacked && '✅'} {/* */}
            </li>
          );
        }
        ```
        

#### 4. Assigning JSX to variables (If/Else outside Return)

- You can declare a variable to hold JSX, and then use `if` or `else` statements to assign different JSX expressions to it. Then, embed that variable in the returned JSX. This is the most verbose but also the most flexible style.
    - **Example:**
        
        ```
        function Item({ name, isPacked }) {
          let itemContent = name; //
          if (isPacked) {
            itemContent = (<del>{name + " ✅"}</del>); // Can be arbitrary JSX
          }
          return (
            <li>
              {itemContent} {/* Embed the variable in JSX */}
            </li>
          );
        }
        ```
        

### C. Refs (`useRef`)

- `useRef` is a React Hook that allows components to **reference values that are not needed for rendering**, or to **directly access DOM elements**.
- Unlike state, updating a ref **does not trigger a re-render** of the component.
- `useRef` returns an object with a single mutable property called `current` (`{ current: value }`).
    - The `current` property can be set to any value (number, string, object, function).
    - When attached to a DOM element (e.g., `<input ref={myRef} />`), `ref.current` will point to that element.

#### Syntax: `useRef`

```
import { useRef } from 'react'; //

function MyForm() {
  const inputRef = useRef(null); // Initialize ref with null

  function handleClick() {
    if (inputRef.current) {
      inputRef.current.focus(); // Example: focus an input element
    }
  }

  return (
    <div>
      <input ref={inputRef} type="text" /> {/* Attach ref to a DOM element */}
      <button onClick={handleClick}>Focus Input</button>
    </div>
  );
}
```

- **Purpose of Refs**:
    - **Direct DOM manipulation**: To focus an input, scroll to an element, play/pause media, etc..
    - **Managing timers/intervals**: Storing interval IDs or timeout IDs that need to be cleared later, as these values are not used for rendering.
    - **Storing mutable values that don't trigger re-renders**: Any value you want to "remember" between renders but that doesn't need to update the UI.
- **When to use `useRef` vs `useState`**: Use `useState` when changes to a value should trigger a re-render and affect the UI. Use `useRef` when you need to "remember" information that doesn't cause a re-render or when you need to interact imperatively with the DOM.
- **Accessing Refs**: Usually, you will access refs from event handlers. If there's no specific event, you might use an Effect.
- **Cleanup**: If a ref holds a resource (like a timer ID), ensure it's cleared or disconnected when the component unmounts or the effect re-runs (using a `useEffect` cleanup function).
- **Strict Mode and Refs**: In Strict Mode, ref callbacks will run twice in development.
- **Avoid using refs to prevent effects from firing**: Do not use a ref as a flag to prevent an effect from running more than once. This does not fix underlying bugs and can lead to uncleaned resources.

### D. Context (`useContext`, `createContext`)

- React Context provides a way to **pass data deeply through the component tree without prop drilling**. It lets a parent component make information available to any component below it, no matter how deep, without explicitly passing it through props.

#### Syntax: Context

1. **Create the context**: Use `createContext()` to create a Context object. It takes a default value as an argument. You should **export it from a file** so that your components can use it.
    
    ```
    // MyContext.js
    import { createContext } from 'react'; //
    export const UserContext = createContext(null); // Default value is null
    ```
    
2. **Provide the context**: Use `MyContext.Provider` to wrap the components that need access to the context. Pass the data as the `value` prop to the provider.
    
    ```
    // App.js
    import { useState } from 'react';
    import { UserContext } from './MyContext.js'; //
    import ComponentA from './ComponentA.jsx'; //
    
    function App() {
      const [user, setUser] = useState('Bro Code'); // State to be shared
    
      return (
        <UserContext.Provider value={user}> {/* Provide the 'user' value */}
          <ComponentA /> {/* Any component inside ComponentA can access 'user' */}
        </UserContext.Provider>
      );
    }
    ```
    
3. **Consume the context**: Use the `useContext()` Hook in any descendant component to read the value provided by the nearest `Context.Provider` above it in the tree.
    
    ```
    // ComponentD.jsx (deeply nested)
    import { useContext } from 'react'; //
    import { UserContext } from './MyContext.js'; //
    
    function ComponentD() {
      const user = useContext(UserContext); // Access the 'user' value
      return <p>Hello, {user} from ComponentD!</p>;
    }
    ```
    

- Context is often combined with `useReducer` for managing complex global state, providing both the state and the dispatch function to deeply nested components.

### E. Keys

- **Keys are essential when rendering lists of components from arrays using JavaScript's `map()` method**.
- You must provide a **unique `key` prop** to each component or element in a list.
- **Purpose**: Keys help React efficiently keep track of items being inserted, removed, or reordered within a list. Without keys, React might confuse elements, leading to unexpected behavior and bugs.
- **Where to get your key**:
    - **Data from a database:** Use database keys/IDs, which are naturally unique.
    - **Locally generated data:** For local data, use an incrementing counter, `crypto.randomUUID()`, or a package like `uuid` when creating items.
    - **Avoid using array index as a key** if the list items can change order, be added/removed, as this can lead to bugs.
- **Scope of Keys**: Keys are not globally unique; they only specify the position _within their parent_ component.
- **Resetting State with Keys**: You can force a component (and its entire subtree) to **reset its state** by giving it a different `key`. This is particularly useful for forms or chat applications where you want to clear input when switching contexts (e.g., chatting with a different recipient). React will unmount the old component and mount a new one.
    - **Example (Resetting chat input):**
        
        ```
        function Messenger() {
          const [to, setTo] = useState(contacts); //
          return (
            <div>
              <ContactList selectedContact={to} onSelect={setTo} />
              <Chat key={to.id} contact={to} /> {/* Add key to reset Chat component */}
            </div>
          );
        }
        ```
        
- When rendering multiple DOM nodes for each list item, if you use a `Fragment` (`<>...</>`), you cannot pass a key directly. You need to use the explicit `<Fragment key={...}>` syntax.

### F. Event Handling

- React lets you add **event handlers** to your JSX. Event handlers are your own functions that will be triggered in response to user interactions like clicking, hovering, focusing form inputs, and so on.

#### Syntax: Adding Event Handlers

```
function Button() {
  function handleClick() { // 1. Declare function inside component
    alert('You clicked me!'); // 2. Implement logic
  }

  return (
    <button onClick={handleClick}>Click me</button> // 3. Add onClick={handleClick} to JSX
  );
}
```

- **Passing Arguments**: If your event handler needs parameters, avoid calling the function directly in JSX (`onClick={handleClick()}`). This would invoke it immediately on render. Instead, wrap it in an **arrow function** or a function expression.
    
    ```
    <button onClick={() => handleClick(someValue)}>Click me</button> //
    ```
    
- **`onChange` Event Handler**: Triggers a function every time the value of an input element changes (e.g., `<input>`, `<textarea>`, `<select>`, radio buttons). It's used for real-time reflection of changes.
    
    ```
    <input value={text} onChange={e => setText(e.target.value)} /> //
    ```
    
- **Event Propagation (Bubbling)**: Event handlers will catch events from any children a component might have. An event "bubbles" or "propagates" up the tree from where it happened.
- **Preventing Default Behavior**: Some browser events have default behavior (e.g., a form `submit` event reloads the page). You can call `e.preventDefault()` on the event object to stop this.
    
    ```
    <form onSubmit={e => { e.preventDefault(); alert('Submitting!'); }}> {/* */}
      <input /> <button>Send</button>
    </form>
    ```
    
- **Passing Event Handlers as Props**: Often, a parent component specifies a child's event handler by passing it as a prop. This allows the child component to trigger logic defined in its parent.
    
    ```
    // Parent
    <Button onClick={handlePlayClick}>Play Movie</Button> //
    
    // Child (Button.jsx)
    function Button({ onClick, children }) { // Accepts onClick prop
      return <button onClick={onClick}>{children}</button>; // Attaches it to native onClick
    }
    ```
    

### G. Styling in React

- React itself is agnostic about styling, supporting various approaches.

1. **External CSS stylesheets**: Create separate `.css` files and import them into your components. Good for global styles or small projects. This approach provides flexibility with media queries and pseudo-selectors.
2. **CSS Modules (`.module.css`)**: Dedicated CSS files for each component that generate unique class names (via hashing) to prevent naming conflicts.
3. **Inline styles (`style={{}}`)**: Directly apply CSS properties as a JavaScript object to JSX elements. Properties are written in `camelCase` (e.g., `backgroundColor`). Inline styles are convenient for isolated components with minimal styling and prevent global style conflicts, but can reduce readability and maintainability in larger applications. **Inline styles have higher precedence than external stylesheets**.
4. **`className` attribute**: Use `className` (in `camelCase`) instead of `class` to assign CSS classes to elements. This is the standard way to apply external CSS classes.
5. **Utility-first CSS frameworks (e.g., Tailwind CSS)**: Apply pre-defined utility classes directly in your JSX `className` attribute (e.g., `<div className="text-3XL font-bold">`). Tailwind CSS is a popular and in-demand way of styling applications.

### H. Project Setup (Vite)

- Modern React projects are often set up using **Vite**, a development server known for its speed and optimized builds, as a replacement for the older Create React App.
- **Steps to create a new React project with Vite**:
    1. Install Node.js (which includes npm).
    2. Open your terminal and run `npm create vite@latest`.
    3. Follow prompts: name your project, select `react` framework, choose `JavaScript` (or TypeScript).
    4. Navigate into your project folder: `cd your-project-name`.
    5. Install dependencies: `npm install` (or `npm i`).
    6. Start the development server: `npm run dev`. The server typically runs on `localhost:5173`. To restart, navigate to the project directory and run `npm run dev`.
- **Key project files and folders**:
    - `node_modules/`: Contains external libraries and packages that the project relies on. You typically don't interact with it directly and it should be ignored by Git.
    - `public/`: For static assets that are served directly (e.g., `hero.png`, `v.svg`). These don't go through Vite's bundler.
    - `src/`: Where your React components and JavaScript logic reside.
        - `main.jsx`: The entry point of your React app, where React is rendered into the DOM. It imports the main `App` component and global styles.
        - `App.jsx`: The root component of your application.
        - `assets/`: Can contain media assets like images or icons that _are_ bundled during final output.
        - Component files (e.g., `Header.jsx`, `MyComponent.jsx`): Your individual React components.
        - `index.css`: Global styles for the application.
    - `package.json`: Contains project metadata (name, version, build tools, React version) and lists all dependencies and scripts (e.g., `dev`, `build`).
    - `package-lock.json`: Automatically generated, locks down versions of dependencies for consistent installations across environments. Do not delete.
    - `.gitignore`: Tells Git which files/folders to ignore (e.g., `node_modules`, `.env` files).
    - `vite.config.js`: Vite-specific configuration for customizing the build process (e.g., adding plugins).
    - `.env.local` (or similar): For environment variables (API keys, project IDs). These need to be kept safe and ignored by Git.
    - `eslint.config`: Config file for ESLint, a linting tool.
    - `README.md`: Project information and documentation.

### I. Server Components vs Client Components (Next.js Context)

- In frameworks like Next.js (especially with the App Router), components are **Server Components by default**.
- **Server Components** render on the server, cannot keep track of state, and **cannot use React Hooks** like `useState` or `useEffect`. They also don't have access to browser-specific APIs like `window` or `localStorage`.
- **Client Components** are rendered on the client (browser) and can use `useState`, `useEffect`, and other Hooks, as well as browser APIs.
- To make a component a Client Component, you must add the `'use client'` directive at the top of the file.
- If a Server Component imports another component that has `'use client'`, or if it imports into a component that is already a client component, then all components imported into that client component automatically become client components as well, creating a "client boundary".

### J. Deployment

- To deploy a React application, you first need to create an **optimized production build**. This is done by running `npm run build` in your terminal.
- This command generates a `dist` (distribution) folder containing all the optimized files of your application.
- These contents (including `index.html` and assets) can then be uploaded to a web hosting service (e.g., Hostinger's `public_html` folder).
- It is recommended to use a custom domain name and secure it with an SSL certificate.

---