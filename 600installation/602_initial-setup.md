---
title: "Vertica 초기설정: DB 생성 후 권장 설정 가이드 - Vertica Blog"
layout: default
description: "Vertica 데이터베이스 생성 후 권장되는 초기 설정 방법을 알아봅니다. PK/UK 제약조건 활성화, 클라이언트 연결 로드 밸런싱, DBeaver 설정, Export Address 설정 등을 다룹니다."
keywords: "vertica, initial setup, configuration, primary key, unique key, load balancing, roundrobin, dbeaver, export address, post-installation"
canonical_url: "https://jin0326.github.io/vertica_blog/600installation/600installation#initial-setup"
---

<div id="initial-setup" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody">
## 초기 설정
<div class="architecture-section">
  <p class="section-description">데이터베이스 생성 후, 안정적인 운영과 성능 최적화를 위해 몇 가지 초기 설정을 수행하는 것이 좋습니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. PK/UK 제약조건 기본 활성화</h3>
    <p class="section-description">Primary Key 및 Unique Key 제약조건이 생성 시 기본적으로 활성화(ENABLE)되도록 설정합니다.</p>
    <div class="syntax-box">
      <pre><code>SELECT SET_CONFIG_PARAMETER('EnableNewUniqueKeysByDefault', 1);
SELECT SET_CONFIG_PARAMETER('EnableNewPrimaryKeysByDefault', 1);</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 클라이언트 연결 로드 밸런싱 설정</h3>
    <p class="section-description">클라이언트 접속 요청을 클러스터 노드 간에 라운드 로빈(Round Robin) 방식으로 분산하여 단일 노드의 접속 부하를 방지합니다.</p>
    <div class="syntax-box">
      <pre><code>SELECT SET_LOAD_BALANCE_POLICY('ROUNDROBIN');

-- 확인: vsql -C 옵션으로 접속 시도 시, 접속되는 노드가 매번 변경되는지 확인
-- vsql -C -c "select current_session();"</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. 클라이언트 설정 (DBeaver)</h3>
    <p class="section-description">DBeaver와 같은 GUI 클라이언트에서 안정적인 연결을 위해 고가용성(HA) 관련 속성을 설정합니다.</p>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>AutoCommit:</strong> <code>false</code>로 설정하여 수동 커밋 모드로 전환합니다.</li>
      <li><span class="feature-list__icon">🔹</span> <strong>ConnectionLoadBalance:</strong> <code>true</code>로 설정하여 접속 부하를 분산합니다.</li>
      <li><span class="feature-list__icon">🔹</span> <strong>BackupServerNode:</strong> 주 접속 노드 장애 시 대체할 노드 IP 목록을 지정합니다.</li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">4. Export Address 설정</h3>
    <p class="section-description">클러스터의 각 노드가 외부와 통신할 때 사용할 Public IP 주소를 지정합니다. 이를 통해 Private 네트워크는 내부 통신에만 사용하고, 외부 데이터 전송은 Public 네트워크를 통하도록 역할을 분리할 수 있습니다.</p>
    <div class="syntax-box">
      <pre><code>-- 1. Export에 사용할 서브넷 생성
CREATE SUBNET sub1 WITH '&lt;SUBNET_IP_RANGE&gt;';

-- 2. DB의 기본 Export 서브넷으로 지정
ALTER DATABASE DEFAULT EXPORT ON sub1;

-- 3. 각 노드별 Export 주소 생성 및 할당
CREATE NETWORK ADDRESS sub01 ON v_&lt;DB_NAME&gt;_node0001 WITH '&lt;PUBLIC_IP_1&gt;' PORT 5433;
CREATE NETWORK ADDRESS sub02 ON v_&lt;DB_NAME&gt;_node0002 WITH '&lt;PUBLIC_IP_2&gt;' PORT 5433;
CREATE NETWORK ADDRESS sub03 ON v_&lt;DB_NAME&gt;_node0003 WITH '&lt;PUBLIC_IP_3&gt;' PORT 5433;
ALTER NODE v_&lt;DB_NAME&gt;_node0001 EXPORT ON sub01;
ALTER NODE v_&lt;DB_NAME&gt;_node0002 EXPORT ON sub02;
ALTER NODE v_&lt;DB_NAME&gt;_node0003 EXPORT ON sub03;

-- 4. 설정 확인
SELECT node_name, node_address, export_address FROM nodes;

     node_name     | node_address | export_address
  -----------------+--------------+----------------
   v_&lt;DB_NAME&gt;_node0001 | &lt;PRIVATE_IP_1&gt;   | &lt;PUBLIC_IP_1&gt;
   v_&lt;DB_NAME&gt;_node0002 | &lt;PRIVATE_IP_2&gt;   | &lt;PUBLIC_IP_2&gt;
   v_&lt;DB_NAME&gt;_node0003 | &lt;PRIVATE_IP_3&gt;   | &lt;PUBLIC_IP_3&gt;
</code></pre>
    </div>
  </div>
</div>
</div>
</div>
