---
title: "Vertica Export: Parquet/Iceberg/CSV 데이터 내보내기 - Vertica Blog"
layout: default
description: "Vertica의 데이터를 외부로 내보내는 다양한 방법을 알아봅니다. vsql을 이용한 텍스트 파일 추출, EXPORT TO DELIMITED, 그리고 데이터 레이크 연동을 위한 EXPORT TO PARQUET/ICEBERG, 클러스터 간 복제를 위한 EXPORT TO VERTICA 사용법을 다룹니다."
keywords: "vertica, export, data export, vsql, export to delimited, export to parquet, export to iceberg, export to vertica, data lake, s3, hdfs"
canonical_url: "https://jin0326.github.io/vertica_blog/300coretech/300coretech#export"
---

<div id="export" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody">
## Export (데이터 내보내기)

<div class="architecture-section">
  <p class="section-description">Vertica의 데이터를 외부 파일로 추출하거나 다른 클러스터로 전송하는 방법입니다. 운영 환경에서는 주로 사용자 데이터의 논리적 백업이나 타 시스템과의 데이터 연계를 위해 사용됩니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. vsql을 이용한 텍스트 파일 내보내기</h3>
    <p class="section-description">가장 보편적인 방법으로, <code>vsql</code>의 실행 결과를 파일로 리다이렉션하여 CSV나 TSV 형태의 텍스트 파일을 생성합니다.</p>
    
    <div class="syntax-box">
      <strong>기본 실행 구문:</strong>
      <pre><code>vsql -U username -w password -At -F ',' -c "SELECT * FROM public.sales;" -o sales_backup.csv</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 옵션 설명</dt>
      <dd class="feature-dd">
        <strong>-A (unaligned):</strong> 출력 시 컬럼 간격을 맞추지 않고 붙여서 출력합니다.<br>
        <strong>-t (tuples only):</strong> 컬럼명(Header) 없이 데이터 행만 출력합니다.<br>
        <strong>-F (field separator):</strong> 컬럼 구분자를 지정합니다 (예: <code>','</code>, <code>'|'</code>).<br>
        <strong>-o (output):</strong> 결과를 콘솔이 아닌 지정한 파일명으로 저장합니다.
      </dd>
    </dl>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. EXPORT TO DELIMITED (구분자 파일로 내보내기)</h3>
    <p class="section-description">쿼리 결과를 CSV나 TSV와 같은 구분자 기반 텍스트 파일로 내보냅니다. 클러스터의 각 노드에 병렬로 파일이 생성되어 대용량 데이터 추출에 효과적입니다.</p>
    <div class="syntax-box">
      <pre><code>EXPORT TO DELIMITED(
    directory = '/data/export/sales/',
    delimiter = '|',
    null = 'NULL'
) AS SELECT * FROM public.sales;</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 옵션 설명</dt>
      <dd class="feature-dd">
        <strong>directory:</strong> 파일이 저장될 서버 경로를 지정합니다. (필수)<br>
        <strong>delimiter:</strong> 컬럼을 구분할 문자를 지정합니다.<br>
        <strong>null:</strong> NULL 값을 표현할 문자열을 지정합니다.
      </dd>
    </dl>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. EXPORT TO PARQUET (데이터 레이크 연동)</h3>
    <p class="section-description">분석용 표준 포맷인 Parquet 형태로 데이터를 내보냅니다. S3나 HDFS 같은 외부 스토리지로 데이터를 백업하거나 데이터 레이크와 연동할 때 매우 효율적입니다.</p>
    <div class="syntax-box">
      <pre><code>EXPORT TO PARQUET(
    directory='s3://my-bucket/backup/sales/',
    partition_by='sale_year, sale_month',
    compression='snappy'
) OVER (PARTITION BY sale_year, sale_month)
AS SELECT 
    EXTRACT(YEAR FROM sale_date) AS sale_year, 
    EXTRACT(MONTH FROM sale_date) AS sale_month, 
    * 
FROM public.sales;</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 옵션 설명</dt>
      <dd class="feature-dd">
        <strong>partition_by:</strong> 지정된 컬럼 값에 따라 하위 디렉터리를 생성하여 데이터를 파티셔닝합니다. (예: <code>/sale_year=2024/sale_month=7/</code>)<br>
        <strong>compression:</strong> 압축 코덱(<code>snappy</code>, <code>gzip</code>)을 지정합니다.<br>
        <strong>fileSizeMB:</strong> 개별 Parquet 파일의 최대 크기를 MB 단위로 지정합니다.
      </dd>
    </dl>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">4. EXPORT TO ICEBERG (Iceberg 테이블로 내보내기)</h3>
    <p class="section-description">데이터를 Apache Iceberg 테이블 포맷으로 내보냅니다. 데이터 레이크하우스 환경에서 트랜잭션과 스키마 변경을 효율적으로 관리할 수 있습니다.</p>
    <div class="syntax-box">
      <pre><code>EXPORT TO ICEBERG(
    location='s3://my-iceberg-lake/warehouse/sales_iceberg',
    format='parquet'
)
AS SELECT * FROM public.sales;</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">5. EXPORT TO VERTICA (클러스터 간 복제)</h3>
    <p class="section-description">네트워크를 통해 한 Vertica 클러스터에서 다른 클러스터로 직접 데이터를 전송합니다.</p>
    <div class="syntax-box">
      <strong>다른 DB로 데이터 복제 예시:</strong>
      <pre><code>-- 1. 대상 데이터베이스에 연결
CONNECT TO VERTICA target_db USER dbadmin PASSWORD 'password' ON 'target_host', 5433;

-- 2. 현재 DB의 'sales' 테이블 데이터를 대상 DB의 'sales' 테이블로 복제
EXPORT TO VERTICA target_db.public.sales 
AS SELECT * FROM public.sales;

-- 3. 연결 해제
DISCONNECT target_db;</code></pre>
    </div>
  </div>
</div>
</div>
</div>