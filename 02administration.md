---
title: Vertica 운영 및 관리 가이드 - Vertica Blog
layout: default
description: "Vertica 데이터베이스 운영을 위한 관리 가이드입니다. 테이블, 스키마, 사용자 관리부터 백업/복구, 클러스터 운영, 리소스 풀 설정까지 다룹니다."
---
<div class="page-hero">
  <span class="chip">Administration</span>
  <h1>Vertica Administration</h1>
  <p>Vertica 데이터베이스의 핵심 객체(Table, Schema, User) 관리부터 리소스 풀, 권한, 백업/복구, 클러스터 운영까지 전반적인 관리 방법을 알아봅니다.</p>
</div>

<div class="page-layout">
  <div class="content-section" markdown="1">

<div id="table-projection" style="scroll-margin-top: 100px;"></div>

## Table · Projection

<div class="architecture-section">
  <p class="section-description">Vertica 아키텍처의 핵심은 **논리적 모델(Table)**과 **물리적 저장(Projection)**의 완벽한 분리에 있습니다. 데이터베이스에 질의를 던질 때, Vertica의 옵티마이저는 논리적 Table에 연결된 여러 물리적 Projection 중 가장 응답 속도가 빠른 것을 스스로 선택하여 쿼리를 수행합니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">Table의 종류 (Types of Tables)</h3>
    <p class="section-description" style="margin-bottom: 1.5rem;">Vertica는 용도에 따라 세 가지 종류의 테이블을 지원합니다: 일반 테이블, 임시 테이블, 외부 테이블.</p>

  <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">1. Native Table (일반 테이블)</h4>
      <p class="section-description">데이터가 Vertica 클러스터 내에 물리적으로 저장되는 가장 일반적인 형태의 테이블입니다. 대부분의 상용 DB 데이터 타입과 호환되며, Projection을 통해 데이터 분산, 정렬, 압축을 최적화할 수 있습니다.</p>
      <dl class="feature-dl">
        <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 지원 데이터 타입</dt>
        <dd class="feature-dd">
          <strong>Numeric Types:</strong> <code>INTEGER</code> (<code>INT</code>, <code>BIGINT</code>, <code>SMALLINT</code>), <code>NUMERIC</code> (<code>DECIMAL</code>), <code>FLOAT</code> (<code>DOUBLE PRECISION</code>)<br>
          <strong>Character Types:</strong> <code>CHAR</code>, <code>VARCHAR</code> (최대 65,000 바이트), <code>LONG VARCHAR</code> (최대 32MB)<br>
          <strong>Binary Types:</strong> <code>BINARY</code>, <code>VARBINARY</code> (최대 65,000 바이트), <code>LONG VARBINARY</code> (최대 32MB)<br>
          <strong>Date/Time Types:</strong> <code>DATE</code>, <code>TIME</code>, <code>TIMESTAMP</code> (<code>DATETIME</code>), <code>TIMETZ</code>, <code>TIMESTAMPTZ</code>, <code>INTERVAL</code><br>
          <strong>Boolean & UUID:</strong> <code>BOOLEAN</code> (true/false), <code>UUID</code><br>
          <strong>Geospatial Types:</strong> <code>GEOMETRY</code>, <code>GEOGRAPHY</code><br>
          <strong>Complex Types:</strong> <code>ARRAY[...]</code>, <code>ROW(...)</code>
        </dd>
      </dl>
      <div class="syntax-box">
        <strong>테이블 생성 예시 (분산 및 파티션 설정 포함):</strong>
        <pre><code>CREATE TABLE sales (
    sale_id INT NOT NULL,
    product_id INT,
    sale_date DATE,
    amount DECIMAL(18,2)
)
-- 정렬키 설정
ORDER BY sale_id, product_id, sale_date
-- 분산키 설정
SEGMENTED BY HASH(sale_id) ALL NODES
-- 파티션 설정
PARTITION BY sale_date::DATE 
GROUP BY CALENDAR_HIERARCHY_DAY(sale_date::DATE, 2, 2);</code></pre>
      </div>
    </div>

  <div class="step-section">
      <h4 class="step-title">2. Temporary Table (임시 테이블)</h4>
      <p class="section-description">세션 기간 동안에만 존재하는 테이블로, 복잡한 분석 과정에서 중간 결과 데이터를 임시로 저장하는 용도로 사용됩니다. 데이터는 세션이 종료되면 자동으로 사라지며, 다른 세션에서는 보이지 않아 충돌을 방지합니다.</p>
      <div class="syntax-box">
        <strong>임시 테이블 생성 및 사용 예시:</strong>
        <pre><code>-- ON COMMIT PRESERVE ROWS: 커밋 후에도 세션이 끝날 때까지 데이터 유지 (기본값)
CREATE TEMPORARY TABLE temp_sales (
    sale_id INT,
    amount DECIMAL(18,2)
) ON COMMIT PRESERVE ROWS;

-- 데이터 삽입
INSERT INTO temp_sales SELECT sale_id, amount FROM sales WHERE sale_date > '2024-01-01';

-- 임시 테이블 조회
SELECT * FROM temp_sales;</code></pre>
      </div>
    </div>

  <div class="step-section">
      <h4 class="step-title">3. External Table (외부 테이블)</h4>
      <p class="section-description">S3, HDFS와 같은 외부 스토리지에 저장된 데이터를 Vertica에 직접 로드하지 않고 참조하는 테이블입니다. 데이터 레이크에 있는 Parquet, ORC, CSV 등의 파일을 즉시 SQL로 분석할 수 있게 해줍니다.</p>
      <div class="syntax-box">
        <strong>외부 테이블 생성 및 사용 예시:</strong>
        <pre><code>-- 외부 파일의 구조를 기반으로 외부 테이블 자동 생성
CREATE EXTERNAL TABLE ext_logs (
    log_timestamp TIMESTAMP,
    ip_address VARCHAR,
    message VARCHAR
) AS COPY FROM 's3://my-log-bucket/logs/2024/07/log-*.gz'
PARSER FDELIMITEDPARSER(delimiter=',', record_terminator='\n')
GZIP;

-- 외부 테이블 조회
SELECT ip_address, count(*) 
FROM ext_logs 
WHERE message LIKE '%ERROR%'
GROUP BY 1;</code></pre>
      </div>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">다른 테이블을 이용한 테이블 생성 (CTAS & LIKE)</h3>
    <p class="section-description">기존 테이블의 구조나 쿼리 결과를 바탕으로 새 테이블을 빠르고 쉽게 생성할 수 있습니다.</p>

    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">1. CREATE TABLE ... AS SELECT ... (CTAS)</h4>
      <p class="section-description">쿼리(<code>SELECT</code>)의 실행 결과를 바탕으로 데이터와 스키마를 모두 포함하는 새 테이블을 생성합니다. 복잡한 조인이나 집계 결과를 물리적인 테이블로 만들어 분석 성능을 높일 때 유용합니다.</p>
      <div class="syntax-box">
        <strong>CTAS 구문 예시:</strong>
        <pre><code>-- 2024년 월별 매출 집계 테이블 생성
CREATE TABLE monthly_sales_2024 AS
SELECT
    DATE_TRUNC('month', sale_date) AS sales_month,
    product_id,
    SUM(amount) AS total_amount,
    COUNT(*) AS transaction_count
