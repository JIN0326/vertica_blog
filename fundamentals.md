---
title: Vertica Fundamentals
layout: default
---

<style>
  :root {
    --bg-1: #f8fbff;
    --bg-2: #eef4ff;
    --bg-3: #dfe9ff;
    --card-bg: rgba(255, 255, 255, 0.95);
    --text: #0f1f3d;
    --sub: #556b8a;
    --accent: #2d69ff;
    --accent-2: #1c44d9;
    --shadow: 0 20px 50px rgba(45,105,255,0.08);
    --border-color: rgba(45, 105, 255, 0.14);
  }

  * { box-sizing: border-box; }

  .fundamentals-wrap {
    min-height: 100vh;
    color: var(--text);
    font-family: "Malgun Gothic", "Pretendard", "Noto Sans KR", sans-serif;
    padding: 2.5rem 1rem 3rem;
  }

  .fundamentals-hero {
    width: min(920px, 92vw);
    margin: 0 auto;
    padding: 3.2rem 2.4rem;
    border-radius: 24px;
    background: var(--card-bg);
    border: 1px solid var(--border-color);
    backdrop-filter: blur(12px);
    box-shadow: var(--shadow);
    position: relative;
    overflow: hidden;
  }

  .fundamentals-glow {
    position: absolute;
    width: 38rem;
    height: 38rem;
    border-radius: 50%;
    filter: blur(70px);
    opacity: 0.22;
    z-index: 0;
  }

  .fundamentals-glow.one { background: rgba(45, 105, 255, 0.22); top: -8rem; left: -6rem; }
  .fundamentals-glow.two { background: rgba(41, 109, 255, 0.14); right: -8rem; bottom: -10rem; }

  .fundamentals-inner {
    position: relative;
    z-index: 1;
  }

  .fundamentals-chip {
    display: inline-block;
    padding: 0.4rem 0.85rem;
    border-radius: 999px;
    background: rgba(45, 105, 255, 0.08);
    color: var(--accent);
    font-size: 0.84rem;
    letter-spacing: 0.02em;
    margin-bottom: 1rem;
  }

  .fundamentals-title {
    margin: 0;
    font-size: clamp(2rem, 4.2vw, 3.4rem);
    line-height: 1.2;
    font-weight: 800;
  }

  .fundamentals-description {
    margin: 1rem auto 2rem;
    max-width: 680px;
    color: var(--sub);
    font-size: clamp(1rem, 2vw, 1.15rem);
    line-height: 1.7;
  }

  .fundamentals-actions {
    display: flex;
    gap: 0.8rem;
    justify-content: center;
    flex-wrap: wrap;
  }

  .fundamentals-actions a,
  .fundamentals-actions button {
    border: none;
    border-radius: 12px;
    font-size: 1rem;
    font-weight: 700;
    padding: 0.9rem 1.4rem;
    cursor: pointer;
    transition: transform 180ms ease;
    text-decoration: none;
    display: inline-flex;
    align-items: center;
    justify-content: center;
  }

  .fundamentals-actions a:hover,
  .fundamentals-actions button:hover { transform: translateY(-2px); }

  .fundamentals-primary {
    background: linear-gradient(120deg, #f6bd60, #f28482);
    color: #1c1f2a;
  }

  .fundamentals-secondary {
    background: rgba(255, 255, 255, 0.2);
    color: var(--text);
    border: 1px solid rgba(255, 255, 255, 0.24);
  }

  .fundamentals-card {
    width: min(940px, 92vw);
    margin: 2rem auto 0;
    padding: 2.2rem 2rem;
    border-radius: 24px;
    background: var(--card-bg);
    border: 1px solid var(--border-color);
    box-shadow: var(--shadow);
  }

  .fundamentals-card h2 {
    margin-top: 0;
    font-size: 1.8rem;
    color: var(--accent-2);
  }

  .study-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
    gap: 18px;
    margin-top: 1.6rem;
  }

  .study-card {
    padding: 1.25rem;
    border-radius: 18px;
    background: var(--card-bg);
    border: 1px solid var(--border-color);
    min-height: 180px;
    display: flex;
    box-shadow: var(--shadow);
    flex-direction: column;
    justify-content: space-between;
  }

  .study-card h3 {
    margin: 0 0 0.75rem;
    font-size: 1rem;
    color: var(--text);
  }

  .study-card p {
    font-size: 0.9rem;
    margin: 0;
    color: var(--sub);
    line-height: 1.6;
    flex-grow: 1;
  }

  .study-card a {
    margin-top: 1rem;
    color: var(--accent);
    font-weight: 700;
    text-decoration: none;
  }

  .fundamentals-section {
    margin-top: 1.6rem;
  }

  .fundamentals-section h3 {
    margin-bottom: 0.75rem;
    color: var(--text);
    font-size: 1.15rem;
  }

  .fundamentals-list {
    padding-left: 1.2rem;
    margin: 0;
    color: var(--sub);
    line-height: 1.8;
  }

  .fundamentals-list li {
    margin-bottom: 0.65rem;
  }

  .fundamentals-footer {
    margin-top: 1.8rem;
    text-align: center;
    font-size: 0.88rem;
    color: var(--sub);
  }
