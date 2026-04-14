---
title: Vertica Blog Main
layout: default
---
<style>
  :root {
    --bg-1: #f4f8ff;
    --bg-2: #eef3ff;
    --bg-3: #e6edff;
    --card-bg: #ffffff;
    --text: #0f1f3d;
    --sub: #2f3340;
    --accent: #2d69ff;
    --accent-2: #0f1f3d;
    --shadow: 0 14px 34px rgba(45, 105, 255, 0.08);
    --border-color: rgba(45, 105, 255, 0.16);
    --mode-border: #1f5f9c;
  }

  body {
    background: linear-gradient(180deg, var(--bg-1), var(--bg-2), var(--bg-3));
    color: var(--text);
    font-family: "Malgun Gothic", "Pretendard", "Noto Sans KR", sans-serif;
  }

  a {
    text-decoration: none;
  }

  .site-content {
    width: min(1000px, calc(100% - 42px)); /* 전체 콘텐츠 폭을 약간 넓혔습니다. (980px -> 1000px) */
    padding: 44px 0 64px;
  }

  h1 {
    margin: 0;
    font-size: clamp(2.2rem, 5vw, 3.7rem);
    line-height: 1.14;
    font-weight: 800;
    color: var(--text);
    letter-spacing: -0.02em;
  }

  h2 {
    margin-top: 2rem;
    font-size: 1.8rem;
    color: var(--accent-2);
  }

  p {
    margin: 1rem 0 2rem;
    max-width: 760px;
    color: var(--sub);
    font-size: clamp(1rem, 1.8vw, 1.15rem);
    line-height: 1.6;
  }

  /* 1. 상단 ANALYTICS PLATFORM 크기 좀 더 크게 */
  .eyebrow {
    display: flex;
    width: min(100%, 760px);
    min-height: 40px; /* (변경) 높이 증가 30px -> 40px */
    align-items: center;
    padding: 0 16px; /* (변경) 패딩 조정 12px -> 16px */
    border-radius: 12px; /* (변경) 곡률 조정 10px -> 12px */
    background: #e5ecff;
    color: var(--accent);
    font-size: 1rem; /* (변경) 글씨 크기 크게 0.76rem -> 1rem */
    text-transform: uppercase;
    letter-spacing: 0.03em;
    margin-bottom: 24px; /* (변경) 여백 증가 16px -> 24px */
  }

  .hero-container {
    padding: 8px 0 4px;
  }

  .hero-content {
    width: 100%;
  }

  /* 2. Vertica의 차세대 ~ 문구 글씨 조금 더 크게 */
  .hero-content p {
    font-size: 1.25rem; /* (변경) 글씨 크기 증가 1.15rem -> 1.25rem */
    font-weight: 500;   /* (변경) 가독성을 위해 굵기 약간 추가 */
    margin: 1.5rem 0 1.5rem; /* (변경) 위아래 여백 조정 */
    /* text-align: left; /* (추가) 왼쪽 정렬 - p태그 기본값이 왼쪽 정렬이라 명시하지 않아도 됩니다. */
  }

  /* 3. Vertica 공식 문서 및 파트너사 링크 굵게 + 위로 이동 */
  .link-container {
    display: flex;
    gap: 12px; /* (변경) 간격 조정 10px -> 12px */
    margin-top: -10px; /* (변경) 위치 위로 이동 (양수에서 음수로 변경) */
    margin-bottom: 30px; /* (추가) 아래 summary-section과의 간격 확보 */
    flex-wrap: wrap;
  }

  .link-container a {
    padding: 10px 18px; /* (변경) 패딩 조정 8px 14px -> 10px 18px */
    background-color: var(--accent);
    color: white;
    border-radius: 6px; /* (변경) 곡률 조정 4px -> 6px */
    font-weight: 800; /* (변경) 더 굵게 bold -> 800 */
    font-size: 0.9rem; /* (변경) 글씨 크기 크게 0.72rem -> 0.9rem */
    border: 1px solid rgba(15, 31, 61, 0.06);
  }

  /* 4. Vertica는 무엇인가? ~ 글씨 크게 및 그림과 공간 구분 없애기 */
  .summary-section {
    padding: 0px 0px 8px; /* (변경) 패딩 최소화 - 테두리와 배경색을 없애기 위해 0으로 */
    background-color: transparent; /* (변경) 배경색 투명화 */
    border-radius: 0px; /* (변경) 곡률 제거 */
    margin-top: 10px; /* (변경) 여백 조정 */
    border: none; /* (변경) 테두리 제거 */
  }

  .summary-inner {
    margin-bottom: 0px; /* (변경) 아래 architecture-grid와의 간격 제거 */
  }

  /* Vertica는 무엇인가? 제목 크기 */
  .summary-inner h2 {
    margin: 0;
    color: #111111;
    font-size: clamp(2.4rem, 4.2vw, 3.4rem); /* (변경) 글씨 크기 증가 - clamp값 조정 */
    line-height: 1.2;
    margin-bottom: 20px; /* (추가) 본문과의 간격 */
  }

  /* Vertica는 무엇인가? 본문 크기 */
  .summary-inner p {
    margin-top: 0px;
    margin-bottom: 40px; /* (변경) 아래 그림과의 간격 */
    color: #202429;
    max-width: none; /* (변경) 최대 폭 제한 제거 */
    font-size: 1.25rem; /* (변경) 글씨 크기 크게 1.15rem -> 1.25rem */
    line-height: 1.7; /* (변경) 줄 간격 조정 */
  }

  .headline-mark {
    text-decoration: underline;
    text-underline-offset: 4px;
    text-decoration-thickness: 3px;
  }

  .inline-mark {
    text-decoration: underline;
    text-underline-offset: 2px;
  }

  .architecture-grid {
    display: flex;
    gap: 30px; /* (변경) 그림 사이 간격 증가 16px -> 30px */
    flex-wrap: wrap;
    margin-top: 0px; /* (추가) summary-inner와의 간격 제거 */
  }
  
  /* 5. Enterprise/Eon Mode 그림 크게 및 테두리 변경 */
  .mode-card {
    flex: 1;
    min-width: 300px;
    border: 6px solid #e0e0e0; /* (변경) 테두리 색상 및 두께 변경 var(--mode-border) -> #e0e0e0, 3px -> 6px */
    padding: 30px 30px 34px; /* (변경) 패딩 증가 20px -> 30px */
    border-radius: 60px; /* (변경) 곡률 증가 48px -> 60px */
    display: flex;
    flex-direction: column;
    background: #ffffff;
    box-shadow: 0 10px 30px rgba(0,0,0,0.05); /* (추가) 약한 그림자 효과 */
  }

  .mode-card > div:first-child {
    width: 100%;
    height: 250px; /* (변경) 그림 영역 높이 증가 180px -> 250px */
    display: flex;
    align-items: center;
    justify-content: center;
    margin-bottom: 15px; /* (변경) 간격 증가 8px -> 15px */
    overflow: hidden;
  }

  .mode-card img {
    width: 100%;
    height: 100%;
    object-fit: contain;
  }

  .mode-card h3 {
    margin: 10px 0 12px; /* (변경) 간격 조정 */
    color: #111111;
    font-size: 1.6rem; /* (추가) 제목 크기 증가 */
  }

  .mode-card p {
    margin: 0;
    max-width: none;
    color: #202429;
    font-size: 1.1rem; /* (변경) 글씨 크기 증가 1rem -> 1.1rem */
    line-height: 1.6; /* (변경) 줄 간격 조정 1.45 -> 1.6 */
  }

  .playbook-section,
  .administration-section,
  .fundamentals-section {
    margin-top: 40px; /* (변경) 간격 증가 28px -> 40px */
  }

  .cards {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 20px;
    margin-top: 20px;
  }

  .card {
    padding: 20px;
    border-radius: 12px;
    background: var(--card-bg);
    border: 1px solid var(--border-color);
    box-shadow: var(--shadow);
    display: flex;
    flex-direction: column;
    justify-content: space-between;
  }

  .card h3 {
    margin-top: 0;
    font-size: 1.1rem;
    color: var(--text);
  }

  .card p {
    color: var(--sub);
    line-height: 1.6;
    flex-grow: 1;
  }

  .card a {
    margin-top: 15px;
    display: inline-block;
    color: var(--accent);
    font-weight: 600;
  }

  @media (max-width: 768px) {
    .site-content {
      width: calc(100% - 28px);
      padding-top: 28px;
    }

    .summary-section {
      padding: 0px 0px 4px; /* (변경) 모바일 패딩 조정 */
    }

    .mode-card {
      border-radius: 40px; /* (변경) 모바일 곡률 조정 */
      border-width: 4px; /* (변경) 모바일 테두리 두께 조정 */
    }
  }
