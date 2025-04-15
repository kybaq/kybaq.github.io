---
title: 24/05/13 컴포넌트와 JSX
date: 2024-05-10 21:00:00 +09:00
categories: [Development, React] # 메인 카테고리 , 보조 카테고리
tags: [Development, React, JSX]
---

## 목적

리액트에 대해서 배우기 시작했다.
사실, 정확히 어떤 지점에서 필요한지는 완전히 이해하지는 못했다.

다만, 바닐라 JS 에서 해쉬 라우팅을 이용하면 SPA 구현이 가능은 하지만 url 의 변경이 없으니
사용성 측면에서 불편함(서버에서는 메인 페이지 접속 후 기록이 안 남음, 뒤로 가기 불가, SEO 에서 불리함 등)이 있기 때문에 해소하기 위한 도구라는 것은 이해했다.

이게 몇 점 짜리 답변일지..

아무튼 핵심 요소인 컴포넌트와 JSX에 대해 간단히 강의 내용을 요약해보기로 했다.

### React Component 란??

UI 를 재사용이 가능한 여러 조각으로 나눈 뒤, 각 조각을 개별적으로 다룰 수 있게 해주는 것.

형태나 개념적으로 JS 의 함수와 유사하다.
임의의 입력(props 라고 함)을 받고, 화면에 어떻게 표현이 될지 작성하는 React 요소(Element)를 반환한다.

예시를 살펴보자.

```typescript
fucntion Welcome(props) {
    return <h1>Hello, {props.name}</h1>; // 화면에 어떻게 표현될지 알려주는 HTML 요소와 유사한 React 엘리먼트 를 반환.
}
```

위 함수 형태가 컴포넌트라고 이해하면 되겠다.
물론, 클래스 형태로 작성할 수 있지만 더 이상 많이 사용하지 않는다고 한다(강의 피셜).

#### Component 조금 더 살펴보기

```typescript
import React from "react"; // 우리는 모듈 구조 기반으로 작성할 것이라 대부분 이렇게 분리해서 작성한 뒤 import 하는 방식.

const App = () => {
  // JS 사용하는 영역

  return <div>{/*JSX 사용하는 영역*/}</div>;
  // 실제 react 엘리먼트를 반환 하는 역할임. HTML 과 굉장히 유사한 구조.
};

// 바깥으로 App 컴포넌트를 내보냄. 그럼 다른 파일에서 import React 처럼 가져와서 쓸 수 있다.
export default App;
```

### JSX 란??

JS 를 확장한 문법으로, JS 모든 기능을 포함하고 있다.
HTML 을 품은 JS라고 생각하자. H품J, like 초품아 ㅋㅋ

```typescript
import React from "react";

function App() {
  return (
    <div>
      <Grand />
    </div>
  );
}

export default App;

function Grand() {
  return (
    <div>
      조상 <Mom />
    </div>
  );
}

function Mom() {
  return (
    <div>
      <div> 부모 </div> <Child />
    </div>
  );
}

function Child() {
  return <div> 애기 </div>;
}
```

이렇게 작성한 예시가 있는데, 이때 반드시 `<div>` 같은 요소를 부모 요소로 추가해야한다.
이것은 가상 DOM 에 관한 얘기인데, 별도로 정리해야 할 것 같다.

흔히 리액트를 왜 사용하냐고하면, 강의에서 가상 DOM 어쩌고 라는 말을 사람들이 달달 외워서 말한다고 그거 틀렸다 말한 것을 주워들은 적 있긴하다.

#### JSX 는 어떻게 해석이 될까?

브라우저에서 JSX 를 해석할 수는 없다.
따라서, babel 과 같은 tool 로 변환을 해준다고 한다.

```typescript
const elem = {
    <h1 className="greeting">
        Hello, world!
    </h1>
}

// 변환 후

const elem = React.createElemnet(
    "h1",
    {className: "greeting"},
    "Hello, world!"
)
```

이렇게 말이다.

마지막으로 몇가지 주의사항을 적고 마무리해보자.

```typescript
function Child() {
  return <button></button>; /* 이렇게 태그를 닫을 때는 상관이 없지만 */
  return (
    <input />
  ); /* 닫은 태그가 없는 경우에는 반드시 self-closing tag 형태로 작성해야 오류가 발생하지 않는다. */
}
```

이렇게 html 태그는 반드시 닫힌 형태여야만 한다.

다음, 앞서 말했듯이 react 컴포넌트를 return 할 때 반드시 최상위 부모 하나만 있어야한다.

```typescript
function Child() {
  return <div> 애기 </div> <div> 형제 </div>; /* 이 역시 오류 발생함. */
}

function Child() {
  return <>
  <div> 애기 </div> <div> 형제 </div>; /* 이렇게 하면 오류가 생기지 않으며 div 2개만 브라우저에서 인식함. */
  </>
}
```

최상위 태그는 하나만 존재해야한다.
최상위 태그의 이름을 비워도 문제가 없다! 브라우저에서는 최상위 태그를 무시하고 자식 div 2개만 표시하게 된다.

#### JS 문법을 사용하고 싶을 때

```typescript
const NUMBER = 100;

function Child() {
  return (
    <>
      <div> 애기 </div> <div> {NUMBER} </div>; /* 마치 템플릿 리터럴 처럼! */
    </>
  );
}

const arr = [1];

function Child() {
  return <>{arr.forEach((elem) => console.log(elem))}</>;
}
```

이렇게 중괄호를 이용하면 변수 사용이나 함수 정의 등 JS 문법을 사용할 수 있다.

마지막, css 속성을 부여하고 싶을 때는 객체로 선언해줘야 한다.

```typescript
const App = () => {
    const divStyle = {
        color: "red",
        font-size: "20px";
    }

    function Child() {
    return <>
    <div style={divStyle}> 애기 </div> <div style={divStyle}> 형제 </div>; /* 이렇게 하면 오류가 생기지 않으며 div 2개만 브라우저에서 인식함. */
    </>
    }
}
```

여기까지 간단히 알아본 컴포넌트와 jsx 였다!
