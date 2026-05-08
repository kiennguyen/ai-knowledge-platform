# Business strategy — AI Knowledge OS (v1)

**Audience:** founders, investors, product; **binding for engineering** only where cross-referenced in `MASTER_SPEC.md`.

---

## 1. Vision

Xây dựng **AI Knowledge Operating System** — lớp AI và tri thức nằm trên toàn bộ tài liệu, giao tiếp và quy trình nội bộ doanh nghiệp / cơ quan: không chỉ “chat với PDF”, mà là **tìm kiếm có quyền**, **truy hồi có căn cứ**, **đồ thị tri thức**, và (giai đoạn sau) **workflow + agent** có kiểm soát.

**North-star:** *Central AI brain for organizations* — an toàn, kiểm chứng được, tối ưu chi phí inference.

---

## 2. Vì sao thị trường (đặc biệt VN & ASEAN SME / agency)?

- Tri thức phân tán: Drive, email, Zalo/Slack, file cục bộ, ERP/CRM.
- Chi phí cơ hội: tìm kiếm chậm, onboarding kém, mất kiến thức khi nghỉ việc, quá tải thông tin.
- Khoảng trống: nền tảng **ngang** (Copilot, v.v.) khó đi sâu **workflow địa phương**, **tuân thủ**, **tích hợp hành chính / logistics / giáo dục**.

**Không cạnh tranh ở tầng foundation model.** Dùng model tốt nhất theo từng tác vụ; moat nằm ở **connector + permission graph + ingestion tin cậy + workflow + bộ nhớ tổ chức**.

---

## 3. Chiến lược tầng sản phẩm (roadmap kinh doanh)

| Giai đoạn | Định vị | Đối tượng |
|-----------|---------|-----------|
| **P1 — AI Internal Search** | “Google nội bộ + AI” — upload, connector cơ bản, chat có citation, RAG có quyền | SME 20–300 người, agency, logistics, giáo dục, XNK |
| **P2 — AI Knowledge Workspace** | Tóm tắt email/cuộc họp, onboarding, SOP/policy assistant, không gian nhóm | Team & SMB mở rộng |
| **P3 — AI Workflow OS** | Agent có giới hạn, orchestration, phê duyệt, automation vận hành | Doanh nghiệp lớn, pilot cơ quan (khi đủ ER) |

**Verticalization (bắt buộc để scale):** chọn 1–2 vertical sớm (ví dụ: hành chính văn bản / logistics / giáo dục) để packaging compliance, template workflow, và sales.

---

## 4. GTM & monetization

- **GTM:** demo-led, founder sales, nội dung LinkedIn, workshop doanh nghiệp, partner tư vấn / reseller.
- **Pricing gợi ý:** Free (giới hạn) → Pro (theo seat + storage) → Business (SSO, retention, SLA) → Enterprise (VPC, on-prem, BYOK/BYOM).
- **Thước đo P1:** time-to-answer, độ chính xác truy hồi, tỷ lệ upload/sync thành công, **không lộ dữ liệu cross-tenant**, khách trả phí đầu tiên.

---

## 5. Rủi ro & định vị cạnh tranh

- **Tránh:** generic “chat PDF”, wrapper mỏng quanh một API, RAG không permission.
- **Tập trung:** ingestion production-grade, **permission-aware retrieval**, knowledge graph theo giai đoạn, audit, cost router, triển khai hybrid/on-prem khi khách yêu cầu.

---

## 6. Tuân thủ & niềm tin (doanh nghiệp / cơ quan)

- Phân loại dữ liệu (nhãn), lưu vết truy cập, xuất audit phục vụ kiểm tra.
- Tách bạch tenant; không dùng dữ liệu khách để huấn luyện mặc định (chính sách rõ ràng trong hợp đồng).
- Hỗ trợ **SSO/SAML/OIDC**, **SCIM** (phase enterprise), **DLP hook** (metadata, không lưu nội dung nhạy cảm vào log).

---

## 7. Tài liệu liên quan

- `FEATURES.md` — capability theo phase.
- `ARCHITECTURE.md` — kiến trúc kỹ thuật & stack.
- `MASTER_SPEC.md` — ràng buộc không thể bỏ qua khi codegen.
- `TASK_LIST.md` — backlog có ID ổn định cho agent (ví dụ Composer).