</style>

<div class="hero-container">
  
  <div class="hero-content">
    <span class="eyebrow" style="font-weight: bold;">Analytics Platform</span>
    <h1>데이터의 한계를 넘어서, <br> Vertica로 실현하는 초고속 분석</h1>
    <p>Vertica의 차세대 레이크하우스 아키텍처와 고성능 분석 노하우를 집약한 기술 블로그입니다.</p>
    <div class="link-container">
    <a href="https://docs.vertica.com/26.1.x/en/" target="_blank">
        Vertica 공식 문서 (Documentation)
    </a>
    <a href="http://www.gtgsc.com/gtg/sub/bigdata/vertica.php" target="_blank">
        Vertica 공식 파트너사 (GTG)
    </a>
    </div>
  </div>
</div>

<section class="summary-section">
  <div class="summary-inner">
    <h2>Vertica는 무엇인가?</h2>
    <p>Vertica는 대규모 데이터 분석에 최적화된 SQL 데이터 웨어하우스 플랫폼입니다. <br>
    빠른 쿼리 처리, 고급 분석 기능, 클라우드 및 온프레미스 확장성을 모두 제공하여 기업용 데이터 분석 환경을 단순화합니다.</p>
  </div>
  
  <div class="architecture-grid">
    
    <article class="mode-card">
      <div>
        <img src="{{ '/image/EnteroriseMode.png' | relative_url }}" alt="Vertica Enterprise Mode" />
      </div>
      <div>
        <h3>Enterprise Mode</h3>
        <p>전통적인 분산형 아키텍처로 계산과 저장이 동일한 노드에 배치됩니다. 고성능 및 낮은 지연 시간 분석이 필요한 환경에서 적합하며, 전체 시스템을 하나의 클러스터로 관리합니다.</p>
      </div>
    </article>

    <article class="mode-card">
      <div>
        <img src="{{ '/image/EonMode.png' | relative_url }}" alt="Vertica Eon Mode" />
      </div>
      <div>
        <h3>Eon Mode</h3>
        <p>스토리지와 컴퓨팅을 분리하여 비용 효율성과 확장성을 강화한 아키텍처입니다. Object Storage 기반으로 데이터를 중앙에 저장하고, 필요에 따라 컴퓨팅 리소스를 유연하게 확장할 수 있습니다.</p>
      </div>
    </article>
    
  </div>
