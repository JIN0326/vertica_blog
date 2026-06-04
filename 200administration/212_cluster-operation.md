---
title: "Vertica Cluster Operation: 클러스터 운영 및 장애 조치 - Vertica Blog"
layout: default
description: "Vertica 클러스터의 안정적인 운영을 위한 기동/중지, 노드 장애 시 조치 절차, 그리고 진단 파일 생성(Scrutinize) 등 핵심 운영 가이드를 제공합니다."
keywords: "vertica, cluster operation, admintools, start_db, stop_db, restart_node, scrutinize, management console"
canonical_url: "https://jin0326.github.io/vertica_blog/200administration/200administration#cluster-operation"
---

<div id="cluster-operation" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody">
## Cluster Operation & Diagnostics

<div class="architecture-section">
  <p class="section-description">Vertica 클러스터의 안정적인 운영을 위해 데이터베이스를 기동/중지하고, 노드 장애 시 운영 가이드에 따른 표준 조치 절차를 수행합니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. DB 기동 및 중지 (Start / Stop)</h3>
    <p class="section-description">Vertica 관리자 도구인 <code>admintools</code>를 사용하여 데이터베이스 전체를 안전하게 제어합니다.</p>
    <div class="syntax-box">
      <strong>Vertica 상태 확인:</strong>
      <pre><code>admintools -t view_cluster</code></pre>
      <strong>Vertica DB 기동:</strong>
      <pre><code>admintools -t start_db -d DBNM -p 'vertica계정password' -i</code></pre>
      <strong>기동 로그 모니터링:</strong>
      <pre><code>tail -f /catalog/DBNM/v_dbnm_node00XX_catalog/startup.log</code></pre>
      <strong>Vertica DB 중지:</strong>
      <pre><code>admintools -t stop_db -d DBNM -p 'vertica계정password' </code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 클라이언트 접속 세션 관리</h3>
    <p class="section-description">점검 전후로 클라이언트의 최대 접속 세션 수를 조정하여 시스템 진입을 제어합니다.</p>
    <div class="syntax-box">
      <pre><code>vsql -U vertica -w 'password' -c "SELECT set_config_parameter('MaxClientSessions', 1000);"</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. 장애 발생 시 클러스터/노드 재기동</h3>
    
  <h4 class="section-subtitle">3-1. 장애 진단 프로세스</h4>
  <p class="section-description">장애 발생 시점부터 복구 완료까지의 주요 단계를 시각적으로 확인합니다.</p>

  <div class="process-grid">
    <div class="process-step">
      <div class="image-box-styled"><img src="/vertica_blog/assets/images/cluster_operation_1.png" alt="정상단계"></div>
      <p class="process-step__title">[1단계] 정상 단계</p>
      <p class="process-step__description">모든 노드가 UP 상태로 서비스 중</p>
    </div>
    <div class="process-step">
      <div class="image-box-styled"><img src="/vertica_blog/assets/images/cluster_operation_2.png" alt="장애발생"></div>
      <p class="process-step__title process-step__title--failure">[2단계] 장애 발생</p>
      <p class="process-step__description">특정 노드가 DOWN되어 가용성 저하</p>
    </div>
    <div class="process-step">
      <div class="image-box-styled"><img src="/vertica_blog/assets/images/cluster_operation_3.png" alt="장애노드 재시작"></div>
      <p class="process-step__title">[3단계] 장애 노드 재시작</p>
      <p class="process-step__description">Admintools를 통한 노드 복구 시도</p>
    </div>
    <div class="process-step">
      <div class="image-box-styled"><img src="/vertica_blog/assets/images/cluster_operation_4.png" alt="정상 서비스단계"></div>
      <p class="process-step__title process-step__title--success">[4단계] 정상 서비스 단계</p>
      <p class="process-step__description">데이터 동기화 완료 및 서비스 정상화</p>
    </div>
  </div>

  <h4 class="section-subtitle">3-2. 장애 조치 Check List</h4>
  <p class="section-description">재기동 전에 따라 아래 항목을 반드시 점검하여 잔류 프로세스를 정리합니다</p>
  <ul class="feature-list">
    <li>
      <span class="feature-list__icon">🔹</span> <strong>SSH 접속 확인:</strong> <span>각 노드 간 패스워드 없는 SSH 통신이 가능한지 점검 (<code>ssh [Target_Node_IP]</code>)</span>
    </li>
    <li>
      <span class="feature-list__icon">🔹</span> <strong>프로세스 생존 확인:</strong> <span>비정상 종료 시 남은 <code>vertica</code>, <code>spread</code> 프로세스를 확인하고 <code>kill -9</code>로 종료</span>
    </li>
     <div class="syntax-box">
        <pre><code>ps -ef | grep vertica
ps -ef | grep spread</code></pre>
        </div>
    </ul>

  <h4 class="section-subtitle">3-3. 노드 재기동 실행</h4>
  <div class="syntax-box">
    <strong>재기동 Command:</strong>
    <pre><code>admintools -t restart_node -d DBNM -p 'password' --hosts [Down_Node_IP]</code></pre>
  </div>
  <h4 class="section-subtitle">3-4. 기동 모니터링</h4>
  <div class="syntax-box">
    <strong>로그 확인:</strong>
    <pre><code>tail -f /catalog/DBNM/v_dbnm_node00XX_catalog/startup.log</code></pre>
  </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">4. 장애 분석 파일 생성 (Scrutinize)</h3>
    <p class="section-description">원인 분석을 위해 기술 지원팀에 전달할 정밀 진단 파일을 생성합니다.</p>
    <div class="syntax-box">
      <pre><code>/opt/vertica/bin/scrutinize --by-minute yes -d DBNM -P 'password'</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">5. Management Console (MC) 기동/중지</h3>
    <p class="section-description">클러스터 작업 시 관리 UI 서비스를 제어합니다.</p>
    <div class="syntax-box">
      <pre><code>sudo systemctl status vertica-consoled
sudo systemctl start vertica-consoled
sudo systemctl stop vertica-consoled</code></pre>
    </div>
  </div>
</div>
</div>
</div>