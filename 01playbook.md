---
title: Vertica Playbook
layout: default
---

<div class="page-hero">
  <span class="chip">Playbook</span>
  <h1>Vertica Playbook</h1>
  <p>Vertica의 핵심 개념, 강점, 아키텍처, 운영 편의성 등 필수 정보를 한곳에 모아 정리했습니다. 아래 목차를 통해 원하는 섹션으로 바로 이동할 수 있습니다.</p>
</div>

<div class="page-layout">
  <div class="content-section" markdown="1">

  <div id="what-is-vertica" style="scroll-margin-top: 100px;"></div>

  ## Vertica란 무엇인가 
  <div class="slide-banner">
    <div class="slide-banner__intro">
      <p>Vertica는 데이터 수집부터 분석, 시각화에 이르는 전 과정을 통합한 현대적인 데이터 레이크하우스 아키텍처를 제공합니다.</p>
    </div>

    <div class="slide-panels">
      <article class="slide-panel">
      <img src="{{ '/assets/images/WhatisVertica_1.png' | relative_url }}" alt="What is Vertica slide 1" />
        <h3>Data Sources</h3>
        <ul>
          <li><b>Files:</b> CSV, JSON, Parquet, ORC, Avro</li>
          <li><b>Enterprise:</b> ERP, CRM, OLTP (Oracle, MySQL 등)</li>
          <li><b>Big Data:</b> Kafka 실시간 스트리밍, Spark, HDFS</li>
        </ul>
      </article>

      <article class="slide-panel">
      <img src="{{ '/assets/images/WhatisVertica_2.png' | relative_url }}" alt="What is Vertica slide 2" />
        <h3>Analytics Platform</h3>
        <ul>
          <li><b>Unified Engine:</b> Native 및 External 데이터 통합 처리</li>
          <li><b>In-DB ML:</b> 데이터 이동 없는 고속 머신러닝 분석</li>
          <li><b>Management:</b> 통합 모니터링 및 클러스터 관리 콘솔</li>
        </ul>
      </article>

      <article class="slide-panel">
      <img src="{{ '/assets/images/WhatisVertica_3.png' | relative_url }}" alt="What is Vertica slide 3" />
        <h3>Consumption Layer</h3>
        <ul>
          <li><b>BI Tools:</b> Tableau, Looker, Power BI 연동</li>
          <li><b>Data Science:</b> Jupyter, Python, R 환경 지원</li>
          <li><b>Connectivity:</b> JDBC, ODBC, ADO.net 표준 지원</li>
        </ul>
      </article>
    </div>
  </div>

  Vertica 아키텍처는 유연한 확장성과 강력한 분석 성능을 동시에 보장합니다.

  ### 주요 컴포넌트 상세

  #### 1. 다양한 데이터 소스 통합 (Ingestion)
  전통적인 RDBMS 데이터부터 최신 빅데이터 포맷인 Parquet, ORC는 물론이고, Kafka를 통한 실시간 스트리밍 데이터까지 하나의 플랫폼으로 수집하고 분석할 수 있습니다.

  #### 2. 하이브리드 스토리지 엔진 (Core)
  - **Native Storage:** 최적의 압축률과 성능을 보장하는 자체 컬럼형 저장소
  - **External Data:** S3, HDFS 등 외부에 있는 데이터를 복제 없이 즉시 쿼리
  - **Management Console:** 직관적인 GUI를 통한 클러스터 성능 최적화

  #### 3. 사용자 중심의 데이터 활용 (Output)
  - 현업 사용자들을 위한 **BI 도구(Tableau, Power BI 등)**와의 완벽한 호환성
  - 데이터 과학자를 위한 **Python/R 및 In-DB ML** 라이브러리 제공
  - 개발자를 위한 다양한 표준 인터페이스 및 커넥터 지원

  ### 아키텍처의 강점

  - **환경의 제약 없음:** 온프레미스, 퍼블릭 클라우드, 쿠버네티스 어디서든 동일한 아키텍처 유지
  - **데이터 격리(Isolation):** 워크로드 분리를 통해 분석과 적재 업무 간의 간섭 최소화
  - **무한 확장성:** 데이터 양이 늘어나도 노드 추가만으로 선형적인 성능 향상 증명

  <hr style="margin: 3rem 0;">
  <div id="technology" style="scroll-margin-top: 100px;"></div>

  ## Vertica 핵심 기술
Vertica는 다음 5가지 기술 요소를 중심으로 고성능 분석을 지원합니다.

