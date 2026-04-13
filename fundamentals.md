---
title: Vertica Fundamentals
layout: default
---

<style>
  :root {
    --bg-1: #0f2027;
    --bg-2: #203a43;
    --bg-3: #2c5364;
    --card: rgba(255, 255, 255, 0.12);
    --text: #eef7ff;
    --sub: #bdd4e6;
    --accent: #f6bd60;
    --accent-2: #f7ede2;
    --bg-card: #1f2937;
    --bg-panel: rgba(255, 255, 255, 0.08);
  }

  * { box-sizing: border-box; }

  .fundamentals-wrap {
    min-height: 100vh;
    background: linear-gradient(135deg, var(--bg-1), var(--bg-2), var(--bg-3));
    color: var(--text);
    font-family: "Malgun Gothic", "Pretendard", "Noto Sans KR", sans-serif;
    padding: 2.5rem 1rem 3rem;
  }

  .fundamentals-hero {
    width: min(920px, 92vw);
    margin: 0 auto;
    padding: 3.2rem 2.4rem;
    border-radius: 24px;
    background: var(--card);
    border: 1px solid rgba(255, 255, 255, 0.22);
    backdrop-filter: blur(12px);
    box-shadow: 0 24px 60px rgba(0, 0, 0, 0.25);
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

  .fundamentals-glow.one { background: #9ad1d4; top: -8rem; left: -6rem; }
  .fundamentals-glow.two { background: #f6bd60; right: -8rem; bottom: -10rem; }

  .fundamentals-inner {
    position: relative;
    z-index: 1;
  }

  .fundamentals-chip {
    display: inline-block;
    padding: 0.4rem 0.85rem;
    border-radius: 999px;
    background: rgba(255, 255, 255, 0.16);
    color: var(--accent-2);
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
    width: min(920px, 92vw);
    margin: 2rem auto 0;
    padding: 2.2rem 2rem;
    border-radius: 24px;
    background: var(--card);
    border: 1px solid rgba(255, 255, 255, 0.22);
    box-shadow: 0 24px 60px rgba(0, 0, 0, 0.18);
  }

  .fundamentals-card h2 {
    margin-top: 0;
    font-size: 1.8rem;
    color: var(--accent-2);
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
      <h1 class="fundamentals-title">버티카 기초 교육</h1>
      <p class="fundamentals-description">Vertica, 대용량 데이터 분석을 위한 컬럼 기반 DB의 시작입니다. 핵심 개념부터 운영, 쿼리, 백업, 적재까지 기초를 빠르게 정리하세요.</p>
      <div class="fundamentals-actions">
        <a class="fundamentals-primary" href="{{ '/what-is-vertica' | relative_url }}">학습하기</a>
        <a class="fundamentals-secondary" href="{{ '/technology' | relative_url }}">학습가이드</a>
        <a class="fundamentals-secondary" href="file:///C:/Users/SOO/MyProject/vertica_study/index.html">로컬 Study 프로젝트</a>
      </div>
    </div>
  </div>

  <article class="fundamentals-card">
    <h2>Fundamentals 학습 개요</h2>
    <div class="fundamentals-section">
      <h3>1. Vertica DB 구성과 초기 설정</h3>
      <ul class="fundamentals-list">
        <li>Vertica는 여러 노드로 구성된 분산형 데이터베이스입니다.</li>
        <li>기본 설치 후 `configuration parameter`, `profile`, `user`, `role`, `resource pool`을 설정해야 합니다.</li>
        <li>`private/public IP`와 `export address` 구성은 클러스터 통신을 위해 필수입니다.</li>
        <li>EON 모드에서는 Object Storage 기반 스토리지와 `minio` 같은 S3 호환 스토리지 구성이 필요합니다.</li>
      </ul>
    </div>

    <div class="fundamentals-section">
      <h3>2. 사용자와 권한 관리</h3>
      <ul class="fundamentals-list">
        <li>사용자와 역할(Role)을 생성하고 객체별 권한을 `grant`로 부여합니다.</li>
        <li>`schema`, `table` 단위로 권한을 설정하며 `WITH GRANT OPTION`을 필요에 따라 사용합니다.</li>
        <li>권한 체계는 보안과 운영 편의성을 모두 고려해 설계해야 합니다.</li>
      </ul>
    </div>

    <div class="fundamentals-section">
      <h3>3. 백업, 복구, 업그레이드</h3>
      <ul class="fundamentals-list">
        <li>Vertica 백업은 `vbr`을 통해 수행됩니다.</li>
        <li>`full`, `object`, `copycluster` 백업 전략을 적절히 조합합니다.</li>
        <li>`Scrutinize`를 생성하여 로그와 백업 결과를 분석합니다.</li>
        <li>업그레이드 전 백업을 반드시 준비하고, 완료 후 검증 절차를 수행합니다.</li>
      </ul>
    </div>

    <div class="fundamentals-section">
      <h3>4. 운영 모니터링과 상태 확인</h3>
      <ul class="fundamentals-list">
        <li>`sessions`, `user_sessions`를 점검하여 연결 상태와 활동을 모니터링합니다.</li>
        <li>`transaction`, `statement`, `session`, `rollback`, `commit` 관계를 이해해야 안정적 운영이 가능합니다.</li>
        <li>`system tables`를 활용해 쿼리 상태, 에러, 로드, 라이선스 정보를 확인합니다.</li>
        <li>정기 점검 쿼리로 DB와 MC 상태를 주기적으로 점검합니다.</li>
      </ul>
    </div>

    <div class="fundamentals-section">
      <h3>5. 쿼리와 성능 튜닝</h3>
      <ul class="fundamentals-list">
        <li>Vertica MPP 구조와 `projection` 개념을 이해하면 성능을 개선할 수 있습니다.</li>
        <li>`EXPLAIN`으로 쿼리 실행 계획을 분석하고 튜닝합니다.</li>
        <li>`functions`을 활용해 데이터 처리와 분석을 효율적으로 수행합니다.</li>
        <li>`Comments` 구문과 `dynamic query` 작성은 복잡한 SQL 작업에서 유용합니다.</li>
      </ul>
    </div>

    <div class="fundamentals-section">
      <h3>6. 데이터 적재와 내보내기</h3>
      <ul class="fundamentals-list">
        <li>`COPY` 구문으로 데이터를 Vertica에 적재합니다.</li>
        <li>`delimiter`, `recordterminator`, `null 처리`, `filler`, 특수문자 처리 등 옵션을 활용합니다.</li>
        <li>`copy from vertica`와 `export to vertica`로 데이터 이동을 수행합니다.</li>
        <li>`CTAS`, `LIKE`, `COPY_TABLE`로 테이블 생성 및 관리를 수행합니다.</li>
      </ul>
    </div>

    <div class="fundamentals-section">
      <h3>7. 검사와 성능 테스트</h3>
      <ul class="fundamentals-list">
        <li>`Vxperf`로 `vioper`, `vnetperf`, `vcpuperf` 성능 지표를 확인합니다.</li>
        <li>로그 파일(install, create, start, vbr 수행 시 로그)을 이해하고 경로를 확인합니다.</li>
        <li>포트 변경 테스트 및 네트워크 구성을 통해 운영 환경 안정성을 검증합니다.</li>
      </ul>
    </div>

    <p class="fundamentals-footer">© 2026 Vertica Basic Study. All rights reserved.</p>
  </article>
</div>
