# Cloud / System Architecture Overview

This document provides a high-level system context for the TODO App monorepo. The environment is a development container hosting both the React frontend and Express backend. Persistence is currently an in-memory SQLite database (ephemeral) within the backend process.

## Context Summary
- User interacts via a browser with the React SPA (`packages/frontend`).
- Frontend communicates over HTTP with the Express API (`/api/tasks`) served by `packages/backend`.
- The API uses an in-memory SQLite database for task storage (no external persistence layer yet).
- All components run inside a single dev container / monorepo workspace (no separate cloud services provisioned).

## System Context Diagram

```mermaid
architecture-beta
    group dev(cloud)[DevContainer]
    service browser(internet)[Browser]
    service frontend(server)[ReactSPA] in dev
    service api(server)[ExpressAPI] in dev
    service db(database)[SQLiteDB] in dev

    browser:R --> L:frontend
    frontend:R --> L:api
    api:R --> L:db
```

## Notes
- Arrows show request flow direction (left to right).
- In-Memory SQLite is transient; restarting the backend clears data.
- Future evolution may introduce: external persistence (PostgreSQL), auth service, and CDN for static assets.

## Potential Extensions
- Add group for "External Services" when introducing third-party APIs or persistent storage.
- Introduce junctions if fan-out (e.g., API calling multiple microservices) is added.

## Sequence: User Creates a TODO

The following sequence diagram shows the interaction flow when a user creates a new task.

```mermaid
sequenceDiagram
    actor User
    participant Browser as React SPA
    participant API as Express API
    participant DB as SQLite (In-Memory)

    User->>Browser: Type title, description, (optional) due date
    User->>Browser: Click "Add Task"
    Browser->>API: POST /api/tasks { title, description, due_date }
    API->>DB: Insert task row
    DB-->>API: New row (id, created_at)
    API-->>Browser: 201 Created + JSON task
    Browser->>API: GET /api/tasks (refresh list)
    API->>DB: Query ordered tasks
    DB-->>API: Task list
    API-->>Browser: 200 OK task list
    Browser-->>User: Render updated list with new task

    alt Missing title
        API-->>Browser: 400 Bad Request { error: "Task title is required" }
        Browser-->>User: Show validation message
    end
```

---
_Last updated: 2025-11-04_
