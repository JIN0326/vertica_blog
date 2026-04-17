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
      <p>Vertica는 SQL 데이터 웨어하우스, 고급 분석 및 머신러닝, 통합 쿼리 엔진을 하나의 플랫폼으로 결합한 현대적인 분석 데이터베이스입니다.</p>
    </div>

    <div class="slide-panels">
      <article class="slide-panel">
      <img src="{{ '/assets/images/WhatisVertica_1.png' | relative_url }}" alt="What is Vertica slide 1" />
        <h3>SQL Data Warehouse</h3>
        <ul>
          <li>페타바이트(PB) 규모의 데이터 처리</li>
          <li>표준 SQL 지원 및 ACID 트랜잭션 보장</li>
          <li>고성능 데이터 적재 및 관리</li>
        </ul>
      </article>

      <article class="slide-panel">
      <img src="{{ '/assets/images/WhatisVertica_2.png' | relative_url }}" alt="What is Vertica slide 2" />
        <h3>Analytics & Machine Learning</h3>
        <ul>
          <li>데이터 이동 없는 In-Database 분석</li>
          <li>시계열, 시공간, 패턴 매칭 등 고급 분석 함수</li>
          <li>Python/R 연동 및 병렬 머신러닝 알고리즘 내장</li>
        </ul>
      </article>

      <article class="slide-panel">
      <img src="{{ '/assets/images/WhatisVertica_3.png' | relative_url }}" alt="What is Vertica slide 3" />
        <h3>Unified Query Engine</h3>
        <ul>
          <li>컬럼 기반 저장 및 MPP 아키텍처</li>
          <li>S3, HDFS 등 외부 데이터 직접 쿼리</li>
          <li>자동 쿼리 최적화 및 고속 실행</li>
        </ul>
      </article>
    </div>
  </div>

  Vertica는 유연한 확장성과 강력한 분석 성능을 동시에 보장합니다.

  ### 주요 기능 상세

  #### 1. SQL Data Warehouse
  페타바이트(PB) 규모의 데이터를 안정적으로 저장하고 관리하며, 표준 SQL을 통해 복잡한 데이터 워크로드를 처리합니다. ACID 트랜잭션을 완벽하게 지원하여 데이터의 일관성을 보장합니다.

  #### 2. Analytics & Machine Learning
  데이터를 다른 시스템으로 옮길 필요 없이 Vertica 내부에서 직접 고급 분석과 머신러닝을 수행합니다. 시계열, 시공간 분석부터 예측 모델링, 분류, 군집화 등 다양한 분석 기능을 SQL 함수 형태로 제공합니다.

  #### 3. Unified Query Engine
  컬럼형 저장 방식과 MPP(대규모 병렬 처리) 아키텍처를 기반으로 한 강력한 쿼리 엔진입니다. Vertica 내부 데이터는 물론, S3나 HDFS와 같은 외부 데이터 레이크에 저장된 데이터를 이동 없이 단일 쿼리로 통합 분석할 수 있습니다.

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
  <div id="advanced" style="scroll-margin-top: 100px;"></div>

  ## 고급 개념
  Vertica의 내부 동작을 이해하기 위한 핵심 개념들입니다.

  ### Query Execution Workflow
  Vertica에서 쿼리가 실행되는 과정은 크게 **Parse → Optimize → Execute** 3단계로 나뉩니다.
  1.  **Parse (구문 분석):** SQL 쿼리의 문법적 오류를 확인하고, 쿼리 트리를 생성합니다.
  2.  **Optimize (최적화):** 쿼리 최적화기(Query Optimizer)가 통계 정보(Statistics)와 프로젝션(Projection) 설계를 바탕으로 가장 효율적인 실행 계획(Execution Plan)을 수립합니다. 이 단계에서 조인 순서, 데이터 접근 방식 등이 결정됩니다.
  3.  **Execute (실행):** 생성된 실행 계획에 따라 각 노드에 분산된 데이터를 병렬로 처리하고, 최종 결과를 클라이언트에 반환합니다.

  ### 에포크 (Epoch)
  에포크는 Vertica의 데이터 일관성과 복구를 보장하는 핵심 메커니니즘입니다.
  - **개념:** 에포크는 데이터베이스의 특정 시점을 나타내는 타임스탬프와 유사합니다. 모든 DML(INSERT, UPDATE, DELETE, COPY) 작업은 특정 에포크 내에서 트랜잭션으로 처리됩니다.
  - **종류:**
    - **Current Epoch:** 현재 쓰기 작업이 진행 중인 에포크입니다.
    - **Last Good Epoch (LGE):** 모든 노드에 데이터 쓰기가 성공적으로 완료되어 안전하게 저장된 마지막 에포크입니다. 쿼리는 LGE 시점의 데이터를 읽어 일관성을 보장합니다.
    - **Ancient History Mark (AHM):** 가장 오래된 LGE를 의미하며, AHM 이전의 삭제된 데이터는 `Tuple Mover`에 의해 물리적으로 정리(Purge)될 수 있습니다.
  - **역할:** 노드 장애 발생 시, 모든 노드는 LGE 시점의 데이터로 복구하여 데이터 정합성을 유지합니다.

  <hr style="margin: 3rem 0;">
  <div id="operation" style="scroll-margin-top: 100px;"></div>

  ## 운영과 개발 편의성
  Vertica는 운영 안정성과 개발 편의성을 모두 고려하여 설계되었습니다.

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
        <li><a href="#advanced">고급 개념</a></li>
        <li><a href="#operation">운영과 개발 편의성</a></li>
      </ul>
    </div>
  </aside>
</div>