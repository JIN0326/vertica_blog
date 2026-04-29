---
title: Vertica Core Tech
layout: default
---
<div class="page-hero">
  <span class="chip">Core Tech</span>
  <h1>Vertica Core Tech</h1>
  <p>Vertica 개발 및 운영에 필수적인 핵심 기술인 vsql, 데이터 적재(COPY), 그리고 쿼리 실행 계획 분석(EXPLAIN)에 대해 상세히 알아봅니다.</p>
</div>

<div class="page-layout">
  <div class="content-section" markdown="1">

<div id="vsql" style="scroll-margin-top: 100px;"></div>

## vsql (Command Line Interface)

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
        <strong>vsql DB명:</strong> 현재 OS 계정과 동일한 DB User로 접속합니다.<br>
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

<hr style="margin: 3rem 0;">
<div id="copy" style="scroll-margin-top: 100px;"></div>

## COPY (대용량 데이터 적재)

<div class="architecture-section">
  <p class="section-description"><code>COPY</code>문은 Vertica에서 대용량 데이터를 가장 빠르고 효율적으로 적재(Load)하기 위한 핵심 명령어입니다. 외부 파일이나 스트림으로부터 데이터를 읽어 테이블에 고속으로 삽입합니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">COPY 기본 구문 및 옵션</h3>
    <div class="syntax-box">
      <strong>기본 구문:</strong>
      <pre><code>COPY schema.table (column_list)
      FROM '/path/to/file.csv'
      [옵션 지정];</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 로드 옵션</dt>
      <dd class="feature-dd">
        <strong>DIRECT:</strong> WOS(메모리)를 거치지 않고 바로 ROS(디스크)에 물리적으로 데이터를 기록하여 대용량 적재 시 속도를 극대화합니다.<br>
        <strong>DELIMITER:</strong> 데이터의 구분자를 지정합니다. (예: <code>DELIMITER ','</code>)<br>
        <strong>ENCLOSED BY:</strong> 특정 기호로 감싸진 문자열 데이터를 처리합니다. (예: <code>ENCLOSED BY '"'</code>)<br>
        <strong>SKIP:</strong> 파일의 시작 부분에서 건너뛸 행(Row)의 수를 지정합니다. (헤더 행을 무시할 경우 <code>SKIP 1</code>)
      </dd>
    </dl>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">에러 처리 및 데이터 검증</h3>
    <p class="section-description">데이터 적재 시 발생하는 포맷 오류나 제약조건 위반 데이터를 추적하고 안전하게 격리할 수 있습니다.</p>
    <div class="syntax-box">
      <strong>에러 처리 구문 예시:</strong>
      <pre><code>COPY sales FROM '/data/sales_data.csv' DELIMITER ',' DIRECT
      REJECTED DATA '/data/logs/sales_rejected.csv'
      EXCEPTIONS '/data/logs/sales_exceptions.log'
      ABORT ON ERROR;</code></pre>
    </div>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>REJECTED DATA:</strong> <span>적재에 실패한 원본 데이터(Row)를 지정한 경로의 파일로 따로 저장합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>EXCEPTIONS:</strong> <span>데이터가 거부된 구체적인 이유(에러 메시지 및 발생 위치)를 파일에 기록하여 원인 파악을 돕습니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>ABORT ON ERROR:</strong> <span>단 1건의 에러라도 발생하면 전체 COPY 작업을 즉시 중단하고 롤백(Rollback)시켜 데이터 정합성을 보호합니다.</span></li>
    </ul>
  </div>
</div>

<hr style="margin: 3rem 0;">
<div id="export" style="scroll-margin-top: 100px;"></div>

## 데이터 내보내기 (Export)

<div class="architecture-section">
  <p class="section-description">Vertica의 데이터를 외부 파일로 추출하거나 다른 클러스터로 전송하는 방법입니다. 운영 환경에서는 주로 사용자 데이터의 논리적 백업이나 타 시스템과의 데이터 연계를 위해 사용됩니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. vsql을 이용한 텍스트 파일 내보내기</h3>
    <p class="section-description">가장 보편적인 방법으로, <code>vsql</code>의 실행 결과를 파일로 리다이렉션하여 CSV나 TSV 형태의 텍스트 파일을 생성합니다.</p>
    
    <div class="syntax-box">
      <strong>기본 실행 구문:</strong>
      <pre><code>vsql -U username -w password -At -F ',' -c "SELECT * FROM public.sales;" -o sales_backup.csv</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 옵션 설명</dt>
      <dd class="feature-dd">
        <strong>-A (unaligned):</strong> 출력 시 컬럼 간격을 맞추지 않고 붙여서 출력합니다.<br>
        <strong>-t (tuples only):</strong> 컬럼명(Header) 없이 데이터 행만 출력합니다.<br>
        <strong>-F (field separator):</strong> 컬럼 구분자를 지정합니다 (예: <code>','</code>, <code>'|'</code>).<br>
        <strong>-o (output):</strong> 결과를 콘솔이 아닌 지정한 파일명으로 저장합니다.
      </dd>
    </dl>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. EXPORT TO PARQUET (데이터 레이크 연동)</h3>
    <p class="section-description">분석용 표준 포맷인 Parquet 형태로 데이터를 내보냅니다. S3나 HDFS 같은 외부 스토리지로 데이터를 백업할 때 매우 효율적입니다.</p>
    <div class="syntax-box">
      <pre><code>EXPORT TO PARQUET(directory='s3://my-bucket/backup/sales/') 
