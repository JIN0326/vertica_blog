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

<section class="summary-section" style="padding: 26px 20px; background-color: #f9f9f9; border-radius: 8px; margin-top: 24px;">
  <div class="summary-inner">
    <h2>Vertica는 무엇인가?</h2>
    <p>Vertica는 대규모 데이터 분석에 최적화된 SQL 데이터 웨어하우스 플랫폼입니다. 빠른 쿼리 처리, 고급 분석 기능, 클라우드 및 온프레미스 확장성을 모두 제공하여 기업용 데이터 분석 환경을 단순화합니다.</p>
  </div>
  
  <div class="architecture-grid" style="display: flex; gap: 20px; flex-wrap: wrap;">
    
    <article class="mode-card" style="flex: 1; min-width: 300px; border: 1px solid #eee; padding: 25px; border-radius: 12px; box-shadow: 0 2px 5px rgba(0,0,0,0.05); display: flex; flex-direction: column;">
      <div style="width: 100%; height: 280px; display: flex; align-items: center; justify-content: center; margin-bottom: 15px; overflow: hidden;">
        <img src="{{ '/image/EnteroriseMode.png' | relative_url }}" alt="Vertica Enterprise Mode" 
             style="width: 100%; height: 100%; object-fit: contain;" />
      </div>
      <div>
        <h3>Enterprise Mode</h3>
        <p>전통적인 분산형 아키텍처로 계산과 저장이 동일한 노드에 배치됩니다. 고성능 및 낮은 지연 시간 분석이 필요한 환경에서 적합하며, 전체 시스템을 하나의 클러스터로 관리합니다.</p>
      </div>
    </article>

    <article class="mode-card" style="flex: 1; min-width: 300px; border: 1px solid #eee; padding: 25px; border-radius: 12px; box-shadow: 0 2px 5px rgba(0,0,0,0.05); display: flex; flex-direction: column;">
      <div style="width: 100%; height: 280px; display: flex; align-items: center; justify-content: center; margin-bottom: 15px; overflow: hidden;">
        <img src="{{ '/image/EonMode.png' | relative_url }}" alt="Vertica Eon Mode" 
             style="width: 100%; height: 100%; object-fit: contain;" />
      </div>
      <div>
        <h3>Eon Mode</h3>
        <p>스토리지와 컴퓨팅을 분리하여 비용 효율성과 확장성을 강화한 아키텍처입니다. Object Storage 기반으로 데이터를 중앙에 저장하고, 필요에 따라 컴퓨팅 리소스를 유연하게 확장할 수 있습니다.</p>
      </div>
    </article>
    
  </div>
</section>


<section id="playbook" class="playbook-section" style="margin-top: 28px;">
  <h2>Vertica Playbook</h2>
  <div class="cards">
    <article class="card">
      <h3>Vertica Playbook</h3>
      <p>Vertica 핵심 페이지를 한 곳에서 확인합니다.</p>
      <a href="{{ '/playbook' | relative_url }}">이동 →</a>
    </article>
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

<section id="Administration" class="administration-section" style="margin-top: 28px;">
  <h2>Vertica Administration</h2>
  <div class="cards">
    <article class="card">
      <h3>Table · Projection</h3>
      <p> xx </p>
      <a href="{{ '/administration-table-projection' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Schema</h3>
      <p> xx </p>
      <a href="{{ '/administration-schema' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>User</h3>
      <p> xx </p>
      <a href="{{ '/administration-user' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Profile</h3>
      <p>xx </p>
      <a href="{{ '/administration-profile' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Resource Pools</h3>
      <p>xx </p>
      <a href="{{ '/administration-resource-pool' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Privilege</h3>
      <p>xx </p>
      <a href="{{ '/administration-privilege' | relative_url }}">이동 →</a>
    </article>
  </div>
</section>

<section id="Fundamentals" class="fundamentals-section" style="margin-top: 28px;">
  <h2>Vertica Fundamentals</h2>
  <div class="cards">
    <article class="card">
      <h3>Vertica 학습하기</h3>
      <p> xx </p>
      <a href="{{ '/what-is-vertica' | relative_url }}">이동 →</a>
    </article>
</section>