FROM sales
WHERE sale_date >= '2024-01-01' AND sale_date < '2025-01-01'
GROUP BY 1, 2;</code></pre>
      </div>
      <ul class="feature-list" style="margin-top: 1rem;">
        <li><span class="feature-list__icon">💡</span> <strong>Tip:</strong> <span>CTAS로 생성된 테이블은 원본 테이블의 제약 조건(Primary Key 등)이나 기본값 설정을 상속하지 않으므로, 필요 시 <code>ALTER TABLE</code>로 추가해야 합니다.</span></li>
      </ul>
    </div>

    <div class="step-section">
      <h4 class="step-title">2. CREATE TABLE ... LIKE ...</h4>
      <p class="section-description">데이터는 제외하고 원본 테이블의 스키마 구조(컬럼명, 데이터 타입, 제약 조건 등)만 그대로 복제하여 비어 있는 새 테이블을 생성합니다. 동일한 구조의 다른 테이블을 만들 때 유용합니다.</p>
      <div class="syntax-box">
        <strong>LIKE 구문 예시:</strong>
        <pre><code>-- 'sales' 테이블과 동일한 구조를 가진 'sales_archive' 테이블 생성
CREATE TABLE sales_archive LIKE sales;

-- 특정 컬럼의 속성만 복사 (예: 기본값, 제약조건)
CREATE TABLE sales_archive_light LIKE sales INCLUDING COLUMN DEFAULTS;</code></pre>
      </div>
       <dl class="feature-dl" style="margin-top: 1rem;">
        <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 복제 옵션</dt>
        <dd class="feature-dd">
          <strong>INCLUDING PROJECTIONS:</strong> 원본 테이블의 프로젝션까지 함께 복제합니다.<br>
          <strong>INCLUDING CONSTRAINTS:</strong> 제약 조건을 복제합니다.<br>
          <strong>INCLUDING COLUMN DEFAULTS:</strong> 컬럼의 기본값 설정을 복제합니다.
        </dd>
      </dl>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">Projection (물리적 저장)</h3>
    <p class="section-description">실제 테이블 데이터가 분산 및 압축되어 디스크에 저장되는 오브젝트입니다. 테이블에 데이터가 처음 저장될 때 자동으로 생성되거나, 명시적으로 생성할 수 있습니다.</p>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">1</span> 컬럼 인코딩 및 압축 (Encoding)</dt>
      <dd class="feature-dd">RLE, DELTAVAL 등 데이터 성격에 맞는 압축 방식을 지정하여 I/O를 최소화합니다. 날짜나 반복되는 값이 많은 컬럼은 RLE 방식이 성능에 유리합니다.</dd>
      <dt class="feature-dt"><span class="feature-dt__icon">2</span> 컬럼 정렬 저장 순서 (Order by)</dt>
      <dd class="feature-dd">물리적 정렬을 통해 조회 및 조인 속도를 비약적으로 향상시킵니다. Cardinality가 낮은 컬럼을 우선 배치하는 것이 효율적입니다.</dd>
      <dt class="feature-dt"><span class="feature-dt__icon">3</span> 데이터 분산 정책 (Segmentation)</dt>
      <dd class="feature-dd">해시 분산(Segmented) 또는 전체 복제(Unsegmented)를 결정합니다.</dd>
    </dl>
    <div class="syntax-box">
      <strong>프로젝션 명시적 생성 예시:</strong>
      <pre><code>CREATE PROJECTION sales_optimized_proj (
    sale_id ENCODING COMMONDELTA_COMP,
    product_id,
    sale_date ENCODING RLE, -- 조건 조회 빈도가 높은 컬럼 RLE 권장 
    amount
)
AS SELECT sale_id, product_id, sale_date, amount FROM sales
ORDER BY sale_date, product_id -- 정렬키 정의
SEGMENTED BY HASH(sale_id) ALL NODES; -- 분산키 정의</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">Projection의 4가지 종류</h3>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">1</span> Super 프로젝션</dt>
      <dd class="feature-dd">테이블의 모든 컬럼을 포함하는 기본 프로젝션입니다. 새로운 속성의 Super 프로젝션 생성 및 REFRESH 후 기존 프로젝션 삭제가 가능합니다.</dd>
      <dt class="feature-dt"><span class="feature-dt__icon">2</span> Aggregate 프로젝션</dt>
      <dd class="feature-dd">집계 함수(SUM, COUNT 등)가 포함되어 있어, 실시간 대시보드나 요약 정보 조회 시 미리 계산된 결과를 반환하여 응답 속도를 극대화합니다.</dd>
      <dt class="feature-dt"><span class="feature-dt__icon">3</span> Query-specific 프로젝션</dt>
      <dd class="feature-dd">특정 고부하 쿼리 튜닝을 위해 명시적으로 추가된 맞춤형 프로젝션입니다. 분석에 필요한 일부 컬럼만으로도 생성할 수 있습니다.</dd>
      <dt class="feature-dt"><span class="feature-dt__icon">4</span> Buddy 프로젝션</dt>
      <dd class="feature-dd">HA(고가용성) 구성을 위해 인접 노드에 복제되는 이중화 프로젝션입니다. 특정 노드 장애 시 Buddy가 서비스를 대신 수행하여 고가용성을 보장합니다.</dd>
    </dl>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">Projection 데이터 분산</h3>
    <div class="projection-distribution-grid">
      <div class="architecture-subsection">
        <h4 class="section-subtitle">1. Segmentation (해시 분산)</h4>
        <ul class="feature-list">
          <li><span class="feature-list__icon">🔹</span> <span>Segment key 해시값 기반 분산 저장</span></li>
          <li><span class="feature-list__icon">🔹</span> <span>동일 Segment key 간 조인(Join) 성능 우수</span></li>
        </ul>
        <div class="image-box-styled">
          <img src="{{ '/assets/images/proj_segmentation.png' | relative_url }}" alt="Segmentation (해시 분산) 다이어그램">
        </div>
      </div>
      <div class="architecture-subsection">
        <h4 class="section-subtitle">2. Replication (복제)</h4>
        <ul class="feature-list">
          <li><span class="feature-list__icon">🔹</span> <span>소규모 코드성 테이블을 모든 노드에 복제하여 조인 성능 향상</span></li>
          <li><span class="feature-list__icon">🔹</span> <span>노드 장애 시에도 즉각적인 고가용성 제공</span></li>
        </ul>
        <div class="image-box-styled">
          <img src="{{ '/assets/images/proj_replication.png' | relative_url }}" alt="Replication (복제) 다이어그램">
        </div>
      </div>
      <div class="architecture-subsection">
        <h4 class="section-subtitle">3. 고가용성을 위한 데이터 이중화 (Buddy)</h4>
        <ul class="feature-list">
          <li><span class="feature-list__icon">🔹</span> <span>노드 장애에 대비해 다른 노드에 복제본 자동 생성 및 관리</span></li>
          <li><span class="feature-list__icon">🔹</span> <span>장애 복구 후 자동으로 데이터 동기화(Recovery) 수행</span></li>
        </ul>
        <div class="image-box-styled">
          <img src="{{ '/assets/images/proj_buddy.png' | relative_url }}" alt="Buddy 프로젝션을 이용한 이중화 다이어그램">
        </div>
      </div>
      <div class="architecture-subsection">
        <h4 class="section-subtitle">4. 특정 노드에 프로젝션 생성</h4>
        <ul class="feature-list">
          <li><span class="feature-list__icon">🔹</span> <span>네트워크 부하 최소화를 위해 입출력이 필요한 특정 노드에만 저장</span></li>
        </ul>
        <div class="image-box-styled">
          <img src="{{ '/assets/images/proj_specific_node.png' | relative_url }}" alt="특정 노드에 프로젝션 생성 다이어그램">
        </div>
      </div>
    </div>
  </div>
