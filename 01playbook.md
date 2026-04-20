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

<p>Vertica는 데이터 수집부터 분석, 시각화에 이르는 전 과정을 통합한 <strong>현대적인 데이터 레이크하우스(Data Lakehouse)</strong> 아키텍처를 제공합니다.</p>

<div class="slide-panels">
  <article class="slide-panel">
    <img src="{{ '/assets/images/WhatisVertica_1.png' | relative_url }}" alt="SQL 데이터 웨어하우스" />
    <h3>SQL Data Warehouse</h3>
    <ul>
      <li><strong>MPP + Columnar</strong> 아키텍처를 통해 대용량 데이터 분석에 최적화</li>
      <li>On-premise / Public Cloud / k8s 등 <strong>어떠한 인프라 환경</strong>에서도 배포및 운영 지원</li>
      <li>I/O 최적화로 전통적인 DW 환경보다 <strong>10-50배 이상 빠른 처리 성능</strong> 제공</li>
    </ul>
  </article>
  
  <article class="slide-panel">
    <img src="{{ '/assets/images/WhatisVertica_2.png' | relative_url }}" alt="분석 및 머신러닝" />
    <h3>Analytics & Machine Learning</h3>
    <ul>
      <li>페타바이트 규모의 <strong>In-Database Machine Learning</strong> 지원</li>
      <li>사용자에게 친숙한 <strong>SQL로 ML 프로세스</strong> 지원</li>
      <li>PMML / TensorFlow 모델에 대한 <strong>export/import</strong> 및 Geospatial, Fulltext search 지원</li>
    </ul>
  </article>
  
  <article class="slide-panel">
    <img src="{{ '/assets/images/WhatisVertica_3.png' | relative_url }}" alt="쿼리 엔진" />
    <h3>Query Engine</h3>
    <ul>
      <li>HDFS/Object Store의 <strong>다양한 포맷(Parquet, ORC 등)</strong> 데이터 직접 조회</li>
      <li>DW 데이터와 Data Lake에 존재하는 <strong>데이터 연계 분석(Join)</strong> 지원</li>
      <li>ARRAY, MAP, STRUCT 등 오픈소스 파일의 <strong>Complex Type</strong> 완벽 지원</li>
    </ul>
  </article>
</div>

Vertica는 유연한 확장성과 강력한 분석 성능을 통해 데이터 가치를 극대화합니다.

<div id="technology" style="scroll-margin-top: 100px; margin-top: 60px;"></div>

## Vertica 핵심 기술

<p>Vertica는 다음 6가지 기술 요소를 중심으로 고성능 분석을 지원합니다.</p>

<div class="tech-features-grid">
  <article class="tech-feature-card">
    <img src="{{ '/assets/images/Vertica5tech_1.png' | relative_url }}" alt="Columnar Storage" />
    <h3>Native Columnar Storage</h3>
    <ul>
      <li>필요한 컬럼만을 조회하여 빠른 쿼리 성능을 보장합니다.</li>
    </ul>
  </article>

  <article class="tech-feature-card">
    <img src="{{ '/assets/images/Vertica5tech_2.png' | relative_url }}" alt="Data Compression" />
    <h3>Compression/Encoding</h3>
    <ul>
      <li> Disk I/O 비용을 최소화하는 동시에 성능을 가속화합니다.</li>
    </ul>
  </article>

  <article class="tech-feature-card">
    <img src="{{ '/assets/images/Vertica5tech_3.png' | relative_url }}" alt="Projections
    " />
    <h3>Projections</h3>
    <ul>
      <li>노드 장애 대처와 쿼리 성능을 담보하기 위한 최적화 방안 제공합니다.</li>
    </ul>
  </article>

  <article class="tech-feature-card">
    <img src="{{ '/assets/images/Vertica5tech_4.png' | relative_url }}" alt="Distributed Query Engine" />
    <h3>Distributed Query Engine</h3>
    <ul>
      <li>대규모 클러스터의 모든 노드에 쿼리를 병렬로 분산시켜 대용량 데이터를 고속으로 처리합니다.</li>
    </ul>
  </article>

  <article class="tech-feature-card">
    <img src="{{ '/assets/images/Vertica5tech_5.png' | relative_url }}" alt="MCP Intergration
    " />
    <h3>MCP Intergration</h3>
    <ul>
      <li>MCP 기반 자연어 질의 지원으로 SQL에 익숙하지 않은 사용자도 데이터 분석가능 가능합니다.</li>
    </ul>
  </article>

  <article class="tech-feature-card">
    <img src="{{ '/assets/images/Vertica5tech_6.png' | relative_url }}" alt="MPP Scale-out
    " />
    <h3>MPP Scale-out</h3>
    <ul>
      <li>Name node와 같은 Single point of failure를 제거한 순수 MPP 아키텍처입니다.(Massively Parallel Processing)</li>
    </ul>
  </article>
