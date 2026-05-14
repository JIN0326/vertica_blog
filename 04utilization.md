---
title: Vertica 활용 - Python, Iceberg, Airflow 연동 - Vertica Blog
layout: default
description: "Vertica를 다양한 데이터 생태계와 연동하여 활용하는 방법을 알아봅니다. Python, Apache Airflow, Iceberg, Trino 등과의 연동 예제를 제공합니다."
---

<div class="page-hero">
  <span class="chip">Utilization</span>
  <h1>Vertica Utilization</h1>
  <p>Python, Airflow, 그리고 Apache Iceberg와 Polaris를 활용한 엔터프라이즈 데이터 레이크하우스 구축 및 데이터 파이프라인 자동화 방법을 소개합니다.</p>
</div>

<div class="page-layout">
  <div class="content-section" markdown="1">

<div id="python" style="scroll-margin-top: 100px;"></div>

## Python 연동

<div class="architecture-section">
    <p class="section-description">Vertica는 Python 환경과의 원활한 연동을 위해 <strong><code>vertica-python</code></strong>(기본 드라이버)과 <strong><code>verticapy</code></strong>(데이터 과학 라이브러리) 두 가지 주요 라이브러리를 제공합니다.</p>
    
<div class="architecture-subsection">
      <h3 class="section-subtitle">1. 기본 연결 및 데이터 조회 (vertica-python)</h3>
      <p class="section-description"><code>vertica-python</code>은 DB-API 2.0을 준수하는 저수준(low-level) 드라이버로, SQL 쿼리를 실행하고 결과를 가져오는 기본적인 데이터베이스 접근에 사용됩니다. 더 자세한 정보는 <a href="https://github.com/vertica/vertica-python" target="_blank">공식 GitHub 저장소</a>에서 확인할 수 있습니다.</p>
      <div class="syntax-box">
        <strong>Pandas DataFrame으로 변환 예제:</strong>
        <pre><code>import vertica_python
import pandas as pd

conn_info = {
    'host': '&lt;DB_IP&gt;', 'port': 5433,
    'user': 'dbadmin', 'password': 'password', 'database': 'VDB'
}

with vertica_python.connect(**conn_info) as connection:
    cursor = connection.cursor()
    cursor.execute("SELECT * FROM public.sales")
    df = pd.DataFrame(cursor.fetchall(), columns=[desc[0] for desc in cursor.description])
    print(df.head())</code></pre>
      </div>
    </div>

    <div class="architecture-subsection">
      <h3 class="section-subtitle">2. 인데이터베이스 분석 (verticapy)</h3>
      <p class="section-description"><code>verticapy</code>는 Pandas와 유사한 인터페이스를 제공하는 고수준(high-level) 라이브러리로, 데이터를 클라이언트로 가져오지 않고 데이터베이스 내에서 직접 분석 및 머신러닝을 수행할 수 있게 해줍니다. 연결 시 고가용성(HA) 옵션을 설정할 수 있습니다.</p>
      <div class="syntax-box">
        <strong>vDataFrame 생성 및 사용 예제 (HA 포함):</strong>
        <pre><code>import verticapy as vp

# 고가용성 옵션을 포함한 연결 정보
conn_info = {
    "host": "&lt;DB_IP&gt;",
    "port": 5433,
    "database": "EONDB",
    "user": "vertica",
    "password": "your_password",
    "backup_server_node": ["&lt;BACKUP_IP_1&gt;:5433", "&lt;BACKUP_IP_2&gt;:5433"], 
    "connection_load_balance": True
}

# verticapy 연결 생성
vp.connect("my_vertica_conn", **conn_info)

# 'product_dimension' 테이블을 vDataFrame으로 로드
data = vp.vDataFrame("product_dimension")

# 데이터 확인
print(data.head())

# 연결 해제
vp.close_connect("my_vertica_conn")</code></pre>
      </div>
    </div>
  </div>

<hr style="margin: 3rem 0;">

<div id="airflow" style="scroll-margin-top: 100px;"></div>

## Apache Airflow 연동

<div class="architecture-section">
    <p class="section-description"><strong>Apache Airflow</strong>를 활용하면 Vertica 데이터베이스의 데이터 적재(ETL/ELT) 및 분석 쿼리 작업을 스케줄링하고 자동화된 데이터 파이프라인을 구축할 수 있습니다.</p>

