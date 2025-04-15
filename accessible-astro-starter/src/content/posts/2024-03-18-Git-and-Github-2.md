---
title: Git area 에 대해
date: 2024-03-18 20:34:00 +09:00
categories: [Development, Git] # 메인 카테고리 , 보조 카테고리
tags:
  [
    Development,
    Git,
    Github
  ]
---

Git 으로 관리하는 경로의 working directory(area) 에서
Github Desktop 이나 git status 명령어를 통해 확인해보면,
commit 을 하지 않아도 파일에 어떠한 변경사항이든 확인할 수 있다.  

<img src="https://kybaq.github.io/assets/img/posts/2024-03-18-Git-area-1.png" alt="command line 과 gui 를 통해 살펴본 화면">

이때, git add 를 입력하면
수정사항이 반영된 상태의 파일은 staging area 에 존재하는 상태다.
이후 commit 을 통해 이 사항을 반영하게 되면 local repository(commit) area 로 이동해 기록이 되는 것이다. 최종적으로, push 를 진행하면 수정된 내용이나 수정한 사람 또는 날짜 등 다양한 정보가 remote repository 에 함께 기록된다.

