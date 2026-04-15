---
title: Vertica Administration
layout: default
---

<div class="page-hero">
  <span class="chip">Administration</span>
  <h1>Vertica Administration</h1>
  <p>Vertica 데이터베이스의 핵심 객체(Table, Schema, User 등)를 관리하고 운영하는 방법을 상세히 설명합니다. 아래 목차를 통해 원하는 섹션으로 바로 이동할 수 있습니다.</p>
</div>

<div class="button-grid-section">
  <div class="button-grid">
    <a href="#table-projection" class="button secondary">Table · Projection</a>
    <a href="#schema" class="button secondary">Schema</a>
    <a href="#user" class="button secondary">User</a>
    <a href="#profile" class="button secondary">Profile</a>
    <a href="#resource-pool" class="button secondary">Resource Pool</a>
    <a href="#privilege" class="button secondary">Privilege</a>
  </div>
</div>

<div class="content-section" markdown="1">

<div id="table-projection" style="scroll-margin-top: 100px;"></div>

## Table · Projection

Vertica에서 가장 중요한 객체는 `Table`과 `Projection`입니다. Table은 데이터 구조를 정의하고 저장을 담당하며, Projection은 데이터를 빠르게 조회하도록 최적화하는 물리 계층입니다.

### Table 생성 및 관리

- `CREATE TABLE`으로 테이블을 생성합니다.
- 컬럼 타입과 `ENCODING`을 적절히 설계해야 합니다.
- `COPY`명령으로 데이터를 로드하며, `DELETE`, `UPDATE`, `MERGE` 등을 통해 데이터를 관리합니다.
- `EXPORT`를 사용해 데이터를 외부 파일로 내보낼 수 있습니다.

예시:

```sql
CREATE TABLE sales (
  sale_id INT,
  product_id INT,
  sale_date DATE,
  amount DECIMAL(18,2)
) SEGMENTED BY HASH(sale_id) ALL NODES;
```

### Projection의 역할

- Projection은 `Query Optimizer`가 사용할 물리적 뷰입니다.
- 기본적으로 Vertica는 자동 Projection을 생성하지만, 중요한 쿼리에는 `CREATE PROJECTION`으로 최적화된 projection을 구성할 수 있습니다.
- Projection은 `자주 사용하는 컬럼`, `정렬 키`, `조인 컬럼` 중심으로 설계합니다.

### Projection 최적화 포인트

- `ORDER BY`는 쿼리 수행 속도에 큰 영향을 줍니다.
- `PARTITION BY`는 큰 테이블 처리 성능을 개선합니다.
- 중복 제거 및 집계 쿼리를 위한 `segment by`, `local order by`를 적절히 사용합니다.

예시:

```sql
CREATE PROJECTION sales_projection (
  sale_id,
  product_id,
  sale_date,
  amount
)
AS
SELECT sale_id, product_id, sale_date, amount
FROM sales
ORDER BY sale_date, product_id;
```

### 운영 팁

- `SELECT * FROM projections WHERE anchor_table_name = 'sales';`로 테이블에 연결된 projection을 조회합니다.
- `SELECT REFRESH_PROJECTIONS()`로 projection 상태를 재생성/재정비할 수 있습니다.
- `ANALYZE_STATISTICS`로 통계 정보를 갱신하면 최적화에 도움이 됩니다.

<hr style="margin: 3rem 0;">
<div id="schema" style="scroll-margin-top: 100px;"></div>

## Schema

Vertica에서 `Schema`는 객체를 그룹화하고 접근 권한을 분리하는 네임스페이스 역할을 합니다. Schema를 잘 설계하면 운영과 보안 관리가 쉬워집니다.

### Schema 생성

- `CREATE SCHEMA`로 스키마를 생성합니다.
- 스키마는 테이블, 뷰, 프로젝션, 시퀀스 등의 객체를 논리적으로 구분합니다.
- 네임스페이스를 분리하면 동일 이름의 객체를 서로 다른 스키마에 생성할 수 있습니다.

예시:

```sql
CREATE SCHEMA analytics;
CREATE SCHEMA staging;
```

### Schema 관리

- 스키마 단위로 `GRANT`와 `REVOKE`를 적용할 수 있습니다.
- `SET SEARCH_PATH`로 기본 스키마를 지정해 쿼리 작성 편의성을 높입니다.
- 스키마 내 객체를 삭제할 때는 `DROP SCHEMA ... CASCADE`로 관련 객체를 함께 제거할 수 있습니다.

예시:

```sql
GRANT USAGE ON SCHEMA analytics TO analyst_role;
REVOKE CREATE ON SCHEMA analytics FROM public;
```

### 운영 포인트

- 개발/테스트/프로덕션 환경별 스키마를 분리합니다.
- 민감 데이터가 있는 테이블은 별도의 스키마로 분리하여 권한을 강화합니다.
- 스키마 이름과 역할 이름을 명확하게 정해 운영 혼선을 줄입니다.

<hr style="margin: 3rem 0;">
<div id="user" style="scroll-margin-top: 100px;"></div>

## User

<!-- 
  '02_3user.md' 파일이 제공되지 않아 해당 내용은 여기에 포함되지 않았습니다.
  'User' 관리에 대한 내용을 이 주석 아래에 추가해주세요.
-->

<hr style="margin: 3rem 0;">
<div id="profile" style="scroll-margin-top: 100px;"></div>

## Profile

Vertica `Profile`은 사용자 세션에 적용되는 실행 환경과 자원 제한을 정의합니다. 프로필을 통해 각 사용자의 쿼리 실행 조건을 제어할 수 있습니다.

### Profile 생성

- `CREATE PROFILE`로 프로필을 생성합니다.
- `COST_LIMIT`, `CONCURRENCY_LIMIT`, `RESOURCE_PORTION` 등의 속성을 설정할 수 있습니다.
- 프로필은 사용자 또는 역할에 할당됩니다.

예시:

```sql
CREATE PROFILE analyst_profile
  SET COST_LIMIT = 10000,
      CONCURRENCY_LIMIT = 10;
```

### Profile 할당

- `ALTER USER ... SET PROFILE`로 사용자에게 프로필을 할당합니다.
- `GRANT`와 함께 역할 기반 프로필 할당도 가능합니다.

예시:

```sql
ALTER USER analyst SET PROFILE analyst_profile;
```

### 운영 포인트

- 프로필로 리소스 사용을 제한하면 대형 쿼리로 인한 전체 성능 저하를 방지할 수 있습니다.
- 사용자 유형별로 서로 다른 프로필을 구성하여 개발/분석/운영 환경을 분리합니다.
- 프로필 설정 변경 시 테스트를 통해 안정성을 확인합니다.

<hr style="margin: 3rem 0;">
<div id="resource-pool" style="scroll-margin-top: 100px;"></div>

## Resource Pool

<!-- 
  '02_5resourcepool.md' 파일이 제공되지 않아 해당 내용은 여기에 포함되지 않았습니다.
  'Resource Pool' 관리에 대한 내용을 이 주석 아래에 추가해주세요.
-->

<hr style="margin: 3rem 0;">
<div id="privilege" style="scroll-margin-top: 100px;"></div>

## Privilege

<!-- 
  '02_6privilege.md' 파일이 제공되지 않아 해당 내용은 여기에 포함되지 않았습니다.
  'Privilege' 관리에 대한 내용을 이 주석 아래에 추가해주세요.
-->

</div>