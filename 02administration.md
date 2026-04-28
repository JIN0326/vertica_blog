---
title: Vertica Administration
layout: default
---
<div class="page-hero">
  <span class="chip">Administration</span>
  <h1>Vertica Administration</h1>
  <p>Vertica 데이터베이스의 핵심 객체(Table, Schema, User 등)를 관리하고 운영하는 방법을 상세히 설명합니다. 아래 목차를 통해 원하는 섹션으로 바로 이동할 수 있습니다.</p>
</div>

<div class="page-layout"> 
<div class="content-section" markdown="1">

<div id="table-projection" style="scroll-margin-top: 100px;"></div>

## Table · Projection

<p>Vertica 아키텍처의 핵심은 **논리적 모델(Table)**과 **물리적 저장(Projection)**의 완벽한 분리에 있습니다. 데이터베이스에 질의를 던질 때, Vertica의 옵티마이저는 논리적 Table에 연결된 여러 물리적 Projection 중 가장 응답 속도가 빠른 것을 스스로 선택하여 쿼리를 수행합니다.</p>

<div class="feature-box" style="margin-top: 2rem;">
  <h3 class="eon-section-title" style="margin-top: 0; margin-bottom: 1rem;">Table (논리적 모델)</h3>
  <p style="color: var(--sub); margin-bottom: 1.5rem;">대부분의 상용 DB 데이터 타입과 호환되는 데이터 논리 모델링의 오브젝트입니다. 테이블 정의에는 특별한 옵션이 필요하지 않으며, 대부분의 부가적인 옵션은 파티션 구문 정도입니다.</p>
  
  <dl class="feature-dl">
    <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 지원 데이터 타입</dt>
    <dd class="feature-dd">
      <strong>Character Type:</strong> CHAR(1-65,000), VARCHAR(1-65,000), LONG VARCHAR(1-32,000,000)<br>
      <strong>Date/Time Type:</strong> DATE, DATETIME=TIMESTAMP, INTERVAL<br>
      <strong>Approximate Numeric:</strong> Signed 64-bit IEEE, DOUBLE PRECISION, FLOAT/FLOAT8/REAL<br>
      <strong>Exact Numeric:</strong> INT/INTEGER/BIGINT/INT8/SMALLINT/TINYINT, DECIMAL/NUMERIC/NUMBER
    </dd>
  </dl>

  <div class="syntax-box">
    <strong>테이블 생성 예시 (분산 및 파티션 설정 포함):</strong>
    <pre><code>CREATE TABLE sales (
    sale_id INT NOT NULL,
    product_id INT,
    sale_date DATE,
    amount DECIMAL(18,2)
)
--정렬키 설정
ORDER BY sale_id, product_id, sale_date, amount
-- 분산키 설정
SEGMENTED BY HASH(sale_id) ALL NODES 
-- 계층화된 파티션 설정
PARTITION BY sale_date::DATE 
GROUP BY CALENDAR_HIERARCHY_DAY(sale_date::DATE, 2, 2);</code></pre>
  </div>
</div>

<div class="feature-box">
  <h3 class="eon-section-title" style="margin-top: 0; margin-bottom: 1rem;">Projection (물리적 저장)</h3>
  <p style="color: var(--sub); margin-bottom: 1.5rem;">실제 테이블 데이터가 분산 및 압축되어 디스크에 저장되는 오브젝트입니다. 테이블에 데이터가 처음 저장될 때 자동으로 생성되거나, 명시적으로 생성할 수 있습니다.</p>
  
  <dl class="feature-dl">
    <dt class="feature-dt"><span class="feature-dt__icon">1</span> 컬럼 인코딩 및 압축 (Encoding)</dt>
    <dd class="feature-dd">RLE, DELTAVAL 등 데이터 성격에 맞는 압축 방식을 지정하여 I/O를 최소화합니다. 날짜나 반복되는 값이 많은 컬럼은 RLE 방식이 성능에 유리합니다.</dd>
    <dt class="feature-dt"><span class="feature-dt__icon">2</span> 컬럼 정렬 저장 순서 (Order by)</dt>
    <dd class="feature-dd">물리적 정렬을 통해 조회 및 조인 속도를 비약적으로 향상시킵니다. Cardinality가 낮은 컬럼을 우선 배치하는 것이 효율적입니다.</dd>
    <dt class="feature-dt"><span class="feature-dt__icon">3</span> 데이터 분산 정책 (Segmentation)</dt>
    <dd class="feature-dd">해시 분산(Segmented) 또는 전체 복제(Unsegmented)를 결정합니다.</dd>
  </dl>

  <div class="syntax-box">
    <strong>프로젝션 명시적 생성 예시:</strong>
    <pre><code>CREATE PROJECTION sales_optimized_proj (
    sale_id ENCODING COMMONDELTA_COMP,
    product_id,
    sale_date ENCODING RLE, -- 조건 조회 빈도가 높은 컬럼 RLE 권장 
    amount
)
AS SELECT sale_id, product_id, sale_date, amount FROM sales
ORDER BY sale_date, product_id -- 정렬키 정의
SEGMENTED BY HASH(sale_id) ALL NODES; -- 분산키 정의</code></pre>
  </div>
