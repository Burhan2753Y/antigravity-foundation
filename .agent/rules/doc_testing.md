---
trigger: always_on
---

## Documentation & Testing Requirements

- **Documentation:**
  - Every exported function/class must have a TSDoc/Docstring explaining:
    1. What it does.
    2. Parameters.
    3. Return value.
  - Update `README.md` if new environment variables or architectural changes are introduced.

- **Testing:**
  - Write unit tests for all business logic and utility functions.
  - Follow the "Arrange, Act, Assert" pattern in test cases.
  - Mock external API calls; never hit real endpoints in test suites.
  - Aim for high test coverage, but prioritize critical paths over 100% coverage.