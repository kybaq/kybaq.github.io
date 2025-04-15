---
title: Tanstack Query 기본 사용법
date: 2024-06-16 21:00:00 +09:00
categories: [Development, React] # 메인 카테고리 , 보조 카테고리
tags: [Development, React, Tanstack Query]
---

> Tanstack Query(구 React Query)는 서버 상태 관리 라이브러리다.

## 클라이언트 상태란?

클라이언트 측에서 관리하는 데이터다.
그 예시로는 세션 데이터, 캐시 데이터, 로컬 저장소가 있다.

- 세션 데이터: 현재 로그인한 유저 정보, 현재 페이지, 폼 입력값 등
- 캐시 데이터: 정적 리소스(이미지, 스크립트), API 응답 데이터
- 로컬 저장소: 웹 스토리지(local, session), 쿠키

이런 클라이언트 상태는, 서버와 연결되어 있지 않은 상태로 내부에서만 사용하는 값으로 생각할 수 있다.

따라서, 클라이언트 간 동일한 데이터를 갖도록 유지하는 것이 힘들다.

## 서버 상태

서버 측에서 관리하는 데이터로, 데이터베이스, 서버 세션, 서버 캐시 등이 있다.

- 데이터 베이스: 회원 정보나, 쇼핑몰 물건의 정보 등
- 서버 세션: 사용자의 세션 데이터
- 서버 캐시: 클라이언트가 자주 요청하는 데이터 등

대부분의 웹 사이트는 데이터를 보관하는 별도의 서버가 필요하다.
이용자가 많은 웹 사이트의 경우 유저의 정보와 작성한 게시글만 저장한다고 하더라도, 큰 용량을 차지하게 된다!

이처럼, 현대의 웹 앱은 클라이언트 상태보다는 서비스를 이용하는 데 있어 핵심인 서버 상태를 주로 다루게 된 것이다.

그런데, 서버와 통신하게 되니 서비스의 대부분이 비동기 작업으로 구성된 것이다.

이를 위해 Redux Thunk, Redux-saga 등의 미들웨어를 통해 중간에 비동기 작업의 결과를 받아온 뒤 `dispatch()` 하도록 만들어 주었다.

하지만, 여러 문제 점이 있었다.

일단, 기존 Redux의 단점 만큼 미들웨어들도 복잡한 보일러플레이트가 존재했다. Redux Toolkit 의 보일러플레이트 조차도 복잡한 데, 미들웨어도 만만치 않았기 때문이다.

또한, 미들웨어도 사실 서버 상태를 관리해주는 역할은 아니었기 때문에 비동기 작업의 상태(pending, fulfilled, rejected)에 따라 개발자가 직접 관리해주어야 했다. 게다가 자주 요청하는 작업은 캐싱하는 코드 또한 필요했다.

이를 해결하는 것이 바로 Tanstack Query!

컴포넌트에서 훅을 이용하는 것 처럼 서버상태를 편리하게 관리할 수 있게 된다.

## Tanstack Query

Tanstack Query 의 3가지 핵심 개념이 존재한다!

1. 데이터 캐싱: 동일한 데이터를 할 때 잦은 API 호출을 막기 위해 캐싱한다.
2. 자동 리패칭: 데이터가 변경되었을 대 자동으로 리패칭하여 최신 상태를 유지한다.
3. 쿼리 무효화: 특정 이벤트가 발생했을 때 쿼리를 무효화하고 데이터를 다시 가져오게 만든다.

이 세 가지 주요기능을 통해, 서버 상태를 보다 쉽게 관리할 수 있다!

## Tanstack Query 의 주요 hook

1. `useQuery()`: 데이터를 가져오는 역할(get, Read)

2. `useMutation()`: 데이터를 수정하는 역할(modify, Create, Update, Delete)

3. `invalidateQueries()`: 쿼리 무효화가된 데이터를 다시 가져오게 만드는 역할(refresh)

## 기본 사용법

먼저, 프로젝트 tanstack query 를 설치해준다.

```sh
yarn add @tanstack/tanstack-query
```

Context API나 redux 기반 상태 관리 도구에서 사용했던 것 처럼 Provider 가 필요하다!

서버상태 관리가 필요한 컴포넌트 중 최상위 컴포넌트에 다음과 같이 작성해주면 된다.

```javascript
// main.jsx
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App.jsx";
import "./index.css";
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";

// store 를 생성하는 것과 유사하다.
const queryClient = new QueryClient();

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    {/* Provider 를 작성해준다. */}
    <QueryClientProvider client={queryClient}>
      <App />
    </QueryClientProvider>
  </React.StrictMode>
);
```

이제 3가지 hook 의 사용법에 대해 알아보자.

## `useQuery()`

이 hook 은 데이터를 가져오는 경우에 사용할 수 있다.

