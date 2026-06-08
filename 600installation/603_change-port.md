---
title: "Vertica 포트 변경: 기본 DB 포트(5433) 변경 방법 - Vertica Blog"
layout: default
description: "보안 정책이나 다른 서비스와의 충돌을 피하기 위해 Vertica의 기본 데이터베이스 포트(5433)를 변경하는 방법을 알아봅니다. DB 생성 시점과 생성 후 변경 방법을 모두 다룹니다."
keywords: "vertica, port, change port, 5433, admintools.conf, alter node, security, database port"
canonical_url: "https://jin0326.github.io/vertica_blog/600installation/600installation#change-port"
---

<div id="change-port" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody" markdown="1">
## Vertica 포트 변경
<div class="architecture-section">
  <p class="section-description">보안 정책이나 다른 서비스와의 충돌을 피하기 위해 Vertica의 기본 포트를 변경할 수 있습니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. DB 생성 시 포트 변경</h3>
    <p class="section-description"> <code>admintools.conf</code> 파일에서 기본 포트를 변경할 수 있습니다.</p>
    <div class="syntax-box">
      <pre><code># 1. /opt/vertica/config/admintools.conf 파일 수정
# [Configuration] 섹션의 last_port 파라미터 변경
last_port = 35433

# 2. DB 생성 실행
/opt/vertica/bin/admintools -t create_db ...</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 생성된 DB의 포트 변경</h3>
    <p class="section-description">이미 운영 중인 데이터베이스의 포트를 변경하려면, DB를 중지한 후 <code>ALTER NODE</code> 명령과 <code>admintools.conf</code> 파일 수정을 함께 수행해야 합니다.</p>
    <div class="syntax-box">
      <pre><code>-- 1. ALTER NODE 명령으로 포트 번호 변경
ALTER NODE v_&lt;DB_NAME&gt;_node0001 PORT 35433;

-- 2. DB 중지
admintools -t stop_db -d &lt;DB_NAME&gt;

-- 3. admintools.conf 파일의 port 변경 후 클러스터에 배포
-- /opt/vertica/config/admintools.conf 파일의 [Database] 섹션 port 변경
admintools -t distribute_config_files

-- 4. DB 재기동 후 포트 변경 확인
admintools -t start_db -d &lt;DB_NAME&gt; -i
vsql -p 35433 -c "SELECT name, address, clientport FROM vs_nodes;"</code></pre>
    </div>
  </div>
</div>
</div>
</div>
