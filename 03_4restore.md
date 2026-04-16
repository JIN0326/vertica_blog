---
title: 데이터 복구 (Restore)
layout: default
---

<div class="page-hero">
  <span class="chip">CoreTech</span>
  <h1>데이터 복구 (Restore)</h1>
  <p>vbr 유틸리티를 사용하여 전체 데이터베이스, 특정 스키마/테이블 등 원하는 단위로 데이터를 복구하는 방법을 안내합니다.</p>
</div>

<div class="content-section content-section-centered" markdown="1">
  ## vbr 유틸리티 개요

  `vbr`은 Vertica 데이터베이스의 백업 및 복구를 위한 강력한 커맨드 라인 유틸리티입니다. `vbr`을 사용하면 전체 데이터베이스, 특정 스키마나 테이블 등 다양한 단위로 데이터를 안정적으로 복구할 수 있습니다.

  - **스냅샷 기반**: 특정 시점의 데이터 스냅샷을 사용하여 복구하므로 일관성이 보장됩니다.
  - **다양한 복구 레벨**: 전체 DB 복구, 스키마 단위 복구, 테이블 단위 복구를 모두 지원합니다.
  - **유연한 대상**: 로컬 스토리지, NFS, S3 호환 오브젝트 스토리지 등 다양한 백업 위치에서 데이터를 복구할 수 있습니다.

  ---

  ## 복구 절차

  데이터 복구는 일반적으로 다음 단계로 진행됩니다.

  1.  **설정 파일 준비**: 복구 작업을 위한 `.ini` 설정 파일을 준비합니다. 이 파일에는 백업 위치, DB 정보, 복구할 객체 등의 정보가 포함됩니다.
  2.  **복구 대상 DB 정지**: 데이터 일관성을 위해 복구를 진행할 데이터베이스를 정지합니다.
  3.  **vbr 복구 명령어 실행**: `vbr --task restore` 명령어를 사용하여 복구를 시작합니다.
  4.  **DB 재시작 및 확인**: 복구가 완료되면 데이터베이스를 재시작하고 데이터가 정상적으로 복구되었는지 확인합니다.

  ---

  ## 전체 데이터베이스 복구 예시

  `my_backup.ini` 설정 파일을 사용하여 전체 데이터베이스를 복구하는 예시입니다.

  **1. 설정 파일 (`my_backup.ini`) 예시**
  ```ini
  [Mapping]
  v_vdb_node0001 = 192.168.1.101
  v_vdb_node0002 = 192.168.1.102
  v_vdb_node0003 = 192.168.1.103

  [Database]
  dbName = VDB
  dbUser = dbadmin

  [Transmission]

  [Misc]
  snapshotName = my_full_backup_20240520
  restorePointLimit = 1
  ```

  **2. 복구 명령어 실행**
  ```bash
  # DB 정지
  admintools -t stop_db -d VDB -p <password>

  # vbr 복구 실행
  /opt/vertica/bin/vbr --task restore --config-file my_backup.ini

  # DB 시작
  admintools -t start_db -d VDB -p <password>
  ```

  ### 테이블 단위 복구

  특정 테이블만 복구하려면 설정 파일에 `objects` 파라미터를 추가합니다.

  ```ini
  [Misc]
  snapshotName = my_full_backup_20240520
  objects = public.sales, public.products
  ```
</div>