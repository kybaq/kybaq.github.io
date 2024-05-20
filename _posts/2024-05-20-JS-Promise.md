---
title: JS Promise 에 대해 간단 이해해보기
date: 2024-05-20 21:00:00 +09:00
categories: [Development, JavaScript] # 메인 카테고리 , 보조 카테고리
tags: [Development, Web, JavaScript]
---

## 목적

아무리 해봐도, `Promise` 가 뭔지 잘 모르겠었어서 일단 차근차근 정리해보기 위해 간단한 내용부터 시작해 살을 붙여보려고 한다.

## Promise 란?

    ✨ JS 에서 비동기 작업의 최종 완료 또는 실패를 나타내는 객체.
    비동기 작업이 수행이 될 때, 그 결과를 보장해주는 역할(성공, 실패 무관)

그래서, Promise 는 비동기 작업의 상태를 3가지로 표현한다.

- Pending(대기중) : 요청을 보낸 후, 아직 결과가 정해지지 않은 상태
- Fulfilled(성공) : 응답을 성공적으로 받아 Promise 가 결과를 return.
- Rejected(거부됨) : 오류 또는 실패로 인해 응답을 받지 못한 상태

## 사용법

```javascript
// Promise : JS 에서 비동기 작업의 최종 완료, 실패를 나타내는 객체
// ==> 비동기 작업의 결과를 반드시 보장함(성공, 실패 무관)
const promise = new Promise((resolve, reject) => {
  if (true) resolve("1");
  else reject("0");
});

// 비동기 요청이 성공하면 then() 이 동작, 실패하면 catch() 가 동작함.
// 그때 resolve() 로 넘겨진 값은 then() 으로, reject() 로 넘겨진 값은 catch() 로.

promise
  .then((value) => {
    /* 성공시 실행할 콜백 함수 */
    console.log("then : " + value); // "then : 1
  })
  .catch((err) => {
    /* 실패시 실행할 콜백 함수 */
    console.log("catch : " + err);
  })
  .finally((value) => {
    // 요청 결과와 무관하게 무조건 실행되는 콜백함수
    console.log("finanlly : " + value);
  }); // "finally : undefined"
```

위 예시에서 `myPromise` 함수를 선언할 때, 요청이 성공했을 때와 실패했을 때로 나누어 처리를 할 수 있다.

요청이 성공했을 때는, `resolve()` 가 호출되어 값이 전달 된다. 이후, `then()` 을 통해 값을 전달받아 콜백함수를 실행한다.
반대로 실패했을 땐, `reject()` 가 호출된다. `catch()` 를 통해 값을 전달 받고 콜백함수를 실행한다.
`finally()` 는 promise 의 결과와 관계 없이 무조건 실행이 되는 코드다.

위 3가지는 promise handler 라고 부른다.
프로미스 핸들러 또한, 프로미스 객체를 반환하기 때문에 handler chaining 이 가능해진다. 이 때문에, 프로미스 지옥에 빠지게 되기도 한다..

### `fetch()` 에 대해

Promise 에 대해 찾아보게 된다면 `fetch()` 라는 메서드는 이미 접했을 것이다. `fetch()` 는 스스로 프로미스 객체를 생성해 이를 반환해주는 역할을 한다.

```javascript
fetch('https://jsonplaceholder.typicode.com/posts/1')
  .then((response) => response.json()) // 응답 객체에서 JSON 데이터를 추출한다.
  .then((data) => console.log(data)); // JSON 데이터를 콘솔에 출력한다.
  .catch((err) => console.log(err)); // 실패시 에러 처리
```

따라서, `fetch()` 또한 promise 와 동일하게 사용하면 된다.

### Promise Hell

> Promise 에서 사용할 수 있는 다양한 정적 메서드가 있지만, 여기서는 넘어가겠다.

```javascript
fetch("https://api.github.com/users")
  .then((response) => {
    if (response.ok) {
      return response.json();
    } else {
      throw new Error("Network Error");
    }
  })
  .then((users) => {
    return users.map((user) => user.login);
  })
  .then((logins) => {
    return logins.join(", ");
  })
  .then((result) => {
    console.log(result);
  })
  .catch((error) => {
    console.error(error);
  });
```

위는 [참조링크, 인파님 블로그][1] 에서 가져온 예시 코드다.  
위 코드는 API 호출을 진행해, 가져온 정보를 `.then()` 메서드로 여러번 연결해 문자열로 표현하는 동작을 수행한다.

이렇게 메서드가 연속되다보면, 결국 새로운 지옥이 열리게 된다..

이를 개선하려면 아래처럼 `async` / `await` 를 사용하면 된다.

```javascript
try {
  const response = await fetch("");

  if (response.ok) {
    const users = await response.json();
    const logins = users.map((user) => user.login);
    const result = logins.join(", ");
    console.log(result);
  } else {
    throw new Error("Network Error");
  }
} catch (error) {
  console.error(error);
}
```

`async` / `await` 에 대해서는 다른 포스팅으로 자세히 다뤄보자.

[1]: https://inpa.tistory.com/entry/JS-%F0%9F%93%9A-%EB%B9%84%EB%8F%99%EA%B8%B0%EC%B2%98%EB%A6%AC-Promise
