---
title: Vertica Blog Main
layout: default
---

<section class="top-links-section">
  <div class="top-links">
    <article class="top-link-card">
      <h3>Vertica Playbook</h3>
      <p>Intro, Technology, Portfolio, Architecture를 하나로 묶은 실무 가이드입니다.</p>
      <a href="#playbook">플레이북 보기 →</a>
    </article>
    <article class="top-link-card">
      <h3>Vertica Administration</h3>
      <p>클러스터 운영과 관리, 모니터링을 중심으로 한 운영 가이드입니다.</p>
      <a href="#administration">관리 가이드 →</a>
    </article>
    <article class="top-link-card">
      <h3>Vertica Fundamentals</h3>
      <p>기본 개념과 핵심 원리를 빠르게 정리한 학습형 가이드입니다.</p>
      <a href="#fundamentals">Fundamentals →</a>
    </article>
  </div>
</section>

<section class="hero-banner">
  <div class="hero-copy">
    <span class="eyebrow">Analytics Platform</span>
    <h1>데이터의 한계를 넘어서,<br>Vertica로 실현하는 초고속 분석</h1>
    <p>Vertica는 대규모 데이터 분석에 최적화된 SQL 데이터 웨어하우스 플랫폼입니다. 고속 쿼리 처리, 유연한 확장성, 그리고 엔터프라이즈급 안정성을 제공합니다.</p>
    <div class="hero-actions">
      <a class="button primary" href="{{ '/what-is-vertica' | relative_url }}">Vertica 소개</a>
      <a class="button secondary" href="{{ '/technology' | relative_url }}">핵심 기술 보기</a>
    </div>
    <div class="hero-badges">
      <span>Enterprise Mode</span>
      <span>Eon Mode</span>
    </div>
  </div>
  <div class="hero-media">
    <img src="{{ '/image/EonMode.png' | relative_url }}" alt="Vertica Eon Mode" />
  </div>
</section>

<section id="administration" class="summary-section">
  <div class="summary-inner">
    <h2>Vertica는 무엇인가?</h2>
    <p>Vertica는 대규모 데이터 분석에 최적화된 SQL 데이터 웨어하우스 플랫폼입니다. 빠른 쿼리 처리, 고급 분석 기능, 클라우드 및 온프레미스 확장성을 모두 제공하여 기업용 데이터 분석 환경을 단순화합니다.</p>
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

<section class="architecture-section">
  <h2>Vertica 아키텍처 - Enterprise | Eon Mode</h2>
  <div class="architecture-grid">
    <article class="mode-card">
      <img src="{{ '/image/EnteroriseMode.png' | relative_url }}" alt="Vertica Enterprise Mode" />
      <div>
        <h3>Enterprise Mode</h3>
        <p>계산과 저장이 동일한 노드에 배치된 전통적인 클러스터 아키텍처입니다. 낮은 지연 시간과 고성능 분석이 필요한 환경에 적합합니다.</p>
      </div>
    </article>
    <article class="mode-card">
      <img src="{{ '/image/EonMode.png' | relative_url }}" alt="Vertica Eon Mode" />
      <div>
        <h3>Eon Mode</h3>
        <p>컴퓨팅과 스토리지를 분리하여 비용 효율성을 높인 아키텍처입니다. Object Storage 기반으로 데이터를 중앙에 저장하고, 필요에 따라 컴퓨팅을 유연하게 확장할 수 있습니다.</p>
      </div>
    </article>
  </div>
</section>

<section id="fundamentals" class="overview-section">
  <h2 class="section-title">이 블로그에서 확인할 수 있는 내용</h2>
  <ul class="feature-list">
    <li>Vertica의 데이터 분석 플랫폼 개요</li>
    <li>대용량 분석에 최적화된 아키텍처 설명</li>
    <li>클라우드와 온프레미스 환경 지원 전략</li>
    <li>데이터 무중단 운영, 백업/복구, 확장성 사례</li>
  </ul>
</section>
