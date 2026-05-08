# MASTER_SPEC — AI Knowledge OS (**v2**)

**Binding rules:** ràng buộc thiết kế cho agent codegen. **v2** mở rộng **§3 Identity (multi-auth)** và **§13 Deployment packaging**. Phần còn lại kế thừa **v1** trừ khi conflict — ưu tiên **v2**.

**Reference v1:** `../v1/MASTER_SPEC.md` cho các mục AI, RAG, connector chưa lặp lại đầy đủ dưới đây (v2 giả định chúng vẫn hiệu lực).

---

## 1. Stack (frozen — v2 = v1 + packaging artifacts)

- Giữ **v1 §1** (pnpm, Turborepo, Next.js, NestJS, PostgreSQL 16+, Redis, BullMQ, Qdrant, S3-compatible, OpenTelemetry).
- **Bổ sung:** repository **phải** chứa `packaging/` hoặc `deploy/` với:
  - `docker-compose*.yml` có **profile** `all-in-one` và `distributed` (tên profile có thể đổi nhưng phải document).
  - `helm/` umbrella chart (tối thiểu skeleton + `values.yaml` mẫu) — có thể phase **P0.8** hoàn thiện.

---

## 2. Multi-tenancy & isolation

Giữ nguyên **v1 §2**.

---

## 3. Identity & access — **multi-auth (v2 binding)**

### 3.1 Kiến trúc

- **AuthProvider** là khái niệm hạng nhất: mỗi provider có `type` (`local_password`, `magic_link`, `oauth2`, `oidc`, `saml`, `ldap`, `api_key`) và cấu hình riêng.
- **OrganizationAuthPolicy** (hoặc tương đương): danh sách provider **cho phép** + thứ tự hiển thị UI + có bắt buộc SSO hay không.
- Mọi đăng nhập thành công phải map về **một `user_id` nội bộ** và **một tenant context** hợp lệ.

### 3.2 User & linking

- Bảng (hoặc tương đương) **`user_identities`**: `user_id`, `provider_type`, `issuer`, `subject`, `metadata`, `created_at`.
- **Unique:** (`issuer`, `subject`) hoặc (`provider_type`, `subject`) tùy thiết kế — **không** cho duplicate accidental.
- **Account linking:** chỉ cho phép khi policy org cho phép; có audit event.

### 3.3 Phương thức tối thiểu (MVP v2)

- **A)** Email + password (hash Argon2id hoặc bcrypt cost đủ cao).
- **B)** Ít nhất **một** trong: OAuth2 Google **hoặc** OIDC generic discovery.
- **C)** SAML **hoặc** OIDC enterprise: có trong roadmap **P1 late / P2** nhưng schema & abstraction phải chừa từ **P0–P1**.

### 3.4 SAML / OIDC enterprise

- SAML: hỗ trợ SP-initiated; metadata; clock skew config; **signed assertions**; attribute mapping → `email`, `displayName`, optional `groups`.
- OIDC: discovery; PKCE cho public clients nếu có; validate `iss`, `aud`, `exp`.
- **JIT provisioning:** tạo user khi lần đầu SSO thành công nếu bật; default role `viewer` hoặc theo mapping.

### 3.5 API keys / service accounts

- Key format: prefix rõ (`hak_…` gợi ý); lưu **hash**; rotation; scope tối thiểu `read` / `ingest` / `admin` tách bạch.
- **Không** dùng API key thay SSO người dùng trong UI interactive.

### 3.6 Session & tokens

- Access token TTL ngắn; refresh an toàn (rotation); revoke list hoặc version `session_version` trên user.
- CORS và cookie `SameSite` theo môi trường; ghi rõ trong runbook packaging.

### 3.7 RBAC, document ACL, classification

Giữ **v1 §3.3–3.5** (roles, ACL snapshot, classification).

### 3.8 Audit (auth)

- Log: `login_success`, `login_failure` (không log password), `logout`, `provider_linked`, `sso_callback_error` (message rút gọn).
- Ghi **provider_type** + **issuer** (không log full SAML assertion).

---

## 4–12. AI, connectors, ingestion, search, security, privacy, API, NFR

Áp dụng **v1 §4–11** đầy đủ. Bổ sung:

- **Rate limit** riêng cho `/auth/*` và `/sso/*` theo IP + fingerprint nhẹ.

---

## 13. Deployment packaging (**v2 binding**)

### 13.1 Compose `all-in-one`

- Một lệnh documented (ví dụ `docker compose --profile all-in-one up -d`) khởi chạy đủ để: **web + api + worker + postgres + redis + qdrant + object storage**.
- **Migrate** chạy trước khi API nhận traffic production (entrypoint hoặc init container pattern trong Compose v2).
- **Secrets:** không commit `.env` thật; chỉ `.env.example` + `secrets/README`.

### 13.2 Helm

- Chart cài được trên K8s 1.28+; values cho replica, resources, ingress TLS.
- **Job** migrate với hook upgrade.

### 13.3 Health

- `GET /health` (hoặc `/healthz`) trên API: `ok` + version build; worker có metric hoặc health port tùy chọn.

### 13.4 Tài liệu

- `packaging/README.md`: ma trận **mode** × **yêu cầu tài nguyên tối thiểu**.

---

## 14. References

- `README.md`, `Business_strategy.md`, `FEATURES.md`, `ARCHITECTURE.md`, `TASK_LIST.md`.
- `ADR/0004` (multi-auth), `ADR/0005` (one-package).
- `../v1/MASTER_SPEC.md` — baseline lịch sử.
