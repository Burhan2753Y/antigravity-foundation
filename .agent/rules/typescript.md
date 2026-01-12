---
trigger: glob
globs: *.ts
---

## TypeScript Guidelines

- **Strict Typing:**
  - Avoid `any` at all costs. Use `unknown` if the type is truly dynamic, then narrow it.
  - Prefer `interface` for object definitions and `type` for unions/intersections.
  - Ensure strict null checks are respected.

- **Syntax & Style:**
  - Use `const` by default; use `let` only if reassignment is necessary. Never use `var`.
  - Use arrow functions for callbacks and simple functional components.
  - Prefer async/await over raw `.then()` chains.

- **Data Handling:**
  - Use optional chaining (`?.`) and nullish coalescing (`??`) for safer property access.
  - Use immutability practices (e.g., spread operator `...`) rather than mutating objects/arrays directly.

- **Logging:**
* **Library:** Use Morgan for HTTP request-specific logs, and general-purpose loggers Pino for application-level events and errors.
* **Frontend:** `console.log` is forbidden in production builds. Use a wrapper or remove via build plugins (e.g., `terser`).