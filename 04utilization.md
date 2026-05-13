---
title: Vertica Utilization
layout: default
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
    <p class="section-description"><code>vertica-python</code>은 별도의 드라이버 없이 Python 환경에서 Vertica에 쉽게 연결할 수 있게 해주는 클라이언트입니다.</p>
    
<div class="architecture-subsection">
      <h3 class="section-subtitle">사용 예제</h3>
      <div class="syntax-box">
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
      <p style="font-size: 0.9rem; color: #666; margin-top: 10px;">
        ※ 첨부해주신 원본 스크립트는 Airflow가 DAG을 파싱할 때마다 쿼리가 실행되는 구조였습니다. 이를 실제 스케줄러가 Task를 실행할 때만 작동하도록 <code>PythonOperator</code>를 사용하는 <strong>표준 Airflow 문법</strong>으로 개선하여 작성했습니다.
      </p>
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
      <p class="section-description">Spark에서 S3 또는 MinIO 스토리지에 Iceberg 형식의 테이블을 생성하고 데이터를 삽입합니다.</p>

      <div class="syntax-box">
        <strong>spark-defaults.conf 설정</strong>
        <pre><code>spark.sql.catalog.my_catalog.type=hadoop
spark.sql.catalog.my_catalog.warehouse=s3a://iceberg-storage/warehouse/
spark.hadoop.fs.s3a.endpoint=http://&lt;STG_IP&gt;:9000
spark.hadoop.fs.s3a.access.key=minioadmin
spark.hadoop.fs.s3a.secret.key=stg12345!</code></pre>
      </div>
    </div>

<div class="architecture-subsection">
      <h3 class="section-subtitle">2. Vertica에서 Iceberg 데이터 조회</h3>
      <p class="section-description">Vertica 세션에 스토리지 인증 정보를 설정하고, Iceberg 메타데이터 위치를 참조하여 외부 테이블을 생성합니다.</p>

      <div class="syntax-box">
        <strong>Vertica SQL 실행</strong>
        <pre><code>=> ALTER SESSION SET AWSAuth='minioadmin:stg12345!';
=> ALTER SESSION SET AWSEndpoint='&lt;STG_IP&gt;:9000';

=> CREATE EXTERNAL TABLE user_ext 
   STORED BY ICEBERG LOCATION 's3://iceberg-storage/warehouse/mydb/user_ext/'
   COLUMN TYPES (id int, name varchar);

=> SELECT * FROM user_ext;</code></pre>
      </div>
    </div>
  </div>

<hr style="margin: 3rem 0;">

<div id="polaris" style="scroll-margin-top: 100px;"></div>

## Apache Polaris 연동

<div class="architecture-section">
<div class="flow-diagram">
  <img src="{{ '/assets/images/util_polaris.png' | relative_url }}" alt="Apache Polaris">
</div>
    <p class="section-description"><strong>Apache Polaris</strong>는 레이크하우스의 중앙 메타데이터 서비스 역할을 수행하며, 자격 증명 제공(credential vending) 및 역할 기반 접근 제어(RBAC) 기능을 지원합니다.</p>

<div class="architecture-subsection">
      <h3 class="section-subtitle">1. Polaris REST API 관리 예제</h3>
      <div class="syntax-box">
        <strong>인증 토큰 발급 및 카탈로그 생성</strong>
        <p>OAuth2 API를 통해 관리 토큰을 발급받고, 데이터 저장소 정보를 포함한 카탈로그를 생성합니다.</p>
        <pre><code># 토큰 발급
TOKEN=$(curl -s -X POST http://&lt;META_IP&gt;:8181/api/catalog/v1/oauth/tokens \
  -d "grant_type=client_credentials&client_id=&lt;ID&gt;&client_secret=&lt;KEY&gt;&scope=PRINCIPAL_ROLE:ALL" \
  | sed -E 's/.*"access_token":"([^"]+)".*/\1/')

