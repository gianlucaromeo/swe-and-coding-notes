# Type coercion

Coercion is the automatic or implicit conversion of values from one data type to another (such as strings to numbers).

## Example

```javascript
console.log("5" * 2) // 10
console.log("5" + 2) // "7"
```

## Some rules 

* If one operand is a **string** and the operator is `+` → convert both to strings → concatenate.
* For other arithmetic operators (`-`, `*`, `/`, `%`, `**`) → convert operands to numbers.
* In **comparisons (`==`)** → values are converted to a common type:
  * `null` and `undefined` equal each other, and nothing else.
  * Booleans convert to numbers (`true → 1`, `false → 0`).
  * Strings convert to numbers if compared with a number.
* In **boolean contexts** (`if`, `while`, `!`, `&&`, `||`) → values convert to `true` or `false` using *truthy/falsy* rules.
* `===` never coerces. Only `==` does.

## Some examples

| Expression      | Result              | Why                                                   |
| --------------- | ------------------- | ----------------------------------------------------- |
| `"5" * 2`       | `10`                | Multiplication coerces strings to numbers |
| `"5" + 2`       | `"52"`              | Plus operator coerces to concatenate strings |
| `true + 1`      | `2`                 | `true` coerces to `1` |
| `false + 1`     | `1`                 | `false` coerces to `0` |
| `null + 1`      | `1`                 | `null` coerces to `0` |
| `undefined + 1` | `NaN`               | `undefined` becomes `NaN` in numeric context |
| `[] + []`       | `""`                | Arrays become empty strings attempting concatenation |
| `[] + {}`       | `"[object Object]"` | Array becomes string `""`; Object becomes `"[object Object]"`; Plus sign sees at least one string, so it attempts concatenation |
| `{} + []`       | `"[object Object]"` | Same as above |
| `false == 0`    | `true`              | `false` is coerced to `0` in `==` |
| `false == "0"`  | `true`              | `"0"` is coerced to `0` |
| `0 == ""`       | `true`              | `""` is coerced to `0` |
| `[] == ![]`     | `true`              | `![]` coerced to `false` and then to `0`; `[]` becomes `0` |

## Test this javascript madness yourself!

```javascript
const tests = [
  ["'5' * 2", "5" * 2],             // 10
  ["'5' + 2", "5" + 2],             // "52"
  ["true + 1", true + 1],           // 2
  ["false + 1", false + 1],         // 1
  ["null + 1", null + 1],           // 1
  ["undefined + 1", undefined + 1], // NaN
  ["[] + []", [] + []],             // ""
  ["[] + {}", [] + {}],             // "[object Object]"
  ["{} + []", {} + []],             // "[object Object]"
  ["false == 0", false == 0],       // true
  ["false == '0'", false == "0"],   // true
  ["0 == ''", 0 == ""],             // true
  ["[] == ![]", [] == ![]],         // true
]

tests.forEach(([expr, value]) => {
  console.log(expr.padEnd(15), "=>", value)
})
```