---
title: Table · Projection
layout: default
---

<div class="page-layout">
  <div class="content-section" markdown="1">

# Table · Projection

Vertica에서 가장 중요한 객체는 `Table`과 `Projection`입니다. Table은 데이터 구조를 정의하고 저장을 담당하며, Projection은 데이터를 빠르게 조회하도록 최적화하는 물리 계층입니다.

## Table 생성 및 관리

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

## Projection의 역할

- Projection은 `Query Optimizer`가 사용할 물리적 뷰입니다.
- 기본적으로 Vertica는 자동 Projection을 생성하지만, 중요한 쿼리에는 `CREATE PROJECTION`으로 최적화된 projection을 구성할 수 있습니다.
- Projection은 `자주 사용하는 컬럼`, `정렬 키`, `조인 컬럼` 중심으로 설계합니다.

## Projection 최적화 포인트

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

## 운영 팁

- `SELECT * FROM projections WHERE anchor_table_name = 'sales';`로 테이블에 연결된 projection을 조회합니다.
- `SELECT REFRESH_PROJECTIONS()`로 projection 상태를 재생성/재정비할 수 있습니다.
- `ANALYZE_STATISTICS`로 통계 정보를 갱신하면 최적화에 도움이 됩니다.

  </div>

  {% include administration_sidebar.html %}
</div>
