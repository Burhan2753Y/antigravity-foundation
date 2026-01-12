---
trigger: always_on
---

Security Guidelines

Secrets & Config

Caution: Avoid outputting API keys or passwords in plain text.

Prefer using process.env or a configuration file for secrets where feasible.

Ensure .env files are included in .gitignore.

Input Validation

Sanitize HTML inputs to help prevent XSS issues.

Basic validation for file uploads (type and size) is recommended.

Use parameterized queries or an ORM to mitigate SQL injection risks.

Authentication & Access Control

Use standard libraries (e.g., bcrypt) for password hashing.

Verify authorization headers on protected API endpoints.

Consider rate limiting on public forms if spam becomes an issue.

Grant permissions based on the functional needs of the service.

Error Handling

Production: Avoid exposing full stack traces to end users.

Feedback: Use user-friendly error messages (e.g., "Invalid login") to avoid confusion.

Dependency Management

Keep dependencies reasonably up to date.

Periodically check for high-severity vulnerabilities (e.g., via npm audit).

Logging & Monitoring

Avoid logging sensitive data like passwords or full tokens.

Log important events like failed login attempts to assist with debugging.