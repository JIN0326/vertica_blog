---
title: "Vertica 핵심 기술: Columnar, 압축, Projections, 분산 쿼리 엔진 - Vertica Blog"
layout: default
description: "Vertica의 6가지 핵심 기술 요소인 Native Columnar Storage, 데이터 압축, Projections, 분산 쿼리 엔진, MCP 연동, MPP Scale-out에 대해 알아봅니다."
keywords: "columnar storage, data compression, projections, distributed query engine, mcp, mpp scale-out"
canonical_url: "https://jin0326.github.io/vertica_blog/100playbook/100playbook#technology"
---

<div id="technology" style="scroll-margin-top: 100px; margin-top: 60px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody">
## Vertica 핵심 기술

Vertica는 다음 6가지 기술 요소를 중심으로 고성능 분석을 지원합니다.

<div class="tech-features-grid">
  <article class="tech-feature-card">
    <img src="/vertica_blog/assets/images/Vertica5tech_1.png" alt="Columnar Storage" >
    <h3>Native Columnar Storage</h3>
    <ul>
      <li>필요한 컬럼만을 조회하여 빠른 쿼리 성능을 보장합니다.</li>
    </ul>
  </article>

  <article class="tech-feature-card">
    <img src="/vertica_blog/assets/images/Vertica5tech_2.png" alt="Data Compression" >
    <h3>Compression/Encoding</h3>
    <ul>
      <li> Disk I/O 비용을 최소화하는 동시에 성능을 가속화합니다.</li>
    </ul>
  </article>

  <article class="tech-feature-card">
    <img src="/vertica_blog/assets/images/Vertica5tech_3.png" alt="Projections" >
    <h3>Projections</h3>
    <ul>
      <li>노드 장애 대처와 쿼리 성능을 담보하기 위한 최적화 방안 제공합니다.</li>
    </ul>
  </article>

  <article class="tech-feature-card">
    <img src="/vertica_blog/assets/images/Vertica5tech_4.png" alt="Distributed Query Engine" >
    <h3>Distributed Query Engine</h3>
    <ul>
      <li>대규모 클러스터의 모든 노드에 쿼리를 병렬로 분산시켜 대용량 데이터를 고속으로 처리합니다.</li>
    </ul>
  </article>

  <article class="tech-feature-card">
    <img src="/vertica_blog/assets/images/Vertica5tech_5.png" alt="MCP Intergration" >
    <h3>MCP Intergration</h3>
    <ul>
      <li>MCP 기반 자연어 질의 지원으로 SQL에 익숙하지 않은 사용자도 데이터 분석가능 가능합니다.</li>
    </ul>
  </article>

  <article class="tech-feature-card">
    <img src="/vertica_blog/assets/images/Vertica5tech_6.png" alt="MPP Scale-out" >
    <h3>MPP Scale-out</h3>
    <ul>
      <li>Name node와 같은 Single point of failure를 제거한 순수 MPP 아키텍처입니다.(Massively Parallel Processing)</li>
    </ul>
  </article>
</div>
</div>
</div>