<div class="tech-features-grid">

  <article class="tech-feature-card">
    <img src="{{ '/assets/images/Vertica5tech_1.png' | relative_url }}" alt="Native Columnar Storage Icon" />
    <h3>1. Native Columnar Storage</h3>
    <ul>
      <li>필요한 컬럼만 읽어오는 설계로 I/O를 크게 감소</li>
      <li>컬럼 단위 저장과 처리를 위한 최적화</li>
      <li>쿼리 성능과 저장 효율을 동시에 확보</li>
    </ul>
  </article>

  <article class="tech-feature-card">
    <img src="{{ '/assets/images/Vertica5tech_2.png' | relative_url }}" alt="Compression / Encoding Icon" />
    <h3>2. Compression / Encoding</h3>
    <ul>
      <li>컬럼 단위 저장으로 높은 압축률 달성</li>
      <li>12가지 인코딩 및 압축 알고리즘 지원</li>
      <li>75% 이상의 스토리지 절감 효과</li>
    </ul>
  </article>

  <article class="tech-feature-card">
    <img src="{{ '/assets/images/Vertica5tech_3.png' | relative_url }}" alt="MPP Scale-out Icon" />
    <h3>3. MPP Scale-out</h3>
    <ul>
      <li>순수 MPP 구조로 노드 간 종속성 제거</li>
      <li>Exabyte 수준 확장성 제공</li>
      <li>노드 장애 시에도 분산 쿼리 수행이 가능</li>
    </ul>
  </article>

  <article class="tech-feature-card">
    <img src="{{ '/assets/images/Vertica5tech_4.png' | relative_url }}" alt="Distributed Query Icon" />
    <h3>4. Distributed Query</h3>
    <ul>
      <li>여러 노드에 분산된 데이터를 효율적으로 처리</li>
      <li>최적화된 쿼리 계획으로 대형 분석 워크로드 지원</li>
    </ul>
  </article>

  <article class="tech-feature-card">
    <img src="{{ '/assets/images/Vertica5tech_5.png' | relative_url }}" alt="Projections Icon" />
    <h3>5. Projections</h3>
    <ul>
      <li>쿼리 성능을 가속화하는 물리계층 인덱스 개념</li>
      <li>필요한 컬럼/행만 빠르게 조회 가능</li>
    </ul>
  </article>

</div>

  <hr style="margin: 3rem 0;">
  <div id="portfolio" style="scroll-margin-top: 100px;"></div>

  ## Vertica 포트폴리오
  Vertica 제품군은 다양한 환경과 요구에 맞춰 구성됩니다.
  <hr style="margin: 3rem 0;">
  <div id="architecture" style="scroll-margin-top: 100px;"></div>

  ## Vertica 아키텍처
  Vertica는 단순하고 효율적인 아키텍처로 대규모 데이터 분석을 지원합니다.

  ### Pure-MPP 구조
  - 별도의 마스터 노드 없이 모든 노드가 동일한 역할 수행
  - 비용 효율적인 구성 및 빠른 클러스터 구축
  - 단순하고 빠른 확장과 운영

  ### Native Columnar
  - 필요한 컬럼만 읽어 I/O 최소화
  - 컬럼 기반 저장과 쿼리 옵티마이저 최적화
  - 적은 하드웨어로 더 높은 성능 제공

  ### 압축 및 인코딩
  - 컬럼 단위 저장으로 높은 압축률
  - 데이터 타입별 최적화된 인코딩
  - 스토리지 사용량과 I/O 비용 절감

  ### 데이터 이중화와 무중단 서비스
  - RAID와 유사한 데이터 이중화로 장애 복원력 강화
  - 노드 추가/제거 시에도 서비스 무중단
  - 장애 발생 시 자동 데이터 동기화 지원

  ### Scale-out
  - 노드 증설로 성능 확장
  - 온라인 데이터 재분배로 다운타임 최소화
  - 라이선스 재사용 가능

  <hr style="margin: 3rem 0;">
  <div id="operation" style="scroll-margin-top: 100px;"></div>

  ## 운영과 개발 편의성
  Vertica는 운영 안정성과 개발 편의성을 모두 고려한 플랫폼입니다.

  ### 운영 편의성
  - 무중단 서비스 지원
  - 장애 복구와 데이터 이중화 기능
  - 빠른 백업/복구 아키텍처
  - 로컬 스토리지, Object Storage, NFS, DR 클러스터 등 다양한 백업 대상 지원

  ### 개발 편의성
  - SQL 기반 분석 및 ML 워크플로우 지원
  - 복잡한 데이터 타입과 대용량 데이터 처리 지원
  - 다양한 데이터 소스를 쉽게 통합
  - 클라우드와 온프레미스 환경 간 유연한 전환

  </div>

  <aside class="page-sidebar">
    <div class="sidebar-panel">
      <h3>On this page</h3>
      <ul>
        <li><a href="#what-is-vertica">Vertica란 무엇인가</a></li>
        <li><a href="#technology">Vertica 핵심 기술</a></li>
        <li><a href="#portfolio">Vertica 포트폴리오</a></li>
        <li><a href="#architecture">Vertica 아키텍처</a></li>
        <li><a href="#operation">운영과 개발 편의성</a></li>
      </ul>
    </div>
  </aside>
</div>