---
title: Vertica(버티카) 보안: LDAP, TLS, Okta, Client Authentication 설정 - Vertica Blog
layout: default
description: "Vertica(버티카) 보안 및 인증 가이드. Client Authentication, LDAP(OpenLDAP, AD) 연동, TLS(SSL) 상호 인증(Mutual Mode) 설정, Okta를 활용한 OAuth/OIDC 기반 SSO(Single Sign-On) 구현 등 Vertica 데이터베이스의 보안을 강화하는 방법을 알아봅니다."
keywords: "vertica, security, authentication, ldap, openldap, active directory, tls, ssl, mutual mode, okta, oauth, oidc, sso, client authentication"
permalink: /500auth_security/
---
<div class="page-hero">
  <span class="chip">Auth&Security</span>
  <h1>Vertica Auth&Security</h1>
  <p>Vertica 환경에서 데이터를 안전하게 보호하고 접근을 제어하기 위한 핵심 인증 체계인 Client Auth, LDAP, TLS(SSL), 그리고 Okta 연동 방법에 대해 알아봅니다.</p>
</div>

<div class="page-layout">
  <div class="content-section" markdown="1">

  {% assign page_501 = site.pages | where: "name", "501_auth-overview.md" | first %}
  {{ page_501.content }}

  <hr style="margin: 3rem 0;">
  
  {% assign page_502 = site.pages | where: "name", "502_ldap.md" | first %}
  {{ page_502.content }}

  <hr style="margin: 3rem 0;">
  
  {% assign page_503 = site.pages | where: "name", "503_tls.md" | first %}
  {{ page_503.content }}

  <hr style="margin: 3rem 0;">
  
  {% assign page_504 = site.pages | where: "name", "504_okta.md" | first %}
  {{ page_504.content }}

  
</div>

  <aside class="page-sidebar">
    <div class="sidebar-panel" style="padding-right: 1rem;">
      <h3>Auth & Security</h3>
      <ul>
        <li><a href="#auth-overview">Client Auth</a></li>
        <li><a href="#ldap">LDAP 인증</a></li>
        <li><a href="#tls">TLS 보안</a></li>
        <li><a href="#okta">Okta 연동</a></li>
      </ul>
    </div>
  </aside>
</div>