</div>

<hr style="margin: 3rem 0;">
<div id="partition" style="scroll-margin-top: 100px;"></div>

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
ALTER TABLE online_sales PARTITION BY EXTRACT(YEAR FROM order_date);</code></pre>
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
SELECT DROP_PARTITIONS('online_sales', 2022, 2022);

-- 테이블의 모든 파티션 삭제
SELECT TRUNCATE_PARTITIONS('online_sales', 0, 9999);</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">5. 파티션과 세그먼테이션 비교 (Partitioning vs. Segmentation)</h3>
    <p class="section-description">파티셔닝과 세그먼테이션은 모두 대용량 데이터를 관리하는 기술이지만, 목적과 동작 방식에서 근본적인 차이가 있습니다. 두 기술을 함께 사용하면 쿼리 성능과 관리 효율성을 극대화할 수 있습니다.</p>
    <div class="image-box-styled">
      <img src="{{ '/assets/images/parti_segment.png' | relative_url }}" alt="Partitioning vs Segmentation">
    </div>
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

<hr style="margin: 3rem 0;">
<div id="schema" style="scroll-margin-top: 100px;"></div>

## Schema

<div class="architecture-section">
  <p class="section-description">Vertica에서 **Schema(스키마)**는 객체들의 논리적인 그룹이며 별도의 물리적인 특징을 가지지 않습니다. 스키마를 통해 객체 접근 권한을 분리하고 네임스페이스를 관리함으로써 운영 효율성을 높일 수 있습니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. 기본 스키마 (Default Schemas)</h3>
    <p class="section-description">데이터베이스 생성 시 시스템 관리를 위해 자동으로 생성되는 기본 스키마들입니다.</p>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>v_internal:</strong> <span>데이터베이스 내부 테이블용 스키마 </span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>v_catalog:</strong> <span>오브젝트 정보 카탈로그 스키마 </span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>v_monitor:</strong> <span>모니터링용 테이블 스키마 </span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>public:</strong> <span>일반 사용자를 위한 기본 스키마 </span></li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. Schema 생성 (CREATE SCHEMA)</h3>
    <p class="section-description">스키마는 <code>dbadmin</code> 계정으로 생성 가능하며, 권한 상속 설정을 통해 하위 객체 관리를 자동화할 수 있습니다.</p>
    <div class="syntax-box">
      <strong>기본 구문:</strong>
      <pre><code>CREATE SCHEMA [ IF NOT EXISTS ] [database.]schema 
[ AUTHORIZATION username] 
[ DEFAULT { INCLUDE | EXCLUDE } [ SCHEMA ] PRIVILEGES ];</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 파라미터</dt>
      <dd class="feature-dd">
        <strong>IF NOT EXISTS:</strong> 동일 이름의 스키마가 존재할 경우 오류 없이 통과합니다. <br>
        <strong>AUTHORIZATION:</strong> 생성과 동시에 특정 유저에게 스키마 소유권을 부여합니다. <br>
        <strong>DEFAULT INCLUDE PRIVILEGES:</strong> 스키마 권한을 하위 테이블에 자동 상속합니다.
      </dd>
    </dl>
    <p class="example-label">예시</p>
    <pre><code>-- 분석용 스키마 생성 및 권한 상속 설정
CREATE SCHEMA analytics DEFAULT INCLUDE PRIVILEGES;

-- 특정 유저 소유의 스키마 생성
CREATE SCHEMA staging AUTHORIZATION user_admin;</code></pre>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. Schema 수정 및 관리 (ALTER SCHEMA)</h3>
    <p class="section-description">스키마의 이름, 소유자, 또는 권한 상속 정책을 변경할 때 사용합니다.</p>
    <div class="syntax-box">
      <strong>주요 수정 구문:</strong>
      <pre><code>-- 스키마 이름 변경
ALTER SCHEMA schema RENAME TO new_schema_name;

-- 스키마 소유자 변경
ALTER SCHEMA schema OWNER TO username;

-- 권한 상속 설정 변경
ALTER SCHEMA schema DEFAULT {INCLUDE | EXCLUDE} SCHEMA PRIVILEGES;</code></pre>
    </div>
    <p class="example-label">예시</p>
    <pre><code>-- 스키마 이름 변경
ALTER SCHEMA old_biz RENAME TO sales_biz;

-- 소유자를 dbadmin으로 변경
ALTER SCHEMA sales_biz OWNER TO dbadmin;</code></pre>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">4. Schema 삭제 및 조회</h3>
    <div class="syntax-box">
      <strong>삭제 구문 (DROP SCHEMA):</strong>
      <pre><code>DROP SCHEMA [ IF EXISTS ] schema[,…] [ CASCADE | RESTRICT ];</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> 삭제 옵션</dt>
      <dd class="feature-dd">
        <strong>CASCADE:</strong> 스키마 내 모든 객체(테이블, 뷰 등)를 함께 삭제합니다. <br>
        <strong>RESTRICT:</strong> (기본값) 스키마가 비어있을 때만 삭제가 가능합니다.
      </dd>
    </dl>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> 정보 조회</dt>
      <dd class="feature-dd">
        전체 스키마 목록을 확인하려면 시스템 테이블을 조회합니다.
        <pre><code>SELECT * FROM SCHEMATA;</code></pre>
      </dd>
    </dl>
  </div>
</div>

<hr style="margin: 3rem 0;">
<div id="user" style="scroll-margin-top: 100px;"></div>

## User

<div class="architecture-section">
  <p class="section-description">Vertica에서 <strong>User(사용자)</strong>는 데이터베이스에 접근하는 주체입니다. 관리자 계정(Administrator)은 데이터베이스 설치 시 생성되는 <code>vertica</code>(OS 계정과 동일)이며, 일반 사용자는 명시적으로 생성하여 권한 및 리소스를 할당해야 합니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. 사용자 생성 및 관리 (CREATE / ALTER USER)</h3>
    <p class="section-description">데이터베이스 사용자를 생성하고, 패스워드, 리소스 풀, 프로파일 등의 속성을 지정합니다. 시스템 보안 정책상 암호를 10회 이상 틀리면 계정이 잠기며(Lock), 이 경우 관리자가 <code>ACCOUNT UNLOCK</code> 작업을 수행해야 합니다.</p>
    <div class="syntax-box">
      <strong>기본 구문:</strong>
      <pre><code>CREATE USER user_name [ account_parameter value[,...] ];
