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

  Vertica에서 가장 중요한 객체는 `Table`과 `Projection`입니다. Table은 데이터 구조를 정의하고 저장을 담당하며, Projection은 데이터를 빠르게 조회하도록 최적화하는 물리 계층입니다.

  ### Table 생성 및 관리

  - `CREATE TABLE`으로 테이블을 생성합니다.
  - 컬럼 타입과 `ENCODING`을 적절히 설계해야 합니다.
  - `COPY`명령으로 데이터를 로드하며, `DELETE`, `UPDATE`, `MERGE` 등을 통해 데이터를 관리합니다.
  - `EXPORT`를 사용해 데이터를 외부 파일로 내보낼 수 있습니다.

  예시:

  ```sql
  CREATE TABLE sales (
    sale_id INT,
    product_id INT,
    sale_date DATE,
    amount DECIMAL(18,2)
  ) SEGMENTED BY HASH(sale_id) ALL NODES;
  ```

  ### Projection의 역할

  - Projection은 `Query Optimizer`가 사용할 물리적 뷰입니다.
  - 기본적으로 Vertica는 자동 Projection을 생성하지만, 중요한 쿼리에는 `CREATE PROJECTION`으로 최적화된 projection을 구성할 수 있습니다.
  - Projection은 `자주 사용하는 컬럼`, `정렬 키`, `조인 컬럼` 중심으로 설계합니다.

  ### Projection 최적화 포인트

  - `ORDER BY`는 쿼리 수행 속도에 큰 영향을 줍니다.
  - `PARTITION BY`는 큰 테이블 처리 성능을 개선합니다.
  - 중복 제거 및 집계 쿼리를 위한 `segment by`, `local order by`를 적절히 사용합니다.

  예시:

  ```sql
  CREATE PROJECTION sales_projection (
    sale_id,
    product_id,
    sale_date,
    amount
  )
  AS
  SELECT sale_id, product_id, sale_date, amount
  FROM sales
  ORDER BY sale_date, product_id;
  ```

  ### 운영 팁

  - `SELECT * FROM projections WHERE anchor_table_name = 'sales';`로 테이블에 연결된 projection을 조회합니다.
  - `SELECT REFRESH_PROJECTIONS()`로 projection 상태를 재생성/재정비할 수 있습니다.
  - `ANALYZE_STATISTICS`로 통계 정보를 갱신하면 최적화에 도움이 됩니다.

  <hr style="margin: 3rem 0;">
  <div id="schema" style="scroll-margin-top: 100px;"></div>

  ## Schema

  Vertica에서 `Schema`는 객체를 그룹화하고 접근 권한을 분리하는 네임스페이스 역할을 합니다. Schema를 잘 설계하면 운영과 보안 관리가 쉬워집니다.

  ### Schema 생성

  - `CREATE SCHEMA`로 스키마를 생성합니다.
  - 스키마는 테이블, 뷰, 프로젝션, 시퀀스 등의 객체를 논리적으로 구분합니다.
  - 네임스페이스를 분리하면 동일 이름의 객체를 서로 다른 스키마에 생성할 수 있습니다.

  예시:

  ```sql
  CREATE SCHEMA analytics;
  CREATE SCHEMA staging;
  ```

  ### Schema 관리

  - 스키마 단위로 `GRANT`와 `REVOKE`를 적용할 수 있습니다.
  - `SET SEARCH_PATH`로 기본 스키마를 지정해 쿼리 작성 편의성을 높입니다.
  - 스키마 내 객체를 삭제할 때는 `DROP SCHEMA ... CASCADE`로 관련 객체를 함께 제거할 수 있습니다.

  예시:

  ```sql
  GRANT USAGE ON SCHEMA analytics TO analyst_role;
  REVOKE CREATE ON SCHEMA analytics FROM public;
  ```

  ### 운영 포인트

  - 개발/테스트/프로덕션 환경별 스키마를 분리합니다.
  - 민감 데이터가 있는 테이블은 별도의 스키마로 분리하여 권한을 강화합니다.
  - 스키마 이름과 역할 이름을 명확하게 정해 운영 혼선을 줄입니다.

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

  예시:

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

  예시:

  ```sql
  CREATE PROFILE analyst_profile
    SET COST_LIMIT = 10000,
        CONCURRENCY_LIMIT = 10;
  ```

  ### Profile 할당

  - `ALTER USER ... SET PROFILE`로 사용자에게 프로필을 할당합니다.
  - `GRANT`와 함께 역할 기반 프로필 할당도 가능합니다.

  예시:

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

  예시:

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

  예시:

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