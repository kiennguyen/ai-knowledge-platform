# Business strategy — AI Knowledge OS (**v2**)

**Audience:** founders, investors, product; **binding** cho engineering chỉ qua `MASTER_SPEC.md`.

**Thay đổi so v1:** nhấn mạnh **đa xác thực** (doanh nghiệp / cơ quan dùng nhiều IdP và chính sách) và **triển khai gói đơn** (rút thời gian POC, on-prem, đối tác tích hợp).

---

## 1. Vision

Giữ nguyên north-star v1: **AI Knowledge Operating System** — tìm kiếm & RAG **có quyền**, có căn cứ, mở rộng graph/workflow.

**v2 bổ sung positioning vận hành:**

- *“Đăng nhập đúng chuẩn tổ chức bạn — SAML, Entra, Google Workspace, hoặc tài khoản nội bộ — trên cùng một nền.”*
- *“Cài đặt một gói: POC trong ngày, không cần lắp từng service tay.”*

---

## 2. Vì sao v2 matters cho thị trường

- **DN / CQ:** thường đã có **Entra ID / Keycloak / LDAP**; yêu cầu **không ép một kiểu login**; cần **domain / tenant** tách IdP.
- **Bán hàng & đối tác:** giảm friction triển khai → **Helm umbrella** hoặc **Compose production profile** → reseller / SI dễ đưa vào VPC khách.
- **Chi phí cơ hội:** POC chậm vì thiếu “bộ cài” chuẩn → mất deal so với vendor có installer.

---

## 3. Chiến lược tầng sản phẩm

Giữ bảng phase v1 (P1 Search → P2 Workspace → P3 Workflow OS).

**Điều chỉnh GTM v2:**

| Chủ đề | Hành động |
|--------|-----------|
| **Auth** | Gói Business/Enterprise: **SSO bắt buộc** optional theo tenant; Pro: social + email; self-host: admin bật provider |
| **Deploy** | SKU “**Airgap-friendly bundle**” (tài liệu + image list + offline registry guide) — roadmap ER |
| **Pricing** | Có thể tính thêm “**managed IdP connector**” hoặc gói **on-prem support** |

---

## 4. GTM & monetization

- Giữ direct sales, demo-led, verticalization (v1).
- **Thước đo bổ sung v2:** thời gian **từ zero → POC login SSO + upload + chat** (mục tiêu: cùng ngày với one-package).
- **Trust:** audit login theo **provider** + method (không chỉ “user id”).

---

## 5. Rủi ro & cạnh tranh

- **Tránh:** mỗi khách một nhánh code auth — phải **plugin/config**.
- **Tránh:** “one-package” nhưng không có **healthcheck / migrate / backup** → production giả; bắt buộc có checklist trong `TASK_LIST` **P0.6–P0.9**.

---

## 6. Tuân thủ & niềm tin

- Giữ v1 (classification, audit, DLP hook).
- **v2:** mapping chính sách **“ai được dùng phương thức đăng nhập nào”** theo organization (ví dụ chỉ SAML cho CQ).

---

## 7. Tài liệu liên quan

- `FEATURES.md`, `ARCHITECTURE.md`, `MASTER_SPEC.md`, `TASK_LIST.md`, `ADR/0004`, `ADR/0005`.
