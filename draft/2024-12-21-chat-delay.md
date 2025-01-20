---
title: Supabase Realtime 을 사용한 채팅 기능에서 간헐적으로 발생하는 지연 현상
date: 2024-12-21 21:00:00 +09:00
categories: [Development, Project] # 메인 카테고리 , 보조 카테고리
tags: [Development, TroubleShooting]
---

## 문제 상황

[관련한 JIRA 이슈](https://gatherhere.atlassian.net/jira/software/projects/GH/boards/1/backlog?assignee=712020%3Af9657b51-c3a8-4ded-9e62-8fee85fc620b&selectedIssue=GH-21)

이전에 구현해두었던 채팅 기능에서 간헐적으로 지연현상이 발생한다.

지금 추측하는 원인으로는, 최소 2-3일 정도 DB에 요청이 들어오지 않으면 유휴상태에 들어가는 것이 원인일까 싶다.
하지만, 관련된 내용을 아직 찾지 못했으므로 지속적으로 추적해보려고 한다.

## 일지

### 24/12/21

채팅 기능을 사용해보았지만, 지연 상황없이 바로 전송이 이루어짐.