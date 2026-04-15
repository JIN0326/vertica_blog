---
title: Resource Pool · Privilege
layout: default
---

# Resource Pool 

Vertica 운영에서는 `Resource Pool`과 `Privilege`를 통해 리소스 사용량과 권한을 분리 관리합니다. 올바른 구성이 안정적인 분석 환경을 만드는 핵심입니다.

## Resource Pool

- `CREATE RESOURCE POOL`로 리소스 풀을 생성합니다.
- `MAX_MEMORY_SIZE`, `MAX_CONCURRENCY`, `MAX_RUNNING` 같은 속성으로 쿼리 자원 할당을 제어합니다.
- 각 풀은 특정 유형의 작업 또는 사용자 그룹에 할당할 수 있습니다.

예시:

```sql
CREATE RESOURCE POOL analyst_pool
  MAX_MEMORY_SIZE '4GB'
  MAX_CONCURRENCY 10
  MAX_RUNNING 8;
```

## 운영 포인트

- 리소스 풀과 프로필을 함께 사용하면 쿼리 성능과 안정성을 모두 관리할 수 있습니다.
- 최소 권한 원칙을 적용해 불필요한 권한 부여를 제한합니다.
- 권한 변경 작업은 문서화하고, 주기적으로 권한 검토를 수행합니다.
