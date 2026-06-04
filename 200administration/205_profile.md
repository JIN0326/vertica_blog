---
title: "Vertica Profile: 패스워드 정책 및 보안 프로파일 관리 - Vertica Blog"
layout: default
description: "Vertica의 Profile(프로파일)을 사용하여 사용자의 보안 및 패스워드 정책을 관리하는 방법을 알아봅니다. 패스워드 유효 기간, 최소 길이, 로그인 실패 횟수 등을 설정합니다."
keywords: "vertica, profile, create profile, password policy, security, failed_login_attempts, password_life_time"
canonical_url: "https://jin0326.github.io/vertica_blog/200administration/200administration#profile"
---

<div id="profile" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody">
## Profile

<div class="architecture-section">
  <p class="section-description">Vertica <strong>Profile(프로파일)</strong>은 사용자의 <strong>보안 및 패스워드 정책</strong>을 정의하고 관리하는 객체입니다. 사용자별로 보안 등급에 따라 프로파일을 생성하여 할당할 수 있으며, 기본적으로 제공되는 default 프로파일이 존재합니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">Profile 생성 및 할당</h3>
    <p class="section-description"><code>CREATE PROFILE</code>로 패스워드 관련 제약 조건을 설정하고, 사용자 생성 또는 수정 시 이를 할당합니다.</p>
    <dl class="feature-dl">
      <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 제약조건 (Password Parameters)</dt>
      <dd class="feature-dd">
        <strong>PASSWORD_LIFE_TIME:</strong> 패스워드의 유효 기간을 설정합니다.<br>
        <strong>FAILED_LOGIN_ATTEMPTS:</strong> 허용되는 최대 로그인 실패 횟수입니다 (초과 시 Lock).<br>
        <strong>PASSWORD_LOCK_TIME:</strong> 로그인이 잠긴 후 유지되는 시간입니다.<br>
        <strong>PASSWORD_MIN_LENGTH:</strong> 패스워드의 최소 길이를 지정합니다.
      </dd>
    </dl>
    <p class="example-label">프로파일 생성 및 적용 예시</p>
    <pre><code>-- 보안이 강화된 프로파일 생성 (길이 10자 이상, 5회 실패 시 잠금)
CREATE PROFILE strict_profile LIMIT 
    PASSWORD_MIN_LENGTH 10 
    FAILED_LOGIN_ATTEMPTS 5;

-- 기존 사용자에게 새 프로파일 적용
ALTER USER analyst PROFILE strict_profile;</code></pre>
  </div>
</div>
</div>
</div>