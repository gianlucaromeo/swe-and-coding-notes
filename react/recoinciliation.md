# Reconciliation

Reconciliation is the process React uses to figure out how to efficiently update the DOM (Document Object Model) when changes occur in the UI. React's goal is to update the page as efficiently as possible, without unnecessary re-rendering or slow performance.

> Note: React doesn't diff it's virtual DOM with the real DOM, but it diffs it with a previous instance of a virtual DOM, and then communicates changes to the real DOM.

## How React checks diffs

React’s reconciliation works primarily by **reference equality**, not deep value checks:

- **Primitive props** (`string`, `number`, `boolean`): compared by value.  
  Example: `42 === 42` → no re-render if unchanged.

- **Objects, arrays, functions**: compared by reference.  
  Example: `{} === {}` → always false, even if contents look the same.  
  Example: if a new function is created on every render, React sees it as "changed."

- **Children**: If the parent does not re-render, the children prop reference is not re-created. React skips reconciliation for them.

- **Keys**: For lists, React uses `key` to decide whether to reuse or recreate elements.