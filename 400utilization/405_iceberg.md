---
title: "Vertica Apache Iceberg 연동: 데이터 레이크하우스 구축 - Vertica Blog"
layout: default
description: "Vertica와 Apache Iceberg를 연동하여 데이터 레이크하우스를 구축하는 방법을 알아봅니다. Spark를 이용한 테이블 생성 및 Vertica 외부 테이블 조회를 설명합니다."
keywords: "vertica, apache iceberg, data lakehouse, spark, external table, stored by iceberg, minio, s3"
---

<div id="iceberg" style="scroll-margin-top: 100px;"></div>

## Apache Iceberg 연동 
<div class="architecture-section">
<div class="flow-diagram">
  <img src="/vertica_blog/assets/images/util_iceberg.png" alt="Apache Iceberg">
</div>
<p class="section-description">Apache Iceberg는 대규모 분석 데이터를 위한 오픈 테이블 포맷으로, Vertica에서 이를 <strong>외부 테이블(External Table)</strong>로 조회하여 고속 분석을 수행할 수 있습니다.</p>

<div class="architecture-subsection">
    <h3 class="section-subtitle">아키텍처 및 구성 요소</h3>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>Apache Spark + Iceberg:</strong> <span>데이터 처리 및 Iceberg 테이블 생성을 담당하는 컴퓨팅 엔진입니다. 원본 데이터를 가공하여 스토리지에 적재합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>Amazon S3 / MinIO:</strong> <span>Iceberg의 실제 데이터 파일(Parquet 등)과 메타데이터 파일(Avro 등)이 영구적으로 저장되는 오브젝트 스토리지입니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>Vertica:</strong> <span>오브젝트 스토리지에 저장된 Iceberg 테이블을 외부 테이블(External Table)로 직접 연결하여, 데이터를 이동시키지 않고도 고속 OLAP 분석 및 쿼리를 수행하는 분석 플랫폼입니다.</span></li>
    </ul>
</div>
    
<div class="architecture-subsection">
      <h3 class="section-subtitle">1. Spark 기반 Iceberg 테이블 생성</h3>
      <p class="section-description">Spark에서 S3 또는 MinIO 스토리지에 Iceberg 형식의 테이블을 생성하고 데이터를 삽입합니다. 스토리지 환경에 따라 <code>spark-defaults.conf</code> 파일을 아래와 같이 구성합니다.</p>

  <div class="syntax-box">
        <strong>AWS S3 연동 시 설정:</strong>
        <pre><code># Iceberg Catalog (Hadoop)
spark.sql.catalog.my_catalog=org.apache.iceberg.spark.SparkCatalog
spark.sql.catalog.my_catalog.type=hadoop
spark.sql.catalog.my_catalog.warehouse=s3a://iceberg-stg/warehouse/

# Iceberg Extensions
spark.sql.extensions=org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions

# AWS S3 / S3A 설정
spark.hadoop.fs.s3a.impl=org.apache.hadoop.fs.s3a.S3AFileSystem
spark.hadoop.fs.s3a.path.style.access=true
spark.hadoop.fs.s3a.connection.ssl.enabled=false
spark.hadoop.fs.s3a.endpoint=s3.ap-northeast-2.amazonaws.com
spark.hadoop.fs.s3a.access.key=&lt;YOUR_AWS_ACCESS_KEY&gt;
spark.hadoop.fs.s3a.secret.key=&lt;YOUR_AWS_SECRET_KEY&gt;

# 관련 Jar 파일 경로 (버전 및 환경에 맞게 지정)
spark.jars=/opt/spark/jars/iceberg-spark-runtime-3.5_2.12-1.5.2.jar,/opt/spark/jars/iceberg-aws-1.5.2.jar,...</code></pre>
      </div>

  <div class="syntax-box">
        <strong>On-Premise MinIO 연동 시 설정:</strong>
        <pre><code># Iceberg Catalog (Hadoop)
spark.sql.catalog.my_catalog=org.apache.iceberg.spark.SparkCatalog
spark.sql.catalog.my_catalog.type=hadoop
spark.sql.catalog.my_catalog.warehouse=s3a://iceberg-storage/warehouse/

