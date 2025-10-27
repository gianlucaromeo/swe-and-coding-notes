# NPM Package Installation Guide

## Summary Table

| Syntax             | Meaning                        |
| ------------------ | ------------------------------ |
| `package`          | Latest public version          |
| `@scope/package`   | Scoped package                 |
| `package@latest`   | Explicit latest tag            |
| `package@x.y.z`    | Exact version                  |
| `package@^x.y.z`   | Compatible minor/patch updates |
| `package@~x.y.z`   | Only patch updates             |
| `-g`               | Global install                 |
| `--save-dev`       | Dev dependency                 |
| `user/repo` or URL | Git or local source            |


## 1. Scoped vs Unscoped Packages

### Unscoped
```bash
npm install express
````

Installs from the public npm registry at the latest stable version.
→ Package name: `express`.

### Scoped

```bash
npm install @nestjs/core
```

The `@scope/` prefix identifies a namespace (often an organization).
→ `@nestjs` is the scope, `core` is the package.
These may come from private registries or npm’s default one.

---

## 2. Version Specifiers

### a. Latest (default)

```bash
npm install express@latest
```

Equivalent to:

```bash
npm install express
```

Pulls the version tagged as `latest` in npm (usually the newest stable release).

### b. Specific Version

```bash
npm install express@4.17.1
```

Installs that exact version.

### c. Version Range

```bash
npm install express@^4.17.0
npm install express@~4.17.0
```

* `^` → allows newer *minor* and *patch* updates (e.g., 4.18.0).
* `~` → allows only *patch* updates (e.g., 4.17.5).
* No symbol → exact version.

---

## 3. Global vs Local Installation

### Local (default)

```bash
npm install express
```

Adds package to `node_modules/` and `package.json` dependencies.

### Global

```bash
npm install -g nodemon
```

Installs system-wide for CLI use.

---

## 4. Dev Dependencies

```bash
npm install --save-dev eslint
```

Saved under `"devDependencies"` for tools not required in production.

---

## 5. Installing from Other Sources

```bash
npm install user/repo
npm install https://github.com/user/repo
npm install ./local-folder
```

Installs directly from GitHub or local paths.
