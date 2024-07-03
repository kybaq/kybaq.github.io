---
title: Next.js Route Handler 에 대해
date: 2024-07-04 21:00:00 +09:00
categories: [Development, Next.js] # 메인 카테고리 , 보조 카테고리
tags: [Development, Next.js]
---

> Next.js 의 Route Handler 를 통해, 백엔드 구현이 가능하다! 이에 대해 알아보자

## Route Handler 란?

공식 홈페이지의 설명을 인용하자면, 주어진 route 에서 사용하는 웹 요청 / API 응답을 처리하는 custom handler 구현이 가능한 기능이라고 한다.

데이터를 요청받는 과정과, 이를 클라이언트에게 반환해주는 것까지 구현할 수 있는 것이 백엔드의 역할인데, 이것을 구현할 수 있게 해주는 게 route handler 다!
쉽게 말하면, 프론트 엔드 구현을 하면서 한 번에 DB 와 통신을 주고 받을 수 있다는 것이 특징이다!

- GET

```typescript
// localhost:3000/api/test/route.ts
export const GET = async (request: Request) => {
  const response = await fetch("http://localhost:4000/todos");
  const todos = await response.json();

  if (!todos) {
    return new Response("Todo is not found", { status: 404 });
  } else {
    return Response.json({
      todos: todos
    });
  }
};
```

json-server 를 이용해 `http://localhost:4000/todos` 에 아래와 같은 json data 를 구성했다.

```json
{
  "todos": [
    {
      "id": "1",
      "title": "커피 마시기",
      "contents": "아메리카노",
      "isDone": true
    },
    {
      "id": "2",
      "title": "점심 먹기",
      "contents": "라면",
      "isDone": false
    },
    {
      "id": "3",
      "title": "과제하기",
      "contents": "포켓몬 도감 구현",
      "isDone": false
    }
  ]
}
```

- POST

```typescript
export const POST = async (request: Request) => {
  const { title, contents } = await request.json();

  const response = await fetch("http://localhost:4000/todos", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ title, contents, isDone: false })
  });

  const todo = await response.json();

  return Response.json({
    todo
  });
};
```

이렇게 하면, POST 요청을 보냈을 때 그에 대한 응답을 보내줄 수 있다.
