---
title: Vertica Blog
layout: default
permalink: /
description: "Vertica 분석 플랫폼에 대한 기술 정보와 사용법을 공유합니다. 아키텍처, 운영, 데이터 연동, 보안 등 다양한 주제를 다룹니다."
---

<div class="hero-container">
  <div class="hero-content">
    <span class="eyebrow">Analytics Database</span>    
    <h1>데이터의 한계를 넘어서, <br> Vertica로 실현하는 초고속 분석</h1>
    <p>Vertica는 대용량 분석에 특화된 MPP 및 컬럼 기반(Columnar) 아키텍처를 갖춘 SQL 데이터 웨어하우스입니다. <br>
    페타바이트 규모의 복잡한 데이터 워크로드를 처리하여 기업이 데이터를 관리하고 분석하는 방식을 혁신하도록 설계된 Analytics Database입니다.</p>
  </div>

    <div class="architecture-grid">
    <article class="mode-card">
      <div class="mode-image">
        <img src="{{ '/assets/images/EnterpriseMode.png' | relative_url }}" alt="Vertica Enterprise Mode" />
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

<section id="reference" class="administration-section">
  <h2>Vertica 고객사례</h2>
  <div class="portfolio-container" style="max-width: 1080px; margin: 0 auto;">
      <div class="portfolio-row">
        <div class="portfolio-content">
          <h3>금융 분야</h3>
          <p>국내 주요 시중은행들은 차세대 통합 분석 플랫폼을 구축하기 위해 Vertica를 선택했습니다. 기존의 레거시 데이터 웨어하우스(DW)를 성공적으로 대체하여, 페타바이트(PB) 규모의 데이터를 처리하는 핵심 분석 시스템, 머신러닝 기반 이상 거래 탐지(AML), 그리고 마이데이터 서비스의 기반을 마련했습니다. 특히 유연한 Eon Mode 아키텍처를 도입하여 클라우드 환경과 같은 확장성과 안정성을 확보했습니다.</p>
          <p>또한, 신용정보 기관에서는 대규모 빅데이터 분석 시스템에, 그리고 가상자산 거래소에서는 전사 데이터 웨어하우스(EDW)를 구축하는 데 Vertica를 활용하고 있습니다.</p>
          <ul>
            <li><strong>주요 활용 사례</strong>: 차세대 정보계 DW, 통합 분석 플랫폼, 빅데이터 분석, 마이데이터, AML/FDS, EDW</li>
          </ul>
        </div>
        <img src="{{ '/assets/images/ref_fin.png' | relative_url }}" alt="금융 분야 고객사례" style="border-radius: 8px;">
      </div>

      <div class="portfolio-row">
        <div class="portfolio-content">
          <h3>기업 및 공공 분야</h3>
          <p>국내 대표적인 통신사들은 기존 외산 DW를 Vertica로 전환하여 수백 테라바이트(TB) 규모의 차세대 비즈니스 인텔리전스(BI) 및 DW 시스템을 성공적으로 구축했습니다. 또한, 글로벌 제조 기업들은 물류 및 공급망 관리(SCM) 데이터 분석을 위해 기존 시스템을 Vertica로 대체하여 운영 효율성을 극대화하고 있습니다.</p>
          <p>이 외에도 다수의 공공기관 및 유통, 패션 기업들이 전사 데이터 웨어하우스(EDW)와 핵심 분석 시스템에 Vertica를 도입하여 데이터 기반 의사결정 체계를 강화하고 있습니다.</p>
          <ul>
            <li><strong>주요 활용 사례</strong>: 차세대 BIDW, 통합 정보계 시스템, 물류/SCM 분석, 운영 자동화 DW, EDW</li>
          </ul>
        </div>
        <img src="{{ '/assets/images/ref_copr.png' | relative_url }}" alt="기업 및 공공 분야 고객사례" style="border-radius: 8px;">
      </div>

      <div class="portfolio-row">
        <div class="portfolio-content">
          <h3>의료 분야</h3>
          <p>국내 대부분의 상급 종합병원 및 주요 대학 병원들이 의료 데이터 분석을 위한 임상 데이터 웨어하우스(CDW) 플랫폼으로 Vertica를 채택했습니다. 방대한 양의 의료 데이터를 효과적으로 분석하여 임상 연구를 활성화하고, 데이터에 기반한 병원 경영 혁신을 이루어내고 있습니다.</p>
          <p>온프레미스 환경뿐만 아니라 클라우드(AWS) 환경에서도 성공적으로 시스템을 구축하며, Vertica의 유연성과 강력한 성능을 입증하고 있습니다.</p>
          <ul>
            <li><strong>주요 활용 사례</strong>: 임상 데이터 웨어하우스(CDW), 의료정보 분석 시스템, 연구용 데이터 플랫폼</li>
          </ul>
        </div>
        <img src="{{ '/assets/images/ref_hosp.png' | relative_url }}" alt="의료 분야 고객사례" style="border-radius: 8px;">
      </div>
    </div>
