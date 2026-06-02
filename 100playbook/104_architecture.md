---
title: "Vertica 아키텍처: Pure-MPP, Native Columnar 구조 심층 분석 - Vertica Blog"
layout: default
description: "Vertica의 단순하고 효율적인 아키텍처를 알아봅니다. 별도의 마스터 노드가 없는 Pure-MPP 구조와 I/O를 획기적으로 줄이는 Native Columnar 저장 방식의 강점을 분석합니다."
keywords: "vertica architecture, pure-mpp, mpp, native columnar, columnar"
---

<div id="architecture" style="scroll-margin-top: 100px;"></div>

## Vertica 아키텍처

<div class="architecture-section">
  <p class="section-description">
    Vertica는 단순하고 효율적인 아키텍처로 대규모 데이터 분석을 지원합니다.
  </p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">
      Pure-MPP (Massively Parallel Processing)
    </h3>
    <p class="section-description">
      Vertica는 별도의 마스터 노드를 분리하지 않고 모든 노드가 동일한 역할을 수행하는 pure-MPP 구조입니다.
      또한 클러스터 구성이나 노드 구성에 특별한 하드웨어나 소프트웨어를 필요로 하지 않기 때문에 비용적인 강점이 있으며,
      복잡한 구성이 불필요하여 클러스터 구성 과정이 매우 간단하고 빠릅니다.
    </p>
    
  <div class="comparison-grid">
    <div class="comparison-column">
      <div class="card card--other">
        <img src="/vertica_blog/assets/images/architecture1.png" alt="아키텍처 비교: Other Systems">
        <ul>
          <li>두 종류 이상의 서버</li>
          <li>특별한 H/W 사용으로 복잡도 증가</li>
          <li>마스터 서버를 통한 작업 수행</li>
          <li>저가형 서버 사용</li>
          <li>관리/운영에 다양한 고려사항 존재</li>
        </ul>
      </div>
    </div>
    <div class="comparison-column">
      <div class="card card--vertica">
        <img src="/vertica_blog/assets/images/architecture2.png" alt="아키텍처 비교: Vertica">
        <ul>
          <li>동일 스펙 / 동일 구성의 서버</li>
          <li>단순한 구성</li>
          <li>아무 서버에나 작업을 요청하면 전 노드가 병렬 수행</li>
          <li>시장의 신뢰도를 확보한 서버 사용</li>
          <li>관리 및 사용 용이성 확보</li>
        </ul>
      </div>
    </div>
  </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">
      Native Columnar
    </h3>
    <p class="section-description">
      대용량 데이터를 관리하는 DW 데이터베이스의 성능은 I/O를 얼마나 줄일 수 있느냐에 달려 있습니다.
      Vertica의 Columnar DBMS 아키텍처는 Query 수행에 필요한 Column 만을 읽어 올 수 있도록 설계되었기 때문에,
      Query 시 마다 모든 열을 읽어와야 하는 row 기반 DBMS와 비교하여 I/O 발생량을 획기적으로 감소시킬 수 있습니다.
    </p>

  <div class="columnar-layout">
      <div class="columnar-image">
        <img src="/vertica_blog/assets/images/architecture3.png" alt="Native Columnar Architecture">
      </div>
      <ul class="feature-list">
        <li><span class="feature-list__icon">🔹</span> <span>압축과 질의가 물리적인 I/O 레벨부터 컬럼 단위로 처리</span></li>
        <li><span class="feature-list__icon">🔹</span> <span>컬럼 기반 저장 기술에 맞는 쿼리 옵티마이저</span></li>
        <li><span class="feature-list__icon">🔹</span> <span>컬럼 단위 저장, 처리를 위한 별도의 옵션이나 절차가 불필요</span></li>
        <li><span class="feature-list__icon">🔹</span> <span>컬럼 저장 구조에 최적화된 데이터 적재와 트랜잭션 처리</span></li>
        <li><span class="feature-list__icon">🔹</span> <span>적은 하드웨어 리소스로 다른 DBMS와 동일한 작업 수행</span></li>
      </ul>
    </div>
  </div>
</div>
