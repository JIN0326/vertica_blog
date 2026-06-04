---
title: "Vertica Table & Projection: 논리/물리 모델, 분산 정책 - Vertica Blog"
layout: default
description: "Vertica의 핵심 객체인 Table(논리 모델)과 Projection(물리 저장)의 개념을 알아봅니다. CTAS, LIKE를 이용한 테이블 생성과 Segmentation, Replication 등 데이터 분산 정책을 설명합니다."
keywords: "vertica, table, projection, ctas, like, segmentation, replication, buddy projection, columnar"
canonical_url: "https://jin0326.github.io/vertica_blog/200administration/200administration#table-projection"
---

<div id="table-projection" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody">
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
          <img src="/vertica_blog/assets/images/proj_segmentation.png" alt="Segmentation (해시 분산) 다이어그램">
        </div>
      </div>
      <div class="architecture-subsection">
        <h4 class="section-subtitle">2. Replication (복제)</h4>
        <ul class="feature-list">
          <li><span class="feature-list__icon">🔹</span> <span>소규모 코드성 테이블을 모든 노드에 복제하여 조인 성능 향상</span></li>
          <li><span class="feature-list__icon">🔹</span> <span>노드 장애 시에도 즉각적인 고가용성 제공</span></li>
        </ul>
        <div class="image-box-styled">
          <img src="/vertica_blog/assets/images/proj_replication.png" alt="Replication (복제) 다이어그램">
        </div>
      </div>
      <div class="architecture-subsection">
        <h4 class="section-subtitle">3. 고가용성을 위한 데이터 이중화 (Buddy)</h4>
        <ul class="feature-list">
          <li><span class="feature-list__icon">🔹</span> <span>노드 장애에 대비해 다른 노드에 복제본 자동 생성 및 관리</span></li>
          <li><span class="feature-list__icon">🔹</span> <span>장애 복구 후 자동으로 데이터 동기화(Recovery) 수행</span></li>
        </ul>
        <div class="image-box-styled">
          <img src="/vertica_blog/assets/images/proj_buddy.png" alt="Buddy 프로젝션을 이용한 이중화 다이어그램">
        </div>
      </div>
      <div class="architecture-subsection">
        <h4 class="section-subtitle">4. 특정 노드에 프로젝션 생성</h4>
        <ul class="feature-list">
          <li><span class="feature-list__icon">🔹</span> <span>네트워크 부하 최소화를 위해 입출력이 필요한 특정 노드에만 저장</span></li>
        </ul>
        <div class="image-box-styled">
          <img src="/vertica_blog/assets/images/proj_specific_node.png" alt="특정 노드에 프로젝션 생성 다이어그램">
        </div>
      </div>
    </div>
  </div>
</div>
</div>
</div>