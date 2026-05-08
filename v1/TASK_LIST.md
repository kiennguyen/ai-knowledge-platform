# TASK_LIST — AI Knowledge OS (v1)

**Cách dùng:** Giữ **ID ổn định** khi commit / PR / agent (Composer-2). **DoD** = Definition of Done.

**Nguồn chân lý kỹ thuật:** `MASTER_SPEC.md` + `ARCHITECTURE.md`.

---

## PHASE 0 — Repo & foundations

- [ ] **P0.1** Monorepo **pnpm** + **Turborepo**; `apps/web`, `apps/api`, `apps/worker`, `packages/db`, `packages/shared-types`, `packages/ai-core` (hoặc tương đương).
- [ ] **P0.2** TypeScript **strict**; ESLint + Prettier; CI lint + typecheck trên PR.
- [ ] **P0.3** `docker-compose` dev: PostgreSQL 16, Redis, MinIO, **Qdrant**; `.env.example` thống nhất.
- [ ] **P0.4** OpenTelemetry bootstrap (API + worker) — có thể noop exporter local.
- [ ] **P0.5** Structured logging + `X-Request-Id` propagation.

**DoD:** `pnpm turbo run build` xanh local + CI; `docker compose up` chạy được stack dev.

---

## PHASE 1 — Identity, tenant, RBAC

- [ ] **A1.1** Schema: `organizations`, `workspaces`, `users`, `workspace_members`, invites (token hash + TTL).
- [ ] **A1.2** Auth: OIDC/JWT (hoặc local auth tạm) + refresh; session policy documented.
- [ ] **A1.3** RBAC guards NestJS; `@WorkspaceRoles()` hoặc tương đương.
- [ ] **A1.4** Tenant isolation middleware + **tests** cross-tenant (`403`/empty).
- [ ] **A1.5** Audit table: đăng nhập, thay đổi role, mời thành viên.

**DoD:** không có API leak workspace khác; audit có mốc thời gian + actor.

---

## PHASE 2 — Documents & object storage

- [ ] **D2.1** Upload API + multipart; virus scan **interface** + stub/dev implementation.
- [ ] **D2.2** S3-compatible storage; presigned URL; content-type validation.
- [ ] **D2.3** Versioning tài liệu; metadata (title, source, hash).
- [ ] **D2.4** Folder/collection model; ACL inheritance (ít nhất: workspace + explicit doc ACL).
- [ ] **D2.5** Classification label trên document (enum `MASTER_SPEC`).

**DoD:** upload PDF/DOCX → metadata persist; ACL đọc được ở ingestion.

---

## PHASE 3 — Ingestion pipeline

- [ ] **I3.1** Worker queue BullMQ: job `ingest_document` idempotent theo `(workspace_id, content_hash)`.
- [ ] **I3.2** Text extract PDF/DOCX/HTML; OCR path cho ảnh (feature flag).
- [ ] **I3.3** Chunking + overlap; lưu chunk table với `embedding_version`.
- [ ] **I3.4** Permission snapshot trên chunk (denormalize để filter nhanh).
- [ ] **I3.5** DLQ + admin-visible error reason (API nội bộ).

**DoD:** re-run ingestion không duplicate chunk; ACL thay đổi có đường cập nhật (full hoặc delta).

---

## PHASE 4 — Embeddings & Qdrant

- [ ] **E4.1** Embedding provider abstraction (OpenAI + optional self-host).
- [ ] **E4.2** Qdrant collection naming + payload schema **chuẩn** (`tenant_id`, `workspace_id`, …).
- [ ] **E4.3** Hybrid search: PostgreSQL FTS + vector; merge deterministic.
- [ ] **E4.4** Optional reranker (cross-encoder hoặc API) — behind flag.
- [ ] **E4.5** Citation payload trả về `source_ref` ổn định cho UI.

**DoD:** search không trả chunk của user khác; có evaluation set tiếng Việt tối thiểu (manual checklist OK P1).

---

## PHASE 5 — AI chat (RAG)

- [ ] **AI5.1** Chat API streaming (SSE hoặc WS — chọn một).
- [ ] **AI5.2** Context builder: retrieve → compress → LLM; max token budget.
- [ ] **AI5.3** Model router + usage metering (`llm_usage_events`).
- [ ] **AI5.4** Safety: prompt injection mitigations baseline (system prompt + tool allowlist).
- [ ] **AI5.5** Trả lời có **citations**; mode “strict no source → refuse”.

