---
title: "Vertica Python 연동: vertica-python 및 verticapy 활용 - Vertica Blog"
layout: default
description: "Vertica와 Python 연동 방법을 알아봅니다. vertica-python 드라이버를 사용한 기본 연결 및 데이터 조회, verticapy를 활용한 인데이터베이스 분석 방법을 설명합니다."
keywords: "vertica, python, vertica-python, verticapy, sqlalchemy, pandas, vdataframe"
canonical_url: "https://jin0326.github.io/vertica_blog/400utilization/400utilization#python"
---

<div id="python" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody">
## Python 연동

<div class="architecture-section">
    <p class="section-description">Vertica는 Python 환경과의 원활한 연동을 위해 <strong><code>vertica-python</code></strong>(기본 드라이버)과 <strong><code>verticapy</code></strong>(데이터 과학 라이브러리) 두 가지 주요 라이브러리를 제공합니다.</p>
    
<div class="architecture-subsection">
      <h3 class="section-subtitle">1. 기본 연결 및 데이터 조회 (vertica-python)</h3>
      <p class="section-description"><code>vertica-python</code>은 DB-API 2.0을 준수하는 저수준(low-level) 드라이버로, SQL 쿼리를 실행하고 결과를 가져오는 기본적인 데이터베이스 접근에 사용됩니다. 더 자세한 정보는 <a href="https://github.com/vertica/vertica-python" target="_blank">공식 GitHub 저장소</a>에서 확인할 수 있습니다.</p>
      <div class="syntax-box">
        <strong>Pandas DataFrame으로 변환 예제:</strong>
        <pre><code>import vertica_python
import pandas as pd

conn_info = {
    'host': '&lt;DB_IP&gt;', 'port': 5433,
    'user': 'dbadmin', 'password': 'password', 'database': 'VDB'
}

with vertica_python.connect(**conn_info) as connection:
    cursor = connection.cursor()
    cursor.execute("SELECT * FROM public.sales")
    df = pd.DataFrame(cursor.fetchall(), columns=[desc[0] for desc in cursor.description])
    print(df.head())</code></pre>
      </div>
    </div>

    <div class="architecture-subsection">
      <h3 class="section-subtitle">2. 인데이터베이스 분석 (verticapy)</h3>
      <p class="section-description"><code>verticapy</code>는 Pandas와 유사한 인터페이스를 제공하는 고수준(high-level) 라이브러리로, 데이터를 클라이언트로 가져오지 않고 데이터베이스 내에서 직접 분석 및 머신러닝을 수행할 수 있게 해줍니다. 연결 시 고가용성(HA) 옵션을 설정할 수 있습니다.</p>
      <div class="syntax-box">
        <strong>vDataFrame 생성 및 사용 예제 (HA 포함):</strong>
        <pre><code>import verticapy as vp

# 고가용성 옵션을 포함한 연결 정보
conn_info = {
    "host": "&lt;DB_IP&gt;",
    "port": 5433,
    "database": "EONDB",
    "user": "vertica",
    "password": "your_password",
    "backup_server_node": ["&lt;BACKUP_IP_1&gt;:5433", "&lt;BACKUP_IP_2&gt;:5433"], 
    "connection_load_balance": True
}

# verticapy 연결 생성
vp.connect("my_vertica_conn", **conn_info)

# 'product_dimension' 테이블을 vDataFrame으로 로드
data = vp.vDataFrame("product_dimension")

# 데이터 확인
print(data.head())

# 연결 해제
vp.close_connect("my_vertica_conn")</code></pre>
      </div>
    </div>
  </div>
</div>
</div>