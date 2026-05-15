﻿# Vertica Blog

Vertica 분석 플랫폼에 대한 기술 정보와 사용법을 공유하기 위한 Jekyll 기반의 기술 블로그입니다.

> Repository: [https://github.com/JIN0326/vertica_blog/](https://github.com/JIN0326/vertica_blog/)

## 🚀 프로젝트 소개

이 프로젝트는 Vertica 데이터베이스에 대한 다양한 주제를 다루며, 다음과 같은 기술 문서와 가이드를 포함합니다.

- **Vertica Playbook**: 핵심 개념, 아키텍처, 기술 요소 등
- **Vertica Administration**: 테이블, 스키마, 사용자 관리 등 운영 가이드
- **Vertica CoreTech**: 데이터 적재, 쿼리 튜닝 등 핵심 기술
- **Vertica Utilization**: 클라이언트 접속, 모니터링, Python 연동 등 활용법
- **기타 서비스**: 교육 신청, Q&A, 고객사례 등

## 📂 프로젝트 구조

프로젝트의 주요 파일 및 디렉토리 구조는 다음과 같습니다.

```text
.
├── _data/
│   └── navigation.yml    # 네비게이션 설정
├── _layouts/
│   └── default.html      # 기본 레이아웃
├── _includes/
│   ├── header.html       # 헤더
│   ├── footer.html       # 푸터
│   └── sidebar.html      # 사이드바
├── assets/
│   ├── css/style.css     # 스타일시트
│   └── images/           # 이미지
├── 00index.md            # 메인 페이지
├── 01playbook.md         # Vertica Playbook
├── 02administration.md   # Vertica Administration
├── 03coretech.md         # Vertica CoreTech
├── 04utilization.md      # Vertica Utilization
├── 05auth_security.md    # Vertica Auth & Security
├── 90fundamentals.md     # Vertica Fundamentals
├── 91Class.md            # 교육 신청
├── 92QnA.md              # Q&A
├── 93Reference.md        # 고객사례
├── 94lifecycle.md        # 제품 수명주기
├── _config.yml           # Jekyll 설정 파일
└── README.md             # 프로젝트 설명 파일
```

## 📑 상세 콘텐츠 목차

### 📘 Vertica Playbook (`01playbook.md`)
* **Vertica란 무엇인가**: SQL 데이터 웨어하우스, 분석 및 ML, 쿼리 엔진 특징
* **Vertica 핵심 기술**: Native Columnar, 압축, Projections, 분산 쿼리 엔진, MCP 연동, MPP Scale-out 등 6가지 핵심 요소
* **Vertica 포트폴리오**: 온프레미스, 클라우드, Eon Mode 등 다양한 배포 옵션
* **Vertica 아키텍처**: Pure-MPP 아키텍처 및 타 시스템과의 상세 비교
* **Vertica 연계**: Kafka, Spark, HDFS, Object Storage 통합 가이드
* **Vertica MCP 연동**: LLM과 Vertica를 연동하여 자연어 기반 데이터 분석을 수행하는 방법
* **Vertica EonMode**: 컴퓨팅/스토리지 분리 아키텍처의 이점 및 실전 Use Case

### 🛠️ Vertica Administration (`02administration.md`)
* **Table · Projection**: 논리 모델(Table)과 물리 저장소(Projection)의 분리 및 분산 정책
* **Partition**: 대용량 테이블 관리 및 성능 최적화를 위한 파티셔닝
* **Schema**: 시스템 기본 스키마 설명 및 생성/관리 방법
* **User**: 사용자 생성, 관리 및 계정 잠금 해제 방법
* **Profile**: 보안 정책 및 패스워드 제약 조건 설정
* **Resource Pool**: 메모리 및 동시성 제어를 통한 워크로드 관리
* **Privilege**: 객체 접근 권한 및 스키마 권한 상속 체계
* **Locks**: 동시성 제어를 위한 잠금(Lock) 메커니즘과 모니터링 방법
* **Backup & Restore**: vbr 도구를 이용한 스냅샷 백업 및 복구 프로세스
* **Cluster Operation & Diagnostics**: DB 기동/중지, 장애 조치, 진단 파일 생성 등 핵심 클러스터 운영 가이드

### ⚙️ Vertica CoreTech (`03coretech.md`)
* **vsql (CLI)**: CLI 접속 옵션 및 유용한 메타 커맨드(`\d`, `\timing`, `\x` 등)
* **Monitoring**: Management Console(MC) 및 Grafana를 활용한 모니터링 방법
* **Clients**: vsql(CLI) 및 DBeaver(GUI) 접속 가이드
* **Copy (데이터 적재)**: 대용량 데이터 로딩 구문, 옵션 및 에러 데이터 처리
* **Export (데이터 내보내기)**: vsql, EXPORT TO PARQUET/VERTICA 등 다양한 데이터 추출 방법
* **EXPLAIN (쿼리 분석)**: 실행 계획 분석(EXPLAIN/PROFILE) 및 성능 최적화 포인트

### 🚀 Vertica Utilization (`04utilization.md`)
* **Python 연동**: `vertica-python` 라이브러리를 사용한 데이터 처리 및 분석
* **Apache Airflow 연동**: Airflow를 활용한 데이터 파이프라인 자동화 및 스케줄링
* **Apache Iceberg 연동**: Spark 기반 Iceberg 테이블 생성 및 Vertica 연동 조회
* **Apache Polaris 연동**: 중앙 메타데이터 서비스 Polaris와 동적 연동
* **AWS Glue & PyIceberg**: PyIceberg와 AWS Glue를 활용한 서버리스 데이터 운영
* **Trino & Iceberg 연동**: 분산 SQL 쿼리 엔진 Trino와 Iceberg를 활용한 데이터 레이크하우스 연동 아키텍처

### 🔒 Vertica Auth&Security (`05auth_security.md`)
* **Client Authentication**: Hash, LDAP, TLS, OAuth 등 다양한 클라이언트 인증 방식 설정 및 관리
* **LDAP 인증**: Active Directory 또는 OpenLDAP 서버와 연동하여 통합 계정 인증 구현
* **TLS 보안**: 서버-클라이언트 간 상호 인증(Mutual Mode)으로 통신 암호화 및 보안 강화
* **Okta 연동**: Okta IdP를 활용하여 데이터베이스 및 MC에 대한 SSO(Single Sign-On) 구현

### 🚀 Vertica Installation (`06installation.md`)
* **Vertica 설치**: Vertica 데이터베이스 설치를 위한 사전 요구사항 및 설치 과정
* **초기 설정**: `admintools`를 사용한 데이터베이스 클러스터 생성 및 초기 구성
* **MC 설치**: Management Console 설치 및 데이터베이스 등록 방법

### 🔗 기타 서비스 페이지
* **Class (`91Class.md`)**: Vertica 교육 과정 안내 및 신청 방법 (메일 문의)
* **QnA (`92QnA.md`)**: Giscus를 이용한 기술 문의 및 답변 게시판
* **Reference (`93Reference.md`)**: 금융, 기업/공공, 의료 분야별 실제 고객 도입 사례
* **Lifecycle (`94lifecycle.md`)**: 제품 릴리즈 주기, 지원 정책, OS 호환성 정보

## 🔗 외부 관련 링크

- **Vertica Docs (공식 문서)**: https://docs.vertica.com/26.1.x/en/
- **Vertica Partner**: http://www.gtgsc.com/gtg/sub/bigdata/vertica.php