# Undefined vs null

* `undefined` means a variable has been declared but not assigned or a function has no return. 

* `null` is an intentional assignment representing no value.

```javascript
console.log(undefined == null); // true, loose equality considers them equal
console.log(undefined === null); // false, they are not strictly equal in type
```