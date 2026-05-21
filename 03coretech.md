---
title: Vertica(버티카) 핵심 기술: vsql, 모니터링, Copy, Export, Explain - Vertica Blog
layout: default
description: "Vertica(버티카) 핵심 기술 가이드. vsql(CLI) 사용법, MC/Grafana 모니터링, Data Collector, DBeaver/JDBC/ODBC 클라이언트 연동, 대용량 데이터 적재(Copy) 및 내보내기(Export to Parquet/Iceberg), 그리고 EXPLAIN/PROFILE을 통한 쿼리 분석 및 튜닝 방법을 알아봅니다."
---
<div class="page-hero">
  <span class="chip">Core Tech</span>
  <h1>Vertica Core Tech</h1>
  <p>Vertica 개발 및 운영에 필수적인 핵심 기술인 vsql, Monitoring, Clients, 데이터 적재(Copy), 데이터 내보내기(Export) 그리고 쿼리 분석(Explain)에 대해 상세히 알아봅니다.</p>
</div>

<div class="page-layout">
  <div class="content-section" markdown="1">

<div id="vsql" style="scroll-margin-top: 100px;"></div>

## vsql (CLI)

<div class="architecture-section">
  <p class="section-description"><code>vsql</code>은 Vertica 데이터베이스에 접속하여 SQL 쿼리를 실행하고 결과를 확인할 수 있는 강력한 커맨드 라인 유틸리티입니다. 데이터베이스 관리 및 스크립트 기반의 자동화 작업에 주로 활용됩니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">주요 접속 및 실행 옵션</h3>
    <p class="section-description">vsql 실행 시 다양한 파라미터를 통해 접속 대상과 실행 방식을 세밀하게 지정할 수 있습니다.</p>

  <div class="syntax-box">
      <strong>vsql 접속 구문:</strong>
      <pre><code>vsql -h [hostname/IP] -d [dbname] -U [username] -w [password]</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 접속 옵션</dt>
      <dd class="feature-dd">
        <strong>vsql :</strong> 현재 OS 계정과 동일한 DB User로 접속합니다.<br>
        <strong>-h:</strong> 접속할 DB의 IP를 지정합니다. (미지정 시 Local 접속)<br>
        <strong>-d:</strong> 접속할 DB명을 지정합니다. (기본값: 현재 OS 계정)<br>
        <strong>-U:</strong> 접속할 DB User명을 지정합니다. (기본값: 현재 OS 계정)<br>
        <strong>-w:</strong> 접속할 계정의 패스워드를 지정합니다.
      </dd>
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 실행 옵션</dt>
      <dd class="feature-dd">
        <strong>-c "query":</strong> vsql 접속 화면으로 들어가지 않고 단일 쿼리를 실행한 후 즉시 종료합니다.<br>
        <strong>-f filename.sql:</strong> 파일에 작성된 SQL 스크립트를 일괄 실행합니다.<br>
        <strong>-a:</strong> 스크립트 파일 실행 시, 수행되는 쿼리문도 함께 화면에 출력합니다.<br>
        <strong>-E:</strong> <code>\d</code> 같은 메타 커맨드 수행 시, 내장되어 백그라운드에서 동작하는 실제 쿼리를 출력합니다.<br>
        <strong>-e:</strong> 사용자가 수행하는 쿼리를 화면에 한 번 더 출력해 줍니다.<br>
        <strong>-m:</strong> 메시지 출력 레벨을 지정합니다 (디버그용).
      </dd>
    </dl>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">vsql 메타 커맨드 (Meta-Commands)</h3>
    <p class="section-description">vsql 내부에서 <code>\</code>(백슬래시)로 시작하는 명령어를 통해 다양한 정보 조회 및 환경 설정을 수행할 수 있습니다.</p>

  <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>\?:</strong> <span>메타 커맨드(Meta-Command) 전반에 대한 도움말을 출력합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>\h:</strong> <span>SQL 명령어 구문에 대한 상세 도움말을 출력합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>\c:</strong> <span>현재 세션을 끊고 다른 DB 혹은 다른 User로 재접속합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>\d [table]:</strong> <span>특정 테이블, 뷰, 시퀀스 등의 구조(컬럼 타입 등) 및 상세 정보를 출력합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>\dt:</strong> <span>현재 접속한 스키마의 전체 테이블 목록을 조회합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>\v:</strong> <span>현재 사용 가능한 View 객체 목록을 출력합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>\e:</strong> <span>마지막으로 실행한 쿼리를 OS 기본 에디터(vi 등)로 열어 편집 후 바로 실행합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>\timing:</strong> <span>쿼리 실행 시간(Elapsed time) 출력 기능을 On/Off 합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>\x:</strong> <span>출력 포맷을 컬럼형(세로) 또는 레코드형(가로)으로 전환(Expanded display)하여 긴 결과값을 보기 쉽게 만듭니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>\!:</strong> <span>vsql 프롬프트를 빠져나가지 않은 상태에서 OS 쉘 명령어(예: <code>\! ls -al</code>)를 실행합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>\o [file]:</strong> <span>이후 실행되는 쿼리의 결과를 화면이 아닌 지정한 파일로 저장합니다.</span></li>
    </ul>
  </div>
