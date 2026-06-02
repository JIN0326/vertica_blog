---
title: "Vertica란 무엇인가: SQL 데이터 웨어하우스, 분석 및 ML, 쿼리 엔진 - Vertica Blog"
layout: default
description: "Vertica는 데이터 수집부터 분석, 시각화에 이르는 전 과정을 통합한 현대적인 데이터 레이크하우스(Data Lakehouse) 아키텍처를 제공합니다."
keywords: "vertica, data lakehouse, sql data warehouse, analytics, machine learning, query engine"
---

<div id="what-is-vertica" style="scroll-margin-top: 100px;"></div>

## Vertica란 무엇인가 

Vertica는 데이터 수집부터 분석, 시각화에 이르는 전 과정을 통합한 <strong>현대적인 데이터 레이크하우스(Data Lakehouse)</strong> 아키텍처를 제공합니다.

<div class="slide-panels">
  <article class="slide-panel">
    <img src="/vertica_blog/assets/images/WhatisVertica_1.png" alt="SQL 데이터 웨어하우스" >
    <h3>SQL Data Warehouse</h3>
    <ul>
      <li><strong>MPP + Columnar</strong> 아키텍처를 통해 대용량 데이터 분석에 최적화</li>
      <li>On-premise / Public Cloud / k8s 등 <strong>어떠한 인프라 환경</strong>에서도 배포 및 운영 지원</li>
      <li>I/O 최적화로 전통적인 DW 환경보다 <strong>10-50배 이상 빠른 처리 성능</strong> 제공</li>
    </ul>
  </article>
  
  <article class="slide-panel">
    <img src="/vertica_blog/assets/images/WhatisVertica_2.png" alt="분석 및 머신러닝" >
    <h3>Analytics & Machine Learning</h3>
    <ul>
      <li>페타바이트 규모의 <strong>In-Database Machine Learning</strong> 지원</li>
      <li>사용자에게 친숙한 <strong>SQL로 ML 프로세스</strong> 지원</li>
      <li>PMML / TensorFlow 모델에 대한 <strong>export/import</strong> 및 Geospatial, Fulltext search 지원</li>
    </ul>
  </article>
  
  <article class="slide-panel">
    <img src="/vertica_blog/assets/images/WhatisVertica_3.png" alt="쿼리 엔진" >
    <h3>Query Engine</h3>
    <ul>
      <li>HDFS/Object Store의 <strong>다양한 포맷(Parquet, ORC 등)</strong> 데이터 직접 조회</li>
      <li>DW 데이터와 Data Lake에 존재하는 <strong>데이터 연계 분석(Join)</strong> 지원</li>
      <li>ARRAY, MAP, STRUCT 등 오픈소스 파일의 <strong>Complex Type</strong> 완벽 지원</li>
    </ul>
  </article>
</div>

Vertica는 유연한 확장성과 강력한 분석 성능을 통해 데이터 가치를 극대화합니다.