# 카탈로그 생성 (MinIO 엔드포인트 포함)
curl -X POST http://&lt;META_IP&gt;:8181/api/management/v1/catalogs \
  -H "Authorization: Bearer $TOKEN" -H "Content-Type: application/json" \
  -d '{
    "catalog": {
      "name": "polaris", "type": "INTERNAL",
      "storageConfigInfo": {
        "storageType": "S3", "endpoint": "http://&lt;STG_IP&gt;:9000",
        "allowedLocations": ["s3a://iceberg-polaris/warehouse/"]
      }
    }
  }'</code></pre>
      </div>
    </div>

<div class="architecture-subsection">
      <h3 class="section-subtitle">2. Vertica 동적 메타데이터 연동 실습</h3>
      <p class="section-description">Vertica는 Polaris API를 호출하여 테이블의 <strong>최신 메타데이터 JSON 경로</strong>를 동적으로 획득할 수 있습니다.</p>

      <div class="syntax-box">
        <strong>메타데이터 위치 조회 API 호출</strong>
        <pre><code>curl -s -H "Authorization: Bearer $TOKEN" \
  http://&lt;META_IP&gt;:8181/api/catalog/v1/polaris/namespaces/mydb/tables/test_table \
  | jq -r '."metadata-location"'

# 결과: s3a://iceberg-polaris/warehouse/mydb/test_table/metadata/00001-...metadata.json</code></pre>
      </div>

      <div class="syntax-box">
        <strong>Vertica 외부 테이블 매핑</strong>
        <pre><code>=> ALTER SESSION SET AWSEndpoint='&lt;STG_IP&gt;:9000';
=> CREATE EXTERNAL TABLE polaris_test
   STORED BY ICEBERG 
   LOCATION 's3://iceberg-polaris/warehouse/mydb/test_table/metadata/00001-...metadata.json';</code></pre>
      </div>
    </div>
  </div>

<hr style="margin: 3rem 0;">

<div id="pyiceberg" style="scroll-margin-top: 100px;"></div>

## PyIceberg & AWS Glue 연동

<div class="architecture-section">
<div class="flow-diagram">
  <img src="{{ '/assets/images/util_pyiceberg.png' | relative_url }}" alt="PyIceberg and AWS Glue">
</div>
    <p class="section-description"><strong>PyIceberg</strong>는 JVM 없이 순수 Python 환경에서 Iceberg 테이블을 제어하며, AWS Glue 카탈로그와 연동하여 서버리스 데이터 운영을 가능하게 합니다.</p>

<div class="architecture-subsection">
      <h3 class="section-subtitle">1. Python 데이터 적재 실습</h3>
      <div class="syntax-box">
        <strong>Glue Catalog 연동 코드 (test_connect.py)</strong>
        <pre><code>from pyiceberg.catalog import load_catalog
import pandas as pd
import pyarrow as pa

catalog = load_catalog("default", **{"type": "glue", "s3.region": "ap-northeast-2", "warehouse": "s3://v-iceberg-2601/warehouse/"})

df = pd.DataFrame({"id": [1, 2, 3], "data": ["Hello", "Iceberg", "Vertica"]})
table = catalog.create_table("my_iceberg_db.test_table", schema=pa.Table.from_pandas(df).schema)
table.append(pa.Table.from_pandas(df))</code></pre>
      </div>
    </div>

<div class="architecture-subsection">
      <h3 class="section-subtitle">2. Vertica Glue 통합 조회</h3>
      <p class="section-description">Vertica의 <code>GLUE_DB</code>와 <code>GLUE_TABLE</code> 옵션을 사용하면 스키마 변경 시에도 메타데이터 경로 수정 없이 자동 동기화 조회가 가능합니다.</p>
      <div class="syntax-box">
        <pre><code>=> ALTER SESSION SET IcebergPathMapping = '{"s3://v-iceberg-2601/":"s3a://v-iceberg-2601/"}';

=> CREATE EXTERNAL TABLE ext_iceberg_test 
   STORED BY ICEBERG 
   LOCATION 's3a://v-iceberg-2601/warehouse/my_iceberg_db.db/test_table'
   GLUE_DB 'my_iceberg_db'
   GLUE_TABLE 'test_table';</code></pre>
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
        <li><a href="#pyiceberg">PyIceberg & AWS Glue 연동</a></li>
      </ul>
    </div>
  </aside>
</div>