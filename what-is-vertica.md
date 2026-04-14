---
title: Vertica Architecture & Ecosystem
layout: default
---

# Vertica Analytics Platform Architecture
<div class="slide-banner">
  <div class="slide-banner__intro">
    <p class="eyebrow">Architecture Overview</p>
    <p>Vertica는 데이터 수집부터 분석, 시각화에 이르는 전 과정을 통합한 현대적인 데이터 레이크하우스 아키텍처를 제공합니다.</p>
  </div>

  <div class="slide-panels">
    <article class="slide-panel">
    <img src="{{ '/image/WhatisVertica_1.png' | relative_url }}" alt="What is Vertica slide 1" />
      <h3>Data Sources</h3>
      <ul>
        <li><b>Files:</b> CSV, JSON, Parquet, ORC, Avro</li>
        <li><b>Enterprise:</b> ERP, CRM, OLTP (Oracle, MySQL 등)</li>
        <li><b>Big Data:</b> Kafka 실시간 스트리밍, Spark, HDFS</li>
      </ul>
    </article>

    <article class="slide-panel">
    <img src="{{ '/image/WhatisVertica_2.png' | relative_url }}" alt="What is Vertica slide 2" />
      <h3>Analytics Platform</h3>
      <ul>
        <li><b>Unified Engine:</b> Native 및 External 데이터 통합 처리</li>
        <li><b>In-DB ML:</b> 데이터 이동 없는 고속 머신러닝 분석</li>
        <li><b>Management:</b> 통합 모니터링 및 클러스터 관리 콘솔</li>
      </ul>
    </article>

    <article class="slide-panel">
    <img src="{{ '/image/WhatisVertica_3.png' | relative_url }}" alt="What is Vertica slide 3" />
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

## 주요 컴포넌트 상세

### 1. 다양한 데이터 소스 통합 (Ingestion)
전통적인 RDBMS 데이터부터 최신 빅데이터 포맷인 Parquet, ORC는 물론이고, Kafka를 통한 실시간 스트리밍 데이터까지 하나의 플랫폼으로 수집하고 분석할 수 있습니다.

### 2. 하이브리드 스토리지 엔진 (Core)
- **Native Storage:** 최적의 압축률과 성능을 보장하는 자체 컬럼형 저장소
- **External Data:** S3, HDFS 등 외부에 있는 데이터를 복제 없이 즉시 쿼리
- **Management Console:** 직관적인 GUI를 통한 클러스터 성능 최적화

### 3. 사용자 중심의 데이터 활용 (Output)
- 현업 사용자들을 위한 **BI 도구(Tableau, Power BI 등)**와의 완벽한 호환성
- 데이터 과학자를 위한 **Python/R 및 In-DB ML** 라이브러리 제공
- 개발자를 위한 다양한 표준 인터페이스 및 커넥터 지원

## 아키텍처의 강점

- **환경의 제약 없음:** 온프레미스, 퍼블릭 클라우드, 쿠버네티스 어디서든 동일한 아키텍처 유지
- **데이터 격리(Isolation):** 워크로드 분리를 통해 분석과 적재 업무 간의 간섭 최소화
- **무한 확장성:** 데이터 양이 늘어나도 노드 추가만으로 선형적인 성능 향상 증명