ALTER USER user_name { account_parameter setting | ACCOUNT UNLOCK };</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 파라미터</dt>
      <dd class="feature-dd">
        <strong>IDENTIFIED BY:</strong> 사용자의 비밀번호를 설정합니다.<br>
        <strong>PROFILE:</strong> 사용자에게 적용할 보안/패스워드 프로파일을 지정합니다.<br>
        <strong>RESOURCE POOL:</strong> 쿼리 실행 시 사용할 전용 리소스 풀을 지정합니다.
      </dd>
    </dl>
    <p class="example-label">예시</p>
    <pre><code>-- 사용자 생성 (비밀번호 및 리소스 풀 지정)
CREATE USER analyst IDENTIFIED BY 'StrongPassw0rd' RESOURCE POOL analyst_pool;

-- 암호 오류로 잠긴 계정 해제 (관리자 권한)
ALTER USER analyst ACCOUNT UNLOCK;</code></pre>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 사용자 삭제 및 정보 조회</h3>
    <div class="syntax-box">
      <strong>삭제 구문 (DROP USER):</strong>
      <pre><code>DROP USER [ IF EXISTS ] user_name[,...] [ CASCADE ];</code></pre>
    </div>
    <ul class="feature-list" style="margin-top: 1rem;">
      <li><span class="feature-list__icon">🔹</span> <strong>CASCADE 옵션:</strong> <span>해당 사용자가 생성한 모든 객체(테이블 등)를 함께 삭제합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>사용자 조회:</strong> <span><code>SELECT * FROM USERS;</code> 쿼리를 통해 전체 데이터베이스 사용자 정보를 확인할 수 있습니다.</span></li>
    </ul>
  </div>
</div>

<hr style="margin: 3rem 0;">
<div id="profile" style="scroll-margin-top: 100px;"></div>

## Profile

<div class="architecture-section">
  <p class="section-description">Vertica <strong>Profile(프로파일)</strong>은 사용자의 <strong>보안 및 패스워드 정책</strong>을 정의하고 관리하는 객체입니다. 사용자별로 보안 등급에 따라 프로파일을 생성하여 할당할 수 있으며, 기본적으로 제공되는 default 프로파일이 존재합니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">Profile 생성 및 할당</h3>
    <p class="section-description"><code>CREATE PROFILE</code>로 패스워드 관련 제약 조건을 설정하고, 사용자 생성 또는 수정 시 이를 할당합니다.</p>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 제약조건 (Password Parameters)</dt>
      <dd class="feature-dd">
        <strong>PASSWORD_LIFE_TIME:</strong> 패스워드의 유효 기간을 설정합니다.<br>
        <strong>FAILED_LOGIN_ATTEMPTS:</strong> 허용되는 최대 로그인 실패 횟수입니다 (초과 시 Lock).<br>
        <strong>PASSWORD_LOCK_TIME:</strong> 로그인이 잠긴 후 유지되는 시간입니다.<br>
        <strong>PASSWORD_MIN_LENGTH:</strong> 패스워드의 최소 길이를 지정합니다.
      </dd>
    </dl>
    <p class="example-label">프로파일 생성 및 적용 예시</p>
    <pre><code>-- 보안이 강화된 프로파일 생성 (길이 10자 이상, 5회 실패 시 잠금)
CREATE PROFILE strict_profile LIMIT 
    PASSWORD_MIN_LENGTH 10 
    FAILED_LOGIN_ATTEMPTS 5;

-- 기존 사용자에게 새 프로파일 적용
ALTER USER analyst PROFILE strict_profile;</code></pre>
  </div>
</div>

<hr style="margin: 3rem 0;">
<div id="resource-pool" style="scroll-margin-top: 100px;"></div>

## Resource Pool

<div class="architecture-section">
  <p class="section-description"><strong>Resource Pool(리소스 풀)</strong>은 버티카 데이터베이스에서 작업(Workload)을 관리하기 위해 할당하는 메모리 및 스레드 공간입니다. 기본적으로 <code>general</code>, <code>sysquery</code>, <code>tm</code>(Tuple Mover) 등의 Built-in 리소스 풀이 제공되며, 업무와 사용자 성격에 맞춰 커스텀 풀을 추가 생성하는 것이 핵심 운영 전략입니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. Resource Pool 구성 및 관리</h3>
    <p class="section-description">효율적인 자원 배분을 위해 풀을 생성(<code>CREATE</code>), 수정(<code>ALTER</code>), 삭제(<code>DROP</code>)할 수 있습니다.</p>
    <div class="syntax-box">
      <strong>기본 생성 구문:</strong>
      <pre><code>CREATE RESOURCE POOL pool_name [ parameter_name setting ]...;</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 파라미터</dt>
      <dd class="feature-dd">
        <strong>MEMORYSIZE / MAXMEMORYSIZE:</strong> 풀의 초기 메모리 크기와 사용 가능한 최대 메모리 크기<br>
        <strong>MAXQUERYMEMORYSIZE:</strong> 단일 쿼리가 사용할 수 있는 최대 메모리 (초과 시 쿼리 취소)<br>
        <strong>MAXCONCURRENCY:</strong> 풀에서 동시에 실행될 수 있는 최대 세션/애플리케이션 개수<br>
        <strong>PLANNEDCONCURRENCY:</strong> 풀 내 메모리를 분할할 동시성 기준값 (1/n)<br>
        <strong>QUEUETIMEOUT / RUNTIMECAP:</strong> 대기 큐 타임아웃 및 쿼리 최대 실행 시간 제한
      </dd>
    </dl>
    <ul class="feature-list" style="margin-top: 1rem;">
      <li><span class="feature-list__icon">💡</span> <strong>운영 Best Practice:</strong> <span>배치 작업 시간대와 일반 사용자 접속 시간대의 리소스 풀 구성을 스크립트로 분리·조정하여 시스템 사용을 극대화합니다.</span></li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. Resource Pool 권한 부여 및 할당</h3>
    <p class="section-description">생성된 리소스 풀은 다수의 사용자가 공유할 수 있으나, 한 사용자는 동시에 하나의 풀만 사용할 수 있습니다.</p>
    <p class="example-label">적용 예시</p>
    <pre><code>-- 1. 리소스 풀 생성
CREATE RESOURCE POOL batch_pool 
    MAXMEMORYSIZE '10G' MAXCONCURRENCY 4;

-- 2. 사용자에게 리소스 풀 사용 권한 부여 (GRANT USAGE)
GRANT USAGE ON RESOURCE POOL batch_pool TO batch_user;

-- 3. 사용자에게 기본 리소스 풀 할당 (ALTER USER)
ALTER USER batch_user RESOURCE POOL batch_pool;</code></pre>
  </div>
</div>

<hr style="margin: 3rem 0;">
<div id="privilege" style="scroll-margin-top: 100px;"></div>

## Privilege
<div class="architecture-section">
  <p class="section-description"><strong>Privilege(권한)</strong>는 사용자가 SCHEMA, TABLE, RESOURCE POOL 등의 객체에 접근하고 조작할 수 있는 권리를 의미합니다. Vertica에서는 <strong>Role(역할)</strong> 기반의 권한 관리와 <strong>스키마 권한 상속(Schema Inheritance)</strong>을 적극 활용하여 복잡한 권한 체계를 효율적으로 관리할 수 있습니다.</p>

  <div class="image-box-styled" style="margin-bottom: 2.5rem;">
    <img src="{{ '/assets/images/object_privileges.png' | relative_url }}" alt="Vertica Object Privileges">
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. 권한 상속 및 Role 활용 실습 (Schema Inheritance & Roles)</h3>
    <p class="section-description">스키마의 <code>DEFAULT INCLUDE PRIVILEGES</code> 옵션이 어떻게 동작하는지, Role을 통해 권한을 어떻게 부여하는지 실습을 통해 알아봅니다.</p>
    
    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">1단계: 환경 구성 (Schema, User, Role 생성)</h4>
      <div class="syntax-box">
        <pre><code>-- 권한 상속이 비활성화된 스키마
