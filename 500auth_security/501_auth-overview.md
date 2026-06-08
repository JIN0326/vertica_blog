---
title: "Vertica Client Authentication: 인증 방식 개요 - Vertica Blog"
layout: default
description: "Vertica가 지원하는 다양한 클라이언트 인증 방식(Hash, LDAP, TLS, OAuth)과 인증 우선순위 로직을 알아봅니다."
keywords: "vertica, client authentication, hash, ldap, tls, oauth, authentication method, priority"
canonical_url: "https://jin0326.github.io/vertica_blog/500auth_security/500auth_security#auth-overview"
---

<div id="auth-overview" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody" markdown="1">
## Client Authentication

<div class="architecture-section">
  <p class="section-description">Vertica는 데이터베이스 접근을 제어하기 위해 다양한 클라이언트 인증 방식을 제공하며, <code>CLIENT_AUTH</code> 및 <code>USER_CLIENT_AUTH</code> 시스템 테이블을 통해 이를 관리합니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">주요 인증 방식 (Methods)</h3>
    <ul class="feature-list">
      <li><span class="feature-list__icon">🔹</span> <strong>Hash:</strong> <span>가장 일반적인 방식으로, 네트워크를 통해 해시된 암호를 전송하여 검증합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>LDAP:</strong> <span>Active Directory 또는 LDAP 서버를 통해 사용자를 인증합니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>TLS:</strong> <span>디지털 인증서를 사용하여 클라이언트를 인증하며 보안성이 가장 높습니다.</span></li>
      <li><span class="feature-list__icon">🔹</span> <strong>OAuth:</strong> <span>액세스 토큰을 사용하여 Okta와 같은 IdP(Identity Provider)와 연동합니다.</span></li>
    </ul>

<div class="syntax-box">
      <strong>인증 우선순위 로직:</strong>
      <ol>
        <li><strong>auth_priority:</strong> 가장 먼저 판별되는 우선순위 값입니다.</li>
        <li><strong>method_priority:</strong> 인증 방법 자체의 우선순위입니다.</li>
        <li><strong>address_priority:</strong> 특정 IP 대역폭 설정의 우선순위입니다.</li>
      </ol>
    </div>
  </div>
</div>
</div>
</div>