</section>


<section id="playbook" class="playbook-section">
  <h2>Vertica Playbook</h2>
  <div class="cards">
    <article class="card">
      <h3>Vertica란 무엇인가</h3>
      <p>Vertica의 개념과 강점을 한 번에 확인할 수 있습니다.</p>
      <a href="{{ '/what-is-vertica' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Vertica 핵심 기술</h3>
      <p>Native Columnar, Compression, MPP 등 주요 기술 요소를 정리했습니다.</p>
      <a href="{{ '/technology' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Vertica 포트폴리오</h3>
      <p>온프레미스, 클라우드, Eon 구조까지 Vertica 제품군을 소개합니다.</p>
      <a href="{{ '/portfolio' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>아키텍처 장점</h3>
      <p>Pure-MPP, Columnar Storage, 압축, 무중단 운영 등 핵심 장점을 정리했습니다.</p>
      <a href="{{ '/architecture' | relative_url }}">이동 →</a>
    </article>
  </div>
</section>

<section id="Administration" class="administration-section">
  <h2>Vertica Administration</h2>
  <div class="cards">
    <article class="card">
      <h3>Table · Projection</h3>
      <p>Vertica의 핵심 객체인 Table과 Projection의 개념 및 관리 방법을 설명합니다.</p>
      <a href="{{ '/administration-table-projection' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Schema</h3>
      <p>데이터 객체 그룹화 및 권한 관리를 위한 Schema의 생성 및 운영 방법을 다룹니다.</p>
      <a href="{{ '/administration-schema' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>User</h3>
      <p>데이터베이스 사용자 계정 생성, 권한 부여, 관리 팁을 제공합니다.</p>
      <a href="{{ '/administration-user' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Profile</h3>
      <p>사용자 세션의 실행 환경과 자원 제한을 정의하는 Profile에 대해 알아봅니다.</p>
      <a href="{{ '/administration-profile' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Resource Pools</h3>
      <p>쿼리 메모리 및 동시 실행 정책을 제어하는 Resource Pool의 구성 및 할당 방법을 설명합니다.</p>
      <a href="{{ '/administration-resource-pool' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Privilege</h3>
      <p>리소스 접근 및 실행 정책을 통제하는 Vertica 권한 관리 체계를 정리합니다.</p>
      <a href="{{ '/administration-privilege' | relative_url }}">이동 →</a>
    </article>
  </div>
</section>

<section id="Fundamentals" class="fundamentals-section">
  <h2>Vertica Fundamentals</h2>
  <div class="cards">
    <article class="card">
      <h3>Vertica 학습하기</h3>
      <p>Vertica의 기본 개념부터 운영, 쿼리, 백업, 적재까지 28가지 학습 항목을 소개합니다.</p>
      <a href="{{ '/what-is-vertica' | relative_url }}">이동 →</a>
    </article>
  </div>
</section>