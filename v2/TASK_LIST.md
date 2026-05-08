# TASK_LIST — AI Knowledge OS (**v2**)

**Cách dùng:** ID ổn định cho commit / PR / Composer-2. **DoD** = Definition of Done.  
**Delta so v1:** **P0.6–P0.10** (packaging), **A1.6–A1.12** (multi-auth); một số mục **ER11** tinh gọn vì SAML sớm hơn trong abstraction.

**Nguồn chân lý:** `MASTER_SPEC.md` (v2) + `ARCHITECTURE.md` (v2).

---

## PHASE 0 — Repo & foundations

- [ ] **P0.1** Monorepo pnpm + Turborepo; `apps/web`, `apps/api`, `apps/worker`, `packages/db`, `packages/shared-types`, `packages/ai-core`.
- [ ] **P0.2** TypeScript strict; ESLint + Prettier; CI lint + typecheck.
- [ ] **P0.3** `docker-compose` **dev** + **profile** `distributed` (Postgres, Redis, MinIO, Qdrant).
- [ ] **P0.4** OpenTelemetry bootstrap (API + worker).
- [ ] **P0.5** Structured logging + `X-Request-Id`.
- [ ] **P0.6** Thư mục **`packaging/`** (hoặc `deploy/`) + `packaging/README.md` mô tả mode triển khai.
- [ ] **P0.7** **`docker-compose.release.yml`** (tên có thể đổi nhưng 1 file “all-in-one” rõ ràng) với profile **`all-in-one`**: web, api, worker, postgres, redis, qdrant, minio; **healthchecks** + **migrate trước listen**.
- [ ] **P0.8** **Helm umbrella** skeleton `packaging/helm/hydra-knowledge/` + `values.yaml` mẫu + README cài đặt.
- [ ] **P0.9** **`GET /health`** API: status + **version/build** (git sha hoặc semver image).
- [ ] **P0.10** CI: build **tất cả** Docker images (hoặc một image monolith nếu chọn — phải ADR) và scan cơ bản (Trivy optional).

**DoD:** `docker compose --profile all-in-one up` (hoặc lệnh tương đương trong README) lên được stack; migrate chạy; health xanh.

---

## PHASE 1 — Identity, tenant, RBAC (**multi-auth**)

- [ ] **A1.1** Schema org, workspace, users, members, invites (giữ v1).
- [ ] **A1.2** Bảng **`user_identities`** + unique (`issuer`,`subject`) hoặc tương đương.
- [ ] **A1.3** **AuthProviderRegistry** config (DB hoặc YAML seed + override DB) — types: `local_password`, `oauth2`, `oidc`, `saml`, `magic_link`, `api_key` (stub LDAP có thể sau).
- [ ] **A1.4** **OrganizationAuthPolicy** — danh sách provider cho phép; flag `sso_required`.
- [ ] **A1.5** **Local auth:** register/invite + password hash Argon2id/bcrypt; rate limit.
- [ ] **A1.6** **OAuth2** (Google hoặc Microsoft consumer) — env client id/secret; redirect URI documented trong packaging.
- [ ] **A1.7** **OIDC generic** — discovery URL, client creds, claim mapping → email/name.
- [ ] **A1.8** **SAML 2.0** — metadata upload/URL; SP ACS; attribute mapping; JIT optional.
- [ ] **A1.9** **Magic link** (optional P1) — TTL, one-time token, email provider abstraction.
- [ ] **A1.10** **API keys** — hash, scope, rotation UI/API nội bộ.
- [ ] **A1.11** RBAC guards + tenant isolation tests **cho từng auth path** (local vs SSO mock).
- [ ] **A1.12** Audit auth events (`login_success/fail`, `provider_linked`, `sso_error`).

**DoD:** 2+ phương thức auth hoạt động; SAML hoặc OIDC có ít nhất một integration test (mock hoặc dockerized IdP); không leak cross-tenant.

---

## PHASE 2 — Documents & object storage

Giữ **v1 D2.1–D2.5** (đổi prefix nếu cần nhất quán repo).

**DoD:** giữ nguyên v1.

---

## PHASE 3 — Ingestion pipeline

Giữ **v1 I3.1–I3.5**.

---

## PHASE 4 — Embeddings & Qdrant

Giữ **v1 E4.1–E4.5**.

---

## PHASE 5 — AI chat (RAG)

Giữ **v1 AI5.1–AI5.5**.

---

## PHASE 6 — Connectors (MVP)

Giữ **v1 C6.1–C6.4**.

---

## PHASE 7 — Knowledge graph (MVP)

Giữ **v1 KG7.1–KG7.3**.

---

## PHASE 8 — Workspace AI features

Giữ **v1 W8.1–W8.4**.

---

## PHASE 9 — Workflow engine

Giữ **v1 WF9.1–WF9.3**.

---

## PHASE 10 — Agents (controlled)

Giữ **v1 AG10.1–AG10.3**.

---

## PHASE 11 — Enterprise / government readiness

- [ ] **ER11.1** Domain claim + **SSO-only** org mode (đã có nền A1.8).
- [ ] **ER11.2** SCIM (optional).
- [ ] **ER11.3** Audit export + retention.
- [ ] **ER11.4** BYOK blueprint.
- [ ] **ER11.5** BYOM OpenAI-compatible.
- [ ] **ER11.6** **Airgap / offline** install guide: mirror images + `helm install` với `imagePullSecrets`.
- [ ] **ER11.7** MFA WebAuthn/TOTP (optional).

**DoD:** checklist 1 trang + evidence; airgap doc link từ `packaging/README.md`.

---

## PHASE 12 — Observability, reliability, cost

Giữ **v1 O12.1–O12.4**.

---

## PHASE 13 — Monetization & platform

Giữ **v1 B13.1–B13.3**.

---

## Anti-patterns (v2 bổ sung)

- Một file `auth.service.ts` khổng lồ if/else provider — phải **strategy/registry**.
- Compose production không migrate — **cấm**.
- Lưu SAML `NameID` + assertion trong log — **cấm** (chỉ hash/id ngắn).

---

## Gợi ý timeline (v2)

| Cửa sổ | Mục tiêu |
|--------|----------|
| Tuần 1–2 | P0.6–P0.9 + A1.1–A1.6 |
| Tuần 3–6 | A1.7–A1.12 + P0.8 Helm + Phase 2 docs |
| Tiếp theo | Giữ roadmap v1 từ Phase 2 trở đi |

---

**END v2**
