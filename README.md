# KT
Architecture Overview
Backend (NestJS)

Modular structure: Auth, Users, Tasks, Organizations

TypeORM as ORM layer

SQLite for development; PostgreSQL supported by config

Guards & decorators abstract access-control logic

Services contain business logic

Repositories used for database operations

Frontend (Angular)

Standalone components

Task management views

Auth service stores JWT in memory/local storage

HTTP interceptor automatically attaches JWT

Shared “data” library keeps common types consistent

Shared Libraries

libs/data

DTOs

Interfaces

Shared enums (roles, permissions, categories)

libs/auth

@Roles() decorator

RolesGuard

JwtAuthGuard

Permission utilities

Organization scope helpers

🗃 Data Model Overview
Entities:

User

id, email, passwordHash, role, organizationId

Organization

id, name, parentId (supports 2-level hierarchy)

Task

id, title, description, category, organizationId, createdBy

Audit Log

timestamp, userId, action, resource

ERD (conceptual)
Organization (1) ─── (M) Users
Organization (1) ─── (M) Tasks
Users (1) ────────── (M) Tasks (createdBy)

Access Control Implementation
Role Rules
Role	Can View	Can Create	Can Edit	Can Delete	Audit Log
Owner	all org tasks	yes	yes	yes	yes
Admin	own org tasks	yes	yes	limited	yes
Viewer	own org tasks	no	no	no	no
How access is enforced

JWT Guard – ensures the request is authenticated

RolesGuard – checks whether the role matches required permissions

Organization Scope Check – ensures user belongs to same org

Task Ownership Check (for edits/deletes)

RBAC logic is centralized in libs/auth.

 API Documentation
Auth

POST /auth/login
Request:

{
  "email": "user@example.com",
  "password": "Password123!"
}


Response:

{
  "access_token": "<jwt>"
}

Tasks

GET /tasks – List tasks
POST /tasks – Create task
PUT /tasks/:id – Update task
DELETE /tasks/:id – Delete task
GET /audit-log – Admin/Owner only

 Future Improvements

Refresh tokens & rotation

Organization admin delegation

Advanced hierarchical RBAC

Task analytics dashboard

Real-time updates via WebSocket
