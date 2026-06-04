---
title: "Vertica Add Node: Enterprise Mode 노드 추가 및 리밸런싱 - Vertica Blog"
layout: default
description: "Vertica Enterprise Mode 클러스터에 새로운 노드를 추가하는 방법을 알아봅니다. update_vertica를 이용한 호스트 추가, db_add_node를 이용한 DB 노드 추가, 데이터 리밸런싱 과정을 다룹니다."
keywords: "vertica, add node, enterprise mode, cluster, scale out, update_vertica, db_add_node, rebalance, start_rebalance_cluster"
canonical_url: "https://jin0326.github.io/vertica_blog/600installation/600installation#add-node"
---

<div id="add-node" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody">
## Add Node (Enterprise Mode)

<div class="architecture-section">
  <p class="section-description">Enterprise Mode 클러스터에 새로운 노드를 추가하는 과정입니다. 노드 추가는 DB가 온라인 상태일 때 수행할 수 있으며, 4개 이상의 노드를 구성하려면 유효한 라이선스가 필요합니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. 클러스터에 호스트 추가 (update_vertica)</h3>
    <p class="section-description">기존 클러스터에 새로운 호스트를 물리적으로 추가하고 Vertica 소프트웨어를 설치합니다. 이 작업은 기존 클러스터 노드 중 하나에서 실행해야 합니다.</p>
    <div class="syntax-box">
      <strong>[as root]</strong>
      <pre><code>/opt/vertica/sbin/update_vertica --add-hosts &lt;NEW_NODE_HOSTNAME&gt; \
--rpm /path/to/vertica-&lt;VERSION&gt;.rpm \
--dba-user vertica --dba-user-password '&lt;DBA_USER_PASSWORD&gt;'</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 데이터베이스에 노드 추가 (db_add_node)</h3>
    <p class="section-description">클러스터에 추가된 호스트를 실제 데이터베이스의 멤버로 포함시킵니다. 이 작업은 <code>admintools</code>를 통해 수행됩니다.</p>
    <div class="syntax-box">
      <strong>[as vertica]</strong>
      <pre><code>admintools -t db_add_node -d &lt;DB_NAME&gt; -p '&lt;DB_PASSWORD&gt;' -s &lt;NEW_NODE_HOSTNAME&gt;</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. 데이터 리밸런싱 (Rebalancing)</h3>
    <p class="section-description">새로운 노드가 추가된 후, 기존 데이터를 클러스터 전체에 재분배하여 데이터 균형을 맞춥니다. 이 작업은 시스템 부하가 적을 때 수행하는 것이 좋습니다.</p>
    <div class="syntax-box">
      <strong>[vsql]</strong>
      <pre><code>SELECT START_REBALANCE_CLUSTER();</code></pre>
    </div>
  </div>
</div>
</div>
</div>