</div>

<hr style="margin: 3rem 0;">

<div id="monitoring" style="scroll-margin-top: 100px;"></div>

## Monitoring

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

  <div class="architecture-subsection">
        <h3 class="section-subtitle">2. Grafana 대시보드</h3>
        <p class="section-description">Grafana는 오픈소스 시각화 도구로, Vertica의 시스템 테이블 데이터를 활용하여 맞춤형 모니터링 대시보드를 구축할 수 있습니다.</p>
        
  <div class="image-box-styled">
            <img src="{{ '/assets/images/monitoring_2.png' | relative_url }}" alt="Vertica Grafana Dashboard">
        </div>

  <dl class="feature-dl">
            <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 활용 지표</dt>
            <dd class="feature-dd">
                <strong>실시간 성능 지표:</strong> CPU, 메모리, 디스크 I/O, 네트워크 등 핵심 시스템 리소스 사용량을 시각적으로 추적합니다.<br>
                <strong>쿼리 및 세션 분석:</strong> 활성 쿼리 수, 리소스 풀별 점유율, 사용자 세션 등 상세한 워크로드 분석이 가능합니다.<br>
                <strong>유연한 커스터마이징:</strong> Vertica의 다양한 시스템 테이블(v_monitor, v_internal)과 연동하여 필요한 모든 지표를 자유롭게 추가하고 시각화할 수 있습니다.
            </dd>
        </dl>
    </div>
  </div>
  
<hr style="margin: 3rem 0;">

<div id="data-collector" style="scroll-margin-top: 100px;"></div>

## Data Collector

<div class="architecture-section">
  <p class="section-description"><strong>Data Collector (DC)</strong>는 Vertica 데이터베이스의 핵심 유틸리티로, 시스템 메트릭, 쿼리 통계, 리소스 사용량 등 다양한 운영 데이터를 자동으로 수집하여 시스템 테이블에 저장합니다. 이 데이터는 성능 분석, 문제 해결, 용량 계획 등에 활용됩니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. 역할 및 개요</h3>
    <p class="section-description">Data Collector는 백그라운드에서 지속적으로 데이터를 수집하며, 수집된 정보는 주로 <code>v_monitor</code> 스키마의 시스템 테이블에 저장됩니다.</p>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>시스템 메트릭:</strong> <span>CPU, 메모리, 디스크 I/O, 네트워크 사용량 등 하드웨어 리소스 정보를 수집합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>쿼리 통계:</strong> <span>실행된 쿼리의 시작/종료 시간, 사용자, 소요 시간, 사용 리소스 등 상세 정보를 기록합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>리소스 풀 사용량:</strong> <span>각 리소스 풀의 현재 상태, 큐 대기 시간, 메모리 사용량 등을 모니터링합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>자동화된 수집:</strong> <span>별도의 설정 없이 Vertica 설치 시 자동으로 활성화되어 데이터를 수집합니다.</span></li>
    </ul>
    <div class="syntax-box">
      <strong>Data Collector 관련 시스템 테이블 조회 예시:</strong>
      <pre><code>-- 최근 실행된 쿼리 요청 정보 조회
SELECT * FROM v_monitor.query_requests ORDER BY start_timestamp DESC LIMIT 10;

