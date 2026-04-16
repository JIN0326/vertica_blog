---
title: vsql 명령어
layout: default
---

<div class="page-hero">
  <span class="chip">CoreTech</span>
  <h1>vsql 명령어</h1>
  <p>Vertica의 기본 CLI(Command-Line Interface) 클라이언트인 vsql의 사용법과 유용한 팁을 학습합니다.</p>
</div>

<div class="content-section content-section-centered" markdown="1">
  ## vsql 개요

  `vsql`은 Vertica 데이터베이스와 상호작용하기 위한 터미널 기반의 프론트엔드 도구입니다. PostgreSQL의 `psql`을 기반으로 하여, SQL 쿼리 실행, 메타데이터 조회, 스크립트 실행 등 다양한 작업을 수행할 수 있습니다.

  ---

  ## 접속하기

  다음과 같은 명령어로 데이터베이스에 접속할 수 있습니다.

  ```bash
  /opt/vertica/bin/vsql -h <hostname> -p <port> -d <database> -U <username>
  ```

  - `-h`: 호스트 주소
  - `-p`: 포트 번호 (기본값: 5433)
  - `-d`: 데이터베이스 이름
  - `-U`: 사용자 이름

  접속 후 비밀번호를 입력하라는 프롬프트가 나타납니다.

  ---

  ## 유용한 메타 명령어 (Meta-Commands)

  `vsql`에서는 `\`로 시작하는 메타 명령어를 사용하여 데이터베이스 객체 정보를 쉽게 조회할 수 있습니다.

  - `\l`: 데이터베이스 목록을 보여줍니다.
  - `\c [database]`: 다른 데이터베이스에 접속합니다.
  - `\dt [schema.]`: 스키마의 테이블 목록을 보여줍니다.
  - `\d [table_name]`: 특정 테이블의 상세 정보(컬럼, 타입 등)를 보여줍니다.
  - `\dj [schema.]`: 스키마의 프로젝션 목록을 보여줍니다.
  - `\di [schema.]`: 스키마의 인덱스 목록을 보여줍니다.
  - `\h [command]`: 특정 SQL 명령어의 도움말을 보여줍니다. (예: `\h CREATE TABLE`)
  - `\timing`: 쿼리 실행 시간을 표시하거나 끕니다.
  - `\i [filename]`: 파일에 저장된 SQL 스크립트를 실행합니다.
  - `\o [filename]`: 쿼리 결과를 파일로 저장합니다.
  - `\q`: vsql을 종료합니다.

  ---

  ## 팁

  - **스크립트 실행**: `-f` 옵션을 사용하여 로그인 없이 스크립트를 바로 실행할 수 있습니다. `vsql -f my_script.sql`
  - **출력 포맷**: `-A` (정렬되지 않은), `-H` (HTML), `-x` (확장) 등 다양한 출력 옵션을 활용하여 결과를 원하는 형태로 볼 수 있습니다.
</div>