</style>

<div class="fundamentals-wrap">
  <div class="fundamentals-hero">
    <div class="fundamentals-glow one"></div>
    <div class="fundamentals-glow two"></div>
    <div class="fundamentals-inner">
      <span class="fundamentals-chip">Vertica Study Program</span>
      <h1 class="fundamentals-title">Vertica 기초 교육</h1>
      <p class="fundamentals-description">Vertica, 대용량 데이터 분석을 위한 컬럼 기반 DB의 시작입니다. 핵심 개념부터 운영, 쿼리, 백업, 적재까지 기초를 빠르게 정리하세요.</p>
      <div class="fundamentals-actions">
        <a class="fundamentals-primary" href="file:///C:/Users/SOO/MyProject/vertica_study/start.html">학습하기</a>
      </div>
    </div>
  </div>

  <article class="fundamentals-card">
    <h2>28개 Vertica 학습 항목</h2>
    <div class="study-grid">
      <article class="study-card">
        <h3>01. Vertica DB 3대 구성</h3>
        <p>3대 노드 구성 차이를 경험하며 Vertica 클러스터 구조를 이해합니다.</p>
        <a href="file:///C:/Users/SOO/MyProject/vertica_study/99ref/Vertica_Install_Prerequisites.txt">참고자료 다운로드</a>
      </article>
      <article class="study-card">
        <h3>02. Vertica DB 구성 후 초기 세팅 적용</h3>
        <p>Configuration parameter, profile, user, role, resource pool 설정을 적용합니다.</p>
        <a href="file:///C:/Users/SOO/MyProject/vertica_study/99ref/Vertica_초기세팅_TEST.txt">참고자료 다운로드</a>
      </article>
      <article class="study-card">
        <h3>03. private/public IP 구분 및 export address 설정</h3>
        <p>네트워크 구성을 확인하고 export address를 정확하게 세팅합니다.</p>
        <a href="file:///C:/Users/SOO/MyProject/vertica_study/99ref/Vertica_Export_Address.txt">참고자료 다운로드</a>
      </article>
      <article class="study-card">
        <h3>04. Vertica role 권한 생성</h3>
        <p>Schema와 table 권한을 role로 생성하고 사용자에게 부여합니다.</p>
        <a href="file:///C:/Users/SOO/MyProject/vertica_study/99ref/Vertica_grant_role.txt">참고자료 다운로드</a>
      </article>
      <article class="study-card">
        <h3>05. Vertica vbr backup 및 restore 수행</h3>
        <p>full, object, copycluster 백업 옵션을 확인하고 restore를 수행합니다.</p>
        <a href="file:///C:/Users/SOO/MyProject/vertica_study/99ref/Vertica_VBR_백업.pdf">참고자료 다운로드</a>
      </article>
      <article class="study-card">
        <h3>06. Vertica Scrutinize 생성</h3>
        <p>Scrutinize를 생성하고 로그를 확인하여 시스템 상태를 분석합니다.</p>
        <a href="file:///C:/Users/SOO/MyProject/vertica_study/99ref/Vertica_Scrutinize.pdf">참고자료 다운로드</a>
      </article>
      <article class="study-card">
        <h3>07. Vertica MC 설치 및 DB 등록</h3>
        <p>Management Console을 설치하고 Vertica DB를 등록합니다.</p>
        <a href="file:///C:/Users/SOO/MyProject/vertica_study/99ref/MC_install.pdf">참고자료 다운로드</a>
      </article>
      <article class="study-card">
        <h3>08. Vertica 정기점검 쿼리 수행</h3>
        <p>시스템 테이블 기반 정기점검 쿼리를 수행하며 상태를 점검합니다.</p>
        <a href="file:///C:/Users/SOO/MyProject/vertica_study/99ref/정기점검.sql">참고자료 다운로드</a>
      </article>
      <article class="study-card">
        <h3>09. Vertica EonDB 구성 및 Storage 구성(Minio)</h3>
        <p>EON 모드와 Minio 기반 스토리지를 구성하여 확장성을 확인합니다.</p>
        <a href="file:///C:/Users/SOO/MyProject/vertica_study/99ref/Vertica_Eonmode.txt">참고자료 다운로드</a>
      </article>
      <article class="study-card">
        <h3>10. Vertica DB Node 추가 구성</h3>
        <p>Node 추가 구성을 진행하고 Ksafe 개념을 이해합니다.</p>
        <a href="file:///C:/Users/SOO/MyProject/vertica_study/99ref/Vertica_NodeAdd.sql">참고자료 다운로드</a>
      </article>
      <article class="study-card">
        <h3>11. Vertica RE IP 작업 수행</h3>
        <p>RE IP 과정으로 노드 네트워크 설정을 재구성합니다.</p>
        <a href="file:///C:/Users/SOO/MyProject/vertica_study/99ref/Vertica_ReIP.txt">참고자료 다운로드</a>
      </article>
      <article class="study-card">
        <h3>12. Vertica DB 업그레이드 작업 수행</h3>
        <p>백업 후 업그레이드 수행과 결과 검증 절차를 점검합니다.</p>
        <a href="#">참고자료 없음</a>
      </article>
      <article class="study-card">
        <h3>13. Vertica Stored Procedure 생성 및 수행</h3>
        <p>stored procedure를 생성하고 perform, execute, exception을 학습합니다.</p>
        <a href="file:///C:/Users/SOO/MyProject/vertica_study/99ref/Vertica_StoredProcedure_2025.pdf">참고자료 다운로드</a>
      </article>
      <article class="study-card">
        <h3>14. vsql_command + vsql_option</h3>
        <p>vsql 명령어와 옵션을 이해하여 스크립트 실행을 제어합니다.</p>
        <a href="file:///C:/Users/SOO/MyProject/vertica_study/99ref/Vertica_vsql_command.sql">참고자료 다운로드</a>
      </article>
      <article class="study-card">
        <h3>15. projection 정보 조회 쿼리 작성</h3>
        <p>Projection 관련 시스템 테이블을 조회하여 성능 정보를 파악합니다.</p>
        <a href="#">참고자료 없음</a>
      </article>
      <article class="study-card">
        <h3>16. transaction / statement / session / rollback / commit 이해</h3>
        <p>트랜잭션과 세션 관련 동작을 이해하여 안정적 DB 운영을 준비합니다.</p>
        <a href="#">참고자료 없음</a>
      </article>
      <article class="study-card">
        <h3>17. sessions / user_sessions 정보 이해</h3>
        <p>세션 정보와 user_sessions 차이를 학습합니다.</p>
        <a href="#">참고자료 없음</a>
      </article>
      <article class="study-card">
        <h3>18. constraint system tables</h3>
        <p>Constraint 관련 시스템 테이블과 체크 방법을 살펴봅니다.</p>
        <a href="#">참고자료 없음</a>
      </article>
      <article class="study-card">
        <h3>19. comments 구문 + dynamic query 생성</h3>
        <p>동적 쿼리와 comments 구문을 활용해 유연한 SQL을 작성합니다.</p>
        <a href="#">참고자료 없음</a>
      </article>
      <article class="study-card">
        <h3>20. Vxperf - Vioperf / Vnetperf / Vcpuperf 수행</h3>
        <p>성능 유틸리티를 실행하여 I/O, 네트워크, CPU 성능을 점검합니다.</p>
        <a href="#">참고자료 없음</a>
      </article>
      <article class="study-card">
        <h3>21. LOG 정리</h3>
        <p>설치, DB 생성, 시작, VBR 로그를 확인하고 위치를 파악합니다.</p>
        <a href="#">참고자료 없음</a>
      </article>
      <article class="study-card">
        <h3>22. DB / MC 기본 PORT 변경</h3>
        <p>기본 포트를 변경하고 정상 동작을 확인합니다.</p>
        <a href="#">참고자료 없음</a>
      </article>
      <article class="study-card">
        <h3>23. SYSTEM TABLE CHECK</h3>
        <p>쿼리, 오류, 로드, 세션, 라이선스 정보를 시스템 테이블로 점검합니다.</p>
        <a href="#">참고자료 없음</a>
      </article>
      <article class="study-card">
        <h3>24. Functions</h3>
        <p>date, string, math, rank, row_number 등 주요 함수를 학습합니다.</p>
        <a href="#">참고자료 없음</a>
      </article>
      <article class="study-card">
        <h3>25. COPY 이해</h3>
        <p>COPY 로드 방식과 delimiter, NULL 처리, Filler, 특수문자 처리를 학습합니다.</p>
        <a href="file:///C:/Users/SOO/MyProject/vertica_study/99ref/Vertica_COPY.zip">참고자료 다운로드</a>
      </article>
      <article class="study-card">
        <h3>26. Explain + Tuning</h3>
        <p>Explain을 사용해 쿼리 실행 계획을 분석하고 튜닝합니다.</p>
        <a href="#">참고자료 없음</a>
      </article>
      <article class="study-card">
        <h3>27. COPY FROM VERTICA / EXPORT TO VERTICA</h3>
        <p>Vertica 간 복사/내보내기 작업을 이해하고 shell 스크립트를 구성합니다.</p>
        <a href="#">참고자료 없음</a>
      </article>
      <article class="study-card">
        <h3>28. CTAS</h3>
        <p>CTAS, LIKE, COPY_TABLE 차이와 데이터 이관 옵션을 정리합니다.</p>
        <a href="#">참고자료 없음</a>
      </article>
    </div>

  </article>
</div>
