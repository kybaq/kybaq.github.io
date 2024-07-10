---
title: Next.js 프로젝트에서 Supabase 연동하기
date: 2024-07-10 21:00:00 +09:00
categories: [Development, Next.js] # 메인 카테고리 , 보조 카테고리
tags: [Development, Next.js, Supabase]
---

> Next js 프로젝트에서 Supabase 를 이용할 일이 생겼다. 그런데, 코드 작성할 때 조금 애를 먹기도 해서 어떤 요소를 고려해야하는지 코드와 함께 정리해보려고 한다!

## 상황

app router 방식으로 구성한 NextJS 프로젝트에서, supabase 를 사용할 필요가 있었다.
별도의 DB 를 구성할 수는 없기에, BaaS 를 이용하는게 적절해보였다.
동시에, Next JS 로는 별도의 route 구성을 통해 api 요청을 처리할 수도 있어 이 둘을 함께 사용해보기로 했다.

## Server vs Client

해당 프로젝트에서 Supbase 를 사용하기 위해 사전에 설정할 요소들이 있다.

1. BrowserClient
2. ServerClient
3. Middleware

여기서 말하는 Client 는, Supbase Client 다.
NextJS 프로젝트 내의 컴포넌트가 서버 컴포넌트인지, 클라이언트 컴포넌트인지에 따라 사용하는 클라이언트가 달라진다. 사실 이 절차도 모두 공식문서에서 다 제공하고 있기 때문에 언제나 공식문서를 잘 참고해보자.

✨ 미들웨어는 서버 컴포넌트에서 API 요청을 보낼 때 쿠키를 다루기 위해 있는 것 같다.
정확히 어떤 역할인지는 추후에 다시 정리해보자.

## Route Handler 구성

이 부분은 필요 없지만, 개인적으로 정리해두고 싶었다.

supabase 에 요청을 보내 결과를 받아오는 로직을 분리하고 싶었다.
물론, 컴포넌트 내에서 바로 처리하는 것도 가능하지만 이미 해본 것이니!

`src/app/api/home` 경로 내에 `route.ts` 파일을 생성했다.

```typescript
// src/app/api/home/route.ts
import { createClient } from "@/supabase/server";
import { NextResponse } from "next/server";

async function getPosts() {
  // Server component 에서 supabase client 를 이용할 경우, 매 요청마다 새로운 client 를 생성함.
  // 공식문서에서는 cookie 와 관련한 것 때문에 그렇다고 함.
  const supabase = createClient();
  try {
    const { data: post } = await supabase.from("post").select("*");

    // 서버 컴포넌트에서 데이터를 받아오는 경우, NextResponse.json() 을 이용하여 데이터를 반환함.
    return NextResponse.json(post);
  } catch (error) {
    throw new Error("데이터 조회에 실패했습니다.");
  }
}

export async function GET() {
  const post = await getPosts();

  return post;
}
```

위 코드는 supabase 에서 직접 데이터를 가져오는 역할을 한다.

```typescript
const fetchPost = async () => {
  try {
    // 얘는 HTTP 요청에 대한 정보를 담고 있음, status code, header, body 등
    const response = await fetch("http://localhost:3000/api/home");

    if (response.ok) {
      // response.json()은 body를 JSON으로 파싱하는 메소드
      const posts: Promise<Post[]> = await response.json();

      return posts;
    }
  } catch (error) {
    throw new Error("데이터 조회에 실패했습니다.");
  }
};
```

위 코드는 `route.ts` 파일이 있는 경로에 `GET` 요청을 보내는 코드다.

supabase 같은 BaaS 를 이용하면 이 과정이 사실 필요 없지 않나 생각이 든다.
별도로 GET, POST 등의 요청에 대한 API 를 새로 구성할 필요가 없고 제공하는 기능을 쓰면 되니까!
하지만, 어디까지나 공부한 것을 적용해보는 것이 더 중요하다고 생각해서 이렇게 구성해봤다!
