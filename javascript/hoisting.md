# Hoisting

Hoisting is the process whereby the interpreter moves declaration of functions, variables, classes, imports to the top of their scope during compilation, before the execution of the code.

## `var` hoisting

A variable declared as `var`is hoisted at the top of its scope and initialized with undefined. This means that you can access it anywhere within the scope, even if it's declaration isn't reached yet.

Only its declaration and default initialization (with undefined) is hoisted, but not its value assignment.

```javascript
console.log(a); // undefined (not error, because hoisted)
var a = 5;
```

## `let` and `const` hoisting

They are hoisted but but left uninitialized.

Referencing the variable in the block before its declaration always results in a `ReferenceError`, because the variable is in a "temporal dead zone" from the start of the block until the declaration is processed.

```javascript
console.log(x); // ReferenceError
const x = 3;

console.log(y); // ReferenceError
let y = 3;
```

## Functions hoisting

Unlike var declarations, which only hoist the declaration but not its value, function declarations are hoisted entirely — you can safely call the function anywhere in its scope.

```javascript
sayHi(); // works

function sayHi() {
  console.log("Hello");
}
```

But careful: Function hoisting only works with function declarations - not with function expressions:

```javascript
console.log(square(5)); // ReferenceError: Cannot access 'square' before initialization

const square = function (n) {
  return n * n;
};
```

## Class hoisting

Classes behaves similarly to `let` and `const`, meaning that they are hoisted but in the TDZ until they are evaluated.

```javascript
new MyClass(); // ReferenceError: Cannot access 'MyClass' before initialization

class MyClass {}
```

## Import hoisting

Import declarations are hoisted, meaning that the imported values are available in the module's code even before the place it is declared.

However, it is best practice to put all the imports on top.

This code works:

```javascript
// ... Some code

const something = Something("ABC", 123);
something.doSomething();

// Import is in the middle!
import { Something } from './somewhere/something.js';

something.doSomethingElse();

// ... Some code
```