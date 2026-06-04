---
title: "Vertica Apache Airflow 연동: 데이터 파이프라인 자동화 - Vertica Blog"
layout: default
description: "Apache Airflow를 사용하여 Vertica 데이터 파이프라인을 자동화하고 스케줄링하는 방법을 알아봅니다. Vertica Provider 설정 및 모니터링 DAG 작성 예제를 제공합니다."
keywords: "vertica, airflow, apache airflow, data pipeline, etl, scheduling, dag, verticahook"
canonical_url: "https://jin0326.github.io/vertica_blog/400utilization/400utilization#airflow"
---

<div id="airflow" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody">
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
</div>
</div>