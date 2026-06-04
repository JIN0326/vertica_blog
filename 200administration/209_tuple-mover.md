---
title: "Vertica Tuple Mover: Mergeout과 Purge를 통한 최적화 - Vertica Blog"
layout: default
description: "Vertica의 백그라운드 프로세스인 Tuple Mover의 역할을 알아봅니다. Mergeout을 통한 데이터 컨테이너 병합과 Purge를 통한 삭제 데이터 공간 회수 방법을 설명합니다."
keywords: "vertica, tuple mover, mergeout, purge, ros, wos, do_tm_task, purge_table"
canonical_url: "https://jin0326.github.io/vertica_blog/200administration/200administration#tuple-mover"
---

<div id="tuple-mover" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody">
## Tuple Mover (Mergeout & Purge)

<div class="architecture-section">
  <p class="section-description"><strong>Tuple Mover</strong>는 Vertica의 핵심적인 백그라운드 프로세스로, 데이터베이스의 성능과 효율성을 유지하는 데 중요한 역할을 합니다. 주요 작업으로는 <strong>Mergeout</strong>과 <strong>Purge</strong>가 있습니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. Tuple Mover의 역할</h3>
    <p class="section-description">Tuple Mover는 다음과 같은 자동화된 작업을 수행합니다.</p>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>Mergeout:</strong> <span>ROS에 있는 작은 데이터 컨테이너들을 병합하여 더 크고 효율적인 컨테이너로 만듭니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>Purge:</strong> <span><code>DELETE</code> 문에 의해 삭제 표시된 데이터를 디스크에서 영구적으로 제거하여 공간을 회수합니다.</span></li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. Mergeout</h3>
    <p class="section-description">Mergeout은 여러 개의 작은 ROS 컨테이너를 하나의 큰 컨테이너로 병합하는 작업입니다. 이 과정은 쿼리 성능을 향상시키고 데이터 압축률을 높이는 데 기여합니다.</p>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> Mergeout의 이점</dt>
      <dd class="feature-dd">
        <strong>쿼리 성능 향상:</strong> 쿼리 실행 시 스캔해야 할 파일(ROS 컨테이너)의 수가 줄어들어 I/O가 감소하고 응답 속도가 빨라집니다.<br>
        <strong>압축 효율 증가:</strong> 데이터를 재정렬하고 다시 압축하여 디스크 공간 사용량을 줄입니다.
      </dd>
    </dl>
    <div class="syntax-box">
      <strong>수동 Mergeout 실행:</strong>
      <pre><code>-- 특정 테이블에 대해 Mergeout 작업을 수동으로 트리거
SELECT DO_TM_TASK('mergeout', 'public.sales');</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. Purge</h3>
    <p class="section-description">Vertica에서 <code>DELETE</code> 문은 데이터를 즉시 물리적으로 삭제하지 않고, 삭제되었다는 표시(epoch)만 남깁니다. <strong>Purge</strong> 작업은 이렇게 삭제 표시된 데이터를 디스크에서 영구적으로 제거하여 실제 디스크 공간을 회수하는 역할을 합니다.</p>
    <div class="syntax-box">
      <strong>수동 Purge 실행:</strong>
      <pre><code>-- 테이블에서 삭제된 데이터를 영구적으로 제거
SELECT PURGE_TABLE('public.sales');

-- 특정 파티션의 삭제된 데이터만 제거
SELECT PURGE_PARTITION('public.sales', '2023-01-01');</code></pre>
    </div>
    <ul class="feature-list" style="margin-top: 1rem;">
      <li><span class="feature-list__icon">💡</span> <strong>Tip:</strong> <span>대규모 <code>DELETE</code> 작업 후에는 디스크 공간을 즉시 회수하기 위해 수동으로 Purge 작업을 실행하는 것이 좋습니다.</span></li>
    </ul>
  </div>
</div>
</div>
</div>