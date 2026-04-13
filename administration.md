---
title: Vertica Administration
layout: default
---

<div class="page-hero">
  <span class="chip">Vertica Administration</span>
  <h1>Vertica 객체 생성 및 관리</h1>
  <p>Table, Projection, Schema, User, Profile, Resource Pool, Privilege 중심으로 Vertica 운영 관리 항목을 정리합니다.</p>
  <div class="actions">
    <a class="button primary" href="{{ '/administration-table-projection' | relative_url }}">Table · Projection</a>
    <a class="button secondary" href="{{ '/administration-schema' | relative_url }}">Schema</a>
  </div>
</div>

<section style="margin-top: 28px; padding: 26px; background: rgba(255,255,255,0.94); border-radius: 24px; border: 1px solid rgba(45,105,255,0.14); box-shadow: 0 20px 50px rgba(45,105,255,0.08);">
  <div style="display: grid; gap: 18px;">
    <a class="button secondary" href="{{ '/administration-user' | relative_url }}">User</a>
    <a class="button secondary" href="{{ '/administration-profile' | relative_url }}">Profile</a>
    <a class="button secondary" href="{{ '/administration-resource-pool-privilege' | relative_url }}">Resource Pool · Privilege</a>
  </div>
</section>

## Vertica Administration 핵심

Vertica 운영자는 객체 생성과 권한 관리를 핵심으로 합니다. 이 페이지에서는 `Table`, `Projection`, `Schema`, `User`, `Profile`, `Resource Pool`, `Privilege`를 분리해 실무 중심으로 정리합니다.

- `Table`과 `Projection`: 데이터 저장과 조회 성능의 기반
- `Schema`: 네임스페이스와 객체 그룹핑
- `User` / `Profile`: 접근 제어와 실행 환경 관리
- `Resource Pool` / `Privilege`: 리소스 제어와 권한 분리

---

### administration 페이지 링크

- [Table · Projection]({{ '/administration-table-projection' | relative_url }})
- [Schema]({{ '/administration-schema' | relative_url }})
- [User]({{ '/administration-user' | relative_url }})
- [Profile]({{ '/administration-profile' | relative_url }})
- [Resource Pool · Privilege]({{ '/administration-resource-pool-privilege' | relative_url }})
