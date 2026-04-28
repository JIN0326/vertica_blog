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
  <div class="architecture-section" markdown="1">
    <p class="section-description">Vertica는 `vsql`, `DBeaver` 등 다양한 클라이언트 도구를 지원하여 사용자가 편리하게 데이터베이스에 접속할 수 있도록 합니다.</p>
    <div class="architecture-subsection">
      <h3 class="section-subtitle">vsql (CLI)</h3>
      <p class="section-description">`vsql`은 Vertica와 함께 제공되는 공식 커맨드 라인 클라이언트입니다.</p>
      <pre><code>/opt/vertica/bin/vsql -h &lt;host_ip&gt; -d &lt;db_name&gt; -U &lt;user_name&gt;</code></pre>
    </div>
    <div class="architecture-subsection">
      <h3 class="section-subtitle">DBeaver (GUI)</h3>
      <p class="section-description">DBeaver와 같은 GUI 도구는 Vertica JDBC 드라이버를 사용하여 쉽게 연결할 수 있습니다.</p>
      <ol style="padding-left: 20px; list-style-type: decimal;">
        <li>Vertica 공식 웹사이트에서 JDBC 드라이버(<code>.jar</code> 파일)를 다운로드합니다.</li>
        <li>DBeaver의 <code>드라이버 관리자</code>에서 다운로드한 <code>.jar</code> 파일을 추가하여 새 드라이버를 설정합니다.</li>
        <li>설정한 드라이버를 사용하여 새 데이터베이스 연결을 생성합니다.</li>
      </ol>
    </div>
  </div>
  
  <hr style="margin: 3rem 0;">

  <div id="monitoring" style="scroll-margin-top: 100px;"></div>
  ## 모니터링 및 Grafana
  <div class="architecture-section" markdown="1">
    <p class="section-description">안정적인 데이터베이스 운영을 위해서는 시스템의 상태를 지속적으로 모니터링하는 것이 필수적입니다. Vertica는 <code>v_monitor</code>와 <code>v_catalog</code> 스키마에 다양한 시스템 테이블을 제공합니다.</p>
    <div class="architecture-subsection">
      <h3 class="section-subtitle">Grafana를 이용한 시각화</h3>
      <p class="section-description">시스템 테이블의 데이터를 주기적으로 수집하여 Grafana와 같은 시각화 도구로 대시보드를 만들면, 시스템 상태를 직관적으로 파악하고 이상 징후를 빠르게 감지할 수 있습니다.</p>
      <ol style="padding-left: 20px; list-style-type: decimal;">
        <li><strong>데이터 수집 (Exporter)</strong>: Python 스크립트나 오픈소스 Exporter를 사용하여 Vertica 메트릭을 수집합니다.</li>
        <li><strong>저장 (TSDB)</strong>: 수집된 메트릭을 Prometheus나 InfluxDB와 같은 시계열 데이터베이스에 저장합니다.</li>
        <li><strong>시각화 (Grafana)</strong>: Grafana에서 TSDB를 데이터 소스로 연결하여 대시보드를 구성합니다.</li>
      </ol>
    </div>
  </div>
  
  <hr style="margin: 3rem 0;">

  <div id="python" style="scroll-margin-top: 100px;"></div>
  ## Python 연동
  <div class="architecture-section" markdown="1">
    <p class="section-description"><code>vertica-python</code>은 Vertica 데이터베이스와 통신하기 위한 순수 Python DB-API 클라이언트입니다. 별도의 외부 드라이버 없이 Python만으로 Vertica에 쉽게 연결할 수 있습니다.</p>
    <div class="architecture-subsection">
      <h3 class="section-subtitle">설치</h3>
      <pre><code>pip install vertica-python</code></pre>
    </div>
    <div class="architecture-subsection">
      <h3 class="section-subtitle">사용 예제</h3>
      <pre><code>import vertica_python
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
    print(df.head())</code></pre>
    </div>
  </div>
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