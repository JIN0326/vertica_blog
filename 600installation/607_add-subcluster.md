---
title: "Vertica Add Subcluster: Eon Mode 서브클러스터 및 노드 추가 - Vertica Blog"
layout: default
description: "Vertica Eon Mode에서 워크로드 격리 및 확장을 위해 새로운 서브클러스터를 추가하거나, 기존 서브클러스터에 노드를 추가하는 방법을 알아봅니다."
keywords: "vertica, add subcluster, add node, eon mode, scale out, workload isolation, db_add_subcluster, db_add_node, rebalance_shards, secondary subcluster"
---

<div id="add-subcluster" style="scroll-margin-top: 100px;"></div>

## Add Subcluster (Eon Mode)

<div class="architecture-section">
  <p class="section-description">Eon Mode에서는 워크로드 격리나 확장성 확보를 위해 새로운 서브클러스터를 추가하거나, 기존 서브클러스터에 노드를 추가할 수 있습니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. 새로운 서브클러스터 추가</h3>
    <p class="section-description">완전히 독립된 컴퓨팅 리소스를 가진 새로운 서브클러스터를 추가합니다. 추가할 노드는 사전에 <code>update_vertica</code>를 통해 클러스터에 포함되어 있어야 합니다.</p>
    <div class="syntax-box">
      <strong>[as vertica]</strong>
      <pre><code># 'sub_cluster'라는 이름의 Secondary 서브클러스터 추가
admintools -t db_add_subcluster -d &lt;DB_NAME&gt; -p '&lt;DB_PASSWORD&gt;' -c sub_cluster -s &lt;NEW_NODE_HOSTNAME&gt; --is-secondary</code></pre>
    </div>
    <p class="section-description" style="margin-top: 1.5rem;">서브클러스터 추가 후, <code>REBALANCE_SHARDS</code>를 실행하여 샤드 구독을 최적화합니다. </p>
    <img src="vertica_blog/assets/images/add_sub1.png" alt="서브클러스터 추가 후 샤드 상태" style="width: 100%; max-width: 600px; margin: 1.5rem auto; display: block; border: 1px solid #e0e0e0; border-radius: 8px;">
    <ul class="feature-list" style="margin-top: 1rem;">
      <li><span class="feature-list__icon">💡</span> <strong>에러 해결 팁:</strong> <span>서브클러스터 추가 시 에러가 발생하면, 추가하려는 노드가 Communal Storage(S3, MinIO 등)와 정상적으로 통신할 수 있는지 네트워크 및 방화벽 설정을 반드시 확인해야 합니다.</span></li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 기존 서브클러스터에 노드 추가</h3>
    <p class="section-description">기존 서브클러스터의 컴퓨팅 파워를 보강하기 위해 새로운 노드를 추가합니다.</p>
    <div class="syntax-box">
      <strong>[as vertica]</strong>
      <pre><code># 'default_subcluster'에 새로운 노드 추가
admintools -t db_add_node -d &lt;DB_NAME&gt; -p '&lt;DB_PASSWORD&gt;' -s &lt;NEW_NODE_HOSTNAME&gt; --subcluster default_subcluster</code></pre>
    </div>
    <p class="section-description" style="margin-top: 1.5rem;">노드 추가 후, <code>REBALANCE_SHARDS</code>를 실행하여 해당 서브클러스터 내에서 샤드 구독을 재분배합니다. </p>
    <img src="/vertica_blog/assets/images/add_sub2.png" alt="노드 추가 후 샤드 상태" style="width: 100%; max-width: 600px; margin: 1.5rem auto; display: block; border: 1px solid #e0e0e0; border-radius: 8px;">
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. 노드 및 서브클러스터 제거</h3>
    <div class="syntax-box">
      <strong>[as vertica]</strong>
      <pre><code># 서브클러스터 제거
admintools -t db_remove_subcluster -c sub_cluster -d &lt;DB_NAME&gt;

# 노드 제거
admintools -t db_remove_node -d &lt;DB_NAME&gt; -s &lt;NODE_HOSTNAME_TO_REMOVE&gt;</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">4. 노드 구독 상태 확인</h3>
    <p class="section-description">클러스터의 모든 노드가 각 샤드를 어떻게 구독하고 있는지 상세 정보를 확인합니다.</p>
    <div class="syntax-box">
      <strong>[vsql]</strong>
      <pre><code>SELECT subcluster_name, n.node_name, shard_name, subscription_state, ns.is_primary 
FROM v_catalog.nodes n 
LEFT JOIN v_catalog.node_subscriptions ns ON (n.node_name = ns.node_name) 
ORDER BY 1,2,3,5;</code></pre>
    </div>
  </div>
</div>