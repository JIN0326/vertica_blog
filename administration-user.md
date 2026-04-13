---
title: User
layout: default
---

# User

Vertica에서 `User`는 데이터베이스에 접근하는 주체입니다. 사용자를 적절하게 구성하고 권한을 관리하는 것이 안정적인 운영의 시작입니다.

## 사용자 생성

- `CREATE USER`로 사용자를 생성합니다.
- 사용자 패스워드, 인증 모드를 지정할 수 있습니다.
- 비밀번호 정책과 계정 잠금 정책을 운영 정책에 맞춰 구성합니다.

예시:

```sql
CREATE USER analyst IDENTIFIED BY 'StrongPassw0rd';
```

## 사용자 권한 관리

- 사용자에게 직접 권한을 부여하거나 역할(Role)을 통해 간접적으로 권한을 관리합니다.
- `GRANT`로 객체 권한을 부여하고, `REVOKE`로 제거합니다.
- 사용자 그룹 대신 역할을 사용하면 권한 변경 시 관리가 용이합니다.

예시:

```sql
GRANT SELECT ON analytics.sales TO analyst;
GRANT analyst_role TO analyst;
```

## 사용자 관리 팁

- `SELECT * FROM users;`로 사용자 목록을 확인합니다.
- 사용하지 않는 사용자 계정은 즉시 비활성화하거나 삭제합니다.
- 감사 추적을 위해 사용자 생성, 권한 변경 시 로그를 기록합니다.