</div>

<h3 class="integration-subsection__title" style="margin: 3rem 0 1.5rem;">Projection의 4가지 종류</h3>
<div class="feature-box" style="margin-top: 0;">
  <dl class="feature-dl">
    <dt class="feature-dt"><span class="feature-dt__icon">1</span> Super 프로젝션</dt>
    <dd class="feature-dd">테이블의 모든 컬럼을 포함하는 기본 프로젝션입니다. 새로운 속성의 Super 프로젝션 생성 및 REFRESH 후 기존 프로젝션 삭제가 가능합니다.</dd>
    
    <dt class="feature-dt"><span class="feature-dt__icon">2</span> Aggregate 프로젝션</dt>
    <dd class="feature-dd">집계 함수(SUM, COUNT 등)가 포함되어 있어, 실시간 대시보드나 요약 정보 조회 시 미리 계산된 결과를 반환하여 응답 속도를 극대화합니다.</dd>
    
    <dt class="feature-dt"><span class="feature-dt__icon">3</span> Query-specific 프로젝션</dt>
    <dd class="feature-dd">특정 고부하 쿼리 튜닝을 위해 명시적으로 추가된 맞춤형 프로젝션입니다. 분석에 필요한 일부 컬럼만으로도 생성할 수 있습니다.</dd>
    
    <dt class="feature-dt"><span class="feature-dt__icon">4</span> Buddy 프로젝션</dt>
    <dd class="feature-dd">HA(고가용성) 구성을 위해 인접 노드에 복제되는 이중화 프로젝션입니다. 특정 노드 장애 시 Buddy가 서비스를 대신 수행하여 고가용성을 보장합니다.</dd>
  </dl>
</div>

<div class="architecture-section" markdown="1">
  <h3 class="integration-subsection__title" style="margin-bottom: 2rem;">Projection 데이터 분산</h3>
  
  <div class="projection-distribution-grid">
    <div class="architecture-subsection">
      <h4 class="section-subtitle">1. Segmentation (해시 분산)</h4>
      <ul class="feature-list">
        <li><span class="feature-list__icon">🔹</span> <span>Segment key 해시값 기반 분산 저장</span></li>
        <li><span class="feature-list__icon">🔹</span> <span>동일 Segment key 간 조인(Join) 성능 우수</span></li>
      </ul>
      <div class="image-box-styled">
        <img src="{{ '/assets/images/proj_segmentation.png' | relative_url }}" alt="Segmentation (해시 분산) 다이어그램">
      </div>
    </div>

  <div class="architecture-subsection">
      <h4 class="section-subtitle">2. Replication (복제)</h4>
      <ul class="feature-list">
        <li><span class="feature-list__icon">🔹</span> <span>소규모 코드성 테이블을 모든 노드에 복제하여 조인 성능 향상</span></li>
        <li><span class="feature-list__icon">🔹</span> <span>노드 장애 시에도 즉각적인 고가용성 제공</span></li>
      </ul>
      <div class="image-box-styled">
        <img src="{{ '/assets/images/proj_replication.png' | relative_url }}" alt="Replication (복제) 다이어그램">
      </div>
    </div>

  <div class="architecture-subsection">
      <h4 class="section-subtitle">3. 고가용성을 위한 데이터 이중화 (Buddy)</h4>
      <ul class="feature-list">
        <li><span class="feature-list__icon">🔹</span> <span>노드 장애에 대비해 다른 노드에 복제본 자동 생성 및 관리</span></li>
        <li><span class="feature-list__icon">🔹</span> <span>장애 복구 후 자동으로 데이터 동기화(Recovery) 수행</span></li>
      </ul>
      <div class="image-box-styled">
        <img src="{{ '/assets/images/proj_buddy.png' | relative_url }}" alt="Buddy 프로젝션을 이용한 이중화 다이어그램">
      </div>
    </div>

  <div class="architecture-subsection">
      <h4 class="section-subtitle">4. 특정 노드에 프로젝션 생성</h4>
      <ul class="feature-list">
        <li><span class="feature-list__icon">🔹</span> <span>네트워크 부하 최소화를 위해 입출력이 필요한 특정 노드에만 저장</span></li>
      </ul>
      <div class="image-box-styled">
        <img src="{{ '/assets/images/proj_specific_node.png' | relative_url }}" alt="특정 노드에 프로젝션 생성 다이어그램">
      </div>
    </div>
  </div>
