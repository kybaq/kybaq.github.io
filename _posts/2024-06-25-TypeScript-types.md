---
title: TypeScript 의 기본 타입
date: 2024-06-25 21:00:00 +09:00
categories: [Development, TypeScript] # 메인 카테고리 , 보조 카테고리
tags: [Development, TypeScript]
---

> TypeScript 내의 기본 타입을 간단히 정리해보려고 한다!

## ⭕ ❌ Boolean

- 2 가지의 상태를 표현하고 싶을 경우 사용해주면 되겠다.
- 3 가지 이상을 표현하고 싶다면 enum 이나 string 을 쓰자!

```typescript
function isValidPassword(password: string): boolean {
  return password.length >= 8;
}

const password = "q1w2e3r4!";
const valid = isValidPassword(password);

if (valid) {
  console.log("유효한 패스워드입니다!");
} else {
  console.log("유효하지 않은 패스워드입니다!");
}
```

위 처럼, 함수의 타입을 boolean 으로 설정한 예시다.

## 🔢 Number

- 자바스크립트와 동일하게 하나의 타입으로 정수, 실수를 모두 포함한다.

```typescript
function calculateArea(radius: number): number {
  return Math.PI * radius * radius;
}

const radius = 5;
const area = calculateArea(radius);
console.log(`반지름이 ${radius}인 원의 넓이: ${area}`);
```

## 🆗 String

- 텍스트 데이터를 나타내는 역할.
- 작은 따옴표(''), 큰 따옴표(""), 백쿼트 or 백틱(``) 로 표현 가능하다.

```typescript
function greet(name: string): string {
  return `안녕, ${name}!`;
}

const name = "Spartan";
const greeting = greet(name);
console.log(greeting);
```

## 📗 Array

- 배열을 표현하는 법은 기본 타입에 [] 를 붙여주면 된다.
- 한 가지 타입만 가질 수 있다.

```typescript
function calculateSum(numbers: number[]): number {
  let sum: number = 0;
  for (let i = 0; i < numbers.length; i++) {
    sum += numbers[i];
  }
  return sum;
}

const testScores: number[] = [90, 85, 78, 92, 88];
const sumScore = calculateSum(testScores);
console.log(`점수의 총합: ${sumScore}`);
```

## 📚 Tuple

- 배열과는 달리, 서로 다른 타입을 가질 수 있다.

```typescript
const person: [string, number, boolean] = ["Spartan", 25, false];
const person2: [string, number, boolean] = [25, "Spartan", false]; // 오류!
```

하지만, 배열처럼 사용하는 것은 권장하지 않는다.
`push` 메서드를 사용해서 임의로 데이터를 계속해서 추가할 수 있지만, 튜플 내부 구조의 변경이 생기니 권장하지 않는다고 한다.

## 🚂 enum

- 열거형 데이터 타입으로, 자바스크립트에는 존재하지 않았다.
- 하지만, 형태를 보면 객체 리터럴과 아주 흡사하다!
- 관련있는 상수를 모아두고 문자열로 쉽게 접근할 수 있게 해준다.
- enum 내부의 요소에 값을 부여하지 않으면 기본적으로 첫 번째 요소는 0 부터 시작하게 된다.

```typescript
enum UserRole {
  ADMIN = "ADMIN",
  EDITOR = "EDITOR",
  USER = "USER"
}

enum UserLevel {
  NOT_OPERATOR, // 0
  OPERATOR // 1
}

function checkPermission(userRole: UserRole, userLevel: UserLevel): void {
  if (userLevel === UserLevel.NOT_OPERATOR) {
    console.log("당신은 일반 사용자 레벨이에요");
  } else {
    console.log("당신은 운영자 레벨이군요");
  }

  if (userRole === UserRole.ADMIN) {
    console.log("당신은 어드민이군요");
  } else if (userRole === UserRole.EDITOR) {
    console.log("당신은 에디터에요");
  } else {
    console.log("당신은 사용자군요");
  }
}

const userRole: UserRole = UserRole.EDITOR;
const userLevel: UserLevel = UserLevel.NOT_OPERATOR;
checkPermission(userRole, userLevel);
```

enum 을 활용할 때는, 객체 내의 모든 프로퍼티 값이 상수일 때 사용을 고려하면 될 것이다.

## 변수 선언 키워드

### let

