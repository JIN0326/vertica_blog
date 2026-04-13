---
title: Vertica Blog Main
layout: default
---

<div class="hero-container" style="display: flex; flex-wrap: wrap; gap: 40px; align-items: flex-start; justify-content: space-between; padding: 20px;">
  
  <div class="hero-content" style="flex: 1; min-width: 300px;">
    <span class="eyebrow">Analytics Platform</span>
    <h1>데이터의 한계를 넘어서, <br> Vertica로 실현하는 초고속 분석</h1>
    <p>Vertica의 차세대 레이크하우스 아키텍처와 고성능 분석 노하우를 집약한 기술 블로그입니다.</p>
    <div class="link-container" style="display: flex; gap: 15px; margin-top: 25px;">
    <a href="https://docs.vertica.com/26.1.x/en/" target="_blank" style="padding: 12px 24px; background-color: #0073e6; color: white; border-radius: 6px; text-decoration: none; font-weight: bold; font-size: 0.9rem;">
        Vertica 공식 문서 (Documentation)
    </a>
    <a href="http://www.gtgsc.com/gtg/sub/bigdata/vertica.php" target="_blank" style="padding: 12px 24px; background-color: #0073e6; color: white;  border-radius: 6px; text-decoration: none; font-weight: bold; font-size: 0.9rem;">
        Vertica 공식 파트너사 (GTG)
    </a>
    </div>
  </div>
</div>

<section class="summary-section" style="padding: 40px 20px; background-color: #f9f9f9; border-radius: 8px;">
  <div class="summary-inner">
    <h2>Vertica는 무엇인가?</h2>
    <p>Vertica는 대규모 데이터 분석에 최적화된 SQL 데이터 웨어하우스 플랫폼입니다. 빠른 쿼리 처리, 고급 분석 기능, 클라우드 및 온프레미스 확장성을 모두 제공하여 기업용 데이터 분석 환경을 단순화합니다.</p>
  </div>
</section>

<section class="architecture-section" style="padding: 40px 20px;">
  <h2 style="margin-bottom: 20px;">Vertica 아키텍처 - Enterprise | Eon Mode</h2>
  <div class="architecture-grid" style="display: flex; gap: 20px; flex-wrap: wrap;">
    
    <article class="mode-card" style="flex: 1; min-width: 300px; border: 1px solid #eee; padding: 25px; border-radius: 12px; box-shadow: 0 2px 5px rgba(0,0,0,0.05);">
      <img src="{{ '/image/EnteroriseMode.png' | relative_url }}" alt="Vertica Enterprise Mode" style="width: 100%; margin-bottom: 15px;"/>
      <div>
        <h3>Enterprise Mode</h3>
        <p>전통적인 분산형 아키텍처로 계산과 저장이 동일한 노드에 배치됩니다. 고성능 및 낮은 지연 시간 분석이 필요한 환경에서 적합하며, 전체 시스템을 하나의 클러스터로 관리합니다.</p>
      </div>
    </article>

    <article class="mode-card" style="flex: 1; min-width: 300px; border: 1px solid #eee; padding: 25px; border-radius: 12px; box-shadow: 0 2px 5px rgba(0,0,0,0.05);">
      <img src="{{ '/image/EonMode.png' | relative_url }}" alt="Vertica Eon Mode" style="width: 100%; margin-bottom: 15px;"/>
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

<section style="margin-top: 48px; padding: 30px; background-color: #f0f4f8; border-radius: 10px;">
  <h2 class="section-title">이 블로그에서 확인할 수 있는 내용</h2>
  <ul class="feature-list" style="line-height: 1.8;">
    <li>Vertica의 데이터 분석 플랫폼 개요</li>
    <li>대용량 분석에 최적화된 아키텍처 설명</li>
    <li>클라우드와 온프레미스 환경 지원 전략</li>
    <li>데이터 무중단 운영, 백업/복구, 확장성 사례</li>
  </ul>
</section>