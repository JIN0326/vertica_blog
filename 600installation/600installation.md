---
title: Vertica(버티카) 설치: DB/MC 설치, 초기설정, 포트변경, 노드추가 - Vertica Blog
layout: default
description: "Vertica(버티카) 설치 및 초기 설정 가이드. Vertica 데이터베이스와 Management Console(MC) 설치, 클러스터 구성, 포트 변경, vcluster(REST API CLI) 사용법, Enterprise/Eon Mode에서 노드(Node) 및 서브클러스터(Subcluster) 추가 방법까지 단계별로 안내합니다."
keywords: "vertica, installation, setup, management console, mc, cluster, port change, vcluster, rest api, add node, add subcluster, enterprise mode, eon mode"
permalink: /600installation/
---

<div class="page-hero">
  <span class="chip">Installation</span>
  <h1>Vertica Installation & Setup</h1>
  <p>Vertica 데이터베이스와 Management Console(MC)의 설치, 클러스터 구성, 그리고 초기 설정 방법을 단계별로 안내합니다.</p>
</div>

<div class="page-layout">
  <div class="content-section" markdown="1">

  {% assign page_601 = site.pages | where: "name", "601_vertica-install.md" | first %}
  {{ page_601.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_602 = site.pages | where: "name", "602_initial-setup.md" | first %}
  {{ page_602.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_603 = site.pages | where: "name", "603_change-port.md" | first %}
  {{ page_603.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_604 = site.pages | where: "name", "604_mc-install.md" | first %}
  {{ page_604.content }}

<hr style="margin: 3rem 0;">

  {% assign page_605 = site.pages | where: "name", "605_vcluster.md" | first %}
  {{ page_605.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_606 = site.pages | where: "name", "606_add-node.md" | first %}
  {{ page_606.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_607 = site.pages | where: "name", "607_add-subcluster.md" | first %}
  {{ page_607.content }}


</div>
  <aside class="page-sidebar">
    <div class="sidebar-panel" style="padding-right: 1rem;">
      <h3>Installation</h3>
      <ul>
        <li><a href="#vertica-install">Vertica 설치</a></li>
        <li><a href="#initial-setup">Vertica 초기설정</a></li>
        <li><a href="#change-port">Vertica 포트변경</a></li>
        <li><a href="#mc-install">MC 설치</a></li>
        <li><a href="#vcluster">vcluster (REST API CLI)</a></li>
        <li><a href="#add-node">Add Node (Enterprise)</a></li>
        <li><a href="#add-subcluster">Add Subcluster (Eon)</a></li>
      </ul>
    </div>
  </aside>
</div>