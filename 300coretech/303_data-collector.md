---
title: "Vertica Data Collector: 시스템 메트릭 수집 및 보존 정책 - Vertica Blog"
layout: default
description: "Vertica의 Data Collector(DC) 역할과 데이터 보존 정책 설정 방법을 알아봅니다. 시스템 메트릭, 쿼리 통계 등 운영 데이터를 효율적으로 관리하는 방법을 설명합니다."
keywords: "vertica, data collector, dc, retention policy, get_data_collector_policy, set_data_collector_policy"
---

<div id="data-collector" style="scroll-margin-top: 100px;"></div>

## Data Collector

<div class="architecture-section">
  <p class="section-description"><strong>Data Collector (DC)</strong>는 Vertica 데이터베이스의 핵심 유틸리티로, 시스템 메트릭, 쿼리 통계, 리소스 사용량 등 다양한 운영 데이터를 자동으로 수집하여 시스템 테이블에 저장합니다. 이 데이터는 성능 분석, 문제 해결, 용량 계획 등에 활용됩니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. 역할 및 개요</h3>
    <p class="section-description">Data Collector는 백그라운드에서 지속적으로 데이터를 수집하며, 수집된 정보는 주로 <code>v_monitor</code> 스키마의 시스템 뷰들의 참조 테이블로 제공됩니다.</p>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>시스템 메트릭:</strong> <span>CPU, 메모리, 디스크 I/O, 네트워크 사용량 등 하드웨어 리소스 정보를 수집합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>쿼리 통계:</strong> <span>실행된 쿼리의 시작/종료 시간, 사용자, 소요 시간, 사용 리소스 등 상세 정보를 기록합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>리소스 풀 사용량:</strong> <span>각 리소스 풀의 현재 상태, 큐 대기 시간, 메모리 사용량 등을 모니터링합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>자동화된 수집:</strong> <span>별도의 설정 없이 Vertica 설치 시 자동으로 활성화되어 데이터를 수집합니다.</span></li>
    </ul>
    <div class="syntax-box">
      <strong>v_monitor 뷰가 참조하는 DC 테이블 확인 예시:</strong>
      <pre><code>-- 'query_requests' 뷰의 정의를 확인하여 어떤 DC 테이블을 참조하는지 조회
select * from vs_system_views where view_name='query_requests';</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 데이터 보존 정책 (Retention Policy) 설정</h3>
    <p class="section-description">Data Collector가 수집하는 데이터는 시간이 지남에 따라 디스크 공간을 많이 차지할 수 있습니다. Vertica는 이러한 시스템 테이블의 데이터를 자동으로 관리하기 위한 보존 정책을 제공합니다. 필요에 따라 변경할 수 있습니다.</p>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 현재 보존 정책 확인</dt>
      <dd class="feature-dd">
        <p class="section-description"><code>GET_DATA_COLLECTOR_POLICY</code> 함수나 <code>data_collector</code> 시스템 테이블을 통해 현재 설정된 정책을 확인할 수 있습니다.</p>
        <div class="syntax-box">
          <pre><code>-- 특정 컴포넌트의 정책 요약 확인
SELECT GET_DATA_COLLECTOR_POLICY('ResourceAcquisitions');

-- data_collector 테이블에서 상세 정보 확인
SELECT * FROM data_collector 
WHERE table_name ILIKE '%dc_resource_acquisitions%' AND node_name ILIKE '%001';</code></pre>
        </div>
      </dd>
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 보존 정책 변경</dt>
      <dd class="feature-dd">
        <p class="section-description"><code>SET_DATA_COLLECTOR_POLICY</code> 함수를 사용하여 메모리 및 디스크 크기를, <code>SET_DATA_COLLECTOR_TIME_POLICY</code> 함수를 사용하여 수집 간격(Interval)을 조정할 수 있습니다.</p>
        <div class="syntax-box">
          <strong>정책 변경 예시:</strong>
          <pre><code>-- 메모리 및 디스크 크기 조정
SELECT SET_DATA_COLLECTOR_POLICY('ResourceAcquisitions', '1000', '10000');

-- 특정 컴포넌트의 수집 간격 조정
SELECT SET_DATA_COLLECTOR_TIME_POLICY('ResourceAcquisitions', '1 day'::interval);

-- 전체 컴포넌트의 수집 간격 일괄 조정
SELECT SET_DATA_COLLECTOR_TIME_POLICY('1 day'::interval);

-- 수집 간격을 기본값으로 원복
SELECT SET_DATA_COLLECTOR_TIME_POLICY('-1');</code></pre>
        </div>
      </dd>
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 고급 예제: 사용량 기반 디스크 크기 조정</dt>
      <dd class="feature-dd">
        <p class="section-description">아래 쿼리는 <code>user_sessions</code>, <code>query_requests</code>와 같이 데이터가 빠르게 쌓이는 주요 시스템 테이블에 대해, 일일 사용량(<code>kb_per_day</code>)을 기반으로 5일치 데이터를 보관할 수 있는 디스크 크기를 계산하고, 이를 적용할 수 있는 <code>SET_DATA_COLLECTOR_POLICY</code> 구문을 동적으로 생성합니다.</p>
        <div class="syntax-box">
          <pre><code>SELECT 
    x.table_name, 
    '--SELECT SET_DATA_COLLECTOR_POLICY('''|| component || ''', ''' || memory_buffer_size_kb || ''', ''' || (kb_per_day::INT * 5) || ''');' AS set_policy_command
FROM data_collector x
WHERE x.kb_per_day <> 0
  AND x.table_name IN (
    'dc_session_starts', 'dc_requests_issued', 'dc_requests_completed', 
    'dc_errors', 'dc_resource_acquisitions'
  )
LIMIT 1 OVER(PARTITION BY x.table_name ORDER BY kb_per_day DESC);</code></pre>
        </div>
      </dd>
    </dl>
  </div>
</div>
