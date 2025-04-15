---
title: 24/05/31 Supabase 기본 사용법
date: 2024-05-27 19:00:00 +09:00
categories: [Development, Supabase] # 메인 카테고리 , 보조 카테고리
tags: [Development, Supabase]
---

## SUPABASE 란?

> Postgre SQL 을 지원하는 오픈 소스 백엔드 서비스다.
> 별도의 백엔드 서버를 구성하지 않아도, 쉽게 관계형 데이터 베이스를 구축할 수 있다.

## 기본 설정하기

[수파베이스](https://supabase.com/)

링크에 접속한 뒤, 로그인을 진행한다.

이후 아래의 Start your project 를 눌러 Dashboard 로 이동한다.

![supabase 메인 페이지](../assets/img/posts/2024-05-31-Supabase-Usage-1.png)

Dashboard 에 진입하면, new Project 를 눌러 새 프로젝트를 생성하면 된다.

![supabase 메인 페이지](../assets/img/posts/2024-05-31-Supabase-Usage-2.png)

현재는 무료 플랜으로 2개의 프로젝트까지 생성 가능하다.
500MB 의 저장소를 제공하며, API 요청 횟수는 제한 없다!

사용할 때마다 변경되었는지 확인하는게 좋겠다.

![supabase 프로젝트 설정 페이지](../assets/img/posts/2024-05-31-Supabase-Usage-3.png)

프로젝트의 이름과 비밀번호를 설정하고 Region 까지 설정하면 된다!
서울로 하는 것이 좋겠다.

설정을 마치고 나면, API 와 url 을 조회할 수 있다.
anon key 는 공개되어도 상관 없다고 하는데, 그냥 찝찝하니 사진은 올리지 않았다.

왼쪽의 메뉴는 다음과 같다.

![supabase 프로젝트 설정 페이지](../assets/img/posts/2024-05-31-Supabase-Usage-4.png)

Table Editor 는 말 그대로, 테이블 형태로 데이터를 조회하고 수정할 수 있다.
SQL Editor 는 AI 를 이용해 도움을 받을 수 있다!

Database 는 생성된 테이블들을 모두 조회할 수 있다.

![supabase 프로젝트 설정 페이지](../assets/img/posts/2024-05-31-Supabase-Usage-5.png)

Authentication 은 회원 가입, 로그인 또는 로그아웃을 위한 인증 기능을 제공한다.

Storage 는 업로드한 파일을 보관하는 파일 전용이다.

Edge Function 은 CLI 를 제공하는 것 같다.

API Docs 는 supabase 엣허 이용할 수 있는 API 에 대한 정보가 있다!

이후, Editor 를 이용해 데이터를 관리하는 것은 다른 포스팅으로 다뤄보자.