<div class="architecture-subsection">
      <h3 class="section-subtitle">1. 필수 패키지 설치</h3>
      <p class="section-description">Airflow 환경에서 Vertica와 통신하기 위해 필요한 파이썬 라이브러리와 Airflow Provider를 설치합니다.</p>
      <div class="syntax-box">
        <pre><code># Vertica Python 클라이언트 및 SQLAlchemy 방언 설치
pip install vertica-python
pip install sqlalchemy-vertica-python


# Airflow용 Vertica Provider 설치
pip install apache-airflow-providers-vertica</code></pre>
      </div>
    </div>

<div class="architecture-subsection">
      <h3 class="section-subtitle">2. Airflow Connection 설정</h3>
      <p class="section-description">Airflow Web UI(기본 <code>http://&lt;IP&gt;:8080</code>)에 접속하여 Vertica 데이터베이스 접속 정보를 등록합니다.</p>
      <ol>
        <li>상단 메뉴에서 <strong>Admin > Connections</strong> 로 이동 후 <code>+</code> (Add a new record) 버튼을 클릭합니다.</li>
        <li><strong>Connection Id:</strong> <code>vertica_default</code> (코드에서 호출할 ID)</li>
        <li><strong>Connection Type:</strong> <code>Vertica</code></li>
        <li><strong>Host, Schema, Login, Password, Port</strong>(기본 5433) 정보를 입력하고 저장합니다.</li>
      </ol>
    </div>

<div class="architecture-subsection">
      <h3 class="section-subtitle">3. Vertica 모니터링 DAG 작성 예제</h3>
      <p class="section-description"><code>VerticaHook</code>을 사용하여 Vertica의 테이블 데이터 건수를 주기적으로 확인하고 로그로 남기는 기초적인 DAG(스케줄링 작업) 예제입니다.</p>
      
<div class="syntax-box">
        <strong>/root/airflow/dags/vertica_test.py</strong>
        <pre><code>from datetime import datetime, timedelta
import logging
from airflow import DAG
from airflow.operators.python import PythonOperator
from airflow.providers.vertica.hooks.vertica import VerticaHook

default_args = {
    'owner': 'airflow',
    'depends_on_past': False,
    'start_date': datetime.today(),
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

# 함수 정의: Vertica에 쿼리를 실행하고 결과를 반환
def get_vertica_status():
    # Airflow UI에서 등록한 Connection ID 사용
    hook = VerticaHook(vertica_conn_id='vertica_default')
    cur = hook.get_cursor()
    
    sql = "SELECT count(1) FROM store.store_dimension"
    cur.execute(sql)
    result = cur.fetchall()
    
    logging.info('--- Vertica Query Result ---')
    logging.info(f'Table Row Count: {result[0][0]}')
    
    return result[0][0]

# DAG 및 Task 구성
with DAG("vertica_monitoring_1", default_args=default_args, schedule_interval=None, catchup=False) as dag:
    
    check_vertica_task = PythonOperator(
        task_id='check_vertica_status',
        python_callable=get_vertica_status
  )</code></pre>
  </div>
    </div>
  </div>

<hr style="margin: 3rem 0;">

<div id="iceberg" style="scroll-margin-top: 100px;"></div>

## Apache Iceberg 연동 
<div class="architecture-section">
<div class="flow-diagram">
  <img src="{{ '/assets/images/util_iceberg.png' | relative_url }}" alt="Apache Iceberg">
</div>
<p class="section-description">Apache Iceberg는 대규모 분석 데이터를 위한 오픈 테이블 포맷으로, Vertica에서 이를 <strong>외부 테이블(External Table)</strong>로 조회하여 고속 분석을 수행할 수 있습니다.</p>
    
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

<hr style="margin: 3rem 0;">

<div id="polaris" style="scroll-margin-top: 100px;"></div>

## Apache Polaris 연동

<div class="architecture-section">
    <p class="section-description"><strong>Apache Polaris</strong>는 레이크하우스의 중앙 메타데이터 서비스 역할을 수행합니다. 테이블/뷰/네임스페이스를 추적하고, 메타데이터 위치를 유지하며, 역할 기반 접근 제어(RBAC)와 같은 엔터프라이즈급 거버넌스 기능을 제공합니다. Spark, Flink 등 다양한 컴퓨팅 엔진이 일관된 REST API를 통해 Polaris에 연결하여 데이터에 접근할 수 있습니다.</p>

  <div class="architecture-subsection">
      <h3 class="section-subtitle">아키텍처 구성도</h3>
      <p class="section-description">아래는 Spark, Polaris, MinIO, Vertica를 연동한 데이터 레이크하우스 아키텍처의 예시입니다.</p>
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

<hr style="margin: 3rem 0;">

<div id="aws-glue-pyiceberg" style="scroll-margin-top: 100px;"></div>

## AWS Glue & PyIceberg 연동

<div class="architecture-section">
  <p class="section-description"><strong>PyIceberg</strong>는 JVM(Java Virtual Machine) 없이 순수 Python 환경에서 Iceberg 테이블을 제어할 수 있게 해주는 라이브러리입니다. 이를 <strong>AWS Glue Catalog</strong>와 연동하면, 서버리스 환경에서 Iceberg 테이블의 메타데이터를 중앙에서 관리하고, Vertica에서 이를 동적으로 조회하여 분석할 수 있습니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">아키텍처 및 구성 요소</h3>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>PyIceberg:</strong> <span>Python 애플리케이션에서 Iceberg 테이블의 스키마를 정의하고 데이터를 적재하는 역할을 합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>AWS Glue Catalog:</strong> <span>S3에 저장된 데이터 파일의 구조, 위치 등 메타데이터를 관리하는 중앙 저장소 역할을 합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>Amazon S3:</strong> <span>실제 데이터 파일(Parquet 등)이 저장되는 오브젝트 스토리지입니다.</span></li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. AWS 환경 설정 (IAM, Glue, S3)</h3>
    <p class="section-description">PyIceberg와 Vertica가 AWS 리소스에 접근할 수 있도록 IAM 정책, 역할, 사용자를 생성하고, Glue 데이터베이스와 S3 버킷을 준비합니다.</p>
    <div class="syntax-box">
      <strong>IAM 정책(Policy) 생성 예시:</strong>
      <pre><code>{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "GlueCatalogAccess",
            "Effect": "Allow",
            "Action": [
                "glue:GetDatabase", "glue:CreateTable", "glue:UpdateTable",
                "glue:GetTable", "glue:DeleteTable", "glue:CreateDatabase", ...
            ],
            "Resource": [
                "arn:aws:glue:ap-northeast-2:&lt;AWS_ACCOUNT_ID&gt;:catalog",
                "arn:aws:glue:ap-northeast-2:&lt;AWS_ACCOUNT_ID&gt;:database/*",
                "arn:aws:glue:ap-northeast-2:&lt;AWS_ACCOUNT_ID&gt;:table/*"
            ]
        },
        {
            "Sid": "S3DataAccess",
            "Effect": "Allow",
            "Action": ["s3:GetObject", "s3:PutObject", "s3:DeleteObject", "s3:ListBucket"],
            "Resource": ["arn:aws:s3:::v-iceberg-2601", "arn:aws:s3:::v-iceberg-2601/*"]
        }
    ]
}</code></pre>
    </div>
    <ul class="feature-list" style="margin-top: 1rem;">
      <li><span class="feature-list__icon">💡</span> <strong>설정 절차:</strong> <span>1) 위 내용으로 IAM 정책 생성 → 2) 해당 정책을 부여한 IAM 역할(Role) 및 사용자(User) 생성 → 3) 생성된 사용자의 Access Key 발급 → 4) AWS Glue에서 데이터베이스 생성 → 5) S3에서 버킷 생성</span></li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. PyIceberg를 이용한 데이터 적재</h3>
    <p class="section-description">Python 스크립트를 사용하여 AWS Glue 카탈로그에 연결하고, Pandas DataFrame으로 생성한 샘플 데이터를 Iceberg 테이블로 적재합니다.</p>
    <div class="syntax-box">
      <strong>데이터 적재 Python 스크립트 예시 (test_connect.py):</strong>
      <pre><code>import pandas as pd
