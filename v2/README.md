# AI Knowledge OS — documentation **v2**

**Kế thừa:** `../v1/` — v2 bổ sung hai hướng cải tiến chính:

1. **Đa kiểu xác thực (multi-auth)** — nhiều phương thức đăng nhập / IdP trên cùng nền tảng, cấu hình theo tenant.
2. **One-package deployment** — một “gói” triển khai (Compose / Helm) để **một lệnh** (hoặc gần một lệnh) chạy full stack có kiểm tra sức khỏe và migrate.

## Thứ tự đọc (trước khi implement lớn)

1. **`Business_strategy.md`**
2. **`MASTER_SPEC.md`** — ràng buộc bắt buộc (**v2** mở rộng §Identity & §Deployment packaging).
3. **`ARCHITECTURE.md`**
4. **`FEATURES.md`**
5. **`TASK_LIST.md`**

## ADR (`ADR/`)

- `0001` — quy ước ADR (thư mục **v2**).
- `0002` — BullMQ vs Temporal (kế thừa v1).
- `0003` — Qdrant vs pgvector pilot (kế thừa v1).
- **`0004`** — Pluggable identity & multi-auth.
- **`0005`** — One-package deployment (Compose + Helm).

## Packaging spec (triển khai)

- **`packaging/README.md`** — layout gợi ý + quickstart mục tiêu cho repo sản phẩm thực tế.

## Quan hệ với v1

- **v1** giữ làm baseline lịch sử; **mọi codegen mới ưu tiên v2** trừ khi dự án ghi rõ “freeze v1”.
- Thay đổi breaking so với v1 → ghi trong ADR 0004/0005 hoặc ADR mới *Supersedes*.

## Gợi ý cho agent (Composer-2)

- Mọi flow auth phải đi qua **abstraction Identity** (`MASTER_SPEC` §3); không hardcode một provider duy nhất.
- Triển khai mới phải có đường **`deploy/` hoặc `packaging/`** đồng bộ với `TASK_LIST` **P0.6–P0.9**.
