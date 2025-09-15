# What is scope and which types of scope are there in Javascript?

Scope defines where in code you can access a variable or function.

In other words, scope is the "current context of executino in which values and expressions are visible or can be referenced".

Scopes can also be layered in a hierarchy, so that child scopes have access to parent scopes, but not vice versa.

## Types of scope

| Type           | Description                                                                                                       |
| -------------- | ----------------------------------------------------------------------------------------------------------------- | 
| Global         | Variables declared outside any block or function are accessible everywhere.                                       |
| Function       | Variables declared inside a function exists only within that function.                                            |
| Block          | Variables declared with `let`or `const` are limited to `{ }` (if, for, while, etc.), but `var` ignores this rule. |

Note: inner functions can access variables from their parent functions.


## Sample code

```javascript
// Global scope
let globalVar = "globalVar: I am global";

function myFunction() {
  // Function scope
  var functionVar = "functionVar: I am inside function";
  
  console.log("[Inside function, before block]")
  console.log(globalVar); // ✅
  console.log(functionVar); // ✅
  console.log("blockLet: ❌ ReferenceError") // ❌
  console.log("blockConst: ❌ ReferenceError") // ❌
  console.log("blockVar: ", blockVar) // ✅ accessible but undefined

  if (true) {
    // Block scope
    let blockLet = "blockLet: I am inside block";
    const blockConst = "blockConst: I am also inside block";
    var blockVar = "blockVar: I leak out of block (var)";
    
    console.log("\n[Inside block]");
    console.log(globalVar);
    console.log(functionVar);
    console.log(blockLet);
    console.log(blockConst);
    console.log(blockVar);
  }

  console.log("\n[Inside function, after block]");
  console.log(globalVar); // ✅ accessible
  console.log(functionVar); // ✅ accessible
  console.log("blockLet: ❌ ReferenceError"); // ❌ outside of its scope
  console.log("blockConst: ❌ ReferenceError"); // ❌ outside of its scope
  console.log(blockVar); // ✅ accessible because var ignores block
}

myFunction();

console.log("\n[Global scope]");
console.log(globalVar); // ✅ accessible
console.log("functionVar: ❌ ReferenceError"); // ❌ outside of its scope
console.log("blockLet: ❌ ReferenceError"); // ❌ outside of its scope
console.log("blockConst: ❌ ReferenceError"); // ❌ outside of its scope
console.log("blockVar: ❌ ReferenceError"); // ❌ outside of its scope
```

Output:

```
[Inside function, before block]
globalVar: I am global
functionVar: I am inside function
blockLet: ❌ ReferenceError
blockConst: ❌ ReferenceError
blockVar:  undefined

[Inside block]
globalVar: I am global
functionVar: I am inside function
blockLet: I am inside block
blockConst: I am also inside block
blockVar: I leak out of block (var)

[Inside function, after block]
globalVar: I am global
functionVar: I am inside function
blockLet: ❌ ReferenceError
blockConst: ❌ ReferenceError
blockVar: I leak out of block (var)

[Global scope]
globalVar: I am global
functionVar: ❌ ReferenceError
blockLet: ❌ ReferenceError
blockConst: ❌ ReferenceError
blockVar: ❌ ReferenceError
```