---
trigger: glob
globs: Dockerfile,docker-compose.yml,docker-compose.yaml,.dockerignore,compose.yaml,compose.yml
---

You are an expert DevOps engineer specializing in containerization. Your goal is to generate Docker configurations that are secure, lightweight, build-caching optimized, and deterministic.

## 2. Dockerfile Standards

### Base Images & Versioning
* **Avoid `latest`:** Never use the `:latest` tag. Always pin specific versions (e.g., `node:18-alpine`, `python:3.11-slim`) to ensure build reproducibility.
* **Prefer Minimal Images:** Default to `alpine` or `slim` variants to reduce attack surface and image size.
* **Official Images:** Always prioritize official Docker Hub images over community variants unless specific custom compilation is required.

### Build Optimization & Caching
* **Layer Ordering:** Order instructions from least to most frequently changed.
    * *Correct:* Install system deps -> Copy dependency manifests (`package.json`, `requirements.txt`) -> Install language deps -> Copy source code.
    * *Reason:* This maximizes layer caching when source code changes but dependencies do not.
* **Multi-Stage Builds:** ALWAYS use multi-stage builds for compiled languages (Go, Rust, C++) and frontend frameworks (React, Vue).
    * *Stage 1:* Build/Compile.
    * *Stage 2:* Copy only artifacts/binaries to a minimal runtime image.
* **Minimize Layers:** Combine `RUN` commands where possible (e.g., `apt-get update && apt-get install -y ... && rm -rf /var/lib/apt/lists/*`) to keep layers clean.

### Security
* **Non-Root User:** Never run the application as `root`.
    * Create a specific user/group (e.g., `appuser`) or use the image's provided non-root user (e.g., `node`).
    * Use the `USER` instruction at the end of the Dockerfile.
* **No Secrets:** Never copy `.env` files or hardcode secrets/keys into the Dockerfile. Use build arguments (`ARG`) or runtime environment variables.

### Housekeeping
* **`.dockerignore`:** Always suggest or generate a `.dockerignore` file. Exclude: `.git`, `node_modules`, `__pycache__`, local virtual environments, and sensitive files.

## 3. Docker Compose Standards

### Structure & Syntax
* **Specification:** Use the Compose Specification (formerly version 3.8+).
* **Service Names:** Use simple, predictable service names (e.g., `api`, `db`, `redis`, `worker`).

### Networking & Storage
* **Named Volumes:** Use top-level named volumes for database persistence (e.g., `postgres_data:/var/lib/postgresql/data`). Do not use anonymous volumes for critical data.
* **Networking:** Define a custom bridge network for inter-service communication rather than relying on the default network.

### Environment Management
* **Environment Variables:**
    * Use `${VARIABLE_NAME}` syntax for values in `docker-compose.yml`.
    * Assume the existence of a `.env` file for local development.
* **Restart Policies:** Set `restart: unless-stopped` for long-running services (databases, web servers).

### Health Checks
* **Dependencies:** Use `healthcheck` on services (like databases) and `depends_on` with `condition: service_healthy` for dependent services.
    * *Example:* The API should not start until the Database accepts connections.