CREATE SCHEMA sc1;
-- 권한 상속이 활성화된 스키마
CREATE SCHEMA sc2 DEFAULT INCLUDE SCHEMA PRIVILEGES;

-- 사용자 및 역할 생성
CREATE USER u1; CREATE USER u2;
CREATE ROLE r1; CREATE ROLE r2;</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">2단계: Role에 권한 부여 및 사용자에게 Role 할당</h4>
      <div class="syntax-box">
        <pre><code>-- 각 Role에 스키마 사용 및 조회 권한 부여
GRANT USAGE, SELECT ON SCHEMA sc1 TO r1;
GRANT USAGE, SELECT ON SCHEMA sc2 TO r2;

-- 각 사용자에게 Role 부여
GRANT r1 TO u1;
GRANT r2 TO u2;

-- 사용자 접속 시 기본 Role 설정
ALTER USER u1 DEFAULT ROLE r1;
ALTER USER u2 DEFAULT ROLE r2;</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">3단계: 테이블 생성 후 권한 상속 테스트</h4>
      <p class="section-description">각 스키마에 테이블을 생성하고, 사용자들이 해당 테이블을 조회할 수 있는지 확인합니다.</p>
      <div class="syntax-box">
        <pre><code>-- 각 스키마에 테이블 생성
CREATE TABLE sc1.t1(a int);
CREATE TABLE sc2.t1(a int);

-- u1 사용자로 sc1.t1 조회 시도
-- 결과: 실패 (ERROR: Permission denied for relation t1)
-- 이유: sc1은 권한 상속이 비활성화되어, 스키마에 부여된 SELECT 권한이 하위 테이블 t1에 자동으로 적용되지 않음
vsql -U u1 -c "SELECT * FROM sc1.t1;"

-- u2 사용자로 sc2.t1 조회 시도
-- 결과: 성공
-- 이유: sc2는 'DEFAULT INCLUDE SCHEMA PRIVILEGES'로 생성되어, 스키마에 부여된 SELECT 권한이 하위 테이블 t1에 자동으로 상속됨
vsql -U u2 -c "SELECT * FROM sc2.t1;"</code></pre>
      </div>
      <ul class="feature-list" style="margin-top: 1rem;">
        <li><span class="feature-list__icon">💡</span> <strong>Tip:</strong> <span>권한 상속이 비활성화된 스키마(`sc1`)의 테이블을 조회하려면, 테이블에 대한 `SELECT` 권한을 명시적으로 부여해야 합니다. (<code>GRANT SELECT ON TABLE sc1.t1 TO r1;</code>)</span></li>
      </ul>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 권한 위임 (WITH GRANT OPTION)</h3>
    <p class="section-description"><code>WITH GRANT OPTION</code>은 특정 사용자에게 부여된 권한을 다른 사용자에게 다시 부여할 수 있는 '위임' 권한을 줍니다. 특히, 다른 사용자가 생성한 뷰(View)를 조회해야 할 때 이 옵션이 중요하게 사용됩니다.</p>
    
    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">문제 상황: 뷰(View) 조회 권한</h4>
      <p class="section-description">사용자 `u2`가 `u1`이 생성한 뷰 `v_t`를 조회하려고 합니다. `v_t`는 `sc4.t1` 테이블을 참조합니다. `u2`는 뷰에 대한 `SELECT` 권한은 있지만, 기반 테이블 `sc4.t1`에 대한 권한이 없어 조회가 실패합니다.</p>
      <div class="syntax-box">
        <pre><code>-- 1. u1에게 sc4.t1 테이블 조회 권한 부여 (위임 권한 없음)
GRANT SELECT ON TABLE sc4.t1 TO u1;

-- 2. u1이 sc4.t1을 참조하는 뷰 생성
-- (u1은 뷰의 소유자이므로, 다른 사용자에게 이 뷰에 대한 권한을 부여할 수 있음)
vsql -U u1 -c "CREATE VIEW sc4.v_t AS SELECT * FROM sc4.t1;"

-- 3. u1이 u2에게 뷰 조회 권한 부여
vsql -U u1 -c "GRANT SELECT ON VIEW sc4.v_t TO u2;"

-- 4. u2가 뷰 조회 시도 -> 실패
-- 이유: u2는 뷰의 기반 테이블인 sc4.t1에 대한 SELECT 권한이 없음
vsql -U u2 -c "SELECT * FROM sc4.v_t;"</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">해결 방법: WITH GRANT OPTION을 통한 권한 위임</h4>
      <p class="section-description">DBA가 `u1`에게 `WITH GRANT OPTION`을 부여하면, `u1`은 자신이 받은 `sc4.t1` 테이블의 `SELECT` 권한을 `u2`에게 위임할 수 있습니다. 이로써 `u2`는 뷰와 기반 테이블 모두에 대한 권한을 갖게 되어 조회가 가능해집니다.</p>
      <div class="syntax-box">
        <pre><code>-- 1. DBA가 u1에게 sc4.t1에 대한 SELECT 권한을 '위임 옵션'과 함께 부여
GRANT SELECT ON TABLE sc4.t1 TO u1 WITH GRANT OPTION;

-- 2. u1이 자신이 받은 권한을 u2에게 위임
vsql -U u1 -c "GRANT SELECT ON TABLE sc4.t1 TO u2;"

-- 3. u2가 뷰 조회 시도 -> 성공
-- 이유: u2는 이제 뷰(sc4.v_t)와 기반 테이블(sc4.t1) 모두에 대한 SELECT 권한을 가짐
vsql -U u2 -c "SELECT * FROM sc4.v_t;"</code></pre>
      </div>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. 권한 정보 종합 조회</h3>
    <p class="section-description">다음 쿼리는 데이터베이스 내의 모든 사용자와 역할(Role)에 부여된 스키마 및 테이블 권한을 종합적으로 조회합니다. 권한 현황을 감사하거나 복잡한 권한 문제를 진단할 때 유용합니다.</p>
    <div class="syntax-box">
      <strong>사용자 및 역할 권한 조회 쿼리:</strong>
      <pre><code>SELECT 
    'usernm' AS gbn, 
    user_name, 
    is_super_user, 
    resource_pool,
    CASE WHEN all_roles <> 'dbduser*, dbadmin*, pseudosuperuser*' THEN all_roles ELSE 'admin' END AS all_roles,
    CASE WHEN default_roles <> 'dbduser*, dbadmin*, pseudosuperuser*' THEN default_roles ELSE 'admin' END AS default_roles,
    CASE WHEN search_path <> '"$user", public, v_catalog, v_monitor, v_internal, v_func' THEN search_path ELSE 'default' END AS search_path,
    object_type, 
    object_schema, 
    object_name,
    CASE WHEN user_name ILIKE 'vertica' THEN 'admin' ELSE privileges_description END AS privileges_description
