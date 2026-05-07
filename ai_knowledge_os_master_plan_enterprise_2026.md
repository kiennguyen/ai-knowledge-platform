# ARCHITECTURE.md

# AI Knowledge OS — Kiến trúc tổng thể

## 1. Định vị sản phẩm

### Vision dài hạn

Xây dựng:

> “AI Knowledge Operating System cho doanh nghiệp”

Một lớp AI nằm trên toàn bộ dữ liệu, tài liệu, giao tiếp và workflow nội bộ.

Không chỉ là:
- AI chat với PDF
- Enterprise search
- RAG chatbot

Mà là:
- Company Brain
- Enterprise Memory Layer
- AI Workflow Runtime
- Knowledge Graph cho doanh nghiệp
- Multi-agent operating layer

---

## 2. Product strategy

### Phase 1 — AI Internal Search

Mục tiêu:
- Universal search
- Chat with company knowledge
- Gmail/Drive integration
- Permission-aware RAG

Định vị:

> “Google nội bộ cho doanh nghiệp + AI”

Target:
- SME Việt Nam
- Công ty 20–300 nhân sự
- Agency
- Logistics
- Education center
- Công ty xuất nhập khẩu

---

### Phase 2 — AI Knowledge Workspace

Bổ sung:
- AI summarize email
- AI summarize meetings
- AI onboarding assistant
- AI SOP generator
- AI policy assistant
- AI report generator

Định vị:

> “AI trợ lý vận hành doanh nghiệp”

---

### Phase 3 — AI Workflow OS

Bổ sung:
- AI agents
- AI task orchestration
- AI auto follow-up
- AI operations workflow
- AI memory graph
- AI business automation

Định vị:

> “AI employee operating system”

---

# 3. Kiến trúc tổng thể

## Core principles

| Principle | Meaning |
|---|---|
| Knowledge Graph First | Dữ liệu doanh nghiệp phải được chuẩn hóa thành graph thay vì chỉ vector search |
| Permission-aware by design | Mọi query/search/chat đều kiểm tra quyền |
| Multi-tenant from day one | Tenant isolation ở mọi layer |
| Event-driven architecture | Async ingestion + workflow + automation |
| AI as infrastructure | AI là lớp nền, không chỉ feature |
| Retrieval before generation | Luôn grounding trước khi generate |
| Hybrid AI architecture | Cloud model + local model + BYO model |
| Incremental ingestion | Không re-index toàn bộ khi file thay đổi |
| Auditability | Track AI actions và reasoning |
| Agent-ready architecture | Kiến trúc chuẩn bị sẵn cho multi-agent |

---

# 4. High-level architecture

```text
Users
  ↓
Web / Desktop / Mobile
  ↓
API Gateway
  ↓
Auth + RBAC + Tenant Guard
  ↓
Knowledge OS Core
    ├── Connector Service
    ├── Ingestion Service
    ├── OCR Service
    ├── Embedding Service
    ├── Knowledge Graph Service
    ├── RAG Service
    ├── AI Orchestrator
    ├── Agent Runtime
    ├── Search Service
    ├── Workflow Engine
    ├── Notification Service
    └── Audit Service
  ↓
Storage Layer
    ├── PostgreSQL
    ├── Qdrant
    ├── Redis
    ├── S3-compatible storage
    └── Event Bus
```

---

# 5. Canonical stack

| Layer | Technology |
|---|---|
| Monorepo | pnpm + Turborepo |
| Frontend | Next.js + React + TypeScript |
| Backend | NestJS |
| Database | PostgreSQL 16 |
| Vector DB | Qdrant |
| Queue | Redis + BullMQ |
| Object Storage | S3 / MinIO |
| Search | PostgreSQL FTS + Qdrant Hybrid |
| AI SDK | Vercel AI SDK |
| Embedding | OpenAI / bge-m3 / jina |
| Local LLM | vLLM |
| Workflow | Temporal hoặc custom workflow engine |
| Observability | OpenTelemetry + Grafana |
| Auth | JWT + RBAC |
| Infra | Kubernetes |

---

# 6. Core services

## 6.1 Connector Service