```typescript
let num: number = 5;
console.log(num); // 출력: 5

num = 10;
console.log(num); // 출력: 10
```

### const

```typescript
const nums: number[] = [];
console.log(nums); // 출력: []
nums.push(1); // 할당은 되지 않아도 배열에 데이터를 추가/삭제하는 것은 가능
nums.push(2); // 은근히 헷갈릴 수 있지만 = 연산자 기준으로만 생각하면 매우 쉽다!
console.log(nums); // 출력: [1, 2]

nums = []; // 에러: 'nums'는 const로 선언되었으므로 다시 할당될 수 없음!
```

### readonly

- TypeScript 에서 등장한 키워드로, readonly 는 TypeScript 에서 등장한 키워드다.
- readonly 는 TypeScript 에서 객체의 속성을 불변으로 만드는 데 사용한다.
- 클래스의 속성이나 인터페이스의 속성을 변경할 수 없도록 만든다.
- 일반 변수를 정의할 때 `const` 를 사용하는 것과 비슷한 목적이라고 생각하면 된다!

```typescript
class Person {
  readonly name: string;
  readonly age: number;

  // 생성자로 접근할 때만 값 설정이 가능
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
}

const person = new Person("Spartan", 30);

console.log(person.name); // 출력: 'Spartan'
console.log(person.age); // 출력: 30

person.name = "Jane"; // 에러: 'name'은 readonly 속성이므로 재할당할 수 없다.
person.age = 25; // 에러: 'age'은 readonly 속성이므로 재할당할 수 없다.
```

## any

- 모든 타입의 super type 이다.
- 어떤 타잎의 값이든 저장 가능하다는 의미로, `object` 처럼 최 상위 타입이라고 생각하면 된다.
- any 를 남발할 거라면 vanila javascript 쓰는 것이 낫지 않을까..?
- TypeScript 는 JavaScript 를 정적으로 사용해 안정성을 확보하려는 목적이었으니 any 를 남발하면 사실 존재 의의가 없어진다..

```typescript
let anything: any;
anything = 5; // 최초에는 숫자를 할당
anything = "Hello"; // 문자열 할당에도 문제 없다.
anything = { id: 1, name: "John" }; // 객체도 가능.
```

## unknown

- any 와 비슷하게 모든 타입의 값을 할당할 수 있다.
- 하지만 차이점이 있다면, unknown type 의 값을 다른 변수에 할당하려면 명시적으로 타입을 확인해야만 한다.

```typescript
let unknownValue: unknown = "나는 문자열이지롱!";
console.log(unknownValue); // 나는 문자열이지롱!

let stringValue: string;
stringValue = unknownValue; // 에러 발생! unknownValue가 string임이 보장이 안되기 때문!
stringValue = unknownValue as string;
console.log(stringValue); // 나는 문자열이지롱!
```

`stringValue = unknownValue as string;` 부분은 Type Assertion(타입 단언)이라고 함.
이렇게 `string` 으로 타입을 지정해준 뒤, string type 인 변수 `stringValue`에 할당해준 상황이다.

물론 `typeof` 연산자를 사용한 것도 가능하다

```tsx
let unknownValue: unknown = "나는 문자열이지롱!";
let stringValue: string;

if (typeof unknownValue === "string") {
  stringValue = unknownValue;
  console.log("unknownValue는 문자열이네요~");
} else {
  console.log("unknownValue는 문자열이 아니었습니다~");
}
```

## union

- unknown 을 사용해 타입을 체크하고 지정해서 할당하는 것 보다 union 으로 설정해주면 된다.
- 특정 변수가 문자여도 상관없고, 숫자여도 상관이 없는 상황이라면 사용할수 있다.

```tsx
type StringOrNumber = string | number; // 원한다면 | boolean 이런식으로 타입 추가가 가능해요!

function processValue(value: StringOrNumber) {
  if (typeof value === "string") {
    // value는 여기서 string 타입으로 간주됩니다.
    console.log("String value:", value);
  } else if (typeof value === "number") {
    // value는 여기서 number 타입으로 간주되구요!
    console.log("Number value:", value);
  }
}

processValue("Hello");
processValue(42);
```

- 하지만, TypeScript 는 이런 유연성을 최대한 줄이고 안정성을 높이기 위해 고안된 것이니 만큼 남발하면 안되겠다!
