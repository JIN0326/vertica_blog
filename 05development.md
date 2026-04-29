---
title: Vertica SQL Development
layout: default
---

<div class="page-hero">
  <span class="chip">Development</span>
  <h1>Vertica SQL Development</h1>
  <p>Vertica 아키텍처에 최적화된 SQL 작성 방법과 성능 극대화를 위한 개발 Best Practices를 알아봅니다.</p>
</div>

<div class="page-layout">
<div class="content-section" markdown="1">

<div id="architecture-sql" style="scroll-margin-top: 100px;"></div>

## SQL 수행 아키텍처 이해

<div class="architecture-section" markdown="1">
  <p class="section-description">Vertica는 MPP(Massively Parallel Processing) 구조로 작동하므로, 쿼리 작성 시 데이터가 노드 간에 어떻게 분산되고 처리되는지 이해하는 것이 중요합니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">Initiator와 Executor 노드</h3>
    <p class="section-description">Application이 접속한 노드는 <strong>Initiator</strong>가 되어 쿼리 플랜을 생성하고 다른 <strong>Executor</strong> 노드들에게 전달합니다. 모든 노드는 동시에 Initiator이자 Executor로 동작할 수 있습니다.</p>
    
    
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">데이터 저장 구조 (ROS)</h3>
    <p class="section-description">Vertica의 디스크 저장 영역인 <strong>ROS(Read Optimized Store)</strong>는 컬럼 단위로 데이터를 압축하여 저장합니다. 필요한 컬럼만 물리적으로 I/O를 발생시키기 때문에 대용량 분석에 최적화되어 있습니다.</p>
  </div>
</div>

<hr style="margin: 3rem 0;">

<div id="query-best-practices" style="scroll-margin-top: 100px;"></div>

## 효율적인 쿼리 작성 (Best Practices)

<div class="architecture-section" markdown="1">
  <p class="section-description">성능을 극대화하기 위해 SQL 작성 시 반드시 지켜야 할 원칙들입니다.</p>

  <div class="architecture-subsection">
    <h4 class="section-subtitle">1. SELECT * 사용 지양</h4>
    <ul class="feature-list">
      <li><span class="feature-list__icon">⚠️</span> <span>불필요한 컬럼 읽기를 방지하기 위해 <strong>반드시 필요한 컬럼만 명시</strong>합니다.</span></li>
      <li><span class="feature-list__icon">⚠️</span> <span>컬럼 스토리지의 장점을 살리려면 I/O 양을 최소화해야 합니다.</span></li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h4 class="section-subtitle">2. 데이터 타입 최적화</h4>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <span>소수점이 없는 18자리 이하 숫자는 <strong>NUMERIC 대신 INTEGER</strong>를 사용합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <span>VARCHAR 컬럼 길이를 과도하게 크게 잡지 않도록 주의합니다 (메모리 사용량에 영향).</span></li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h4 class="section-subtitle">3. 분석 함수(Analytic Functions) 활용</h4>
    <p class="section-description">Self-Join이 필요한 복잡한 로직은 <code>OVER()</code> 절을 사용하는 분석 함수로 대체하여 가독성과 성능을 동시에 높입니다.</p>
    <div class="syntax-box">
      <strong>권장 예시:</strong>
      <pre><code>SELECT user_id, 
       sal, 
       RANK() OVER(ORDER BY sal DESC) as rank 
FROM salary;</code></pre>
    </div>
  </div>
</div>

<hr style="margin: 3rem 0;">

<div id="dml-optimization" style="scroll-margin-top: 100px;"></div>

## DML 최적화 및 적재 가이드

<div class="architecture-section" markdown="1">
  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. Bulk 처리 원칙</h3>
    <p class="section-description">Vertica는 소량의 데이터를 빈번하게 <code>INSERT/UPDATE/DELETE</code> 하는 작업에 취약합니다.</p>
    <ul class="feature-list">
      <li><span class="feature-list__icon">❌</span> <span><strong>지양:</strong> 단건 단위의 DML 처리</span></li>
      <li><span class="feature-list__icon">✅</span> <span><strong>권장:</strong> <code>COPY</code> 구문을 이용한 Bulk 적재 또는 대단위 Batch 처리</span></li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 시퀀스(Sequence) 성능 설정</h3>
    <p class="section-description">시퀀스 사용 시 성능 향상을 위해 <strong>Cache</strong>를 충분히 설정하는 것이 좋습니다 (노드별 기본값 250,000 권장).</p>
  </div>
</div>


<aside class="page-sidebar">
  <div class="sidebar-panel">
    <h3>On this page</h3>
    <ul>
      <li><a href="#architecture-sql">SQL 아키텍처</a></li>
      <li><a href="#query-best-practices">쿼리 Best Practices</a></li>
      <li><a href="#dml-optimization">DML 최적화 가이드</a></li>
    </ul>
  </div>
</aside>
</div>