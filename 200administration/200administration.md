---
title: Vertica(버티카) 운영: 테이블, 파티션, 사용자, 백업, 클러스터 관리 - Vertica Blog
layout: default
description: "Vertica(버티카) 운영 및 관리 가이드. Table, Projection, Partition, Schema, User, Profile, Resource Pool, Privilege, Locks, Tuple Mover, Statistics, Backup & Restore(vbr), Cluster Operation 등 Vertica 데이터베이스의 전반적인 관리 방법을 다룹니다."
keywords: "table, projection, partition, schema, user, profile, resource pool, privilege, locks, tuple mover, mergeout, purge, statistics, backup, restore, vbr, cluster operation, ctas, like"
permalink: /200administration/
---
<div class="page-hero">
  <span class="chip">Administration</span>
  <h1>Vertica Administration</h1>
  <p>Vertica 데이터베이스의 핵심 객체(Table, Schema, User) 관리부터 리소스 풀, 권한, 백업/복구, 클러스터 운영까지 전반적인 관리 방법을 알아봅니다.</p>
</div>
<div class="page-layout">
  <div class="content-section" markdown="1">

  {% assign page_201 = site.pages | where: "name", "201_table-projection.md" | first %}
  {{ page_201.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_202 = site.pages | where: "name", "202_partition.md" | first %}
  {{ page_202.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_203 = site.pages | where: "name", "203_schema.md" | first %}
  {{ page_203.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_204 = site.pages | where: "name", "204_user.md" | first %}
  {{ page_204.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_205 = site.pages | where: "name", "205_profile.md" | first %}
  {{ page_205.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_206 = site.pages | where: "name", "206_resource-pool.md" | first %}
  {{ page_206.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_207 = site.pages | where: "name", "207_privilege.md" | first %}
  {{ page_207.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_208 = site.pages | where: "name", "208_locks.md" | first %}
  {{ page_208.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_209 = site.pages | where: "name", "209_tuple-mover.md" | first %}
  {{ page_209.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_210 = site.pages | where: "name", "210_statistics.md" | first %}
  {{ page_210.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_211 = site.pages | where: "name", "211_backup-restore.md" | first %}
  {{ page_211.content }}

  <hr style="margin: 3rem 0;">

  {% assign page_212 = site.pages | where: "name", "212_cluster-operation.md" | first %}
  {{ page_212.content }}

  </div>

  <aside class="page-sidebar">
    <div class="sidebar-panel" style="padding-right: 1rem;">
    <h3>Administration</h3>
    <ul>
      <li><a href="#table-projection">Table · Projection</a></li>
      <li><a href="#partition">Partition</a></li>
      <li><a href="#schema">Schema</a></li>
      <li><a href="#user">User</a></li>
      <li><a href="#profile">Profile</a></li>
      <li><a href="#resource-pool">Resource Pool</a></li>
      <li><a href="#privilege">Privilege</a></li>
      <li><a href="#locks">Locks</a></li>
      <li><a href="#tuple-mover">Tuple Mover</a></li>
      <li><a href="#statistics">Statistics</a></li>
      <li><a href="#backup-restore">Backup & Restore</a></li>
      <li><a href="#cluster-operation">Cluster Operation</a></li>
    </ul>
    </div>
  </aside>
</div>
