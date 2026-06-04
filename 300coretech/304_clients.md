---
title: "Vertica Clients: DBeaver, JDBC, ODBC 연결 및 HA 설정 - Vertica Blog"
layout: default
description: "Vertica에 접속하기 위한 다양한 클라이언트(DBeaver, JDBC, ODBC) 설정 방법을 알아봅니다. 안정적인 운영을 위한 로드 밸런싱(Load Balancing)과 고가용성(HA)을 위한 대체 노드(Backup Server Node) 설정 방법을 포함합니다."
keywords: "vertica, clients, jdbc, odbc, dbeaver, datagrip, connection, ha, high availability, load balancing, backupservernode, dsn"
canonical_url: "https://jin0326.github.io/vertica_blog/300coretech/300coretech#clients"
---

<div id="clients" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody">
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
      <li style="word-break: keep-all;"><span class="feature-list__icon">🔹</span>환경 변수 등록 : ODBC 드라이버가 vertica.ini 파일을 인식하도록 (.profile) 또는 (.bash_profile)에 "export VERTICAINI=/etc/vertica.ini" 환경 변수를 추가해야 합니다.</li>
    </ul>
  </div>
</div>
</div>
</div>
