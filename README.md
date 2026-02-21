# Hooks Interview Questions (2025–2026)

### Table of Contents

1. [useLayoutEffect](#1-uselayouteffect)
2. [useReducer](#2-usereducer)
3. [Custom Hooks](#3-custom-hooks)
4. [Difference between useState and useReducer](#4-difference-between-usestate-and-usereducer)
5. [Difference between useEffect and useLayoutEffect](#5-difference-between-useeffect-and-uselayouteffect)
6. [Difference between useMemo and useCallback](#6-difference-between-usememo-and-usecallback)
7. [Difference between useRef and useState](#7-difference-between-useref-and-usestate)
8. [Types of Hooks in React?](#8-types-of-hooks-in-react)
9. [useMemo](#9-usememo)
10. [useCallback](#10-usecallback)
11. [Difference between custom hook and components](#11-difference-between-custom-hook-and-components)
12. [useReducer vs Context API vs Redux](#12-usereducer-vs-context-api-vs-redux)
13. [Form Validation (Custom Hook)](#13-form-validation-custom-hook)
14. [Form Validation (react hook form)](#13-form-validation-react-hook-form)
15. [Form Validation (formik)](#15-form-validation-formik)


## 1. **useLayoutEffect**

useLayoutEffect is a Hook that runs synchronously after DOM updates but before the browser paints the screen.

### 🔄 Execution Order

When a component renders:

- 1️⃣ Render phase
- 2️⃣ DOM updated
- 3️⃣ useLayoutEffect runs
- 4️⃣ Browser paints
- 5️⃣ useEffect runs

```jsx
import React, { useLayoutEffect, useRef } from "react";

function App() {
  const boxRef = useRef(null);

  useLayoutEffect(() => {
    boxRef.current.style.backgroundColor = "lightblue";
  }, []);

  return (
    <div
      ref={boxRef}
      style={{ width: "200px", height: "100px", backgroundColor: "pink" }}
    >
      Hello
    </div>
  );
}

export default App;
```

### 🔎 What Happens

- React renders div with pink color
- DOM updates
- useLayoutEffect runs immediately
- Background changes to lightblue
- Browser paints
- 👉 User only sees lightblue
- 👉 No flicker

### 🆚 If We Use useEffect

If you replace useLayoutEffect with useEffect:
- User may briefly see pink
- Then it changes to lightblue
- Small flicker happens

### 🧠 Simple Rule

Use useLayoutEffect when:
- You need to measure DOM
- You need to change layout immediately
- You want to avoid flicker

<br>

## 2. **useReducer**

***useReducer*** is a React Hook used for state management when state logic is complex or depends on previous state.

***Syntax***
```jsx
const [state, dispatch] = useReducer(reducer, initialState);
```
***Example***
simple useReducer pattern for a form with many fields.

`When a form has:`
- name
- email
- password
- confirmPassword
- etc

Using multiple useState becomes messy. So we use useReducer.

### 🧠 Why useReducer for forms?
```jsx
const [name, setName] = useState("")
const [email, setEmail] = useState("")
const [password, setPassword] = useState("")
```
We manage everything in one state object.

### ✅ Simple Example: Registration Form

Step 1️⃣ Define initial state
```jsx
const initialState = {
  name: "",
  email: "",
  password: ""
};
```

Step 2️⃣ Create reducer function
```jsx
function formReducer(state, action) {
  switch (action.type) {
    case "UPDATE_FIELD":
      return {
        ...state,
        [action.field]: action.value
      };

    case "RESET":
      return initialState;

    default:
      return state;
  }
}
```

Step 3️⃣ Use in Component
```jsx
import React, { useReducer } from "react";

export default function SignupForm() {
  const [state, dispatch] = useReducer(formReducer, initialState);

  function handleChange(e) {
    dispatch({
      type: "UPDATE_FIELD",
      field: e.target.name,
      value: e.target.value
    });
  }

  function handleSubmit(e) {
    e.preventDefault();
    console.log(state);
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="name"
        value={state.name}
        onChange={handleChange}
        placeholder="Name"
      />

      <input
        name="email"
        value={state.email}
        onChange={handleChange}
        placeholder="Email"
      />

      <input
        name="password"
        type="password"
        value={state.password}
        onChange={handleChange}
        placeholder="Password"
      />

      <button type="submit">Submit</button>
      <button type="button" onClick={() => dispatch({ type: "RESET" })}>
        Reset
      </button>
    </form>
  );
}
```

### 🔥 Why This Is Better

- ✔ One state object
- ✔ Centralized logic
- ✔ Easy to scale
- ✔ Easy to add validation
- ✔ Cleaner for complex forms


| useState         | useReducer           |
| ---------------- | -------------------- |
| Simple state     | Complex state        |
| Direct updates   | Action based updates |
| Less boilerplate | More predictable     |

<br>

## 3. **custom hooks**

- A custom hook is a reusable JavaScript function that uses React hooks and starts with the word ***use***.
- It helps you share logic between components without repeating code.
  
### ***Custom Hook**
```jsx
import { useState } from "react";

function useCounter() {
  const [count, setCount] = useState(0);

  const increment = () => setCount(count + 1);
  const decrement = () => setCount(count - 1);

  return { count, increment, decrement };
}

```

### ***Use the Hook in a Component***
```jsx
function Counter() {
  const { count, increment, decrement } = useCounter();

  return (
    <>
      <p>{count}</p>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
    </>
  );
}
```

### Key Points
- Must start with use
- Only call hooks at the top level
- Only call hooks from:
    1. React function components, or
    2. Other custom hooks

 ### Benefit
 
|       Name                 | Description                                                   |
| ------------------------- | ------------------------------------------------------------- |
| ♻ **Reusability**         | Write once, reuse logic across multiple components.           |
| ✨ **Cleaner Code**        | Keeps components simple by separating logic.                  |
| 🧪 **Testability**        | Easier to test logic in isolation (without UI).               |
| 📦 **Abstraction**        | Hides complex logic behind a simple API.                      |
| 📁 **Organized Codebase** | Logic and UI are better separated, improving maintainability. |

***Difference from Component***

| Custom Hook            | Component            |
| ---------------------- | -------------------- |
| Reuses logic           | Renders UI           |
| No JSX return          | Returns JSX          |
| Used inside components | Used in UI tree      |
| Starts with `use`      | Normal function name |


### Use Cases

***API Calls***
```jsx
function useUsers() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetch("/api/users")
      .then(res => res.json())
      .then(setUsers);
  }, []);

  return users;
}
```

***Loaders***
```jsx
function useFetchData() {
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    setLoading(true);
    fetchData().finally(() => setLoading(false));
  }, []);

  return loading;
}
```
***Handling Repeated Side Effects***
- Window resize
- Scroll position
- Online/offline status
- Event listeners
  
```jsx
function useWindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    const handler = () => setWidth(window.innerWidth);
    window.addEventListener("resize", handler);
    return () => window.removeEventListener("resize", handler);
  }, []);

  return width;
}
```

***Abstracting Complex State Logic***
- Forms
- Multi-step flows
- Validation
  
```jsx
function useForm(initialValues) {
  const [values, setValues] = useState(initialValues);

  const handleChange = (e) => {
    setValues({ ...values, [e.target.name]: e.target.value });
  };

  return { values, handleChange };
}
```

***localStorage***
```jsx
function useLocalStorage(key, initial) {
  const [value, setValue] = useState(() => {
    return JSON.parse(localStorage.getItem(key)) ?? initial;
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
}
```

<br>

## 4. Difference between useState and useReducer

| Feature      | useState             | useReducer             |
| ------------ | -------------------- | ---------------------- |
| Complexity   | Simple state         | Complex state logic    |
| Update style | Direct update        | Action based update    |
| Readability  | Less for large state | Better for large state |
| Use case     | Counters, toggles    | Forms, complex flows   |

<br>

## 5. Difference between useEffect and useLayoutEffect

| Feature        | useEffect    | useLayoutEffect      |
| -------------- | ------------ | -------------------- |
| Execution time | After paint  | Before paint         |
| UI blocking    | Non blocking | Blocking             |
| Performance    | Better       | Can hurt performance |
| Use case       | API calls    | DOM measurements     |

<br>

## 6. Difference between useMemo and useCallback

| Feature      | useMemo                | useCallback                      |
| ------------ | ---------------------- | -------------------------------- |
| Purpose      | Memoizes value         | Memoizes function                |
| Return value | Cached value           | Cached function                  |
| Usage        | Expensive calculations | Prevent re creation of functions |

<br>

## 7. Difference between useRef and useState

| Feature    | useRef             | useState             |
| ---------- | ------------------ | -------------------- |
| Re render  | Does not re render | Re renders component |
| Mutability | Mutable            | Immutable            |
| Use case   | DOM access         | UI updates           |

<br>

## 8. Types of Hooks in React?

***Basic Hooks***
| Hook       | Purpose             | Use Case                 | Example                                 |
| ---------- | ------------------- | ------------------------ | --------------------------------------- |
| useState   | Manage local state  | Form inputs, counters    | `const [count, setCount] = useState(0)` |
| useEffect  | Handle side effects | API calls, subscriptions | `useEffect(() => fetchData(), [])`      |
| useContext | Consume context     | Theme, auth, language    | `const theme = useContext(ThemeCtx)`    |

***Additional Hooks***
| Hook        | Purpose                         | Use Case                 | Example                                               |
| ----------- | ------------------------------- | ------------------------ | ----------------------------------------------------- |
| useRef      | Persist value without re-render | Focus input, timers      | `const inputRef = useRef(null)`                       |
| useReducer  | Complex state logic             | Forms, state machines    | `const [state, dispatch] = useReducer(reducer, init)` |
| useCallback | Memoize function                | Prevent child re-renders | `useCallback(fn, [])`                                 |
| useMemo     | Memoize value                   | Expensive calculations   | `useMemo(calc, [deps])`                               |

***Layout & Effect Hooks***
| Hook               | Purpose                     | Use Case            | Example                            |
| ------------------ | --------------------------- | ------------------- | ---------------------------------- |
| useLayoutEffect    | DOM-read/write before paint | Measure layout      | `useLayoutEffect(() => {}, [])`    |
| useInsertionEffect | Inject styles before paint  | CSS-in-JS libraries | `useInsertionEffect(() => {}, [])` |

***Use Case Mapping***
| Scenario            | Best Hook             |
| ------------------- | --------------------- |
| Input handling      | useState              |
| API call            | useEffect             |
| Avoid re-render     | useMemo / useCallback |
| Share data globally | useContext            |
| Heavy UI update     | useTransition         |
| Access DOM          | useRef                |
| Complex logic       | useReducer            |

<br>

## 9. useMemo

***useMemo*** returns a cached value and recomputes it only when its dependencies change.

```jsx
import { useMemo, useState } from "react";

function App() {
  const [count, setCount] = useState(0);
  const [number, setNumber] = useState(5);

  const squared = useMemo(() => {
    console.log("Calculating...");
    return number * number;
  }, [number]);

  return (
    <>
      <p>Square: {squared}</p>
      <button onClick={() => setCount(count + 1)}>Re render</button>
    </>
  );
}
```

🔥  ***When to use useMemo***
- ✔ Prevent unnecessary re computations
- ✔ Expensive calculations
- ✔ Large array filtering
- ✔ Sorting big data
- ✔ Complex derived values
- ✔ Performance optimization needed

🚫 ***When NOT To Use useMemo***
- ❌ For small simple calculations
- ❌ Just to “optimize everything”
- ❌ Without performance issue
- Because useMemo itself has some overhead.

🏋️ ***Real Life Example***

**You have:**
- 10,000 employee records
- You calculate total yearly salary
- This calculation is heavy.

**Now suppose:**
*You also have a button that changes theme color.*
- When theme changes:
- Component re-renders.

**Without useMemo:**
- Total salary recalculates again ❌
- Even though salary data did not change.

**With useMemo:**
- React remembers the previous calculation ✅
- It only recalculates if employee data changes.

<br>
  
## 10. useCallback

***useCallback*** remembers a function, so it is not recreated on every render.

```jsx
import React, { useState, useCallback } from "react";

function App() {
  const [count, setCount] = useState(0);

  const sayHello = useCallback(() => {
    console.log("Hello");
  }, []);

  return (
    <>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>Increase</button>
      <button onClick={sayHello}>Say Hello</button>
    </>
  );
}

export default App;
```
- useCallback remembers a function so it is not recreated on every re render.
- React normally creates functions again and again.
- useCallback stops that and keeps the same function.


🔥 When To Use useCallback
- ✔ Passing functions to child components
- ✔ Child component is wrapped with React.memo
- ✔ Function is inside dependency array of useEffect
- ✔ Performance optimization is needed


🚫 When NOT To Use It
- ❌ If function is not passed to children
- ❌ If app is small
- ❌ If there is no performance issue
- ❌ For every function blindly
  
<br>

## 11. Difference between custom hook and components

| Aspect          | Component                  | Custom Hook             |
| --------------- | -------------------------- | ----------------------- |
| Purpose         | Builds and renders UI      | Reuses stateful logic   |
| Return value    | JSX                        | Data, state, functions  |
| Naming          | Starts with Capital letter | Must start with `use`   |
| Can use hooks   | Yes                        | Yes                     |
| Can return JSX  | Yes                        | No                      |
| Reusability     | Reused by rendering        | Reused by calling       |
| Concern         | UI + behavior              | Behavior only           |
| Example usage   | `<UserProfile />`          | `useUser()`             |
| Best used when  | You need to show something | You need to share logic |
| React treats as | Renderable element         | Logic abstraction       |

## 12. useReducer vs Context API vs Redux

🎬 Imagine You Are Managing a Restaurant

***We’ll compare:***
- useReducer
- Context API
- Redux


## 1️⃣ useReducer
🍳 `Kitchen Manager`

*Inside the kitchen:*

***Many things can happen to one order:****

- Add item
- Remove item
- Change quantity
- Apply discount
- Cancel order

*All these actions affect one order state.*

***You need rules:***
- Cannot cook if ingredients are out
- Cannot apply discount twice
- Cannot cancel after serving

So the kitchen manager handles all order decisions properly.

👉 **That kitchen manager = useReducer**

***When to use:***
- One feature
- Complex logic
- Many actions changing the same state
- Clear decision rules needed

## 2️⃣ Context API
📢 `Restaurant Announcement System`

*Now imagine:*

***The restaurant has information everyone needs:***
- Today’s special dish
- Restaurant closing time
- Discount day
- Chef name

*Instead of telling each waiter individually, you make an announcement.*

*Everyone can access that info easily.*

👉 **That announcement system = Context API**

***When to use:***
- Theme
- Logged-in user
- Language
- Small global settings
- Data that many components need

***Important:***
- Context only shares data.
- It does not manage complex logic.


## 3️⃣ Redux
🏢 `Restaurant Franchise Headquarters`

***Now imagine:***

*You own 200 restaurant branches across the country.*

***You need:***
- Centralized order tracking
- Inventory management
- Employee management
- Payment systems
- Analytics
- Logging
- Strict rules
- Audit tracking

*You cannot manage this casually.*

***You need:***
- Structured flow
- Predictable updates
- Middleware
- Debugging tools
- Scalable architecture

👉 **That headquarters system = Redux**



## 13. Form Validation (Custom Hook)

Simple Custom Hook for Form Validation

### ✅ Step 1: Create Simple Custom Hook
`📄 useForm.js`
```jsx
import { useState } from "react";

function useForm(initialValues) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});

  const handleChange = (e) => {
    const { name, value } = e.target;

    setValues({
      ...values,
      [name]: value,
    });
  };

  const validate = () => {
    let newErrors = {};

    if (!values.name) {
      newErrors.name = "Name is required";
    }

    if (!values.email) {
      newErrors.email = "Email is required";
    }

    if (!values.password || values.password.length < 6) {
      newErrors.password = "Password must be 6+ characters";
    }

    setErrors(newErrors);

    return Object.keys(newErrors).length === 0;
  };

  const handleSubmit = (e, callback) => {
    e.preventDefault();

    if (validate()) {
      callback(); // only runs if valid
    }
  };

  return {
    values,
    errors,
    handleChange,
    handleSubmit,
  };
}

export default useForm;
```

### ✅ Step 2: Use It in Form Component
`📄 RegisterForm.jsx`

```jsx
import useForm from "./useForm";

function RegisterForm() {
  const { values, errors, handleChange, handleSubmit } = useForm({
    name: "",
    email: "",
    password: "",
  });

  const onSubmit = () => {
    console.log("Form Submitted", values);
  };

  return (
    <form onSubmit={(e) => handleSubmit(e, onSubmit)}>
      <div>
        <input
          name="name"
          placeholder="Name"
          value={values.name}
          onChange={handleChange}
        />
        {errors.name && <p>{errors.name}</p>}
      </div>

      <div>
        <input
          name="email"
          placeholder="Email"
          value={values.email}
          onChange={handleChange}
        />
        {errors.email && <p>{errors.email}</p>}
      </div>

      <div>
        <input
          type="password"
          name="password"
          placeholder="Password"
          value={values.password}
          onChange={handleChange}
        />
        {errors.password && <p>{errors.password}</p>}
      </div>

      <button type="submit">Submit</button>
    </form>
  );
}

export default RegisterForm;
```

### 🔥 What This Does

- Custom hook manages state
- Validation runs on submit
- If errors exist → form does not submit
- Component stays clean

## 14. Form Validation (react hook form)

Unlike normal React forms:

- It uses uncontrolled components
- It does less re-rendering
- Very small bundle size
- Easy integration with validation libraries like ***Yup***

```jsx
import { useForm } from "react-hook-form";

function RegisterForm() {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm();

  const onSubmit = (data) => {
    console.log("Form Submitted", data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      
      <input
        placeholder="Name"
        {...register("name", {
          required: "Name is required",
        })}
      />
      {errors.name && <p>{errors.name.message}</p>}

      <input
        placeholder="Email"
        {...register("email", {
          required: "Email is required",
          pattern: {
            value: /^\S+@\S+\.\S+$/,
            message: "Invalid email format",
          },
        })}
      />
      {errors.email && <p>{errors.email.message}</p>}

      <input
        type="password"
        placeholder="Password"
        {...register("password", {
          required: "Password is required",
          minLength: {
            value: 6,
            message: "Minimum 6 characters",
          },
        })}
      />
      {errors.password && <p>{errors.password.message}</p>}

      <button type="submit">Submit</button>
    </form>
  );
}

export default RegisterForm;
```
<br>

## 15. Form Validation (formik)

```jsx
import { useFormik } from "formik";

function LoginForm() {
  const formik = useFormik({
    initialValues: {
      email: "",
      password: "",
    },

    validate: (values) => {
      const errors = {};

      if (!values.email) {
        errors.email = "Email is required";
      }

      if (!values.password) {
        errors.password = "Password is required";
      }

      return errors;
    },

    onSubmit: (values) => {
      console.log("Form Submitted", values);
    },
  });

  return (
    <form onSubmit={formik.handleSubmit}>
      <input
        name="email"
        placeholder="Email"
        onChange={formik.handleChange}
        value={formik.values.email}
      />
      {formik.errors.email && <p>{formik.errors.email}</p>}

      <input
        type="password"
        name="password"
        placeholder="Password"
        onChange={formik.handleChange}
        value={formik.values.password}
      />
      {formik.errors.password && <p>{formik.errors.password}</p>}

      <button type="submit">Submit</button>
    </form>
  );
}

export default LoginForm;
```

### 🔥 What Happens

- useFormik() manages form state
- validate() runs before submit
- If errors exist → form does NOT submit
- onSubmit() runs only if no errors
