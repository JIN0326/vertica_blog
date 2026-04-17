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
    <img src="{{ '/assets/images/Vertica5tech_5.png' | relative_url }}" alt="Projections" />
    <h3>Projections</h3>
    <ul>
      <li>노드 장애 대처와 쿼리 성능을 담보하기 위한 최적화 방안 제공</li>
    </ul>
  </article>
</div>

<div id="portfolio" style="scroll-margin-top: 100px; margin-top: 60px;"></div>

## Vertica 포트폴리오

<p>Vertica는 온프레미스부터 퍼블릭/프라이빗 클라우드까지 고객의 환경에 최적화된 다양한 배포 옵션을 제공합니다.</p>

<div class="portfolio-container" style="display: flex; flex-direction: column; gap: 30px; margin-top: 20px;">

  <div class="portfolio-row" style="display: flex; justify-content: space-between; align-items: center;">
    <div class="portfolio-content">
      <h3 style="margin: 0;">Vertica Enterprise On-Premise</h3>
      <ul style="margin-top: 8px;">
        <li>컬럼 처리 및 선진 압축 기법 기반</li>
        <li>최대 성능 및 확장성 보장</li>
        <li>ML, Pattern Matching, Flex Tables 등 다양한 고급 기능 제공</li>
      </ul>
    </div>
    <img src="{{ '/assets/images/portfolio1.png' | relative_url }}"  />
  </div>

  <div class="portfolio-row" style="display: flex; justify-content: space-between; align-items: center;">
    <div class="portfolio-content">
      <h3 style="margin: 0;">Vertica Enterprise in the Clouds</h3>
      <ul style="margin-top: 8px;">
        <li>클라우드 플랫폼으로의 빠른 전개 (BYOL)</li>
        <li>AWS, Azure, Google, Vmware 완벽 지원</li>
        <li>유연한 클라우드 기반 옵션 제공</li>
      </ul>
    </div>
    <img src="{{ '/assets/images/portfolio2.png' | relative_url }}" />
  </div>

  <div class="portfolio-row highlight" style="display: flex; justify-content: space-between; align-items: center;">
    <div class="portfolio-content">
      <h3 style="margin: 0;">Vertica Eon in the Clouds & On-Premise</h3>
      <ul style="margin-top: 8px;">
        <li>Computing Node와 Storage의 완전 분리</li>
        <li>Object Storage 기반의 무한한 확장성</li>
        <li>Workload에 따른 유연한 증설 및 Multi-Cluster 구성 지원</li>
      </ul>
    </div>
    <img src="{{ '/assets/images/portfolio3.png' | relative_url }}" />
  </div>

  <div class="cloud-logos" style="display: flex; justify-content: space-around; align-items: center; flex-wrap: wrap; margin: 40px 0; gap: 20px;">
    <img src="{{ '/assets/images/portfolio4.png' | relative_url }}" alt="AWS" height="40">
    <img src="{{ '/assets/images/portfolio5.png' | relative_url }}" alt="Kubernetes" height="50">
    <img src="{{ '/assets/images/portfolio6.png' | relative_url }}"  alt="Azure" height="40">
    <img src="{{ '/assets/images/portfolio7.png' | relative_url }}"  alt="Google Cloud" height="40">
    <img src="{{ '/assets/images/portfolio8.png' | relative_url }}"  alt="VMware" height="25">
  </div>

  <div class="cloud-ecosystem">
    <h3 style="margin-bottom: 5px;">Public / Private Cloud Support</h3>
    <ul style="margin-top: 0; list-style-type: disc;">
      <li>AWS Azure GCP Naver Cloud KT Cloud Kubernetes Vmware</li>
      <li>주요 Public cloud marketplace를 통한 수분 내 클러스터 프로비저닝 지원 및 Private cloud(K8s, Vmware) 기반의 DW 구축 지원</li>
    </ul>
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