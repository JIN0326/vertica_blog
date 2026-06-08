---
title: "Vertica EonMode: 컴퓨팅/스토리지 분리 아키텍처 심층 분석 - Vertica Blog"
layout: default
description: "Vertica Eon Mode의 컴퓨팅/스토리지(C/S) 분리 아키텍처를 심층 분석합니다. 확장성, 비용 효율성, 고가용성 등 3대 핵심 이점과 워크로드 격리, 제로 다운타임 마이그레이션 등 실전 Use Case를 알아봅니다."
keywords: "vertica, eon mode, c/s separation, cloud, object storage, scalability, availability, workload isolation, revive"
canonical_url: "https://jin0326.github.io/vertica_blog/100playbook/100playbook#eonmode"
---

<div id="eonmode" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody" markdown="1">
## Vertica EonMode

<div class="eonmode-section architecture-section">
  <p class="section-description">
    Eon Mode는 클라우드 이코노믹스의 동적 워크로드 및 효율적인 확장 요구에 대응하기 위해 설계된 <strong>컴퓨팅/스토리지(C/S) 분리 아키텍처</strong>입니다. 페타바이트 규모 이상의 통합 데이터 레이크하우스(Lakehouse) 플랫폼을 구축하는데 최적화되어 있습니다.
  </p>

  <h3 class="eon-section-title">Eon Mode의 3대 핵심이점</h3>
  
  <div class="slide-panels" style="margin-bottom: 4rem;">
    <article class="slide-panel">
      <img src="/vertica_blog/assets/images/eonmode1.png" alt="뛰어난 확장성">
      <h3>뛰어난 확장성 (Scalability)</h3>
      <ul>
        <li>스토리지와 컴퓨팅이 분리되어 있어, 데이터 재분산(Rebalancing)이나 조정 작업 없이 필요 시 즉각적으로 컴퓨팅 노드를 확장할 수 있습니다.</li>
        <li>컴퓨팅 자원과 완전히 독립적으로 스토리지를 무한 확장할 수 있습니다.</li>
      </ul>
    </article>

  <article class="slide-panel">
      <img src="/vertica_blog/assets/images/eonmode2.png" alt="비용 효율성">
      <h3>비용 효율성 (Cost)</h3>
      <ul>
        <li>대용량의 영구 데이터를 비용 효율적인 오픈 오브젝트 스토리지에 저장하여 전체 스토리지 비용을 최적화합니다.</li>
        <li>컴퓨팅 파워가 필요한 순간에만 동적으로 자원을 할당하고 활용하여 효율적인 컴퓨팅 비용 모델을 제공합니다.</li>
      </ul>
    </article>

  <article class="slide-panel">
      <img src="/vertica_blog/assets/images/eonmode3.png" alt="고가용성 보장">
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
      <h4 class="use-case-card__title">서브클러스터(Subcluster) 기반 <br> 워크로드 격리</h4>
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
      <h4 class="use-case-card__title">Revive를 통한 제로 다운타임 <br> 마이그레이션</h4>
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
</div>
</div>