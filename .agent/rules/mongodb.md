---
trigger: model_decision
description: Activate when writing MongoDB aggregation pipelines, defining Mongoose/ODM schemas, or performing NoSQL data modeling. Focus on document embedding strategies and index usage.
---

## MongoDB & NoSQL Guidelines

- **Schema Design:**
  - **Embedding vs. Referencing:** Prefer embedding data (denormalization) for read-heavy operations where data is accessed together. Use references only to avoid unbounded arrays or for rarely accessed detailed data.
  - Use PascalCase for model names and camelCase for field names.

- **Querying & Aggregation:**
  - Use the **Aggregation Pipeline** for complex transformations rather than processing data in the application code.
  - Always place `$match` and `$project` stages as early as possible in the pipeline to reduce the dataset size immediately.
  - Use explicit projections (`{ name: 1, _id: 0 }`) to fetch only required fields.

- **Performance & Indexing:**
  - Create **Compound Indexes** based on your most frequent queries (Equality, Sort, Range - ESR Rule).
  - Avoid case-insensitive regex queries without specific text indexes.
  - Use `.explain("executionStats")` to verify queries are hitting indexes (Stage: `IXSCAN`) rather than scanning documents (Stage: `COLLSCAN`).

- **Safety:**
  - Sanitize inputs to prevent NoSQL injection, even when using ODMs like Mongoose.
  - Handle `ObjectId` serialization properly (convert to string) before sending data to the client.