Kết nối:
- Gmail
- Outlook
- Google Drive
- OneDrive
- Slack
- Zalo OA
- Notion
- Confluence
- Jira
- ERP
- CRM
- Local files

Connector responsibilities:
- OAuth
- Sync scheduling
- Delta sync
- Webhook ingestion
- Retry handling
- Rate limit handling
- File metadata extraction

Moat lớn nhất nằm ở connector layer.

---

## 6.2 Ingestion Pipeline

Pipeline:

```text
Raw file/email/message
  ↓
Virus scan
  ↓
OCR / parsing
  ↓
Chunking
  ↓
Metadata extraction
  ↓
Permission mapping
  ↓
Embedding
  ↓
Knowledge graph update
  ↓
Vector indexing
```

Supported formats:
- PDF
- DOCX
- XLSX
- PPTX
- Email
- HTML
- Images
- Audio transcript
- Video transcript

---

## 6.3 Knowledge Graph Service

Đây là core moat.

Graph entities:
- Users
- Teams
- Projects
- Customers
- Documents
- Emails
- Meetings
- Tasks
- Policies
- SOPs
- Conversations

Graph relationships:
- authored_by
- related_to
- belongs_to
- mentioned_in
- approved_by
- works_with
- customer_of

Graph giúp AI hiểu:
- context
- organization structure
- workflow
- project relationship
- hidden connections

---

## 6.4 Search Service

Hybrid search:
- semantic search
- keyword search
- metadata filtering
- graph-aware retrieval
- recency ranking
- permission filtering

Search modes:
- document search
- people search
- project search
- email search
- meeting search
- company-wide search

---

## 6.5 AI Orchestrator

Responsibilities:
- context building
- model routing
- cost optimization
- retrieval orchestration
- tool calling
- prompt templating
- safety filtering
- agent coordination

Model routing:
- cheap model for summarize
- strong model for reasoning
- local model for privacy-sensitive data

---

## 6.6 Agent Runtime

Agents:
- Email Agent
- HR Agent
- Sales Agent
- Compliance Agent
- Knowledge Agent
- Meeting Agent
- Task Agent

Agent capabilities:
- read company knowledge
- create tasks
- summarize
- send notifications
- update CRM
- generate reports
- follow-up workflow

---

## 6.7 Workflow Engine

Workflow examples:

### Employee onboarding

```text
New employee joined
  ↓
AI creates onboarding checklist
  ↓
AI shares documents
  ↓
AI schedules meetings
  ↓
AI answers onboarding questions
```

### Sales follow-up

```text
Meeting transcript uploaded
  ↓
AI extracts action items
  ↓
AI drafts follow-up email
  ↓
AI creates CRM tasks
```

---

# 7. Security architecture

## Security principles

| Principle | Meaning |
|---|---|
| Zero trust | Không trust internal requests |
| Tenant isolation | Mọi query filter tenant |
| Permission-aware retrieval | AI không được bypass quyền |
| Audit everything | Log mọi AI action |
| Encryption | Encryption at rest + in transit |
| BYO model support | Enterprise có thể dùng local model |

---

## Permission model

RBAC:
- admin
- manager
- employee
- viewer

Document-level ACL:
- owner
- team access
- organization access
- restricted access

AI retrieval:
- chỉ retrieve docs user có quyền đọc
- agent inherit user permissions

---

# 8. Data architecture

## PostgreSQL

Store:
- metadata
- users
- permissions
- workflows
- audit logs
- connectors
- organizations

---

## Qdrant

Store:
- embeddings
- semantic search index
- chunk vectors
- conversation memory

Payload:
- tenant_id
- document_id
- permission tags
- embedding_version
- source type

---

## Redis

Store:
- queues
- cache
- realtime events
- workflow state
- session cache

---

# 9. AI memory architecture

## Multi-layer memory

| Layer | Purpose |
|---|---|
| Session memory | Current conversation |
| Workspace memory | Company-wide knowledge |
| User memory | User preferences/context |
| Long-term memory | Historical insights |
| Graph memory | Relationship understanding |

---

# 10. Deployment architecture

## Phase 1

- Single-region
- Kubernetes
- Shared cluster
- Multi-tenant SaaS

---

## Phase 2

- Regional deployment
- Dedicated vector cluster
- GPU inference cluster
- Enterprise isolation

