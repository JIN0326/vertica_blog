---
title: Vertica Blog Main
layout: default
---

<div class="hero-container">
  <div class="hero-content">
    <span class="eyebrow">Analytics Platform</span>
    <h1>데이터의 한계를 넘어서,<br>Vertica로 실현하는 초고속 분석</h1>
    <p>Vertica의 차세대 레이크하우스 아키텍처와 고성능 분석 노하우를 집약한 기술 블로그입니다.</p>
    <div class="link-container">
      <a href="https://docs.vertica.com/26.1.x/en/" target="_blank">Vertica 공식 문서 (Documentation)</a>
      <a href="http://www.gtgsc.com/gtg/sub/bigdata/vertica.php" target="_blank">Vertica 공식 파트너사 (GTG)</a>
    </div>
  </div>
</div>

<section class="summary-section">
  <div class="summary-header">
    <h2>Vertica는 무엇인가?</h2>
    <p>Vertica는 대규모 데이터 분석에 최적화된 SQL 데이터 웨어하우스 플랫폼입니다.<br>
    빠른 쿼리 처리, 고급 분석 기능, 클라우드 및 온프레미스 확장성을 모두 제공하여 기업용 데이터 분석 환경을 단순화합니다.</p>
  </div>

  <div class="architecture-grid">
    <article class="mode-card">
      <div class="mode-image">
        <img src="{{ '/assets/images/EnteroriseMode.png' | relative_url }}" alt="Vertica Enterprise Mode" />
      </div>
      <div class="mode-body">
        <h3>Enterprise Mode</h3>
        <p>전통적인 분산형 아키텍처로 계산과 저장이 동일한 노드에 배치됩니다. 고성능 및 낮은 지연 시간 분석이 필요한 환경에서 적합하며, 전체 시스템을 하나의 클러스터로 관리합니다.</p>
      </div>
    </article>

    <article class="mode-card">
      <div class="mode-image">
        <img src="{{ '/assets/images/EonMode.png' | relative_url }}" alt="Vertica Eon Mode" />
      </div>
      <div class="mode-body">
        <h3>Eon Mode</h3>
        <p>스토리지와 컴퓨팅을 분리하여 비용 효율성과 확장성을 강화한 아키텍처입니다. Object Storage 기반으로 데이터를 중앙에 저장하고, 필요에 따라 컴퓨팅 리소스를 유연하게 확장할 수 있습니다.</p>
      </div>
    </article>
  </div>
</section>