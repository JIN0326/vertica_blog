---
title: "Vertica Statistics: 쿼리 옵티마이저를 위한 통계 관리 - Vertica Blog"
layout: default
description: "Vertica 쿼리 옵티마이저의 성능을 좌우하는 통계(Statistics) 정보 관리 방법을 알아봅니다. ANALYZE_STATISTICS를 이용한 통계 수집, 내보내기, 가져오기 방법을 설명합니다."
keywords: "vertica, statistics, analyze_statistics, query optimizer, execution plan, export_statistics"
canonical_url: "https://jin0326.github.io/vertica_blog/200administration/200administration#statistics"
---

<div id="statistics" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody">
## Statistics (통계)

<div class="architecture-section">
  <p class="section-description">Vertica의 <strong>쿼리 옵티마이저</strong>는 가장 효율적인 쿼리 실행 계획을 수립하기 위해 테이블의 데이터 분포, 카디널리티, 중복 값 등의 <strong>통계(Statistics)</strong> 정보를 사용합니다. 정확한 통계 정보는 쿼리 성능에 직접적인 영향을 미치므로, 주기적인 관리가 매우 중요합니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. 통계 수집 (ANALYZE_STATISTICS)</h3>
    <p class="section-description"><code>ANALYZE_STATISTICS</code> 함수를 사용하여 테이블 또는 특정 컬럼에 대한 통계를 수집합니다. 통계 정보가 없거나 오래된 경우, 옵티마이저는 비효율적인 실행 계획을 생성할 수 있습니다.</p>
    <div class="syntax-box">
      <strong>통계 수집 예시:</strong>
      <pre><code>-- 'sales' 테이블의 모든 컬럼에 대한 통계 수집
SELECT ANALYZE_STATISTICS('public.sales');

-- 특정 컬럼에 대해서만 통계 수집
SELECT ANALYZE_STATISTICS('public.sales', 'product_id, sale_date');</code></pre>
    </div>
    <ul class="feature-list" style="margin-top: 1rem;">
      <li><span class="feature-list__icon">💡</span> <strong>언제 수집해야 하나요?</strong>
        <ul>
          <li>테이블 생성 및 초기 데이터 적재 후</li>
          <li>대규모 데이터 변경(INSERT, UPDATE, DELETE) 작업 후</li>
          <li>쿼리 성능이 예상보다 저하될 때</li>
        </ul>
      </li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 파티션 통계 수집 (ANALYZE_STATISTICS_PARTITION)</h3>
    <p class="section-description">대용량 파티션 테이블의 경우 전체 테이블에 대한 통계를 수집하는 것은 많은 시간과 리소스를 소모할 수 있습니다. <code>ANALYZE_STATISTICS_PARTITION</code> 함수를 사용하면 특정 파티션 또는 파티션 범위에 대해서만 통계를 효율적으로 수집할 수 있습니다. 이는 새로운 데이터가 최신 파티션에만 추가되는 시계열 데이터에 특히 유용합니다. 더 자세한 정보는 <a href="https://docs.vertica.com/26.1.x/en/sql-reference/functions/performance-analysis-functions/statistics-management-functions/analyze-statistics-partition/" target="_blank">공식 문서</a>에서 확인할 수 있습니다.</p>
    <div class="syntax-box">
      <strong>파티션 통계 수집 예시:</strong>
      <pre><code>-- 'sales' 테이블의 2024년 7월 파티션에 대한 통계만 수집
SELECT ANALYZE_STATISTICS_PARTITION('public.sales', '2024-07-01', '2024-07-31');

-- 특정 파티션의 특정 컬럼에 대한 통계 수집
SELECT ANALYZE_STATISTICS_PARTITION('public.sales', '2024-07-01', '2024-07-31', 'product_id');</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. 통계 정보 관리</h3>
    <p class="section-description">수집된 통계 정보는 시스템 테이블을 통해 확인하거나, 다른 환경으로 내보내고 가져올 수 있습니다.</p>
    <div class="syntax-box">
      <strong>통계 관리 함수 예시:</strong>
      <pre><code>-- 통계 정보 삭제
SELECT DROP_STATISTICS('public.sales');

-- 통계 정보를 파일로 내보내기
SELECT EXPORT_STATISTICS('/tmp/sales_stats.json', 'public.sales');

-- 파일에서 통계 정보 가져오기
SELECT IMPORT_STATISTICS('/tmp/sales_stats.json');</code></pre>
    </div>
    <p class="section-description" style="margin-top: 1.5rem;"><code>PROJECTION_COLUMNS</code> 시스템 테이블을 조회하여 각 컬럼의 통계 정보가 최신 상태인지(<code>statistics_up_to_date</code>) 확인할 수 있습니다.</p>
    <div class="syntax-box">
      <strong>통계 상태 확인 쿼리:</strong>
      <pre><code>SELECT
    projection_schema,
    projection_name,
    column_name,
    statistics_up_to_date
FROM v_catalog.projection_columns
WHERE projection_schema = 'public' AND projection_name LIKE 'sales%';</code></pre>
    </div>
  </div>
</div>
</div>
</div>