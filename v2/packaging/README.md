# Packaging specification (v2) — expected repo layout

Tài liệu này mô tả **cấu trúc gợi ý** trong **repository sản phẩm** (không nhất thiết nằm trong `hydra-knowledge`). Agent codegen nên tạo tương đương khi bootstrap monorepo.

```
packaging/
  README.md                 ← Ma trận mode × CPU/RAM tối thiểu; lệnh quickstart
  compose/
    docker-compose.dev.yml
    docker-compose.release.yml   # profile all-in-one + distributed
  helm/
    hydra-knowledge/
      Chart.yaml
      values.yaml
      templates/
      README.md
  scripts/
    smoke.sh                  # optional: curl health + login stub
```

## Quickstart (mục tiêu sau implement)

- **All-in-one:** `docker compose -f packaging/compose/docker-compose.release.yml --profile all-in-one up -d`
- **K8s:** `helm upgrade --install hk ./packaging/helm/hydra-knowledge -f values.prod.yaml`

## Ghi chú

- Image tag: dùng **cùng semver** cho `web`, `api`, `worker` trừ khi ADR khác.
- Secrets: `ExternalSecrets` / Vault integration nằm ngoài spec tối thiểu nhưng Helm values phải có `existingSecret` hooks.

Tham chiếu: `../MASTER_SPEC.md` §13, `../ADR/0005-one-package-deployment-compose-helm.md`.
