---
title: Vite + React + TypeScript 프로젝트 기반 PWA
date: 2025-03-05 11:00:00 +09:00
categories: [Development, TypeScript] # 메인 카테고리 , 보조 카테고리
tags: [Development, TypeScript]
---

npm create @vite-pwa/pwa@latest Rhythmix -- --template react-ts

![vite pwa 실행 시 나타나는 프롬프트](../assets/img/posts/2025-03-05-Vite-PWA-Setup.png)

https://thdud4479.tistory.com/310 참고한 링크임

Select a strategy :
generateSW injectManifest 서비스 워커를 생성하고 관리하는 방식 선택
generateSW   	injectManifest
서비스 워커 파일을 자동 생성
필요한 캐시 로직과 파일목록을 Vite가 생성함 	개발자가 직접 서비스 워커를 작성
복잡한 PWA기능을 구현할 때, 세부적으로 서비서 워커 동작을 제어할 수 있음

Select a behavior :
prompt for update auto update PWA의 업데이트 방식 설정
prompt for update 	 auto update
- 사용자에게 업데이트 요청 알림 띄워줌
- 사용자가 수동으로 업데이트 진행할 수 있도록 하여 새 버전 알림을 전달할 수 있다 	- 새 버전이 배포되면 자동으로 업데이트 실행
- 서비스를 항상 최신 버전으로 유지할 수 있다