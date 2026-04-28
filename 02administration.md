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

  <div class="content-section" style="padding: 1rem; margin-top: 1rem; background-color: rgba(45, 105, 255, 0.04);">
    <strong>테이블 생성 예시 (분산 및 파티션 설정 포함):</strong>
    <pre><code>CREATE TABLE sales (
    sale_id INT NOT NULL,
    product_id INT,
    sale_date DATE,
    amount DECIMAL(18,2)
) 
-- 세그먼트 키 및 K-Safe 설정
SEGMENTED BY HASH(sale_id) ALL NODES KSAFE 1
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

  <div class="content-section" style="padding: 1rem; margin-top: 1rem; background-color: rgba(45, 105, 255, 0.04);">
    <strong>프로젝션 명시적 생성 예시:</strong>
    <pre><code>CREATE PROJECTION sales_optimized_proj (
    sale_id ENCODING COMMONDELTA_COMP,
    product_id,
    sale_date ENCODING RLE, -- 조건 조회 빈도가 높은 컬럼 RLE 권장 
    amount
)
AS SELECT sale_id, product_id, sale_date, amount FROM sales
ORDER BY sale_date, product_id -- 물리적 정렬 순서 정의
SEGMENTED BY HASH(sale_id) ALL NODES; -- 분산 정책 정의</code></pre>
  </div>
</div>

<h3 class="integration-subsection__title" style="margin: 3rem 0 1.5rem;">Projection의 4가지 종류</h3>
<div class="use-case-grid">
  <div class="card card--use-case">
    <h4 class="use-case-card__title">1. Super 프로젝션</h4>
    <ul class="use-case-card__list">
      <li>테이블의 모든 컬럼을 포함하는 기본 프로젝션</li>
      <li>새로운 속성의 Super 프로젝션 생성 및 REFRESH 후 기존 프로젝션 삭제 가능</li>
    </ul>
  </div>
  <div class="card card--use-case">
    <h4 class="use-case-card__title">2. Aggregate 프로젝션</h4>
    <ul class="use-case-card__intro">
      집계 함수가 포함되어 실시간 대시보드 조회 속도를 극대화합니다.
    </ul>
    <pre><code>-- LAP 예시
CREATE PROJECTION sales_sum AS 
SELECT product_id, SUM(amount) 
FROM sales GROUP BY product_id;</code></pre>
  </div>
  <div class="card card--use-case">
    <h4 class="use-case-card__title">3. Query-specific 프로젝션</h4>
    <ul class="use-case-card__list">
      <li>특정 고부하 쿼리 튜닝을 위해 명시적으로 추가된 맞춤형 프로젝션 </li>
      <li>전체 컬럼이 아닌 분석에 필요한 일부 컬럼만으로도 생성 가능</li>
    </ul>
  </div>
  <div class="card card--use-case">
    <h4 class="use-case-card__title">4. Buddy 프로젝션</h4>
    <ul class="use-case-card__list">
      <li>HA 구성을 위해 인접 노드에 복제되는 이중화 프로젝션 </li>
      <li>특정 노드 장애 시 Buddy가 서비스를 대신 수행하여 고가용성 보장 </li>
    </ul>
  </div>
</div>

<div class="architecture-section" markdown="1">
  <h3 class="integration-subsection__title" style="margin-bottom: 2rem;">Projection 데이터 분산</h3>
  
  <div class="projection-distribution-grid">
    <div class="architecture-subsection">
      <h4 class="section-subtitle">1. Segmentation (해시 분산)</h4>
      <ul class="feature-list">
        <li><span class="feature-list__icon">🔹</span> <span>Segment key 해시값 기반 분산 저장 </span></li>
        <li><span class="feature-list__icon">🔹</span> <span>동일 Segment key 간 조인(Join) 성능 우수 </span></li>
      </ul>
      <pre style="margin-bottom: 1rem;"><code>-- SQL Syntax
SEGMENTED BY HASH(c1, c2) ALL NODES;</code></pre>
      <div class="image-box-styled">
        <img src="{{ '/assets/images/proj_segmentation.png' | relative_url }}" alt="Segmentation 데이터 분산">
      </div>
    </div>

    <div class="architecture-subsection">
      <h4 class="section-subtitle">2. Replication (복제)</h4>
      <ul class="feature-list">
        <li><span class="feature-list__icon">🔹</span> <span>소규모 코드성 테이블을 모든 노드에 복제하여 조인 성능 향상 [cite: 111]</span></li>
        <li><span class="feature-list__icon">🔹</span> <span>노드 장애 시에도 즉각적인 고가용성 제공</span></li>
      </ul>
      <pre style="margin-bottom: 1rem;"><code>-- SQL Syntax
