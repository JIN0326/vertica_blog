---
title: "Vertica Locks: 잠금 메커니즘과 동시성 제어 - Vertica Blog"
layout: default
description: "Vertica의 동시성 제어를 위한 잠금(Lock) 메커니즘을 알아봅니다. I, S, U, X, O 잠금 모드의 종류와 호환성, 그리고 LOCKS 시스템 테이블을 통한 모니터링 방법을 설명합니다."
keywords: "vertica, locks, concurrency control, deadlock, lock modes, share lock, exclusive lock, update lock"
canonical_url: "https://jin0326.github.io/vertica_blog/200administration/200administration#locks"
---

<div id="locks" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody">
## Locks

<div class="architecture-section">
  <p class="section-description">Vertica는 동시성 제어(Concurrency Control)를 위해 <strong>잠금(Lock)</strong> 메커니즘을 사용합니다. 여러 세션이 동시에 동일한 데이터베이스 객체에 접근할 때 데이터의 일관성과 무결성을 보장하기 위해, Vertica는 작업의 종류에 따라 자동으로 다양한 수준의 잠금을 획득하고 해제합니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. 주요 잠금 모드 (Lock Modes)</h3>
    <p class="section-description">Vertica는 계층적인 잠금 모델을 사용하며, 각 작업의 성격에 따라 다음 5가지 주요 잠금 모드를 사용합니다.</p>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> I (Intent) Lock</dt>
      <dd class="feature-dd">
        테이블과 같은 상위 객체에 설정되며, 하위 객체(예: 특정 파티션)에 더 강력한 잠금(S, U, X)을 설정하려는 <strong>의도</strong>를 나타냅니다. 다른 세션이 상위 객체 전체에 대한 배타적 잠금(X Lock)을 획득하는 것을 방지합니다.
      </dd>
      <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> S (Share) Lock</dt>
      <dd class="feature-dd">
        주로 <code>SELECT</code>와 같은 읽기 작업에 사용됩니다. 여러 세션이 동시에 동일한 객체에 대한 S Lock을 획득할 수 있어, 읽기 작업의 동시성을 극대화합니다.
      </dd>
      <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> U (Update) Lock</dt>
      <dd class="feature-dd">
        <code>UPDATE</code>, <code>DELETE</code>, <code>MERGE</code>와 같은 DML 작업에 사용됩니다. 한 객체에 대해 오직 하나의 세션만 U Lock을 가질 수 있으며, 이는 두 세션이 서로를 기다리는 교착 상태(Deadlock)를 방지하는 역할을 합니다. U Lock은 실제 데이터를 수정하기 직전에 X Lock으로 승격될 수 있습니다.
      </dd>
      <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> X (Exclusive) Lock</dt>
      <dd class="feature-dd">
        <code>ALTER TABLE</code>, <code>DROP TABLE</code>, <code>TRUNCATE</code>와 같은 DDL 작업이나 실제 데이터 수정 시에 사용되는 가장 강력한 잠금입니다. 한 세션이 X Lock을 획득하면, 다른 어떤 세션도 해당 객체에 대한 어떠한 종류의 잠금도 획득할 수 없습니다.
      </dd>
      <dt class="feature-dt"><span class="feature-dt__icon">🔹</span> O (Ownership) Lock</dt>
      <dd class="feature-dd">
        객체가 생성될 때 획득하는 특수한 잠금으로, 해당 객체에 대한 소유권을 나타냅니다.
      </dd>
    </dl>
    <p class="section-description" style="margin-top: 2rem; margin-bottom: 1rem;">
      아래는 각 잠금 모드 간의 호환성을 나타내는 매트릭스입니다. 'Yes'는 두 잠금이 충돌 없이 공존할 수 있음을, 'No'는 충돌이 발생함을 의미합니다.
    </p>
    <div class="image-box-styled">
      <img src="/vertica_blog/assets/images/locks.png" alt="Vertica Lock Compatibility Matrix">
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 잠금 모니터링 (Lock Monitoring)</h3>
    <p class="section-description">데이터베이스에서 현재 활성화된 잠금이나 잠금 대기 상태를 확인하는 것은 성능 문제 해결 및 병목 현상 분석에 매우 중요합니다. Vertica는 <code>LOCKS</code> 시스템 테이블을 통해 현재 잠금 상태를 모니터링할 수 있는 뷰를 제공합니다.</p>
    <div class="syntax-box">
      <strong>현재 잠금 상태 조회:</strong>
      <pre><code>-- 현재 부여된 모든 잠금 확인
SELECT * FROM locks;</code></pre>
    </div>
    <ul class="feature-list" style="margin-top: 1rem;">
      <li><span class="feature-list__icon">💡</span> <strong>Tip:</strong> <span>장시간 실행되는 쿼리나 DML 작업으로 인해 다른 세션들이 잠금 대기(Lock Wait) 상태에 빠지는 경우가 많습니다. <code>v_monitor.locks</code>와 <code>v_monitor.sessions</code>를 조인하여 잠금을 보유한 세션의 상세 정보를 파악하고 문제를 해결할 수 있습니다.</span></li>
    </ul>
  </div>
</div>
</div>
</div>