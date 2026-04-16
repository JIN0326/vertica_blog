---
title: Vertica Utilization
layout: default
---

<div class="page-hero">
  <span class="chip">Utilization</span>
  <h1>Vertica Utilization</h1>
  <p>클라이언트 접속, 모니터링, Python 연동 등 Vertica를 실제 환경에서 활용하는 다양한 방법을 소개합니다.</p>
</div>

<div class="page-layout">
  <div class="content-section" markdown="1">

  <div id="connecting" style="scroll-margin-top: 100px;"></div>
  
  ## 클라이언트 접속
  Vertica는 `vsql`, `DBeaver` 등 다양한 클라이언트 도구를 지원하여      사용자가 편리하게 데이터베이스에 접속할 수 있도록 합니다.

  #### vsql (CLI)
  `vsql`은 Vertica와 함께 제공되는 공식 커맨드 라인 클라이언트입니다.
  ```bash
  /opt/vertica/bin/vsql -h <host_ip> -d <db_name> -U <user_name>
  ```

  #### DBeaver (GUI)
  DBeaver와 같은 GUI 도구는 Vertica JDBC 드라이버를 사용하여 쉽게       연결할 수 있습니다.
  1.  Vertica 공식 웹사이트에서 JDBC 드라이버(`.jar` 파일)를      다운로드합니다.
  2.  DBeaver의 `드라이버 관리자`에서 다운로드한 `.jar` 파일을  추가하여     새 드라이버를 설정합니다.
  3.  설정한 드라이버를 사용하여 새 데이터베이스 연결을 생성합니다.

  <hr style="margin: 3rem 0;">

  <div id="monitoring" style="scroll-margin-top: 100px;"></div>
  ## 모니터링 및 Grafana
  안정적인 데이터베이스 운영을 위해서는 시스템의 상태를 지속적으로      모니터링하는 것이 필수적입니다. Vertica는 `v_monitor`와   `v_catalog`   스키마에 다양한 시스템 테이블을 제공합니다.

  #### Grafana를 이용한 시각화
  시스템 테이블의 데이터를 주기적으로 수집하여 Grafana와 같은 시각화      도구로 대시보드를 만들면, 시스템 상태를 직관적으로 파악하고 이상  징후를   빠르게 감지할 수 있습니다.

  1.  **데이터 수집 (Exporter)**: Python 스크립트나 오픈소스      Exporter를 사용하여 Vertica 메트릭을 수집합니다.
  2.  **저장 (TSDB)**: 수집된 메트릭을 Prometheus나 InfluxDB와  같은     시계열 데이터베이스에 저장합니다.
  3.  **시각화 (Grafana)**: Grafana에서 TSDB를 데이터 소스로  연결하여     대시보드를 구성합니다.

  <hr style="margin: 3rem 0;">

  <div id="python" style="scroll-margin-top: 100px;"></div>
  ## Python 연동
  `vertica-python`은 Vertica 데이터베이스와 통신하기 위한 순수  Python     DB-API 클라이언트입니다. 별도의 외부 드라이버 없이  Python만으로     Vertica에 쉽게 연결할 수 있습니다.

  #### 설치
  ```bash
  pip install vertica-python
  ```

  #### 사용 예제
  ```python
  import vertica_python
  import pandas as pd

  conn_info = {
      'host': '192.168.1.101', 'port': 5433,
      'user': 'dbadmin', 'password': 'password', 'database': 'VDB'
  }

  with vertica_python.connect(**conn_info) as connection:
      cursor = connection.cursor()
      cursor.execute("SELECT * FROM public.sales")

      columns = [desc[0] for desc in cursor.description]
      df = pd.DataFrame(cursor.fetchall(), columns=columns)
      print(df.head())
  ```
  </div>

  <aside class="page-sidebar">
    <div class="sidebar-panel">
      <h3>On this page</h3>
      <ul>
        <li><a href="#connecting">클라이언트 접속</a></li>
        <li><a href="#monitoring">모니터링 및 Grafana</a></li>
        <li><a href="#python">Python 연동</a></li>
      </ul>
    </div>
  </aside>
</div>