FROM users t1
LEFT OUTER JOIN (
    SELECT DISTINCT grantee, grantor, object_type, object_schema,
           CASE WHEN object_type ILIKE 'table' THEN '' ELSE object_name END AS object_name,
           privileges_description
    FROM grants 
    WHERE object_type ILIKE ANY ('schema','table')
) t2 ON user_name = grantee
UNION ALL
SELECT 
    'rolenm' AS gbn,  
    t1.object_name, 
    NULL, '', '', '', '',
    t2.object_type, 
    object_schema, 
    t2.object_name,
    CASE WHEN grantee ILIKE 'vertica' THEN 'admin' ELSE privileges_description END AS privileges_description
FROM (
    SELECT object_name, object_type 
    FROM grants 
    WHERE object_type ILIKE 'role' AND object_name NOT ILIKE 'useradmin'
) t1
LEFT OUTER JOIN (
    SELECT DISTINCT grantee, grantor, object_type, object_schema,
           CASE WHEN object_type ILIKE 'table' THEN '' ELSE object_name END AS object_name,
           privileges_description
    FROM grants 
    WHERE object_type ILIKE ANY ('schema','table')
) t2 ON t1.object_name = t2.grantee
ORDER BY 1, 2, 3;</code></pre>
    </div>
  </div>
</div>

<hr style="margin: 3rem 0;">
<div id="backup-restore" style="scroll-margin-top: 100px;"></div>

## Locks

<div class="architecture-section">
  <p class="section-description">Vertica는 동시성 제어(Concurrency Control)를 위해 <strong>잠금(Lock)</strong> 메커니즘을 사용합니다. 여러 세션이 동시에 동일한 데이터베이스 객체에 접근할 때 데이터의 일관성과 무결성을 보장하기 위해, Vertica는 작업의 종류에 따라 자동으로 다양한 수준의 잠금을 획득하고 해제합니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. 주요 잠금 모드 (Lock Modes)</h3>
    <p class="section-description">Vertica는 계층적인 잠금 모델을 사용하며, 각 작업의 성격에 따라 다음 5가지 주요 잠금 모드를 사용합니다.</p>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> I (Intent) Lock</dt>
      <dd class="feature-dd">
        테이블과 같은 상위 객체에 설정되며, 하위 객체(예: 특정 파티션)에 더 강력한 잠금(S, U, X)을 설정하려는 <strong>의도</strong>를 나타냅니다. 다른 세션이 상위 객체 전체에 대한 배타적 잠금(X Lock)을 획득하는 것을 방지합니다.
      </dd>
      <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> S (Share) Lock</dt>
      <dd class="feature-dd">
        주로 <code>SELECT</code>와 같은 읽기 작업에 사용됩니다. 여러 세션이 동시에 동일한 객체에 대한 S Lock을 획득할 수 있어, 읽기 작업의 동시성을 극대화합니다.
      </dd>
      <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> U (Update) Lock</dt>
      <dd class="feature-dd">
        <code>UPDATE</code>, <code>DELETE</code>, <code>MERGE</code>와 같은 DML 작업에 사용됩니다. 한 객체에 대해 오직 하나의 세션만 U Lock을 가질 수 있으며, 이는 두 세션이 서로를 기다리는 교착 상태(Deadlock)를 방지하는 역할을 합니다. U Lock은 실제 데이터를 수정하기 직전에 X Lock으로 승격될 수 있습니다.
      </dd>
      <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> X (Exclusive) Lock</dt>
      <dd class="feature-dd">
        <code>ALTER TABLE</code>, <code>DROP TABLE</code>, <code>TRUNCATE</code>와 같은 DDL 작업이나 실제 데이터 수정 시에 사용되는 가장 강력한 잠금입니다. 한 세션이 X Lock을 획득하면, 다른 어떤 세션도 해당 객체에 대한 어떠한 종류의 잠금도 획득할 수 없습니다.
      </dd>
      <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> O (Ownership) Lock</dt>
      <dd class="feature-dd">
        객체가 생성될 때 획득하는 특수한 잠금으로, 해당 객체에 대한 소유권을 나타냅니다.
      </dd>
    </dl>
    <p class="section-description" style="margin-top: 2rem; margin-bottom: 1rem;">
      아래는 각 잠금 모드 간의 호환성을 나타내는 매트릭스입니다. 'Yes'는 두 잠금이 충돌 없이 공존할 수 있음을, 'No'는 충돌이 발생함을 의미합니다.
    </p>
    <div class="image-box-styled">
      <img src="{{ '/assets/images/locks.png' | relative_url }}" alt="Vertica Lock Compatibility Matrix">
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 잠금 모니터링 (Lock Monitoring)</h3>
    <p class="section-description">데이터베이스에서 현재 활성화된 잠금이나 잠금 대기 상태를 확인하는 것은 성능 문제 해결 및 병목 현상 분석에 매우 중요합니다. Vertica는 <code>LOCKS</code> 시스템 테이블을 통해 현재 잠금 상태를 모니터링할 수 있는 뷰를 제공합니다.</p>
    <div class="syntax-box">
      <strong>현재 잠금 상태 조회:</strong>
      <pre><code>-- 현재 부여된 모든 잠금 확인
SELECT * FROM locks;</code></pre>
    </div>
    <ul class="feature-list" style="margin-top: 1rem;">
      <li><span class="feature-list__icon">💡</span> <strong>Tip:</strong> <span>장시간 실행되는 쿼리나 DML 작업으로 인해 다른 세션들이 잠금 대기(Lock Wait) 상태에 빠지는 경우가 많습니다. <code>v_monitor.locks</code>와 <code>v_monitor.sessions</code>를 조인하여 잠금을 보유한 세션의 상세 정보를 파악하고 문제를 해결할 수 있습니다.</span></li>
    </ul>
  </div>
</div>

<hr style="margin: 3rem 0;">
<div id="backup-restore" style="scroll-margin-top: 100px;"></div>

## Backup & Restore
<div class="architecture-section">
  <p class="section-description">Vertica는 <code>vbr</code> 유틸리티를 통해 다양한 백업 및 복구 시나리오를 지원합니다. 백업 방식은 크게 전체 DB를 백업하는 <strong>Full Backup</strong>과 특정 객체만 백업하는 <strong>Object-Level Backup</strong>으로 나뉩니다. 모든 설정은 <code>.ini</code> 구성 파일을 통해 관리되며, 다양한 예제 파일이 <code>/opt/vertica/share/vbr/example_configs</code> 경로에 제공됩니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. Full Backup (전체 백업)</h3>
    <p class="section-description">데이터베이스 전체를 백업하며, 복구 시에는 DB가 중단된 상태여야 합니다. 첫 백업 이후 동일한 위치에 백업하면 자동으로 증분 백업이 수행됩니다.</p>
    
    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">구성 파일 예시 (backup_restore_full_local.ini)</h4>
      <div class="syntax-box">
        <pre><code>[Mapping]
# 각 DB 노드의 데이터를 백업할 로컬 경로를 지정합니다.
v_entdb_node0001 = [&lt;DB_IP_1&gt;]:/data/BACKUP
v_entdb_node0002 = [&lt;DB_IP_2&gt;]:/data/BACKUP
v_entdb_node0003 = [&lt;DB_IP_3&gt;]:/data/BACKUP

