---
title: Vertica Administration
layout: default
---
<style>
  :root {
    --bg-1: #f8fbff;
    --bg-2: #eef4ff;
    --bg-3: #dfe9ff;
    --card-bg: rgba(255, 255, 255, 0.95);
    --text: #0f1f3d;
    --sub: #556b8a;
    --accent: #2d69ff;
    --accent-2: #1c44d9;
    --shadow: 0 20px 50px rgba(45,105,255,0.08);
    --border-color: rgba(45, 105, 255, 0.14);
  }

  .page-hero {
    text-align: center;
    padding: 2rem 1rem;
  }

  .chip {
    display: inline-block;
    padding: 0.4rem 0.85rem;
    border-radius: 999px;
    background: rgba(45, 105, 255, 0.08);
    color: var(--accent);
    font-size: 0.84rem;
    letter-spacing: 0.02em;
    margin-bottom: 1rem;
  }

  .page-hero h1 {
    margin: 0 auto;
    font-size: clamp(2rem, 4.2vw, 3.4rem);
    line-height: 1.2;
    font-weight: 800;
    color: var(--text);
  }

  .page-hero p {
    margin: 1rem auto 0;
    max-width: 680px;
    color: var(--sub);
    font-size: clamp(1rem, 2vw, 1.15rem);
    line-height: 1.7;
  }

  .button-grid-section {
    margin-top: 28px;
    padding: 26px;
    background: var(--card-bg);
    border-radius: 24px;
    border: 1px solid var(--border-color);
    box-shadow: var(--shadow);
  }

  .button-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 18px;
  }

  .button {
    display: block;
    padding: 14px 20px;
    border-radius: 12px;
    text-align: center;
    font-weight: bold;
    text-decoration: none;
    transition: transform 0.2s ease;
  }
  .button:hover {
    transform: translateY(-2px);
  }

  .button.primary {
    background-color: var(--accent);
    color: white;
  }

  .content-card {
    margin-top: 28px;
    padding: 32px;
    background: var(--card-bg);
    border-radius: 24px;
    border: 1px solid var(--border-color);
    box-shadow: var(--shadow);
  }

  .content-card h2 {
    margin-top: 0;
    color: var(--accent-2);
  }

  .content-article {
    margin-top: 24px;
  }

  .page-layout {
    display: grid;
    grid-template-columns: minmax(0, 1fr) 280px;
    gap: 28px;
    margin-top: 28px;
  }

  .page-sidebar {
    position: sticky;
    top: 104px;
    align-self: start;
  }

  .sidebar-panel {
    padding: 24px;
    border-radius: 24px;
    background: var(--card-bg);
    border: 1px solid var(--border-color);
    box-shadow: var(--shadow);
  }

  .sidebar-panel h3 {
    margin-top: 0;
    font-size: 1.05rem;
    color: var(--text);
  }

  .sidebar-panel ul {
    list-style: none;
    margin: 0;
    padding: 0;
    display: grid;
    gap: 12px;
  }

  .sidebar-panel a {
    color: var(--accent);
    font-weight: 600;
    text-decoration: none;
  }

  @media (max-width: 900px) {
    .page-layout {
      grid-template-columns: 1fr;
    }
    .page-sidebar {
      position: static;
      top: auto;
    }
  }
</style>

<div class="page-hero">
  <span class="chip">Vertica Administration</span>
  <h1>Vertica 객체 생성 및 관리</h1>
  <p>Table, Projection, Schema, User, Profile, Resource Pool, Privilege 중심으로 Vertica 운영 관리 항목을 정리합니다.</p>
</div>

<div class="page-layout">
  <main>
    <section class="button-grid-section">
      <div class="button-grid">
        <a class="button primary" href="{{ '/administration-table-projection' | relative_url }}">Table · Projection</a>
        <a class="button primary" href="{{ '/administration-schema' | relative_url }}">Schema</a>
        <a class="button primary" href="{{ '/administration-user' | relative_url }}">User</a>
        <a class="button primary" href="{{ '/administration-profile' | relative_url }}">Profile</a>
        <a class="button primary" href="{{ '/administration-resource-pool' | relative_url }}">Resource Pool</a>
        <a class="button primary" href="{{ '/administration-privilege' | relative_url }}">Privilege</a>
      </div>
    </section>

    <section class="content-card">
      <h2>Vertica 운영자 가이드 요약</h2>
      <p>버티카 운영자 가이드 문서의 운영자 관리 섹션을 바탕으로, Vertica 객체 생성 및 운영 관리 핵심 항목을 요약합니다.</p>

      <article class="content-article">
        <h3>USER 관리</h3>
        <p>Vertica 운영자는 OS 계정 <code>vertica</code>로 접속하여 사용자 계정을 생성, 수정, 삭제합니다.</p>
        <pre><code>CREATE USER user-name [ account-parameter value[,...] ]
ALTER USER user-name { account-parameter setting | SET PARAMETER cfg-parameter=value | CLEAR PARAMETER cfg-parameter }
DROP USER [ IF EXISTS ] user-name [ CASCADE ]
SELECT * FROM USERS;</code></pre>
        <p>USER는 <code>PROFILE</code>, <code>RESOURCE POOL</code>, <code>MAXCONNECTIONS</code>, <code>MEMORYCAP</code>, <code>IDLESESSIONTIMEOUT</code>, <code>PASSWORD EXPIRE</code> 등으로 제어합니다.</p>
      </article>

      <article class="content-article">
        <h3>PROFILE 관리</h3>
        <p>PROFILE은 비밀번호 정책과 로그인 제한을 관리하며, 기본 프로필 외에 별도 프로필을 생성해 적용합니다.</p>
        <pre><code>CREATE PROFILE profile-name LIMIT [ password-parameter setting ]...
