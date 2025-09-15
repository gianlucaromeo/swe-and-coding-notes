```javascript
import React, { useState, useCallback } from "react";

export default function App() {
  const [isOn, setIsOn] = useState(false);

  // Stable handler. Identity never changes.
  const handleToggle = useCallback(() => {
    setIsOn(prev => !prev);
  }, []);

  return (
    <div className="App">
      <p>{isOn ? "On" : "Off"}</p>
      <button type="button" onClick={handleToggle}>Click me</button>
    </div>
  );
}

```


## `useCallback` vs Inline Functions in React

In React, every time a component re-renders, **inline functions** are re-created.  
For example:

```jsx
<button onClick={() => setIsOn(prev => !prev)}>Click me</button>
````

The arrow function above is a **new function object** on every render.
This usually isn’t a problem, but it can cause:

* **Unnecessary re-renders** in child components that are wrapped with `React.memo`, because props change identity even if the logic didn’t.
* **Broken dependency checks** in hooks (`useEffect`, `useMemo`) if you pass the function inside their dependency array.

---

### Why `useCallback`

`useCallback` ensures your function keeps the same identity between renders unless its dependencies change:

```jsx
const handleToggle = useCallback(() => {
  setIsOn(prev => !prev);
}, []);
```

* `handleToggle` is stable across renders.
* Child components that rely on prop identity won’t re-render unnecessarily.
* Useful when passing handlers deep down to optimized children.

---

### Rule of Thumb

* **Inline function** → fine if the function is only used inside the component and not passed as a prop.
* **`useCallback`** → use when passing the function as a prop to memoized children or when the function is a dependency in other hooks.