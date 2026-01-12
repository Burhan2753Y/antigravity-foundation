---
trigger: model_decision
description: "Activate when designing, modifying, or reviewing REST APIs. Enforces strict standards for URI naming, HTTP methods, versioning, status codes, and JSON response structures to ensure consistency."
---

# API Design Guidelines

## 1. General Principles
* **RESTful Architecture:** We adhere to REST principles. APIs are resource-oriented, stateless, and use standard HTTP methods.
* **JSON First:** All API requests and responses must use `application/json` as the `Content-Type`.
* **Statelessness:** No client context is stored on the server between requests. State is handled via tokens (e.g., JWT).
* **Secure by Design:** All endpoints utilize HTTPS. Input is always validated; output is always sanitized.

---

## 2. Resource Naming & URI Structure

### Naming Conventions
* **Nouns, not Verbs:** Use concrete nouns to represent resources.
    * ✅ `/users`, `/orders`
    * ❌ `/getUsers`, `/createOrder`
* **Pluralization:** Always use plural nouns for collections.
    * ✅ `/api/v1/projects`
    * ❌ `/api/v1/project`
* **Casing:**
    * **URIs:** Use **kebab-case** (lowercase with hyphens).
        * ✅ `/api/v1/user-profiles`
        * ❌ `/api/v1/userProfiles`, `/api/v1/UserProfiles`
    * **JSON Keys:** Use **camelCase**.
        * ✅ `{ "firstName": "Alice" }`
        * ❌ `{ "first_name": "Alice" }`

### Nesting Resources
Limit nesting to **2 levels** to maintain readability. If deeper nesting is required, reconsider the data model or use query parameters.
* ✅ `/api/v1/users/{userId}/posts` (Posts belonging to a specific user)
* ✅ `/api/v1/users/{userId}/posts/{postId}/comments` (Borderline, but acceptable)
* ❌ `/api/v1/users/{userId}/posts/{postId}/comments/{commentId}/likes` (Too deep; flatten this)

---

## 3. Versioning
* **URI Versioning:** Versioning is mandatory and must be explicit in the URL path.
* **Format:** `v{major_version}`.
    * Example: `/api/v1/resources`
* **Breaking Changes:** Increment the major version (e.g., move to `v2`) only when introducing breaking changes. Non-breaking additions do not require a version bump.

---

## 4. HTTP Methods
Use the correct HTTP verb to describe the action being performed.

| Method | Action | Description | Idempotent |
| :--- | :--- | :--- | :--- |
| **GET** | Read | Retrieve a resource or a collection. | ✅ Yes |
| **POST** | Create | Create a new resource. Returns the created object. | ❌ No |
| **PUT** | Replace | Update a resource strictly by **replacing** it entirely. | ✅ Yes |
| **PATCH** | Update | **Partially** update a resource (only fields sent are updated). | ❌ No* |
| **DELETE**| Delete | Remove a resource. | ✅ Yes |

*\*PATCH is generally not idempotent, though it can be designed to be.*

---

## 5. Status Codes
Return the standard HTTP status codes to indicate the success or failure of a request.

### Success
* `200 OK`: Standard response for successful GET, PUT, or PATCH requests.
* `201 Created`: The resource was successfully created (POST). *Must* return the 'Location' header with the link to the new resource.
* `204 No Content`: The request was successful, but there is no body to return (common for DELETE).

### Client Errors
* `400 Bad Request`: General validation failure or malformed JSON.
* `401 Unauthorized`: Authentication is missing or invalid (e.g., expired token).
* `403 Forbidden`: Authenticated, but lacks permission for this specific resource/action.
* `404 Not Found`: The requested resource does not exist.
* `409 Conflict`: The request conflicts with current state (e.g., duplicate email registration).
* `429 Too Many Requests`: Rate limit exceeded.

### Server Errors
* `500 Internal Server Error`: Something went wrong on the server side. (Do not expose stack traces to the client).
* `503 Service Unavailable`: The server is down for maintenance or overloaded.

---

## 6. Request & Response Standards

### Date and Time
* Always use **ISO 8601** format in **UTC**.
    * ✅ `"2024-03-14T15:30:00Z"`
    * ❌ `"1698765432"`, `"2024/03/14"`

### Response Envelopes
* **Single Resource:** Return the raw object.
    ```json
    {
      "id": "123",
      "name": "Widget A"
    }
    ```
* **Collections:** Wrap in a `data` object to support pagination metadata.
    ```json
    {
      "data": [
        { "id": "123", "name": "Widget A" },
        { "id": "124", "name": "Widget B" }
      ],
      "meta": {
        "page": 1,
        "limit": 20,
        "total": 500
      }
    }
    ```

### Error Responses
Errors must follow a standard structure to allow consistent parsing by clients.
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid email format.",
    "details": [
      {
        "field": "email",
        "issue": "Must be a valid email address."
      }
    ],
    "traceId": "abc-123-xyz" // For debugging logs
  }
}

## 7. Filtering, Sorting, & Pagination

Filtering

Use query parameters for filtering.

GET /users?role=admin&active=true

Sorting

Use a sort parameter. Use a hyphen - for descending order.

GET /users?sort=-created_at (Newest first)

GET /users?sort=name (A-Z)

Pagination

Use page and limit (or cursor for infinite scroll).

GET /users?page=2&limit=50

Defaults: APIs should default to page=1 and a reasonable limit (e.g., 20) if not specified.

## 8. Security & Headers

Authorization: Pass tokens via the Authorization header using the Bearer schema.

Authorization: Bearer <token>

CORS: Configure CORS headers carefully. Allow only trusted origins.

Content-Type: application/json is required for requests containing a body.