---

## Phase 3

- On-prem deployment
- Hybrid cloud
- Enterprise VPC deployment
- BYO model runtime

---

# 11. Scalability strategy

## Scale bottlenecks

### First bottleneck
Embedding ingestion

Solution:
- queue-based ingestion
- batch embedding
- async indexing

### Second bottleneck
RAG latency

Solution:
- hybrid retrieval
- reranking cache
- query optimization

### Third bottleneck
LLM cost

Solution:
- model router
- prompt caching
- retrieval compression
- local models

---

# 12. Long-term moat

## Real moat is NOT model

Moat =
- enterprise connectors
- knowledge graph
- permissions
- workflow automation
- company memory
- agent infrastructure
- enterprise trust
- integrations

---

# MASTER_SPEC.md

# AI Knowledge OS — Master Specification

## 0. Product Definition

AI Knowledge OS là nền tảng AI cho doanh nghiệp giúp:
- quản lý tri thức
- tìm kiếm nội bộ
- AI chat với dữ liệu doanh nghiệp
- AI workflow automation
- AI agents
- enterprise memory

Không chỉ là chatbot.

Mục tiêu:

> “Central AI brain for organizations.”

---

# 1. Product Phases

# Phase 1 — AI Internal Search

## Goal

Build MVP có thể:
- upload docs
- connect Gmail/Drive
- AI chat với company knowledge
- semantic search
- permission-aware retrieval

---

## Features

### Core

- upload files
- AI chat
- semantic search
- folder organization
- document tagging
- OCR
- Vietnamese support
- workspace management

---

### Connectors

- Gmail
- Google Drive
- OneDrive

---

### AI

- summarize docs
- answer questions
- cite sources
- semantic retrieval

---

## APIs

### Upload

```http
POST /api/files/upload
```

### Chat

```http
POST /api/chat
```

### Search

```http
GET /api/search
```

### Connectors

```http
POST /api/connectors/google-drive/connect
POST /api/connectors/gmail/connect
```

---

## Success metrics

- Time-to-answer < 10s
- Upload success > 99%
- Search relevance acceptable
- First paying customer

---

## Business strategy

### Target

- SME Việt Nam
- 20–300 employees
- knowledge-heavy companies

---

### Pricing

| Plan | Price |
|---|---|
| Free | giới hạn storage |
| Pro | per-user pricing |
| Business | custom |

---

### GTM

- direct sales
- founder-led sales
- LinkedIn content
- demo-driven acquisition
- AI workshops cho doanh nghiệp

---

# Phase 2 — AI Knowledge Workspace

## Goal

Từ search tool → AI workspace.

---

## Features

### AI summarize

- email summarize
- meeting summarize
- project summarize
- policy summarize

---

### AI onboarding

- onboarding assistant
- company handbook assistant
- SOP generation

---

### Workspace collaboration

- comments
- shared chats
- shared collections
- team spaces

---

### Knowledge graph

- relationship mapping
- project graph
- customer graph
- people graph

---

## APIs

```http
POST /api/meetings/transcript
POST /api/sop/generate
POST /api/reports/generate
```

---

## Business strategy

### Expansion strategy

Verticalize:
- logistics
- education
- government
- real estate
- manufacturing

---

### Sales strategy

- B2B outbound
- enterprise pilots
- reseller partnerships
- consulting partners

---

### Monetization

- seat-based pricing
- AI credits
- storage pricing
- workflow pricing

---

# Phase 3 — AI Workflow OS

## Goal

AI tự động hóa workflow doanh nghiệp.

---

## Features

### AI Agents

- autonomous agents
- multi-agent coordination
- workflow execution
- approval chains

---

### AI Operations

- auto follow-up
- task creation
- CRM updates
- compliance checks
- risk detection

---

### Enterprise Memory

- persistent organizational memory
- company-wide insights
- cross-department intelligence

---

## APIs

```http
POST /api/workflows/run
POST /api/agents/execute
POST /api/agents/schedule
```

---

## Business strategy

### Enterprise strategy

- enterprise contracts
- annual licensing
- dedicated deployment
- private cloud
- on-prem

---

### Ecosystem strategy