</div>

<hr style="margin: 3rem 0;">

<div id="portfolio" style="scroll-margin-top: 100px;"></div>

## Vertica 포트폴리오

<p>Vertica는 온프레미스부터 퍼블릭/프라이빗 클라우드까지 고객의 환경에 최적화된 다양한 배포 옵션을 제공합니다.</p>

<div class="portfolio-container">

  <div class="portfolio-row">
    <div class="portfolio-content">
      <h3>Vertica Enterprise On-Premise</h3>
      <ul>
        <li>컬럼 처리 및 선진 압축 기법 기반</li>
        <li>최대 성능 및 확장성 보장</li>
        <li>ML, Pattern Matching, Flex Tables 등 다양한 고급 기능 제공</li>
      </ul>
    </div>
    <img src="{{ '/assets/images/portfolio1.png' | relative_url }}" alt="Vertica Enterprise On-Premise" />
  </div>

  <div class="portfolio-row">
    <div class="portfolio-content">
      <h3>Vertica Enterprise in the Clouds</h3>
      <ul>
        <li>클라우드 플랫폼으로의 빠른 전개 (BYOL)</li>
        <li>AWS, Azure, Google, Vmware 완벽 지원</li>
        <li>유연한 클라우드 기반 옵션 제공</li>
      </ul>
    </div>
    <img src="{{ '/assets/images/portfolio2.png' | relative_url }}" alt="Vertica Enterprise in the Clouds" />
  </div>

  <div class="portfolio-row">
    <div class="portfolio-content">
      <h3>Vertica Eon in the Clouds & On-Premise</h3>
      <ul>
        <li>Computing Node와 Storage의 완전 분리</li>
        <li>Object Storage 기반의 무한한 확장성</li>
        <li>Workload에 따른 유연한 증설 및 Multi-Cluster 구성 지원</li>
      </ul>
    </div>
    <img src="{{ '/assets/images/portfolio3.png' | relative_url }}" alt="Vertica Eon Mode" />
  </div>

  <div class="cloud-logos">
    <div class="cloud-logos-row">
      <img src="{{ '/assets/images/portfolio4.png' | relative_url }}" alt="Google Cloud">
      <img src="{{ '/assets/images/portfolio5.png' | relative_url }}" alt="Kubernetes">
    </div>
    <div class="cloud-logos-row">
      <img src="{{ '/assets/images/portfolio6.png' | relative_url }}"  alt="AWS">
      <img src="{{ '/assets/images/portfolio7.png' | relative_url }}"  alt="Azure">
      <img src="{{ '/assets/images/portfolio8.png' | relative_url }}"  alt="VMware">
    </div>
  </div>

  <div class="cloud-ecosystem">
    <h3>Public / Private Cloud Support</h3>
    <ul>
      <li>AWS Azure GCP Naver Cloud KT Cloud Kubernetes Vmware</li>
      <li>주요 Public cloud marketplace를 통한 수분 내 클러스터 프로비저닝 지원 및 Private cloud(K8s, Vmware) 기반의 DW 구축 지원</li>
    </ul>
  </div>

</div>

<hr style="margin: 3rem 0;">

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
        <h4 class="comparison-column__title">Other Systems</h4>
        <div class="card card--other">
          <img src="{{ '/assets/images/architecture1.png' | relative_url }}" alt="아키텍처 비교: Other Systems">
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
        <h4 class="comparison-column__title comparison-column__title--vertica">VERTICA</h4>
        <div class="card card--vertica">
          <img src="{{ '/assets/images/architecture2.png' | relative_url }}" alt="아키텍처 비교: Vertica">
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
        <img src="{{ '/assets/images/architecture3.png' | relative_url }}" alt="Native Columnar Architecture">
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
</div>

<aside class="page-sidebar">
  <div class="sidebar-panel">
    <h3>On this page</h3>
    <ul>
      <li><a href="#what-is-vertica">Vertica란 무엇인가</a></li>
      <li><a href="#technology">Vertica 핵심 기술</a></li>
      <li><a href="#portfolio">Vertica 포트폴리오</a></li>
      <li><a href="#architecture">Vertica 아키텍처</a></li>
      <li><a href="#embracing">Vertica 연계</a></li>
      <li><a href="#eonmode">Vertica EonMode</a></li>
    </ul>
  </div>
</aside>
</div>