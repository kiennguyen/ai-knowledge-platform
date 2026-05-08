# ADR 0004 — Pluggable identity: multi-auth per organization

- **Status:** Accepted  
- **Date:** 2026-05-06  
- **Deciders:** Architecture / Security  

## Context

Doanh nghiệp và cơ quan yêu cầu:

- Nhiều **IdP** (Entra, Keycloak, Google Workspace, LDAP) và không thống nhất một vendor.
- Một số tenant chỉ cho **SSO**; tenant khác cho phép email/password hoặc social.
- Cần **audit** và **account linking** có kiểm soát.

Hardcode một luồng OAuth duy nhất sẽ chặn sales và gây fork code theo khách.

## Decision

1. **AuthProvider abstraction** bắt buộc: mỗi kiểu xác thực là module/strategy đăng ký với `type` + config (DB hoặc seed + override).
2. **OrganizationAuthPolicy** quyết định provider nào **hiển thị / cho phép**; cờ `sso_required` tắt local password khi cần.
3. **user_identities** lưu mapping bên ngoài → `user_id` nội bộ; đăng nhập luôn kết thúc bằng session/JWT nội bộ thống nhất.
4. **SAML và OIDC enterprise** là first-class trong roadmap sớm (schema + callback routes từ Phase 1), không “bolt-on” sau.
5. **API keys** là identity class riêng (service account), không reuse refresh token người dùng.

## Consequences

- **Positive:** một codebase phục vụ SME (social+local) và CQ/DN (SAML); demo SSO không fork.
- **Negative:** surface area bảo mật lớn hơn — cần test matrix và hardening `MASTER_SPEC` §3.
- **Risks:** lỗi mapping SAML attribute → user sai org — **mitigation:** strict `NameID`/claim rules + JIT chỉ khi domain verified.

## Links

- `MASTER_SPEC.md` §3  
- `FEATURES.md` (Identity)  
- `TASK_LIST.md` A1.2–A1.12  
