---
title: "Vertica vsql (CLI): 접속, 메타 커맨드, 실행 옵션 가이드 - Vertica Blog"
layout: default
description: "Vertica의 강력한 CLI 유틸리티인 vsql 사용법을 알아봅니다. 데이터베이스 접속, 쿼리 실행 옵션, 그리고 \d, \timing, \x 등 유용한 메타 커맨드를 설명합니다."
keywords: "vertica, vsql, cli, command line, meta-command, vsql options"
canonical_url: "https://jin0326.github.io/vertica_blog/300coretech/300coretech#vsql"
---

<div id="vsql" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody">
## vsql (CLI)

<div class="architecture-section">
  <p class="section-description"><code>vsql</code>은 Vertica 데이터베이스에 접속하여 SQL 쿼리를 실행하고 결과를 확인할 수 있는 강력한 커맨드 라인 유틸리티입니다. 데이터베이스 관리 및 스크립트 기반의 자동화 작업에 주로 활용됩니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">주요 접속 및 실행 옵션</h3>
    <p class="section-description">vsql 실행 시 다양한 파라미터를 통해 접속 대상과 실행 방식을 세밀하게 지정할 수 있습니다.</p>

  <div class="syntax-box">
      <strong>vsql 접속 구문:</strong>
      <pre><code>vsql -h [hostname/IP] -d [dbname] -U [username] -w [password]</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 접속 옵션</dt>
      <dd class="feature-dd">
        <strong>vsql :</strong> 현재 OS 계정과 동일한 DB User로 접속합니다.<br>
        <strong>-h:</strong> 접속할 DB의 IP를 지정합니다. (미지정 시 Local 접속)<br>
        <strong>-d:</strong> 접속할 DB명을 지정합니다. (기본값: 현재 OS 계정)<br>
        <strong>-U:</strong> 접속할 DB User명을 지정합니다. (기본값: 현재 OS 계정)<br>
        <strong>-w:</strong> 접속할 계정의 패스워드를 지정합니다.
      </dd>
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 실행 옵션</dt>
      <dd class="feature-dd">
        <strong>-c "query":</strong> vsql 접속 화면으로 들어가지 않고 단일 쿼리를 실행한 후 즉시 종료합니다.<br>
        <strong>-f filename.sql:</strong> 파일에 작성된 SQL 스크립트를 일괄 실행합니다.<br>
        <strong>-a:</strong> 스크립트 파일 실행 시, 수행되는 쿼리문도 함께 화면에 출력합니다.<br>
        <strong>-E:</strong> <code>\d</code> 같은 메타 커맨드 수행 시, 내장되어 백그라운드에서 동작하는 실제 쿼리를 출력합니다.<br>
        <strong>-e:</strong> 사용자가 수행하는 쿼리를 화면에 한 번 더 출력해 줍니다.<br>
        <strong>-m:</strong> 메시지 출력 레벨을 지정합니다 (디버그용).
      </dd>
    </dl>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">vsql 메타 커맨드 (Meta-Commands)</h3>
    <p class="section-description">vsql 내부에서 <code>\</code>(백슬래시)로 시작하는 명령어를 통해 다양한 정보 조회 및 환경 설정을 수행할 수 있습니다.</p>

  <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>\?:</strong> <span>메타 커맨드(Meta-Command) 전반에 대한 도움말을 출력합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>\h:</strong> <span>SQL 명령어 구문에 대한 상세 도움말을 출력합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>\c:</strong> <span>현재 세션을 끊고 다른 DB 혹은 다른 User로 재접속합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>\d [table]:</strong> <span>특정 테이블, 뷰, 시퀀스 등의 구조(컬럼 타입 등) 및 상세 정보를 출력합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>\dt:</strong> <span>현재 접속한 스키마의 전체 테이블 목록을 조회합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>\v:</strong> <span>현재 사용 가능한 View 객체 목록을 출력합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>\e:</strong> <span>마지막으로 실행한 쿼리를 OS 기본 에디터(vi 등)로 열어 편집 후 바로 실행합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>\timing:</strong> <span>쿼리 실행 시간(Elapsed time) 출력 기능을 On/Off 합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>\x:</strong> <span>출력 포맷을 컬럼형(세로) 또는 레코드형(가로)으로 전환(Expanded display)하여 긴 결과값을 보기 쉽게 만듭니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>\!:</strong> <span>vsql 프롬프트를 빠져나가지 않은 상태에서 OS 쉘 명령어(예: <code>\! ls -al</code>)를 실행합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>\o [file]:</strong> <span>이후 실행되는 쿼리의 결과를 화면이 아닌 지정한 파일로 저장합니다.</span></li>
    </ul>
  </div>
</div>
</div>
</div>
