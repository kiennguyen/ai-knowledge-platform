# ADR 0001 — Record architecture decisions

- **Status:** Accepted  
- **Date:** 2026-05-06  
- **Deciders:** Product / Architecture  

## Context

Cần nơi lưu **quyết định kiến trúc** có thể thay đổi theo thời gian, tách khỏi `MASTER_SPEC.md` (ràng buộc hiện tại) và khỏi code.

## Decision

- Dùng thư mục **`v1/ADR/`** với file đánh số `NNNN-short-title.md`.
- Mỗi ADR gồm: Context, Decision, Consequences (positive / negative / risks).
- ADR đã publish **không xóa**; khi đảo ngược quyết định, thêm ADR mới ghi *Supersedes ADR NNNN*.

## Consequences

- **Positive:** Lịch sử rõ; onboarding và agent hiểu *vì sao* chọn giải pháp.
- **Negative:** Phải duy trì kỷ luật ghi ADR khi đổi hướng lớn.
- **Risk:** ADR lệch với code — giảm bằng review định kỳ và link từ `TASK_LIST.md` khi cần.
