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