# Iceberg Extensions
spark.sql.extensions=org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions

# MinIO / S3A 설정
spark.hadoop.fs.s3a.impl=org.apache.hadoop.fs.s3a.S3AFileSystem
spark.hadoop.fs.s3a.path.style.access=true
spark.hadoop.fs.s3a.connection.ssl.enabled=false
spark.hadoop.fs.s3a.endpoint=http://&lt;MINIO_IP&gt;:9000
spark.hadoop.fs.s3a.access.key=minioadmin
spark.hadoop.fs.s3a.secret.key=&lt;MINIO_SECRET_KEY&gt;

# 관련 Jar 파일 경로 (버전 및 환경에 맞게 지정)
spark.jars=/opt/spark/jars/hadoop-aws-3.3.4.jar,aws-java-sdk-bundle-1.12.391.jar,iceberg-spark-runtime-3.5_2.12-1.5.2.jar,...</code></pre>
      </div>
    </div>

<div class="architecture-subsection">
      <h3 class="section-subtitle">2. Vertica에서 Iceberg 데이터 조회</h3>
      <p class="section-description">Vertica에서 Iceberg 테이블을 조회하기 전에, 먼저 세션에 스토리지 접근을 위한 인증 정보를 설정해야 합니다. 이후 두 가지 주요 방법으로 데이터를 조회할 수 있습니다.</p>

  <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
        <h4 class="step-title">방법 1: Iceberg 테이블 직접 매핑 (STORED BY ICEBERG)</h4>
        <p class="section-description">Iceberg 테이블의 루트 경로를 직접 지정하여 외부 테이블을 생성합니다. 이 방식은 Iceberg의 메타데이터를 직접 읽어 스키마와 파티션 정보를 활용하는 가장 표준적인 방법입니다.</p>
        <div class="syntax-box">
          <strong>Vertica SQL 실행:</strong>
          <pre><code>-- 1. 스토리지 인증 정보 설정
ALTER SESSION SET AWSAuth='&lt;ACCESS_KEY&gt;:&lt;SECRET_KEY&gt;';
ALTER SESSION SET AWSEndpoint='http://&lt;STORAGE_IP&gt;:9000';
ALTER SESSION SET AWSRegion='us-east-1';

-- 2. Iceberg 테이블을 외부 테이블로 생성 (컬럼 타입은 자동 추론)
CREATE EXTERNAL TABLE user_ext
STORED BY ICEBERG LOCATION 's3a://iceberg-storage/warehouse/mydb/user_ext';

-- 3. 데이터 조회
SELECT * FROM user_ext;
--  id | name
-- ----+------
--   1 | John
--   2 | Jane</code></pre>
        </div>
      </div>

  <div class="step-section">
        <h4 class="step-title">방법 2: Parquet 파일 직접 조회 (INFER_TABLE_DDL)</h4>
        <p class="section-description">Iceberg 테이블의 데이터 파일(Parquet) 경로를 직접 지정하여 스키마를 추론하고, 이를 기반으로 외부 테이블을 생성하는 방식입니다. Iceberg 메타데이터를 거치지 않고 데이터 파일만 읽을 때 사용할 수 있습니다.</p>
        <div class="syntax-box">
          <strong>Vertica SQL 실행:</strong>
          <pre><code>-- 1. Parquet 파일로부터 테이블 DDL 추론
SELECT INFER_TABLE_DDL(
    's3a://iceberg-storage/warehouse/mydb/users/data/*parquet'
    USING PARAMETERS format = 'parquet', table_name='user_ext1', table_type = 'external'
);

-- 2. 추론된 DDL을 사용하여 외부 테이블 생성
CREATE EXTERNAL TABLE "user_ext1" (
 "id" INT,
 "name" VARCHAR
) AS COPY FROM 's3a://iceberg-storage/warehouse/mydb/user_ext/data/*parquet' PARQUET;

-- 3. 데이터 조회
SELECT * FROM user_ext1;
--  id | name
-- ----+------
--   2 | Jane
--   1 | John</code></pre>
        </div>
      </div>
    </div>
  </div>