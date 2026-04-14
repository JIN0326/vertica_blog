---
title: Vertica Administration
layout: default
---

<div class="page-hero">
  <span class="chip">Vertica Administration</span>
  <h1>Vertica 객체 생성 및 관리</h1>
  <p>Table, Projection, Schema, User, Profile, Resource Pool, Privilege 중심으로 Vertica 운영 관리 항목을 정리합니다.</p>
</div>

<section class="button-grid-section">
  <div class="button-grid">
    <a class="button secondary" href="{{ '/administration-table-projection' | relative_url }}">Table · Projection</a>
    <a class="button secondary" href="{{ '/administration-schema' | relative_url }}">Schema</a>
    <a class="button secondary" href="{{ '/administration-user' | relative_url }}">User</a>
    <a class="button secondary" href="{{ '/administration-profile' | relative_url }}">Profile</a>
    <a class="button secondary" href="{{ '/administration-resource-pool' | relative_url }}">Resource Pool</a>
    <a class="button secondary" href="{{ '/administration-privilege' | relative_url }}">Privilege</a>
  </div>
</section>

<div class="page-layout">
  <main>
    <article id="table-projection" class="content-section">
      <h2>Table · Projection</h2>
      <p>Vertica의 테이블(Table)은 논리적인 데이터 구조이며, 프로젝션(Projection)은 테이블 데이터의 물리적인 저장 방식입니다. 쿼리 성능 최적화를 위해 프로젝션의 정렬(ORDER BY) 및 분산(SEGMENTED BY) 설계를 신중하게 해야 합니다.</p>
    </article>

    <article id="schema" class="content-section">
      <h2>Schema</h2>
      <p>스키마(Schema)는 테이블, 뷰, 프로젝션 등 데이터베이스 객체들을 논리적으로 그룹화하는 컨테이너입니다. 스키마를 통해 객체 이름의 충돌을 방지하고, 그룹 단위로 권한을 관리하여 보안을 단순화할 수 있습니다.</p>
    </article>

    <article id="user" class="content-section">
      <h2>User</h2>
      <p>Vertica의 사용자(User)는 데이터베이스에 접근하고 작업을 수행하는 주체입니다. 각 사용자는 고유한 비밀번호, 할당된 Role, Resource Pool, Profile을 가질 수 있으며, 이를 통해 보안 및 리소스 사용을 제어합니다.</p>
    </article>

    <article id="profile" class="content-section">
      <h2>Profile</h2>
      <p>프로필(Profile)은 사용자의 세션 동작과 비밀번호 정책을 관리하는 객체입니다. 비밀번호 만료 기간, 실패한 로그인 시도 횟수, 세션 유휴 시간 등을 설정하여 보안을 강화할 수 있습니다.</p>
    </article>

    <article id="resource-pool" class="content-section">
      <h2>Resource Pool</h2>
      <p>리소스 풀(Resource Pool)은 쿼리 실행에 필요한 메모리와 동시성(Concurrency)을 제어하는 핵심 기능입니다. 각 사용자나 세션을 특정 리소스 풀에 할당하여 워크로드 간의 리소스 경합을 방지하고 시스템 안정성을 유지합니다.</p>
    </article>

    <article id="privilege" class="content-section">
      <h2>Privilege</h2>
      <p>권한(Privilege)은 스키마, 테이블, 뷰 등 특정 데이터베이스 객체에 대한 사용자의 접근 및 조작 권한을 정의합니다. GRANT와 REVOKE 명령어를 사용하여 사용자나 Role에 필요한 최소한의 권한을 부여하는 것이 보안의 핵심입니다.</p>
    </article>
  </main>

  <aside class="page-sidebar">
    <div class="sidebar-panel">
      <h3>Administration 목차</h3>
      <ul>
        <li><a href="#table-projection">Table · Projection</a></li>
        <li><a href="#schema">Schema</a></li>
        <li><a href="#user">User</a></li>
        <li><a href="#profile">Profile</a></li>
        <li><a href="#resource-pool">Resource Pool</a></li>
        <li><a href="#privilege">Privilege</a></li>
      </ul>
    </div>
  </aside>
</div>
