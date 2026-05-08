# AI Knowledge OS — documentation v1

Thư mục này là **nguồn chân lý** cho product/engineering và cho **agent codegen** (ví dụ Composer-2).

## Thứ tự đọc (bắt buộc trước khi implement lớn)

1. **`Business_strategy.md`** — vision, phase, GTM, compliance & trust (bối cảnh kinh doanh).
2. **`MASTER_SPEC.md`** — **ràng buộc kỹ thuật bắt buộc** (tenant, security, RAG, API). Vi phạm = phải sửa code hoặc ghi ADR mới.
3. **`ARCHITECTURE.md`** — stack, bounded contexts, pipeline, deployment.
4. **`FEATURES.md`** — capability theo phase + DoD ngắn (không thay thế MASTER_SPEC).
5. **`TASK_LIST.md`** — backlog có **ID ổn định** (`P0.1`, `A1.1`, …) để commit/PR/agent.

## Architecture Decision Records (`ADR/`)

- **`ADR/0001-*.md`** — quy ước định dạng ADR.
- **`ADR/0002-*.md`** — BullMQ vs Temporal cho async / workflow.
- **`ADR/0003-*.md`** — Qdrant mặc định + pilot pgvector (cost-down).

Khi đổi quyết định đã ghi trong ADR: **không sửa nội dung cũ** (giữ lịch sử); thêm ADR mới trạng thái *Supersedes* nếu cần.

## Tài liệu gốc (legacy)

- `../ai_knowledge_os_master_plan_enterprise_2026.md` — bản tổng hợp cũ (nhiều section trong một file).
- `../FEATURES.md` — ghi chú thị trường / positioning.

Ưu tiên **`v1/*.md`** cho mọi thay đổi mới; legacy chỉ tham chiếu khi cần ngữ cảnh lịch sử.

## Gợi ý cho agent (Composer-2)

- Luôn kiểm tra **tenant isolation** và **permission-aware retrieval** khi chạm search/RAG/connector.
- Mỗi PR nên map tới ít nhất một mục **`TASK_LIST.md`** (giữ ID).
- Thay đổi stack hoặc bỏ ràng buộc trong `MASTER_SPEC.md` → **ADR mới** + cập nhật spec có phiên bản/ngày.