import pyarrow as pa
from pyiceberg.catalog import load_catalog

# 1. AWS 인증 정보 설정 (환경 변수)
# export AWS_ACCESS_KEY_ID="&lt;YOUR_ACCESS_KEY&gt;"
# export AWS_SECRET_ACCESS_KEY="&lt;YOUR_SECRET_KEY&gt;"
# export AWS_DEFAULT_REGION="ap-northeast-2"

# 2. Glue Catalog 연결
catalog = load_catalog(
    "default",
    **{
        "type": "glue",
        "warehouse": "s3://v-iceberg-2601/warehouse/"
    }
)

# 3. 네임스페이스(DB) 생성
catalog.create_namespace("my_iceberg_db")

# 4. 샘플 데이터 생성 및 테이블 스키마 정의
df = pd.DataFrame({"id": [1, 2, 3], "data": ["Hello", "Iceberg", "Vertica"]})
arrow_table = pa.Table.from_pandas(df)

# 5. Iceberg 테이블 생성 및 데이터 추가
table_name = "my_iceberg_db.test_table"
table = catalog.create_table(table_name, schema=arrow_table.schema)
table.append(arrow_table)

print("데이터 적재 성공!")
loaded_table = catalog.load_table(table_name)
print(f"조회 결과:\n{loaded_table.scan().to_pandas()}")</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. Vertica와 Glue 카탈로그 연동 조회</h3>
    <p class="section-description">Vertica에서 AWS 인증 정보를 설정한 후, <code>STORED BY ICEBERG</code> 구문을 사용하여 Glue 카탈로그에 등록된 Iceberg 테이블을 외부 테이블로 조회합니다.</p>
    <div class="syntax-box">
      <strong>Vertica SQL 실행 예시:</strong>
      <pre><code>-- 1. Vertica 세션에 AWS 인증 정보 설정
