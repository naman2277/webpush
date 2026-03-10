# Advanced Task Tracker Platform

A product blueprint for building an enterprise-grade task tracker where **Admins** and **Project Managers (PMs)** can assign work, and **Users** can provide updates, collaborate, and surface delivery risk with intelligent analytics.

## 1) Product Goals

- Centralize planning, assignment, and delivery tracking.
- Give leaders real-time visibility into capacity, workload, and bottlenecks.
- Detect both:
  - genuinely over-occupied users, and
  - users who appear overloaded but are underutilized.
- Deliver a modern, fast UI with collaboration-first workflows.

## 2) Core Personas & Permissions

### Admin
- Manage organization, users, teams, permissions, and global workflows.
- Create custom task types and lifecycle states.
- Access all analytics and audit logs.

### PM (Project Manager)
- Create projects/epics/sprints.
- Assign and rebalance tasks.
- Track project health, SLA risk, and team velocity.
- Review workload recommendations.

### User (Contributor)
- View assigned tasks and priorities.
- Post comments, updates, blockers, and evidence attachments.
- Log time and status confidence.

## 3) Must-Have Features

### Work Management
- Projects, epics, stories, tasks, and subtasks.
- Task templates (per task type).
- Priority matrix (business impact × urgency).
- Dependencies (FS/SS/FF + blockers).
- SLA / due-date policies.

### Assignment & Capacity
- Skill-based assignment suggestions.
- Capacity model (availability, leaves, meeting load, timezone).
- Auto-rebalancing recommendations.
- "What-if" planning before assignment.

### Collaboration
- Threaded comments and mentions (`@user`, `@team`).
- Rich-text updates, attachments, and checklists.
- Daily/weekly progress digest.
- Escalation flows for blocked tasks.

### Automation
- Rule engine (`if condition -> action`).
- Trigger examples:
  - when blocked > 24h, alert PM,
  - when due in 12h and not started, escalate,
  - when dependency delayed, reforecast ETA.

### Modern UI/UX
- Multi-view board: Kanban, List, Calendar, Gantt, Timeline.
- Quick actions from keyboard palette.
- Command bar + saved filters.
- Dark mode and responsive design.

## 4) Advanced Analytics (Key Differentiator)

### A) Task-Type Analytics
- Throughput by task type (weekly/monthly).
- Average cycle time and lead time per type.
- Defect/reopen rate by type.
- Prediction confidence for completion.

### B) User-Level Analytics
- Capacity vs committed workload.
- Actual execution time vs estimated time.
- Focus score (context switching index).
- Blocker exposure and dependency waiting time.

### C) "True Overload vs Apparent Overload" Engine

A user may *look* overloaded because they have many assigned tasks, but may not be effectively occupied. Build a scoring model:

- **Committed Load Score**: sum(weighted estimates / available hours).
- **Execution Load Score**: actual logged productive hours / available hours.
- **Task Fragmentation Score**: high number of tiny tasks can inflate perceived load.
- **Blocked Ratio**: % time tasks are blocked (assigned but not active work).
- **Meetings + Interruptions Penalty**: calendar integration signal.

Classification:
- `true_overloaded`: high committed load + high execution load.
- `apparent_overloaded`: high committed load + low execution load + high blocked/fragmented time.
- `underutilized`: low committed load + low execution load.

### D) Predictive Insights
- Risk of deadline miss (task and project level).
- Suggested reassignment candidates by skill and historical performance.
- Burnout early warning (sustained overload trend).
- Confidence-adjusted ETA based on similar historical tasks.

## 5) Suggested Technical Architecture

- **Frontend**: Next.js + TypeScript + Tailwind + shadcn/ui + ECharts.
- **Backend**: NestJS (or FastAPI) with modular services.
- **DB**: PostgreSQL.
- **Cache/Queue**: Redis + BullMQ.
- **Realtime**: WebSockets for comments, updates, notifications.
- **Search**: OpenSearch/Elastic for task/comment indexing.
- **Auth**: RBAC + SSO (SAML/OAuth).
- **Observability**: OpenTelemetry + Prometheus + Grafana.

## 6) Data Model (High Level)

- `users`, `teams`, `roles`, `permissions`
- `projects`, `epics`, `sprints`
- `tasks`, `task_types`, `task_dependencies`
- `task_comments`, `task_updates`, `attachments`
- `time_logs`, `capacity_snapshots`, `calendar_events`
- `analytics_daily_user`, `analytics_daily_task_type`
- `notifications`, `audit_logs`, `automation_rules`

## 7) APIs (Sample)

- `POST /tasks`, `PATCH /tasks/:id`, `POST /tasks/:id/comments`
- `POST /tasks/:id/updates`
- `GET /analytics/users/:id/workload`
- `GET /analytics/task-types/:typeId`
- `GET /analytics/overload-classification`
- `POST /automation/rules`

## 8) Delivery Plan (Phased)

### Phase 1 (MVP)
- Auth + RBAC
- Task CRUD + assignment + comments
- Kanban/List views
- Basic workload dashboard

### Phase 2
- Capacity planning + rebalancing suggestions
- Dependency tracking + alerts
- Time logging + SLA alerts

### Phase 3
- Predictive analytics + overload classification model
- Burnout & risk prediction
- Full automation engine and enterprise integrations

## 9) Non-Functional Requirements

- Sub-300ms p95 for common reads.
- Eventual consistency acceptable for analytics (< 5 min lag).
- Auditability for all task and permission changes.
- Tenant data isolation for enterprise accounts.

## 10) Suggested Next Step

Start with **Phase 1 MVP** and ship in 4-6 weeks, while collecting data needed for overload intelligence in Phase 3.

