---
title: Vertica 설치 및 초기 설정 가이드 - Vertica Blog
layout: default
description: "Vertica 데이터베이스와 Management Console(MC)의 설치, 클러스터 구성, 초기 설정 방법을 단계별로 안내합니다."
---

<div class="page-hero">
  <span class="chip">Installation</span>
  <h1>Vertica Installation & Setup</h1>
  <p>Vertica 데이터베이스와 Management Console(MC)의 설치, 클러스터 구성, 그리고 초기 설정 방법을 단계별로 안내합니다.</p>
</div>

<div class="page-layout">
  <div class="content-section" markdown="1">

<div id="vertica-install" style="scroll-margin-top: 100px;"></div>

## Vertica 설치

<div class="architecture-section">
  <p class="section-description">이 섹션에서는 Vertica 데이터베이스를 설치하기 위한 사전 요구사항 확인부터 실제 설치 과정까지를 다룹니다. 성공적인 설치를 위해 각 단계를 주의 깊게 따라주세요.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">0. 설치 방법 선택</h3>
    <p class="section-description">Vertica는 두 가지 주요 경로를 통해 설치하고 사용해볼 수 있습니다.</p>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>무료 체험판 사용:</strong> <span>Vertica를 처음 사용해보는 경우, <a href="https://www.opentext.com/kr/products/analytics-database" target="_blank">OpenText 공식 사이트</a>에서 무료 체험판을 신청하여 사용할 수 있습니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>RPM 패키지 및 라이선스 보유:</strong> <span>이미 Vertica 설치 RPM 파일과 유효한 라이선스를 보유하고 있다면, <a href="https://docs.vertica.com/26.2.x/en/setup/" target="_blank">공식 설치 가이드</a>를 참고하여 설치를 진행할 수 있습니다.</span></li>
    </ul>
  </div>
  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. 사전 요구사항 (Prerequisites)</h3>
    <p class="section-description">Vertica를 설치하기 전에 시스템이 최소 요구사항을 충족하는지 확인해야 합니다.</p>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>지원 OS 확인:</strong> <span>RHEL, CentOS, Ubuntu 등 지원되는 운영체제 버전을 확인합니다. (<a href="{{ '/94lifecycle#os' | relative_url }}">OS 호환성 정보</a> 참고)</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>시스템 설정:</strong> <span>NTP, I/O 스케줄러, 방화벽 등 OS 파라미터 설정을 확인합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>네트워크 구성:</strong> <span>클러스터 노드 간의 원활한 통신을 위한 네트워크 설정을 확인합니다.</span></li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. Vertica 기본 포트</h3>
    <p class="section-description">Vertica 클러스터는 원활한 통신과 관리를 위해 여러 네트워크 포트를 사용합니다. 방화벽 설정 시 아래 포트들을 허용해야 합니다.</p>
    <table class="info-table">
      <thead>
        <tr>
          <th>Port</th>
          <th>Protocol</th>
          <th>Service</th>
          <th>Notes</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>22</td><td>TCP</td><td>sshd</td><td>Administration Tools 및 MC 클러스터 설치에 필요합니다.</td>
        </tr>
        <tr>
          <td>5433</td><td>TCP</td><td>Vertica</td><td>vsql, ODBC, JDBC 등 클라이언트 접속 포트입니다.</td>
        </tr>
        <tr>
          <td>5434</td><td>TCP</td><td>Vertica</td><td>클러스터 내부 및 클러스터 간 통신에 사용됩니다.</td>
        </tr>
        <tr>
          <td>5433</td><td>UDP</td><td>Vertica</td><td>Spread 모니터링 및 MC 클러스터 가져오기에 사용됩니다.</td>
        </tr>
        <tr>
          <td>5444</td><td>TCP</td><td>Vertica MC</td><td>MC와 노드 에이전트 간 통신에 사용됩니다.</td>
        </tr>
        <tr>
          <td>5450</td><td>TCP</td><td>Vertica MC</td><td>웹 브라우저에서 MC에 접속하기 위한 포트입니다.</td>
        </tr>
        <tr>
          <td>4803</td><td>TCP/UDP</td><td>Spread</td><td>클러스터 노드 간 통신을 담당하는 Spread 데몬이 사용합니다.</td>
        </tr>
        <tr>
          <td>4804</td><td>UDP</td><td>Spread</td><td>클러스터 노드 간 통신을 담당하는 Spread 데몬이 사용합니다.</td>
        </tr>
      </tbody>
    </table>
  </div>

  <div id="install-packages" style="scroll-margin-top: 100px;"></div>
  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. 필수 패키지 설치 (CentOS/Redhat)</h3>
    <p class="section-description">Vertica 설치 및 운영에 필요한 기본 OS 패키지를 설치합니다.</p>
    <div class="syntax-box">
      <strong>[as root]</strong>
      <pre><code>yum install -y dialog gdb gcc gcc-c++ ntp firewalld glibc which mcelog sysstat openssh-server openssh-clients</code></pre>
    </div>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>NTP 서비스 설정:</strong> <span>시간 동기화를 위해 NTP 서비스를 방화벽에 등록하고 시작합니다.</span></li>
    </ul>
    <div class="syntax-box">
      <strong>[as root]</strong>
      <pre><code>firewall-cmd --permanent --add-service=ntp
