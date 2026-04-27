# Vertica Blog

Vertica 분석 플랫폼에 대한 기술 정보와 사용법을 공유하기 위한 Jekyll 기반의 기술 블로그입니다.

> Repository: [https://github.com/JIN0326/vertica_blog/](https://github.com/JIN0326/vertica_blog/)

## 🚀 프로젝트 소개

이 프로젝트는 Vertica 데이터베이스에 대한 다양한 주제를 다루며, 다음과 같은 내용을 포함합니다.

- **Vertica Playbook**: 핵심 개념, 아키텍처, 기술 요소 등
- **Vertica Administration**: 테이블, 스키마, 사용자 관리 등 운영 가이드
- **Vertica CoreTech**: 데이터 적재, 쿼리 튜닝 등 핵심 기술
- **Vertica Utilization**: 클라이언트 접속, 모니터링, Python 연동 등 활용법
- **Vertica Fundamentals**: Vertica 학습을 위한 기초 지식

## 📂 프로젝트 구조

프로젝트의 주요 파일 및 디렉토리 구조는 다음과 같습니다.

```
.
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
├── 90fundamentals.md     # Vertica Fundamentals
├── 91Class.md            # 교육 신청
├── 92QnA.md              # Q&A
├── 93Reference.md        # 고객사례
├── _config.yml           # Jekyll 설정 파일 (추정)
├── navigation.yml        # 네비게이션 설정 (추정)
└── README.md             # 프로젝트 설명 파일
```

### 📄 콘텐츠 페이지

- **`00index.md`**: 블로그 메인 페이지
- **`01playbook.md`**: Vertica Playbook
  - Vertica란 무엇인가, Vertica 핵심 기술, Vertica 포트폴리오, Vertica 아키텍처, Vertica 연계, Vertica EonMode
- **`02administration.md`**: Vertica Administration
  - Table · Projection, Schema, User, Profile, Resource Pool, Privilege, Backup & Restore
- **`03coretech.md`**: Vertica CoreTech
  - vsql 명령어, 데이터 적재 (COPY), 쿼리 튜닝 (Explain)
- **`04utilization.md`**: Vertica Utilization
  - 클라이언트 접속, 모니터링 및 Grafana, Python 연동
- **`90fundamentals.md`**: Vertica 학습 로드맵
- **`91Class.md`**: Vertica 교육 신청 안내
- **`92QnA.md`**: Vertica 관련 Q&A
- **`93Reference.md`**: Vertica 고객사례

## 💻 로컬에서 실행하기

로컬 환경에서 블로그를 실행하고 테스트하려면 아래 단계를 따르세요. (Ruby와 Bundler가 설치되어 있어야 합니다.)

1.  **의존성 설치:**
    ```bash
    bundle install
    ```

2.  **Jekyll 서버 실행:**
    ```bash
    bundle exec jekyll serve
    ```

3.  웹 브라우저에서 `http://localhost:4000` 주소로 접속합니다.

## 🔗 관련 링크

- **Vertica Docs**: https://docs.vertica.com/26.1.x/en/
- **Vertica Partner**: http://www.gtgsc.com/gtg/sub/bigdata/vertica.php