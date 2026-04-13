---
title: Schema
layout: default
---

# Schema

Vertica에서 `Schema`는 객체를 그룹화하고 접근 권한을 분리하는 네임스페이스 역할을 합니다. Schema를 잘 설계하면 운영과 보안 관리가 쉬워집니다.

## Schema 생성

- `CREATE SCHEMA`로 스키마를 생성합니다.
- 스키마는 테이블, 뷰, 프로젝션, 시퀀스 등의 객체를 논리적으로 구분합니다.
- 네임스페이스를 분리하면 동일 이름의 객체를 서로 다른 스키마에 생성할 수 있습니다.

예시:

```sql
CREATE SCHEMA analytics;
CREATE SCHEMA staging;
```

## Schema 관리

- 스키마 단위로 `GRANT`와 `REVOKE`를 적용할 수 있습니다.
- `SET SEARCH_PATH`로 기본 스키마를 지정해 쿼리 작성 편의성을 높입니다.
- 스키마 내 객체를 삭제할 때는 `DROP SCHEMA ... CASCADE`로 관련 객체를 함께 제거할 수 있습니다.

예시:

```sql
GRANT USAGE ON SCHEMA analytics TO analyst_role;
REVOKE CREATE ON SCHEMA analytics FROM public;
```

## 운영 포인트

- 개발/테스트/프로덕션 환경별 스키마를 분리합니다.
- 민감 데이터가 있는 테이블은 별도의 스키마로 분리하여 권한을 강화합니다.
- 스키마 이름과 역할 이름을 명확하게 정해 운영 혼선을 줄입니다.
