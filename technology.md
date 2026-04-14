---
title: Vertica 핵심 기술
layout: default
---

# Vertica 핵심 기술
<figure class="image-gallery">
  <img src="{{ '/image/Vertica5tech_1.png' | relative_url }}" alt="Vertica 5 tech slide 1" />
  <img src="{{ '/image/Vertica5tech_2.png' | relative_url }}" alt="Vertica 5 tech slide 2" />
  <img src="{{ '/image/Vertica5tech_3.png' | relative_url }}" alt="Vertica 5 tech slide 3" />
  <img src="{{ '/image/Vertica5tech_4.png' | relative_url }}" alt="Vertica 5 tech slide 4" />
  <img src="{{ '/image/Vertica5tech_5.png' | relative_url }}" alt="Vertica 5 tech slide 5" />
  <figcaption>Vertica의 5대 기술 요소를 시각적으로 확인할 수 있는 슬라이드 이미지들입니다.</figcaption>
</figure>

Vertica는 다음 5가지 기술 요소를 중심으로 고성능 분석을 지원합니다.

## 1. Native Columnar Storage

- 필요한 컬럼만 읽어오는 설계로 I/O를 크게 감소
- 컬럼 단위 저장과 처리를 위한 최적화
- 쿼리 성능과 저장 효율을 동시에 확보

## 2. Compression / Encoding

- 컬럼 단위 저장으로 높은 압축률 달성
- 12가지 인코딩 및 압축 알고리즘 지원
- 75% 이상의 스토리지 절감 효과

## 3. MPP Scale-out

- 순수 MPP 구조로 노드 간 종속성 제거
- Exabyte 수준 확장성 제공
- 노드 장애 시에도 분산 쿼리 수행이 가능

## 4. Distributed Query

- 여러 노드에 분산된 데이터를 효율적으로 처리
- 최적화된 쿼리 계획으로 대형 분석 워크로드 지원

## 5. Projections

- 쿼리 성능을 가속화하는 물리계층 인덱스 개념
- 필요한 컬럼/행만 빠르게 조회 가능
