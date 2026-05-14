---
title: Vertica Auth&Security
layout: default
---

<div class="page-hero">
  <span class="chip">Auth&Security</span>
  <h1>Vertica Auth&Security</h1>
  <p>Vertica 환경에서 데이터를 안전하게 보호하고 접근을 제어하기 위한 핵심 인증 체계인 Client Auth, LDAP, TLS(SSL), 그리고 Okta 연동 방법에 대해 알아봅니다.</p>
</div>

<div class="page-layout">
  <div class="content-section" markdown="1">

<div id="auth-overview" style="scroll-margin-top: 100px;"></div>

## Client Authentication

<div class="architecture-section">
  <p class="section-description">Vertica는 데이터베이스 접근을 제어하기 위해 다양한 클라이언트 인증 방식을 제공하며, <code>CLIENT_AUTH</code> 및 <code>USER_CLIENT_AUTH</code> 시스템 테이블을 통해 이를 관리합니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">주요 인증 방식 (Methods)</h3>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>Hash:</strong> <span>가장 일반적인 방식으로, 네트워크를 통해 해시된 암호를 전송하여 검증합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>LDAP:</strong> <span>Active Directory 또는 LDAP 서버를 통해 사용자를 인증합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>TLS:</strong> <span>디지털 인증서를 사용하여 클라이언트를 인증하며 보안성이 가장 높습니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>OAuth:</strong> <span>액세스 토큰을 사용하여 Okta와 같은 IdP(Identity Provider)와 연동합니다.</span></li>
    </ul>

<div class="syntax-box">
      <strong>인증 우선순위 로직:</strong>
      <ol>
        <li><strong>auth_priority:</strong> 가장 먼저 판별되는 우선순위 값입니다.</li>
        <li><strong>method_priority:</strong> 인증 방법 자체의 우선순위입니다.</li>
        <li><strong>address_priority:</strong> 특정 IP 대역폭 설정의 우선순위입니다.</li>
      </ol>
    </div>
  </div>
</div>

<hr style="margin: 3rem 0;">

<div id="ldap" style="scroll-margin-top: 100px;"></div>