systemctl start chronyd
systemctl enable chronyd</code></pre>
    </div>
  </div>

  <div id="os-settings" style="scroll-margin-top: 100px;"></div>
  <div class="architecture-subsection">
    <h3 class="section-subtitle">4. OS 권장 설정 (CentOS/Redhat)</h3>
    <p class="section-description">Vertica의 최적 성능을 위해 OS 수준에서 몇 가지 설정을 변경하는 것이 권장됩니다.</p>
    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">가. 네트워크 설정 (Public/Private)</h4>
      <p class="section-description">Vertica 클러스터는 안정적인 통신과 보안을 위해 네트워크를 두 가지 용도로 분리하여 구성하는 것을 강력히 권장합니다.</p>
      <ul class="feature-list">
        <li><strong>Public Network:</strong> 클라이언트 애플리케이션(BI 툴, vsql 등)이 데이터베이스에 접속하기 위해 사용하는 공용 네트워크입니다.</li>
        <li><strong>Private Network:</strong> 클러스터 노드 간 데이터 재분배(Rebalancing), 쿼리 중간 결과 전송 등 내부 통신에만 사용되는 전용 네트워크입니다. 외부 접근을 차단하여 보안을 강화하고, 노드 간 통신 성능을 보장합니다.</li>
      </ul>
      <div class="image-grid-2">
        <div class="image-box-styled">
          <p style="text-align:center; font-weight:bold;">Enterprise Mode 구성도</p>
          <img src="{{ '/assets/images/구성_ent.png' | relative_url }}" alt="Enterprise Mode 네트워크 구성도">
        </div>
        <div class="image-box-styled">
          <p style="text-align:center; font-weight:bold;">Eon Mode 구성도</p>
          <img src="{{ '/assets/images/구성_eon.png' | relative_url }}" alt="Eon Mode 네트워크 구성도">
        </div>
      </div>
    </div>
    <div class="step-section">
      <h4 class="step-title">나. RAID 구성 및 BIOS/Kernel 파라미터</h4>
      <p class="section-description">`swap`, `/boot`, `/catalog`, `/data` 등 주요 디렉터리에 대한 RAID 구성을 최적화하고, CPU 전원 관리 기능 관련 Kernel 파라미터를 설정합니다.</p>
      <div class="syntax-box">
        <strong>[as root] Kernel 파라미터 추가 예시:</strong>
        <pre><code>grubby --args="intel_idle.max_cstate=0 processor.max_cstate=1 intel_pstate=disable" --update-kernel $(grubby --default-kernel)</code></pre>
      </div>
    </div>
    <div class="step-section">
      <h4 class="step-title">다. OS 버전, 시간대, CPU 설정 확인</h4>
      <p class="section-description">`timedatectl`로 시간대를 `Asia/Seoul`로 설정하고, `cat /proc/cpuinfo` 및 `cpupower frequency-info` 명령어로 HyperThreading 및 CPU 주파수 설정을 확인합니다.</p>
    </div>
  </div>

  <div id="selinux-swappiness" style="scroll-margin-top: 100px;"></div>
  <div class="architecture-subsection">
    <h3 class="section-subtitle">5. SELinux 및 시스템 파라미터 설정</h3>
    <p class="section-description">보안 강화 커널(SELinux)을 비활성화하고, 메모리 및 네트워크 관련 시스템 파라미터를 Vertica에 최적화합니다.</p>
    <div class="syntax-box">
      <strong>[as root] /etc/selinux/config 수정:</strong>
      <pre><code># SELINUX=enforcing 또는 SELINUX=permissive 를 아래와 같이 변경
