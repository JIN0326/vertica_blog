---
title: "Vertica LDAP: OpenLDAP/AD 연동 및 인증 설정 - Vertica Blog"
layout: default
description: "Vertica와 OpenLDAP 또는 Active Directory(AD)를 연동하여 사용자 인증을 설정하는 방법을 알아봅니다. LDAP Bind 및 Search and Bind 방식, Docker를 이용한 실습 과정을 포함합니다."
keywords: "vertica, ldap, openldap, active directory, ad, authentication, ldap bind, search and bind, docker"
---

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