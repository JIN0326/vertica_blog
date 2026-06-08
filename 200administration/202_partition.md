---
title: "Vertica Partition: 파티션 프루닝, 계층적 파티셔닝 - Vertica Blog"
layout: default
description: "Vertica의 파티셔닝 기능을 알아봅니다. 파티션 프루닝을 통한 쿼리 성능 향상, 데이터 관리 용이성, 그리고 CALENDAR_HIERARCHY_DAY를 이용한 계층적 파티셔닝 방법을 설명합니다."
keywords: "vertica, partition, partitioning, partition pruning, hierarchical partitioning, calendar_hierarchy_day, drop_partitions"
canonical_url: "https://jin0326.github.io/vertica_blog/200administration/200administration#partition"
---

<div id="partition" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody" markdown="1">
## Partition

<div class="architecture-section">
  <p class="section-description">테이블 파티셔닝은 대용량 테이블을 특정 컬럼(예: 날짜)의 값을 기준으로 더 작고 관리하기 쉬운 단위인 <strong>파티션(Partition)</strong>으로 분할하는 기능입니다. 이를 통해 쿼리 성능을 최적화하고 데이터 관리를 용이하게 할 수 있습니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. 파티셔닝의 이점</h3>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> 쿼리 성능 향상 (Partition Pruning)</dt>
      <dd class="feature-dd">
        쿼리의 <code>WHERE</code> 조건절에 파티션 키가 포함되면, Vertica 옵티마이저는 불필요한 파티션을 스캔하지 않고 필요한 파티션의 데이터만 읽습니다. 이 <strong>파티션 프루닝(Partition Pruning)</strong> 기능은 I/O를 크게 줄여 응답 속도를 비약적으로 향상시킵니다.
      </dd>
      <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> 데이터 관리 용이성</dt>
      <dd class="feature-dd">
        오래된 데이터를 삭제할 때 대규모 <code>DELETE</code> 작업 대신 해당 파티션을 통째로 <code>DROP</code>하거나 <code>TRUNCATE</code>할 수 있어 매우 빠르고 효율적입니다. 또한, 파티션 단위로 데이터를 백업하거나 이동하는 작업도 용이합니다.
      </dd>
    </dl>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 파티션 생성 및 정의</h3>
    <p class="section-description">파티션은 테이블을 생성할 때 정의하거나, 이미 데이터가 있는 테이블에 나중에 추가할 수 있습니다.</p>
    
    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">신규 테이블 파티셔닝 (CREATE TABLE)</h4>
      <p class="section-description"><code>CREATE TABLE</code> 문에서 <code>PARTITION BY</code> 절을 사용하여 파티션 키를 지정합니다.</p>
      <div class="syntax-box">
        <strong>파티션 생성 예시:</strong>
        <pre><code>CREATE TABLE online_sales (
    order_id INT,
    order_date DATE NOT NULL,
    price DECIMAL(10, 2)
)
PARTITION BY EXTRACT(YEAR FROM order_date);</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">기존 테이블 파티셔닝 (ALTER TABLE)</h4>
      <p class="section-description">이미 데이터가 있는 테이블에 파티션을 적용하거나 변경할 때는 <code>ALTER TABLE</code> 문을 사용합니다. 이 작업은 테이블의 모든 데이터를 재구성하므로 리소스가 많이 소모될 수 있습니다.</p>
      <div class="syntax-box">
        <strong>기존 테이블에 파티션 적용 예시:</strong>
        <pre><code>-- 'online_sales' 테이블을 'order_date'의 연도별로 파티셔닝
