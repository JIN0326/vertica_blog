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

  <p>Vertica는 다음 5가지 기술 요소를 중심으로 고성능 분석을 지원합니다.</p>

  <div class="tech-features-grid">
    <article class="tech-feature-card">
      <img src="{{ '/assets/images/Vertica5tech_1.png' | relative_url }}" alt="Native Columnar Storage" />
      <h3>Native Columnar Storage</h3>
      <ul>
        <li>필요한 컬럼만을 조회하여 빠른 쿼리 성능 보장</li>
      </ul>
    </article>

    <article class="tech-feature-card">
      <img src="{{ '/assets/images/Vertica5tech_2.png' | relative_url }}" alt="Compression/Encoding" />
      <h3>Compression /Encoding</h3>
      <ul>
        <li>Disk I/O 비용을 최소화하는 동시에 성능을 가속화</li>
      </ul>
    </article>

    <article class="tech-feature-card">
      <img src="{{ '/assets/images/Vertica5tech_3.png' | relative_url }}" alt="MPP Scale-out" />
      <h3>MPP Scale-out</h3>
      <ul>
        <li>Single point of failure를 제거한 순수 MPP 아키텍처 및 Exabyte 수준 확장성</li>
      </ul>
    </article>

    <article class="tech-feature-card">
      <img src="{{ '/assets/images/Vertica5tech_4.png' | relative_url }}" alt="Distributed Query" />
      <h3>Distributed Query</h3>
      <ul>
        <li>특정 노드에 대한 종속성이 없는 분산 쿼리 수행</li>
      </ul>
    </article>

    <article class="tech-feature-card">
      <img src="{{ '/assets/images/Vertica5Vertica5tech_5.png' | relative_url }}" alt="Projections" />
      <h3>Projections</h3>
      <ul>
        <li>노드 장애 대처와 쿼리 성능을 담보하기 위한 최적화 방안 제공</li>
      </ul>
    </article>
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
        <li><a href="#advanced">고급 개념</a></li>
        <li><a href="#operation">운영과 개발 편의성</a></li>
      </ul>
    </div>
  </aside>