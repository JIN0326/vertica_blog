---
title: Vertica Fundamentals
layout: default
---

<div class="page-hero">
  <span class="chip">Fundamentals</span>
  <h1>Vertica 학습하기</h1>
  <p>대용량 데이터 분석을 위한 컬럼 기반 DB, Vertica의 시작. <br> 핵심 개념부터 차근차근 데이터 분석의 탄탄한 기본기를 완성해보세요.</p>
</div>

<div class="content-section" markdown="1">
  <div class="fundamentals-grid" id="toc-container">
    <!-- Card 1 -->
    <div class="fundamentals-card">
      <div>
        <h3>1. Vertica DB 구성</h3>
        <ul>
          <li>– 3대 노드로 구성</li>
        </ul>
      </div>
      <a href="{{ '/01playbook#architecture' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 2 -->
    <div class="fundamentals-card">
      <div>
        <h3>2. Vertica DB 구성 후 초기세팅</h3>
        <ul>
          <li>– Configuration paramter 적용</li>
          <li>– profile, user, role 적용</li>
          <li>– resource pool 생성</li>
        </ul>
      </div>
      <a href="{{ '/02administration' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 3 -->
    <div class="fundamentals-card">
      <div>
        <h3>3. Vertica DB의 private/public IP 이해 및 설정</h3>
        <ul>
          <li>– export address 적용</li>
        </ul>
      </div>
      <a href="#" class="button">학습하기</a>
    </div>
    <!-- Card 4 -->
    <div class="fundamentals-card">
      <div>
        <h3>4. Vertica DB의 role/grant 생성</h3>
        <ul>
          <li>– schema, table 별 생성 및 적용</li>
          <li>– with grant option</li>
        </ul>
      </div>
      <a href="{{ '/02administration#privilege' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 5 -->
    <div class="fundamentals-card">
      <div>
        <h3>5. Vertica vbr backup</h3>
        <ul>
          <li>– full, object, copycluster 수행</li>
        </ul>
      </div>
      <a href="{{ '/02administration#backup-restore' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 6 -->
    <div class="fundamentals-card">
      <div>
        <h3>6. Vertica Scrutinize 생성</h3>
        <ul>
          <li>– 생성 후 압축해제하여 파일내 로그들 확인</li>
        </ul>
      </div>
      <a href="{{ '/02administration#cluster-operation' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 7 -->
    <div class="fundamentals-card">
      <div>
        <h3>7. Vertica MC 및 DB 등록</h3>
        <ul>
          <li>– apikey 확인</li>
        </ul>
      </div>
      <a href="{{ '/04utilization#monitoring' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 8 -->
    <div class="fundamentals-card">
      <div>
        <h3>8. Vertica 정기점검 쿼리 수행</h3>
        <ul>
          <li>– 쿼리 내의 시스템테이블 전반적으로 확인</li>
        </ul>
      </div>
      <a href="{{ '/04utilization#monitoring' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 9 -->
    <div class="fundamentals-card">
      <div>
        <h3>9. Vertica EONDB 구성</h3>
        <ul>
          <li>– minio storage 구성</li>
        </ul>
      </div>
      <a href="{{ '/01playbook#eonmode' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 10 -->
    <div class="fundamentals-card">
      <div>
        <h3>10. Vertica DB Node 추가</h3>
        <ul>
          <li>– ksafe 이해</li>
        </ul>
      </div>
      <a href="{{ '/02administration#cluster-operation' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 11 -->
    <div class="fundamentals-card">
      <div>
        <h3>11. Vertica DB의 RE IP 수행</h3>
        <ul>
          <li>– 리눅스 환경의 ip 세팅 확인</li>
        </ul>
      </div>
      <a href="#" class="button">학습하기</a>
    </div>
    <!-- Card 12 -->
    <div class="fundamentals-card">
      <div>
        <h3>12. Vertica DB 업그레이드</h3>
        <ul>
          <li>– 작업에 대한 백업 및 수행결과 확인 프로세스 이해</li>
        </ul>
      </div>
      <a href="#" class="button">학습하기</a>
    </div>
    <!-- Card 13 -->
    <div class="fundamentals-card">
      <div>
        <h3>13. Vertica SP 이해 및 수행</h3>
        <ul>
          <li>– perform, execute, raise, exception</li>
        </ul>
      </div>
      <a href="{{ '/05development' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 14 -->
    <div class="fundamentals-card">
      <div>
        <h3>14. vsql_command + vsql_option</h3>
        <ul>
          <li>– all list 숙지</li>
        </ul>
      </div>
      <a href="{{ '/03coretech#vsql' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 15 -->
    <div class="fundamentals-card">
      <div>
        <h3>15. pj query 작성</h3>
        <ul>
          <li>– schema, table, pj, partition, segment, statistics, uptodate, segexpress, ordexpress, colcnt, rowcnt, dvcnt</li>
        </ul>
      </div>
      <a href="{{ '/02administration#table-projection' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 16 -->
    <div class="fundamentals-card">
      <div>
        <h3>16. transaction/statement/session/rollback/commit 이해</h3>
        <ul>
          <li>– 관계이해</li>
        </ul>
      </div>
      <a href="{{ '/05development' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 17 -->
    <div class="fundamentals-card">
      <div>
        <h3>17. sessions/ user_sessions 이해</h3>
        <ul>
          <li>– 어느 상황에서 확인해야하는 지</li>
        </ul>
      </div>
      <a href="{{ '/02administration#cluster-operation' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 18 -->
    <div class="fundamentals-card">
      <div>
        <h3>18. constraint table 이해</h3>
        <ul>
          <li>– 시스템테이블 확인 및 확인 가능 컬럼 이해</li>
        </ul>
      </div>
      <a href="{{ '/02administration#table-projection' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 19 -->
    <div class="fundamentals-card">
      <div>
        <h3>19. Comments 구문 + dynamic query 작성</h3>
        <ul>
          <li>– comments 구문을 쓸 수 있는 dynamic query 작성</li>
        </ul>
      </div>
      <a href="{{ '/05development' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 20 -->
    <div class="fundamentals-card">
      <div>
        <h3>20. Vxperf 수행</h3>
        <ul>
          <li>– vioper, vnetperf, vcpuperf</li>
        </ul>
      </div>
      <a href="#" class="button">학습하기</a>
    </div>
    <!-- Card 21 -->
    <div class="fundamentals-card">
      <div>
        <h3>21. LOG 파일 이해 및 경로 확인</h3>
        <ul>
          <li>– vertica install, db create, db start, vbr 수행 시 로그</li>
        </ul>
      </div>
      <a href="{{ '/02administration#cluster-operation' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 22 -->
    <div class="fundamentals-card">
      <div>
        <h3>22. DB, MC의 기본 PORT 변경</h3>
        <ul>
          <li>– 각 각 변경하여 테스트</li>
        </ul>
      </div>
      <a href="{{ '/02administration' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 23 -->
    <div class="fundamentals-card">
      <div>
        <h3>23. System Table CHECK</h3>
        <ul>
          <li>– 쿼리, 에러, 로드, 접속중인 세션, 라이선스 확인하는 테이블</li>
        </ul>
      </div>
      <a href="{{ '/04utilization#monitoring' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 24 -->
    <div class="fundamentals-card">
      <div>
        <h3>24. Functions - data/string/math/rank/row_number</h3>
        <ul>
          <li>– 주요 function 확인</li>
        </ul>
      </div>
      <a href="{{ '/05development' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 25 -->
    <div class="fundamentals-card">
      <div>
        <h3>25. COPY구문 (khy)</h3>
        <ul>
          <li>– basic, delimiter, recordterminator, null처리, filler, 특수문자, 여러파일포맷, 등</li>
        </ul>
      </div>
      <a href="{{ '/03coretech#copy' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 26 -->
    <div class="fundamentals-card">
      <div>
        <h3>26. Explain + tuning</h3>
        <ul>
          <li>– 튜닝 케이스 이해</li>
        </ul>
      </div>
      <a href="{{ '/03coretech#explain' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 27 -->
    <div class="fundamentals-card">
      <div>
        <h3>27. copy from vertica/ export to vertica</h3>
        <ul>
          <li>– shell로 작성</li>
        </ul>
      </div>
      <a href="{{ '/03coretech#export' | relative_url }}" class="button">학습하기</a>
    </div>
    <!-- Card 28 -->
    <div class="fundamentals-card">
      <div>
        <h3>28. CTAS</h3>
        <ul>
          <li>– CTAS, LIKE, COPY_TABLE</li>
        </ul>
      </div>
      <a href="{{ '/05development' | relative_url }}" class="button">학습하기</a>
    </div>
  </div>
</div>