</div>

  <hr style="margin: 3rem 0;">
  <div id="schema" style="scroll-margin-top: 100px;"></div>

## Schema

Vertica에서 **Schema(스키마)**는 객체들의 논리적인 그룹이며 별도의 물리적인 특징을 가지지 않습니다. 스키마를 통해 객체 접근 권한을 분리하고 네임스페이스를 관리함으로써 운영 효율성을 높일 수 있습니다.

<div class="feature-box" style="margin-top: 2rem;">
  <h3 class="eon-section-title" style="margin-top: 0; margin-bottom: 1rem;">1. 기본 스키마 (Default Schemas)</h3>
  <p style="color: var(--sub); margin-bottom: 1.5rem;">데이터베이스 생성 시 시스템 관리를 위해 자동으로 생성되는 기본 스키마들입니다.</p>
  
  <ul class="feature-list">
    <li><span class="feature-list__icon">🔹</span> <strong>v_internal:</strong> <span>데이터베이스 내부 테이블용 스키마 </span></li>
    <li><span class="feature-list__icon">🔹</span> <strong>v_catalog:</strong> <span>오브젝트 정보 카탈로그 스키마 </span></li>
    <li><span class="feature-list__icon">🔹</span> <strong>v_monitor:</strong> <span>모니터링용 테이블 스키마 </span></li>
    <li><span class="feature-list__icon">🔹</span> <strong>public:</strong> <span>일반 사용자를 위한 기본 스키마 </span></li>
  </ul>
</div>

<div class="feature-box">
  <h3 class="eon-section-title" style="margin-top: 0; margin-bottom: 1rem;">2. Schema 생성 (CREATE SCHEMA)</h3>
  <p style="color: var(--sub); margin-bottom: 1rem;">스키마는 <code>dbadmin</code> 계정으로 생성 가능하며, 권한 상속 설정을 통해 하위 객체 관리를 자동화할 수 있습니다.</p>
  
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

<div class="feature-box">
  <h3 class="eon-section-title" style="margin-top: 0; margin-bottom: 1rem;">3. Schema 수정 및 관리 (ALTER SCHEMA)</h3>
  <p style="color: var(--sub); margin-bottom: 1rem;">스키마의 이름, 소유자, 또는 권한 상속 정책을 변경할 때 사용합니다.</p>
  
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

<div class="feature-box">
  <h3 class="eon-section-title" style="margin-top: 0; margin-bottom: 1rem;">4. Schema 삭제 및 조회</h3>
  
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

<hr style="margin: 3rem 0;">
<div id="user" style="scroll-margin-top: 100px;"></div>

## User

<p>Vertica에서 <strong>User(사용자)</strong>는 데이터베이스에 접근하는 주체입니다. 관리자 계정(Administrator)은 데이터베이스 설치 시 생성되는 <code>vertica</code>(OS 계정과 동일)이며, 일반 사용자는 명시적으로 생성하여 권한 및 리소스를 할당해야 합니다.</p>

<div class="feature-box" style="margin-top: 2rem;">
  <h3 class="eon-section-title" style="margin-top: 0; margin-bottom: 1rem;">1. 사용자 생성 및 관리 (CREATE / ALTER USER)</h3>
  <p style="color: var(--sub); margin-bottom: 1rem;">데이터베이스 사용자를 생성하고, 패스워드, 리소스 풀, 프로파일 등의 속성을 지정합니다. 시스템 보안 정책상 암호를 10회 이상 틀리면 계정이 잠기며(Lock), 이 경우 관리자가 <code>ACCOUNT UNLOCK</code> 작업을 수행해야 합니다.</p>
  
  <div class="syntax-box">
    <strong>기본 구문:</strong>
    <pre><code>CREATE USER user_name [ account_parameter value[,...] ];