ALTER PROFILE profile-name LIMIT [ password-parameter setting ]...
DROP PROFILE [ IF EXISTS ] profile-name [ CASCADE ]
SELECT * FROM PROFILES;</code></pre>
        <p>주요 파라미터는 <code>PASSWORD_LIFE_TIME</code>, <code>FAILED_LOGIN_ATTEMPTS</code>, <code>PASSWORD_LOCK_TIME</code>, <code>PASSWORD_REUSE_MAX</code>, <code>PASSWORD_MIN_LENGTH</code> 등입니다.</p>
      </article>

      <article class="content-article">
        <h3>SCHEMA 관리</h3>
        <p>SCHEMA는 객체 네임스페이스와 기본 권한 정책을 정의합니다. CREATE/ALTER/DROP/SELECT 명령으로 관리합니다.</p>
        <pre><code>CREATE SCHEMA [ IF NOT EXISTS ] [database.]schema [ AUTHORIZATION username ] [ DEFAULT { INCLUDE | EXCLUDE } [ SCHEMA ] PRIVILEGES ]
ALTER SCHEMA [database.]schema DEFAULT {INCLUDE | EXCLUDE} SCHEMA PRIVILEGES
ALTER SCHEMA [database.]schema OWNER TO user-name
ALTER SCHEMA [database.]schema RENAME TO new-schema-name
DROP SCHEMA [ IF EXISTS ] [database.]schema [ CASCADE | RESTRICT ]
SELECT * FROM SCHEMATA;</code></pre>
        <p><code>INCLUDE</code>는 SCHEMA 권한을 포함하여 하위 객체 권한을 허용하며, <code>EXCLUDE</code>는 개별 권한 제어를 기본값으로 유지합니다.</p>
      </article>

      <article class="content-article">
        <h3>RESOURCE POOL 관리</h3>
        <p>RESOURCE POOL은 쿼리 메모리와 동시 실행 정책을 제어하는 핵심 리소스입니다.</p>
        <pre><code>CREATE RESOURCE POOL pool-name [ parameter-name setting ]...
ALTER RESOURCE POOL pool-name [ parameter-name setting ]...
DROP RESOURCE POOL pool-name
SELECT * FROM RESOURCE_POOLS;</code></pre>
        <p>주요 파라미터: <code>MAXCONCURRENCY</code>, <code>MAXMEMORYSIZE</code>, <code>MAXQUERYMEMORYSIZE</code>, <code>MEMORYSIZE</code>, <code>PLANNEDCONCURRENCY</code>, <code>QUEUETIMEOUT</code>, <code>RUNTIMECAP</code>.</p>
        <p>사용자에게 리소스 풀을 할당하려면:</p>
        <pre><code>ALTER USER user-name RESOURCE POOL pool-name</code></pre>
      </article>

      <article class="content-article">
        <h3>TABLE &amp; PROJECTION</h3>
        <p>Vertica의 TABLE과 PROJECTION은 저장 구조와 조회 성능을 결정합니다.</p>
        <pre><code>CREATE TABLE [ IF NOT EXISTS ] [[database.]schema.]table
  ( column-definition[,...] [, table-constraint ][,...] )
  [ ORDER BY column[,...] ]
  [ segmentation-spec ]
  [ KSAFE [k-num] ]
  [ partition-clause ]
  [ {INCLUDE | EXCLUDE} [SCHEMA] PRIVILEGES ]
CREATE TABLE ... AS query
CREATE TABLE ... LIKE existing-table</code></pre>
        <p>ORDER BY, segmentation, KSAFE, partition 설정으로 데이터 분산과 쿼리 성능을 최적화합니다.</p>
      </article>

      <article class="content-article">
        <h3>PRIVILEGE 관리</h3>
        <p>운영자는 권한 관리를 통해 리소스 접근과 실행 정책을 통제합니다.</p>
        <pre><code>GRANT USAGE ON RESOURCE POOL resource-pool TO grantee [ WITH GRANT OPTION ]
ALTER USER user-name RESOURCE POOL resource-pool
GRANT ... TO ...
REVOKE ... FROM ...</code></pre>
        <p>RESOURCE POOL 사용 권한과 SCHEMA 권한을 명확히 분리하면 운영 안정성을 높일 수 있습니다.</p>
      </article>
    </section>
  </main>

  <aside class="page-sidebar">
    <div class="sidebar-panel">
      <h3>Administration 목차</h3>
      <ul>
        <li><a href="{{ '/administration-table-projection' | relative_url }}">Table · Projection</a></li>
        <li><a href="{{ '/administration-schema' | relative_url }}">Schema</a></li>
        <li><a href="{{ '/administration-user' | relative_url }}">User</a></li>
        <li><a href="{{ '/administration-profile' | relative_url }}">Profile</a></li>
        <li><a href="{{ '/administration-resource-pool' | relative_url }}">Resource Pool</a></li>
        <li><a href="{{ '/administration-privilege' | relative_url }}">Privilege</a></li>
      </ul>
    </div>
  </aside>
</div>
