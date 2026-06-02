---
title: "Vertica Schema: 스키마 생성, 관리, 권한 상속 - Vertica Blog"
layout: default
description: "Vertica의 Schema(스키마) 관리 방법을 알아봅니다. 기본 스키마의 종류, 스키마 생성 및 수정, 그리고 DEFAULT INCLUDE PRIVILEGES를 통한 권한 상속 방법을 설명합니다."
keywords: "vertica, schema, create schema, alter schema, drop schema, public, v_catalog, schema inheritance"
---

<div id="schema" style="scroll-margin-top: 100px;"></div>

## Schema

<div class="architecture-section">
  <p class="section-description">Vertica에서 **Schema(스키마)**는 객체들의 논리적인 그룹이며 별도의 물리적인 특징을 가지지 않습니다. 스키마를 통해 객체 접근 권한을 분리하고 네임스페이스를 관리함으로써 운영 효율성을 높일 수 있습니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. 기본 스키마 (Default Schemas)</h3>
    <p class="section-description">데이터베이스 생성 시 시스템 관리를 위해 자동으로 생성되는 기본 스키마들입니다.</p>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>v_internal:</strong> <span>데이터베이스 내부 테이블용 스키마 </span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>v_catalog:</strong> <span>오브젝트 정보 카탈로그 스키마 </span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>v_monitor:</strong> <span>모니터링용 테이블 스키마 </span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>public:</strong> <span>일반 사용자를 위한 기본 스키마 </span></li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. Schema 생성 (CREATE SCHEMA)</h3>
    <p class="section-description">스키마는 <code>dbadmin</code> 계정으로 생성 가능하며, 권한 상속 설정을 통해 하위 객체 관리를 자동화할 수 있습니다.</p>
    <div class="syntax-box">
      <strong>기본 구문:</strong>
      <pre><code>CREATE SCHEMA [ IF NOT EXISTS ] [database.]schema 
[ AUTHORIZATION username] 
[ DEFAULT { INCLUDE | EXCLUDE } [ SCHEMA ] PRIVILEGES ];</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 파라미터</dt>
      <dd class="feature-dd">
        <strong>IF NOT EXISTS:</strong> 동일 이름의 스키마가 존재할 경우 오류 없이 통과합니다. <br>
        <strong>AUTHORIZATION:</strong> 생성과 동시에 특정 유저에게 스키마 소유권을 부여합니다. <br>
        <strong>DEFAULT INCLUDE PRIVILEGES:</strong> 스키마 권한을 하위 테이블에 자동 상속합니다.
      </dd>
    </dl>
    <p class="example-label">예시</p>
    <pre><code>-- 분석용 스키마 생성 및 권한 상속 설정
CREATE SCHEMA analytics DEFAULT INCLUDE PRIVILEGES;

-- 특정 유저 소유의 스키마 생성
CREATE SCHEMA staging AUTHORIZATION user_admin;</code></pre>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. Schema 수정 및 관리 (ALTER SCHEMA)</h3>
    <p class="section-description">스키마의 이름, 소유자, 또는 권한 상속 정책을 변경할 때 사용합니다.</p>
    <div class="syntax-box">
      <strong>주요 수정 구문:</strong>
      <pre><code>-- 스키마 이름 변경
ALTER SCHEMA schema RENAME TO new_schema_name;

-- 스키마 소유자 변경
ALTER SCHEMA schema OWNER TO username;

-- 권한 상속 설정 변경
ALTER SCHEMA schema DEFAULT {INCLUDE | EXCLUDE} SCHEMA PRIVILEGES;</code></pre>
    </div>
    <p class="example-label">예시</p>
    <pre><code>-- 스키마 이름 변경
ALTER SCHEMA old_biz RENAME TO sales_biz;

-- 소유자를 dbadmin으로 변경
ALTER SCHEMA sales_biz OWNER TO dbadmin;</code></pre>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">4. Schema 삭제 및 조회</h3>
    <div class="syntax-box">
      <strong>삭제 구문 (DROP SCHEMA):</strong>
      <pre><code>DROP SCHEMA [ IF EXISTS ] schema[,…] [ CASCADE | RESTRICT ];</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> 삭제 옵션</dt>
      <dd class="feature-dd">
        <strong>CASCADE:</strong> 스키마 내 모든 객체(테이블, 뷰 등)를 함께 삭제합니다. <br>
        <strong>RESTRICT:</strong> (기본값) 스키마가 비어있을 때만 삭제가 가능합니다.
      </dd>
    </dl>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> 정보 조회</dt>
      <dd class="feature-dd">
        전체 스키마 목록을 확인하려면 시스템 테이블을 조회합니다.
        <pre><code>SELECT * FROM SCHEMATA;</code></pre>
      </dd>
    </dl>
  </div>
</div>