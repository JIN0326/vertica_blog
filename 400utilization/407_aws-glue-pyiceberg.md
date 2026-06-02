---
title: "Vertica, AWS Glue, PyIceberg 연동: 서버리스 데이터 운영 - Vertica Blog"
layout: default
description: "PyIceberg와 AWS Glue Catalog를 연동하여 서버리스 환경에서 Iceberg 테이블을 관리하고, Vertica에서 동적으로 조회하여 분석하는 방법을 알아봅니다."
keywords: "vertica, pyiceberg, aws glue, glue catalog, serverless, s3, iam"
---

<div id="aws-glue-pyiceberg" style="scroll-margin-top: 100px;"></div>

## AWS Glue & PyIceberg 연동

<div class="architecture-section">
  <div class="flow-diagram">
    <img src="/vertica_blog/assets/images/util_pyiceberg.png" alt="PyIceberg and AWS Glue">
  </div>
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