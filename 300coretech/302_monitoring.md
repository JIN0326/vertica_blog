---
title: "Vertica Monitoring: MC 및 Grafana를 활용한 모니터링 - Vertica Blog"
layout: default
description: "Vertica 데이터베이스 모니터링 방법을 알아봅니다. Management Console(MC)의 실시간 현황 확인과 Grafana를 활용한 맞춤형 대시보드 구축 방법을 설명합니다."
keywords: "vertica, monitoring, management console, mc, grafana, dashboard, v_monitor"
canonical_url: "https://jin0326.github.io/vertica_blog/300coretech/300coretech#monitoring"
---

<div id="monitoring" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody">
## Monitoring

<div class="architecture-section">
    <p class="section-description">안정적인 데이터베이스 운영을 위해서는 Management Console(MC)과 Grafana를 통한 지속적인 모니터링이 필수적입니다.</p>

  <div class="architecture-subsection">
      <h3 class="section-subtitle">1. 모니터링 콘솔 (MC)</h3>
      <p class="section-description">Management Console(MC)은 웹 브라우저 기반의 통합 모니터링 및 클러스터 관리 도구입니다.</p>
      
  <div class="image-box-styled">
        <img src="/vertica_blog/assets/images/monitoring_1.png" alt="Vertica Management Console (MC) 화면">
      </div>

  <dl class="feature-dl">
        <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 핵심 기능</dt>
        <dd class="feature-dd">
          <strong>Overview & Activity:</strong> 하드웨어 자원과 리소스 풀 사용량, 쿼리 상태를 실시간으로 확인합니다.<br>
          <strong>Query Execution:</strong> UI 내에서 직접 SQL을 실행하고 시각적인 트리 구조로 실행 계획을 분석합니다.
        </dd>
      </dl>
    </div>

  <div class="architecture-subsection">
        <h3 class="section-subtitle">2. Grafana 대시보드</h3>
        <p class="section-description">Grafana는 오픈소스 시각화 도구로, Vertica의 시스템 테이블 데이터를 활용하여 맞춤형 모니터링 대시보드를 구축할 수 있습니다.</p>
        
  <div class="image-box-styled">
            <img src="/vertica_blog/assets/images/monitoring_2.png" alt="Vertica Grafana Dashboard">
        </div>

  <dl class="feature-dl">
            <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 활용 지표</dt>
            <dd class="feature-dd">
                <strong>실시간 성능 지표:</strong> CPU, 메모리, 디스크 I/O, 네트워크 등 핵심 시스템 리소스 사용량을 시각적으로 추적합니다.<br>
                <strong>쿼리 및 세션 분석:</strong> 활성 쿼리 수, 리소스 풀별 점유율, 사용자 세션 등 상세한 워크로드 분석이 가능합니다.<br>
                <strong>유연한 커스터마이징:</strong> Vertica의 다양한 시스템 테이블(v_monitor, v_internal)과 연동하여 필요한 모든 지표를 자유롭게 추가하고 시각화할 수 있습니다.
            </dd>
        </dl>
    </div>
  </div>
</div>
</div>