UNSEGMENTED ALL NODES;</code></pre>
      <div class="image-box-styled">
        <img src="{{ '/assets/images/proj_replication.png' | relative_url }}" alt="Replication 데이터 복제">
      </div>
    </div>

    <div class="architecture-subsection">
      <h4 class="section-subtitle">3. 고가용성을 위한 데이터 이중화 (Buddy)</h4>
      <ul class="feature-list">
        <li><span class="feature-list__icon">🔹</span> <span>노드 장애에 대비해 다른 노드에 복제본 자동 생성 및 관리 </span></li>
        <li><span class="feature-list__icon">🔹</span> <span>장애 복구 후 자동으로 데이터 동기화(Recovery) 수행 </span></li>
      </ul>
      <div class="image-box-styled">
        <img src="{{ '/assets/images/proj_buddy.png' | relative_url }}" alt="Buddy 프로젝션 이중화">
      </div>
    </div>

    <div class="architecture-subsection">
      <h4 class="section-subtitle">4. 특정 노드에 프로젝션 생성</h4>
      <ul class="feature-list">
        <li><span class="feature-list__icon">🔹</span> <span>네트워크 부하 최소화를 위해 입출력이 필요한 특정 노드에만 저장</span></li>
      </ul>
      <pre style="margin-bottom: 1rem;"><code>-- 특정 노드 지정 Syntax
UNSEGMENTED NODE v_db_node0003;</code></pre>
      <div class="image-box-styled">
        <img src="{{ '/assets/images/proj_specific_node.png' | relative_url }}" alt="특정 노드 프로젝션 생성">
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
  <p style="color: var(--sub); margin-bottom: 1.5rem;">데이터베이스 생성 시 시스템 관리를 위해 자동으로 생성되는 기본 스키마들입니다. </p>
  
  <ul class="feature-list">
    <li><span class="feature-list__icon">🔹</span> <strong>v_internal:</strong> <span>데이터베이스 내부 테이블용 스키마 </span></li>
    <li><span class="feature-list__icon">🔹</span> <strong>v_catalog:</strong> <span>오브젝트 정보 카탈로그 스키마 </span></li>
    <li><span class="feature-list__icon">🔹</span> <strong>v_monitor:</strong> <span>모니터링용 테이블 스키마 </span></li>
    <li><span class="feature-list__icon">🔹</span> <strong>public:</strong> <span>일반 사용자를 위한 기본 스키마 </span></li>
  </ul>
</div>

