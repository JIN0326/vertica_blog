---
title: "Vertica Okta 연동: OAuth/OIDC 기반 SSO 구현 - Vertica Blog"
layout: default
description: "Okta를 IdP로 사용하여 Vertica 데이터베이스(OAuth 2.0) 및 Management Console(MC, OpenID Connect)에 대한 SSO(Single Sign-On)를 구현하는 방법을 알아봅니다."
keywords: "vertica, okta, sso, single sign-on, oauth, oidc, openid connect, idp, management console, mc"
---

<div id="okta" style="scroll-margin-top: 100px;"></div>

## Okta 연동

<div class="architecture-section">
  <p class="section-description">Okta와 같은 IdP(Identity Provider)를 활용하여 Vertica 데이터베이스 및 Management Console(MC)에 대한 SSO(Single Sign-On)를 구현할 수 있습니다. 이를 통해 중앙에서 사용자 계정을 관리하고 보안을 강화할 수 있습니다.</p>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">1. Vertica DB 인증 (OAuth 2.0)</h3>
    <p class="section-description">클라이언트 애플리케이션이 Okta를 통해 인증하고 발급받은 Access Token을 사용하여 Vertica 데이터베이스에 접속하는 방식입니다.</p>

    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">1단계: Okta 애플리케이션 설정</h4>
      <p class="section-description">Okta 관리자 콘솔에서 Vertica 연동을 위한 애플리케이션을 생성하고, 'Resource Owner Password' Grant Type을 활성화해야 합니다.</p>
      <dl class="feature-dl">
        <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 설정 항목</dt>
        <dd class="feature-dd">
          <strong>Grant Type:</strong> <code>Resource Owner Password</code>를 활성화하여 사용자의 ID/PW로 직접 토큰을 발급받을 수 있도록 설정합니다.<br>
          <strong>PKCE:</strong> <code>Require PKCE as additional verification</code> 옵션을 해제하여 일부 클라이언트와의 호환성을 확보합니다.<br>
          <strong>Access Policy:</strong> Security > API 메뉴에서 Access Policy를 생성하고, Rule의 'Grant type'에 <code>Resource Owner Password</code>를 허용하도록 추가합니다.<br>
          <strong>Sign On Policy:</strong> 사용자가 패스워드로 인증할 수 있도록 Sign On 정책 Rule에서 'User must authenticate with' 옵션을 <code>Password</code>로 설정합니다.
        </dd>
      </dl>
    </div>

    <div class="step-section">
      <h4 class="step-title">2단계: Vertica OAuth 인증 설정</h4>
      <p class="section-description">Vertica에서 OAuth 인증 객체를 생성하고, Okta 애플리케이션의 정보를 등록합니다.</p>
      <div class="syntax-box">
        <strong>OAuth 인증 설정 예시:</strong>
        <pre><code>-- 1. OAuth 인증 객체 생성
CREATE AUTHENTICATION v_oauth METHOD 'oauth' HOST '0.0.0.0/0';

-- 2. Okta 애플리케이션 정보 설정
ALTER AUTHENTICATION v_oauth SET 
    validate_type = 'IDP',
    client_id = '&lt;YOUR_OKTA_CLIENT_ID&gt;',
    client_secret = '&lt;YOUR_OKTA_CLIENT_SECRET&gt;',
    introspect_url = 'https://&lt;YOUR_OKTA_DOMAIN&gt;/oauth2/v1/introspect';

