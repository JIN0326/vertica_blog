---
title: "Vertica Privilege & Role: 권한 관리, 상속, 위임 - Vertica Blog"
layout: default
description: "Vertica의 권한(Privilege) 및 역할(Role) 관리 방법을 알아봅니다. 스키마 권한 상속, Role 기반 권한 부여, 그리고 WITH GRANT OPTION을 사용한 권한 위임 방법을 설명합니다."
keywords: "vertica, privilege, role, grant, revoke, schema inheritance, with grant option"
canonical_url: "https://jin0326.github.io/vertica_blog/200administration/200administration#privilege"
---

<div id="privilege" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody" markdown="1">
## Privilege
<div class="architecture-section">
  <p class="section-description"><strong>Privilege(권한)</strong>는 사용자가 SCHEMA, TABLE, RESOURCE POOL 등의 객체에 접근하고 조작할 수 있는 권리를 의미합니다. Vertica에서는 <strong>Role(역할)</strong> 기반의 권한 관리와 <strong>스키마 권한 상속(Schema Inheritance)</strong>을 적극 활용하여 복잡한 권한 체계를 효율적으로 관리할 수 있습니다.</p>

  <div class="image-box-styled" style="margin-bottom: 2.5rem;">
    <img src="/vertica_blog/assets/images/object_privileges.png" alt="Vertica Object Privileges">
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. 권한 상속 및 Role 활용 실습 (Schema Inheritance & Roles)</h3>
    <p class="section-description">스키마의 <code>DEFAULT INCLUDE PRIVILEGES</code> 옵션이 어떻게 동작하는지, Role을 통해 권한을 어떻게 부여하는지 실습을 통해 알아봅니다.</p>
    
    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">1단계: 환경 구성 (Schema, User, Role 생성)</h4>
      <div class="syntax-box">
        <pre><code>-- 권한 상속이 비활성화된 스키마
CREATE SCHEMA sc1;
-- 권한 상속이 활성화된 스키마
CREATE SCHEMA sc2 DEFAULT INCLUDE SCHEMA PRIVILEGES;

-- 사용자 및 역할 생성
CREATE USER u1; CREATE USER u2;
CREATE ROLE r1; CREATE ROLE r2;</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">2단계: Role에 권한 부여 및 사용자에게 Role 할당</h4>
      <div class="syntax-box">
        <pre><code>-- 각 Role에 스키마 사용 및 조회 권한 부여
GRANT USAGE, SELECT ON SCHEMA sc1 TO r1;
GRANT USAGE, SELECT ON SCHEMA sc2 TO r2;

-- 각 사용자에게 Role 부여
GRANT r1 TO u1;
GRANT r2 TO u2;

-- 사용자 접속 시 기본 Role 설정
ALTER USER u1 DEFAULT ROLE r1;
ALTER USER u2 DEFAULT ROLE r2;</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">3단계: 테이블 생성 후 권한 상속 테스트</h4>
      <p class="section-description">각 스키마에 테이블을 생성하고, 사용자들이 해당 테이블을 조회할 수 있는지 확인합니다.</p>
      <div class="syntax-box">
        <pre><code>-- 각 스키마에 테이블 생성
CREATE TABLE sc1.t1(a int);
CREATE TABLE sc2.t1(a int);

-- u1 사용자로 sc1.t1 조회 시도
--- 결과: 실패 (ERROR: Permission denied for relation t1)
--- 이유: sc1은 권한 상속이 비활성화되어, 스키마에 부여된 SELECT 권한이 하위 테이블 t1에 자동으로 적용되지 않음
vsql -U u1 -c "SELECT * FROM sc1.t1;"

-- u2 사용자로 sc2.t1 조회 시도
--- 결과: 성공
--- 이유: sc2는 'DEFAULT INCLUDE SCHEMA PRIVILEGES'로 생성되어, 스키마에 부여된 SELECT 권한이 하위 테이블 t1에 자동으로 상속됨
vsql -U u2 -c "SELECT * FROM sc2.t1;"</code></pre>
      </div>
      <ul class="feature-list" style="margin-top: 1rem;">
        <li><span class="feature-list__icon">💡</span> <strong>Tip:</strong> <span>권한 상속이 비활성화된 스키마(`sc1`)의 테이블을 조회하려면, 테이블에 대한 `SELECT` 권한을 명시적으로 부여해야 합니다. (<code>GRANT SELECT ON TABLE sc1.t1 TO r1;</code>)</span></li>
      </ul>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 권한 위임 (WITH GRANT OPTION)</h3>
    <p class="section-description"><code>WITH GRANT OPTION</code>은 특정 사용자에게 부여된 권한을 다른 사용자에게 다시 부여할 수 있는 '위임' 권한을 줍니다. 특히, 다른 사용자가 생성한 뷰(View)를 조회해야 할 때 이 옵션이 중요하게 사용됩니다.</p>
    
    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">문제 상황: 뷰(View) 조회 권한</h4>
      <p class="section-description">사용자 `u2`가 `u1`이 생성한 뷰 `v_t`를 조회하려고 합니다. `v_t`는 `sc4.t1` 테이블을 참조합니다. `u2`는 뷰에 대한 `SELECT` 권한은 있지만, 기반 테이블 `sc4.t1`에 대한 권한이 없어 조회가 실패합니다.</p>
      <div class="syntax-box">
        <pre><code>-- 1. u1에게 sc4.t1 테이블 조회 권한 부여 (위임 권한 없음)
