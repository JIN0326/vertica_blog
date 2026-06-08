---
title: "Vertica 연계: Kafka, Spark, HDFS, Object Storage 통합 가이드 - Vertica Blog"
layout: default
description: "Vertica와 다양한 데이터 생태계의 연동 방법을 알아봅니다. Kafka, Spark, HDFS, Object Storage 등과 유기적으로 연계하여 데이터 파이프라인을 구축하는 방법을 소개합니다."
keywords: "vertica, kafka, spark, hdfs, object storage, integration, data pipeline"
canonical_url: "https://jin0326.github.io/vertica_blog/100playbook/100playbook#embracing"
---

<div id="embracing" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody" markdown="1">
## Vertica 연계

<div class="integration-section">
  <div class="integration-subsection">
    <h3 class="integration-subsection__title">Kafka & Spark 연계</h3>
    <p class="integration-subsection__description">
      다양한 채널 및 플랫폼에서 유입되는 데이터를 실시간으로 저장/분석하기 위해서는 데이터 파이프 라인 플랫폼과의 유기적인 연계가 필수적입니다.
    </p>
    
  <div class="kafka-layout">
      <div class="kafka-layout__image-wrapper">
        <img src="/vertica_blog/assets/images/embracing1.png" alt="Kafka Integration Architecture">
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
        <img src="/vertica_blog/assets/images/embracing2.png" alt="HDFS Integration">
      </div>
    </article>

  <article class="integration-card">
      <h3 class="integration-card__title">Object Storage 연계</h3>
      <p class="integration-card__description">
        추가적인 장비나 별도의 솔루션 설치 없이 Object Storage 에 저장된 데이터를 직접 분석할 수 있으며 버티카 데이터를 다양한 오픈 포맷으로 추출할 수 있어 타 시스템과 유연한 데이터 연계 지원
      </p>
      <div class="integration-card__image-wrapper">
        <img src="/vertica_blog/assets/images/embracing3.png" alt="Object Storage Integration">
      </div>
    </article>
  </div>
</div>
</div>
</div>