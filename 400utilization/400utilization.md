---
title: Vertica(버티카) 활용: Python, Kafka, dbt, Airflow, Iceberg, Trino 연동 - Vertica Blog
layout: default
description: "Vertica(버티카) 활용 가이드. Python(vertica-python, verticapy), Kafka, dbt, Apache Airflow, Apache Iceberg, Apache Polaris, AWS Glue, PyIceberg, Trino 등 다양한 데이터 생태계와 Vertica를 연동하여 데이터 파이프라인을 구축하고 활용하는 방법을 알아봅니다."
keywords: "python, vertica-python, verticapy, kafka, dbt, airflow, apache iceberg, apache polaris, aws glue, pyiceberg, trino, federated query"
permalink: /400utilization/
---

<div class="page-hero">
  <span class="chip">Utilization</span>
  <h1>Vertica Utilization</h1>
  <p>Python, Airflow, 그리고 Apache Iceberg와 Polaris를 활용한 엔터프라이즈 데이터 레이크하우스 구축 및 데이터 파이프라인 자동화 방법을 소개합니다.</p>
</div>
<div class="page-layout">
  <div class="content-section" markdown="1">

  {% assign page_401 = site.pages | where: "name", "401_python.md" | first %}
  {{ page_401.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_402 = site.pages | where: "name", "402_kafka.md" | first %}
  {{ page_402.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_403 = site.pages | where: "name", "403_dbt.md" | first %}
  {{ page_403.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_404 = site.pages | where: "name", "404_airflow.md" | first %}
  {{ page_404.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_405 = site.pages | where: "name", "405_iceberg.md" | first %}
  {{ page_405.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_406 = site.pages | where: "name", "406_polaris.md" | first %}
  {{ page_406.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_407 = site.pages | where: "name", "407_aws-glue-pyiceberg.md" | first %}
  {{ page_407.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_408 = site.pages | where: "name", "408_trino-iceberg.md" | first %}
  {{ page_408.content }}

  </div>
  
<aside class="page-sidebar">
    <div class="sidebar-panel" style="padding-right: 1rem;">
      <h3>Utilization</h3>
      <ul>
        <li><a href="#python">Python 연동</a></li>
        <li><a href="#kafka">Kafka 연동</a></li>
        <li><a href="#dbt">DBT 연동</a></li>
        <li><a href="#airflow">Apache Airflow 연동</a></li>
        <li><a href="#iceberg">Apache Iceberg 연동</a></li>
        <li><a href="#polaris">Apache Polaris 연동</a></li>
        <li><a href="#aws-glue-pyiceberg">AWS Glue & PyIceberg 연동</a></li>
        <li><a href="#trino-iceberg">Trino & Iceberg 연동</a></li>
      </ul>
    </div>
  </aside>
</div>