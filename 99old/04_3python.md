---
title: Python 연동
layout: default
---

<div class="page-hero">
  <span class="chip">Utilization</span>
  <h1>Python 연동</h1>
  <p>vertica-python 라이브러리를 사용하여 Python 환경에서 Vertica에 접속하고, 데이터를 처리 및 분석하는 방법을 알아봅니다.</p>
</div>

<div class="content-section content-section-centered" markdown="1">
  ## vertica-python 라이브러리

  `vertica-python`은 Vertica 데이터베이스와 통신하기 위한 순수 Python DB-API 클라이언트입니다. 별도의 외부 드라이버(ODBC 등) 없이 Python만으로 Vertica에 쉽게 연결하고 데이터를 다룰 수 있습니다.

  ### 설치

  pip를 사용하여 간단하게 설치할 수 있습니다.

  ```bash
  pip install vertica-python
  ```

  ---

  ## 사용 예제

  ### 1. 데이터베이스 연결

  연결 정보를 딕셔너리 형태로 정의하고 `vertica_python.connect()` 함수를 호출하여 연결 객체를 생성합니다.

  ```python
  import vertica_python

  conn_info = {
      'host': '192.168.1.101',
      'port': 5433,
      'user': 'dbadmin',
      'password': 'password',
      'database': 'VDB'
  }

  # with 문을 사용하면 블록 종료 시 자동으로 연결이 닫힙니다.
  with vertica_python.connect(**conn_info) as connection:
      cursor = connection.cursor()
      # ... 쿼리 실행 ...
  ```

  ### 2. 쿼리 실행 및 결과 가져오기

  `cursor` 객체를 사용하여 SQL 쿼리를 실행하고, `fetchall()`, `fetchone()` 등으로 결과를 가져올 수 있습니다.

  ```python
  cursor.execute("SELECT product_name, price FROM public.products WHERE category = 'Electronics'")

  # 모든 결과 가져오기
  results = cursor.fetchall()
  for row in results:
      print(row)

  # 하나의 결과만 가져오기
  # row = cursor.fetchone()
  ```

  ### 3. Pandas DataFrame으로 변환

  Pandas 라이브러리와 함께 사용하면 조회 결과를 DataFrame으로 쉽게 변환하여 데이터 분석 작업을 편리하게 수행할 수 있습니다.

  ```python
  import pandas as pd

  cursor.execute("SELECT * FROM public.sales")
  
  # 컬럼 이름 가져오기
  columns = [desc[0] for desc in cursor.description]
  
  # DataFrame으로 변환
  df = pd.DataFrame(cursor.fetchall(), columns=columns)
  
  print(df.head())
  ```
</div>