ALTER SESSION SET AWSAuth='&lt;YOUR_ACCESS_KEY&gt;:&lt;YOUR_SECRET_KEY&gt;'; 
ALTER SESSION SET AWSRegion='ap-northeast-2';
ALTER SESSION SET AWSEndpoint = 's3.ap-northeast-2.amazonaws.com';

-- S3 경로 포맷을 맞추기 위한 매핑 설정
ALTER SESSION SET IcebergPathMapping = '{"s3://v-iceberg-2601/":"s3a://v-iceberg-2601/"}';

-- 2. Glue 카탈로그 정보를 참조하는 외부 테이블 생성
CREATE EXTERNAL TABLE ext_iceberg_test 
STORED BY ICEBERG 
LOCATION 's3a://v-iceberg-2601/warehouse/my_iceberg_db.db/test_table' -- 테이블 루트 경로
GLUE_DB 'my_iceberg_db' -- Glue 데이터베이스명
GLUE_TABLE 'test_table'; -- Glue 테이블명

-- 3. 데이터 조회
SELECT * FROM ext_iceberg_test;
--  id |  data
-- ----+---------
--   1 | Hello
--   2 | Iceberg
--   3 | Vertica</code></pre>
    </div>
    <ul class="feature-list" style="margin-top: 1rem;">
      <li><span class="feature-list__icon">💡</span> <strong>자동 메타데이터 동기화:</strong> <span><code>GLUE_DB</code>와 <code>GLUE_TABLE</code> 옵션을 사용하면, Iceberg 테이블의 스키마가 변경되거나 데이터가 추가되어도 Vertica에서 별도의 DDL 수정 없이 최신 상태를 자동으로 읽어올 수 있습니다.</span></li>
    </ul>
  </div>
</div>

<hr style="margin: 3rem 0;">

<div id="trino-iceberg" style="scroll-margin-top: 100px;"></div>

## Trino & Iceberg 연동

<div class="architecture-section">
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
</div>
<aside class="page-sidebar">
    <div class="sidebar-panel" style="padding-right: 1rem;">
      <h3>On this page</h3>
      <ul>
        <li><a href="#python">Python 연동</a></li>
        <li><a href="#airflow">Apache Airflow 연동</a></li>
        <li><a href="#iceberg">Apache Iceberg 연동</a></li>
        <li><a href="#polaris">Apache Polaris 연동</a></li>
        <li><a href="#aws-glue-pyiceberg">AWS Glue & PyIceberg 연동</a></li>
        <li><a href="#trino-iceberg">Trino & Iceberg 연동</a></li>
      </ul>
    </div>
  </aside>
</div>