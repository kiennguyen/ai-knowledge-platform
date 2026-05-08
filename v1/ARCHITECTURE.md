# ARCHITECTURE — AI Knowledge OS (v1)

**Status:** canonical technical architecture for greenfield / major refactor.  
**Principles:** permission-aware by default, multi-tenant isolation, event-driven ingestion, **retrieval before generation**, observable & cost-aware AI.

---

## 1. High-level diagram

```text
Clients (Web / Mobile / API clients)
        │
        ▼
┌───────────────────┐
│  Edge / CDN       │  TLS, WAF (managed), rate limit
└─────────┬─────────┘
          ▼
┌───────────────────┐
│  API Gateway      │  Routing, API keys (B2B), request ID
└─────────┬─────────┘
          ▼
┌───────────────────┐
│  Auth & Identity  │  OIDC / JWT; SAML bridge (enterprise); tenant context
└─────────┬─────────┘
          ▼
┌───────────────────┐
│  Application API  │  NestJS (modular monolith hoặc services tách sau)
│  (Knowledge OS)   │
└─────────┬─────────┘
          │
    ┌─────┴─────┬─────────────┬──────────────┐
    ▼           ▼             ▼              ▼
 Connector   Ingestion    Search/RAG    AI Orchestrator
 Service     Workers      Service       (router, tools)
    │           │             │              │
    └─────┬─────┴──────┬──────┴──────┬───────┘
          ▼            ▼             ▼
┌─────────────────────────────────────────────┐
│ Storage & messaging                          │
│  PostgreSQL 16+  │  Qdrant  │  Redis+BullMQ │
│  S3-compatible   │  (opt) pgvector pilot   │
└─────────────────────────────────────────────┘
          ▼
 Observability: OpenTelemetry → collector → Grafana / Tempo / Loki (hoặc tương đương)
```

---

## 2. Stack binding (2026 — cân bằng “mới” và chi phí)

| Layer | Choice | Ghi chú |
|-------|--------|---------|
| Monorepo | **pnpm** + **Turborepo** | Cache CI, boundary rõ `apps/*`, `packages/*` |
| Web | **Next.js** (App Router) + React + TypeScript | SSR cho portal; CSP strict |
| API | **NestJS** + **Zod**/class-validator | Module hóa; guard tenant/RBAC |
| DB | **PostgreSQL 16+** | Metadata, ACL, audit, workflow; FTS hybrid |
| Vector | **Qdrant** (default) | Payload filter theo `tenant_id`, `doc_acl`, `embedding_version` |
| Vector (cost-down path) | **pgvector** optional | Pilot cho tenant nhỏ / dev |
| Queue | **Redis** + **BullMQ** | Ingestion, embedding batch, notification |
| Long workflow (future) | **Temporal** (self-host hoặc cloud) | Sagas, human-in-the-loop; không bắt buộc P1 |
| Object storage | **S3-compatible** (MinIO dev) | Version blob, virus scan staging |
| AI SDK | **Vercel AI SDK** hoặc provider-agnostic wrapper | Streaming, tool loop |
| Models | Cloud APIs + **local/vLLM** optional | Router theo độ nhạy cảm & budget |
| Embeddings | `text-embedding-3-small` class / **bge-m3** self-host | Chi phí ↔ chất lượng tiếng Việt |
| Containers | **Kubernetes** (prod) | HPA; tách worker pool |
| Secrets | Cloud KMS / **Vault** pattern | Không secret trong image |
| Observability | **OpenTelemetry** | Trace cross API → worker → Qdrant |

---

## 3. Core bounded contexts (logical)

1. **Identity & Tenant** — org, workspace, membership, roles, invitations, SSO metadata.
2. **Content** — documents, versions, folders, classification labels.
3. **Connector** — OAuth tokens (encrypted), sync state, webhooks, rate limits.
4. **Ingestion** — parse/OCR/chunk, virus scan, permission snapshot per chunk.
5. **Index** — embeddings, Qdrant collections per env; re-embed theo `embedding_version`.
6. **Search & RAG** — hybrid retrieve → rerank (optional) → compress context → LLM.
7. **AI Orchestration** — prompts, model router, safety, citations, cost accounting.
8. **Workflow / Agent** (P3) — definitions, runs, approvals.
9. **Audit & Compliance** — append-prefer audit, export APIs.

---

## 4. Permission model (kiến trúc)

- **RBAC** tại workspace: ví dụ `owner`, `admin`, `editor`, `viewer` (tune trong schema).
- **Resource ACL:** document/folder scope — team, user, link share (future), **classification**.
- **Enforcement:**  
  - API layer: guard trên mọi query có `workspace_id`.  
  - Retrieval layer: **filter bắt buộc** trong Qdrant payload + kiểm tra lại ở DB trước khi trả snippet.  
  - Agent tools: chỉ expose “search internal” đã gắn user context.

**Never:** truyền toàn bộ vector collection vào LLM rồi “hy vọng” model tự lọc.

---

## 5. Ingestion pipeline (logical)

```text
Raw object → quarantine scan → extract text (incl. OCR) → normalize
→ chunk (overlap, structure-aware where possible) → extract metadata
→ attach permission snapshot (tenant, acl_hash, classification)
→ embed → upsert Qdrant (+ update graph job async)
```

- **Incremental:** content hash / rev id; không reprocess toàn bộ khi không đổi.
- **Dead letter queue** cho lỗi không retry được; admin UI xem lỗi (phase sau).

---

## 6. Knowledge graph (phase-in)

- P1: quan hệ nhẹ trong PostgreSQL (bảng edges) hoặc property graph service sau.
- P2+: entity linking từ tài liệu + HR/project master (optional connectors).

Graph **không thay thế** vector search — **bổ sung** cho retrieval (expand neighbors có kiểm soát).

---

## 7. Security architecture

- **Zero trust:** service-to-service mTLS (mesh) khi đủ scale; ít nhất signed internal tokens.
- **Encryption:** TLS everywhere; DB/storage encryption at rest; field-level cho OAuth tokens.
- **Audit:** structured logs + audit table; correlation id; PII minimization in logs.
- **Supply chain:** lockfile, image scan, dependency update policy.

---

## 8. Deployment topologies

1. **SaaS multi-tenant** — single region MVP.  
2. **Enterprise** — dedicated namespace / cluster; optional **data residency** flag.  
3. **Government / regulated** — VPC private, no public egress from data plane, **BYOM**, offline embedding option.

---

## 9. Cost optimization (platform)

- Batch embeddings; cache embedding per content hash.
- Reranking chỉ trên top-k nhỏ.
- Prompt compression / structured citations only.
- Router: haiku-class cho extract; strong model cho reasoning có budget gate.
- Spot / autoscale workers; separate GPU node pool cho self-host LLM.

---

## 10. Versioning tài liệu này

- **v1** — baseline cho codegen Composer-2.  
- Mọi thay đổi breaking stack → bump `MASTER_SPEC.md` §1 và ghi **ADR** mới trong `ADR/`.
- Release note ngắn có thể gắn vào `TASK_LIST.md` khi cần.