[Misc]
snapshotName = backup_snapshot
# DB 패스워드가 저장된 파일 경로를 지정합니다.
passwordFile = /home/vertica/Backups/.pwd.txt
# 보관할 백업본의 개수를 지정합니다. (현재 + 6개)
restorePointLimit = 6

[Database]
dbName = ENTDB
dbUser = vertica
# 비밀번호를 프롬프트에서 직접 입력하지 않도록 설정합니다.
dbPromptForPassword = False</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">실행 절차</h4>
      <div class="syntax-box">
        <pre><code># 1. 패스워드 파일 생성 및 보안 권한 설정
echo "[Passwords]" > /home/vertica/Backups/.pwd.txt
echo "dbPassword=your_password" >> /home/vertica/Backups/.pwd.txt
chmod 600 /home/vertica/Backups/.pwd.txt

# 2. 백업 위치 초기화 (최초 1회)
vbr -t init -c backup_restore_full_local.ini

# 3. 백업 실행
vbr -t backup -c backup_restore_full_local.ini

# 4. 복구 실행 (DB 중단 후)
vbr -t restore -c backup_restore_full_local.ini

# 5. 특정 시점(Archive)으로 복구
# 먼저 백업 리스트 확인
vbr -t listbackup -c backup_restore_full_local.ini
# 특정 백업본으로 복구
vbr -t restore -c backup_restore_full_local.ini --archive=20240729_152200</code></pre>
      </div>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. Object-Level Backup (객체 단위 백업)</h3>
    <p class="section-description">특정 스키마나 테이블 단위로 백업하며, DB가 기동 중인 상태에서도 복구가 가능합니다. (HDFS 스토리지에서는 지원되지 않습니다.)</p>
    
    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">구성 파일 예시 (backup_restore_object_local.ini)</h4>
      <div class="syntax-box">
        <pre><code>[Mapping]
v_entdb_node0001 = [&lt;DB_IP_1&gt;]:/data/BACKUP
...
[Misc]
snapshotName = backup_snapshot
# 백업할 객체(스키마 또는 테이블) 지정
objects = public.sales, marketing.campaigns
# 복구 시 동일 객체가 존재할 경우 처리 방식 (coexist: 이름 변경 후 생성)
objectRestoreMode = coexist
passwordFile = /home/vertica/Backups/.pwd.txt
restorePointLimit = 4
# 백업 전 디스크 여유 공간 체크 활성화
enableFreeSpaceCheck = True

[Database]
dbName = ENTDB
dbUser = vertica
dbPromptForPassword = False</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">실행 절차</h4>
      <div class="syntax-box">
        <pre><code># 백업 실행
vbr -t backup -c backup_restore_object_local.ini

# 복구 실행 (DB 기동 중)
# 모든 객체 복구
vbr -t restore -c backup_restore_object_local.ini
# 특정 객체만 복구
vbr -t restore -c backup_restore_object_local.ini --restore-objects=public.sales</code></pre>
      </div>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. Hard-Link Backup (Enterprise Mode)</h3>
    <p class="section-description">Enterprise Mode에서 지원되는 빠른 로컬 백업 방식입니다. 데이터 파일을 복사하는 대신 원본 파일에 대한 하드 링크를 생성하여 백업 시간을 크게 단축합니다.</p>
    
    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">구성 파일 예시 (backup_restore_full_hardlink.ini)</h4>
      <div class="syntax-box">
        <pre><code>[Mapping]
v_entdb_node0001 = [&lt;DB_IP_1&gt;]:/data/BACKUP
...
[Transmission]
# 하드 링크 백업 활성화
hardLinkLocal = True
# 하드 링크 생성 실패 시 파일 복사 시도 안 함
copyOnHardLinkFailure = False

[Database]
dbName = ENTDB
dbUser = vertica

[Misc]
snapshotName = backup_snapshot
passwordFile = /home/vertica/Backups/.pwd.txt</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">실행 절차</h4>
      <div class="syntax-box">
        <pre><code># 백업 실행
vbr -t backup -c backup_restore_full_hardlink.ini

# 복구 실행 (DB 중단 후)
vbr -t restore -c backup_restore_full_hardlink.ini</code></pre>
      </div>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">4. Cloud Backup (Eon Mode)</h3>
    <p class="section-description">Eon Mode에서 지원되는 방식으로, Communal Storage(S3 등)에 저장된 데이터를 다른 클라우드 스토리지 위치로 백업합니다.</p>
    
    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">사전 환경 변수 설정</h4>
      <p class="section-description">vbr을 실행할 셸에서 원본(Communal) 및 대상(Backup) 스토리지에 접근하기 위한 인증 정보를 환경 변수로 설정해야 합니다.</p>
      <div class="syntax-box">
        <pre><code># 대상 백업 스토리지 인증 정보
export VBR_BACKUP_STORAGE_ACCESS_KEY_ID=minioadmin
export VBR_BACKUP_STORAGE_SECRET_ACCESS_KEY=your_secret_key
export VBR_BACKUP_STORAGE_ENDPOINT_URL=http://&lt;STORAGE_IP&gt;:9000

# 원본 Communal 스토리지 인증 정보
export VBR_COMMUNAL_STORAGE_ACCESS_KEY_ID=minioadmin
export VBR_COMMUNAL_STORAGE_SECRET_ACCESS_KEY=your_secret_key
export VBR_COMMUNAL_STORAGE_ENDPOINT_URL=http://&lt;STORAGE_IP&gt;:9000</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">구성 파일 예시 (backup_restore_cloud_storage.ini)</h4>
      <div class="syntax-box">
        <pre><code>[CloudStorage]
# 백업 데이터가 저장될 클라우드 경로
cloud_storage_backup_path = s3://backup-bucket/
# vbr 메타데이터를 저장할 로컬 경로
cloud_storage_backup_file_system_path = /home/vertica/Backups/backup_cloud/
# 백업/복구 시 동시 전송 스레드 수
cloud_storage_concurrency_backup = 10
cloud_storage_concurrency_restore = 10

[Misc]
snapshotName = backup_snapshot
restorePointLimit = 2
passwordFile = /home/vertica/Backups/.pwd.txt

[Database]
dbName = TESTDB
dbUser = vertica
dbPromptForPassword = False</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">실행 절차</h4>
      <div class="syntax-box">
        <pre><code># 백업 위치 초기화 (최초 1회, 기존 백업이 있으면 --cloud-force-init 사용)
vbr -t init -c backup_restore_cloud_storage.ini

# 백업 실행
vbr -t backup -c backup_restore_cloud_storage.ini

# 복구 실행
vbr -t restore -c backup_restore_cloud_storage.ini</code></pre>
      </div>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">5. 백업 히스토리 조회</h3>
    <p class="section-description"><code>dc_vbr_invocations</code> 시스템 테이블을 통해 vbr 작업의 시작/종료 시간, 상태, 소요 시간 등의 이력을 조회할 수 있습니다.</p>
    <div class="syntax-box">
      <strong>백업 이력 조회 쿼리:</strong>
      <pre><code>SELECT 
    vbr.start_time,
    vbr.end_time,
    DECODE(status, 'Fail', '실패', '성공', DECODE(vbr.end_time, NULL, '진행중', '성공')) AS status,
    TIMESTAMPDIFF('s', vbr.start_time, vbr.end_time) AS duration_sec,
    dob.snapshot_name,
    dob.include_patterns,
    ROUND(dob.bytes_transferred/1024^3, 2) AS size_gb
