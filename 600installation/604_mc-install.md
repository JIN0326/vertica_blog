---
title: "Vertica MC Install: Management Console 설치 및 DB 등록 - Vertica Blog"
layout: default
description: "Vertica 클러스터를 모니터링하고 관리하기 위한 웹 기반 도구인 Management Console(MC)의 설치 과정과 데이터베이스 등록 방법을 안내합니다."
keywords: "vertica, management console, mc, install, setup, monitoring, rpm, verticad, 5450"
canonical_url: "https://jin0326.github.io/vertica_blog/600installation/600installation#mc-install"
---

<div id="mc-install" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody">
## Management Console (MC) 설치

<div class="architecture-section">
  <p class="section-description">Management Console(MC)은 Vertica 클러스터를 모니터링하고 관리하기 위한 웹 기반 도구입니다. 데이터베이스 클러스터와는 별도의 전용 서버에 설치하는 것이 권장됩니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">MC 하드웨어 요구사항</h3>
    <p class="section-description">MC는 클러스터의 노드 또는 전용 노드에 설치할 수 있습니다. 클러스터 노드에서 MC를 실행할 때 MC는 다른 데이터베이스 프로세스와 RAM 및 CPU 코어 시간을 공유합니다.</p>
    <p class="section-description">다음 표는 최소 및 권장 하드웨어 요구 사항을 제공합니다.</p>
    <table class="info-table">
      <thead>
        <tr>
          <th>요구사항</th>
          <th>CPU</th>
          <th>RAM</th>
          <th>디스크 공간</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><strong>최소</strong></td>
          <td>4-core</td>
          <td>4G</td>
          <td>2G</td>
        </tr>
        <tr>
          <td><strong>권장</strong></td>
          <td>8-core</td>
          <td>8G</td>
          <td>2G</td>
        </tr>
      </tbody>
    </table>
  </div>
  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. MC RPM 패키지 설치</h3>
    <p class="section-description">Vertica MC 설치 RPM 파일을 서버에 복사한 후, `root` 권한으로 설치합니다.</p>
    <div class="syntax-box">
      <strong>[as root]</strong>
      <pre><code>rpm -Uvh /path/to/vertica-console-&lt;VERSION&gt;.RHEL&lt;OS_VERSION&gt;.x86_64.rpm</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. MC 서비스 기동 및 접속</h3>
    <p class="section-description">RPM 설치가 완료되면 MC 서비스를 시작하고, 웹 브라우저를 통해 접속합니다. 기본 접속 포트는 5450입니다.</p>
    <div class="syntax-box">
      <strong>[as root]</strong>
      <pre><code># MC 서비스 상태 확인
/opt/vertica/sbin/verticad status

# MC 서비스 기동
/opt/vertica/sbin/verticad start</code></pre>
    </div>
    <ul class="feature-list" style="margin-top: 1rem;">
      <li><span class="feature-list__icon">💡</span> <strong>접속 주소:</strong> <span><code>https://&lt;MC_HOST_IP&gt;:5450</code></span></li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. 데이터베이스 등록</h3>
    <p class="section-description">MC에 처음 접속한 후, 모니터링하고 관리할 Vertica 데이터베이스를 등록해야 합니다. MC의 'Import an existing Vertica database' 메뉴를 통해 데이터베이스 호스트, DB명, 사용자 정보 등을 입력하여 등록을 완료합니다.</p>
  </div>
</div>
</div>
</div>