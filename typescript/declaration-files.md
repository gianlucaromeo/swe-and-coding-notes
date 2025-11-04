# Declaration files (`.d.ts`)

A **TypeScript declaration file** (`.d.ts`) describes the **type shape** of code that exists at runtime. It tells the compiler *what types exist*, not *what code does*.

### Purpose

* Provides **type information** for JS code or external libraries.
* Allows autocompletion and static checking without re-implementing logic.
* Loaded automatically by the compiler when in scope.

---

### Allowed contents

* `declare` keyword — defines ambient symbols:

  ```ts
  declare const VERSION: string;
  declare function init(config: object): void;
  declare class User { id: number; name: string; }
  declare namespace API { function get(): void; }
  declare module "lib" { export function run(): void; }
  ```

* `interface`, `type`, `enum` — same as normal `.ts` files.

* `export` / `import` — for modular `.d.ts` files.

* JSDoc comments for documentation.

No executable code is allowed. The compiler strips all declarations at compile time.

---

### Common keywords and constructs

| Keyword                                     | Purpose                                                                  |
| ------------------------------------------- | ------------------------------------------------------------------------ |
| `declare`                                   | Defines something that exists elsewhere (e.g., global variable, module). |
| `namespace`                                 | Groups related declarations under one scope.                             |
| `global`                                    | Adds declarations to the global scope.                                   |
| `module`                                    | Declares typings for an imported module.                                 |
| `interface` / `type`                        | Defines shape of objects or aliases.                                     |
| `typeof import("x")`                        | Reuses types from another module.                                        |
| `keyof`, `typeof`, `infer`, `extends`, `as` | Type-level operators, usable as in `.ts`.                                |
| `unique symbol`                             | Creates nominally unique symbol types.                                   |
| `IntrinsicAttributes` / `IntrinsicElements` | Used internally by JSX to describe built-in elements.                    |
| `__intrinsic` or “intrinsic” comments       | Compiler-internal annotations. Not a language feature.                   |

---

### Typical usage patterns

* **Add typings for JS files:**

  ```ts
  declare function sum(a: number, b: number): number;
  ```
* **Augment a library:**

  ```ts
  declare module "express" {
    interface Request { user?: User; }
  }
  ```
* **Global declarations:**

  ```ts
  declare global {
    interface Window { appVersion: string; }
  }
  ```

---

### Summary

`.d.ts` files:

* Contain only type declarations.
* Are merged automatically by TypeScript.
* Use `declare`, `namespace`, and other type-level constructs.
* Cannot run code.
* May use internal “intrinsic” types or attributes, but these are compiler-level and not user-defined.
