---
title: "Vertica Explain: 쿼리 실행 계획 분석 및 성능 튜닝 - Vertica Blog"
layout: default
description: "Vertica의 EXPLAIN 및 PROFILE 명령어를 사용하여 쿼리 실행 계획을 분석하고 성능 병목을 찾는 방법을 알아봅니다. Cost, Rows, Access Path, Join Type 등 실행 계획의 핵심 요소를 해석하는 방법을 설명합니다."
keywords: "vertica, explain, profile, query tuning, performance, optimization, execution plan, optimizer, cost, access path, join"
---

<div id="explain" style="scroll-margin-top: 100px;"></div>

## Explain (쿼리 분석)

<div class="architecture-section">
  <p class="section-description"><code>EXPLAIN</code>은 Vertica 옵티마이저가 SQL 쿼리를 어떻게 분석하고 실행할 것인지에 대한 <strong>실행 계획(Execution Plan)</strong>을 보여줍니다. 쿼리 성능 튜닝 및 병목 구간 파악을 위한 가장 기초적이고 필수적인 도구입니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">실행 계획 확인 방법</h3>
    <div class="syntax-box">
      <strong>기본 구문:</strong>
      <pre><code>EXPLAIN [SELECT / UPDATE / DELETE 쿼리];
PROFILE [SELECT / UPDATE / DELETE 쿼리];</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> EXPLAIN vs PROFILE</dt>
      <dd class="feature-dd">
        <strong>EXPLAIN:</strong> 쿼리를 <em>실제로 실행하지 않고</em> 옵티마이저가 예측한 경로(Path), 비용(Cost), 예상 행 수(Rows) 등을 텍스트 트리 형태로 반환합니다.<br>
        <strong>PROFILE:</strong> 쿼리를 <em>실제로 실행</em>하면서, 계획의 각 단계별 실제 소요 시간, 메모리 사용량 등의 런타임 통계(Profile Data)를 수집하여 더 정확한 튜닝 지표를 제공합니다.
      </dd>
    </dl>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">실행 계획 분석 포인트</h3>
    <p class="section-description">EXPLAIN 결과를 읽을 때는 트리 구조의 <strong>안쪽(가장 들여쓰기가 많이 된 하위 노드)부터 바깥쪽으로, 아래에서 위로</strong> 해석합니다.</p>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> <strong>Cost (비용)</strong></dt>
      <dd class="feature-dd">옵티마이저가 해당 연산에 필요하다고 추정하는 자원의 양입니다. 튜닝 전후의 쿼리 효율성을 비교하는 상대적인 지표로 활용됩니다.</dd>
      <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> <strong>Rows (예상 건수)</strong></dt>
      <dd class="feature-dd">해당 단계에서 출력될 것으로 예상되는 데이터의 행 수입니다. (통계 정보가 최신화되지 않았다면 실제 건수와 크게 차이 날 수 있습니다.)</dd>
      <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> <strong>Access Path</strong></dt>
      <dd class="feature-dd">데이터를 읽는 방식입니다. Storage Access(물리 디스크 접근) 단계에서 조건절(Filter)이 효율적으로 푸시다운(Push-down) 되었는지 확인합니다.</dd>
      <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> <strong>Join Type</strong></dt>
      <dd class="feature-dd">Hash Join, Merge Join 등 조인 방식이 적절한지 파악합니다. 특히 대용량 조인 시 메모리가 부족해 디스크를 사용하는 현상(Spill to disk)이 발생할 우려가 있는지 체크합니다.</dd>
    </dl>
    <div class="syntax-box">
      <strong>실행 계획 출력 예시:</strong>
      <pre><code>EXPLAIN SELECT * FROM sales s JOIN product p ON s.product_id = p.product_id;

-- 결과 요약 트리 (하단부터 상단으로 해석)
Access Path: 
 +-JOIN HASH [Cost: 500, Rows: 10K] (PATH ID: 1)
 |  Join Cond: (s.product_id = p.product_id)
 | +-- Outer -> STORAGE ACCESS for s [Cost: 200, Rows: 10K] 
 | |      Projection: public.sales_b0
 | +-- Inner -> STORAGE ACCESS for p [Cost: 50, Rows: 1K] 
 | |      Projection: public.product_b0</code></pre>
    </div>
  </div>
</div>