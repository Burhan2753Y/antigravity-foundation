---
trigger: always_on
---

# Logging & Observability Rules

## 1. Core Directives
> **Strict Rule:** **NEVER** use `print()`, `console.log()`, `fmt.Println()`, or similar standard output methods for application logic or debugging in production code.

* **Production Code:** Must use a structured logging library.
* **Debugging:** Temporary print statements used during local development **must** be removed before committing.
* **Exceptions:** CLI tools interacting directly with `stdout`/`stderr` for user prompts are the only exception.

---
## 2. Log Levels Strategy

Use the appropriate level to ensure noise reduction in logging systems (Datadog/Splunk).

| Level | Usage | Example |
| :--- | :--- | :--- |
| **DEBUG** | Granular, high-volume events useful only for development/troubleshooting. | Payload details, specific logic branches taken. |
| **INFO** | Confirming successful high-level operations. | "Service started", "Job completed", "Request received". |
| **WARN** | Unexpected events that are recoverable. Handled errors that don't crash the app. | "Rate limit approaching", "Retrying database connection". |
| **ERROR** | Functionality failed. Human intervention or investigation is likely required. | "Payment gateway timeout", "Null pointer exception". |
| **FATAL** | The application cannot continue running and will crash/exit. | "Missing env var", "Disk full". |

---

## 3. Structuring & Formatting

### JSON Requirement
In **Production** and **Staging** environments, all logs must be emitted as single-line JSON objects.
* **Why?** Allows aggregators to parse, index, and query fields efficiently.
* **Local Dev:** Pretty-printed text is acceptable for readability.

### Contextual Fields (The "What")
Do not rely on message strings for data. Use keys/attributes.

* **Traceability:** `trace_id`, `span_id`, `request_id` (Required for distributed systems).
* **Identity:** `user_id`, `tenant_id`, `org_id`.
* **Origin:** `service_name`, `environment` (prod/stage), `version`.
* **Error Details:** `stack_trace` (only on ERROR/FATAL), `error_code`.

---

## 4. Anti-Patterns (Forbidden)

1.  **Vague Messages:**
    * ❌ `logger.info("here")`
    * ❌ `logger.error("error happened")`
    * ✅ `logger.info("processing_payment_step_start")`

2.  **String Interpolation for Secrets (PII):**
    * ❌ `logger.info(f"User password is {password}")`
    * **Rule:** Never log passwords, API keys, tokens, or PII (Personally Identifiable Information) unless strictly masked.

3.  **Swallowing Errors:**
    * Do not catch an exception and log it as INFO without including the error object/stack trace.

4.  **Logging inside tight loops:**
    * Avoid high-frequency logging (e.g., inside a `for` loop processing 1M items) unless sampling is enabled.