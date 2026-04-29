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
    <img src="{{ '/assets/images/Vertica5tech_3.png' | relative_url }}" alt="Projections" />
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
    <img src="{{ '/assets/images/Vertica5tech_5.png' | relative_url }}" alt="MCP Intergration" />
    <h3>MCP Intergration</h3>
    <ul>
      <li>MCP 기반 자연어 질의 지원으로 SQL에 익숙하지 않은 사용자도 데이터 분석가능 가능합니다.</li>
    </ul>
  </article>

  <article class="tech-feature-card">
    <img src="{{ '/assets/images/Vertica5tech_6.png' | relative_url }}" alt="MPP Scale-out" />
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

<hr style="margin: 3rem 0;">

<div id="embracing" style="scroll-margin-top: 100px;"></div>

## Vertica 연계

<div class="integration-section">
  <div class="integration-subsection">
    <h3 class="integration-subsection__title">Kafka & Spark 연계</h3>
    <p class="integration-subsection__description">
      다양한 채널 및 플랫폼에서 유입되는 데이터를 실시간으로 저장/분석하기 위해서는 데이터 파이프 라인 플랫폼과의 유기적인 연계가 필수적입니다.
    </p>
    
    <div class="kafka-layout">
      <div class="kafka-layout__image-wrapper">
        <img src="{{ '/assets/images/embracing1.png' | relative_url }}" alt="Kafka Integration Architecture">
      </div>
      
      <div class="kafka-steps">
        <div>
          <h4 class="kafka-step__title">
            <span class="kafka-step__number">1</span> Consume
          </h4>
          <ul class="kafka-step__list">
            <li>별도의 솔루션이나 커넥터 없이 카프카 토픽을 적재하는 기능을 빌트인 함수로 제공</li>
            <li>카프카 토픽의 파티션을 병렬로 적재하여 실시간 스트리밍 데이터 처리에 최적화</li>
            <li>자동으로 지속적인 컨슈밍을 지원하기 위한 마이크로배치 기능 탑재</li>
          </ul>
        </div>

        <div>
          <h4 class="kafka-step__title">
            <span class="kafka-step__number">2</span> Realtime Analytics
          </h4>
          <ul class="kafka-step__list">
            <li>매우 짧은 주기로 적재되는 대용량 데이터를 버티카를 통해 분석 수행</li>
          </ul>
        </div>

        <div>
          <h4 class="kafka-step__title">
            <span class="kafka-step__number">3</span> Produce
          </h4>
          <ul class="kafka-step__list">
            <li>버티카에 저장된 데이터를 카프카로 추출하는 기능을 제공</li>
            <li>추가적인 구축 없이 데이터 파이프 라인과 양방향으로 데이터를 주고 받을 수 있는 아키텍처</li>
          </ul>
        </div>

        <div>
          <h4 class="kafka-step__title">
            <span class="kafka-step__number">4</span> Spark integration
          </h4>
          <ul class="kafka-step__list">
            <li>Spark 연계를 위한 커넥터가 기본 제공</li>
            <li>Spark에서 분석이 필요한 업무를 위해 빠르게 스파크 메모리로 버티카 데이터를 로드하여 실시간 분석 수행</li>
            <li>오브젝트 스토리지를 스테이징 파일 시스템으로 활용하여 병렬 추출 및 병렬 적재 지원</li>
            <li>스파크에서 분석된 결과도 버티카로 병렬 추출 및 적재 수행</li>
          </ul>
        </div>
      </div>
    </div>
  </div>

  <div class="integration-cards">
    <article class="integration-card">
      <h3 class="integration-card__title">HDFS 연계</h3>
      <p class="integration-card__description">
        추가적인 장비나 별도의 솔루션 설치 없이 하둡 연계를 지원하여 버티카를 통해 <strong>DW와 하둡 데이터 연계 분석</strong>을 지원합니다.
      </p>
      <div class="integration-card__image-wrapper">
        <img src="{{ '/assets/images/embracing2.png' | relative_url }}" alt="HDFS Integration">
      </div>
    </article>

    <article class="integration-card">
      <h3 class="integration-card__title">Object Storage 연계</h3>
      <p class="integration-card__description">
        추가적인 장비나 별도의 솔루션 설치 없이 Object Storage 에 저장된 데이터를 직접 분석할 수 있으며 버티카 데이터를 다양한 오픈 포맷으로 추출할 수 있어 타 시스템과 유연한 데이터 연계 지원
      </p>
      <div class="integration-card__image-wrapper">
        <img src="{{ '/assets/images/embracing3.png' | relative_url }}" alt="Object Storage Integration">
      </div>
    </article>
  </div>
