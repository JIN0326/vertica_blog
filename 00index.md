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
      <p>Vertica의 개념과 강점, 데이터 레이크하우스 아키텍처를 소개합니다.</p>
      <a href="{{ '/01playbook#what-is-vertica' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Vertica 핵심 기술</h3>
      <p>Native Columnar, 압축, MPP 등 Vertica의 5가지 핵심 기술 요소를 알아봅니다.</p>
      <a href="{{ '/01playbook#technology' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Vertica 아키텍처</h3>
      <p>Pure-MPP, 데이터 이중화, Scale-out 등 효율적인 아키텍처의 특징을 설명합니다.</p>
      <a href="{{ '/01playbook#architecture' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>고급 개념</h3>
      <p>쿼리 실행 워크플로우와 데이터 일관성을 보장하는 Epoch의 동작 원리를 알아봅니다.</p>
      <a href="{{ '/01playbook#advanced' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>운영과 개발 편의성</h3>
      <p>무중단 서비스, 빠른 백업/복구, SQL 기반 분석 등 운영 및 개발 편의성을 알아봅니다.</p>
      <a href="{{ '/01playbook#operation' | relative_url }}">이동 →</a>
    </article>
  </div>
</section>

<section id="Administration" class="administration-section">
  <h2>Vertica Administration</h2>
  <div class="cards">
    <article class="card">
      <h3>Table · Projection</h3>
      <p>Vertica의 핵심 객체인 Table과 Projection의 개념 및 관리 방법을 설명합니다.</p>
      <a href="{{ '/02administration#table-projection' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Schema & User</h3>
      <p>객체를 그룹화하는 Schema와 데이터베이스 접근 주체인 User를 관리합니다.</p>
      <a href="{{ '/02administration#schema' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Profile & Resource Pool</h3>
      <p>사용자 세션 환경과 쿼리 자원 할당을 제어하는 방법을 알아봅니다.</p>
      <a href="{{ '/02administration#profile' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Privilege & Backup</h3>
      <p>객체 접근 권한을 관리하고, vbr을 이용한 데이터 백업/복구 방법을 다룹니다.</p>
      <a href="{{ '/02administration#privilege' | relative_url }}">이동 →</a>
    </article>
  </div>
</section>

<section id="coretech" class="fundamentals-section">
  <h2>Vertica CoreTech</h2>
  <div class="cards">
    <article class="card">
      <h3>데이터 적재 (COPY)</h3>
      <p>벌크 데이터 로딩을 위한 COPY 명령어의 사용법과 주요 옵션을 알아봅니다.</p>
      <a href="{{ '/03coretech#copy' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>vsql 명령어</h3>
      <p>Vertica의 CLI 클라이언트인 vsql의 접속 방법과 유용한 메타 명령어를 학습합니다.</p>
      <a href="{{ '/03coretech#vsql' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>쿼리 튜닝 (Explain)</h3>
      <p>EXPLAIN으로 실행 계획을 분석하고 쿼리 성능을 최적화하는 방법을 다룹니다.</p>
      <a href="{{ '/03coretech#tuning' | relative_url }}">이동 →</a>
    </article>
  </div>
</section>

<section id="Utilization" class="fundamentals-section">
  <h2>Vertica Utilization</h2>
  <div class="cards">
    <article class="card">
      <h3>클라이언트 접속</h3>
      <p>vsql, DBeaver 등 다양한 클라이언트 도구를 사용하여 Vertica에 접속하는 방법을 알아봅니다.</p>
      <a href="{{ '/04utilization#connecting' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>모니터링 및 Grafana</h3>
      <p>시스템 테이블과 Grafana를 활용하여 모니터링 대시보드를 구축하는 방법을 소개합니다.</p>
      <a href="{{ '/04utilization#monitoring' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Python 연동</h3>
      <p>vertica-python 라이브러리를 사용하여 Python 환경에서 데이터를 처리하고 분석합니다.</p>
      <a href="{{ '/04utilization#python' | relative_url }}">이동 →</a>
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
