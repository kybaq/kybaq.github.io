---
title: Next.js 의 Middleware 를 통해 Protected Route 구현하기
date: 2025-04-08
categories: [Next.js, Project] # 메인 카테고리 , 보조 카테고리
tags: [Next.js]
---

## 문제 상황

- 현재, 게시글을 조회할 때는 /maindetail/`{uuid}` 경로를 사용하고, 이 게시글을 수정할 땐 /post/`{uuid}` 경로를 사용.
- url 을 /maindetail/ 에서 /post/ 로 변경해버리면 다른 이의 게시글을 무단으로 수정이 가능함.