## LDAP 인증 
<div class="architecture-section">
  <p class="section-description">Vertica는 사내 통합 계정 관리를 위해 OpenLDAP 또는 Active Directory(AD) 서버와 연동하여 사용자 인증을 수행할 수 있습니다. 주로 <strong>LDAP Bind (단순 바인드)</strong>와 <strong>LDAP Search and Bind (검색 및 바인드)</strong> 두 가지 방식을 지원합니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. LDAP 인증 파라미터</h3>
    <p class="section-description"><code>CREATE AUTHENTICATION ... METHOD 'ldap'</code> 명령어로 LDAP 인증 객체를 생성한 후, <code>ALTER AUTHENTICATION ... SET</code> 명령어를 통해 세부 파라미터를 설정합니다.</p>

    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 공통 파라미터</dt>
      <dd class="feature-dd">
        <strong>host:</strong> LDAP 서버의 URL (예: <code>ldap://&lt;LDAP_IP&gt;:389</code> 또는 <code>ldaps://&lt;LDAP_IP&gt;:636</code>)<br>
        <strong>ldap_continue:</strong> <code>yes</code> 설정 시, '사용자 찾을 수 없음' 에러 발생 시에도 연결을 재시도합니다.<br>
        <strong>starttls:</strong> 연결을 TLS로 업그레이드할지 여부를 결정합니다. (<code>soft</code>: 서버가 지원하면 TLS 사용, <code>hard</code>: 서버가 지원하지 않으면 연결 거부)
      </dd>
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> LDAP Bind (단순 바인드) 파라미터</dt>
      <dd class="feature-dd">
        사용자를 식별하기 위한 '바인드 이름(Bind Name)' 문자열을 생성할 때 사용합니다. 다음 중 하나의 조합만 선택해야 합니다.<br>
        <ul>
          <li><strong>binddn_prefix & binddn_suffix:</strong> 바인드 문자열의 앞/뒷부분을 지정 (예: <code>cn=</code>, <code>,ou=Users,dc=example,dc=com</code>)</li>
          <li><strong>domain_prefix:</strong> <code>도메인\사용자명</code> 형식 생성 (예: <code>Example\username</code>)</li>
          <li><strong>email_suffix:</strong> <code>사용자명@도메인</code> 형식 생성 (예: <code>username@example.com</code>)</li>
        </ul>
        <div class="syntax-box">
          <strong>LDAP Bind 설정 예시:</strong>
          <pre><code>ALTER AUTHENTICATION v_ldap SET
    host='ldap://&lt;LDAP_IP&gt;',
    binddn_prefix='cn=', binddn_suffix=',ou=OrgUsers,dc=example,dc=com';</code></pre>
        </div>
      </dd>
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> LDAP Search and Bind (검색 및 바인드) 파라미터</dt>
      <dd class="feature-dd">
        디렉토리를 먼저 검색한 후 인증하는 방식에 사용됩니다. Vertica는 이 방식을 권장합니다.<br>
        <ul>
          <li><strong>basedn:</strong> 검색을 시작할 기본 DN(Distinguished Name)</li>
          <li><strong>binddn:</strong> 검색을 수행하기 위해 사용할 관리자 또는 특정 계정의 DN (생략 시 익명 검색)</li>
          <li><strong>bind_password:</strong> <code>binddn</code> 계정의 비밀번호</li>
          <li><strong>search_attribute:</strong> LDAP 서버에서 사용자를 찾을 때 기준으로 삼을 속성 (예: <code>uid</code>, <code>cn</code>, <code>sAMAccountName</code>)</li>
        </ul>
        <div class="syntax-box">
          <strong>LDAP Search and Bind 설정 예시:</strong>
          <pre><code>ALTER AUTHENTICATION v_ldap SET
    host='ldap://&lt;LDAP_IP&gt;',
    basedn='ou=People,dc=example,dc=com',
    binddn='cn=admin,dc=example,dc=com',
    bind_password='&lt;ADMIN_PASSWORD&gt;',
    search_attribute='uid';</code></pre>
        </div>
      </dd>
    </dl>
    <ul class="feature-list" style="margin-top: 1rem;">
      <li><span class="feature-list__icon">💡</span> <strong>참고:</strong> <span>바인드 파라미터(<code>binddn_prefix</code> 등)를 하나도 설정하지 않으면 Vertica는 자동으로 Search and Bind 방식을 시도합니다.</span></li>
      <li><span class="feature-list__icon">💡</span> <strong>FALLTHROUGH:</strong> <span>LDAP 인증 실패 시 로컬 계정으로 접속을 시도하게 하려면 <code>ALTER AUTHENTICATION ldap_auth FALLTHROUGH;</code>를 설정합니다.</span></li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. LDAP 연동 실습 (OpenLDAP & Vertica)</h3>
    <p class="section-description">Docker Compose를 이용하여 OpenLDAP 서버를 구축하고, Vertica에서 LDAP Search and Bind 방식으로 인증을 설정하는 과정을 실습합니다.</p>

    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">1단계: OpenLDAP 서버 구축 (Docker Compose)</h4>
      <p class="section-description">OpenLDAP 서버를 실행할 호스트에 Docker Compose 파일을 생성하고 실행합니다.</p>
      <div class="syntax-box">
        <strong>docker-compose.yml:</strong>
        <pre><code>version: '3.8'

services:
  openldap:
    image: osixia/openldap:latest
    container_name: openldap-server
    environment:
      - LDAP_ORGANISATION=MyCompany
      - LDAP_DOMAIN=example.com
      - LDAP_ADMIN_PASSWORD=&lt;ADMIN_PASSWORD&gt;
    ports:
      - "389:389" # LDAP
      - "636:636" # LDAPS
    volumes:
      - ./ldap_data:/var/lib/ldap
      - ./ldap_config:/etc/ldap/slapd.d

  phpldapadmin:
    image: osixia/phpldapadmin:latest
    container_name: phpldapadmin
    environment:
      - PHPLDAPADMIN_LDAP_HOSTS=openldap
      - PHPLDAPADMIN_HTTPS=false
    ports:
      - "8080:80" # phpLDAPadmin 웹 UI
    depends_on:
      - openldap</code></pre>
        <strong>실행:</strong>
        <pre><code>mkdir -p ~/ldap-docker && cd ~/ldap-docker
docker-compose up -d</code></pre>
      </div>
      <ul class="feature-list" style="margin-top: 1rem;">
        <li><span class="feature-list__icon">💡</span> <strong>phpLDAPadmin:</strong> <span><code>http://&lt;LDAP_HOST_IP&gt;:8080</code>으로 접속하여 LDAP 사용자 및 그룹을 시각적으로 관리할 수 있습니다. (로그인 DN: <code>cn=admin,dc=example,dc=com</code>, PW: <code>&lt;ADMIN_PASSWORD&gt;</code>)</span></li>
      </ul>
    </div>

    <div class="step-section">
      <h4 class="step-title">2단계: Vertica LDAP 인증 설정</h4>
      <p class="section-description">Vertica에서 LDAP 인증 객체를 생성하고, OpenLDAP 서버의 정보를 기반으로 Search and Bind 파라미터를 설정합니다.</p>
      <div class="syntax-box">
        <strong>Vertica SQL 실행:</strong>
        <pre><code>-- 1. LDAP 인증 객체 생성
CREATE AUTHENTICATION ldap_auth METHOD 'ldap' HOST '0.0.0.0/0';

-- 2. LDAP 서버 정보 및 Search and Bind 파라미터 설정
ALTER AUTHENTICATION ldap_auth SET 
    host='ldap://&lt;LDAP_HOST_IP&gt;', 
    basedn='ou=People,dc=example,dc=com',
    binddn='cn=admin,dc=example,dc=com',
    bind_password='&lt;ADMIN_PASSWORD&gt;',
    search_attribute='uid';

-- 3. 인증 활성화 및 우선순위 설정
ALTER AUTHENTICATION ldap_auth PRIORITY 100 ENABLE;

-- 4. Vertica 사용자 생성 및 LDAP 인증 할당
DROP USER IF EXISTS test_user CASCADE;
CREATE USER test_user IDENTIFIED BY 'local_password'; -- 로컬 패스워드는 FALLTHROUGH 테스트용
GRANT AUTHENTICATION ldap_auth TO test_user;</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">3단계: LDAP 인증 테스트</h4>
      <p class="section-description">LDAP에 등록된 사용자 계정으로 Vertica에 접속하여 인증이 성공하는지 확인합니다.</p>
      <div class="syntax-box">
        <strong>vsql 접속 테스트:</strong>
        <pre><code># OpenLDAP에 등록된 'test_user'의 비밀번호로 접속 시도
vsql -U test_user -h &lt;VERTICA_HOST_IP&gt; -w '&lt;LDAP_USER_PASSWORD&gt;' -c "SELECT user_name, client_authentication_name FROM sessions;"
-- 결과:
--  user_name  | client_authentication_name
-- -----------+----------------------------
--  test_user | ldap_auth

# LDAP 인증 실패 시 로컬 패스워드로 대체 시도 (FALLTHROUGH 비활성화 상태)
vsql -U test_user -h &lt;VERTICA_HOST_IP&gt; -w 'wrong_ldap_password' -c "SELECT user_name FROM sessions;"
-- 결과: FATAL 2248: Authentication failed for username "test_user" (실패)

# FALLTHROUGH 활성화 후 로컬 패스워드로 접속 시도
ALTER AUTHENTICATION ldap_auth FALLTHROUGH;
vsql -U test_user -h &lt;VERTICA_HOST_IP&gt; -w 'local_password' -c "SELECT user_name, client_authentication_name FROM sessions;"
-- 결과:
--  user_name  | client_authentication_name
-- -----------+----------------------------
--  test_user | default_hash_network_ipv4 (로컬 인증으로 전환)</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">4단계: LDAP 인증 정보 조회</h4>
      <p class="section-description">시스템 테이블을 통해 설정된 LDAP 인증 파라미터와 사용자별 인증 방식을 확인할 수 있습니다.</p>
      <div class="syntax-box">
        <strong>Vertica SQL 실행:</strong>
        <pre><code>-- 특정 LDAP 인증 객체의 파라미터 조회
SELECT auth_parameter_name, auth_parameter_value
FROM CLIENT_AUTH_PARAMS
WHERE AUTH_NAME = 'ldap_auth'
ORDER BY auth_parameter_name;

-- 사용자에게 할당된 인증 방식 조회
SELECT user_name, auth_name, is_auth_enabled, auth_method
FROM USER_CLIENT_AUTH uca
JOIN CLIENT_AUTH ca ON uca.auth_oid = ca.auth_oid
WHERE user_name = 'test_user'
ORDER BY method_priority;</code></pre>
      </div>
    </div>
  </div>
</div>

<hr style="margin: 3rem 0;">

<div id="tls" style="scroll-margin-top: 100px;"></div>

## TLS 보안 

<div class="architecture-section">
  <p class="section-description">TLS(Transport Layer Security)는 네트워크를 통해 전송되는 데이터를 암호화하고, 인증서를 통해 서버와 클라이언트의 신원을 확인하여 통신의 <strong>암호화, 인증, 무결성</strong>을 보장하는 핵심 보안 프로토콜입니다. Vertica는 데이터베이스 내에서 직접 인증서를 생성하고 관리하며, 서버와 클라이언트가 서로의 인증서를 검증하는 <strong>상호 인증(Mutual Mode)</strong>을 통해 보안을 극대화할 수 있습니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. 인증서 생성 (In-Database)</h3>
    <p class="section-description">Vertica는 <code>CREATE KEY</code>와 <code>CREATE CERTIFICATE</code> 명령어를 사용하여 데이터베이스 내부에서 직접 CA(인증 기관), 서버, 클라이언트 인증서를 생성하고 관리할 수 있습니다.</p>
    <div class="syntax-box">
      <strong>인증서 생성 절차:</strong>
      <pre><code>-- 1. CA(인증 기관) 키 및 인증서 생성
CREATE KEY k_ca TYPE 'RSA' LENGTH 4096;
CREATE CA CERTIFICATE c_ca
    SUBJECT '/C=KR/ST=Seoul/L=Seoul/O=MyCompany/OU=CA/CN=My Vertica Root CA'
    VALID FOR 3650
    KEY k_ca;

-- 2. 서버 키 및 인증서 생성
CREATE KEY k_server TYPE 'RSA' LENGTH 2048;
CREATE CERTIFICATE c_server
    SUBJECT '/C=KR/ST=Seoul/L=Seoul/O=MyCompany/OU=server/CN=&lt;VERTICA_HOST_IP&gt;'
    SIGNED BY c_ca
    EXTENSIONS 'extendedKeyUsage' = 'serverAuth', 'subjectAltName' = 'IP:&lt;VERTICA_HOST_IP&gt;'
    KEY k_server;

-- 3. 클라이언트(사용자) 키 및 인증서 생성
CREATE KEY k_user1 TYPE 'RSA' LENGTH 2048;
CREATE CERTIFICATE c_user1
    SUBJECT '/C=KR/ST=Seoul/L=Seoul/O=MyCompany/OU=client/CN=user1'
    SIGNED BY c_ca
    EXTENSIONS 'extendedKeyUsage' = 'clientAuth'
    KEY k_user1;</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 서버 측 TLS 설정</h3>
    <p class="section-description">생성된 서버 인증서와 CA 인증서를 Vertica의 <code>server</code> TLS 구성에 적용하고, 상호 인증을 위해 <code>TLSMODE</code>를 <code>VERIFY_CA</code>로 설정합니다.</p>
    <div class="syntax-box">
      <strong>서버 TLS 구성:</strong>
      <pre><code>-- 1. 서버 TLS 구성에 인증서 적용
ALTER TLS CONFIGURATION server CERTIFICATE c_server ADD CA CERTIFICATES c_ca;

-- 2. 상호 인증 모드(Mutual Mode) 활성화
ALTER TLS CONFIGURATION server TLSMODE 'VERIFY_CA';</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 TLSMODE 값</dt>
      <dd class="feature-dd">
        <strong>DISABLE:</strong> TLS를 비활성화합니다.<br>
        <strong>ENABLE:</strong> 서버 인증만 수행하는 단방향 TLS를 활성화합니다.<br>
        <strong>VERIFY_CA:</strong> 클라이언트 인증서가 신뢰할 수 있는 CA에서 발급되었는지 검증하는 상호 인증을 활성화합니다. (권장)<br>
        <strong>VERIFY_FULL:</strong> CA 검증과 함께 클라이언트 인증서의 호스트 이름 일치 여부까지 검증합니다.
      </dd>
    </dl>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. 클라이언트 접속 설정</h3>
    <p class="section-description">클라이언트가 TLS 인증을 통해 접속할 수 있도록 필요한 인증서 파일을 서버에서 추출하여 클라이언트 환경에 맞게 설정합니다.</p>
    
    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">vsql 접속 설정</h4>
      <p class="section-description"><code>vsql</code>로 접속하려면 클라이언트의 키, 인증서, 그리고 CA 인증서를 <code>~/.vsql/</code> 디렉터리에 저장해야 합니다.</p>
      <div class="syntax-box">
        <strong>인증서 파일 추출 및 접속:</strong>
        <pre><code># 1. 인증서 및 키 파일 추출
mkdir -p ~/.vsql
vsql -At -c "SELECT key FROM cryptographic_keys WHERE name = 'k_user1';" -o ~/.vsql/client.key
vsql -At -c "SELECT certificate_text FROM certificates WHERE name = 'c_user1';" -o ~/.vsql/client.crt
vsql -At -c "SELECT certificate_text FROM certificates WHERE name = 'c_ca';" -o ~/.vsql/root.crt

# 2. 파일 권한 설정 (중요)
chmod 600 ~/.vsql/*

# 3. vsql 접속 (verify-ca 모드)
vsql -h &lt;VERTICA_HOST_IP&gt; -U user1 -m verify-ca</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">DBeaver (JDBC) 접속 설정</h4>
      <p class="section-description">DBeaver와 같은 Java 기반 GUI 도구는 클라이언트 인증서를 Java KeyStore 형식(.p12)으로 변환하고, CA 인증서를 Java의 신뢰 저장소(cacerts)에 등록해야 합니다.</p>
      <div class="syntax-box">
        <strong>인증서 변환 및 등록:</strong>
        <pre><code># 1. 클라이언트 인증서와 키를 PKCS#12 (.p12) 파일로 통합
openssl pkcs12 -export -in client.crt -inkey client.key -out client_cert.p12 -name "vertica_client" -passout pass:&lt;p12_password&gt;

# 2. CA 인증서를 DER 형식으로 변환
openssl x509 -in root.crt -out root.crt.der -outform der

# 3. Java 신뢰 저장소(cacerts)에 CA 인증서 추가
# (DBeaver에 내장된 JRE 경로 또는 시스템 JRE 경로 사용)
keytool -import -v -trustcacerts -alias vertica_ca -file root.crt.der -keystore "&lt;JRE_PATH&gt;/lib/security/cacerts"</code></pre>
      </div>
      <dl class="feature-dl" style="margin-top: 1rem;">
        <dt class="feature-dt"><span class="feature-dt__icon">◆</span> DBeaver 드라이버 속성</dt>
        <dd class="feature-dd">
          <strong>ssl:</strong> <code>true</code><br>
          <strong>tlsmode:</strong> <code>verify-ca</code><br>
          <strong>keystore:</strong> 생성한 <code>client_cert.p12</code> 파일의 전체 경로<br>
          <strong>keystorepassword:</strong> <code>.p12</code> 파일 생성 시 지정한 비밀번호
        </dd>
      </dl>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">4. TLS 인증 레코드 생성 및 확인</h3>
    <p class="section-description">TLS 인증을 사용할 사용자와 IP 대역을 지정하는 인증 레코드를 생성하고, 실제 접속이 TLS를 통해 이루어졌는지 확인합니다.</p>
    <div class="syntax-box">
      <strong>인증 레코드 생성 및 접속 확인:</strong>
      <pre><code>-- 1. TLS 인증 레코드 생성 및 사용자에게 권한 부여
CREATE AUTHENTICATION auth_tls METHOD 'tls' HOST TLS '0.0.0.0/0';
GRANT AUTHENTICATION auth_tls TO user1;

-- 2. 접속 후 세션 정보 확인
SELECT user_name, ssl_state, tls_version, authentication_method 
FROM user_sessions 
WHERE session_id = CURRENT_SESSION();
--  user_name | ssl_state | tls_version | authentication_method
-- -----------+-----------+-------------+-----------------------
--  user1     | Mutual    | TLSv1.2     | TLS</code></pre>
    </div>
  </div>
</div>

<hr style="margin: 3rem 0;">

<div id="okta" style="scroll-margin-top: 100px;"></div>

## Okta 연동

<div class="architecture-section">
  <p class="section-description">Okta와 같은 IdP(Identity Provider)를 활용하여 Vertica 데이터베이스 및 Management Console(MC)에 대한 SSO(Single Sign-On)를 구현할 수 있습니다. 이를 통해 중앙에서 사용자 계정을 관리하고 보안을 강화할 수 있습니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. Vertica DB 인증 (OAuth 2.0)</h3>
    <p class="section-description">클라이언트 애플리케이션이 Okta를 통해 인증하고 발급받은 Access Token을 사용하여 Vertica 데이터베이스에 접속하는 방식입니다.</p>

    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">1단계: Okta 애플리케이션 설정</h4>
      <p class="section-description">Okta 관리자 콘솔에서 Vertica 연동을 위한 애플리케이션을 생성하고, 'Resource Owner Password' Grant Type을 활성화해야 합니다.</p>
      <dl class="feature-dl">
        <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 설정 항목</dt>
        <dd class="feature-dd">
          <strong>Grant Type:</strong> <code>Resource Owner Password</code>를 활성화하여 사용자의 ID/PW로 직접 토큰을 발급받을 수 있도록 설정합니다.<br>
          <strong>PKCE:</strong> <code>Require PKCE as additional verification</code> 옵션을 해제하여 일부 클라이언트와의 호환성을 확보합니다.<br>
          <strong>Access Policy:</strong> Security > API 메뉴에서 Access Policy를 생성하고, Rule의 'Grant type'에 <code>Resource Owner Password</code>를 허용하도록 추가합니다.<br>
          <strong>Sign On Policy:</strong> 사용자가 패스워드로 인증할 수 있도록 Sign On 정책 Rule에서 'User must authenticate with' 옵션을 <code>Password</code>로 설정합니다.
        </dd>
      </dl>
    </div>

    <div class="step-section">
      <h4 class="step-title">2단계: Vertica OAuth 인증 설정</h4>
      <p class="section-description">Vertica에서 OAuth 인증 객체를 생성하고, Okta 애플리케이션의 정보를 등록합니다.</p>
      <div class="syntax-box">
        <strong>OAuth 인증 설정 예시:</strong>
        <pre><code>-- 1. OAuth 인증 객체 생성
CREATE AUTHENTICATION v_oauth METHOD 'oauth' HOST '0.0.0.0/0';

-- 2. Okta 애플리케이션 정보 설정
ALTER AUTHENTICATION v_oauth SET 
    validate_type = 'IDP',
    client_id = '&lt;YOUR_OKTA_CLIENT_ID&gt;',
    client_secret = '&lt;YOUR_OKTA_CLIENT_SECRET&gt;',
    introspect_url = 'https://&lt;YOUR_OKTA_DOMAIN&gt;/oauth2/v1/introspect';

-- 3. 인증 활성화 및 사용자에게 권한 부여
ALTER AUTHENTICATION v_oauth ENABLE PRIORITY 100;
GRANT AUTHENTICATION v_oauth TO "user@example.com";</code></pre>
      </div>
      <ul class="feature-list" style="margin-top: 1rem;">
        <li><span class="feature-list__icon">💡</span> <strong>접속 방식:</strong> <span>클라이언트 애플리케이션(Python, Java 등)은 먼저 사용자의 Okta ID/PW를 사용하여 Okta로부터 <strong>Access Token</strong>을 발급받습니다. 그 후, Vertica에 접속할 때 이 Access Token을 <strong>비밀번호</strong>로 사용하여 인증을 수행합니다.</span></li>
      </ul>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. Vertica Management Console (MC) 인증 (OpenID Connect)</h3>
    <p class="section-description">MC 로그인 시 Okta 로그인 페이지로 리디렉션하여 SSO를 구현하는 방식입니다. 이를 통해 MC 사용자 계정을 Okta에서 중앙 관리할 수 있습니다.</p>

    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">1단계: MC OpenID Connect 설정</h4>
      <p class="section-description">MC 관리 페이지의 'Identity Providers' 메뉴에서 OpenID Connect를 설정하고 Okta의 Discovery Endpoint를 등록합니다.</p>
      <dl class="feature-dl">
        <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 설정 항목</dt>
        <dd class="feature-dd">
          <strong>Discovery endpoint:</strong> Okta의 OIDC 설정 메타데이터 URL을 입력합니다. (예: <code>https://&lt;YOUR_OKTA_DOMAIN&gt;/.well-known/openid-configuration</code>)<br>
          <strong>Trust Email:</strong> <code>On</code>으로 설정하여 이메일 주소를 신뢰합니다.<br>
          <strong>First login flow override:</strong> <code>first broker login (registration)</code>으로 설정하여 최초 로그인 시 MC에 사용자를 자동 등록합니다.<br>
          <strong>Sync mode:</strong> <code>Import</code>로 설정하여 Okta의 사용자 정보를 MC로 가져옵니다.
        </dd>
      </dl>
      <div class="image-grid-2">
        <div class="image-box-styled">
          <img src="{{ '/assets/images/okta1.png' | relative_url }}" alt="Okta OIDC Provider 설정 1">
        </div>
        <div class="image-box-styled">
          <img src="{{ '/assets/images/okta2.png' | relative_url }}" alt="Okta OIDC Provider 설정 2">
        </div>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">2단계: MC Attribute Mappers 설정</h4>
      <p class="section-description">Okta에서 전달받은 사용자 정보(Claim)를 MC의 사용자 속성(Attribute)에 매핑하여 사용자 정보를 동기화합니다.</p>
      <div class="image-box-styled" style="margin-bottom: 1.5rem;">
        <img src="{{ '/assets/images/okta3.png' | relative_url }}" alt="Okta Attribute Mappers 설정">
      </div>
      <table class="info-table">
        <thead>
          <tr>
            <th>Mapper Name</th>
            <th>Mapper Type</th>
            <th>Claim</th>
            <th>User Attribute Name</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>username-mapping</td>
            <td>Attribute Importer</td>
            <td>preferred_username</td>
            <td>username</td>
          </tr>
          <tr>
            <td>email-mapping</td>
            <td>Attribute Importer</td>
            <td>email</td>
            <td>email</td>
          </tr>
          <tr>
            <td>firstname-mapping</td>
            <td>Attribute Importer</td>
            <td>given_name</td>
            <td>firstName</td>
          </tr>
          <tr>
            <td>lastname-mapping</td>
            <td>Attribute Importer</td>
            <td>family_name</td>
            <td>lastName</td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="step-section">
      <h4 class="step-title">3단계: Okta SSO 로그인</h4>
      <p class="section-description">모든 설정이 완료되면, MC 로그인 화면에 'Log in with Okta' 버튼이 나타나며, 이를 통해 Okta 계정으로 SSO 로그인을 수행할 수 있습니다.</p>
      <div class="image-box-styled">
        <img src="{{ '/assets/images/okta4.png' | relative_url }}" alt="Vertica MC Okta 로그인 화면">
      </div>
    </div>
  </div>
</div>

</div>

  <aside class="page-sidebar">
    <div class="sidebar-panel" style="padding-right: 1rem;">
      <h3>On this page</h3>
      <ul>
        <li><a href="#auth-overview">Client Auth</a></li>
        <li><a href="#ldap">LDAP 인증</a></li>
        <li><a href="#tls">TLS 보안</a></li>
        <li><a href="#okta">Okta 연동</a></li>
      </ul>
    </div>
  </aside>
</div>