Build:
- plugin marketplace
- workflow marketplace
- AI agent marketplace
- third-party integrations

---

# 2. Multi-tenant architecture

## Workspace model

Every resource:
- tenant-scoped
- permission-aware
- audit-logged

---

## Tables

- organizations
- workspaces
- users
- workspace_members
- documents
- embeddings
- chats
- workflows
- agents
- audit_logs

---

# 3. AI architecture

## Retrieval flow

```text
User question
  ↓
Permission filter
  ↓
Hybrid search
  ↓
Reranking
  ↓
Context compression
  ↓
LLM answer
  ↓
Source citation
```

---

## Model strategy

| Use case | Model type |
|---|---|
| Fast summarize | cheap model |
| Complex reasoning | strong model |
| Sensitive data | local model |

---

# 4. Enterprise features roadmap

## Future features

- voice assistant
- realtime meeting AI
- AI phone call summarize
- multilingual knowledge search
- enterprise analytics
- AI compliance monitoring
- AI policy enforcement

---

# 5. Infrastructure roadmap

## MVP

- single cluster
- shared infra
- cloud LLM

---

## Growth

- dedicated vector cluster
- GPU inference
- observability platform

---

## Enterprise

- dedicated deployment
- on-prem
- air-gapped deployment
- BYO LLM

---

# TASK_LIST.md

# AI Knowledge OS — TASK LIST

# PHASE 0 — Repo & Foundations

- [ ] P0.1 Setup monorepo (pnpm + Turborepo)
- [ ] P0.2 apps/web
- [ ] P0.3 apps/api
- [ ] P0.4 apps/worker
- [ ] P0.5 packages/db
- [ ] P0.6 packages/shared-types
- [ ] P0.7 packages/ai-core
- [ ] P0.8 Docker Compose (PostgreSQL, Redis, MinIO, Qdrant)
- [ ] P0.9 CI/CD pipeline
- [ ] P0.10 OpenTelemetry setup

Definition of Done:
- local development boot được bằng 1 command
- CI chạy thành công

---

# PHASE 1 — Authentication & Multi-tenancy

- [ ] A1.1 organizations table
- [ ] A1.2 workspaces table
- [ ] A1.3 RBAC
- [ ] A1.4 JWT auth
- [ ] A1.5 tenant guard
- [ ] A1.6 workspace member invitations
- [ ] A1.7 audit logging
- [ ] A1.8 tenant isolation tests

Definition of Done:
- tenant A không thể truy cập tenant B

---

# PHASE 2 — Document Management

- [ ] D2.1 file upload service
- [ ] D2.2 S3 integration
- [ ] D2.3 OCR pipeline
- [ ] D2.4 document metadata extraction
- [ ] D2.5 document tagging
- [ ] D2.6 document versioning
- [ ] D2.7 folder structure
- [ ] D2.8 permission mapping
- [ ] D2.9 virus scan pipeline

Definition of Done:
- upload PDF/DOCX thành công
- metadata extract được
- OCR hoạt động

---

# PHASE 3 — Connector System

- [ ] C3.1 Gmail OAuth
- [ ] C3.2 Google Drive OAuth
- [ ] C3.3 incremental sync
- [ ] C3.4 webhook sync
- [ ] C3.5 retry system
- [ ] C3.6 connector scheduler
- [ ] C3.7 connector monitoring
- [ ] C3.8 connector permission sync

Definition of Done:
- sync Gmail/Drive thành công
- delta sync hoạt động

---

# PHASE 4 — Embedding & Search

- [ ] E4.1 chunking service
- [ ] E4.2 embedding pipeline
- [ ] E4.3 Qdrant indexing
- [ ] E4.4 hybrid search
- [ ] E4.5 reranking
- [ ] E4.6 metadata filtering
- [ ] E4.7 permission-aware retrieval
- [ ] E4.8 citation support
- [ ] E4.9 Vietnamese retrieval optimization

Definition of Done:
- semantic search usable
- AI answers có citation

---

# PHASE 5 — AI Chat

- [ ] AI5.1 chat service
- [ ] AI5.2 conversation memory
- [ ] AI5.3 streaming response
- [ ] AI5.4 context builder
- [ ] AI5.5 prompt templates
- [ ] AI5.6 source citations
- [ ] AI5.7 model router
- [ ] AI5.8 token usage tracking
- [ ] AI5.9 AI guardrails

