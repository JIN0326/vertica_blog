---
title: Vertica CoreTech
layout: default
---

<div class="page-hero">
  <span class="chip">CoreTech</span>
  <h1>Vertica CoreTech</h1>
  <p>데이터 적재, 쿼리, 튜닝, 복구 등 Vertica의 핵심 기술과 운영 방법을 상세히 설명합니다.</p>
</div>

<div class="page-layout">
  <div class="content-section" markdown="1">

    <div id="copy" style="scroll-margin-top: 100px;"></div>
    ## 데이터 적재 (COPY)
    `COPY` 명령어는 Vertica에서 데이터를 벌크로 로딩하는 가장 효율적인 방법입니다. 클라이언트 시스템의 파일이나 S3와 같은 외부 소스에서 데이터를 병렬로 읽어와 데이터베이스 노드에 직접 분산 저장합니다.

    - **고속 로딩**: 데이터를 파싱하고 압축하여 노드에 직접 분산시키므로 매우 빠른 속도를 보장합니다.
    - **다양한 포맷 지원**: CSV, JSON, Parquet, ORC 등 다양한 파일 포맷을 지원합니다.
    - **에러 핸들링**: 데이터 로딩 중 발생하는 오류를 별도의 파일에 기록하고, 성공한 데이터만 커밋하는 등 강력한 에러 처리 기능을 제공합니다.

    #### 기본 사용법
    ```sql
    COPY public.sales
    FROM LOCAL '/path/to/sales_data.csv'
    DELIMITER ','
    ENCLOSED BY '"'
    SKIP 1
    REJECTED DATA '/path/to/sales_rejects.log'
    EXCEPTIONS '/path/to/sales_exceptions.log';
    ```

    <hr style="margin: 3rem 0;">

    <div id="vsql" style="scroll-margin-top: 100px;"></div>
    ## vsql 명령어
    `vsql`은 Vertica 데이터베이스와 상호작용하기 위한 터미널 기반의 프론트엔드 도구입니다. PostgreSQL의 `psql`을 기반으로 하여, SQL 쿼리 실행, 메타데이터 조회, 스크립트 실행 등 다양한 작업을 수행할 수 있습니다.

    #### 접속하기
    ```bash
    /opt/vertica/bin/vsql -h <hostname> -p <port> -d <database> -U <username>
    ```

    #### 유용한 메타 명령어 (Meta-Commands)
    - `\l`: 데이터베이스 목록을 보여줍니다.
    - `\dt [schema.]`: 스키마의 테이블 목록을 보여줍니다.
    - `\d [table_name]`: 특정 테이블의 상세 정보(컬럼, 타입 등)를 보여줍니다.
    - `\h [command]`: 특정 SQL 명령어의 도움말을 보여줍니다. (예: `\h CREATE TABLE`)
    - `\timing`: 쿼리 실행 시간을 표시하거나 끕니다.
    - `\q`: vsql을 종료합니다.

    <hr style="margin: 3rem 0;">

    <div id="tuning" style="scroll-margin-top: 100px;"></div>
    ## 쿼리 튜닝 (Explain)
    `EXPLAIN`은 Vertica의 쿼리 최적화기(Query Optimizer)가 특정 SQL 쿼리를 어떻게 실행할 것인지에 대한 계획(Execution Plan)을 보여주는 명령어입니다. 이 실행 계획을 분석하면 쿼리 성능에 영향을 미치는 요소를 파악하고 튜닝 방향을 결정할 수 있습니다.

    #### 실행 계획 분석의 주요 확인 항목
    1.  **Access Path (데이터 접근 경로)**: 쿼리에 최적화된 프로젝션을 사용하고 있는지 확인합니다.
    2.  **Join Operations (조인 연산)**: 조인 순서와 방식이 효율적인지 확인합니다.
    3.  **Data Movement (데이터 이동)**: 노드 간 불필요한 데이터 이동(Resegment, Broadcast)이 발생하는지 확인합니다.
    4.  **Cost (비용)**: 비용이 비정상적으로 높은 구간이 성능 병목 지점일 가능성이 높습니다.

    <hr style="margin: 3rem 0;">

    <div id="restore" style="scroll-margin-top: 100px;"></div>
    ## 데이터 복구 (Restore)
    `vbr`은 Vertica 데이터베이스의 백업 및 복구를 위한 강력한 커맨드 라인 유틸리티입니다. `vbr`을 사용하면 전체 데이터베이스, 특정 스키마나 테이블 등 다양한 단위로 데이터를 안정적으로 복구할 수 있습니다.

    #### 복구 절차
    1.  **설정 파일 준비**: 복구 작업을 위한 `.ini` 설정 파일을 준비합니다.
    2.  **복구 대상 DB 정지**: 데이터 일관성을 위해 복구를 진행할 데이터베이스를 정지합니다.
    3.  **vbr 복구 명령어 실행**: `vbr --task restore` 명령어를 사용하여 복구를 시작합니다.
    4.  **DB 재시작 및 확인**: 복구가 완료되면 데이터베이스를 재시작하고 데이터가 정상적으로 복구되었는지 확인합니다.

    #### 전체 데이터베이스 복구 예시
    ```bash
    # DB 정지
    admintools -t stop_db -d VDB -p <password>

    # vbr 복구 실행
    /opt/vertica/bin/vbr --task restore --config-file my_backup.ini

    # DB 시작
    admintools -t start_db -d VDB -p <password>
    ```
  </div>

  <aside class="page-sidebar">
    <div class="sidebar-panel">
      <h3>On this page</h3>
      <ul>
        <li><a href="#copy">데이터 적재 (COPY)</a></li>
        <li><a href="#vsql">vsql 명령어</a></li>
        <li><a href="#tuning">쿼리 튜닝 (Explain)</a></li>
        <li><a href="#restore">데이터 복구 (Restore)</a></li>
      </ul>
    </div>
  </aside>
</div>