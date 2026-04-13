---
title: Vertica Fundamentals
layout: default
---

<div class="page-hero">
  <span class="chip">Vertica Study Program</span>
  <h1>버티카 기초 교육</h1>
  <p>Vertica, 대용량 데이터 분석을 위한 컬럼 기반 DB의 시작입니다. 핵심 개념부터 운영, 쿼리, 백업, 적재까지 기초를 빠르게 정리하세요.</p>
  <div class="actions">
    <a class="button primary" href="{{ '/what-is-vertica' | relative_url }}">학습하기</a>
    <a class="button secondary" href="{{ '/technology' | relative_url }}">학습가이드</a>
  </div>
</div>

<p class="copyright">© 2026 Vertica Basic Study. All rights reserved.</p>

## Fundamentals 학습 개요

Vertica 기초 학습을 위해 아래 주요 항목을 빠르게 정리했습니다. 이 가이드는 설치/구성, 운영, 권한, 백업, 성능, 로드/언로드 등 핵심 개념을 중심으로 구성되어 있습니다.

## 1. Vertica DB 구성과 초기 설정

- Vertica는 여러 노드로 구성된 분산형 데이터베이스입니다.
- 기본 설치 후 `configuration parameter`, `profile`, `user`, `role`, `resource pool`을 설정해야 합니다.
- `private/public IP`와 `export address` 구성은 클러스터 통신을 위해 필수입니다.
- EON 모드에서는 Object Storage 기반 스토리지와 `minio` 같은 S3 호환 스토리지 구성이 필요합니다.

## 2. 사용자와 권한 관리

- 사용자와 역할(Role)을 생성하고 객체별 권한을 `grant`로 부여합니다.
- `schema`, `table` 단위로 권한을 설정하며 `WITH GRANT OPTION`을 필요에 따라 사용합니다.
- 권한 체계는 보안과 운영 편의성을 모두 고려해 설계해야 합니다.

## 3. 백업, 복구, 업그레이드

- Vertica 백업은 `vbr`을 통해 수행됩니다.
- `full`, `object`, `copycluster` 백업 전략을 적절히 조합합니다.
- `Scrutinize`를 생성하여 로그와 백업 결과를 분석합니다.
- 업그레이드 전 백업을 반드시 준비하고, 완료 후 검증 절차를 수행합니다.

## 4. 운영 모니터링과 상태 확인

- `sessions`, `user_sessions`를 점검하여 연결 상태와 활동을 모니터링합니다.
- `transaction`, `statement`, `session`, `rollback`, `commit` 관계를 이해해야 안정적 운영이 가능합니다.
- `system tables`를 활용해 쿼리 상태, 에러, 로드, 라이선스 정보를 확인합니다.
- 정기 점검 쿼리로 DB와 MC 상태를 주기적으로 점검합니다.

## 5. 쿼리와 성능 튜닝

- Vertica MPP 구조와 `projection` 개념을 이해하면 성능을 개선할 수 있습니다.
- `EXPLAIN`으로 쿼리 실행 계획을 분석하고 튜닝합니다.
- `functions`을 활용해 데이터 처리와 분석을 효율적으로 수행합니다.
- `Comments` 구문과 `dynamic query` 작성은 복잡한 SQL 작업에서 유용합니다.

## 6. 데이터 적재와 내보내기

- `COPY` 구문으로 데이터를 Vertica에 적재합니다.
- `delimiter`, `recordterminator`, `null 처리`, `filler`, 특수문자 처리 등 옵션을 활용합니다.
- `copy from vertica`와 `export to vertica`로 데이터 이동을 수행합니다.
- `CTAS`, `LIKE`, `COPY_TABLE`로 테이블 생성 및 관리를 수행합니다.

## 7. 검사와 성능 테스트

- `Vxperf`로 `vioper`, `vnetperf`, `vcpuperf` 성능 지표를 확인합니다.
- 로그 파일(install, create, start, vbr 수행 시 로그)을 이해하고 경로를 확인합니다.
- 포트 변경 테스트 및 네트워크 구성을 통해 운영 환경 안정성을 검증합니다.
