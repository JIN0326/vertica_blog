---
title: "Vertica Backup & Restore: vbr을 이용한 백업 및 복구 - Vertica Blog"
layout: default
description: "Vertica의 vbr 유틸리티를 사용한 백업 및 복구 방법을 알아봅니다. 전체 백업, 객체 단위 백업, 하드링크 백업, 클라우드 백업 등 다양한 시나리오를 설명합니다."
keywords: "vertica, backup, restore, vbr, full backup, object-level backup, hard-link, cloud backup, eon mode"
canonical_url: "https://jin0326.github.io/vertica_blog/200administration/200administration#backup-restore"
---

<div id="backup-restore" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody" markdown="1">
## Backup & Restore
<div class="architecture-section">
  <p class="section-description">Vertica는 <code>vbr</code> 유틸리티를 통해 다양한 백업 및 복구 시나리오를 지원합니다. 백업 방식은 크게 전체 DB를 백업하는 <strong>Full Backup</strong>과 특정 객체만 백업하는 <strong>Object-Level Backup</strong>으로 나뉩니다. 모든 설정은 <code>.ini</code> 구성 파일을 통해 관리되며, 다양한 예제 파일이 <code>/opt/vertica/share/vbr/example_configs</code> 경로에 제공됩니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. Full Backup (전체 백업)</h3>
    <p class="section-description">데이터베이스 전체를 백업하며, 복구 시에는 DB가 중단된 상태여야 합니다. 첫 백업 이후 동일한 위치에 백업하면 자동으로 증분 백업이 수행됩니다.</p>
    
    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">구성 파일 예시 (backup_restore_full_local.ini)</h4>
      <div class="syntax-box">
        <pre><code>[Mapping]
# 각 DB 노드의 데이터를 백업할 로컬 경로를 지정합니다.
v_entdb_node0001 = [&lt;DB_IP_1&gt;]:/data/BACKUP
v_entdb_node0002 = [&lt;DB_IP_2&gt;]:/data/BACKUP
v_entdb_node0003 = [&lt;DB_IP_3&gt;]:/data/BACKUP

[Misc]
snapshotName = backup_snapshot
# DB 패스워드가 저장된 파일 경로를 지정합니다.
passwordFile = /home/vertica/Backups/.pwd.txt
# 보관할 백업본의 개수를 지정합니다. (현재 + 6개)
restorePointLimit = 6

[Database]
dbName = ENTDB
dbUser = vertica
# 비밀번호를 프롬프트에서 직접 입력하지 않도록 설정합니다.
dbPromptForPassword = False</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">실행 절차</h4>
      <div class="syntax-box">
        <pre><code># 1. 패스워드 파일 생성 및 보안 권한 설정
echo "[Passwords]" > /home/vertica/Backups/.pwd.txt
echo "dbPassword=your_password" >> /home/vertica/Backups/.pwd.txt
chmod 600 /home/vertica/Backups/.pwd.txt

# 2. 백업 위치 초기화 (최초 1회)
vbr -t init -c backup_restore_full_local.ini

# 3. 백업 실행
vbr -t backup -c backup_restore_full_local.ini

# 4. 복구 실행 (DB 중단 후)
vbr -t restore -c backup_restore_full_local.ini

# 5. 특정 시점(Archive)으로 복구
# 먼저 백업 리스트 확인
vbr -t listbackup -c backup_restore_full_local.ini
# 특정 백업본으로 복구
vbr -t restore -c backup_restore_full_local.ini --archive=20240729_152200</code></pre>
      </div>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. Object-Level Backup (객체 단위 백업)</h3>
    <p class="section-description">특정 스키마나 테이블 단위로 백업하며, DB가 기동 중인 상태에서도 복구가 가능합니다. (HDFS 스토리지에서는 지원되지 않습니다.)</p>
    
    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">구성 파일 예시 (backup_restore_object_local.ini)</h4>
      <div class="syntax-box">
        <pre><code>[Mapping]
v_entdb_node0001 = [&lt;DB_IP_1&gt;]:/data/BACKUP
...
[Misc]
snapshotName = backup_snapshot
# 백업할 객체(스키마 또는 테이블) 지정
objects = public.sales, marketing.campaigns
# 복구 시 동일 객체가 존재할 경우 처리 방식 (coexist: 이름 변경 후 생성)
objectRestoreMode = coexist
passwordFile = /home/vertica/Backups/.pwd.txt
restorePointLimit = 4
# 백업 전 디스크 여유 공간 체크 활성화
enableFreeSpaceCheck = True

[Database]
dbName = ENTDB
dbUser = vertica
dbPromptForPassword = False</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">실행 절차</h4>
      <div class="syntax-box">
        <pre><code># 백업 실행
vbr -t backup -c backup_restore_object_local.ini

# 복구 실행 (DB 기동 중)
# 모든 객체 복구
vbr -t restore -c backup_restore_object_local.ini
# 특정 객체만 복구
vbr -t restore -c backup_restore_object_local.ini --restore-objects=public.sales</code></pre>
      </div>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. Hard-Link Backup (Enterprise Mode)</h3>
    <p class="section-description">Enterprise Mode에서 지원되는 빠른 로컬 백업 방식입니다. 데이터 파일을 복사하는 대신 원본 파일에 대한 하드 링크를 생성하여 백업 시간을 크게 단축합니다.</p>
    
    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">구성 파일 예시 (backup_restore_full_hardlink.ini)</h4>
      <div class="syntax-box">
        <pre><code>[Mapping]
