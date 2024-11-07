---
title: JavaScript 에는 왜 타입이 필요할까?
date: 2024-11-07 21:00:00 +09:00
categories: [Development, Project] # 메인 카테고리 , 보조 카테고리
tags: [Development, React, 회고]
---

![타입스크립트 교과서 사진, 출처: https://github.com/zerocho/ts-book](../assets/img/posts/2024-10-04-ts-book-1.png)

타입스크립트 교과서 교재를 통해 제대로 익히고 넘어가기로 마음을 먹었다.

간단히 시작해보자.

### TypeScript 란?

```plain
TypeScript is JavaScript with syntax for types
```

어디까지나 타입스크립트는 **자바스크립트**다. 자바스크립트에 타입을 위한 구문이 추가되어있는 것, 이게 전부다!

간단히 예시로 살펴보자.

```ts
const hello: string = 'world';

function add(x: number, y: number): number {
    return x + y;

}

interface Person {
    name: string,
    age: number,
}

const person: Person = {
    name: 'kyb',
    age: 28,
};
```

위는 타입스크립트, 아래는 자바스크립트다.

```js
const hello = 'world';

function add(x, y) {
    return x + y;

}

const person = {
    name: 'kyb',
    age: 28,
};
```

### 타입이 왜 필요할지 생각해보았다

이렇게 타입을 사용하는 이유는, 결국 "개발자의 편의를 위해서" 라고 생각한다.

게다가 자바스크립트는 동작이 멈추는 것을 최소화 하기 위해, 코드가 동작하지 않더라도 오류를 띄우기 보단 그냥 아무런 동작이 없을 때가 많다.

이에 대해서 개인적으로는 웹 사이트를 이용하는데, 어떤 버튼을 누를 때 개발자가 의도하지 않은 오류 메시지가 마구마구 나타면서 사이트가 접속이 안된다면 사용자 경험을 해치기 때문이라고 생각한다.

아무튼, 이러한 특성이 개발자가 디버깅하기 어렵게 만드는 원인 중 하나임은 분명하다.
타입을 도입해 코드의 동작이 보다 직관적으로 동작할 수 있게 만드는 것이다.

또한, TypeScript 를 이용하면 오타도 잡아주기 때문에 무척 편리하다.

물론, 그렇다고 모든 변수 / 객체에 타입을 지정해줘야할까? **절 대 아 니 다**.

100줄만 작성한다고 해도, 그 모든 것에 타입을 작성하려고 하면 정말 귀찮을 것이다.
어쩔 때는 타입을 명시하지 않아도 충분히 파악이 되는 경우도 있을 것이다.

아래에서 예시를 통해 살펴보자.

### 그럼 어떻게 쓰라는거야?

```ts
const string1: string = "Hello";
```

가장 쉬운 예시를 들어서 정확히 어쩌라는 건지 살펴보자.
위 예시를 보면, 이렇게 식별자에 변수를 할당하는 경우엔 이 변수의 타입을 직접 설정하지 않아도 너무 명확하다!

그러니, 이럴 땐 그냥 자바스크립트 처럼 쓰는 편이 낫다.

```ts
const string1 = "Hello";
```

이렇게 작성하면, TypeScript 가 알아서 **타입 추론**을 해주기 때문이다.
심지어 더 정확하다. 타입을 "Hello" 라고 추론해준다.

타입 추론에 대해서는 이후에 조금 더 다루어보자.

### 마무리

앞으로는, TypeScript 가 어떻게 동작하는지 천천히 파악해나가면서 코드를 작성할 때 타입을 활용하는 방법을 만들어보자.
