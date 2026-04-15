---
title: Profile
layout: default
---

# Profile

Vertica `Profile`은 사용자 세션에 적용되는 실행 환경과 자원 제한을 정의합니다. 프로필을 통해 각 사용자의 쿼리 실행 조건을 제어할 수 있습니다.

## Profile 생성

- `CREATE PROFILE`로 프로필을 생성합니다.
- `COST_LIMIT`, `CONCURRENCY_LIMIT`, `RESOURCE_PORTION` 등의 속성을 설정할 수 있습니다.
- 프로필은 사용자 또는 역할에 할당됩니다.

예시:

```sql
CREATE PROFILE analyst_profile
  SET COST_LIMIT = 10000,
      CONCURRENCY_LIMIT = 10;
```

## Profile 할당

- `ALTER USER ... SET PROFILE`로 사용자에게 프로필을 할당합니다.
- `GRANT`와 함께 역할 기반 프로필 할당도 가능합니다.

예시:

```sql
ALTER USER analyst SET PROFILE analyst_profile;
```

## 운영 포인트

- 프로필로 리소스 사용을 제한하면 대형 쿼리로 인한 전체 성능 저하를 방지할 수 있습니다.
- 사용자 유형별로 서로 다른 프로필을 구성하여 개발/분석/운영 환경을 분리합니다.
- 프로필 설정 변경 시 테스트를 통해 안정성을 확인합니다.
