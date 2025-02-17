---
title: Jest 를 이용한 유닛 테스트
date: 2025-02-15 21:00:00 +09:00
categories: [Development] # 메인 카테고리 , 보조 카테고리
tags: [Jest, Unit Test]
---

## Jest 설치

[Vite, TypeScript, React Testing Library, Jest 설정하기 - (2) React 테스트 환경 설정 및 Jest 에러 해결](https://bichoninthefront.tistory.com/107)

위 블로그를 참고했다. 현재 나의 환경과 완전히 동일한 상황에서 설정한 블로그라 선택했다.
공식문서를 통해 설정하려고 했으나, 짧은 시간 내 정확히 어떤 것을 선택할지 애매하던 참이었는데 잘 됐다.

```zsh
npm install --save-dev jest @types/jest ts-node ts-jest @testing-library/react identity-obj-proxy jest-environment-jsdom @testing-library/jest-dom jest-svg-transformer
```

`--save-dev` 파라미터를 사용하면, 개발 환경에서만 사용하는 dependency 가 된다.
배포 환경에서는 jest 가 필요 없으니, 파라미터를 사용해주자.

## package.json 수정

이후, Jest 실행을 편리하게 진행위해 package.json에 아래의 명령어를 추가하자.

"scripts":{
    ...
    "test": "jest"
 }

이렇게 하면 `npm run test` 를 해주면, jest 가 동작하게 될 것이다.

## jest 설정파일 추가

다음으로는 루트 폴더에 jest.config.ts 파일을 생성 후, 아래와 같이 입력하자.

```ts
export default {
  testEnvironment: "jsdom",
  transform: {
    "^.+\\.tsx?$": "ts-jest",
  },
  moduleNameMapper: {
    "^.+\\.svg$": "jest-svg-transformer",
    "\\.(css|less|sass|scss)$": "identity-obj-proxy",
  },
  setupFilesAfterEnv: ["<rootDir>/jest.setup.ts"],
};
```
여기서 부터는 내가 찾은 내용이다. 기존 자료들이랑 차이가 좀 있다. [출처](https://stackoverflow.com/questions/77328459/cannot-find-module-testing-library-jest-dom-extend-expect-from-jest-setup-js)
루트 폴더에 추가로 jest.setup.ts 파일을 생성 후 다음과 같이 입력하자.

```ts
import '@testing-library/jest-dom';
```