Definition of Done:
- chat với company knowledge hoạt động ổn định

---

# PHASE 6 — Knowledge Graph

- [ ] KG6.1 graph schema
- [ ] KG6.2 entity extraction
- [ ] KG6.3 relationship extraction
- [ ] KG6.4 people graph
- [ ] KG6.5 project graph
- [ ] KG6.6 customer graph
- [ ] KG6.7 graph retrieval
- [ ] KG6.8 graph visualization

Definition of Done:
- AI hiểu relationship cơ bản giữa documents/projects/users

---

# PHASE 7 — AI Workspace Features

- [ ] W7.1 email summarize
- [ ] W7.2 meeting summarize
- [ ] W7.3 SOP generation
- [ ] W7.4 onboarding assistant
- [ ] W7.5 report generation
- [ ] W7.6 shared chats
- [ ] W7.7 collections
- [ ] W7.8 comments

Definition of Done:
- AI workspace usable cho team nhỏ

---

# PHASE 8 — Workflow Engine

- [ ] WF8.1 workflow runtime
- [ ] WF8.2 workflow triggers
- [ ] WF8.3 workflow scheduler
- [ ] WF8.4 workflow approvals
- [ ] WF8.5 workflow history
- [ ] WF8.6 retry/recovery
- [ ] WF8.7 event bus

Definition of Done:
- AI workflow chạy được production-safe

---

# PHASE 9 — AI Agents

- [ ] AG9.1 agent runtime
- [ ] AG9.2 tool calling
- [ ] AG9.3 agent memory
- [ ] AG9.4 multi-agent coordination
- [ ] AG9.5 agent permissions
- [ ] AG9.6 agent audit logs
- [ ] AG9.7 autonomous workflows

Definition of Done:
- AI agents chạy workflow thực tế

---

# PHASE 10 — Enterprise Readiness

- [ ] ER10.1 SSO/SAML
- [ ] ER10.2 audit export
- [ ] ER10.3 data retention policies
- [ ] ER10.4 on-prem deployment
- [ ] ER10.5 BYO LLM
- [ ] ER10.6 private VPC deployment
- [ ] ER10.7 compliance tooling
- [ ] ER10.8 enterprise billing

Definition of Done:
- deploy được cho enterprise lớn

---

# PHASE 11 — Observability & Reliability

- [ ] O11.1 metrics dashboard
- [ ] O11.2 tracing
- [ ] O11.3 queue monitoring
- [ ] O11.4 AI latency monitoring
- [ ] O11.5 cost monitoring
- [ ] O11.6 alerting
- [ ] O11.7 disaster recovery
- [ ] O11.8 backup automation

Definition of Done:
- production observability đầy đủ

---

# PHASE 12 — Business Expansion

- [ ] B12.1 billing system
- [ ] B12.2 AI credits
- [ ] B12.3 reseller portal
- [ ] B12.4 partner APIs
- [ ] B12.5 plugin SDK
- [ ] B12.6 workflow marketplace
- [ ] B12.7 AI agent marketplace

Definition of Done:
- platform monetization scale được

---

# Recommended GTM Timeline

| Timeline | Goal |
|---|---|
| Month 1–2 | Internal search MVP |
| Month 3–4 | Gmail/Drive integrations |
| Month 5–6 | First paying customers |
| Month 7–9 | AI workspace features |
| Month 10–12 | Workflow engine |
| Year 2 | Enterprise expansion |
| Year 3 | AI operating system |

---

# Strategic Advice

## Không nên làm

- generic AI chatbot
- upload PDF demo app
- pure wrapper OpenAI
- no-permission RAG
- single-tenant architecture

---

## Nên tập trung

- connectors
- permissions
- knowledge graph
- workflow automation
- enterprise trust
- Vietnamese enterprise workflows
- AI memory infrastructure

---

# Biggest moat

Moat dài hạn nằm ở:
- enterprise integrations
- knowledge graph
- organizational memory
- AI workflow runtime
- enterprise deployment
- operational reliability
- workflow ecosystem

