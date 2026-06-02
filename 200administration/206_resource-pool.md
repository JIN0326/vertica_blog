---
title: "Vertica Resource Pool: 리소스 풀을 이용한 워크로드 관리 - Vertica Blog"
layout: default
description: "Vertica의 Resource Pool(리소스 풀)을 사용하여 워크로드를 관리하는 방법을 알아봅니다. 메모리, 동시성, 쿼리 실행 시간 등을 제어하여 시스템 자원을 효율적으로 배분합니다."
keywords: "vertica, resource pool, workload management, memorysize, maxconcurrency, plannedconcurrency, runtimecap"
---

<div id="resource-pool" style="scroll-margin-top: 100px;"></div>

## Resource Pool

<div class="architecture-section">
  <p class="section-description"><strong>Resource Pool(리소스 풀)</strong>은 버티카 데이터베이스에서 작업(Workload)을 관리하기 위해 할당하는 메모리 및 스레드 공간입니다. 기본적으로 <code>general</code>, <code>sysquery</code>, <code>tm</code>(Tuple Mover) 등의 Built-in 리소스 풀이 제공되며, 업무와 사용자 성격에 맞춰 커스텀 풀을 추가 생성하는 것이 핵심 운영 전략입니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. Resource Pool 구성 및 관리</h3>
    <p class="section-description">효율적인 자원 배분을 위해 풀을 생성(<code>CREATE</code>), 수정(<code>ALTER</code>), 삭제(<code>DROP</code>)할 수 있습니다.</p>
    <div class="syntax-box">
      <strong>기본 생성 구문:</strong>
      <pre><code>CREATE RESOURCE POOL pool_name [ parameter_name setting ]...;</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 파라미터</dt>
      <dd class="feature-dd">
        <strong>MEMORYSIZE / MAXMEMORYSIZE:</strong> 풀의 초기 메모리 크기와 사용 가능한 최대 메모리 크기<br>
        <strong>MAXQUERYMEMORYSIZE:</strong> 단일 쿼리가 사용할 수 있는 최대 메모리 (초과 시 쿼리 취소)<br>
        <strong>MAXCONCURRENCY:</strong> 풀에서 동시에 실행될 수 있는 최대 세션/애플리케이션 개수<br>
        <strong>PLANNEDCONCURRENCY:</strong> 풀 내 메모리를 분할할 동시성 기준값 (1/n)<br>
        <strong>QUEUETIMEOUT / RUNTIMECAP:</strong> 대기 큐 타임아웃 및 쿼리 최대 실행 시간 제한
      </dd>
    </dl>
    <ul class="feature-list" style="margin-top: 1rem;">
      <li><span class="feature-list__icon">💡</span> <strong>운영 Best Practice:</strong> <span>배치 작업 시간대와 일반 사용자 접속 시간대의 리소스 풀 구성을 스크립트로 분리·조정하여 시스템 사용을 극대화합니다.</span></li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. Resource Pool 권한 부여 및 할당</h3>
    <p class="section-description">생성된 리소스 풀은 다수의 사용자가 공유할 수 있으나, 한 사용자는 동시에 하나의 풀만 사용할 수 있습니다.</p>
    <p class="example-label">적용 예시</p>
    <pre><code>-- 1. 리소스 풀 생성
CREATE RESOURCE POOL batch_pool 
    MAXMEMORYSIZE '10G' MAXCONCURRENCY 4;

-- 2. 사용자에게 리소스 풀 사용 권한 부여 (GRANT USAGE)
GRANT USAGE ON RESOURCE POOL batch_pool TO batch_user;

-- 3. 사용자에게 기본 리소스 풀 할당 (ALTER USER)
ALTER USER batch_user RESOURCE POOL batch_pool;</code></pre>
  </div>
</div>