-- 리소스 풀 상태 정보 조회
SELECT * FROM v_monitor.resource_pool_status LIMIT 10;</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 데이터 보존 정책 (Retention Policy) 설정</h3>
    <p class="section-description">Data Collector가 수집하는 데이터는 시간이 지남에 따라 디스크 공간을 많이 차지할 수 있습니다. Vertica는 이러한 시스템 테이블의 데이터를 자동으로 관리하기 위한 보존 정책을 제공합니다. 필요에 따라 변경할 수 있습니다.</p>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 현재 보존 정책 확인</dt>
      <dd class="feature-dd">
        <p class="section-description"><code>GET_DATA_COLLECTOR_POLICY</code> 함수나 <code>data_collector</code> 시스템 테이블을 통해 현재 설정된 정책을 확인할 수 있습니다.</p>
        <div class="syntax-box">
          <pre><code>-- 특정 컴포넌트의 정책 요약 확인
SELECT GET_DATA_COLLECTOR_POLICY('ResourceAcquisitions');

-- data_collector 테이블에서 상세 정보 확인
SELECT * FROM data_collector 
WHERE table_name ILIKE '%dc_resource_acquisitions%' AND node_name ILIKE '%001';</code></pre>
        </div>
      </dd>
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 보존 정책 변경</dt>
      <dd class="feature-dd">
        <p class="section-description"><code>SET_DATA_COLLECTOR_POLICY</code> 함수를 사용하여 메모리 및 디스크 크기를, <code>SET_DATA_COLLECTOR_TIME_POLICY</code> 함수를 사용하여 수집 간격(Interval)을 조정할 수 있습니다.</p>
        <div class="syntax-box">
          <strong>정책 변경 예시:</strong>
          <pre><code>-- 메모리 및 디스크 크기 조정
SELECT SET_DATA_COLLECTOR_POLICY('ResourceAcquisitions', '1000', '10000');

-- 특정 컴포넌트의 수집 간격 조정
SELECT SET_DATA_COLLECTOR_TIME_POLICY('ResourceAcquisitions', '1 day'::interval);

-- 전체 컴포넌트의 수집 간격 일괄 조정
SELECT SET_DATA_COLLECTOR_TIME_POLICY('1 day'::interval);

