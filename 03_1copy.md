---
title: 데이터 적재 (COPY)
layout: default
---

<div class="page-hero">
  <span class="chip">CoreTech</span>
  <h1>데이터 적재 (COPY)</h1>
  <p>Vertica에서 대용량 데이터를 빠르고 효율적으로 적재하는 COPY 명령어의 사용법과 주요 옵션을 알아봅니다.</p>
</div>

<div class="content-section content-section-centered" markdown="1">
  ## COPY 명령어 개요

  `COPY` 명령어는 Vertica에서 데이터를 벌크로 로딩하는 가장 효율적인 방법입니다. 클라이언트 시스템의 파일이나 S3와 같은 외부 소스에서 데이터를 병렬로 읽어와 데이터베이스 노드에 직접 분산 저장합니다.

  - **고속 로딩**: 데이터를 파싱하고 압축하여 노드에 직접 분산시키므로 매우 빠른 속도를 보장합니다.
  - **다양한 포맷 지원**: CSV, JSON, Parquet, ORC 등 다양한 파일 포맷을 지원합니다.
  - **에러 핸들링**: 데이터 로딩 중 발생하는 오류를 별도의 파일에 기록하고, 성공한 데이터만 커밋하는 등 강력한 에러 처리 기능을 제공합니다.

  ---

  ## 기본 사용법

  가장 일반적인 사용 사례는 로컬 파일 시스템의 CSV 파일을 테이블에 적재하는 것입니다.

  ```sql
  COPY public.sales
  FROM LOCAL '/path/to/sales_data.csv'
  DELIMITER ','
  ENCLOSED BY '"'
  SKIP 1
  REJECTED DATA '/path/to/sales_rejects.log'
  EXCEPTIONS '/path/to/sales_exceptions.log';
  ```

  ### 주요 옵션 설명

  - `FROM LOCAL`: vsql 클라이언트가 실행되는 머신의 파일을 지정합니다.
  - `DELIMITER`: 필드를 구분하는 문자입니다. (기본값: `|`)
  - `ENCLOSED BY`: 문자열 데이터를 감싸는 문자입니다.
  - `SKIP`: 파일의 첫 N줄을 건너뜁니다. (주로 헤더를 제외할 때 사용)
  - `REJECTED DATA`: 파싱에 실패하거나 제약 조건을 위반한 데이터를 저장할 파일 경로입니다.
  - `EXCEPTIONS`: 로딩 중 발생한 예외(오류) 로그를 저장할 파일 경로입니다.

  ---

  ## COPY DIRECT

  `COPY DIRECT`는 `COPY`와 유사하지만, 데이터를 WOS(Write Optimized Store)를 거치지 않고 ROS(Read Optimized Store) 컨테이너에 직접 씁니다. 이는 대규모 초기 데이터 로딩에 매우 유용하지만, 동시 로딩 작업에는 제약이 있을 수 있습니다.

  ```sql
  COPY public.sales (...) DIRECT;
  ```
</div>