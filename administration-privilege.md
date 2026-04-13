---
title: Resource Pool · Privilege
layout: default
---

# Privilege

Vertica 운영에서는 `Resource Pool`과 `Privilege`를 통해 리소스 사용량과 권한을 분리 관리합니다. 올바른 구성이 안정적인 분석 환경을 만드는 핵심입니다.

## Privilege 관리

- Vertica 권한 체계는 객체별 `GRANT`/`REVOKE` 기반입니다.
- `ALL PRIVILEGES`, `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `USAGE`, `CREATE` 등으로 세분화됩니다.
- 권한을 직접 사용자에게 부여하기보다 역할(Role)을 활용하는 것이 관리에 유리합니다.

예시:

```sql
GRANT USAGE ON SCHEMA analytics TO analyst_role;
GRANT SELECT ON analytics.sales TO analyst_role;
```

## 운영 포인트

- 리소스 풀과 프로필을 함께 사용하면 쿼리 성능과 안정성을 모두 관리할 수 있습니다.
- 최소 권한 원칙을 적용해 불필요한 권한 부여를 제한합니다.
- 권한 변경 작업은 문서화하고, 주기적으로 권한 검토를 수행합니다.
