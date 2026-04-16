---
title: 클라이언트 접속
layout: default
---

<div class="page-hero">
  <span class="chip">Utilization</span>
  <h1>클라이언트 접속</h1>
  <p>vsql, DBeaver 등 다양한 클라이언트 도구를 사용하여 Vertica 데이터베이스에 접속하는 방법을 알아봅니다.</p>
</div>

<div class="content-section content-section-centered" markdown="1">
  ## vsql (CLI)

  `vsql`은 Vertica와 함께 제공되는 공식 커맨드 라인 클라이언트입니다. 터미널 환경에서 쿼리를 실행하고 데이터베이스를 관리하는 데 사용됩니다.

  ```bash
  # 기본 접속 명령어
  /opt/vertica/bin/vsql -h <host_ip> -d <db_name> -U <user_name>
  ```

  자세한 사용법은 **vsql 명령어** 페이지를 참고하세요.

  ---

  ## DBeaver (GUI)

  DBeaver는 다양한 데이터베이스를 지원하는 인기 있는 오픈소스 GUI 도구입니다. Vertica JDBC 드라이버를 사용하여 쉽게 연결할 수 있습니다.

  1.  **드라이버 다운로드**: Vertica 공식 웹사이트에서 최신 JDBC 드라이버(`.jar` 파일)를 다운로드합니다.
  2.  **DBeaver 드라이버 설정**:
      - DBeaver에서 `데이터베이스` > `드라이버 관리자`로 이동합니다.
      - `새 드라이버`를 클릭하고 Vertica 드라이버를 설정합니다.
      - `라이브러리` 탭에서 다운로드한 `.jar` 파일을 추가합니다.
  3.  **새 연결 생성**:
      - `데이터베이스` > `새 데이터베이스 연결`을 선택하고, 방금 생성한 Vertica 드라이버를 선택합니다.
      - 호스트, 포트(5433), 데이터베이스, 사용자 이름, 비밀번호 등 연결 정보를 입력합니다.
      - `Test Connection`으로 연결을 확인한 후 완료합니다.

  ---

  ## 기타 표준 드라이버

  Vertica는 업계 표준 연결 방식을 지원하여 대부분의 BI 도구 및 애플리케이션과 쉽게 연동할 수 있습니다.

  - **JDBC**: Java 기반 애플리케이션에서 연결할 때 사용합니다.
  - **ODBC**: C/C++, Python, R 등 다양한 언어 및 Windows 기반 애플리케이션에서 연결할 때 사용합니다.
  - **ADO.NET**: .NET 프레임워크 기반 애플리케이션에서 연결할 때 사용합니다.
</div>