<div class="feature-box">
  <h3 class="eon-section-title" style="margin-top: 0; margin-bottom: 1rem;">2. Schema 생성 (CREATE SCHEMA)</h3>
  <p style="color: var(--sub); margin-bottom: 1rem;">스키마는 <code>dbadmin</code> 계정으로 생성 가능하며, 권한 상속 설정을 통해 하위 객체 관리를 자동화할 수 있습니다. </p>
  
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
  <p style="color: var(--sub); margin-bottom: 1rem;">스키마의 이름, 소유자, 또는 권한 상속 정책을 변경할 때 사용합니다. </p>
  
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

  Vertica에서 `User`는 데이터베이스에 접근하는 주체입니다. 사용자를 적절하게 구성하고 권한을 관리하는 것이 안정적인 운영의 시작입니다.

  ### 사용자 생성

  - `CREATE USER`로 사용자를 생성합니다.
  - 사용자 패스워드, 인증 모드를 지정할 수 있습니다.
  - 비밀번호 정책과 계정 잠금 정책을 운영 정책에 맞춰 구성합니다.

  예시:

  ```sql
  CREATE USER analyst IDENTIFIED BY 'StrongPassw0rd';
  ```

  ### 사용자 권한 관리

  - 사용자에게 직접 권한을 부여하거나 역할(Role)을 통해 간접적으로 권한을 관리합니다.
  - `GRANT`로 객체 권한을 부여하고, `REVOKE`로 제거합니다.
  - 사용자 그룹 대신 역할을 사용하면 권한 변경 시 관리가 용이합니다.

  <p class="example-label">예시</p>

  ```sql
  GRANT SELECT ON analytics.sales TO analyst;
  GRANT analyst_role TO analyst;
  ```

  ### 사용자 관리 팁

  - `SELECT * FROM users;`로 사용자 목록을 확인합니다.
  - 사용하지 않는 사용자 계정은 즉시 비활성화하거나 삭제합니다.
  - 감사 추적을 위해 사용자 생성, 권한 변경 시 로그를 기록합니다.

  <hr style="margin: 3rem 0;">
  <div id="profile" style="scroll-margin-top: 100px;"></div>

  ## Profile

  Vertica `Profile`은 사용자 세션에 적용되는 실행 환경과 자원 제한을 정의합니다. 프로필을 통해 각 사용자의 쿼리 실행 조건을 제어할 수 있습니다.

  ### Profile 생성

  - `CREATE PROFILE`로 프로필을 생성합니다.
  - `COST_LIMIT`, `CONCURRENCY_LIMIT`, `RESOURCE_PORTION` 등의 속성을 설정할 수 있습니다.
  - 프로필은 사용자 또는 역할에 할당됩니다.

  <p class="example-label">예시</p>

  ```sql
  CREATE PROFILE analyst_profile
    SET COST_LIMIT = 10000,
        CONCURRENCY_LIMIT = 10;
  ```

  ### Profile 할당

  - `ALTER USER ... SET PROFILE`로 사용자에게 프로필을 할당합니다.
  - `GRANT`와 함께 역할 기반 프로필 할당도 가능합니다.

  <p class="example-label">예시</p>

  ```sql
  ALTER USER analyst SET PROFILE analyst_profile;
  ```

  ### 운영 포인트

  - 프로필로 리소스 사용을 제한하면 대형 쿼리로 인한 전체 성능 저하를 방지할 수 있습니다.
  - 사용자 유형별로 서로 다른 프로필을 구성하여 개발/분석/운영 환경을 분리합니다.
  - 프로필 설정 변경 시 테스트를 통해 안정성을 확인합니다.

  <hr style="margin: 3rem 0;">
  <div id="resource-pool" style="scroll-margin-top: 100px;"></div>

  ## Resource Pool

  Vertica 운영에서는 `Resource Pool`과 `Privilege`를 통해 리소스 사용량과 권한을 분리 관리합니다. 올바른 구성이 안정적인 분석 환경을 만드는 핵심입니다.

  ### Resource Pool 생성 및 관리

  - `CREATE RESOURCE POOL`로 리소스 풀을 생성합니다.
  - `MAX_MEMORY_SIZE`, `MAX_CONCURRENCY`, `MAX_RUNNING` 같은 속성으로 쿼리 자원 할당을 제어합니다.
  - 각 풀은 특정 유형의 작업 또는 사용자 그룹에 할당할 수 있습니다.

  <p class="example-label">예시</p>

  ```sql
  CREATE RESOURCE POOL analyst_pool
    MAX_MEMORY_SIZE '4GB'
    MAX_CONCURRENCY 10
    MAX_RUNNING 8;
  ```

  <hr style="margin: 3rem 0;">
  <div id="privilege" style="scroll-margin-top: 100px;"></div>

  ## Privilege

  ### Privilege 관리

  - Vertica 권한 체계는 객체별 `GRANT`/`REVOKE` 기반입니다.
  - `ALL PRIVILEGES`, `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `USAGE`, `CREATE` 등으로 세분화됩니다.
  - 권한을 직접 사용자에게 부여하기보다 역할(Role)을 활용하는 것이 관리에 유리합니다.

  <p class="example-label">예시</p>

  ```sql
  GRANT USAGE ON SCHEMA analytics TO analyst_role;
  GRANT SELECT ON analytics.sales TO analyst_role;
  ```

  ### 운영 포인트

  - 리소스 풀과 프로필을 함께 사용하면 쿼리 성능과 안정성을 모두 관리할 수 있습니다.
  - 최소 권한 원칙을 적용해 불필요한 권한 부여를 제한합니다.
  - 권한 변경 작업은 문서화하고, 주기적으로 권한 검토를 수행합니다.

  <hr style="margin: 3rem 0;">
  <div id="backup-restore" style="scroll-margin-top: 100px;"></div>

  ## Backup & Restore
  `vbr`은 Vertica 데이터베이스의 백업 및 복구를 위한 강력한 커맨드 라인 유틸리티입니다. `vbr`을 사용하면 전체 데이터베이스, 특정 스키마나 테이블 등 다양한 단위로 데이터를 안정적으로 백업하고 복구할 수 있습니다.

  ### 주요 기능
  - **백업 (`--task backup`)**: 전체, 객체, 증분 등 다양한 방식으로 백업을 수행합니다.
  - **복구 (`--task restore`)**: 스냅샷 기반으로 특정 시점의 데이터를 복구하여 일관성을 보장합니다.

  ### 복구 절차 예시
  1.  **설정 파일 준비**: 복구 작업을 위한 `.ini` 설정 파일을 준비합니다.
  2.  **복구 대상 DB 정지**: 데이터 일관성을 위해 복구를 진행할 데이터베이스를 정지합니다.
  3.  **vbr 복구 명령어 실행**: `vbr --task restore` 명령어를 사용하여 복구를 시작합니다.
  4.  **DB 재시작 및 확인**: 복구가 완료되면 데이터베이스를 재시작하고 데이터가 정상적으로 복구되었는지 확인합니다.

  <p class="example-label">vbr 복구 실행 예시</p>

  ```bash
  # vbr 복구 실행 예시
  /opt/vertica/bin/vbr --task restore --config-file my_backup.ini
  ```

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