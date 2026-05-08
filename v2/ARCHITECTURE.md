# ARCHITECTURE — AI Knowledge OS (**v2**)

**Status:** canonical technical architecture (supersedes **v1** cho các mục Identity & Deployment).  
**Principles v1:** permission-aware, multi-tenant, event-driven ingestion, retrieval-before-generation, observable AI.  
**Principles v2:** **pluggable authentication**; **packaged deploy** (Compose + Helm).

---

## 1. High-level diagram (cập nhật)

```text
Clients (Web / Mobile / API clients)
        │
        ▼
┌───────────────────┐
│  Edge / CDN       │  TLS, WAF, rate limit
└─────────┬─────────┘
          ▼
┌───────────────────┐
│  API Gateway      │  Routing, B2B API keys, request ID
└─────────┬─────────┘
          ▼
┌───────────────────────────────┐
│  Identity & Auth Gateway      │  Multi-provider: OIDC, SAML, LDAP*, local, API keys
│  (modular / Passport-style)   │  → unified session + JWT / cookie
└─────────┬─────────────────────┘
          ▼
┌───────────────────┐
│  Tenant context   │  org policy: allowed auth methods
└─────────┬─────────┘
          ▼
┌───────────────────┐
│  Application API  │  NestJS — Knowledge OS
└─────────┬─────────┘
          │
    (Connector, Ingestion, Search/RAG, AI Orchestrator — giữ như v1)
          ▼
┌─────────────────────────────────────────────┐
│ PostgreSQL │ Qdrant │ Redis+BullMQ │ S3      │
└─────────────────────────────────────────────┘
```

\* LDAP optional theo tier / ADR.

---

## 2. Stack binding (v2 = v1 + Identity & packaging)

| Layer | Choice | Ghi chú v2 |
|-------|--------|------------|
| Auth | **Modular providers** trong API (NestJS guards + strategy per method) | Không hardcode một OAuth app |
| Session | httpOnly cookie **và/hoặc** Bearer access JWT | Cấu hình theo `MASTER_SPEC` |
| SAML | `passport-saml` hoặc tương đương + metadata store | SP metadata per tenant hoặc global SP + tenant IdP |
| OIDC | OpenID Connect discovery | |
| Packaging | **Docker Compose** profiles + **Helm umbrella** | Xem §8 |
| Còn lại | Giữ v1 (Next, Nest, PG, Qdrant, Redis, BullMQ, OTel) | |

---

## 3. Bounded context — **Identity** (mở rộng)

1. **AuthProviderRegistry** — danh sách provider enabled globally + per-organization overrides.
2. **UserIdentity** — bảng liên kết `user_id` ↔ `provider` ↔ `subject` (issuer + sub); unique constraint tránh trùng.
3. **Session / Token** — refresh rotation; revoke theo user / org.
4. **Service accounts** — API keys hash; không dùng cho interactive SSO.

---

## 4. Permission model

Không đổi so v1 (RBAC + ACL + retrieval filter).

**v2:** role assignment có thể map từ **SAML attributes** / **OIDC claims** / **LDAP groups** (configurable mapping table).

---

## 5–7. Ingestion, graph, security

Giữ nguyên nội dung **v1** (`../v1/ARCHITECTURE.md` §5–7) — áp dụng trừ khi ADR mới sửa.

---

## 8. Deployment topologies & **one-package**

### 8.1 Profile `all-in-one` (Compose)

- Một file `docker-compose.release.yml` (hoặc tên tương đương) với **tất cả** service: `web`, `api`, `worker`, `postgres`, `redis`, `qdrant`, `minio` (hoặc mock S3).
- **Healthchecks** trên mỗi service; `depends_on: condition: service_healthy` nơi cần.
- **Migration:** container `api migrate` hoặc entrypoint `&& prisma migrate deploy` trước khi listen.
- **Giới hạn:** phù hợp POC / nhỏ; không khuyến nghị CQ production-scale trên một VM không hardening.

### 8.2 Profile `distributed` (Compose)

- Tách volume/network giống dev nâng cao; phục vụ staging.

### 8.3 Kubernetes — **Helm umbrella chart**

- Chart `hydra-knowledge` (tên gợi ý) với subchart hoặc templates cho từng component; `values.yaml` cho image tag, resources, ingress, secrets (ExternalSecrets tùy chọn).
- **Job** `helm hook` post-install/upgrade cho DB migrate.

### 8.4 Versioning release

- Một **tag semver** cho toàn bộ image (mono-version) hoặc matrix version có bảng tương thích trong `packaging/README.md`.

---

## 9. Cost optimization

Giữ v1.

---

## 10. Versioning tài liệu

- **v2** — bổ sung multi-auth + one-package; kế thừa v1.
- Breaking → ADR + cập nhật `MASTER_SPEC.md` §1.
