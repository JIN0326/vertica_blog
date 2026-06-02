---
title: "Vertica User: 사용자 생성, 관리, 계정 잠금 해제 - Vertica Blog"
layout: default
description: "Vertica 데이터베이스 사용자(User) 관리 방법을 알아봅니다. 사용자 생성, 패스워드 및 리소스 풀 할당, 그리고 암호 오류로 잠긴 계정을 해제하는 방법을 설명합니다."
keywords: "vertica, user, create user, alter user, drop user, account unlock, resource pool, profile"
---

<div id="user" style="scroll-margin-top: 100px;"></div>

## User

<div class="architecture-section">
  <p class="section-description">Vertica에서 <strong>User(사용자)</strong>는 데이터베이스에 접근하는 주체입니다. 관리자 계정(Administrator)은 데이터베이스 설치 시 생성되는 <code>vertica</code>(OS 계정과 동일)이며, 일반 사용자는 명시적으로 생성하여 권한 및 리소스를 할당해야 합니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. 사용자 생성 및 관리 (CREATE / ALTER USER)</h3>
    <p class="section-description">데이터베이스 사용자를 생성하고, 패스워드, 리소스 풀, 프로파일 등의 속성을 지정합니다. 시스템 보안 정책상 암호를 10회 이상 틀리면 계정이 잠기며(Lock), 이 경우 관리자가 <code>ACCOUNT UNLOCK</code> 작업을 수행해야 합니다.</p>
    <div class="syntax-box">
      <strong>기본 구문:</strong>
      <pre><code>CREATE USER user_name [ account_parameter value[,...] ];
ALTER USER user_name { account_parameter setting | ACCOUNT UNLOCK };</code></pre>
    </div>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 파라미터</dt>
      <dd class="feature-dd">
        <strong>IDENTIFIED BY:</strong> 사용자의 비밀번호를 설정합니다.<br>
        <strong>PROFILE:</strong> 사용자에게 적용할 보안/패스워드 프로파일을 지정합니다.<br>
        <strong>RESOURCE POOL:</strong> 쿼리 실행 시 사용할 전용 리소스 풀을 지정합니다.
      </dd>
    </dl>
    <p class="example-label">예시</p>
    <pre><code>-- 사용자 생성 (비밀번호 및 리소스 풀 지정)
CREATE USER analyst IDENTIFIED BY 'StrongPassw0rd' RESOURCE POOL analyst_pool;

-- 암호 오류로 잠긴 계정 해제 (관리자 권한)
ALTER USER analyst ACCOUNT UNLOCK;</code></pre>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. 사용자 삭제 및 정보 조회</h3>
    <div class="syntax-box">
      <strong>삭제 구문 (DROP USER):</strong>
      <pre><code>DROP USER [ IF EXISTS ] user_name[,...] [ CASCADE ];</code></pre>
    </div>
    <ul class="feature-list" style="margin-top: 1rem;">
      <li><span class="feature-list__icon">🔹</span> <strong>CASCADE 옵션:</strong> <span>해당 사용자가 생성한 모든 객체(테이블 등)를 함께 삭제합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>사용자 조회:</strong> <span><code>SELECT * FROM USERS;</code> 쿼리를 통해 전체 데이터베이스 사용자 정보를 확인할 수 있습니다.</span></li>
    </ul>
  </div>
</div>