SELINUX=disabled</code></pre>
      <strong>[as root] /etc/sysctl.conf 수정 (파일 끝에 추가):</strong>
      <pre><code># Swappiness 설정 (v11 이상은 0, v10 이하는 1 권장)
vm.swappiness = 0

# Vertica 권장 네트워크 파라미터
net.core.somaxconn = 1024
net.core.wmem_max = 16777216
net.core.rmem_max = 16777216
net.ipv4.tcp_mem = 16777216 16777216 16777216
net.ipv4.tcp_wmem = 8192 262144 8388608
net.ipv4.tcp_rmem = 8192 262144 8388608
...</code></pre>
      <strong>[as root] 즉시 반영:</strong>
      <pre><code>setenforce 0
sysctl -p</code></pre>
    </div>
  </div>

  <div id="vertica-user-setup" style="scroll-margin-top: 100px;"></div>
  <div class="architecture-subsection">
    <h3 class="section-subtitle">6. Vertica OS 사용자 생성 및 디렉터리 설정</h3>
    <p class="section-description">Vertica 데이터베이스를 소유하고 운영할 전용 OS 사용자를 생성하고, 데이터 및 카탈로그 디렉터리의 소유권을 부여합니다.</p>
    <div class="syntax-box">
      <strong>[as root]</strong>
      <pre><code># 'verticadba' 그룹 및 'vertica' 사용자 생성
/usr/sbin/groupadd -r verticadba
/usr/sbin/useradd -r -m -s /bin/bash -g verticadba vertica

# 데이터 및 카탈로그 디렉터리 생성 및 권한 부여
mkdir /data
mkdir /catalog
chown vertica:verticadba /data
chown vertica:verticadba /catalog</code></pre>
    </div>
  </div>

  <div id="vertica-rpm" style="scroll-margin-top: 100px;"></div>
  <div class="architecture-subsection">
    <h3 class="section-subtitle">7. Vertica RPM 설치</h3>
    <p class="section-description">Vertica 소프트웨어 RPM 파일을 다운로드하여 설치합니다. 설치 전 파일의 무결성을 확인하는 것이 좋습니다.</p>
    <div class="syntax-box">
      <strong>[as root]</strong>
      <pre><code># Checksum 확인 (옵션)
sha1sum &lt;VERTICA_RPM_FILE_PATH&gt;

# RPM 패키지 설치
rpm -Uvh /home/vertica/vertica-&lt;VERSION&gt;.RHEL&lt;OS_VERSION&gt;.x86_64.rpm</code></pre>
    </div>
  </div>

  <div id="vertica-software-install" style="scroll-margin-top: 100px;"></div>
  <div class="architecture-subsection">
    <h3 class="section-subtitle">8. Vertica 소프트웨어 설치 스크립트 실행</h3>
    <p class="section-description">Vertica 설치 스크립트(<code>install_vertica</code>)를 실행하여 데이터베이스 소프트웨어를 설치합니다.</p>
    <div class="syntax-box">
      <strong>설치 스크립트 실행 예시:</strong>
      <pre><code># [as root] Enterprise Mode 설치