FROM (
    SELECT 
        identifier,
        MAX(DECODE(status, 'Start', time, NULL)) AS start_time,
        MAX(DECODE(status, 'Complete', time, NULL)) AS end_time,
        MAX(DECODE(status, 'Fail', '실패', '성공')) AS status
    FROM dc_vbr_invocations
    GROUP BY 1
) vbr
LEFT OUTER JOIN dc_object_backups dob ON (dob.identifier = vbr.identifier)
WHERE 1=1
  AND start_time > 'date time' -- 조회할 기간
ORDER BY vbr.start_time DESC;</code></pre>
    </div>
  </div>
</div>


<hr style="margin: 3rem 0;">
<div id="cluster-operation" style="scroll-margin-top: 100px;"></div>

## Cluster Operation & Diagnostics

<div class="architecture-section">
  <p class="section-description">Vertica 클러스터의 안정적인 운영을 위해 데이터베이스를 기동/중지하고, 노드 장애 시 운영 가이드에 따른 표준 조치 절차를 수행합니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. DB 기동 및 중지 (Start / Stop)</h3>
    <p class="section-description">Vertica 관리자 도구인 <code>admintools</code>를 사용하여 데이터베이스 전체를 안전하게 제어합니다.</p>
    <div class="syntax-box">
      <strong>Vertica 상태 확인:</strong>
      <pre><code>admintools -t view_cluster</code></pre>
      <strong>Vertica DB 기동:</strong>
      <pre><code>admintools -t start_db -d DBNM -p 'vertica계정password' -i</code></pre>
      <strong>기동 로그 모니터링:</strong>
      <pre><code>tail -f /catalog/DBNM/v_dbnm_node00XX_catalog/startup.log</code></pre>
      <strong>Vertica DB 중지:</strong>
      <pre><code>admintools -t stop_db -d DBNM -p 'vertica계정password' </code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 클라이언트 접속 세션 관리</h3>
    <p class="section-description">점검 전후로 클라이언트의 최대 접속 세션 수를 조정하여 시스템 진입을 제어합니다.</p>
    <div class="syntax-box">
      <pre><code>vsql -U vertica -w 'password' -c "SELECT set_config_parameter('MaxClientSessions', 1000);"</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. 장애 발생 시 클러스터/노드 재기동</h3>
    
    <h4 class="section-subtitle">가. 장애 진단 프로세스</h4>
    <p class="section-description">장애 발생 시점부터 복구 완료까지의 주요 단계를 시각적으로 확인합니다.</p>

    <div class="process-grid">
      <div class="process-step">
        <div class="image-box-styled"><img src="{{ '/assets/images/cluster_operation_1.png' | relative_url }}" alt="정상단계"></div>
        <p class="process-step__title">[1단계] 정상 단계</p>
        <p class="process-step__description">모든 노드가 UP 상태로 서비스 중</p>
      </div>
      <div class="process-step">
        <div class="image-box-styled"><img src="{{ '/assets/images/cluster_operation_2.png' | relative_url }}" alt="장애발생"></div>
        <p class="process-step__title process-step__title--failure">[2단계] 장애 발생</p>
        <p class="process-step__description">특정 노드가 DOWN되어 가용성 저하</p>
      </div>
      <div class="process-step">
        <div class="image-box-styled"><img src="{{ '/assets/images/cluster_operation_3.png' | relative_url }}" alt="장애노드 재시작"></div>
        <p class="process-step__title">[3단계] 장애 노드 재시작</p>
        <p class="process-step__description">Admintools를 통한 노드 복구 시도</p>
      </div>
      <div class="process-step">
        <div class="image-box-styled"><img src="{{ '/assets/images/cluster_operation_4.png' | relative_url }}" alt="정상 서비스단계"></div>
        <p class="process-step__title process-step__title--success">[4단계] 정상 서비스 단계</p>
        <p class="process-step__description">데이터 동기화 완료 및 서비스 정상화</p>
      </div>
    </div>

    <h4 class="section-subtitle">나. 장애 조치 Check List</h4>
    <p class="section-description">재기동 전에 따라 아래 항목을 반드시 점검하여 잔류 프로세스를 정리합니다.</p>
    <ul class="feature-list">
      <li>
        <span class="feature-list__icon">🔹</span> <strong>SSH 접속 확인:</strong> <span>각 노드 간 패스워드 없는 SSH 통신이 가능한지 점검 (<code>ssh [Target_Node_IP]</code>)</span>
      </li>
      <li>
        <span class="feature-list__icon">🔹</span> <strong>프로세스 생존 확인:</strong> <span>비정상 종료 시 남은 <code>vertica</code>, <code>spread</code> 프로세스를 확인하고 <code>kill -9</code>로 종료</span>
        <div class="syntax-box" style="width: fit-content; margin: 1rem auto 1.5rem;">
          <pre><code>ps -ef | grep vertica
ps -ef | grep spread</code></pre>
        </div>
      </li>
    </ul>

    <h4 class="section-subtitle">다. 노드 재기동 실행</h4>
    <div class="syntax-box">
      <strong>재기동 Command:</strong>
      <pre><code>admintools -t restart_node -d DBNM -p 'password' --hosts [Down_Node_IP]</code></pre>
    </div>

    <h4 class="section-subtitle">라. 기동 모니터링</h4>
    <div class="syntax-box">
      <strong>로그 확인:</strong>
      <pre><code>tail -f /catalog/DBNM/v_dbnm_node00XX_catalog/startup.log</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">4. 장애 분석 파일 생성 (Scrutinize)</h3>
    <p class="section-description">원인 분석을 위해 기술 지원팀에 전달할 정밀 진단 파일을 생성합니다.</p>
    <div class="syntax-box">
      <pre><code>/opt/vertica/bin/scrutinize --by-minute yes -d DBNM -P 'password'</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">5. Management Console (MC) 기동/중지</h3>
    <p class="section-description">클러스터 작업 시 관리 UI 서비스를 제어합니다.</p>
    <div class="syntax-box">
      <pre><code>sudo systemctl status vertica-consoled
sudo systemctl start vertica-consoled
sudo systemctl stop vertica-consoled</code></pre>
    </div>
  </div>
  </div>
  </div>

  <aside class="page-sidebar">
    <div class="sidebar-panel" style="padding-right: 1rem;">
    <h3>On this page</h3>
    <ul>
      <li><a href="#table-projection">Table · Projection</a></li>
      <li><a href="#partition">Partition</a></li>
      <li><a href="#schema">Schema</a></li>
      <li><a href="#user">User</a></li>
      <li><a href="#profile">Profile</a></li>
      <li><a href="#resource-pool">Resource Pool</a></li>
      <li><a href="#privilege">Privilege</a></li>
      <li><a href="#locks">Locks</a></li>
      <li><a href="#backup-restore">Backup & Restore</a></li>
      <li><a href="#cluster-operation">Cluster Operation</a></li>
    </ul>
    </div>
  </aside>
</div>
