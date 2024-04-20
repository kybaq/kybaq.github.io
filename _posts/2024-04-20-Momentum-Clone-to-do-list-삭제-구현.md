---
title: To do list 에서 요소 삭제하는 기능 구현
date: 2024-04-20 21:00:00 +09:00
categories: [Development, JavaScript] # 메인 카테고리 , 보조 카테고리
tags: [Development, Web, JavaScript, TIL]
---

## 목적

Momentum 이라는 크롬 확장 앱을 clone coding 하는 중인데, 여기서 to do list 를 작성해보고 싶었다.

localstorage 를 이용해 내용을 추가하는 것은 무리 없이 구현 했는데,
추가한 내용을 삭제하는 과정을 구현하려는 과정이다.

## 방법

현재 todos 라는 배열을 localstorage에 저장해서 할 일 목록을 만들었다.

// 여기 코드 추가

여기서, filter 함수를 이용해서 새로운 배열을 반환하고 저장하면 될 것이다!

filter 함수 사용법 정리 추가.
