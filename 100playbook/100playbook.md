---
title: Vertica(버티카) Playbook: 핵심 개념, 아키텍처, EonMode, MCP/Kafka 연동 - Vertica Blog
layout: default
description: "Vertica(버티카) Playbook: Vertica의 핵심 개념, 기술, 아키텍처를 소개합니다. Columnar, MPP, EonMode 아키텍처, MCP(LLM) 연동, Kafka, Spark, HDFS 연계 방법 및 Vertica 포트폴리오, Revive를 통한 마이그레이션 등 Vertica의 모든 것을 심층 분석합니다."
keywords: "eonmode, mcp, pure-mpp, columnar, architecture, kafka, spark, hdfs, object storage, portfolio, revive, migration"
permalink: /100playbook/
---
<div class="page-hero">
  <span class="chip">Playbook</span>
  <h1>Vertica Playbook</h1>
  <p>Vertica의 기본 개념과 강점, Pure-MPP 아키텍처, EonMode 등 핵심 기술과 더불어 MCP, Kafka와 같은 외부 시스템과의 연동 방법을 알아봅니다.</p>
</div>

<div class="page-layout">
  <div class="content-section" markdown="1">

  {% assign page_101 = site.pages | where: "name", "101_what-is-vertica.md" | first %}
  {{ page_101.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_102 = site.pages | where: "name", "102_technology.md" | first %}
  {{ page_102.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_103 = site.pages | where: "name", "103_portfolio.md" | first %}
  {{ page_103.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_104 = site.pages | where: "name", "104_architecture.md" | first %}
  {{ page_104.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_105 = site.pages | where: "name", "105_embracing.md" | first %}
  {{ page_105.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_106 = site.pages | where: "name", "106_mcp-integration.md" | first %}
  {{ page_106.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_107 = site.pages | where: "name", "107_eonmode.md" | first %}
  {{ page_107.content }}

  </div>

  <aside class="page-sidebar">
  <div class="sidebar-panel" style="padding-right: 1rem;">
    <h3>Playbook</h3>
    <ul>
      <li><a href="#what-is-vertica">Vertica란 무엇인가</a></li>
      <li><a href="#technology">Vertica 핵심 기술</a></li>
      <li><a href="#portfolio">Vertica 포트폴리오</a></li>
      <li><a href="#architecture">Vertica 아키텍처</a></li>
      <li><a href="#embracing">Vertica 연계</a></li>
      <li><a href="#mcp-integration">Vertica MCP 연동</a></li>
      <li><a href="#eonmode">Vertica EonMode</a></li>
    </ul>
  </div>
  </aside>

</div>