-- 수집 간격을 기본값으로 원복
SELECT SET_DATA_COLLECTOR_TIME_POLICY('-1');</code></pre>
        </div>
      </dd>
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 고급 예제: 사용량 기반 디스크 크기 조정</dt>
      <dd class="feature-dd">
        <p class="section-description">아래 쿼리는 <code>user_sessions</code>, <code>query_requests</code>와 같이 데이터가 빠르게 쌓이는 주요 시스템 테이블에 대해, 일일 사용량(<code>kb_per_day</code>)을 기반으로 5일치 데이터를 보관할 수 있는 디스크 크기를 계산하고, 이를 적용할 수 있는 <code>SET_DATA_COLLECTOR_POLICY</code> 구문을 동적으로 생성합니다.</p>
        <div class="syntax-box">
          <pre><code>SELECT 
    x.table_name, 
    '--SELECT SET_DATA_COLLECTOR_POLICY('''|| component || ''', ''' || memory_buffer_size_kb || ''', ''' || (kb_per_day::INT * 5) || ''');' AS set_policy_command
FROM data_collector x
WHERE x.kb_per_day &lt;&gt; 0
  AND x.table_name IN (
    'dc_session_starts', 'dc_requests_issued', 'dc_requests_completed', 
    'dc_errors', 'dc_resource_acquisitions'
  )
LIMIT 1 OVER(PARTITION BY x.table_name ORDER BY kb_per_day DESC);</code></pre>
        </div>
      </dd>
    </dl>
  </div>
</div>

<hr style="margin: 3rem 0;">

<div id="clients" style="scroll-margin-top: 100px;"></div>

## Clients

<div class="architecture-section">
  <p class="section-description">Vertica는 다양한 클라이언트 도구 및 애플리케이션과의 원활한 연동을 위해 표준 <b>JDBC</b> 및 <b>ODBC</b> 드라이버를 제공합니다. 안정적인 운영 환경을 위해 로드 밸런싱(Load Balancing)과 대체 노드(Backup Server Node) 설정이 강력히 권장됩니다.</p>
  
  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. DBeaver (GUI 클라이언트)</h3>
    <p class="section-description">DBeaver, DataGrip 등의 범용 DB 관리 도구는 대부분 JDBC 드라이버를 기반으로 연결됩니다.</p>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>드라이버 다운로드:</strong> Vertica 공식 웹사이트에서 클러스터 버전에 맞는 JDBC 드라이버(<code>.jar</code>)를 다운로드합니다.</li>
      <li><span class="feature-list__icon">🔹</span> <strong>드라이버 설정:</strong> 도구의 '드라이버 관리자' 메뉴에서 새 드라이버를 생성하고 다운로드한 <code>.jar</code> 파일을 추가합니다.</li>
      <li><span class="feature-list__icon">🔹</span> <strong>연결 생성:</strong> Host(IP), Port(기본 5433), Database, User, Password를 입력하여 접속합니다.</li>
    </ul>
    <dl class="feature-dl" style="margin-top: 1.5rem;">
        <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 드라이버 속성 (Driver Properties)</dt>
        <dd class="feature-dd">
            DBeaver의 <strong>'Edit Connection' > 'Driver properties'</strong> 탭에서 아래와 같은 고급 옵션을 설정하여 안정성과 성능을 향상시킬 수 있습니다.
            <br><br>
            <strong>AutoCommit:</strong> DBeaver는 기본적으로 Auto-commit 모드로 동작합니다. 대용량 DML 작업이나 트랜잭션 제어가 필요할 경우, 수동 커밋(Manual Commit) 모드로 전환하는 것이 권장됩니다.<br>
            <strong>ConnectionLoadBalance:</strong> 값을 <code>true</code>로 설정하면, 접속 시 부하를 여러 노드로 분산시켜 단일 노드의 접속 병목을 방지합니다.<br>
            <strong>BackupServerNode:</strong> 주 접속 노드에 장애가 발생했을 때 자동으로 접속을 시도할 대체 노드들의 IP 목록을 쉼표(,)로 구분하여 입력합니다. (예: <code>&lt;BACKUP_IP_1&gt;,&lt;BACKUP_IP_2&gt;</code>)
        </dd>
    </dl>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. JDBC 접속 (Java 애플리케이션)</h3>
    <p class="section-description">Java 환경에서 접속하기 위해서는 런타임이 라이브러리 클래스를 찾을 수 있도록 <code>CLASSPATH</code>에 Vertica JDBC jar 파일을 반드시 추가해야 합니다. (예: <code>export CLASSPATH=$CLASSPATH:/opt/vertica/java/lib/vertica-jdbc.jar</code>)</p>
    
  <div class="syntax-box">
      <strong>Java JDBC 연결 및 HA(고가용성) 설정 예시:</strong>
      <pre><code>import java.sql.*;
import java.util.Properties;

// 1. 접속 정보 및 핵심 Properties 설정
Properties myProp = new Properties();
myProp.put("user", "dbadmin");
myProp.put("password", "your_password");

// 로드 밸런싱 활성화 (Round Robin 방식으로 접속 분산)
myProp.put("ConnectionLoadBalance", "true"); 

// 장애 대비 대체 노드 리스트 지정 (고가용성 확보)
myProp.put("BackupServerNode", "&lt;BACKUP_IP_1&gt;,&lt;BACKUP_IP_2&gt;"); 

// 2. 연결 생성
Connection conn;
try {
    conn = DriverManager.getConnection(
        "jdbc:vertica://&lt;DB_IP&gt;:5433/mydatabase", myProp
    );
    System.out.println("Connected Successfully!");
} catch (SQLException e) {
    e.printStackTrace();
}</code></pre>
    </div>
    
  <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 JDBC Connection Properties</dt>
      <dd class="feature-dd">
        <strong>ConnectionLoadBalance:</strong> <code>true</code> 설정 시 클라이언트 접속 오버헤드를 클러스터 전체 노드로 균등하게 분산시킵니다.<br>
        <strong>BackupServerNode:</strong> 최초 접속 대상 노드에 장애가 발생했을 때, 자동으로 재접속을 시도할 백업 노드의 IP 목록(쉼표 구분)을 지정합니다.<br>
        <strong>LoginTimeout / LogLevel:</strong> 로그인 대기 시간이나 드라이버 로깅 레벨(DEBUG, INFO, ERROR 등)을 세밀하게 조정할 수 있습니다.
      </dd>
    </dl>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. ODBC 접속 (Linux DSN 설정)</h3>
    <p class="section-description">Linux 환경에서 타사 BI 툴이나 C/C++, Python(pyodbc) 등으로 접속하려면 클라이언트 서버에 <strong>ODBC Driver Manager (UnixODBC 또는 iODBC)</strong>가 사전에 반드시 설치되어 있어야 합니다.</p>
    
  <p class="section-description">ODBC 설정은 <strong>접속 정보(odbc.ini)</strong>와 <strong>드라이버 환경(vertica.ini)</strong>을 나누어 구성합니다.</p>

  <div class="syntax-box">
      <strong>1. /etc/odbc.ini (데이터 소스 및 접속 정보)</strong>
      <pre><code>[VerticaDSN]
Description = Vertica Database
Driver      = /opt/vertica/lib64/libverticaodbc.so
Database    = mydatabase
Servername  = &lt;DB_IP&gt;
UID         = dbadmin
PWD         = your_password
Port        = 5433
ConnSettings=
ConnectionLoadBalance = true
Locale      = en_US@collation=binary
BackupServerNode = &lt;BACKUP_IP_1&gt;,&lt;BACKUP_IP_2&gt;</code></pre>
    </div>

  <div class="syntax-box">
      <strong>2. /etc/vertica.ini (드라이버 환경 구성)</strong>
      <pre><code>[Driver]
DriverManagerEncoding=UTF-16
ODBCInstLib=/usr/local/odbc/lib/libodbcinst.so  # ODBC 설치 라이브러리 경로 지정
ErrorMessagesPath=/opt/vertica
LogLevel=4
LogPath=/tmp</code></pre>
    </div>

  <ul class="feature-list">
      <li style="word-break: keep-all;"><span class="feature-list__icon">🔹</span> <strong>환경 변수 등록:</strong> ODBC 드라이버가 <code>vertica.ini</code> 파일을 인식하도록 <code>.profile</code> 또는 <code>.bash_profile</code>에 <code>export VERTICAINI=/etc/vertica.ini</code> 환경 변수를 추가해야 합니다.</li>
    </ul>
  </div>
</div>

<hr style="margin: 3rem 0;">
<div id="copy" style="scroll-margin-top: 100px;"></div>

## Copy (대용량 데이터 적재)

<div class="architecture-section">
  <p class="section-description"><code>COPY</code>문은 Vertica에서 대용량 데이터를 가장 빠르고 효율적으로 적재(Load)하기 위한 핵심 명령어입니다. 서버, 클라이언트, 또는 표준 입력(STDIN) 등 다양한 소스로부터 데이터를 읽어 테이블에 고속으로 삽입합니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. 서버 파일에서 적재 (COPY FROM)</h3>
    <p class="section-description">가장 일반적인 방식으로, Vertica 클러스터의 노드에 위치한 파일에서 데이터를 적재합니다. 모든 노드에서 파일에 접근할 수 있어야 하며, 와일드카드(*)를 사용하여 여러 파일을 동시에 로드할 수 있습니다.</p>
    <div class="syntax-box">
      <strong>기본 구문:</strong>
      <pre><code>COPY schema.table (column_list)
FROM '/path/on/server/file.csv'
[옵션 지정];</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 로드 옵션</dt>
      <dd class="feature-dd">
        <strong>DELIMITER:</strong> 데이터의 구분자를 지정합니다. (예: <code>DELIMITER ','</code>)<br>
        <strong>ENCLOSED BY:</strong> 특정 기호로 감싸진 문자열 데이터를 처리합니다. (예: <code>ENCLOSED BY '"'</code>)<br>
        <strong>SKIP:</strong> 파일의 시작 부분에서 건너뛸 행(Row)의 수를 지정합니다. (헤더 행을 무시할 경우 <code>SKIP 1</code>)<br>
        <strong>RECORD TERMINATOR:</strong> 레코드(행)의 끝을 나타내는 문자를 지정합니다. (기본값: <code>\n</code>)<br>
        <strong>NO ESCAPE:</strong> 이스케이프 문자 처리를 비활성화하여 백슬래시(<code>\</code>)를 일반 문자로 취급합니다.<br>
        <strong>NULL AS:</strong> 특정 문자열을 NULL 값으로 인식하도록 지정합니다. (예: <code>NULL AS 'N/A'</code>)
      </dd>
    </dl>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 클라이언트 파일에서 적재 (COPY LOCAL)</h3>
    <p class="section-description"><code>vsql</code>과 같은 클라이언트가 실행 중인 로컬 머신의 파일에서 데이터를 적재합니다. 서버에 파일을 업로드하는 중간 과정 없이 로컬 데이터를 바로 테이블에 삽입할 수 있어 편리합니다.</p>
    <div class="syntax-box">
      <strong>기본 구문:</strong>
      <pre><code>COPY schema.table
FROM LOCAL '/path/on/client/data.csv'
DELIMITER ',';</code></pre>
    </div>
    <ul class="feature-list">
      <li><span class="feature-list__icon">💡</span> <strong>Tip:</strong> <span><code>LOCAL</code> 키워드를 사용하면 파일 경로는 서버가 아닌 클라이언트 머신을 기준으로 해석됩니다.</span></li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. 표준 입력에서 적재 (COPY FROM STDIN)</h3>
    <p class="section-description">다른 명령어의 출력 결과를 파이프(<code>|</code>)로 연결하여 파일 생성 없이 바로 Vertica 테이블로 적재할 때 사용됩니다. 쉘 스크립트 기반의 데이터 파이프라인에서 유용하게 활용됩니다.</p>
    <div class="syntax-box">
      <strong>파이프라인 연동 예시:</strong>
      <pre><code># gzip으로 압축된 파일을 풀면서 바로 COPY
gunzip -c /path/on/server/data.csv.gz | vsql -c "COPY schema.table FROM STDIN DELIMITER ','"</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">4. 다른 Vertica DB에서 적재 (COPY FROM VERTICA)</h3>
    <p class="section-description"><code>COPY FROM VERTICA</code>는 다른 Vertica 데이터베이스에 있는 테이블로부터 대용량 데이터를 고속으로 복사하는 명령어입니다. 이 작업을 수행하려면, 먼저 <code>CONNECT TO VERTICA</code> 문을 사용하여 원본 데이터베이스에 연결해야 합니다. 단일/소량 데이터 처리에 사용되는 <code>INSERT</code> 문과 달리, <code>COPY</code>는 대규모 벌크 로드에 최적화되어 있습니다.</p>
    <div class="syntax-box">
      <strong>다른 DB에서 데이터 복사 예시:</strong>
      <pre><code>-- 1. 원본 데이터베이스에 연결
CONNECT TO VERTICA source_db USER dbadmin PASSWORD 'password' ON 'source_host', 5433;

-- 2. 원본 DB의 'source_sales' 테이블에서 현재 DB의 'target_sales'로 데이터 복사
COPY public.target_sales
FROM VERTICA source_db.public.source_sales;

-- 3. 연결 해제
DISCONNECT source_db;</code></pre>
    </div>
  </div>


  <div class="architecture-subsection">
    <h3 class="section-subtitle">5. 에러 처리 및 데이터 검증</h3>
    <p class="section-description">데이터 적재 시 발생하는 포맷 오류나 제약조건 위반 데이터를 추적하고 안전하게 격리할 수 있습니다.</p>
    <div class="syntax-box">
      <strong>에러 처리 구문 예시:</strong>
      <pre><code>COPY sales FROM '/data/sales_data.csv' DELIMITER ','
      REJECTED DATA '/data/logs/sales_rejected.csv'
      EXCEPTIONS '/data/logs/sales_exceptions.log'
      ABORT ON ERROR;</code></pre>
    </div>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>REJECTED DATA:</strong> <span>적재에 실패한 원본 데이터(Row)를 지정한 경로의 파일로 따로 저장합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>EXCEPTIONS:</strong> <span>데이터가 거부된 구체적인 이유(에러 메시지 및 발생 위치)를 파일에 기록하여 원인 파악을 돕습니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>ABORT ON ERROR:</strong> <span>단 1건의 에러라도 발생하면 전체 Copy 작업을 즉시 중단하고 롤백(Rollback)시켜 데이터 정합성을 보호합니다.</span></li>
    </ul>
  </div>
</div>

<hr style="margin: 3rem 0;">
<div id="export" style="scroll-margin-top: 100px;"></div>

## Export (데이터 내보내기)

<div class="architecture-section">
  <p class="section-description">Vertica의 데이터를 외부 파일로 추출하거나 다른 클러스터로 전송하는 방법입니다. 운영 환경에서는 주로 사용자 데이터의 논리적 백업이나 타 시스템과의 데이터 연계를 위해 사용됩니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. vsql을 이용한 텍스트 파일 내보내기</h3>
    <p class="section-description">가장 보편적인 방법으로, <code>vsql</code>의 실행 결과를 파일로 리다이렉션하여 CSV나 TSV 형태의 텍스트 파일을 생성합니다.</p>
    
    <div class="syntax-box">
      <strong>기본 실행 구문:</strong>
      <pre><code>vsql -U username -w password -At -F ',' -c "SELECT * FROM public.sales;" -o sales_backup.csv</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 옵션 설명</dt>
      <dd class="feature-dd">
        <strong>-A (unaligned):</strong> 출력 시 컬럼 간격을 맞추지 않고 붙여서 출력합니다.<br>
        <strong>-t (tuples only):</strong> 컬럼명(Header) 없이 데이터 행만 출력합니다.<br>
        <strong>-F (field separator):</strong> 컬럼 구분자를 지정합니다 (예: <code>','</code>, <code>'|'</code>).<br>
        <strong>-o (output):</strong> 결과를 콘솔이 아닌 지정한 파일명으로 저장합니다.
      </dd>
    </dl>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. EXPORT TO DELIMITED (구분자 파일로 내보내기)</h3>
    <p class="section-description">쿼리 결과를 CSV나 TSV와 같은 구분자 기반 텍스트 파일로 내보냅니다. 클러스터의 각 노드에 병렬로 파일이 생성되어 대용량 데이터 추출에 효과적입니다.</p>
    <div class="syntax-box">
      <pre><code>EXPORT TO DELIMITED(
    directory = '/data/export/sales/',
    delimiter = '|',
    null = 'NULL'
) AS SELECT * FROM public.sales;</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 옵션 설명</dt>
      <dd class="feature-dd">
        <strong>directory:</strong> 파일이 저장될 서버 경로를 지정합니다. (필수)<br>
        <strong>delimiter:</strong> 컬럼을 구분할 문자를 지정합니다.<br>
        <strong>null:</strong> NULL 값을 표현할 문자열을 지정합니다.
      </dd>
    </dl>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. EXPORT TO PARQUET (데이터 레이크 연동)</h3>
    <p class="section-description">분석용 표준 포맷인 Parquet 형태로 데이터를 내보냅니다. S3나 HDFS 같은 외부 스토리지로 데이터를 백업하거나 데이터 레이크와 연동할 때 매우 효율적입니다.</p>
    <div class="syntax-box">
      <pre><code>EXPORT TO PARQUET(
    directory='s3://my-bucket/backup/sales/',
    partition_by='sale_year, sale_month',
    compression='snappy'
) OVER (PARTITION BY sale_year, sale_month)
AS SELECT 
    EXTRACT(YEAR FROM sale_date) AS sale_year, 
    EXTRACT(MONTH FROM sale_date) AS sale_month, 
    * 
FROM public.sales;</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 옵션 설명</dt>
      <dd class="feature-dd">
        <strong>partition_by:</strong> 지정된 컬럼 값에 따라 하위 디렉터리를 생성하여 데이터를 파티셔닝합니다. (예: <code>/sale_year=2024/sale_month=7/</code>)<br>
        <strong>compression:</strong> 압축 코덱(<code>snappy</code>, <code>gzip</code>)을 지정합니다.<br>
        <strong>fileSizeMB:</strong> 개별 Parquet 파일의 최대 크기를 MB 단위로 지정합니다.
      </dd>
    </dl>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">4. EXPORT TO ICEBERG (Iceberg 테이블로 내보내기)</h3>
    <p class="section-description">데이터를 Apache Iceberg 테이블 포맷으로 내보냅니다. 데이터 레이크하우스 환경에서 트랜잭션과 스키마 변경을 효율적으로 관리할 수 있습니다.</p>
    <div class="syntax-box">
      <pre><code>EXPORT TO ICEBERG(
    location='s3://my-iceberg-lake/warehouse/sales_iceberg',
    format='parquet'
)
AS SELECT * FROM public.sales;</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">5. EXPORT TO VERTICA (클러스터 간 복제)</h3>
    <p class="section-description">네트워크를 통해 한 Vertica 클러스터에서 다른 클러스터로 직접 데이터를 전송합니다.</p>
    <div class="syntax-box">
      <strong>다른 DB로 데이터 복제 예시:</strong>
      <pre><code>-- 1. 대상 데이터베이스에 연결
CONNECT TO VERTICA target_db USER dbadmin PASSWORD 'password' ON 'target_host', 5433;

-- 2. 현재 DB의 'sales' 테이블 데이터를 대상 DB의 'sales' 테이블로 복제
EXPORT TO VERTICA target_db.public.sales 
AS SELECT * FROM public.sales;

-- 3. 연결 해제
DISCONNECT target_db;</code></pre>
    </div>
  </div>
</div>

<hr style="margin: 3rem 0;">
<div id="explain" style="scroll-margin-top: 100px;"></div>

## Explain (쿼리 분석)

<div class="architecture-section">
  <p class="section-description"><code>EXPLAIN</code>은 Vertica 옵티마이저가 SQL 쿼리를 어떻게 분석하고 실행할 것인지에 대한 <strong>실행 계획(Execution Plan)</strong>을 보여줍니다. 쿼리 성능 튜닝 및 병목 구간 파악을 위한 가장 기초적이고 필수적인 도구입니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">실행 계획 확인 방법</h3>
    <div class="syntax-box">
      <strong>기본 구문:</strong>
      <pre><code>EXPLAIN [SELECT / UPDATE / DELETE 쿼리];
PROFILE [SELECT / UPDATE / DELETE 쿼리];</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> EXPLAIN vs PROFILE</dt>
      <dd class="feature-dd">
        <strong>EXPLAIN:</strong> 쿼리를 <em>실제로 실행하지 않고</em> 옵티마이저가 예측한 경로(Path), 비용(Cost), 예상 행 수(Rows) 등을 텍스트 트리 형태로 반환합니다.<br>
        <strong>PROFILE:</strong> 쿼리를 <em>실제로 실행</em>하면서, 계획의 각 단계별 실제 소요 시간, 메모리 사용량 등의 런타임 통계(Profile Data)를 수집하여 더 정확한 튜닝 지표를 제공합니다.
      </dd>
    </dl>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">실행 계획 분석 포인트</h3>
    <p class="section-description">EXPLAIN 결과를 읽을 때는 트리 구조의 <strong>안쪽(가장 들여쓰기가 많이 된 하위 노드)부터 바깥쪽으로, 아래에서 위로</strong> 해석합니다.</p>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>Cost (비용):</strong> <span>옵티마이저가 해당 연산에 필요하다고 추정하는 자원의 양입니다. 튜닝 전후의 쿼리 효율성을 비교하는 상대적인 지표로 활용됩니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>Rows (예상 건수):</strong> <span>해당 단계에서 출력될 것으로 예상되는 데이터의 행 수입니다. (통계 정보가 최신화되지 않았다면 실제 건수와 크게 차이 날 수 있습니다.)</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>Access Path:</strong> <span>데이터를 읽는 방식입니다. Storage Access(물리 디스크 접근) 단계에서 조건절(Filter)이 효율적으로 푸시다운(Push-down) 되었는지 확인합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>Join Type:</strong> <span>Hash Join, Merge Join 등 조인 방식이 적절한지 파악합니다. 특히 대용량 조인 시 메모리가 부족해 디스크를 사용하는 현상(Spill to disk)이 발생할 우려가 있는지 체크합니다.</span></li>
    </ul>
    <div class="syntax-box">
      <strong>실행 계획 출력 예시:</strong>
      <pre><code>EXPLAIN SELECT * FROM sales s JOIN product p ON s.product_id = p.product_id;

-- 결과 요약 트리 (하단부터 상단으로 해석)
Access Path: 
 +-JOIN HASH [Cost: 500, Rows: 10K] (PATH ID: 1)
 |  Join Cond: (s.product_id = p.product_id)
 | +-- Outer -> STORAGE ACCESS for s [Cost: 200, Rows: 10K] 
 | |      Projection: public.sales_b0
 | +-- Inner -> STORAGE ACCESS for p [Cost: 50, Rows: 1K] 
 | |      Projection: public.product_b0</code></pre>
    </div>
  </div>
</div>

</div>

  <aside class="page-sidebar">
  <div class="sidebar-panel" style="padding-right: 1rem;">
    <h3>On this page</h3>
    <ul>
      <li><a href="#vsql">vsql</a></li>
      <li><a href="#monitoring">Monitoring</a></li>
      <li><a href="#data-collector">Data Collector</a></li>
      <li><a href="#clients">Clients</a></li>
      <li><a href="#copy">Copy</a></li>
      <li><a href="#export">Export</a></li>
      <li><a href="#explain">Explain</a></li>
    </ul>
  </div>
  </aside>
</div>
