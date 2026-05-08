# FEATURES — AI Knowledge OS (v1)

**Mục đích:** mô tả năng lực sản phẩm theo phase; **không** thay thế chi tiết API — xem `MASTER_SPEC.md`.

**Nguyên tắc:** mọi tính năng đọc/ghi tri thức đều **gắn tenant + permission**; AI **không** được bypass ACL.

---

## Phase 1 — AI Internal Search (MVP bán được)

### Core

- Workspace đa tenant; mời thành viên; vai trò cơ bản (admin / power user / member / viewer — chi tiết trong `MASTER_SPEC`).
- Upload tài liệu: PDF, DOCX, HTML, ảnh (OCR pipeline); metadata & versioning.
- **Universal search:** full-text (DB/FTS) + semantic (vector); filter theo nguồn, thời gian, tag, **quyền**.
- **AI chat:** streaming; trả lời có **trích dẫn nguồn**; từ chối khi không đủ bằng chứng (configurable).
- Ngôn ngữ: **tiếng Việt** ưu tiên; hỗ trợ đa ngôn theo roadmap.

### Connectors (ưu tiên triển khai lần lượt)

- Google Drive, Gmail (OAuth, incremental sync).
- Microsoft 365: OneDrive, Outlook (Graph API).
- File folder / drag-drop.

### AI & cost

- Model router: tóm tắt nhẹ → model rẻ / nhanh; phân tích phức tạp → model mạnh (policy theo tenant).
- Theo dõi token/chi phí theo workspace; hạn mức (soft cap).

### Security & compliance (P1 tối thiểu)

- Audit log: ai truy vấn gì, thời điểm, tenant (không log nội dung prompt đầy đủ mặc định).
- Encryption in transit; encryption at rest (object storage + DB theo cloud provider).

**DoD P1:** tenant A không đọc được dữ liệu B; search/chat chỉ trên chunk được phép; có demo connector ít nhất một nguồn cloud.

---

## Phase 2 — AI Knowledge Workspace

- Tóm tắt email / thread (theo quyền hộp thư đã kết nối).
- Tóm tắt cuộc họp (transcript upload hoặc connector calendar/meeting — sau).
- Trợ lý onboarding, handbook, policy Q&A (citation bắt buộc).
- Collections, comment, không gian nhóm.
- **Knowledge graph (MVP):** thự thể (người, dự án, tài liệu, khách hàng) + quan hệ cơ bản; retrieval graph-aware.

**DoD P2:** ít nhất 2 workflow workspace (ví dụ: onboarding + policy) chạy end-to-end với audit.

---

## Phase 3 — AI Workflow OS

- Workflow engine: trigger, bước phê duyệt, retry, idempotency.
- Agent runtime: tool cho phép (read search, draft email nội bộ, tạo task) — **allowlist** theo role.
- Multi-agent đơn giản (orchestrator + worker) có giới hạn bước và budget token.

**DoD P3:** một workflow production (ví dụ: “transcript → action items → draft follow-up → chờ duyệt”) với logging đầy đủ.

---

## Phase Enterprise / Government hardening (song song từ P2)

- SSO: SAML 2.0 / OIDC (Entra ID, Keycloak, v.v.).
- SCIM provisioning (optional).
- Retention & legal hold; export audit.
- **BYOK** (key encryption), **BYOM** (model endpoint riêng / on-prem LLM).
- Air-gapped / private VPC deployment blueprint.
- Nhãn phân loại tài liệu (Confidential / Internal / Public) ảnh hưởng retrieval & export.

---

## Explicit non-goals (v1 spec)

- Không cam kết “100% không hallucination” — cam kết **citation + policy từ chối** và evaluation nội bộ.
- Không lưu mật khẩu connector dạng plain text — chỉ token/secret vault.

---

## Traceability

Mỗi feature lớn nên map sang `TASK_LIST.md` (ID ổn định) và policy bảo mật trong `MASTER_SPEC.md` §Security.