</section>

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
      <h3>Vertica 포트폴리오</h3>
      <p>온프레미스부터 클라우드까지, 다양한 환경에 최적화된 Vertica의 배포 포트폴리오를 소개합니다.</p>
      <a href="{{ '/01playbook#portfolio' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Vertica 아키텍처</h3>
      <p>Pure-MPP, 데이터 이중화, Scale-out 등 효율적인 아키텍처의 특징을 설명합니다.</p>
      <a href="{{ '/01playbook#architecture' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Vertica 연계</h3>
      <p>Kafka, S3, HDFS 등 다양한 데이터 생태계와 연동하는 방법을 소개합니다.</p>
      <a href="{{ '/01playbook#embracing' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Vertica MCP 연동</h3>
      <p>LLM과 Vertica를 연동하여 자연어 기반 데이터 분석을 수행하는 방법을 알아봅니다.</p>
      <a href="{{ '/01playbook#mcp-integration' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Vertica EonMode</h3>
      <p>스토리지와 컴퓨팅을 분리하여 유연성과 확장성을 극대화한 Eon Mode를 심층 분석합니다.</p>
      <a href="{{ '/01playbook#eonmode' | relative_url }}">이동 →</a>
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
      <h3>Partition</h3>
      <p>대용량 테이블을 관리하고 쿼리 성능을 최적화하는 파티셔닝 기법을 알아봅니다.</p>
      <a href="{{ '/02administration#partition' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Schema</h3>
      <p>객체를 그룹화하는 Schema를 관리합니다.</p>
      <a href="{{ '/02administration#schema' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>User</h3>
      <p>데이터베이스 접근 주체인 User를 관리합니다.</p>
      <a href="{{ '/02administration#user' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Profile</h3>
      <p>사용자 세션 환경을 제어하는 방법을 알아봅니다.</p>
      <a href="{{ '/02administration#profile' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Resource Pool</h3>
      <p>쿼리 자원 할당을 제어하는 방법을 알아봅니다.</p>
      <a href="{{ '/02administration#resource-pool' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Privilege</h3>
      <p>객체 접근 권한을 관리합니다.</p>
      <a href="{{ '/02administration#privilege' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Locks</h3>
      <p>동시성 제어를 위한 잠금(Lock) 메커니즘과 모니터링 방법을 알아봅니다.</p>
      <a href="{{ '/02administration#locks' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Tuple Mover</h3>
      <p>WOS의 데이터를 ROS로 이동시키고, 삭제된 데이터를 영구 제거하는 Tuple Mover의 동작을 알아봅니다.</p>
      <a href="{{ '/02administration#tuple-mover' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Statistics (통계)</h3>
      <p>쿼리 옵티마이저의 효율적인 실행 계획 수립을 위한 통계 정보 수집 및 관리 방법을 알아봅니다.</p>
      <a href="{{ '/02administration#statistics' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Backup & Restore</h3>
      <p>vbr을 이용한 데이터 백업/복구 방법을 다룹니다.</p>
      <a href="{{ '/02administration#backup-restore' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Cluster Operation</h3>
      <p>DB 기동/중지, 장애 조치 등 클러스터 운영의 핵심 가이드를 제공합니다.</p>
      <a href="{{ '/02administration#cluster-operation' | relative_url }}">이동 →</a>
    </article>
  </div>
</section>

<section id="coretech" class="coretech-section">
  <h2>Vertica CoreTech</h2>
  <div class="cards">
    <article class="card">
      <h3>vsql (CLI)</h3>
      <p>Vertica의 CLI 클라이언트인 vsql의 접속 방법과 유용한 메타 커맨드를 학습합니다.</p>
      <a href="{{ '/03coretech#vsql' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Monitoring</h3>
      <p>Management Console(MC)과 Grafana를 활용한 데이터베이스 모니터링 방법을 알아봅니다.</p>
      <a href="{{ '/03coretech#monitoring' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Data Collector</h3>
      <p>Vertica 시스템 메트릭 및 쿼리 통계를 수집하는 Data Collector의 역할과 보존 정책을 알아봅니다.</p>
      <a href="{{ '/03coretech#data-collector' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Clients</h3>
      <p>vsql, DBeaver 등 다양한 클라이언트 도구를 사용하여 Vertica에 접속하는 방법을 알아봅니다.</p>
      <a href="{{ '/03coretech#clients' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Copy (데이터 적재)</h3>
      <p>벌크 데이터 로딩을 위한 Copy 명령어의 사용법과 주요 옵션을 알아봅니다.</p>
      <a href="{{ '/03coretech#copy' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Export (데이터 내보내기)</h3>
      <p>vsql, EXPORT TO PARQUET 등 데이터를 외부로 추출하는 다양한 방법을 알아봅니다.</p>
      <a href="{{ '/03coretech#export' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Explain (쿼리 분석)</h3>
      <p>실행 계획을 분석하고 쿼리 성능을 최적화하는 방법을 다룹니다.</p>
      <a href="{{ '/03coretech#explain' | relative_url }}">이동 →</a>
    </article>
  </div>