ALTER USER user_name { account_parameter setting | ACCOUNT UNLOCK };</code></pre>
  </div>

  <dl class="feature-dl">
    <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 파라미터</dt>
    <dd class="feature-dd">
      <strong>IDENTIFIED BY:</strong> 사용자의 비밀번호를 설정합니다.<br>
      <strong>PROFILE:</strong> 사용자에게 적용할 보안/패스워드 프로파일을 지정합니다.<br>
      <strong>RESOURCE POOL:</strong> 쿼리 실행 시 사용할 전용 리소스 풀을 지정합니다.
    </dd>
  </dl>

  <p class="example-label">예시</p>
  <pre><code>-- 사용자 생성 (비밀번호 및 리소스 풀 지정)
CREATE USER analyst IDENTIFIED BY 'StrongPassw0rd' RESOURCE POOL analyst_pool;

-- 암호 오류로 잠긴 계정 해제 (관리자 권한)
ALTER USER analyst ACCOUNT UNLOCK;</code></pre>
</div>

<div class="feature-box">
  <h3 class="eon-section-title" style="margin-top: 0; margin-bottom: 1rem;">2. 사용자 삭제 및 정보 조회</h3>
  
  <div class="syntax-box">
    <strong>삭제 구문 (DROP USER):</strong>
    <pre><code>DROP USER [ IF EXISTS ] user_name[,...] [ CASCADE ];</code></pre>
  </div>

  <ul class="feature-list" style="margin-top: 1rem;">
    <li><span class="feature-list__icon">🔹</span> <strong>CASCADE 옵션:</strong> <span>해당 사용자가 생성한 모든 객체(테이블 등)를 함께 삭제합니다.</span></li>
    <li><span class="feature-list__icon">🔹</span> <strong>사용자 조회:</strong> <span><code>SELECT * FROM USERS;</code> 쿼리를 통해 전체 데이터베이스 사용자 정보를 확인할 수 있습니다.</span></li>
  </ul>
</div>

<hr style="margin: 3rem 0;">
<div id="profile" style="scroll-margin-top: 100px;"></div>

## Profile

<p>Vertica <strong>Profile(프로파일)</strong>은 사용자의 <strong>보안 및 패스워드 정책</strong>을 정의하고 관리하는 객체입니다. 사용자별로 보안 등급에 따라 프로파일을 생성하여 할당할 수 있으며, 기본적으로 제공되는 default 프로파일이 존재합니다.</p>

<div class="feature-box" style="margin-top: 2rem;">
  <h3 class="eon-section-title" style="margin-top: 0; margin-bottom: 1rem;">Profile 생성 및 할당</h3>
  <p style="color: var(--sub); margin-bottom: 1rem;"><code>CREATE PROFILE</code>로 패스워드 관련 제약 조건을 설정하고, 사용자 생성 또는 수정 시 이를 할당합니다.</p>
  
  <dl class="feature-dl">
    <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 제약조건 (Password Parameters)</dt>
    <dd class="feature-dd">
      <strong>PASSWORD_LIFE_TIME:</strong> 패스워드의 유효 기간을 설정합니다.<br>
      <strong>FAILED_LOGIN_ATTEMPTS:</strong> 허용되는 최대 로그인 실패 횟수입니다 (초과 시 Lock).<br>
      <strong>PASSWORD_LOCK_TIME:</strong> 로그인이 잠긴 후 유지되는 시간입니다.<br>
      <strong>PASSWORD_MIN_LENGTH:</strong> 패스워드의 최소 길이를 지정합니다.
    </dd>
  </dl>

  <p class="example-label">프로파일 생성 및 적용 예시</p>
  <pre><code>-- 보안이 강화된 프로파일 생성 (길이 10자 이상, 5회 실패 시 잠금)
CREATE PROFILE strict_profile LIMIT 
    PASSWORD_MIN_LENGTH 10 
    FAILED_LOGIN_ATTEMPTS 5;

-- 기존 사용자에게 새 프로파일 적용
ALTER USER analyst PROFILE strict_profile;</code></pre>
</div>

<hr style="margin: 3rem 0;">
<div id="resource-pool" style="scroll-margin-top: 100px;"></div>