-- 3. 인증 활성화 및 사용자에게 권한 부여
ALTER AUTHENTICATION v_oauth ENABLE PRIORITY 100;
GRANT AUTHENTICATION v_oauth TO "user@example.com";</code></pre>
      </div>
      <ul class="feature-list" style="margin-top: 1rem;">
        <li><span class="feature-list__icon">💡</span> <strong>접속 방식:</strong> <span>클라이언트 애플리케이션(Python, Java 등)은 먼저 사용자의 Okta ID/PW를 사용하여 Okta로부터 <strong>Access Token</strong>을 발급받습니다. 그 후, Vertica에 접속할 때 이 Access Token을 <strong>비밀번호</strong>로 사용하여 인증을 수행합니다.</span></li>
      </ul>
    </div>
  </div>

  <div class="architecture-subsection">
    <h3 class="section-subtitle">2. Vertica Management Console (MC) 인증 (OpenID Connect)</h3>
    <p class="section-description">MC 로그인 시 Okta 로그인 페이지로 리디렉션하여 SSO를 구현하는 방식입니다. 이를 통해 MC 사용자 계정을 Okta에서 중앙 관리할 수 있습니다.</p>

    <div class="step-section" style="border-top: none; padding-top: 0; margin-top: 0;">
      <h4 class="step-title">1단계: MC OpenID Connect 설정</h4>
      <p class="section-description">MC 관리 페이지의 'Identity Providers' 메뉴에서 OpenID Connect를 설정하고 Okta의 Discovery Endpoint를 등록합니다.</p>
      <dl class="feature-dl">
        <dt class="feature-dt"><span class="feature-dt__icon">◆</span> 주요 설정 항목</dt>
        <dd class="feature-dd">
          <strong>Discovery endpoint:</strong> Okta의 OIDC 설정 메타데이터 URL을 입력합니다. (예: <code>https://&lt;YOUR_OKTA_DOMAIN&gt;/.well-known/openid-configuration</code>)<br>
          <strong>Trust Email:</strong> <code>On</code>으로 설정하여 이메일 주소를 신뢰합니다.<br>
          <strong>First login flow override:</strong> <code>first broker login (registration)</code>으로 설정하여 최초 로그인 시 MC에 사용자를 자동 등록합니다.<br>
          <strong>Sync mode:</strong> <code>Import</code>로 설정하여 Okta의 사용자 정보를 MC로 가져옵니다.
        </dd>
      </dl>
      <img src="/vertica_blog/assets/images/okta1.png" alt="Okta OIDC Provider 설정 1" style="width: 100%; max-width: 800px; margin: 1.5rem auto; display: block; border: 1px solid #e0e0e0; border-radius: 8px;">
      <img src="/vertica_blog/assets/images/okta2.png" alt="Okta OIDC Provider 설정 2" style="width: 100%; max-width: 800px; margin: 1.5rem auto; display: block; border: 1px solid #e0e0e0; border-radius: 8px;">
    </div>

    <div class="step-section">
      <h4 class="step-title">2단계: MC Attribute Mappers 설정</h4>
      <p class="section-description">Okta에서 전달받은 사용자 정보(Claim)를 MC의 사용자 속성(Attribute)에 매핑하여 사용자 정보를 동기화합니다.</p>
      <img src="/vertica_blog/assets/images/okta3.png" alt="Okta Attribute Mappers 설정" style="width: 100%; max-width: 800px; margin: 1.5rem auto; display: block; border: 1px solid #e0e0e0; border-radius: 8px;">
      <table class="info-table">
        <thead>
          <tr>
            <th>Mapper Name</th>
            <th>Mapper Type</th>
            <th>Claim</th>
            <th>User Attribute Name</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>username-mapping</td>
            <td>Attribute Importer</td>
            <td>preferred_username</td>
            <td>username</td>
          </tr>
          <tr>
            <td>email-mapping</td>
            <td>Attribute Importer</td>
            <td>email</td>
            <td>email</td>
          </tr>
          <tr>
            <td>firstname-mapping</td>
            <td>Attribute Importer</td>
            <td>given_name</td>
            <td>firstName</td>
          </tr>
          <tr>
            <td>lastname-mapping</td>
            <td>Attribute Importer</td>
            <td>family_name</td>
            <td>lastName</td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="step-section">
      <h4 class="step-title">3단계: Okta SSO 로그인</h4>
      <p class="section-description">모든 설정이 완료되면, MC 로그인 화면에 'Log in with Okta' 버튼이 나타나며, 이를 통해 Okta 계정으로 SSO 로그인을 수행할 수 있습니다.</p>
      <img src="/vertica_blog/assets/images/okta4.png" alt="Vertica MC Okta 로그인 화면" style="width: 100%; max-width: 800px; margin: 1.5rem auto; display: block; border: 1px solid #e0e0e0; border-radius: 8px;">
    </div>
  </div>
</div>