/opt/vertica/sbin/install_vertica -s &lt;NODE1_HOSTNAME&gt;,&lt;NODE2_HOSTNAME&gt;,&lt;NODE3_HOSTNAME&gt; -u vertica -p '&lt;VERTICA_OS_USER_PASSWORD&gt;' -P '&lt;ROOT_PASSWORD&gt;' -T</code></pre>
    </div>
    <ul class="feature-list" style="margin-top: 1rem;">
      <li><span class="feature-list__icon">💡</span> <strong>`install_vertica` 주요 옵션:</strong>
        <ul>
          <li><code>-s &lt;HOSTNAME_LIST&gt;</code>: 클러스터를 구성할 노드들의 호스트명 (쉼표 구분)</li>
          <li><code>-u &lt;OS_USER&gt;</code>: Vertica DB를 운영할 OS 사용자명 (예: `vertica`)</li>
          <li><code>-p '&lt;PASSWORD&gt;'</code>: `OS_USER` 계정의 비밀번호</li>
          <li><code>-T</code> 또는 <code>--point-to-point</code>: 모든 노드 간 직접 통신(Point-to-Point)을 사용하도록 Spread를 구성합니다. 노드가 동일 서브넷에 없거나 가상 환경일 때 권장됩니다.</li>
          <li><code>--failure-threshold NONE</code>: 설치 중 에러가 발생해도 중단하지 않음 (주의하여 사용)</li>
        </ul>
      </li>
    </ul>
    <dl class="feature-dl" style="margin-top: 1.5rem;">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 통신 방식: Broadcast vs. Point-to-Point</dt>
      <dd class="feature-dd">
        <strong>Broadcast (-U, 기본값):</strong> UDP 브로드캐스트를 사용하여 동일 서브넷 내의 노드들과 통신합니다. 구성이 간단하지만 80개 이하의 노드에서만 지원됩니다.<br>
        <strong>Point-to-Point (-T):</strong> 모든 노드 간에 직접 유니캐스트 통신을 사용합니다. 노드가 서로 다른 서브넷에 있거나, 클라우드/가상 환경일 경우 반드시 사용해야 합니다.
      </dd>
    </dl>
  </div>

  <div id="create-database" style="scroll-margin-top: 100px;"></div>
  <div class="architecture-subsection">
    <h3 class="section-subtitle">9. 데이터베이스 생성</h3>
    <p class="section-description"><code>admintools</code>의 'Create Database' 메뉴를 사용하여 데이터베이스를 생성합니다. 이 과정에서 데이터 및 카탈로그 경로, 노드 정보 등을 설정합니다.</p>
    <div class="syntax-box">
      <strong>[as vertica] Enterprise Mode DB 생성 예시:</strong>
      <pre><code>/opt/vertica/bin/admintools -t create_db \
    -s &lt;NODE1_HOSTNAME&gt;,&lt;NODE2_HOSTNAME&gt;,&lt;NODE3_HOSTNAME&gt; \
    -d &lt;DB_NAME&gt; \
    -c /catalog \
    -D /data</code></pre>
      <strong>[as vertica] Eon Mode DB 생성 예시:</strong>
      <pre><code># S3 인증 정보 파일 생성 (auth_params.conf)
awsauth = &lt;ACCESS_KEY&gt;:&lt;SECRET_KEY&gt;
awsendpoint = &lt;S3_ENDPOINT_IP&gt;:9000
awsenablehttps = 0

# Eon Mode DB 생성
/opt/vertica/bin/admintools -t create_db \
    -s &lt;NODE1_HOSTNAME&gt;,&lt;NODE2_HOSTNAME&gt;,&lt;NODE3_HOSTNAME&gt; \
    -d &lt;DB_NAME&gt; \
    --shard-count=&lt;SHARD_COUNT&gt \
    -c /catalog \
    -D /data \
    --depot-path=/data \
    --communal-storage-location=s3://&lt;BUCKET_NAME&gt;/eondb \
    -x /home/vertica/auth_params.conf</code></pre>
    </div>
  </div>
</div>

<hr style="margin: 3rem 0;">
<div id="initial-setup" style="scroll-margin-top: 100px;"></div>

## 초기 설정
<div class="architecture-section">
  <p class="section-description">데이터베이스 생성 후, 안정적인 운영과 성능 최적화를 위해 몇 가지 초기 설정을 수행하는 것이 좋습니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. PK/UK 제약조건 기본 활성화</h3>
    <p class="section-description">Primary Key 및 Unique Key 제약조건이 생성 시 기본적으로 활성화(ENABLE)되도록 설정합니다.</p>
    <div class="syntax-box">
      <pre><code>SELECT SET_CONFIG_PARAMETER('EnableNewUniqueKeysByDefault', 1);
SELECT SET_CONFIG_PARAMETER('EnableNewPrimaryKeysByDefault', 1);</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 클라이언트 연결 로드 밸런싱 설정</h3>
    <p class="section-description">클라이언트 접속 요청을 클러스터 노드 간에 라운드 로빈(Round Robin) 방식으로 분산하여 단일 노드의 접속 부하를 방지합니다.</p>
    <div class="syntax-box">
      <pre><code>SELECT SET_LOAD_BALANCE_POLICY('ROUNDROBIN');

