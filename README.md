# Vertica 기술 블로그

이 저장소는 Vertica 데이터베이스의 핵심 개념, 운영 관리, 기초 학습 자료를 정리하고 공유하기 위한 Jekyll 기반 기술 블로그입니다.

## 📂 프로젝트 구조 (Project Structure)

프로젝트의 주요 파일과 디렉터리 구조는 다음과 같습니다.

-   `_layouts/default.html`: 모든 페이지의 기본 레이아웃 파일입니다. 헤더, 푸터, 내비게이션 등이 포함됩니다.
-   `_includes/sidebar.html`: 왼쪽의 'Quick Links' 사이드바 메뉴를 관리합니다.
-   `assets/css/style.css`: 사이트의 모든 스타일을 관리하는 중앙 CSS 파일입니다.
-   `assets/images/`: `00index.md` 등 페이지에서 사용하는 이미지 파일이 저장된 디렉터리입니다.
-   `*.md`: 각 페이지의 콘텐츠를 담고 있는 마크다운 파일입니다.
    -   **`00index.md` (메인 페이지)**: 블로그 소개 및 각 주요 콘텐츠 섹션으로의 링크를 제공합니다.
    -   **`01playbook.md` (Vertica Playbook)**: Vertica의 핵심 개념, 기술, 아키텍처 등 필수 정보를 요약합니다.
    -   **`02administration.md` (Vertica Administration)**: `Table`, `Projection`, `User` 등 주요 객체 관리 방법을 설명합니다.
    -   **`03performance.md` (Vertica Performance)**: 데이터 적재, 쿼리 튜닝 등 성능 최적화 기술을 통합하여 설명합니다.
    -   **`04utilization.md` (Vertica Utilization)**: 클라이언트 접속, 모니터링, Python 연동 등 실제 활용 방법을 통합하여 설명합니다.
    -   **`90fundamentals.md` (Vertica Fundamentals)**: DB 구성, 백업/복구, 튜닝 등 초심자를 위한 28가지 실습 주제를 나열합니다.
    -   **`91Class.md` (교육 신청)**: Vertica 교육 과정 및 신청 방법을 안내합니다.
    -   **`92QnA.md` (질문과 답변)**: GitHub Discussions와 연동된 Giscus를 통해 자유롭게 질문하고 답변하는 공개 Q&A 게시판입니다.
    -   **`93Reference.md` (고객사례)**: 금융, 기업, 의료 등 다양한 산업 분야의 Vertica 도입 사례를 소개합니다.

## ✍️ 콘텐츠 및 스타일 수정

-   **콘텐츠**: 각 페이지의 내용은 해당 `.md` 파일을 직접 수정하여 관리합니다.
-   **스타일**: 사이트의 전반적인 디자인은 `assets/css/style.css` 파일에서 수정합니다.
-   **Q&A 관리**: `92QnA.md` 페이지에 올라오는 질문과 답변은 GitHub 저장소의 Discussions 탭에서 직접 관리할 수 있습니다.