## Resource Pool

<p><strong>Resource Pool(리소스 풀)</strong>은 버티카 데이터베이스에서 작업(Workload)을 관리하기 위해 할당하는 메모리 및 스레드 공간입니다. 기본적으로 <code>general</code>, <code>sysquery</code>, <code>tm</code>(Tuple Mover) 등의 Built-in 리소스 풀이 제공되며, 업무와 사용자 성격에 맞춰 커스텀 풀을 추가 생성하는 것이 핵심 운영 전략입니다.</p>

<div class="feature-box" style="margin-top: 2rem;">
  <h3 class="eon-section-title" style="margin-top: 0; margin-bottom: 1rem;">1. Resource Pool 구성 및 관리</h3>
  <p style="color: var(--sub); margin-bottom: 1rem;">효율적인 자원 배분을 위해 풀을 생성(<code>CREATE</code>), 수정(<code>ALTER</code>), 삭제(<code>DROP</code>)할 수 있습니다.</p>
  
  <div class="syntax-box">
    <strong>기본 생성 구문:</strong>
    <pre><code>CREATE RESOURCE POOL pool_name [ parameter_name setting ]...;</code></pre>
  </div>

  <dl class="feature-dl">
    <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 파라미터</dt>
    <dd class="feature-dd">
      <strong>MEMORYSIZE / MAXMEMORYSIZE:</strong> 풀의 초기 메모리 크기와 사용 가능한 최대 메모리 크기<br>
      <strong>MAXQUERYMEMORYSIZE:</strong> 단일 쿼리가 사용할 수 있는 최대 메모리 (초과 시 쿼리 취소)<br>
      <strong>MAXCONCURRENCY:</strong> 풀에서 동시에 실행될 수 있는 최대 세션/애플리케이션 개수<br>
      <strong>PLANNEDCONCURRENCY:</strong> 풀 내 메모리를 분할할 동시성 기준값 (1/n)<br>
      <strong>QUEUETIMEOUT / RUNTIMECAP:</strong> 대기 큐 타임아웃 및 쿼리 최대 실행 시간 제한
    </dd>
  </dl>
  
  <ul class="feature-list" style="margin-top: 1rem;">
    <li><span class="feature-list__icon">💡</span> <strong>운영 Best Practice:</strong> <span>배치 작업 시간대와 일반 사용자 접속 시간대의 리소스 풀 구성을 스크립트로 분리·조정하여 시스템 사용을 극대화합니다.</span></li>
  </ul>
</div>

<div class="feature-box">
  <h3 class="eon-section-title" style="margin-top: 0; margin-bottom: 1rem;">2. Resource Pool 권한 부여 및 할당</h3>
  <p style="color: var(--sub); margin-bottom: 1rem;">생성된 리소스 풀은 다수의 사용자가 공유할 수 있으나, 한 사용자는 동시에 하나의 풀만 사용할 수 있습니다.</p>

  <p class="example-label">적용 예시</p>
  <pre><code>-- 1. 리소스 풀 생성
CREATE RESOURCE POOL batch_pool 
    MAXMEMORYSIZE '10G' MAXCONCURRENCY 4;

-- 2. 사용자에게 리소스 풀 사용 권한 부여 (GRANT USAGE)
GRANT USAGE ON RESOURCE POOL batch_pool TO batch_user;

-- 3. 사용자에게 기본 리소스 풀 할당 (ALTER USER)
ALTER USER batch_user RESOURCE POOL batch_pool;</code></pre>
</div>

<hr style="margin: 3rem 0;">
<div id="privilege" style="scroll-margin-top: 100px;"></div>

## Privilege

<p><strong>Privilege(권한)</strong>는 사용자가 SCHEMA, TABLE, RESOURCE POOL 등의 객체에 접근하고 조작할 수 있는 권리를 의미합니다. Vertica에서는 <strong>스키마 권한 상속(Schema Inheritance)</strong>을 적극 활용하여 권한 관리의 편의성을 극대화합니다.</p>