`useQuery()` 를 사용하는 간단한 에시를 살펴보자.

```javascript
import { useQuery } from "@tanstack/react-query";
import axios from "axios";
import React from "react";

const App = () => {
  const fetchTodos = async () => {
    const response = await axios.get("http://localhost:4000/todos");

    return response.data;
  };

  const {
    data: todos,
    isLoading,
    isError
  } = useQuery({
    // queryKey 를 한 번 가져오고 난 뒤 변경이 생기기 전까지는 캐싱한다는 말??
    queryKey: ["todos"],
    // queryFn: () => {},
    queryFn: fetchTodos
  });

  if (isLoading) return <div>로딩중입니다...</div>;
  else if (isError) return <div>오류가 발생했습니다.</div>;

  return (
    <div>
      <h3>Tanstack Query</h3>
      <ul>
        {todos.map((todo) => {
          return (
            <li key={todo.id} style={{}}>
              <h4>{todo.title}</h4>
              <p>{todo.isDone ? "Done" : "Working"}</p>
            </li>
          );
        })}
      </ul>
    </div>
  );
};

export default App;
```

`queryFn` 을 통해 가져온 값에 대해 `queryKey` 를 식별자로서 사용하는 거라고 봐도 좋겠다.

tanstack query 가 값을 캐싱한다는 것은 이 `queryKey` 가 가리키는 값을 캐싱하는 것이다.

추가로, `queryKey` 는 반드시 배열이어야 하며 내부에는 어떤 값이든 사용할 수 있다.

아래 예시는 tanstack query 공식 문서에서 가져온 예시다.

```jsx
// An individual todo
useQuery({ queryKey: ['todo', 5], ... })

// An individual todo in a "preview" format
useQuery({ queryKey: ['todo', 5, { preview: true }], ...})

// A list of todos that are "done"
useQuery({ queryKey: ['todos', { type: 'done' }], ... })
```

일종의 조건을 걸어서 사용할 수 있다.

추가!

```js
const {
  data: todos,
  isLoading,
  isError
} = useQuery({
  // queryKey 를 한 번 가져오고 난 뒤 변경이 생기기 전까지는 캐싱한다는 말??
  queryKey: ["todos"],
  // queryFn: () => {},
  queryFn: fetchTodos
  select: (todos) => {
    return todos.map((todo) => {
      return { ...todo, test: 1};
    })
  }
});
```

이렇게 하면 가져온 query 모두에 대해 test 라는 프로퍼티를 추가할 수 있다!

## `useMutation()`

이 hook 은 데이터를 추가, 수정, 삭제하는 경우에 사용할 수 있다.

아래 예시를 보자.

```jsx
import { useMutation, useQuery, useQueryClient } from "@tanstack/react-query";
import axios from "axios";
import React, { useState } from "react";

const App = () => {
  const queryClient = useQueryClient();

  const [todoItem, setTodoItem] = useState(null);

  const addTodo = async (newTodo) => {
    await axios.post("http://localhost:4000/todos", newTodo);
  };

  // useMutation 을 바로 사용하지 않고, 객체를 생성함.
  const mutation = useMutation({
    // post 요청을 수행하는 비동기 함수 정의
    mutationFn: addTodo,
    // 성공한 뒤, 쿼리 무효화를 통해 갱신해줌.
    onSuccess: () => {
      alert("데이터 삽입이 성공했습니다.");
      queryClient.invalidateQueries(["todos"]);
    }
  });

  if (isLoading) return <div>로딩중입니다...</div>;
  else if (isError) return <div>오류가 발생했습니다.</div>;

  return (
    <div>
      <form
        action=""
        onSubmit={(evt) => {
          evt.preventDefault();
          // mutationFn 에 필요한 인자를 넘겨준다.
          // json-server 를 사용해 id 는 자동 할당됨
          mutation.mutate({ title: todoItem, isDone: false });
        }}
      >
        <input
          type="text"
          value={todoItem}
          onChange={(evt) => setTodoItem(evt.target.value)}
        />
        <button type="submit">추가</button>
      </form>
    </div>
  );
};

export default App;
```

게시글을 불러오는 logic 은 제외를 했다.

mutation 객체를 정의한 뒤, `mutationFn` 에 게시글 추가 함수를 정의해준다.

이후, 게시글을 추가하면 새로고침을 해야만 추가한 게시물이 나타난다.

그래서, mutation 객체에서 `onSucess` 를 통해 post 요청이 정상적으로 이루어진 후 쿼리 무효화를 통한 refresh 진행을 해주면 된다!

`invalidateQueries()` 를 사용해주면, 게시글 추가 이후 자동으로 갱신이 된다....

얼마나 편한지..! 정말 신세계다!

다음에는 이렇게 편리한 tanstack query 의 동작 원리를 잠시 살펴보도록하자.
