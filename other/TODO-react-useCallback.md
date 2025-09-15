```javascript
import React, { useState, useRef, useCallback } from "react";

export default function App(props) {
  // Piu useState -> Piu sporcizia -> Qualcosa con useRef probabilmente
  const [x, setX] = useState(false);

  // Tenere valore nonostante i rendering, come "valori globali"
  const y = useRef(x ? "On" : "Off");

  const onClickCallback = useCallback(() => {
    setX((value) => {
      y.current = !value ? "On" : "Off";
      return !value;
    });
  }, [setX]); // Non serve in realtà perché è una "stable reference"

  return (
    <div className="App">
      <p children={y.current} />
      <button onClick={onClickCallback}>Cliccami</button>
    </div>
  );
}

// Log to console
// console.log(typeof Number("65"));
// console.log(typeof parseInt("65"));
// console.log(typeof ("65" * 1));

// Stampare 66 come numero usando 1 e "65", no classi, no funzioni
// console.log(1 + +"65");

console.log("5");
console.log(typeof "5");
console.log(Number("5"));
console.log(typeof Number("5"));

console.log();

console.log("5" * 10);
console.log(typeof ("5" * 10));

console.log();

console.log("5" + 10);
console.log();

console.log(+"34");

console.log(1 + +"1");
```