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
  <p class="section-description">사내 통합 계정 관리를 위해 OpenLDAP 또는 AD 서버와 연동합니다. Vertica는 검색 후 인증하는 <strong>Search and Bind</strong> 방식을 권장합니다.</p>

  <div class="syntax-box">
    <strong>LDAP 설정 예시 (Search and Bind):</strong>
    <pre><code>CREATE AUTHENTICATION ldap_auth METHOD 'ldap' HOST '0.0.0.0/0';

ALTER AUTHENTICATION ldap_auth SET 
    host='ldap://IP', 
    basedn='ou=People,dc=example,dc=com',
    binddn='cn=admin,dc=example,dc=com',
    bind_password='adminpassword',
    search_attribute='uid';

ALTER AUTHENTICATION ldap_auth PRIORITY 100 ENABLE;
GRANT AUTHENTICATION ldap_auth TO test_user;</code></pre>
  </div>
  <p class="section-description"><strong>FALLTHROUGH:</strong> LDAP 인증 실패 시 로컬 계정으로 접속을 시도하게 하려면 <code>ALTER AUTHENTICATION ldap_auth FALLTHROUGH;</code>를 설정합니다.</p>
</div>

<hr style="margin: 3rem 0;">

<div id="tls" style="scroll-margin-top: 100px;"></div>

## TLS 보안 

<div class="architecture-section">
  <p class="section-description">네트워크 통신 암호화뿐만 아니라, 서버와 클라이언트가 서로의 인증서를 검증하는 <strong>Mutual Mode</strong>를 통해 보안을 극대화합니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">서버 측 설정</h3>
    <div class="syntax-box">
      <pre><code>-- TLS 모드를 VERIFY_CA(상호 인증)로 설정
ALTER TLS CONFIGURATION server 
    CERTIFICATE c_server ADD CA CERTIFICATES c_ca;
ALTER TLS CONFIGURATION server TLSMODE 'VERIFY_CA';

-- TLS 인증 레코드 생성
CREATE AUTHENTICATION auth_tls METHOD 'tls' HOST TLS 'IP대역/24';
GRANT AUTHENTICATION auth_tls TO user1;</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">클라이언트 접속 (vsql)</h3>
    <p class="section-description">인증서 파일(client.key, client.crt, root.crt)을 추출하여 <code>~/.vsql/</code> 경로에 저장한 후 접속합니다.</p>
    <pre><code>vsql -h DBIP -U user1 -m verify-ca</code></pre>
  </div>
</div>

<hr style="margin: 3rem 0;">

<div id="okta" style="scroll-margin-top: 100px;"></div>

## Okta 연동

<div class="architecture-section">
  <p class="section-description">Okta IdP를 활용하여 데이터베이스 및 Management Console(MC)에 대한 SSO(Single Sign-On)를 구현합니다.</p>

  <div class="syntax-box">
    <strong>OAuth 인증 설정:</strong>
    <pre><code>CREATE AUTHENTICATION v_oauth METHOD 'oauth' HOST '0.0.0.0/0';

ALTER AUTHENTICATION v_oauth SET validate_type = 'IDP'
    , client_id = 'your_client_id'
    , client_secret = 'your_client_secret'
    , introspect_url = 'https://okta-domain/oauth2/v1/introspect';
    
ALTER AUTHENTICATION v_oauth ENABLE PRIORITY 100;
GRANT AUTHENTICATION v_oauth TO "user@example.com";</code></pre>
  </div>
  <p class="section-description">애플리케이션(Python 등)에서 접속 시, Okta에서 발급받은 <strong>Access Token</strong>을 비밀번호 자리에 입력하여 인증을 수행합니다.</p>
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