**DoD:** demo E2E: hỏi câu chỉ có trong doc A → trích dẫn đúng; câu không có nguồn → refuse.

---

## PHASE 6 — Connectors (MVP)

- [ ] **C6.1** Google OAuth; Drive listing + delta sync job.
- [ ] **C6.2** Gmail read scope (theo policy); sync thread metadata + body extract (privacy review).
- [ ] **C6.3** Token encryption at rest; rotate/revoke.
- [ ] **C6.4** Microsoft Graph (phase 1.5 nếu cần) — có thể tách task nhỏ.

**DoD:** một workspace kết nối Drive/Gmail test; revoke token xóa quyền sync.

---

## PHASE 7 — Knowledge graph (MVP)

- [ ] **KG7.1** Entity & edge schema (Postgres) hoặc import graph DB sau.
- [ ] **KG7.2** Job extract entities từ chunk (rule + LLM nhẹ) — flag.
- [ ] **KG7.3** Graph-aware retrieval (expand neighbors có giới hạn bậc + ACL).

**DoD:** query demo “tài liệu liên quan project X” tốt hơn vector-only trên bộ mẫu nội bộ.

---

## PHASE 8 — Workspace AI features

- [ ] **W8.1** Email thread summarize (đã sync).
- [ ] **W8.2** Meeting transcript summarize + action items.
- [ ] **W8.3** Policy / handbook Q&A template.
- [ ] **W8.4** Shared collections & comments (tối thiểu).

**DoD:** 2 workflow nội bộ chạy được với audit.

---

## PHASE 9 — Workflow engine

- [ ] **WF9.1** Workflow definition storage + runner (BullMQ hoặc Temporal spike).
- [ ] **WF9.2** Human approval step + notification hook.
- [ ] **WF9.3** Idempotency keys; retry policy.

**DoD:** một workflow “draft → approve → execute tools read-only” chạy production-like.

---

## PHASE 10 — Agents (controlled)

- [ ] **AG10.1** Agent runtime: tool registry **read-only search** trước.
- [ ] **AG10.2** Per-role tool policy; budget bước + token.
- [ ] **AG10.3** Agent audit: tool call log + output hash.

**DoD:** agent không gọi tool ngoài allowlist; audit đầy đủ.

---

## PHASE 11 — Enterprise / government readiness

- [ ] **ER11.1** SAML/OIDC SSO; domain claim.
- [ ] **ER11.2** SCIM (optional).
- [ ] **ER11.3** Audit export API (CSV/JSON) + retention policies.
- [ ] **ER11.4** BYOK / CMK integration blueprint.
- [ ] **ER11.5** BYOM endpoint (OpenAI-compatible) cho VPC.
- [ ] **ER11.6** Deployment guide: private cluster, no egress option.

**DoD:** checklist bảo mật khách hàng (tick-box) có tài liệu 1 trang + evidence logs.

---

## PHASE 12 — Observability, reliability, cost

- [ ] **O12.1** Dashboards: latency search/chat, queue depth, error rate, **cost/day**.
- [ ] **O12.2** Alerting: DLQ growth, Qdrant latency, DB connections.
- [ ] **O12.3** Backup DB + bucket policy; restore drill documented.
- [ ] **O12.4** Load test kịch bản search/chat (k6 hoặc tương đương).

**DoD:** on-call có runbook 1-pager cho incident ingestion/search.

---

## PHASE 13 — Monetization & platform (khi sẵn sàng)

- [ ] **B13.1** Billing (Stripe hoặc local PSP) + seat limit enforcement.
- [ ] **B13.2** AI credits / soft cap per workspace.
- [ ] **B13.3** Partner API keys scoped.

**DoD:** downgrade plan không leak premium features.

---

## Gợi ý timeline (tham khảo — không binding)

| Cửa sổ | Mục tiêu |
|--------|----------|
| 0–2 tháng | P0–P2 + search hybrid cơ bản |
| 3–4 tháng | P3–P5 + connector đầu tiên |
| 5–8 tháng | P6–P8 + enterprise hardening một phần |
| 12+ tháng | Workflow + agent có kiểm soát + ER đầy đủ |

---

## Anti-patterns (agent không được introduce)

- RAG không filter ACL.
- Lưu OAuth token plain text.
- Log full prompt/PII trong production mặc định.
- Single-tenant assumption trong schema.

---

**END v1**