</div>

<hr style="margin: 3rem 0;">

<div id="eonmode" style="scroll-margin-top: 100px;"></div>

## Vertica EonMode

<div class="eonmode-section architecture-section">
  <p class="section-description">
    Eon Mode는 클라우드 이코노믹스의 동적 워크로드 및 효율적인 확장 요구에 대응하기 위해 설계된 <strong>컴퓨팅/스토리지(C/S) 분리 아키텍처</strong>입니다. 페타바이트 규모 이상의 통합 데이터 레이크하우스(Lakehouse) 플랫폼을 구축하는데 최적화되어 있습니다.
  </p>

  <h3 class="eon-section-title">Eon Mode의 3대 핵심이점</h3>
  
  <div class="slide-panels" style="margin-bottom: 4rem;">
    <article class="slide-panel">
      <img src="{{ '/assets/images/eonmode1.png' | relative_url }}" alt="뛰어난 확장성">
      <h3>뛰어난 확장성 (Scalability)</h3>
      <ul>
        <li>스토리지와 컴퓨팅이 분리되어 있어, 데이터 재분산(Rebalancing)이나 조정 작업 없이 필요 시 즉각적으로 컴퓨팅 노드를 확장할 수 있습니다.</li>
        <li>컴퓨팅 자원과 완전히 독립적으로 스토리지를 무한 확장할 수 있습니다.</li>
      </ul>
    </article>

    <article class="slide-panel">
      <img src="{{ '/assets/images/eonmode2.png' | relative_url }}" alt="비용 효율성">
      <h3>비용 효율성 (Cost)</h3>
      <ul>
        <li>대용량의 영구 데이터를 비용 효율적인 오픈 오브젝트 스토리지에 저장하여 전체 스토리지 비용을 최적화합니다.</li>
        <li>컴퓨팅 파워가 필요한 순간에만 동적으로 자원을 할당하고 활용하여 효율적인 컴퓨팅 비용 모델을 제공합니다.</li>
      </ul>
    </article>

    <article class="slide-panel">
      <img src="{{ '/assets/images/eonmode3.png' | relative_url }}" alt="고가용성 보장">
      <h3>고가용성 보장 (Availability)</h3>
      <ul>
        <li>퍼블릭 클라우드 및 엔터프라이즈급 오브젝트 스토리지의 최고 수준 SLA를 그대로 상속받습니다.</li>
        <li>영구 데이터가 로컬 디스크가 아닌 중앙 오브젝트 스토리지에 저장되므로, 특정 컴퓨팅 노드에 장애가 발생하거나 훼손되어도 데이터 유실이 전혀 없습니다.</li>
      </ul>
    </article>
  </div>

  <h3 class="eon-section-title">혁신적인 컴퓨팅 및 스토리지 아키텍처</h3>
  
  <div class="feature-box">
    <dl class="feature-dl">
      <dt class="feature-dt">
        <span class="feature-dt__icon">◆</span> 스마트 캐싱 <span class="feature-dt__meta">(Compute Architecture)</span>
      </dt>
      <dd class="feature-dd">
        각 컴퓨팅 노드는 영구 데이터를 자체 보관하지 않습니다. 대신 자주 활용되는 핫 데이터(Hot Data)만 로컬 디스크 캐시에 보관하며 LRU(Least Recently Used) 알고리즘으로 관리하여 쿼리 성능을 극대화합니다.
      </dd>

      <dt class="feature-dt">
        <span class="feature-dt__icon">◆</span> 완벽한 트랜잭션 지원 <span class="feature-dt__meta">(Storage Architecture)</span>
      </dt>
      <dd class="feature-dd">
        오브젝트 스토리지를 사용함에도 Write-Once 기반으로 동작하여 DML(갱신/삭제 등) 처리에 제약이 없습니다. 트랜잭션 발생 시 커밋(Commit)된 데이터는 노드 수에 맞춘 최적의 조각으로 나뉘어 저장되며, 즉시 모든 서브클러스터에서 동시에 조회할 수 있습니다.
      </dd>

      <dt class="feature-dt">
        <span class="feature-dt__icon">◆</span> 인프라 종속성 탈피
      </dt>
      <dd class="feature-dd">
        특정 하드웨어에 종속되지 않고 AWS, Azure, GCP 같은 퍼블릭 클라우드는 물론 Kubernetes 환경, VMware, 그리고 온프레미스(Dell, PureStorage, MinIO 등) 하이브리드 구성을 완벽하게 지원합니다.
      </dd>
    </dl>
  </div>

  <h3 class="eon-section-title">실전 Use Case: 동시성 확보 및 운영 단순화</h3>
  
  <div class="use-case-grid">
    <div class="card card--use-case">
      <h4 class="use-case-card__title">서브클러스터(Subcluster) 기반 워크로드 격리</h4>
      <p class="use-case-card__intro">
        전통적 방식에서는 운영 업무 SLA를 지키기 위해 분석 업무나 새로운 워크로드를 제약해야만 했습니다.
      </p>
      <ul class="use-case-card__list">
        <li><strong>독립적인 리소스 구성:</strong> 각 서브클러스터는 다른 노드 수, CPU, Memory 등 개별적인 리소스로 구성할 수 있습니다.</li>
        <li><strong>멀티 클러스터 동시성 확장:</strong> 주간 Batch, BI 대시보드, ML 분석, Spark 연계 등 용도별로 서브클러스터를 분리하여 워크로드 간의 간섭을 원천 차단합니다.</li>
        <li><strong>빠른 확장:</strong> 새로운 클러스터나 노드를 추가할 때 가장 오래 걸리는 '데이터 리밸런싱(Data Rebalancing)' 및 '클러스터 간 데이터 동기화' 과정이 불필요합니다.</li>
      </ul>
    </div>

    <div class="card card--use-case">
      <h4 class="use-case-card__title">Revive를 통한 제로 다운타임 마이그레이션</h4>
      <p class="use-case-card__intro">
        하드웨어 EOL(End of Life)이나 클라우드 전환 시 막대한 데이터 이관(Migration)에 대한 부담을 해소합니다.
      </p>
      <ul class="use-case-card__list">
        <li><strong>OS 및 데이터 무의존성:</strong> 메타데이터와 영구 데이터는 모두 오브젝트 스토리지에 있으므로 컴퓨팅 노드의 OS나 인프라 환경에 얽매이지 않습니다.</li>
        <li><strong>데이터 이관(Migration) 불필요:</strong> 새로운 인프라나 클라우드에 빈 클러스터를 띄운 후, 기존 오브젝트 스토리지의 메타 정보만 동기화(Revive)하면 즉시 DB가 복구됩니다.</li>
        <li><strong>서비스 중단 최소화:</strong> 기존 시스템을 중단할 필요 없이 백그라운드에서 신규 구성을 완료한 뒤 전환할 수 있어 다운타임을 수 분 이내로 최소화합니다.</li>
      </ul>
    </div>
  </div>

</div>

</div> <aside class="page-sidebar">
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