</section>

<section id="utilization" class="utilization-section">
  <h2>Vertica Utilization</h2>
  <div class="cards">
    <article class="card">
      <h3>Python 연동</h3>
      <p>vertica-python 라이브러리를 사용하여 Python 환경에서 데이터를 처리하고 분석합니다.</p>
      <a href="{{ '/04utilization#python' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Kafka 연동</h3>
      <p>Kafka 스트리밍 데이터를 Vertica로 직접 적재하고 실시간 분석 파이프라인을 구축합니다.</p>
      <a href="{{ '/04utilization#kafka' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Apache Airflow 연동</h3>
      <p>Airflow를 활용하여 Vertica의 데이터 파이프라인을 자동화하고 스케줄링하는 방법을 알아봅니다.</p>
      <a href="{{ '/04utilization#airflow' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Apache Iceberg 연동</h3>
      <p>Data Lakehouse 구축을 위해 Apache Iceberg 테이블 포맷과 연동하는 방법을 알아봅니다.</p>
      <a href="{{ '/04utilization#iceberg' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Apache Polaris 연동</h3>
      <p>중앙 메타데이터 서비스인 Polaris와 연동하여 동적으로 최신 테이블 정보를 조회합니다.</p>
      <a href="{{ '/04utilization#polaris' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>AWS Glue & PyIceberg 연동</h3>
      <p>Python 기반 PyIceberg와 AWS Glue를 활용하여 서버리스 데이터 운영을 구현합니다.</p>
      <a href="{{ '/04utilization#aws-glue-pyiceberg' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Trino & Iceberg 연동</h3>
      <p>분산 SQL 쿼리 엔진 Trino와 Iceberg를 연동하여 데이터 레이크하우스를 구축하는 방법을 알아봅니다.</p>
      <a href="{{ '/04utilization#trino-iceberg' | relative_url }}">이동 →</a>
    </article>
  </div>
</section>

<section id="auth-security" class="utilization-section">
  <h2>Vertica Auth & Security</h2>
  <div class="cards">
    <article class="card">
      <h3>Client Authentication</h3>
      <p>Hash, LDAP, TLS, OAuth 등 다양한 클라이언트 인증 방식을 설정하고 관리합니다.</p>
      <a href="{{ '/05auth_security#auth-overview' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>LDAP 인증</h3>
      <p>Active Directory 또는 OpenLDAP 서버와 연동하여 통합 계정 인증을 구현합니다.</p>
      <a href="{{ '/05auth_security#ldap' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>TLS 보안</h3>
      <p>서버-클라이언트 간 상호 인증(Mutual Mode)으로 통신을 암호화하고 보안을 강화합니다.</p>
      <a href="{{ '/05auth_security#tls' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Okta 연동</h3>
      <p>Okta IdP를 활용하여 데이터베이스 및 MC에 대한 SSO(Single Sign-On)를 구현합니다.</p>
      <a href="{{ '/05auth_security#okta' | relative_url }}">이동 →</a>
    </article>
  </div>
</section>


<section id="installation" class="utilization-section">
  <h2>Vertica Installation</h2>
  <div class="cards">
    <article class="card">
      <h3>Vertica 설치</h3>
      <p>Vertica 데이터베이스 설치를 위한 사전 요구사항 및 OS 설정, 설치 과정을 안내합니다.</p>
      <a href="{{ '/06installation#vertica-install' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Vertica 초기설정</h3>
      <p>데이터베이스 생성 후 권장되는 PK/UK 활성화, 로드 밸런싱, Export Address 등 초기 설정을 안내합니다.</p>
      <a href="{{ '/06installation#initial-setup' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Vertica 포트 변경</h3>
      <p>보안 및 서비스 충돌 방지를 위해 Vertica의 기본 포트를 변경하는 방법을 알아봅니다.</p>
      <a href="{{ '/06installation#change-port' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>MC 설치</h3>
      <p>Vertica 클러스터 모니터링 및 관리를 위한 Management Console(MC) 설치 방법을 안내합니다.</p>
      <a href="{{ '/06installation#mc-install' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>vcluster (REST API CLI)</h3>
      <p>REST API를 통해 Vertica 클러스터를 관리하는 vcluster CLI의 사용법을 알아봅니다.</p>
      <a href="{{ '/06installation#vcluster' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Add Node (Enterprise)</h3>
      <p>Enterprise Mode 클러스터에 새로운 노드를 추가하고 데이터를 리밸런싱하는 방법을 알아봅니다.</p>
      <a href="{{ '/06installation#add-node' | relative_url }}">이동 →</a>
    </article>
    <article class="card">
      <h3>Add Subcluster (Eon)</h3>
      <p>Eon Mode 클러스터에 새로운 서브클러스터 또는 노드를 추가하는 방법을 알아봅니다.</p>
      <a href="{{ '/06installation#add-subcluster' | relative_url }}">이동 →</a>
    </article>
  </div>
</section>