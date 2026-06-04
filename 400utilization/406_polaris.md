---
title: "Vertica Apache Polaris 연동: 중앙 메타데이터 관리 - Vertica Blog"
layout: default
description: "Vertica와 중앙 메타데이터 서비스인 Apache Polaris를 연동하는 방법을 알아봅니다. Polaris 서버 구성, Spark 연동, Vertica에서의 동적 메타데이터 조회를 설명합니다."
keywords: "vertica, apache polaris, iceberg, metadata catalog, rest catalog, spark"
canonical_url: "https://jin0326.github.io/vertica_blog/400utilization/400utilization#polaris"
---

<div id="polaris" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody">
## Apache Polaris 연동

<div class="architecture-section">
    <div class="flow-diagram">
      <img src="/vertica_blog/assets/images/util_polaris.png" alt="Apache Polaris">
    </div>
    <p class="section-description"><strong>Apache Polaris</strong>는 레이크하우스의 중앙 메타데이터 서비스 역할을 수행합니다. 테이블/뷰/네임스페이스를 추적하고, 메타데이터 위치를 유지하며, 역할 기반 접근 제어(RBAC)와 같은 엔터프라이즈급 거버넌스 기능을 제공합니다. Spark, Flink 등 다양한 컴퓨팅 엔진이 일관된 REST API를 통해 Polaris에 연결하여 데이터에 접근할 수 있습니다.</p>

  <div class="architecture-subsection">
      <h3 class="section-subtitle">아키텍처 및 구성 요소</h3>
      <p class="section-description">Spark, Polaris, MinIO, Vertica를 연동한 데이터 레이크하우스 아키텍처의 예시입니다.</p>
      <ul class="feature-list">
        <li><span class="feature-list__icon">🔹</span> <strong>Spark Cluster:</strong> <span>데이터 처리 및 Iceberg 테이블 생성을 담당하는 컴퓨팅 엔진입니다.</span></li>
        <li><span class="feature-list__icon">🔹</span> <strong>Polaris Catalog:</strong> <span>Iceberg 테이블의 메타데이터를 관리하는 중앙 카탈로그 서버입니다.</span></li>
        <li><span class="feature-list__icon">🔹</span> <strong>PostgreSQL:</strong> <span>Polaris의 메타데이터를 영구적으로 저장하는 데이터베이스입니다.</span></li>
        <li><span class="feature-list__icon">🔹</span> <strong>MinIO Storage:</strong> <span>Iceberg 데이터 파일(Parquet 등)이 저장되는 오브젝트 스토리지입니다.</span></li>
        <li><span class="feature-list__icon">🔹</span> <strong>Vertica Cluster:</strong> <span>오브젝트 스토리지의 Iceberg 테이블을 외부 테이블로 직접 조회하여 고성능 분석을 수행합니다.</span></li>
      </ul>
    </div>

   <div class="architecture-subsection">
      <h3 class="section-subtitle">1. Polaris 서버 구성 (사전 준비 및 설치)</h3>
      <p class="section-description">Polaris를 실행할 서버에 Java, PostgreSQL 등 필요한 패키지를 설치하고, 메타데이터 저장을 위한 데이터베이스를 설정합니다.</p>
      <div class="syntax-box">
        <strong>PostgreSQL 설정 및 Polaris 서비스 등록 예시:</strong>
        <pre><code># 1. PostgreSQL 설치 및 DB/사용자 생성
sudo -u postgres psql &lt;&lt;EOF
CREATE USER polaris WITH PASSWORD '&lt;DB_PASSWORD&gt;';
CREATE DATABASE polaris OWNER polaris;
GRANT ALL PRIVILEGES ON DATABASE polaris TO polaris;
EOF

# 2. Polaris 소스 클론 및 빌드
cd /opt
git clone https://github.com/apache/polaris.git
cd /opt/polaris
./gradlew :polaris-server:quarkusBuild -Dquarkus.package.type=fast-jar -PincludeEclipseLink

# 3. Polaris systemd 서비스 등록 (/etc/systemd/system/polaris.service)
[Unit]
Description=Polaris Server
After=network.target postgresql.service

[Service]
Type=simple
User=root
WorkingDirectory=/opt/polaris/runtime/server/build/quarkus-build/gen/quarkus-app
ExecStart=/usr/lib/jvm/java-21-openjdk/bin/java \
  -Dquarkus.datasource.jdbc.url=jdbc:postgresql://localhost:5432/polaris \
  -Dquarkus.datasource.username=polaris \
  -Dquarkus.datasource.password=&lt;DB_PASSWORD&gt; \
  -Dpolaris.bootstrap=true \
  -jar quarkus-run.jar
