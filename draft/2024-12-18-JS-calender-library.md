---
title: 적절한 캘린더 라이브러리 선택하기
date: 2024-08-12 21:00:00 +09:00
categories: [Development, Project] # 메인 카테고리 , 보조 카테고리
tags: [Development, 회고]
---

## 개요

현재 FullCalender 를 사용중이다.
"JS 캘린더 라이브러리" 라고 검색하니 가장 상단에 나타나면서, 관련된 블로그 게시글도 많았다.

여러 시행착오를 겪을 때, 참고하기 좋은 선택지라고 생각했다.
그래서 사용하고 있었지만, 불편한 점이 한 두가지가 아니었다.

디자인을 원하는 대로 바꾸기가 생각보다 훨씬 어려웠다.
css 선택자의 우선순위를 이리저리 조정해가면서, 디자인 시안을 맞추기위해 열심히 노력을 부었지만...
완전히 시안을 반영할 수는 없었다.

일단은 어쩔 수 없으니 그대로 진행했지만, 시안을 반영해서 고려했던 기능들까지 다 구현하고 싶었다.
그래서 여러 선택지를 찾아보기로 했다.

## 선택지

### FullCalender 를 그대로 사용한다

우리가 처음에 FullCalender 를 선택한 이유는, 앞서 언급했듯이 많은 사람들이 사용하고 있었고 관련한 정보도 많았다.
그래서 여유가 없던 기간에도 빠르게 사용할 수 있었기 때문에 선택한 것이 크다.

하지만, css 에 대한 제약이 있어 원하는 형태의 기능을 구현하지 못했다.
그래서 디자이너님과 어느정도 타협을 보았던 상황인데, 이제는 내가 디자인 시안대로 만들어내고 싶어진 상황이니... 

css 조정이 자유로운 라이브러리를 찾아야할 것 같다.

### toast.ui calender

NHN 에서 만든 toast ui 중 calender 에 해당하는 라이브러리다.
FullCalender 와 유사한데, 장점은 한글 문서가 있다는 것이다!

사실, 지금 상황에서는 장점이 많지 않은 것으로 보인다.
결국에 css 를 자유롭게 조정할 수 있는 선택지는 아니다..

그냥 FullCalender 한글판인 느낌, 지금 우리 상황에서는 굳이 변경할 필요는 없는 것 같다.

### Calender.js

`A powerful Calendar. With multiple views and tons of settings, Calendar.js can be tailored to suit your every need. Lightweight.`

원하는 대로 수정할 수 있는 라이브러리라고 한다.
하지만, 결국 내가 필요한 것은 css 설정이 자유로워야한다.

직접 살펴보았으나, 역시 css 를 원하는 대로 조절하긴 어려울 것 같았다.

### 마지막... `직접 만든다.`

쓸만할 정도로 만드려면, 라이브러리화해서 나중에 관리할 수 있도록 만들면 어떨까 싶다.
tailwind 기반으로 스타일을 직접 설정할 수 있는 것!

라이브러리 형태로 만드는 방식을 고려해보자.
