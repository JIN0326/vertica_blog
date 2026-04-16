---
title: Vertica Blog Main
layout: default
permalink: /
---

<div class="hero-container">
  <div class="hero-content">
    <span class="eyebrow">Analytics Database</span>
    <h1>데이터의 한계를 넘어서, <br> Vertica로 실현하는 초고속 분석</h1>
    <p>Vertica는 대규모 데이터 분석에 최적화된 SQL 데이터 웨어하우스 플랫폼입니다. <br>
    페타바이트 규모의 복잡한 데이터 워크로드를 처리하여 기업이 데이터를 관리하고 분석하는 방식을 혁신하도록 설계된 Analytics Database 입니다.</p>
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
</div>

<section id="playbook" class="playbook-section">
  <h2>Vertica Playbook</h2>
  <div class="cards">
    <article class="card">
      <h3>Vertica란 무엇인가</h3>
      <p>Vertica의 개념과 강점을 한 번에 확인할 수 있습니다.</p>
      <a href="{{ '/01playbook' | relative_url }}">이동 →</a>
    </article>
  </div>
</section>

<section id="Administration" class="administration-section">
  <h2>Vertica Administration</h2>
  <div class="cards">
    <article class="card">
      <h3>Table · Projection</h3>
      <p>Vertica의 핵심 객체인 Table과 Projection의 개념 및 관리 방법을 설명합니다.</p>
      <a href="{{ '/02administration' | relative_url }}">이동 →</a>
    </article>
  </div>
</section>

<section id="coretech" class="fundamentals-section">
  <h2>Vertica CoreTech</h2>
  <div class="cards">
    <article class="card">
      <h3>Vertica CoreTech</h3>
      <p>데이터 적재, vsql, 쿼리 튜닝, 복구 등 Vertica의 핵심 기술을 알아봅니다.</p>
      <a href="{{ '/03coretech' | relative_url }}">이동 →</a>
    </article>
  </div>
</section>

<section id="Utilization" class="fundamentals-section">
  <h2>Vertica Utilization</h2>
  <div class="cards">
    <article class="card">
      <h3>Vertica Utilization</h3>
      <p>클라이언트 접속, 모니터링, Python 연동 등 Vertica를 실제 환경에서 활용하는 방법을 소개합니다.</p>
      <a href="{{ '/04utilization' | relative_url }}">이동 →</a>
    </article>
  </div>
</section>

<section id="Fundamentals" class="fundamentals-section">
  <h2>Vertica Fundamentals</h2>
  <div class="cards">
    <article class="card">
      <h3>Vertica 학습하기</h3>
      <p>Vertica의 기본 개념부터 운영, 쿼리, 백업, 적재까지 28가지 핵심 학습 항목을 소개합니다.</p>
      <a href="{{ '/90fundamentals' | relative_url }}">이동 →</a>
    </article>
  </div>
</section>
