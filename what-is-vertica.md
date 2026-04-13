---
title: What is VERTICA?
layout: default
---

# What is VERTICA?

<div class="slide-banner">
  <div class="slide-banner__intro">
    <p class="eyebrow">What is VERTICA?</p>
    <p>Vertica는 SQL 기반 대용량 분석을 위한 데이터 웨어하우스 플랫폼입니다. 이 페이지에서는 플랫폼의 핵심 컴포넌트와 실무적 장점을 한눈에 살펴볼 수 있습니다.</p>
  </div>

  <div class="slide-panels">
    <article class="slide-panel">
      <div class="slide-panel__icon">DB</div>
      <h3>SQL Data Warehouse</h3>
      <ul>
        <li>MPP + Columnar 아키텍처로 대용량 데이터 분석에 최적화</li>
        <li>필요한 컬럼만 읽어 I/O를 대폭 절감</li>
        <li>전통적 DW 대비 10-50배 빠른 처리 성능</li>
      </ul>
    </article>

    <article class="slide-panel">
      <div class="slide-panel__icon">AI</div>
      <h3>Analytics & Machine Learning</h3>
      <ul>
        <li>페타바이트 규모의 In-Database ML 지원</li>
        <li>SQL로 ML 워크플로우를 자연스럽게 구성</li>
        <li>PMML / TensorFlow 모델 export/import 기능 지원</li>
      </ul>
    </article>

    <article class="slide-panel">
      <div class="slide-panel__icon">GEAR</div>
      <h3>Query Engine</h3>
      <ul>
        <li>HDFS/Object Store의 다양한 포맷(Parquet/ORC) 직접 조회</li>
        <li>DW와 Data Lake 데이터를 Join 분석 지원</li>
        <li>ARRAY/MAP/STRUCT 등 복합 타입 완벽 지원</li>
      </ul>
    </article>
  </div>
</div>

Vertica는 대용량 데이터 분석을 위해 설계된 SQL 데이터 웨어하우스입니다. Vertica의 주요 특징은 다음과 같습니다.

## 핵심 요소

- SQL Data Warehouse
- Analytics & Machine Learning
- Query Engine
- MPP + Columnar 아키텍처
- On-premise, Public Cloud, Private Cloud, Kubernetes 환경 지원

## 장점

- 데이터 분석에 최적화된 I/O 구조
- 전통적인 DW 대비 10-50배 빠른 처리 성능
- 페타바이트 규모 In-Database Machine Learning 지원
- SQL 기반으로 ML 프로세스 지원
- PMML/TensorFlow 모델의 export/import 기능 지원
- Geospatial, Full text search 고급 분석 기능
- Parquet/ORC 등 다양한 포맷 직접 조회 지원
- DW 데이터와 Data Lake 데이터의 Join 분석 지원
- ARRAY/MAP/STRUCT와 같은 Complex Type 지원
