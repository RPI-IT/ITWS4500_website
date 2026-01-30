# Lab 3: React Basics

## How to Think About React Components (Read This First)

Before writing code, it’s important to understand what a **React component really is**. This mental model will make everything else in this lab—and later in Next.js—much easier to understand.

### What Is a React Component?

At its core:

> **A React component is just a JavaScript function that returns UI.**

Example:

```jsx
function Greeting() {
  return <h2>Welcome to React Basics!</h2>
}
```

This is not a template or special syntax. It is a normal JavaScript function.

When React sees `<Greeting />` in your code, it:
1. Calls the `Greeting()` function
2. Takes the JSX it returns
3. Displays that JSX on the screen

You can think of this as:

```js
Greeting()
```

producing UI.

---

### Components Are Re-Executed on Every Render

A critical mental model in React is this:

> **React re-runs your component function every time state changes.**

For example:

```jsx
function App() {
  const [count, setCount] = useState(0)
  return <p>Count: {count}</p>
}
```

When `setCount()` is called:
1. React stores the new state
2. React re-runs `App()`
3. React compares the old UI to the new UI
4. Only the changed parts of the DOM are updated

This is why:
- You never manually update the DOM
- UI is always a function of state

---

### Why Component Names Must Be Capitalized

React uses capitalization to distinguish components from HTML.

```jsx
<Greeting />   // React component → calls Greeting()
<greeting />   // HTML element → looks for <greeting> tag
```

**Rule:**
- Capital letter → React component  
- Lowercase → HTML element  

---

### Props vs State (Very Important)

| Concept | Description |
|------|-------------|
| **Props** | Data passed *into* a component |
| **State** | Data managed *inside* a component |
| **Props** | Read-only |
| **State** | Can change over time |

---

### Data Flow in React

> **Data flows down, events flow up**

Parents pass data to children via props.  
Children notify parents of events via callback functions.  
Parents update state.  
React re-renders the UI.

---

## Overview

This lab introduces the fundamentals of React. React is a JavaScript library for building user interfaces. Understanding React is essential because Next.js is built on top of it.

## What You'll Learn

- What React components are
- How to manage state with `useState`
- How to handle user events (clicks, form inputs)
- How to pass data between components with props
- How to render lists of items

---

## Part 1: Setting Up

### What is Vite?

Vite is a build tool that creates a development environment for modern JavaScript projects. It provides:
- Fast hot module replacement (changes appear instantly)
- A development server
- Optimized production builds

### Step 1: Create a New React Project

```bash
npx create-vite react
```

---

## (Remaining lab content continues exactly as provided.)
