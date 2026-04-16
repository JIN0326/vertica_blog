---
title: 모니터링 및 Grafana
layout: default
---

<div class="page-hero">
  <span class="chip">Utilization</span>
  <h1>모니터링 및 Grafana</h1>
  <p>Vertica의 상태를 파악하기 위한 주요 시스템 테이블을 알아보고, Grafana를 통해 모니터링 대시보드를 구축하는 방법을 소개합니다.</p>
</div>

<div class="content-section content-section-centered" markdown="1">
  ## Vertica 모니터링 개요

  안정적인 데이터베이스 운영을 위해서는 시스템의 상태를 지속적으로 모니터링하는 것이 필수적입니다. Vertica는 `v_monitor`와 `v_catalog` 스키마에 다양한 시스템 테이블을 제공하여 데이터베이스의 거의 모든 측면을 상세하게 조회할 수 있도록 지원합니다.

  ### 주요 모니터링 대상

  - **시스템 리소스**: 노드별 CPU, 메모리, 디스크, 네트워크 사용량 (`SYSTEM_RESOURCE_USAGE`)
  - **쿼리 성능**: 실행 중인 쿼리, 느린 쿼리, 리소스 사용량이 많은 쿼리 (`QUERY_PROFILES`, `SESSIONS`)
  - **리소스 풀 상태**: 각 리소스 풀의 메모리 사용량, 대기 큐, 동시 실행 쿼리 수 (`RESOURCE_POOL_STATUS`)
  - **데이터 저장소**: 프로젝션별 디스크 사용량, 행 수, 데이터 분산 상태 (`PROJECTION_STORAGE`)
  - **Lock 정보**: 현재 발생한 Lock과 대기 상태 (`LOCKS`)

  ---

  ## Grafana를 이용한 시각화

  시스템 테이블의 데이터를 주기적으로 수집하여 Grafana와 같은 시각화 도구로 대시보드를 만들면, 시스템 상태를 직관적으로 파악하고 이상 징후를 빠르게 감지할 수 있습니다.

  ### 구축 아키텍처 예시

  1.  **데이터 수집기 (Exporter)**: Python 스크립트나 `prometheus-vertica-exporter`와 같은 오픈소스를 사용하여 Vertica 시스템 테이블의 메트릭을 주기적으로 수집합니다.
  2.  **시계열 데이터베이스 (TSDB)**: 수집된 메트릭을 Prometheus나 InfluxDB와 같은 시계열 데이터베이스에 저장합니다.
  3.  **시각화 (Grafana)**: Grafana에서 Prometheus 또는 InfluxDB를 데이터 소스로 연결하고, 쿼리를 작성하여 대시보드를 구성합니다.

  ### Grafana 대시보드 예시

  - **Cluster Overview**: 전체 노드의 CPU/메모리 사용량, 디스크 잔여 공간, 활성 세션 수 등을 한눈에 보여줍니다.
  - **Query Performance**: 평균 쿼리 실행 시간, 실행 중인 쿼리 목록, 리소스 풀별 쿼리 수 등을 시각화합니다.
  - **Storage Dashboard**: 테이블/프로젝션별 스토리지 사용량 Top 10, 데이터 분산(Skew) 상태 등을 차트로 보여줍니다.

  > **참고**: Vertica는 Management Console(MC)이라는 공식 웹 기반 모니터링 도구도 제공합니다. MC는 기본적인 모니터링 기능을 GUI로 제공하여 편리하게 사용할 수 있습니다.
</div>