Restart=on-failure

[Install]
WantedBy=multi-user.target

# 4. 서비스 실행
systemctl daemon-reload
systemctl enable --now polaris</code></pre>
      </div>
    </div>

  <div class="architecture-subsection">
      <h3 class="section-subtitle">2. Spark 연동 및 Iceberg 테이블 생성</h3>
      <p class="section-description">Spark가 Polaris 카탈로그를 통해 Iceberg 테이블을 생성하고 관리할 수 있도록 <code>spark-defaults.conf</code> 파일을 구성합니다.</p>
      <div class="syntax-box">
        <strong>spark-defaults.conf 설정 예시:</strong>
        <pre><code># Iceberg Extension 및 Polaris REST Catalog 설정
spark.sql.extensions                   org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions
spark.sql.catalog.polaris              org.apache.iceberg.spark.SparkCatalog
spark.sql.catalog.polaris.type         rest
spark.sql.catalog.polaris.uri          http://&lt;POLARIS_IP&gt;:8181/api/catalog
spark.sql.catalog.polaris.credential   &lt;CLIENT_ID&gt;:&lt;CLIENT_SECRET&gt;
spark.sql.catalog.polaris.warehouse    polaris

# S3A / MinIO 설정
spark.hadoop.fs.s3a.endpoint           http://&lt;MINIO_IP&gt;:9000
spark.hadoop.fs.s3a.access.key         &lt;MINIO_ACCESS_KEY&gt;
spark.hadoop.fs.s3a.secret.key         &lt;MINIO_SECRET_KEY&gt;
...</code></pre>
      </div>
      <div class="syntax-box">
        <strong>Spark Shell을 이용한 테이블 생성:</strong>
        <pre><code># Spark Shell 실행
/opt/spark/bin/spark-shell

# 네임스페이스(DB) 생성 및 테이블 생성/데이터 삽입
scala> spark.sql("USE polaris")
scala> spark.sql("CREATE NAMESPACE IF NOT EXISTS polaris.mydb")
scala> spark.sql("CREATE TABLE polaris.mydb.test_table (id INT, data STRING) USING iceberg")
scala> spark.sql("INSERT INTO polaris.mydb.test_table VALUES (1, 'Hello Polaris'), (2, 'Iceberg Success')")
scala> spark.sql("SELECT * FROM polaris.mydb.test_table").show()</code></pre>
      </div>
    </div>

  <div class="architecture-subsection">
      <h3 class="section-subtitle">3. Vertica 동적 메타데이터 연동</h3>
      <p class="section-description">Vertica는 Polaris REST API를 호출하여 테이블의 최신 메타데이터 JSON 경로를 동적으로 획득한 후, 이를 기반으로 외부 테이블을 생성하여 데이터를 조회합니다.</p>
      <div class="syntax-box">
        <strong>메타데이터 위치 조회 및 Vertica 테이블 생성:</strong>
        <pre><code># 1. Polaris API로 최신 메타데이터 파일 경로 조회
TOKEN=$(curl -s -X POST http://&lt;POLARIS_IP&gt;:8181/api/catalog/v1/oauth/tokens \
  -d "grant_type=client_credentials&client_id=&lt;CLIENT_ID&gt;&client_secret=&lt;CLIENT_SECRET&gt;&scope=PRINCIPAL_ROLE:ALL" | sed -E 's/.*"access_token":"([^"]+)".*/\1/')
  
METADATA_LOCATION=$(curl -s -H "Authorization: Bearer $TOKEN" \
      http://&lt;POLARIS_IP&gt;:8181/api/catalog/v1/polaris/namespaces/mydb/tables/test_table | jq -r '."metadata-location"')

# 2. Vertica에서 외부 테이블 생성
-- 스토리지 인증 정보 설정
ALTER SESSION SET AWSAuth='&lt;MINIO_ACCESS_KEY&gt;:&lt;MINIO_SECRET_KEY&gt;';
ALTER SESSION SET AWSEndpoint='http://&lt;MINIO_IP&gt;:9000';

-- 조회된 메타데이터 경로를 사용하여 외부 테이블 생성
CREATE EXTERNAL TABLE polaris_test
STORED BY ICEBERG 
LOCATION '$METADATA_LOCATION';

-- 3. 데이터 조회
SELECT * FROM polaris_test;
--  id |      data
-- ----+-----------------
--   2 | Iceberg Success
--   1 | Hello Polaris</code></pre>
      </div>
    </div>
  </div>
</div>
</div>