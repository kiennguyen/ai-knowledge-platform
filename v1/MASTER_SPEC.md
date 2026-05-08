# MASTER_SPEC — AI Knowledge OS (v1)

**Binding rules:** Nội dung dưới đây là **ràng buộc thiết kế** cho agent codegen (Composer-2). Nếu conflict với code cũ, ưu tiên cập nhật code hoặc ghi ADR.

---

## 1. Stack (frozen cho greenfield)

- Monorepo: **pnpm** + **Turborepo**.
- **Next.js** (App Router) + TypeScript strict.
- **NestJS** API + **PostgreSQL 16+**.
- **Redis** + **BullMQ** cho async jobs.
- **Qdrant** làm vector DB mặc định (payload có `tenant_id`, `workspace_id`, `document_id`, `chunk_id`, `acl`, `classification`, `embedding_version`).
- Object storage **S3-compatible**.
- Observability: **OpenTelemetry** instrumentation (HTTP, DB, queue) — có thể no-op trong dev.

---

## 2. Multi-tenancy & isolation

- Mọi bảng business phải có `tenant_id` hoặc `organization_id` + `workspace_id` theo schema chuẩn.
- Mọi API authenticated phải resolve **workspace context** từ token/session; **cấm** tin `workspace_id` từ client không được kiểm tra membership.
- Test bắt buộc: cross-tenant access → `403` / empty result (không leak existence).

---

## 3. Identity & access

### 3.1 Authentication (P1)

- OIDC/JWT access token; refresh flow; httpOnly cookie hoặc Bearer — chọn một pattern và nhất quán.
- Password policy nếu có local auth: theo chuẩn tổ chức (length, breach check optional).

### 3.2 Enterprise (P2+)

- **SAML 2.0** và/hoặc **OIDC** IdP (Entra ID, Keycloak).
- Optional **SCIM** for user lifecycle.

### 3.3 RBAC (baseline)

Roles tối thiểu: `owner`, `admin`, `member`, `viewer` (có thể mở rộng; không đổi ý nghĩa đã publish API).

### 3.4 Document access

- ACL lưu dạng có thể evaluate server-side; snapshot gắn chunk tại thời điểm index.
- Thay đổi ACL → job reindex delta hoặc mark chunk stale + async update.

### 3.5 Classification (enterprise)

- Enum gợi ý: `PUBLIC_INTERNAL`, `INTERNAL`, `CONFIDENTIAL`, `RESTRICTED` — mapping filter retrieval & export.

---

## 4. AI behavior

- **Citation-first:** response referencing `document_id` + `chunk_id` hoặc stable `source_ref`.
- **Refusal:** nếu không có nguồn đạt ngưỡng relevance, trả lời từ chối có cấu trúc (không bịa).
- **Model router:** config per workspace: max cost / day, default models, allowed providers.
- **Prompt/PII:** không ghi full user prompt vào audit mặc định; chỉ hash + metadata (có flag debug riêng, tắt prod).

---

## 5. Connectors

- Lưu OAuth token **encrypted** (KMS hoặc libsodium envelope per env).
- Refresh token rotation; revoke on disconnect.
- Respect provider rate limits — exponential backoff + jitter.

---

## 6. Ingestion & content safety

- Virus/malware scan stage trước khi parse (ClamAV hoặc cloud AV API — abstract interface).
- OCR pipeline optional flag per workspace (cost).
- Supported types P1: PDF, DOCX, HTML, PNG/JPEG (OCR); mở rộng sau.

---

## 7. Search & RAG contract

- Hybrid: PostgreSQL FTS + Qdrant vector; merge với RRF hoặc weighted score (implementation detail nhưng phải deterministic per query version).
- **Permission filter** applied **before** returning snippets to LLM.
- Version API: `search_version` / `embedding_version` trong response để debug.

---

## 8. Security & compliance

- **TLS 1.2+**; HSTS; secure cookies.
- **CSP** chặt trên Next.js; không inline script không cần thiết.
- **CORS** allowlist theo env.
- **Rate limiting** per IP + per user + per workspace.
- **Audit events** tối thiểu: login success/fail, connector connect/disconnect, document index/delete, search (metadata), chat session start (no body), admin RBAC change.
- **Data processing agreement:** default **no training** on customer content; feature flag rõ ràng nếu ever opt-in.

---

## 9. Privacy & data residency

- Config `DATA_REGION` / `STORAGE_REGION` (logical); không hardcode.
- Export & delete user/workspace theo SLA (technical checklist trong TASK_LIST).

---

## 10. API style

- REST JSON cho CRUD; SSE/WebSocket cho chat stream (một pattern được chọn và document).
- Idempotency-Key cho mutation quan trọng (upload complete, connector sync).

---

## 11. Non-functional

- P95 chat first token < SLA theo env (ghi rõ trong runbook).
- Worker retry: max attempts + DLQ; poison message alerting.

---

## 12. References

- `README.md` — thứ tự đọc cho người và agent.
- `Business_strategy.md`, `FEATURES.md`, `ARCHITECTURE.md`, `TASK_LIST.md`.
- `ADR/` — quyết định kiến trúc (BullMQ vs Temporal, Qdrant vs pgvector pilot, …).