AS SELECT * FROM public.sales;</code></pre>
    </div>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>압축 효율:</strong> <span>Parquet의 컬럼 기반 압축을 통해 파일 크기를 최소화합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>병렬 처리:</strong> <span>모든 노드가 동시에 파일을 생성하므로 대용량 데이터 추출 속도가 매우 빠릅니다.</span></li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. EXPORT TO VERTICA (클러스터 간 복제)</h3>
    <p class="section-description">네트워크를 통해 한 Vertica 클러스터에서 다른 클러스터로 직접 데이터를 전송합니다.</p>
    <div class="syntax-box">
      <pre><code>-- 대상 DB에 접속 정보 설정 후 실행
CONNECT TO VERTICA target_db USER dbadmin PASSWORD 'password' ON 'target_host', 5433;

EXPORT TO VERTICA target_db.public.sales 
FROM public.sales;

DISCONNECT target_db;</code></pre>
    </div>
  </div>
</div>

<hr style="margin: 3rem 0;">
<div id="explain" style="scroll-margin-top: 100px;"></div>

## EXPLAIN (쿼리 실행 계획 분석)

<div class="architecture-section">
  <p class="section-description"><code>EXPLAIN</code>은 Vertica 옵티마이저가 SQL 쿼리를 어떻게 분석하고 실행할 것인지에 대한 <strong>실행 계획(Execution Plan)</strong>을 보여줍니다. 쿼리 성능 튜닝 및 병목 구간 파악을 위한 가장 기초적이고 필수적인 도구입니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">실행 계획 확인 방법</h3>
    <div class="syntax-box">
      <strong>기본 구문:</strong>
      <pre><code>EXPLAIN [SELECT / UPDATE / DELETE 쿼리];
PROFILE [SELECT / UPDATE / DELETE 쿼리];</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> EXPLAIN vs PROFILE</dt>
      <dd class="feature-dd">
        <strong>EXPLAIN:</strong> 쿼리를 <em>실제로 실행하지 않고</em> 옵티마이저가 예측한 경로(Path), 비용(Cost), 예상 행 수(Rows) 등을 텍스트 트리 형태로 반환합니다.<br>
        <strong>PROFILE:</strong> 쿼리를 <em>실제로 실행</em>하면서, 계획의 각 단계별 실제 소요 시간, 메모리 사용량 등의 런타임 통계(Profile Data)를 수집하여 더 정확한 튜닝 지표를 제공합니다.
      </dd>
    </dl>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">실행 계획 분석 포인트</h3>
    <p class="section-description">EXPLAIN 결과를 읽을 때는 트리 구조의 <strong>안쪽(가장 들여쓰기가 많이 된 하위 노드)부터 바깥쪽으로, 아래에서 위로</strong> 해석합니다.</p>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>Cost (비용):</strong> <span>옵티마이저가 해당 연산에 필요하다고 추정하는 자원의 양입니다. 튜닝 전후의 쿼리 효율성을 비교하는 상대적인 지표로 활용됩니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>Rows (예상 건수):</strong> <span>해당 단계에서 출력될 것으로 예상되는 데이터의 행 수입니다. (통계 정보가 최신화되지 않았다면 실제 건수와 크게 차이 날 수 있습니다.)</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>Access Path:</strong> <span>데이터를 읽는 방식입니다. Storage Access(물리 디스크 접근) 단계에서 조건절(Filter)이 효율적으로 푸시다운(Push-down) 되었는지 확인합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>Join Type:</strong> <span>Hash Join, Merge Join 등 조인 방식이 적절한지 파악합니다. 특히 대용량 조인 시 메모리가 부족해 디스크를 사용하는 현상(Spill to disk)이 발생할 우려가 있는지 체크합니다.</span></li>
    </ul>
    <div class="syntax-box">
      <strong>실행 계획 출력 예시:</strong>
      <pre><code>EXPLAIN SELECT * FROM sales s JOIN product p ON s.product_id = p.product_id;

-- 결과 요약 트리 (하단부터 상단으로 해석)
Access Path: 
 +-JOIN HASH [Cost: 500, Rows: 10K] (PATH ID: 1)
 |  Join Cond: (s.product_id = p.product_id)
 | +-- Outer -> STORAGE ACCESS for s [Cost: 200, Rows: 10K] 
 | |      Projection: public.sales_b0
 | +-- Inner -> STORAGE ACCESS for p [Cost: 50, Rows: 1K] 
 | |      Projection: public.product_b0</code></pre>
    </div>
  </div>
</div>

</div>

  <aside class="page-sidebar">
  <div class="sidebar-panel" style="padding-right: 1rem;">
    <h3>On this page</h3>
    <ul>
      <li><a href="#vsql">vsql</a></li>
      <li><a href="#copy">COPY</a></li>
      <li><a href="#export">EXPORT</a></li>
      <li><a href="#explain">EXPLAIN</a></li>
    </ul>
  </div>
  </aside>
</div>