-- 확인: vsql -C 옵션으로 접속 시도 시, 접속되는 노드가 매번 변경되는지 확인
-- vsql -C -c "select current_session();"</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. 클라이언트 설정 (DBeaver)</h3>
    <p class="section-description">DBeaver와 같은 GUI 클라이언트에서 안정적인 연결을 위해 고가용성(HA) 관련 속성을 설정합니다.</p>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>AutoCommit:</strong> <code>false</code>로 설정하여 수동 커밋 모드로 전환합니다.</li>
      <li><span class="feature-list__icon">🔹</span> <strong>ConnectionLoadBalance:</strong> <code>true</code>로 설정하여 접속 부하를 분산합니다.</li>
      <li><span class="feature-list__icon">🔹</span> <strong>BackupServerNode:</strong> 주 접속 노드 장애 시 대체할 노드 IP 목록을 지정합니다.</li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">4. Export Address 설정</h3>
    <p class="section-description">클러스터의 각 노드가 외부와 통신할 때 사용할 Public IP 주소를 지정합니다. 이를 통해 Private 네트워크는 내부 통신에만 사용하고, 외부 데이터 전송은 Public 네트워크를 통하도록 역할을 분리할 수 있습니다.</p>
    <div class="syntax-box">
      <pre><code>-- 1. Export에 사용할 서브넷 생성
CREATE SUBNET sub1 WITH '&lt;SUBNET_IP_RANGE&gt;';

-- 2. DB의 기본 Export 서브넷으로 지정
ALTER DATABASE DEFAULT EXPORT ON sub1;

-- 3. 각 노드별 Export 주소 생성 및 할당
CREATE NETWORK ADDRESS sub01 ON v_&lt;DB_NAME&gt;_node0001 WITH '&lt;PUBLIC_IP_1&gt;' PORT 5433;
CREATE NETWORK ADDRESS sub02 ON v_&lt;DB_NAME&gt;_node0002 WITH '&lt;PUBLIC_IP_2&gt;' PORT 5433;
CREATE NETWORK ADDRESS sub03 ON v_&lt;DB_NAME&gt;_node0003 WITH '&lt;PUBLIC_IP_3&gt;' PORT 5433;
ALTER NODE v_&lt;DB_NAME&gt;_node0001 EXPORT ON sub01;
ALTER NODE v_&lt;DB_NAME&gt;_node0002 EXPORT ON sub02;
ALTER NODE v_&lt;DB_NAME&gt;_node0003 EXPORT ON sub03;

-- 4. 설정 확인
SELECT node_name, node_address, export_address FROM nodes;

     node_name     | node_address | export_address
  -----------------+--------------+----------------
   v_&lt;DB_NAME&gt;_node0001 | &lt;PRIVATE_IP_1&gt;   | &lt;PUBLIC_IP_1&gt;
   v_&lt;DB_NAME&gt;_node0002 | &lt;PRIVATE_IP_2&gt;   | &lt;PUBLIC_IP_2&gt;
   v_&lt;DB_NAME&gt;_node0003 | &lt;PRIVATE_IP_3&gt;   | &lt;PUBLIC_IP_3&gt;
</code></pre>
    </div>
  </div>
</div>

<hr style="margin: 3rem 0;">
<div id="change-port" style="scroll-margin-top: 100px;"></div>

## Vertica 포트 변경
<div class="architecture-section">
  <p class="section-description">보안 정책이나 다른 서비스와의 충돌을 피하기 위해 Vertica의 기본 포트를 변경할 수 있습니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. DB 생성 시 포트 변경</h3>
    <p class="section-description">데이터베이스를 생성하기 전에 <code>admintools.conf</code> 파일에서 기본 포트를 변경할 수 있습니다.</p>
    <div class="syntax-box">
      <pre><code># 1. /opt/vertica/config/admintools.conf 파일 수정
# [Database] 섹션의 port 파라미터 변경
port = 35433

# 2. DB 생성 실행
/opt/vertica/bin/admintools -t create_db ...</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 생성된 DB의 포트 변경</h3>
    <p class="section-description">이미 운영 중인 데이터베이스의 포트를 변경하려면, DB를 중지한 후 <code>ALTER NODE</code> 명령과 <code>admintools.conf</code> 파일 수정을 함께 수행해야 합니다.</p>
    <div class="syntax-box">
      <pre><code>-- 1. ALTER NODE 명령으로 포트 번호 변경
