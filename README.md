# Vertica 기술 블로그

이 저장소는 Vertica 데이터베이스의 핵심 개념, 운영 관리, 기초 학습 자료를 정리한 Jekyll 기반 기술 블로그입니다.

## 페이지 구성

-   **`index.md` (메인 페이지)**
    -   블로그의 전체적인 소개와 함께 `Playbook`, `Administration`, `Fundamentals` 섹션으로 연결되는 링크를 제공합니다.
    -   Vertica의 두 가지 주요 아키텍처 모드(Enterprise, Eon)를 시각적으로 비교 설명합니다.

-   **`playbook.md` (Vertica Playbook)**
    -   Vertica의 핵심 개념과 강점, 주요 기술, 포트폴리오, 아키텍처 장점 등 Vertica를 이해하기 위한 필수 정보를 요약합니다.
    -   각 주제별 상세 페이지로 이동할 수 있는 버튼과 목차를 제공합니다.

-   **`administration.md` (Vertica Administration)**
    -   Vertica 데이터베이스 운영에 필요한 핵심 관리 항목들을 다룹니다.
    -   `Table`, `Projection`, `Schema`, `User`, `Profile`, `Resource Pool`, `Privilege` 등 주요 객체의 개념과 관리 방법을 설명합니다.

-   **`fundamentals.md` (Vertica Fundamentals)**
    -   Vertica를 처음 시작하는 사용자를 위한 28가지 기초 학습 항목을 제공합니다.
    -   DB 구성, 초기 설정, 백업/복구, 노드 관리, 쿼리 튜닝 등 실습 위주의 주제로 구성되어 있습니다.

## 사용 방법
1.  **로컬에서 테스트:**
    -   Ruby와 Jekyll이 설치되어 있어야 합니다.
    -   터미널에서 `bundle install`을 실행하여 의존성을 설치합니다.
    -   `bundle exec jekyll serve` 명령어로 로컬 서버를 실행하고 `http://localhost:4000`에서 확인합니다.

2.  **GitHub Pages 배포:**
    -   이 저장소를 GitHub Pages에 배포하려면 `main` 또는 `master` 브랜치의 루트 디렉터리를 GitHub Pages 소스로 지정합니다.

3.  **콘텐츠 및 스타일 수정:**
    -   페이지 내용은 각 `.md` 파일을 수정하여 관리합니다.
    -   모든 스타일은 `assets/css/style.css` 파일에서 중앙 관리됩니다. 디자인 변경이 필요할 경우 이 파일을 수정하세요.

## 프로젝트 구조

-   `_layouts/default.html`: 모든 페이지의 기본 레이아웃 파일입니다. 헤더, 푸터, 드롭다운 내비게이션이 포함되어 있습니다.
-   `assets/css/style.css`: 사이트의 모든 스타일을 관리하는 중앙 CSS 파일입니다.
-   `assets/images/`: `index.md` 등 페이지에서 사용하는 이미지 파일이 저장된 디렉터리입니다.
-   `*.md`: 각 페이지의 콘텐츠를 담고 있는 마크다운 파일입니다.
