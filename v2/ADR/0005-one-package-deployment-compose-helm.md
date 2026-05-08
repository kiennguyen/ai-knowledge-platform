# ADR 0005 — One-package deployment: Compose all-in-one + Helm umbrella

- **Status:** Accepted  
- **Date:** 2026-05-06  
- **Deciders:** Architecture / DevOps  

## Context

- POC và SMB cần **triển khai nhanh** không đủ DevOps để lắp từng service.
- Enterprise / đối tác cần **K8s** có upgrade path, không chỉ Compose.
- “One-package” **không** có nghĩa một binary duy nhất — có nghĩa **một bộ artifact có phiên bản** + **tài liệu một chỗ** + **lệnh chuẩn**.

## Decision

1. **Docker Compose** là cổng vào chính:
   - **Profile `all-in-one`:** đủ component chạy MVP trên một host (tài nguyên tối thiểu ghi trong `packaging/README.md`).
   - **Profile `distributed`:** tách service cho staging / scale nhỏ.
2. **Helm umbrella chart** cho K8s:
   - Values cho image tag (mono-version hoặc matrix có bảng tương thích).
   - **Post-install/upgrade hook** chạy DB migration.
3. **Health & migrate:**
   - API không nhận traffic application cho đến khi migrate xong (pattern entrypoint hoặc init job).
   - `/health` trả version để hỗ trợ support.
4. **Airgap:** tài liệu riêng (ER11.6) — mirror registry + offline values; không ép một công cụ installer độc quyền OS.

## Consequences

- **Positive:** time-to-POC giảm; SI/reseller dễ nhân bản.
- **Negative:** phải duy trì hai đường (Compose + Helm) — CI build nhiều image.
- **Risks:** khách chạy `all-in-one` cho prod CQ mà không hardening — **mitigation:** README cảnh báo + profile prod K8s khuyến nghị.

## Links

- `MASTER_SPEC.md` §13  
- `ARCHITECTURE.md` §8  
- `TASK_LIST.md` P0.6–P0.10  
