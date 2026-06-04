---
title: "Vertica Copy: 대용량 데이터 고속 적재 및 에러 처리 - Vertica Blog"
layout: default
description: "Vertica의 대용량 데이터 고속 적재 명령어인 COPY의 사용법을 알아봅니다. 서버/클라이언트 파일, STDIN, 다른 Vertica DB로부터 데이터를 적재하는 방법과 REJECTED DATA, EXCEPTIONS를 활용한 에러 처리 기법을 다룹니다."
keywords: "vertica, copy, bulk load, data loading, copy from, copy local, copy from stdin, copy from vertica, rejected data, exceptions, delimiter"
canonical_url: "https://jin0326.github.io/vertica_blog/300coretech/300coretech#copy"
---

<div id="copy" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody">
## Copy (대용량 데이터 적재)

<div class="architecture-section">
  <p class="section-description"><code>COPY</code>문은 Vertica에서 대용량 데이터를 가장 빠르고 효율적으로 적재(Load)하기 위한 핵심 명령어입니다. 서버, 클라이언트, 또는 표준 입력(STDIN) 등 다양한 소스로부터 데이터를 읽어 테이블에 고속으로 삽입합니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. 서버 파일에서 적재 (COPY FROM)</h3>
    <p class="section-description">가장 일반적인 방식으로, Vertica 클러스터의 노드에 위치한 파일에서 데이터를 적재합니다. 모든 노드에서 파일에 접근할 수 있어야 하며, 와일드카드(*)를 사용하여 여러 파일을 동시에 로드할 수 있습니다.</p>
    <div class="syntax-box">
      <strong>기본 구문:</strong>
      <pre><code>COPY schema.table (column_list)
FROM '/path/on/server/file.csv'
[옵션 지정];</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 로드 옵션</dt>
      <dd class="feature-dd">
        <strong>DELIMITER:</strong> 데이터의 구분자를 지정합니다. (예: <code>DELIMITER ','</code>)<br>
        <strong>ENCLOSED BY:</strong> 특정 기호로 감싸진 문자열 데이터를 처리합니다. (예: <code>ENCLOSED BY '"'</code>)<br>
        <strong>SKIP:</strong> 파일의 시작 부분에서 건너뛸 행(Row)의 수를 지정합니다. (헤더 행을 무시할 경우 <code>SKIP 1</code>)<br>
        <strong>RECORD TERMINATOR:</strong> 레코드(행)의 끝을 나타내는 문자를 지정합니다. (기본값: <code>\n</code>)<br>
        <strong>NO ESCAPE:</strong> 이스케이프 문자 처리를 비활성화하여 백슬래시(<code>\</code>)를 일반 문자로 취급합니다.<br>
        <strong>NULL AS:</strong> 특정 문자열을 NULL 값으로 인식하도록 지정합니다. (예: <code>NULL AS 'N/A'</code>)
      </dd>
    </dl>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 클라이언트 파일에서 적재 (COPY LOCAL)</h3>
    <p class="section-description"><code>vsql</code>과 같은 클라이언트가 실행 중인 로컬 머신의 파일에서 데이터를 적재합니다. 서버에 파일을 업로드하는 중간 과정 없이 로컬 데이터를 바로 테이블에 삽입할 수 있어 편리합니다.</p>
    <div class="syntax-box">
      <strong>기본 구문:</strong>
      <pre><code>COPY schema.table
FROM LOCAL '/path/on/client/data.csv'
DELIMITER ',';</code></pre>
    </div>
    <ul class="feature-list">
      <li><span class="feature-list__icon">💡</span> <strong>Tip:</strong> <span><code>LOCAL</code> 키워드를 사용하면 파일 경로는 서버가 아닌 클라이언트 머신을 기준으로 해석됩니다.</span></li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. 표준 입력에서 적재 (COPY FROM STDIN)</h3>
    <p class="section-description">다른 명령어의 출력 결과를 파이프(<code>|</code>)로 연결하여 파일 생성 없이 바로 Vertica 테이블로 적재할 때 사용됩니다. 쉘 스크립트 기반의 데이터 파이프라인에서 유용하게 활용됩니다.</p>
    <div class="syntax-box">
      <strong>파이프라인 연동 예시:</strong>
      <pre><code># gzip으로 압축된 파일을 풀면서 바로 COPY
gunzip -c /path/on/server/data.csv.gz | vsql -c "COPY schema.table FROM STDIN DELIMITER ','"</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">4. 다른 Vertica DB에서 적재 (COPY FROM VERTICA)</h3>
    <p class="section-description"><code>COPY FROM VERTICA</code>는 다른 Vertica 데이터베이스에 있는 테이블로부터 대용량 데이터를 고속으로 복사하는 명령어입니다. 이 작업을 수행하려면, 먼저 <code>CONNECT TO VERTICA</code> 문을 사용하여 원본 데이터베이스에 연결해야 합니다. 단일/소량 데이터 처리에 사용되는 <code>INSERT</code> 문과 달리, <code>COPY</code>는 대규모 벌크 로드에 최적화되어 있습니다.</p>
    <div class="syntax-box">
      <strong>다른 DB에서 데이터 복사 예시:</strong>
      <pre><code>-- 1. 원본 데이터베이스에 연결
CONNECT TO VERTICA source_db USER dbadmin PASSWORD 'password' ON 'source_host', 5433;

-- 2. 원본 DB의 'source_sales' 테이블에서 현재 DB의 'target_sales'로 데이터 복사
COPY public.target_sales
FROM VERTICA source_db.public.source_sales;

-- 3. 연결 해제
DISCONNECT source_db;</code></pre>
    </div>
  </div>


  <div class="architecture-subsection">
    <h3 class="section-subtitle">5. 에러 처리 및 데이터 검증</h3>
    <p class="section-description">데이터 적재 시 발생하는 포맷 오류나 제약조건 위반 데이터를 추적하고 안전하게 격리할 수 있습니다.</p>
    <div class="syntax-box">
      <strong>에러 처리 구문 예시:</strong>
      <pre><code>COPY sales FROM '/data/sales_data.csv' DELIMITER ','
      REJECTED DATA '/data/logs/sales_rejected.csv'
      EXCEPTIONS '/data/logs/sales_exceptions.log'
      ABORT ON ERROR;</code></pre>
    </div>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>REJECTED DATA:</strong> <span>적재에 실패한 원본 데이터(Row)를 지정한 경로의 파일로 따로 저장합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>EXCEPTIONS:</strong> <span>데이터가 거부된 구체적인 이유(에러 메시지 및 발생 위치)를 파일에 기록하여 원인 파악을 돕습니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>ABORT ON ERROR:</strong> <span>단 1건의 에러라도 발생하면 전체 Copy 작업을 즉시 중단하고 롤백(Rollback)시켜 데이터 정합성을 보호합니다.</span></li>
    </ul>
  </div>
</div>
</div>
</div>
