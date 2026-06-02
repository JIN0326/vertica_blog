---
title: "Vertica Vcluster: REST API CLI를 이용한 클러스터 관리 - Vertica Blog"
layout: default
description: "Vertica 클러스터를 REST API로 관리하는 커맨드 라인 인터페이스(CLI) 도구인 vcluster의 사용법을 알아봅니다. DB 생성, 시작/중지, 노드/서브클러스터 관리, 복구(revive) 등 다양한 명령어를 다룹니다."
keywords: "vertica, vcluster, rest api, cli, nma, create_db, start_db, add_node, add_subcluster, revive_db, eon mode, scrutinize, re-ip"
---

<div id="vcluster" style="scroll-margin-top: 100px;"></div>

## vcluster (REST API CLI)

<div class="architecture-section">
  <p class="section-description"><strong>vcluster</strong>는 Vertica 클러스터를 REST API로 관리하는 커맨드 라인 인터페이스(CLI) 도구입니다. 이 REST API는 각 노드에서 실행되는 <strong>Node Management Agent (NMA)</strong>와 <strong>Embedded HTTPS 서비스</strong>를 통해 제공됩니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. 사전 준비 (Prerequisites)</h3>
    <p class="section-description">vcluster를 사용하기 전에, 클러스터를 구성할 모든 노드에서 Node Management Agent(NMA)를 시작해야 합니다.</p>
    <div class="syntax-box">
      <strong>[as vertica]</strong>
      <pre><code># 모든 노드에서 NMA 에이전트 시작
/opt/vertica/bin/manage_node_agent.sh start node_management_agent

# HTTPS 인증서 존재 여부 확인
ls -la /opt/vertica/config/https_certs/vertica_https.pem</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 데이터베이스 관리</h3>
    <p class="section-description"><code>vcluster</code>를 사용하여 데이터베이스를 생성, 시작, 중지, 삭제할 수 있습니다.</p>
    <div class="syntax-box">
      <strong>DB 생성 (Eon Mode):</strong>
      <pre><code>vcluster create_db --db-name VDB --hosts v004,v005,v006 \
--catalog-path=/catalog --data-path=/data \
--depot-path=/data --shard-count=6 \
--config-param awsauth=&lt;ACCESS_KEY&gt;:&lt;SECRET_KEY&gt;,awsenablehttps=0,awsregion=ap-northeast-2,awsendpoint=&lt;S3_ENDPOINT&gt; \
--communal-storage-location=s3://vdb/eon-communal/</code></pre>
      <strong>DB 상태 확인:</strong>
      <pre><code># DB 상태 확인
vcluster list_all_nodes -p ""

# Node 정보 확인
vcluster manage_config show</code></pre>
      <strong>DB 시작/중지/삭제:</strong>
      <pre><code># DB 시작 (최초 기동 시 config-param 필수)
vcluster start_db -d VDB -p "" --config-param awsauth=&lt;ACCESS_KEY&gt;:&lt;SECRET_KEY&gt;,awsenablehttps=0,awsregion=ap-northeast-2,awsendpoint=&lt;S3_ENDPOINT&gt;

# 최초 수행 이후로는 아래 명령어로 수행 가능
vcluster start_db -d VDB -p ""

# DB 중지
vcluster stop_db -d VDB -p ""

# DB 삭제
vcluster drop_db -d VDB</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. 클러스터 및 노드 관리</h3>
    <p class="section-description">클러스터에 노드나 서브클러스터를 추가/제거하고, 특정 노드/서브클러스터를 시작/중지할 수 있습니다.</p>
    <div class="syntax-box">
      <strong>노드 관리:</strong>
      <pre><code># 노드 추가
vcluster add_node -d VDB --new-hosts v004 -p ""

# 노드 제거
vcluster remove_node -d VDB --remove v004 -p ""</code></pre>
      <strong>서브클러스터 관리:</strong>
      <pre><code># 서브클러스터 추가
vcluster add_subcluster -d VDB --new-hosts v004,v005,v006 --subcluster second_subcluster -p ""

# 서브클러스터 제거
vcluster remove_subcluster -d VDB --subcluster second_subcluster -p ""</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">4. 진단 및 복구</h3>
    <p class="section-description"><code>scrutinize</code>로 진단 파일을 생성하거나, <code>revive_db</code>로 Communal Storage에서 데이터베이스를 복구할 수 있습니다.</p>
    <div class="syntax-box">
      <strong>Scrutinize 실행:</strong>
      <pre><code>vcluster scrutinize -d VDB --db-user vertica -p ""</code></pre>
      <strong>Revive DB 실행:</strong>
      <pre><code># 기본 Revive
vcluster revive_db -d VDB --communal-storage-location=s3://vdb/eon-communal/ --hosts v001,v002,v003 --config-param awsauth=&lt;ACCESS_KEY&gt;:&lt;SECRET_KEY&gt;,awsenablehttps=0,awsregion=ap-northeast-2,awsendpoint=&lt;S3_ENDPOINT&gt;

# 특정 복원 지점(Restore Point)으로 Revive
vcluster revive_db -d VDB --communal-storage-location=s3://vdb/eonmode/ \
--hosts v001,v002,v003 --config-param awsauth=&lt;ACCESS_KEY&gt;:&lt;SECRET_KEY&gt;,awsenablehttps=0,awsregion=ap-northeast-2,awsendpoint=&lt;S3_ENDPOINT&gt; \
--restore-point-archive vertica_restore --restore-point-index 1</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">5. 기타 유틸리티</h3>
    <p class="section-description"><code>vcluster</code>는 IP 주소 변경(re-ip), 클러스터 간 복제(replication) 등 다양한 관리 기능을 제공합니다.</p>
    <div class="syntax-box">
      <strong>IP 변경 (re_ip):</strong>
      <pre><code># 변경할 IP 매핑 정보를 JSON 파일로 생성
cat re_ip.json
[
    {"from_address": "11.11.11.17", "to_address": "11.11.11.117"},
    {"from_address": "11.11.11.18", "to_address": "11.11.11.118"}
]

# re_ip 실행
vcluster re_ip --db-name VDB --re-ip-file /home/vertica/re_ip.json</code></pre>
      <strong>복제 (Replication):</strong>
      <pre><code># Target 클러스터에서 연결 정보 파일 생성
vcluster create_connection start --target-conn /opt/vertica/config/target_vertica_conn.yaml -p ""

# Source 클러스터에서 복제 시작
vcluster replication start --target-conn /opt/vertica/config/target_vertica_conn.yaml -p ""</code></pre>
    </div>
  </div>
</div>