v_entdb_node0001 = [&lt;DB_IP_1&gt;]:/data/BACKUP
...
[Transmission]
# 하드 링크 백업 활성화
hardLinkLocal = True
# 하드 링크 생성 실패 시 파일 복사 시도 안 함
copyOnHardLinkFailure = False

[Database]
dbName = ENTDB
dbUser = vertica

[Misc]
snapshotName = backup_snapshot
passwordFile = /home/vertica/Backups/.pwd.txt</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">실행 절차</h4>
      <div class="syntax-box">
        <pre><code># 백업 실행
vbr -t backup -c backup_restore_full_hardlink.ini

# 복구 실행 (DB 중단 후)
vbr -t restore -c backup_restore_full_hardlink.ini</code></pre>
      </div>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">4. Cloud Backup (Eon Mode)</h3>
    <p class="section-description">Eon Mode에서 지원되는 방식으로, Communal Storage(S3 등)에 저장된 데이터를 다른 클라우드 스토리지 위치로 백업합니다.</p>
    
    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">사전 환경 변수 설정</h4>
      <p class="section-description">vbr을 실행할 셸에서 원본(Communal) 및 대상(Backup) 스토리지에 접근하기 위한 인증 정보를 환경 변수로 설정해야 합니다.</p>
      <div class="syntax-box">
        <pre><code># 대상 백업 스토리지 인증 정보
export VBR_BACKUP_STORAGE_ACCESS_KEY_ID=minioadmin
export VBR_BACKUP_STORAGE_SECRET_ACCESS_KEY=your_secret_key
export VBR_BACKUP_STORAGE_ENDPOINT_URL=http://&lt;STORAGE_IP&gt;:9000

# 원본 Communal 스토리지 인증 정보
export VBR_COMMUNAL_STORAGE_ACCESS_KEY_ID=minioadmin
export VBR_COMMUNAL_STORAGE_SECRET_ACCESS_KEY=your_secret_key
export VBR_COMMUNAL_STORAGE_ENDPOINT_URL=http://&lt;STORAGE_IP&gt;:9000</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">구성 파일 예시 (backup_restore_cloud_storage.ini)</h4>
      <div class="syntax-box">
        <pre><code>[CloudStorage]
# 백업 데이터가 저장될 클라우드 경로
cloud_storage_backup_path = s3://backup-bucket/
# vbr 메타데이터를 저장할 로컬 경로
cloud_storage_backup_file_system_path = /home/vertica/Backups/backup_cloud/
# 백업/복구 시 동시 전송 스레드 수
cloud_storage_concurrency_backup = 10
cloud_storage_concurrency_restore = 10

[Misc]
snapshotName = backup_snapshot
restorePointLimit = 2
passwordFile = /home/vertica/Backups/.pwd.txt

[Database]
dbName = TESTDB
dbUser = vertica
dbPromptForPassword = False</code></pre>
      </div>
    </div>

    <div class="step-section">
      <h4 class="step-title">실행 절차</h4>
      <div class="syntax-box">
        <pre><code># 백업 위치 초기화 (최초 1회, 기존 백업이 있으면 --cloud-force-init 사용)
vbr -t init -c backup_restore_cloud_storage.ini

# 백업 실행
vbr -t backup -c backup_restore_cloud_storage.ini

# 복구 실행
vbr -t restore -c backup_restore_cloud_storage.ini</code></pre>
      </div>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">5. 백업 히스토리 조회</h3>
    <p class="section-description"><code>dc_vbr_invocations</code> 시스템 테이블을 통해 vbr 작업의 시작/종료 시간, 상태, 소요 시간 등의 이력을 조회할 수 있습니다.</p>
    <div class="syntax-box">
      <strong>백업 이력 조회 쿼리:</strong>
      <pre><code>SELECT 
    vbr.start_time,
    vbr.end_time,
    DECODE(status, 'Fail', '실패', '성공', DECODE(vbr.end_time, NULL, '진행중', '성공')) AS status,
    TIMESTAMPDIFF('s', vbr.start_time, vbr.end_time) AS duration_sec,
    dob.snapshot_name,
    dob.include_patterns,
    ROUND(dob.bytes_transferred/1024^3, 2) AS size_gb
FROM (
    SELECT 
        identifier,
        MAX(DECODE(status, 'Start', time, NULL)) AS start_time,
        MAX(DECODE(status, 'Complete', time, NULL)) AS end_time,
        MAX(DECODE(status, 'Fail', '실패', '성공')) AS status
    FROM dc_vbr_invocations
    GROUP BY 1
) vbr
LEFT OUTER JOIN dc_object_backups dob ON (dob.identifier = vbr.identifier)
WHERE 1=1
  AND start_time > 'date time' -- 조회할 기간
ORDER BY vbr.start_time DESC;</code></pre>
    </div>
  </div>
</div>
</div>
</div>