<div class="feature-box" style="margin-top: 2rem;">
  <h3 class="eon-section-title" style="margin-top: 0; margin-bottom: 1rem;">객체 권한 관리 (GRANT)</h3>
  
  <dl class="feature-dl">
    <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> SCHEMA 권한</dt>
    <dd class="feature-dd">
      기본적으로 <code>USAGE</code>(조회) 및 <code>CREATE</code>(생성) 권한을 부여합니다.<br>
      <strong>구문:</strong> <code>GRANT { privilege | ALL } ON SCHEMA schema TO grantee;</code>
    </dd>

    <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> TABLE 권한</dt>
    <dd class="feature-dd">
      <code>SELECT</code>, <code>INSERT</code>, <code>UPDATE</code>, <code>DELETE</code>, <code>ALTER</code>, <code>DROP</code>, <code>TRUNCATE</code> 등을 제어합니다.<br>
      특정 스키마의 전체 테이블에 일괄 부여할 때는 <code>ALL TABLES IN SCHEMA</code> 구문을 사용합니다.<br>
      <strong>구문:</strong> <code>GRANT SELECT, UPDATE ON ALL TABLES IN SCHEMA analytics TO analyst;</code>
    </dd>
  </dl>
  <div class="syntax-box">
    <strong>스키마 권한 상속 예시:</strong>
    <pre><code>-- 1. 스키마 생성 시 상속 활성화
CREATE SCHEMA analytics DEFAULT INCLUDE PRIVILEGES;
-- 2. 사용자에게 상속될 테이블 접근 권한 지정 (EXTEND 활용)
GRANT ALL PRIVILEGES EXTEND ON SCHEMA analytics TO analyst;</code></pre>
  </div>
</div>

<hr style="margin: 3rem 0;">
<div id="backup-restore" style="scroll-margin-top: 100px;"></div>

## Backup & Restore

<p>Vertica 데이터베이스의 백업은 OS의 <code>rsync</code>를 활용하는 <strong>스냅샷(Snapshot) 방식의 로컬 백업</strong>을 기본으로 수행합니다. Vertica에서 기본 제공하는 <code>vbr</code> 스크립트를 이용하여 백업의 구성 및 복구를 직관적으로 처리할 수 있습니다.</p>

<div class="feature-box" style="margin-top: 2rem;">
  <h3 class="eon-section-title" style="margin-top: 0; margin-bottom: 1rem;">백업 아키텍처 및 구성</h3>
  <p style="color: var(--sub); margin-bottom: 1rem;">각 노드의 디렉토리(예: <code>/data/BACKUP</code>)에 데이터베이스 백업본이 저장됩니다. 첫 번째 Full 백업 이후에 동일한 위치로 백업을 수행하면 자동으로 변경분만 추적하는 <strong>Incremental(증분) 백업</strong>이 수행되어 공간과 시간을 절약합니다. (기존 백업 디렉토리를 삭제하면 다시 Full 백업이 진행됩니다.)</p>
  
  <ul class="feature-list">
    <li><span class="feature-list__icon">🔹</span> <strong>설정 파일 (.ini):</strong> <span>백업 대상 노드, 저장 위치, 패스워드 등을 정의합니다.</span></li>
    <li><span class="feature-list__icon">🔹</span> <strong>실행 스크립트:</strong> <span>통상적으로 Crontab에 등록하여 심야(예: 05:00)에 자동 수행되도록 구성합니다.</span></li>
  </ul>

  <p class="example-label">vbr 백업 구성 및 실행 예시</p>
  <pre><code># 1. 백업 초기화 (vbr 유틸리티 활용)
/opt/vertica/bin/vbr --task init --config-file /home/vertica/DBA/BACKUP/backup.ini

# 2. 데이터베이스 백업 실행 (증분 백업 자동 적용)
/opt/vertica/bin/vbr --task backup --config-file /home/vertica/DBA/BACKUP/backup.ini

# 3. 데이터베이스 복구 실행 (장애 발생 시)
# 복구 작업 전 대상 데이터베이스(혹은 노드)는 반드시 Down 상태여야 합니다.
/opt/vertica/bin/vbr --task restore --config-file /home/vertica/DBA/BACKUP/backup.ini</code></pre>
</div>

  </div>
  <aside class="page-sidebar">
    <div class="sidebar-panel">
      <h3>On this page</h3>
      <ul>
        <li><a href="#table-projection">Table · Projection</a></li>
        <li><a href="#schema">Schema</a></li>
        <li><a href="#user">User</a></li>
        <li><a href="#profile">Profile</a></li>
        <li><a href="#resource-pool">Resource Pool</a></li>
        <li><a href="#privilege">Privilege</a></li>
        <li><a href="#backup-restore">Backup & Restore</a></li>
      </ul>
    </div>
  </aside>
</div>