# ADR 0003 — Vector store: Qdrant default, optional pgvector pilot for cost-sensitive tiers

- **Status:** Accepted  
- **Date:** 2026-05-06  
- **Deciders:** Architecture  

## Context

Cần vector search cho RAG với:

- filter theo **tenant / workspace / ACL** (payload),
- scale ingestion độc lập OLTP,
- tối ưu chi phí cho **tenant nhỏ** hoặc **dev/staging**.

**Qdrant** và **pgvector** (PostgreSQL) đều hợp lệ; mỗi cái có trade-off.

## Decision

1. **Production default:** **Qdrant** làm vector DB chính.  
   - Payload schema chuẩn theo `MASTER_SPEC.md` §4 (Qdrant).  
   - Scale ingestion và tunning recall/latency không ép chung một Postgres OLTP.

2. **Pilot / tier cost-down:** Cho phép **pgvector** trong PostgreSQL cho:
   - môi trường dev đơn giản,
   - hoặc tier “SME nhỏ” với giới hạn rõ (số vector, QPS),
   - **chỉ khi** abstraction embedding/index cho phép swap backend (cùng contract `upsert` / `search`).

3. **Không** yêu cầu hai backend đồng thời cho một workspace trong P1 — một workspace gắn một vector backend đã chọn (config).

## Consequences

- **Positive:** Qdrant phù hợp filter phức tạp và tách tải; pgvector giảm component count khi cần.
- **Negative:** Hai code path cần test; abstraction phải chặt để không leak Qdrant-only API vào domain.
- **Risks:** pgvector trên Postgres lớn ảnh hưởng OLTP — **mitigation:** giới hạn tier, monitor bảng index, hoặc RDS read replica chỉ cho search (tùy cloud).

## Links

- `ARCHITECTURE.md` §2 (stack), §5 ingestion.  
- `MASTER_SPEC.md` §4 (Qdrant payload).  
- `TASK_LIST.md` PHASE 4 (E4.x).