ALTER TABLE online_sales PARTITION BY EXTRACT(YEAR FROM order_date) REORAGNIZE;</code></pre>
      </div>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. 파티션 그룹화 및 계층화</h3>
    <p class="section-description"><code>GROUP BY</code> 절을 활용하여 파티션을 더 유연하게 그룹화하거나 계층적으로 구성할 수 있습니다.</p>

    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">파티션 그룹화 (Partition Grouping)</h4>
      <p class="section-description"><code>CASE</code> 문과 같은 표현식을 사용하여 여러 파티션 키 값을 하나의 논리적인 그룹으로 묶을 수 있습니다. 예를 들어, 여러 지역을 하나의 권역으로 묶어 파티션을 구성할 수 있습니다.</p>
      <div class="syntax-box">
        <strong>파티션 그룹화 예시:</strong>
        <pre><code>CREATE TABLE store_sales (
    store_state VARCHAR(2),
    sale_amount DECIMAL(10,2)
)
PARTITION BY store_state
GROUP BY (CASE 
    WHEN store_state IN ('MA', 'NH', 'VT', 'ME', 'RI', 'CT') THEN 'New England'
    WHEN store_state IN ('NY', 'NJ', 'PA') THEN 'Mid-Atlantic'
    ELSE 'Other'
END);</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">계층적 파티셔닝 (Hierarchical Partitioning)</h4>
      <p class="section-description">여러 레벨로 파티션을 구성하여 데이터 조회 성능을 더욱 세밀하게 최적화할 수 있습니다. 특히 날짜 기반 데이터에서 연도, 월, 일 단위로 계층을 만드는 데 유용합니다. Vertica는 이를 위해 <code>CALENDAR_HIERARCHY_DAY</code> 함수 사용을 권장합니다.</p>
      <div class="syntax-box">
        <strong>계층적 파티션 생성 예시 (연/월 기준):</strong>
        <pre><code>CREATE TABLE sales_history (
    product_id INT,
    sale_timestamp TIMESTAMP NOT NULL,
    amount DECIMAL(18,2)
)
PARTITION BY sale_timestamp
GROUP BY CALENDAR_HIERARCHY_DAY(sale_timestamp, 2, 2);</code></pre>
      </div>
      <ul class="feature-list" style="margin-top: 1rem;">
        <li><span class="feature-list__icon">💡</span> <strong>Tip:</strong> <span><code>CALENDAR_HIERARCHY_DAY(date, N, M)</code>는 날짜 계층(연, 분기, 월, 일 등)에서 상위 N개와 하위 M개 레벨을 제외하고 그룹화하여 효율적인 계층 파티션을 생성합니다.</span></li>
      </ul>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">4. 파티션 관리</h3>
    <p class="section-description">생성된 파티션은 시스템 테이블을 통해 확인하거나, 특정 파티션을 삭제하는 등의 관리가 가능합니다.</p>
    <div class="syntax-box">
      <strong>파티션 관리 예시:</strong>
      <pre><code>-- 테이블의 파티션 키 목록 확인
SELECT GET_PARTITION_KEYS('online_sales');

-- 특정 파티션 삭제 (2022년 데이터)
SELECT DROP_PARTITIONS('online_sales', 2022, 2022);</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">5. 파티션과 세그먼테이션 비교 (Partitioning vs. Segmentation)</h3>
    <p class="section-description">파티셔닝과 세그먼테이션은 모두 대용량 데이터를 관리하는 기술이지만, 목적과 동작 방식에서 근본적인 차이가 있습니다. 두 기술을 함께 사용하면 쿼리 성능과 관리 효율성을 극대화할 수 있습니다.</p>
    <img src="/vertica_blog/assets/images/parti_segment.png" alt="Partitioning vs Segmentation" style="width: 100%; max-width: 800px; margin: 1.5rem auto; display: block; border: 1px solid #e0e0e0; border-radius: 8px;">
    <table class="info-table">
      <thead>
        <tr>
          <th>구분</th>
          <th>파티셔닝 (Partitioning)</th>
          <th>세그먼테이션 (Segmentation)</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><strong>목적</strong></td>
          <td>데이터 관리 및 쿼리 성능 최적화</td>
          <td>데이터 분산 및 병렬 처리</td>
        </tr>
        <tr>
          <td><strong>동작 방식</strong></td>
          <td>테이블 데이터를 논리적인 그룹으로 분할하여 <strong>노드 내에</strong> 저장</td>
          <td>테이블 데이터를 클러스터의 <strong>모든 노드에 걸쳐</strong> 분산</td>
        </tr>
        <tr>
          <td><strong>주요 키워드</strong></td>
          <td><code>PARTITION BY</code></td>
          <td><code>SEGMENTED BY</code>, <code>UNSEGMENTED</code></td>
        </tr>
        <tr>
          <td><strong>핵심 이점</strong></td>
          <td><strong>파티션 프루닝(Partition Pruning)</strong>을 통한 I/O 감소, 파티션 단위의 빠른 데이터 삭제(DROP)</td>
          <td><strong>분산 쿼리(Distributed Query)</strong> 성능 극대화, 고가용성(HA) 확보</td>
        </tr>
      </tbody>
    </table>
  </div>
</div>
</div>
</div>