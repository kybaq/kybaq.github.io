---
title: 24/05/13 React props 란?
date: 2024-05-10 21:00:00 +09:00
categories: [Development, React] # 메인 카테고리 , 보조 카테고리
tags: [Development, React, JSX]
---

## 목적

컴포넌트와 JSX 에 대해 간단히 다루었으니, props 가 동작하는 방법도 적어보자.

## props 란?

1. 컴포넌트끼리의 정보 교환 방식, 부모 컴포넌트에서 출발해 자식 컴포넌트 방향으로만 전달이 된다. 굳이 비유해보자면 이벤트 켑쳐링인가?
2. 반드시 읽기 전용, 수정이나 변형하지 않아야 한다.

일단 뭔지도 모르지만, 규칙부터 적었다.
예시를 통해서 알아보자.

```typescript
/* App.jsx */
import React from "react";

function App() {
  return <Grand />;
}

function Grand() {
  const lastName = "Kim";
  return <Parent lastName={lastName} />;
}

function Parent(prop) {
  return <Kid lastName={prop} />;
}

function Kid(prop) {
  return <div>{prop.lastName}</div>;
}

export default App;
```

위 예제는, 최상위 요소인 `Grand()` 엘리먼트부터 `Kid()` 엘리먼트로 값을 전달하는 예시다.
이 때, 자식 엘리먼트에게 lastName 이라는 변수 명으로 "Kim" 을 넘겨준다.

자식은 이를 객체 형태로 받음.
매개변수는 아무 이름이나 상관 없음.

매개변수명.변수명 형태로 접근이 가능!

또는, 한 가지 방법이 더 존재한다.

```typescript
/* App.jsx */
import React from "react";

function App() {
  return (
    <Layout>
      <div> div is Here. </div>
    </Layout>
  );
}

function Layout(props) {
  return (
    <main>
      <header>헤더입니다.</header>
      {props.children}
      <footer>푸터입니다.</footer>
    </main>
  );
}

export default App;
```
