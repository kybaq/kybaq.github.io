---
title: 24/05/27 React Redux 기본 사용법
date: 2024-05-27 19:00:00 +09:00
categories: [Development, React] # 메인 카테고리 , 보조 카테고리
tags: [Development, React, JavaScript]
---

## 목적

전역 상태 관리를 통해서, props drilling 의 한계를 극복하고자 한다!
따라서, redux 사용법을 간단히 정리해보자.

### 설치 방법

```sh
yarn add redux react-redux
```

간단히 설치할 수 있다.

### 사용 방법

먼저, 프로젝트 초기화할 때 `redux` 라는 디렉토리를 하나 만들자.

```plaintext
redux-excercise
├─ .eslintrc.cjs
├─ .gitignore
├─ README.md
├─ index.html
├─ package.json
├─ src
│  ├─ App.css
│  ├─ App.jsx
│  ├─ assets
│  │  └─ react.svg
│  ├─ index.css
│  ├─ main.jsx
│  └─ redux --> ✨ Redux 를 사용하기 위해 설정할 것.
│     ├─ config
│     │  └─ configStore.js --> Redux 에서 모든 상태를 관리하는 `Store` 를 생성하는 설정.
│     └─ modules --> 우리가 만들 state 를 module 형태로 모아둘 곳. reducer 를 통해 setState 처럼 dispatch 를 통해 상태를 관리함.
├─ vite.config.js
└─ yarn.lock
```

디렉토리 및 파일 생성 이후, 설정이 필요하다.

```jsx
// configStore.js

import { combineReducers, createStore } from "redux";
import counter from "../modules/counter";

// 1) rootReducer 를 만듦.

const rootReducer = combineReducers({
  counter
}); // reudcer 를 인자로 넣는데, modules 에 정의한 state 를 관리하기 위한 logic 을 reducer 라고 함.

// 2) store 생성
const store = createStore(rootReducer); // deprecated. 추후 redux toolkit 에서 대체됨

// 3) 만든 store 를 내보낸다.
export default store;
```

    ❗ `createStore()` 현재 deprecated 로, redux toolkit 에서 다른 것으로 대체할 것이다.

```jsx
// main.jsx

import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App.jsx";
import "./index.css";
import { Provider } from "react-redux";
import store from "./redux/config/configStore.js";

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </React.StrictMode>
);
```

`useContext` 를 사용하는 것 처럼 `Provider` 를 통해 원하는 스코프를 설정해준다.

```jsx
// App.jsx

import "./App.css";
import { useSelector } from "react-redux";

function App() {
  const counterReducer = useSelector((state) => state.counter); // state 가 store 에 존재하는 모든 state 를 지칭하는 게 됨. 아마 첫 인수는 이게 들어오는 것 같음.

  console.log(counterReducer);

  return <div>App</div>;
}

export default App;
```

위 세 파일을 다음과 같이 작성해준다.
