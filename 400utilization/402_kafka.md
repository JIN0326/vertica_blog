---
title: "Vertica Kafka 연동: 실시간 데이터 적재 및 분석 - Vertica Blog"
layout: default
description: "Vertica의 KafkaSource를 사용하여 Apache Kafka 스트리밍 데이터를 실시간으로 적재하고 분석하는 방법을 알아봅니다. 토픽 관리부터 COPY 명령어 사용까지 설명합니다."
keywords: "vertica, kafka, kafkasource, streaming, real-time analytics, copy"
canonical_url: "https://jin0326.github.io/vertica_blog/400utilization/400utilization#kafka"
---

<div id="kafka" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody" markdown="1">
## Kafka 연동

<div class="architecture-section">
  <p class="section-description">Vertica는 <strong>KafkaSource</strong>를 통해 Apache Kafka의 스트리밍 데이터를 직접 소비(Consume)하여 고속으로 적재할 수 있습니다. 이를 통해 실시간 데이터 파이프라인을 구축하고, Kafka에 쌓이는 데이터를 거의 실시간으로 분석할 수 있습니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. Kafka 토픽 관리 (CLI)</h3>
    <p class="section-description">Vertica에서 데이터를 가져올 Kafka 토픽을 생성하고 관리하는 기본적인 CLI 명령어입니다.</p>
    <div class="syntax-box">
      <strong>[as root] Kafka 토픽 생성:</strong>
      <pre><code># Kafka 2.2 버전 이상에서는 zookeeper 대신 bootstrap-server를 사용
/root/kafka/bin/kafka-topics.sh --create --topic test02 --bootstrap-server &lt;KAFKA_BROKER_IP&gt;:9092 --partitions 1</code></pre>
      <strong>[as root] 토픽 리스트 확인:</strong>
      <pre><code>/root/kafka/bin/kafka-topics.sh --list --bootstrap-server &lt;KAFKA_BROKER_IP&gt;:9092</code></pre>
      <strong>[as root] 토픽 삭제:</strong>
      <pre><code>/root/kafka/bin/kafka-topics.sh --delete --bootstrap-server &lt;KAFKA_BROKER_IP&gt;:9092 --topic test02</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. Kafka 메시지 전송 및 확인</h3>
    <p class="section-description">파일에 있는 데이터를 Kafka 토픽으로 전송하고, 컨슈머를 통해 메시지가 정상적으로 수신되는지 확인합니다.</p>
    <div class="syntax-box">
      <strong>[as root] 메시지 파일 생성 (test02.dat):</strong>
      <pre><code>{"COL1": "test01","COL2": "1"}
{"COL1": "test02","COL2": "2"}
{"COL1": "test03","COL2": "3"}</code></pre>
      <strong>[as root] 파일 내용 토픽으로 전송 (Producer):</strong>
      <pre><code>/root/kafka/bin/kafka-console-producer.sh --broker-list &lt;KAFKA_BROKER_IP&gt;:9092 --topic test02 &lt; /root/dat/test02.dat</code></pre>
      <strong>[as root] 컨슈머로 메시지 확인:</strong>
      <pre><code>/root/kafka/bin/kafka-console-consumer.sh --bootstrap-server &lt;KAFKA_BROKER_IP&gt;:9092 --topic test02 --from-beginning 0</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. Vertica에서 Kafka 데이터 적재 (COPY)</h3>
    <p class="section-description">Vertica의 <code>COPY</code> 명령어를 사용하여 Kafka 토픽의 데이터를 실시간으로 테이블에 적재합니다. <code>KafkaSource</code>와 <code>KafkaJSONParser</code>를 활용하여 JSON 형식의 데이터를 파싱합니다.</p>
    <div class="syntax-box">
      <strong>Vertica SQL 실행:</strong>
      <pre><code>-- 1. 데이터 적재용 테이블 생성
CREATE TABLE K_COPY02 (COL1 VARCHAR(10), COL2 INT);

-- 2. Kafka 토픽에서 데이터 적재
COPY K_COPY02
SOURCE KafkaSource(
    stream='test02|0|0', -- '토픽명|파티션번호|시작_오프셋' 형식
    brokers='&lt;KAFKA_BROKER_IP&gt;:9092',
    stop_on_eof=TRUE -- 파일의 끝에 도달하면 적재 중지
)
PARSER KafkaJSONParser(); -- JSON 형식 파싱

-- 3. 적재된 데이터 확인
SELECT * FROM K_COPY02;

-- 4. Kafka 오프셋 정보 확인 (선택 사항)
SELECT ktopic, kpartition, start_offset, end_offset, msg_count, ending 
FROM (SELECT KafkaOffsets() OVER()) AS stats 
ORDER BY kpartition;</code></pre>
    </div>
  </div>
</div>
</div>
</div>