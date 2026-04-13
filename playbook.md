---
title: Vertica Playbook
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

  .page-hero {
    text-align: center;
    padding: 2rem 1rem;
  }

  .chip {
    display: inline-block;
    padding: 0.4rem 0.85rem;
    border-radius: 999px;
    background: rgba(45, 105, 255, 0.08);
    color: var(--accent);
    font-size: 0.84rem;
    letter-spacing: 0.02em;
    margin-bottom: 1rem;
  }

  .page-hero h1 {
    margin: 0 auto;
    font-size: clamp(2rem, 4.2vw, 3.4rem);
    line-height: 1.2;
    font-weight: 800;
    color: var(--text);
  }

  .page-hero p {
    margin: 1rem auto 0;
    max-width: 680px;
    color: var(--sub);
    font-size: clamp(1rem, 2vw, 1.15rem);
    line-height: 1.7;
  }

  .button-grid-section {
    margin-top: 28px;
    padding: 26px;
    background: var(--card-bg);
    border-radius: 24px;
    border: 1px solid var(--border-color);
    box-shadow: var(--shadow);
  }

  .button-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 18px;
  }

  .button {
    display: block;
    padding: 14px 20px;
    border-radius: 12px;
    text-align: center;
    font-weight: bold;
    text-decoration: none;
    transition: transform 0.2s ease;
  }
  .button:hover {
    transform: translateY(-2px);
  }

  .button.primary {
    background-color: var(--accent);
    color: white;
  }

  .page-layout {
    display: grid;
    grid-template-columns: minmax(0, 1fr) 280px;
    gap: 28px;
    margin-top: 32px;
  }

  .content-section {
    scroll-margin-top: 100px; /* Offset for sticky header */
    margin-bottom: 2rem;
    padding: 2rem;
    background: var(--card-bg);
    border-radius: 24px;
    border: 1px solid var(--border-color);
    box-shadow: var(--shadow);
  }
  .content-section h2 {
    margin-top: 0;
  }

  .page-sidebar {
    position: sticky;
    top: 104px;
    align-self: start;
  }

  .sidebar-panel {
    padding: 24px;
    border-radius: 24px;
    background: var(--card-bg);
    border: 1px solid var(--border-color);
    box-shadow: var(--shadow);
  }

  .sidebar-panel h3 {
    margin-top: 0;
    font-size: 1.05rem;
    color: var(--text);
  }

  .sidebar-panel ul {
    list-style: none;
    margin: 0;
    padding: 0;
    display: grid;
    gap: 12px;
  }

  .sidebar-panel a {
    color: var(--accent);
    font-weight: 600;
    text-decoration: none;
  }

  @media (max-width: 900px) {
    .page-layout {
      grid-template-columns: 1fr;
    }
    .page-sidebar {
      position: static;
      top: auto;
    }
  }
</style>

<div class="page-hero">
  <span class="chip">Vertica Playbook</span>
  <h1>Vertica 핵심 개념과 사용 가이드</h1>
  <p>Vertica의 주요 기술 요소와 아키텍처, 포트폴리오를 한 곳에서 살펴보고 관련 페이지로 빠르게 이동할 수 있습니다.</p>
</div>

<section class="button-grid-section">
  <div class="button-grid">
    <a class="button secondary" href="{{ '/what-is-vertica' | relative_url }}">Vertica란 무엇인가</a>
    <a class="button secondary" href="{{ '/technology' | relative_url }}">Vertica 핵심 기술</a>
    <a class="button secondary" href="{{ '/portfolio' | relative_url }}">Vertica 포트폴리오</a>
    <a class="button secondary" href="{{ '/architecture' | relative_url }}">Vertica 아키텍처</a>
  </div>
</section>

<div class="page-layout">
  <main>
    <article id="what-is-vertica" class="content-section">
      <h2>Vertica란 무엇인가?</h2>
      <p>Vertica는 대용량 분석을 위한 컬럼 기반 MPP 데이터베이스입니다. 고급 압축과 분산처리를 통해 빠른 분석 쿼리 성능을 제공합니다.</p>
      <p>이 문서에서는 Vertica의 기본 개념과 아키텍처, 구축 모델을 빠르게 정리합니다.</p>
    </article>

    <article id="technology" class="content-section">
      <h2>Vertica 핵심 기술</h2>
      <p>Vertica는 Native Columnar Storage, Aggressive Compression, Massively Parallel Processing(MPP)을 중심으로 설계되었습니다.</p>
      <p>이러한 기술은 대규모 데이터셋에서도 높은 쿼리 성능과 효율적인 저장소 활용을 가능하게 합니다.</p>
    </article>

    <article id="portfolio" class="content-section">
      <h2>Vertica 포트폴리오</h2>
      <p>Vertica는 온프레미스와 클라우드, Eon 모드를 포함하는 다양한 배포 모델을 제공합니다.</p>
      <p>각 환경에 맞는 유연한 아키텍처 디자인으로 실무 환경에 적합한 확장성과 관리 편의성을 제공합니다.</p>
    </article>

    <article id="architecture" class="content-section">
      <h2>Vertica 아키텍처</h2>
      <p>Vertica는 Enterprise 모드와 Eon 모드로 나뉘며, 각각 계산과 저장소의 결합 또는 분리를 통해 성능과 확장성을 다르게 설계합니다.</p>
      <p>클러스터 노드 구성과 Storage 구조, 네트워크 요구 사항을 함께 이해하는 것이 중요합니다.</p>
    </article>
  </main>

  <aside class="page-sidebar">
    <div class="sidebar-panel">
      <h3>Playbook 목차</h3>
      <ul>
        <li><a href="#what-is-vertica">Vertica란 무엇인가</a></li>
        <li><a href="#technology">Vertica 핵심 기술</a></li>
        <li><a href="#portfolio">Vertica 포트폴리오</a></li>
        <li><a href="#architecture">Vertica 아키텍처</a></li>
      </ul>
    </div>
  </aside>
</div>
