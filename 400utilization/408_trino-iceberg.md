---
title: "Vertica, Trino, Iceberg 연동: 연합 쿼리(Federated Query) - Vertica Blog"
layout: default
description: "Trino, Iceberg, Vertica를 연동하여 데이터 이동 없이 분산된 데이터를 통합 분석하는 연합 쿼리(Federated Query) 아키텍처 구축 방법을 알아봅니다."
keywords: "vertica, trino, prestosql, iceberg, federated query, hive metastore"
---

<div id="trino-iceberg" style="scroll-margin-top: 100px;"></div>

## Trino & Iceberg 연동

<div class="architecture-section">
<div class="flow-diagram">
      <img src="/vertica_blog/assets/images/util_trino.png" alt="Apache Polaris">
    </div>
  <p class="section-description"><strong>Trino(구 PrestoSQL)</strong>는 데이터가 S3, MySQL, Vertica 등 어디에 있든 이동시키지 않고 즉시 SQL로 조회할 수 있게 해주는 분산 SQL 쿼리 엔진입니다. Trino와 <strong>Apache Iceberg</strong>를 연동하면, 서로 다른 저장소에 있는 데이터를 하나의 SQL로 조인하는 강력한 <strong>연합 쿼리(Federated Query)</strong> 데이터 레이크하우스를 구축할 수 있습니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">아키텍처 및 구성 요소</h3>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>Trino:</strong> <span>사용자의 SQL을 받아 MetaStore에서 위치를 찾고 데이터 저장소에서 읽어 처리하는 컴퓨팅 엔진입니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>Hive Metastore:</strong> <span>어떤 파일이 어떤 테이블의 데이터인지 메타데이터를 관리하는 카탈로그 관리자 역할을 합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>PostgreSQL:</strong> <span>Hive Metastore의 실제 메타데이터가 영구적으로 저장되는 카탈로그 저장소입니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>Apache Iceberg:</strong> <span>S3 저장소 내의 메타데이터 경로, 테이블 스키마, 데이터 파일의 위치를 추적하는 테이블 관리 포맷입니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>MinIO (S3):</strong> <span>실제 Parquet 등의 데이터 파일이 보관되는 오브젝트 저장소입니다.</span></li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. Metastore 및 카탈로그 저장소 구성</h3>
    <p class="section-description">Hive Metastore와 PostgreSQL을 설치하여 Iceberg 테이블의 메타데이터를 관리할 기반을 마련합니다.</p>
    
  <div class="syntax-box">
      <strong>PostgreSQL 및 Hive Metastore 설정 예시:</strong>
      <pre><code># 1. PostgreSQL 설치 및 DB/사용자 생성
dnf install -y postgresql-server
postgresql-setup --initdb
systemctl enable --now postgresql

sudo -u postgres psql -c "CREATE USER hive WITH PASSWORD '&lt;HIVE_PASSWORD&gt;';"
sudo -u postgres psql -c "CREATE DATABASE metastore OWNER hive;"

# 2. Hive Metastore 설치 및 metastore-site.xml 구성
&lt;configuration&gt;
    &lt;!-- Metastore 서버 주소 --&gt;
    &lt;property&gt;
        &lt;name&gt;metastore.thrift.uris&lt;/name&gt; &lt;value&gt;thrift://0.0.0.0:9083&lt;/value&gt;
    &lt;/property&gt;
    &lt;!-- PostgreSQL 연결 정보 --&gt;
    &lt;property&gt;
        &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt; &lt;value&gt;jdbc:postgresql://localhost:5432/metastore&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt; &lt;value&gt;hive&lt;/value&gt;
    &lt;/property&gt;
    &lt;!-- MinIO(S3) 접속 정보 --&gt;
    &lt;property&gt;
        &lt;name&gt;fs.s3a.endpoint&lt;/name&gt; &lt;value&gt;http://&lt;MINIO_IP&gt;:9000&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;fs.s3a.access.key&lt;/name&gt; &lt;value&gt;&lt;MINIO_ACCESS_KEY&gt;&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;fs.s3a.secret.key&lt;/name&gt; &lt;value&gt;&lt;MINIO_SECRET_KEY&gt;&lt;/value&gt;
    &lt;/property&gt;
&lt;/configuration&gt;

# 3. Metastore 스키마 초기화 및 실행
/opt/metastore/bin/schematool -initSchema -dbType postgres
nohup /opt/metastore/bin/start-metastore &gt; /var/log/metastore.log 2&gt;&1 &</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. Trino 엔진 및 Iceberg 연동 설정</h3>
    <p class="section-description">Trino 서버를 구성하고, Hive Metastore를 통해 Iceberg 테이블에 접근할 수 있도록 Iceberg 커넥터를 설정합니다.</p>
    
  <div class="syntax-box">
      <strong>iceberg.properties 설정 (/opt/trino/etc/catalog/):</strong>
      <pre><code>connector.name=iceberg
iceberg.catalog.type=hive_metastore
hive.metastore.uri=thrift://&lt;METASTORE_IP&gt;:9083

fs.native-s3.enabled=true
s3.endpoint=http://&lt;MINIO_IP&gt;:9000
s3.region=us-east-1
s3.path-style-access=true
s3.aws-access-key=&lt;MINIO_ACCESS_KEY&gt;
s3.aws-secret-key=&lt;MINIO_SECRET_KEY&gt;

iceberg.file-format=PARQUET</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. Vertica와 Trino의 연동 및 교차 쿼리</h3>
    <p class="section-description">Trino에 Vertica 커넥터를 추가하여 두 엔진 간의 자유로운 데이터 조회 및 조인을 수행합니다. 이를 통해 데이터 이동 없이도 분산된 데이터를 통합하여 분석할 수 있습니다.</p>
    
  <div class="syntax-box">
      <strong>vertica.properties 설정 (/opt/trino/etc/catalog/):</strong>
      <pre><code>connector.name=vertica
connection-url=jdbc:vertica://&lt;VERTICA_IP&gt;:5433/&lt;DB_NAME&gt;
connection-user=&lt;VERTICA_USER&gt;
connection-password=&lt;VERTICA_PASSWORD&gt;</code></pre>
    </div>

  <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> Vertica에서 Trino(Iceberg) 데이터 읽기</dt>
      <dd class="feature-dd">
        외부 테이블(External Table)을 생성하여 Iceberg 테이블의 메타데이터 JSON을 직접 매핑하여 조회합니다.<br>
        <code>CREATE EXTERNAL TABLE trino_test STORED BY ICEBERG LOCATION 's3://iceberg-bucket/test/sensor_data/.../metadata.json';</code>
      </dd>
      
  <dt class="feature-dt"><span class="feature-dt__icon">◆</span> Trino에서 Vertica 테이블 읽기 및 복제</dt>
      <dd class="feature-dd">
        Trino를 통해 Vertica의 데이터를 읽어와 새로운 Iceberg 테이블로 쉽게 복제할 수 있습니다.<br>
        <code>CREATE TABLE iceberg.testdb.archive_sales AS SELECT * FROM vertica.public.sales_data;</code>
      </dd>

  <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 연합 쿼리(Federated Join)</dt>
      <dd class="feature-dd">
        Trino 엔진을 통해 Vertica에 있는 데이터와 S3에 있는 Iceberg 데이터를 즉시 조인하여 분석합니다.
        <pre style="margin-top: 10px;"><code>SELECT v.col1, i.reading, i.event_time
FROM vertica.public.sales_data v
JOIN iceberg.testdb.sensor_data i ON v.id = i.sensor_id
WHERE i.reading > 20.0;</code></pre>
      </dd>
    </dl>
  </div>
</div>