ALTER NODE v_&lt;DB_NAME&gt;_node0001 PORT 35433;

-- 2. DB 중지
admintools -t stop_db -d &lt;DB_NAME&gt;

-- 3. admintools.conf 파일의 port 변경 후 클러스터에 배포
-- /opt/vertica/config/admintools.conf 파일의 [Database] 섹션 port 변경
admintools -t distribute_config_files

-- 4. DB 재기동 후 포트 변경 확인
admintools -t start_db -d &lt;DB_NAME&gt; -i
vsql -p 35433 -c "SELECT name, address, clientport FROM vs_nodes;"</code></pre>
    </div>
  </div>
</div>

<hr style="margin: 3rem 0;">
<div id="mc-install" style="scroll-margin-top: 100px;"></div>

## Management Console (MC) 설치

<div class="architecture-section">
  <p class="section-description">Management Console(MC)은 Vertica 클러스터를 모니터링하고 관리하기 위한 웹 기반 도구입니다. 데이터베이스 클러스터와는 별도의 전용 서버에 설치하는 것이 권장됩니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">MC 하드웨어 요구사항</h3>
    <p class="section-description">MC는 클러스터의 노드 또는 전용 노드에 설치할 수 있습니다. 클러스터 노드에서 MC를 실행할 때 MC는 다른 데이터베이스 프로세스와 RAM 및 CPU 코어 시간을 공유합니다.</p>
    <p class="section-description">다음 표는 최소 및 권장 하드웨어 요구 사항을 제공합니다.</p>
    <table class="info-table">
      <thead>
        <tr>
          <th>요구사항</th>
          <th>CPU</th>
          <th>RAM</th>
          <th>디스크 공간</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><strong>최소</strong></td>
          <td>4-core</td>
          <td>4G</td>
          <td>2G</td>
        </tr>
        <tr>
          <td><strong>권장</strong></td>
          <td>8-core</td>
          <td>8G</td>
          <td>2G</td>
        </tr>
      </tbody>
    </table>
  </div>
  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. MC RPM 패키지 설치</h3>
    <p class="section-description">Vertica MC 설치 RPM 파일을 서버에 복사한 후, `root` 권한으로 설치합니다.</p>
    <div class="syntax-box">
      <strong>[as root]</strong>
      <pre><code>rpm -Uvh /path/to/vertica-console-&lt;VERSION&gt;.RHEL&lt;OS_VERSION&gt;.x86_64.rpm</code></pre>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. MC 서비스 기동 및 접속</h3>
    <p class="section-description">RPM 설치가 완료되면 MC 서비스를 시작하고, 웹 브라우저를 통해 접속합니다. 기본 접속 포트는 5450입니다.</p>
    <div class="syntax-box">
      <strong>[as root]</strong>
      <pre><code># MC 서비스 상태 확인
/opt/vertica/sbin/verticad status

# MC 서비스 기동
/opt/vertica/sbin/verticad start</code></pre>
    </div>
    <ul class="feature-list" style="margin-top: 1rem;">
      <li><span class="feature-list__icon">💡</span> <strong>접속 주소:</strong> <span><code>https://&lt;MC_HOST_IP&gt;:5450</code></span></li>
    </ul>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">3. 데이터베이스 등록</h3>
    <p class="section-description">MC에 처음 접속한 후, 모니터링하고 관리할 Vertica 데이터베이스를 등록해야 합니다. MC의 'Import an existing Vertica database' 메뉴를 통해 데이터베이스 호스트, DB명, 사용자 정보 등을 입력하여 등록을 완료합니다.</p>
  </div>
</div>

</div>
  <aside class="page-sidebar">
    <div class="sidebar-panel" style="padding-right: 1rem;">
      <h3>On this page</h3>
      <ul>
        <li><a href="#vertica-install">Vertica 설치</a></li>
        <li><a href="#initial-setup">Vertica 초기설정</a></li>
        <li><a href="#change-port">Vertica 포트변경</a></li>
        <li><a href="#mc-install">MC 설치</a></li>
      </ul>
    </div>
  </aside>
</div>