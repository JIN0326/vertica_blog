---
title: 쿼리 튜닝 (Explain)
layout: default
---

<div class="page-hero">
  <span class="chip">CoreTech</span>
  <h1>쿼리 튜닝 (Explain)</h1>
  <p>EXPLAIN 명령어를 통해 쿼리 실행 계획을 분석하고, 성능 병목을 찾아 최적화하는 방법을 알아봅니다.</p>
</div>

<div class="content-section content-section-centered" markdown="1">
  ## EXPLAIN 이란?

  `EXPLAIN`은 Vertica의 쿼리 최적화기(Query Optimizer)가 특정 SQL 쿼리를 어떻게 실행할 것인지에 대한 계획(Execution Plan)을 보여주는 명령어입니다. 이 실행 계획을 분석하면 쿼리 성능에 영향을 미치는 요소를 파악하고 튜닝 방향을 결정할 수 있습니다.

  ```sql
  EXPLAIN SELECT ... FROM ... WHERE ...;
  ```

  ---

  ## 실행 계획 분석하기

  `EXPLAIN`의 결과는 쿼리가 실행되는 단계적인 경로(Path)를 보여줍니다. 각 경로는 어떤 연산(Operation)이 수행되는지, 어떤 프로젝션(Projection)이 사용되는지, 예상 비용(Cost)은 얼마인지 등의 정보를 포함합니다.

  ### 주요 확인 항목

  1.  **Access Path (데이터 접근 경로)**:
      - `STORAGE ACCESS for ...`: 어떤 프로젝션에서 데이터를 읽어오는지 보여줍니다. 쿼리에 최적화된 프로젝션을 사용하고 있는지 확인해야 합니다.

  2.  **Join Operations (조인 연산)**:
      - `Join (Hash)`: 해시 조인. 대용량 데이터 조인에 일반적으로 사용됩니다.
      - `Join (Merge)`: 머지 조인. 조인 키가 정렬된 경우 매우 효율적입니다.
      - 조인 순서와 방식이 효율적인지 확인합니다.

  3.  **Data Movement (데이터 이동)**:
      - `NETWORK`: 노드 간 데이터 이동(Resegment, Broadcast)이 발생하는지 보여줍니다. 불필요한 데이터 이동은 성능 저하의 주요 원인이므로 최소화해야 합니다.

  4.  **Cost (비용)**:
      - 실행 계획의 각 단계마다 예상 비용이 표시됩니다. 비용이 비정상적으로 높은 구간이 성능 병목 지점일 가능성이 높습니다.

  ---

  ## 튜닝 포인트

  - **프로젝션 최적화**: 쿼리의 `WHERE`, `GROUP BY`, `ORDER BY` 절에 사용되는 컬럼을 포함하는 최적의 프로젝션을 생성합니다.
  - **통계 정보 수집**: `ANALYZE_STATISTICS`를 실행하여 최적화기가 더 정확한 실행 계획을 세울 수 있도록 합니다.
  - **데이터 분산**: 조인 키를 기준으로 테이블을 분산(Segmentation)하여 네트워크를 통한 데이터 이동을 줄입니다.
</div>