# FEATURES — AI Knowledge OS (**v2**)

**Mục đích:** capability theo phase; chi tiết ràng buộc trong `MASTER_SPEC.md`.

**v2 — themes:** **Multi-auth** + **One-package deployment** xuyên suốt các phase.

**Nguyên tắc:** mọi luồng tri thức **tenant + permission**; AI không bypass ACL.

---

## Cross-cutting (v2) — Identity

Hỗ trợ **nhiều kiểu xác thực**, bật/tắt theo **organization / workspace policy** (trong giới hạn license):

| Loại | Mô tả gợi ý | Ghi chú |
|------|-------------|---------|
| **Email + password** | Đăng ký / mời; hash mật khẩu chuẩn bảo mật | Có thể tắt trên tenant chỉ-SSO |
| **Magic link / OTP email** | Đăng nhập không mật khẩu tĩnh | Rate limit + TTL |
| **OAuth2 social / workspace** | Google, Microsoft consumer, v.v. | Scope tối thiểu |
| **OIDC** | Generic OIDC (Keycloak, Authelia, …) | Discovery URL + client creds |
| **SAML 2.0** | Entra ID, Okta, v.v. | Metadata XML / URL; JIT provision |
| **LDAP / AD** (optional tier) | Bind service account; group → role map | Thường on-prem |
| **API keys / service accounts** | B2B automation, connector server-side | Scope hẹp; rotation |

**Account linking:** cùng email hoặc admin-merge theo policy; tránh duplicate user cross-provider (xem `MASTER_SPEC`).

**MFA (nâng cao):** WebAuthn / TOTP optional theo tenant (roadmap ER).

---

## Cross-cutting (v2) — Deployment

**Mục tiêu:** một **gói triển khai** có tài liệu và artifact rõ ràng.

| Mode | Đối tượng |
|------|-----------|
| **Docker Compose — profile `all-in-one`** | Dev, POC, SMB 1 máy / 1 VM |
| **Docker Compose — profile `distributed`** | Tách DB/Redis/Qdrant (v1-style) |
| **Helm chart (umbrella)** | K8s production; values cho image tag, secrets, ingress |
| **Offline / airgap checklist** | CQ — mirror registry + bundle env |

**Yêu cầu sản phẩm:** health endpoints; job migrate DB một lần; seed tối thiểu; version in `GET /health` hoặc tương đương.

---

## Phase 1 — AI Internal Search

Giữ core v1 (upload, hybrid search, RAG chat, connectors).

**v2 bổ sung DoD P1:**

- Ít nhất **2 phương thức auth** hoạt động trên cùng build (ví dụ: email/password + Google OIDC **hoặc** OIDC generic stub).
- Chạy được stack qua **Compose profile `all-in-one`** theo tài liệu `packaging/` (xem `TASK_LIST` **P0.6**).

---

## Phase 2 — AI Knowledge Workspace

Giữ v1.

**v2:** SAML/OIDC **theo tenant** cho workspace business; audit ghi `auth_provider` + `subject_issuer`.

---

## Phase 3 — AI Workflow OS

Giữ v1.

**v2:** service account / API key có scope để gọi workflow read-only (không thay RBAC người).

---

## Phase Enterprise / Government

Giữ v1 + **bắt buộc** document: chỉ SAML/LDAP; tắt social login; **Compose không dùng cho prod CQ** — dùng K8s + hardening (tham chiếu `ADR/0005`).

---

## Explicit non-goals (v2)

- Không hứa một installer “next-next-finish” cho mọi OS — ưu tiên **Compose + Helm** + doc.
- Không tự host IdP — tích hợp IdP khách.

---

## Traceability

Map tới `TASK_LIST.md` (**P0.6–P0.9**, **A1.x** mở rộng) và `ADR/0004`, `ADR/0005`.
