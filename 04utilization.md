---
title: Vertica Utilization
layout: default
---

<div class="page-hero">
  <span class="chip">Utilization</span>
  <h1>Vertica Utilization</h1>
  <p>클라이언트 접속, 모니터링, Python, 그리고 Apache Iceberg와 Polaris를 활용한 엔터프라이즈 데이터 레이크하우스 구축 방법을 소개합니다.</p>
</div>

<div class="page-layout">
  <div class="content-section" markdown="1">

<div id="connecting" style="scroll-margin-top: 100px;"></div>

## 클라이언트 접속

<div class="architecture-section">
    <p class="section-description">Vertica는 <code>vsql</code>, <code>DBeaver</code> 등 다양한 클라이언트 도구를 지원하여 사용자가 편리하게 데이터베이스에 접속할 수 있도록 합니다.</p>
    
    <div class="architecture-subsection">
      <h3 class="section-subtitle">vsql (CLI)</h3>
      <p class="section-description"><code>vsql</code>은 Vertica와 함께 제공되는 공식 커맨드 라인 클라이언트입니다.</p>
      <div class="syntax-box">
        <strong>접속 예시:</strong>
        <pre><code>/opt/vertica/bin/vsql -h &lt;DB_IP&gt; -d &lt;db_name&gt; -U &lt;user_name&gt;</code></pre>
      </div>
    </div>
    
    <div class="architecture-subsection">
      <h3 class="section-subtitle">DBeaver (GUI)</h3>
      <p class="section-description">DBeaver와 같은 GUI 도구는 Vertica JDBC 드라이버를 사용하여 쉽게 연결할 수 있습니다.</p>
      <ol>
        <li>Vertica 공식 웹사이트에서 JDBC 드라이버(<code>.jar</code> 파일)를 다운로드합니다.</li>
        <li>DBeaver의 <code>드라이버 관리자</code>에서 다운로드한 <code>.jar</code> 파일을 추가하여 새 드라이버를 설정합니다.</li>
      </ol>
    </div>
  </div>
  
  <hr style="margin: 3rem 0;">

  <div id="monitoring" style="scroll-margin-top: 100px;"></div>

## 모니터링

<div class="architecture-section">
    <p class="section-description">안정적인 데이터베이스 운영을 위해서는 Management Console(MC)과 Grafana를 통한 지속적인 모니터링이 필수적입니다.</p>

  <div class="architecture-subsection">
      <h3 class="section-subtitle">1. 모니터링 콘솔 (MC)</h3>
      <p class="section-description">Management Console(MC)은 웹 브라우저 기반의 통합 모니터링 및 클러스터 관리 도구입니다.</p>
      
  <div class="image-box-styled">
        <img src="{{ '/assets/images/monitoring_1.png' | relative_url }}" alt="Vertica Management Console (MC) 화면">
      </div>

  <dl class="feature-dl">
        <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 핵심 기능</dt>
        <dd class="feature-dd">
          <strong>Overview & Activity:</strong> 하드웨어 자원과 리소스 풀 사용량, 쿼리 상태를 실시간으로 확인합니다.<br>
          <strong>Query Execution:</strong> UI 내에서 직접 SQL을 실행하고 시각적인 트리 구조로 실행 계획을 분석합니다.
        </dd>
      </dl>
    </div>
  </div>
  
  <hr style="margin: 3rem 0;">

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

## Apache Polaris

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

## PyIceberg & AWS Glue 

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

  </div>

  <aside class="page-sidebar">
    <div class="sidebar-panel" style="padding-right: 1rem;">
      <h3>On this page</h3>
      <ul>
        <li><a href="#connecting">클라이언트 접속</a></li>
        <li><a href="#monitoring">모니터링</a></li>
        <li><a href="#python">Python 연동</a></li>
        <li><a href="#iceberg">Apache Iceberg 연동</a></li>
        <li><a href="#polaris">Apache Polaris 연동</a></li>
        <li><a href="#pyiceberg">PyIceberg & AWS Glue</a></li>
      </ul>
    </div>
  </aside>
</div>