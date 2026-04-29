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

<div class="architecture-section">
    <p class="section-description">Vertica는 <code>vsql</code>, <code>DBeaver</code> 등 다양한 클라이언트 도구를 지원하여 사용자가 편리하게 데이터베이스에 접속할 수 있도록 합니다.</p>
    
    <div class="architecture-subsection">
      <h3 class="section-subtitle">vsql (CLI)</h3>
      <p class="section-description"><code>vsql</code>은 Vertica와 함께 제공되는 공식 커맨드 라인 클라이언트입니다.</p>
      <div class="syntax-box">
        <strong>접속 예시:</strong>
        <pre><code>/opt/vertica/bin/vsql -h &lt;host_ip&gt; -d &lt;db_name&gt; -U &lt;user_name&gt;</code></pre>
      </div>
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

## 모니터링

<div class="architecture-section">
    <p class="section-description">안정적인 데이터베이스 운영을 위해서는 시스템의 상태를 지속적으로 모니터링하는 것이 필수적입니다. Vertica는 전용 UI 관리 도구인 Management Console(MC)과 오픈소스 기반의 Grafana 연동을 모두 완벽하게 지원합니다.</p>

  <div class="architecture-subsection">
      <h3 class="section-subtitle">1. 모니터링 콘솔 (MC)</h3>
      <p class="section-description">Management Console(MC)은 웹 브라우저 기반(WEB UI)의 강력한 통합 모니터링 및 클러스터 관리 도구입니다. 데이터베이스 상태 분석부터 실제 쿼리 수행까지 운영에 필요한 모든 기능을 직관적으로 제공합니다.</p>
      
  <div class="image-box-styled">
        <img src="{{ '/assets/images/monitoring_1.png' | relative_url }}" alt="Vertica Management Console (MC) 화면">
      </div>

  <dl class="feature-dl">
        <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 핵심 기능</dt>
        <dd class="feature-dd">
          <strong>Overview & Activity:</strong> CPU, Memory, Disk I/O, 네트워크 등 하드웨어 자원과 리소스 풀 사용량, 쿼리 상태(Running/Queued)를 실시간 차트로 확인합니다.<br>
          <strong>Manage & Load:</strong> 노드 추가/제거, 데이터 리밸런싱, DB 기동/정지 처리는 물론, COPY 문을 통한 데이터 적재 내역을 모니터링할 수 있습니다.<br>
          <strong>Query Execution & Plan:</strong> UI 내에서 직접 SQL을 실행하고, <code>EXPLAIN</code> 및 <code>PROFILE</code> 실행 계획을 시각적인 트리 구조로 분석할 수 있습니다.<br>
          <strong>Settings & License:</strong> 최대 세션 수 설정, 라이선스 사용량 점검, Database Designer 실행 등 세부 설정이 가능합니다.
        </dd>
        <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 설치 및 구성 권장사항</dt>
        <dd class="feature-dd">
          안정적인 모니터링 환경을 위해 버티카 클러스터 외부의 <strong>별도 리눅스 서버에 전용으로 설치</strong>하는 것을 권장합니다. 웹 브라우저를 통해 <code>https://&lt;MC서버IP&gt;:5450</code>으로 손쉽게 접속할 수 있습니다.
        </dd>
      </dl>
    </div>

  <div class="architecture-subsection" style="margin-top: 4rem;">
      <h3 class="section-subtitle">2. Grafana 연동</h3>
      <p class="section-description">기본 제공되는 MC 외에도, Vertica의 <code>v_monitor</code>와 <code>v_catalog</code> 스키마에서 수집한 메트릭을 Grafana 등과 연계하여 전사 통합 대시보드를 구축할 수 있습니다.</p>
      
  <div class="image-box-styled">
        <img src="{{ '/assets/images/monitoring_2.png' | relative_url }}" alt="Grafana 모니터링 대시보드 화면">
      </div>

  <ol style="padding-left: 20px; list-style-type: decimal;">
        <li><strong>데이터 수집 (Exporter)</strong>: Python 스크립트나 오픈소스 Exporter를 사용하여 Vertica 메트릭을 수집합니다.</li>
        <li><strong>저장 (TSDB)</strong>: 수집된 메트릭을 Prometheus나 InfluxDB와 같은 시계열 데이터베이스에 저장합니다.</li>
        <li><strong>시각화 (Grafana)</strong>: Grafana에서 TSDB를 데이터 소스로 연결하여 부서나 업무 목적에 맞는 맞춤형 대시보드를 구성합니다.</li>
      </ol>
    </div>
  </div>
  
  <hr style="margin: 3rem 0;">

  <div id="python" style="scroll-margin-top: 100px;"></div>

## Python 연동

<div class="architecture-section">
    <p class="section-description"><code>vertica-python</code>은 Vertica 데이터베이스와 통신하기 위한 순수 Python DB-API 클라이언트입니다. 별도의 외부 드라이버 없이 Python만으로 Vertica에 쉽게 연결할 수 있습니다.</p>
    
  <div class="architecture-subsection">
      <h3 class="section-subtitle">설치</h3>
      <div class="syntax-box">
        <pre><code>pip install vertica-python</code></pre>
      </div>
    </div>
    
  <div class="architecture-subsection">
      <h3 class="section-subtitle">사용 예제</h3>
      <div class="syntax-box">
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

  </div>

  <aside class="page-sidebar">
    <div class="sidebar-panel">
      <h3>On this page</h3>
      <ul>
        <li><a href="#connecting">클라이언트 접속</a></li>
        <li><a href="#monitoring">모니터링</a></li>
        <li><a href="#python">Python 연동</a></li>
      </ul>
    </div>
  </aside>
</div>