CREATE SCHEMA sc4;
CREATE TABLE sc4.t1(a int);
GRANT USAGE, SELECT, CREATE ON SCHEMA PUBLIC TO u1;
GRANT USAGE, SELECT, CREATE ON SCHEMA sc4 TO u1;
GRANT SELECT ON TABLE sc4.t1 TO u1;
GRANT SELECT ON SCHEMA PUBLIC TO u2;


-- 2. u1이 sc4.t1을 참조하는 뷰 생성
--- (u1은 뷰의 소유자이므로, 다른 사용자에게 이 뷰에 대한 권한을 부여할 수 있음)
vsql -U u1 -c "CREATE VIEW v_t AS SELECT * FROM sc4.t1;"
vsql -U u1 -c "SELECT * FROM v_t;"

-- 3. DBA가 u2에게 뷰 조회 권한 부여
GRANT SELECT ON TABLE v_t TO u2;

-- 4. u2가 뷰 조회 시도 -> 실패
--- 이유: u2는 뷰의 기반 테이블인 sc4.t1에 대한 SELECT 권한이 없음
vsql -U u2 -c "SELECT * FROM v_t;"</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">해결 방법: WITH GRANT OPTION을 통한 권한 위임</h4>
      <p class="section-description">DBA가 `u1`에게 `WITH GRANT OPTION`을 부여하면, `u1`은 자신이 받은 `sc4.t1` 테이블의 `SELECT` 권한을 `u2`에게 위임할 수 있습니다. 이로써 `u2`는 뷰와 기반 테이블 모두에 대한 권한을 갖게 되어 조회가 가능해집니다.</p>
      <div class="syntax-box">
        <pre><code>-- 1. DBA가 u1에게 sc4.t1에 대한 SELECT 권한을 '위임 옵션'과 함께 부여
GRANT SELECT ON TABLE sc4.t1 TO u1 WITH GRANT OPTION;

-- 2. u1이 자신이 받은 권한을 u2에게 위임
vsql -U u1 -c "GRANT SELECT ON TABLE sc4.t1 TO u2;"

-- 3. u2가 뷰 조회 시도 -> 성공
--- 이유: u2는 이제 뷰(v_t)와 기반 테이블(sc4.t1) 모두에 대한 SELECT 권한을 가짐
vsql -U u2 -c "SELECT * FROM v_t;"</code></pre>
      </div>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. 권한 정보 종합 조회</h3>
    <p class="section-description">다음 쿼리는 데이터베이스 내의 모든 사용자와 역할(Role)에 부여된 스키마 및 테이블 권한을 종합적으로 조회합니다. 권한 현황을 감사하거나 복잡한 권한 문제를 진단할 때 유용합니다.</p>
    <div class="syntax-box">
      <strong>사용자 및 역할 권한 조회 쿼리:</strong>
      <pre><code>SELECT 
    'usernm' AS gbn, 
    user_name, 
    is_super_user, 
    resource_pool,
    CASE WHEN all_roles <> 'dbduser*, dbadmin*, pseudosuperuser*' THEN all_roles ELSE 'admin' END AS all_roles,
    CASE WHEN default_roles <> 'dbduser*, dbadmin*, pseudosuperuser*' THEN default_roles ELSE 'admin' END AS default_roles,
    CASE WHEN search_path <> '"$user", public, v_catalog, v_monitor, v_internal, v_func' THEN search_path ELSE 'default' END AS search_path,
    object_type, 
    object_schema, 
    object_name,
    CASE WHEN user_name ILIKE 'vertica' THEN 'admin' ELSE privileges_description END AS privileges_description
FROM users t1
LEFT OUTER JOIN (
    SELECT DISTINCT grantee, grantor, object_type, object_schema,
           CASE WHEN object_type ILIKE 'table' THEN '' ELSE object_name END AS object_name,
           privileges_description
    FROM grants 
    WHERE object_type ILIKE ANY ('schema','table')
) t2 ON user_name = grantee
UNION ALL
SELECT 
    'rolenm' AS gbn,  
    t1.object_name, 
    NULL, '', '', '', '',
    t2.object_type, 
    object_schema, 
    t2.object_name,
    CASE WHEN grantee ILIKE 'vertica' THEN 'admin' ELSE privileges_description END AS privileges_description
FROM (
    SELECT object_name, object_type 
    FROM grants 
    WHERE object_type ILIKE 'role' AND object_name NOT ILIKE 'useradmin'
) t1
LEFT OUTER JOIN (
    SELECT DISTINCT grantee, grantor, object_type, object_schema,
           CASE WHEN object_type ILIKE 'table' THEN '' ELSE object_name END AS object_name,
           privileges_description
    FROM grants 
    WHERE object_type ILIKE ANY ('schema','table')
) t2 ON t1.object_name = t2.grantee
ORDER BY 1, 2, 3;</code></pre>
    </div>
  </div>
</div>
</div>
</div>