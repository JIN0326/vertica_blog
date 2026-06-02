---
title: Vertica(버티카) 핵심 기술: vsql, 모니터링, Copy, Export, Explain - Vertica Blog
layout: default
description: "Vertica(버티카) 핵심 기술 가이드. vsql(CLI) 사용법, MC/Grafana 모니터링, Data Collector, DBeaver/JDBC/ODBC 클라이언트 연동, 대용량 데이터 적재(Copy) 및 내보내기(Export to Parquet/Iceberg), 그리고 EXPLAIN/PROFILE을 통한 쿼리 분석 및 튜닝 방법을 알아봅니다."
keywords: "vsql, cli, monitoring, management console, mc, grafana, data collector, jdbc, odbc, dbeaver, copy, export, parquet, iceberg, explain, profile"
permalink: /300coretech/
---
<div class="page-hero">
  <span class="chip">Core Tech</span>
  <h1>Vertica Core Tech</h1>
  <p>Vertica 개발 및 운영에 필수적인 핵심 기술인 vsql, Monitoring, Clients, 데이터 적재(Copy), 데이터 내보내기(Export) 그리고 쿼리 분석(Explain)에 대해 상세히 알아봅니다.</p>
</div>
<div class="page-layout">
  <div class="content-section" markdown="1">

  {% assign page_301 = site.pages | where: "name", "301_vsql.md" | first %}
  {{ page_301.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_302 = site.pages | where: "name", "302_monitoring.md" | first %}
  {{ page_302.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_303 = site.pages | where: "name", "303_data-collector.md" | first %}
  {{ page_303.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_304 = site.pages | where: "name", "304_clients.md" | first %}
  {{ page_304.content }}

<hr style="margin: 3rem 0;">

  {% assign page_305 = site.pages | where: "name", "305_copy.md" | first %}
  {{ page_305.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_306 = site.pages | where: "name", "306_export.md" | first %}
  {{ page_306.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_307 = site.pages | where: "name", "307_explain.md" | first %}
  {{ page_307.content }}

</div>

  <aside class="page-sidebar">
  <div class="sidebar-panel" style="padding-right: 1rem;">
    <h3>Core Tech</h3>
    <ul>
      <li><a href="#vsql">vsql</a></li>
      <li><a href="#monitoring">Monitoring</a></li>
      <li><a href="#data-collector">Data Collector</a></li>
      <li><a href="#clients">Clients</a></li>
      <li><a href="#copy">Copy</a></li>
      <li><a href="#export">Export</a></li>
      <li><a href="#explain">Explain</a></li>
    </ul>
  </div>
  </aside>
</div>
