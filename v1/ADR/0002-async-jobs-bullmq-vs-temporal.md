# ADR 0002 — Async jobs: BullMQ first, path to Temporal for long workflows

- **Status:** Accepted  
- **Date:** 2026-05-06  
- **Deciders:** Architecture  

## Context

Nền tảng cần:

- Ingestion, embedding batch, connector sync, notification — **nhiều job ngắn**, retry, DLQ.
- Tương lai: workflow **dài**, **human-in-the-loop**, compensation (hủy bước), timer — đặc biệt Phase 9–10 (workflow OS / agent có phê duyệt).

Hai họ phổ biến: **Redis + BullMQ** vs **Temporal** (self-host hoặc cloud).

## Decision

1. **Phase 0–2 (MVP):** Dùng **BullMQ** trên Redis cho mọi worker async (ingestion, embed, sync).  
   - Lý do: đội nhỏ vận hành đơn giản, chi phí hạ tầng thấp, đủ cho job phút–giờ.

2. **Phase 3+ (khi có workflow phức tạp):** Đánh giá **Temporal** (hoặc tương đương có state machine + saga) cho:
   - chuỗi bước có **chờ phê duyệt**,
   - timeout dài ngày,
   - replay / versioning workflow.

3. **Không** thay BullMQ toàn bộ ngay: giữ BullMQ cho job “fire-and-forget”; Temporal cho **orchestration** có trạng thái bền và UI/debug workflow.

## Consequences

- **Positive:** Khởi động nhanh, chi phí Redis đã có sẵn; tránh over-engineer P1.
- **Negative:** Hai hệ thống queue/workflow cần guideline “cái gì đặt đâu”.
- **Risks:** Logic phức tạp “nhồi” vào BullMQ → khó maintain; **mitigation:** khi có ≥2 workflow cần chờ người + nhánh, spike Temporal và migrate orchestration.

## Links

- `ARCHITECTURE.md` §2 (stack), §4 ingestion.  
- `TASK_LIST.md` PHASE 9 (workflow).
