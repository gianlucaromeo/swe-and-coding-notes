# Temporal Dead Zone (TDZ)

> TDZ is the period between when a variable is hoisted and when it is initialized.

A variable declared with `let`, `const`, or `class` is said to be in a "temporal dead zone" (TDZ) from the start of the block until code execution reaches the place where the variable is declared and initialized.


```javascript
console.log(a); // undefined
var a = 1;      // ✅ no TDZ with var

console.log(b); // ❌ ReferenceError (TDZ)
let b = 2;

console.log(c); // ❌ ReferenceError (TDZ)
const c = 3;
```

While `var` values will return `undefined` when accessed before declaration, other type of variables will return a value of `undefined` if accessed after declaration but a declaration was not provided:

```javascript
console.log(a)  // ❌ ReferenceError
let a;
console.log(a)  // undefined
```

The zone depends on the time the code is executed rather than the position it is written in:

```javascript
{
  // Start of TDZ for letVar

  const func = () => console.log(letVar); // OK

  let letVar = 3; // End of TDZ for letVar

  func(); // Called outside TDZ
}
```