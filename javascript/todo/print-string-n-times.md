## How to print a string n times?

In Javasript, because of coersion, you cannot multiply a string n times to print it n times:

```javascript
console.log("5" * 2) // ❌ Prints 10, not "55"
console.log("A" * 5) // ❌ Prints NaN, not "AAAAA"
```

## Solution 1

Use the method ```.repeat(count)``` passing an integer between 0 and ```Infinity```:

```javascript
"abc".repeat(-1);    // RangeError
"abc".repeat(0);     // ''
"abc".repeat(1);     // 'abc'
"abc".repeat(2);     // 'abcabc'
"abc".repeat(3.5);   // 'abcabcabc' (count will be converted to integer)
"abc".repeat(1 / 0); // RangeError
```
