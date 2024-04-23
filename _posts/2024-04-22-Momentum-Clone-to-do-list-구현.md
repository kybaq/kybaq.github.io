---
title: To do list 기능 구현
date: 2024-04-22 21:00:00 +09:00
categories: [Development, JavaScript] # 메인 카테고리 , 보조 카테고리
tags: [Development, Web, JavaScript]
---

## 목적

Momentum 이라는 크롬 확장 앱을 clone coding 하는 중인데, 여기서 to do list 를 작성해보고 싶었다.

내가 구현하는 데 생각보다 시간이 오래걸렸다;;

그래서 조금 정리해보고자 작성한다.

## 과정

todos 라는 배열을 localstorage에 저장해서 할 일 목록을 만들었다.

```javascript
let todos = [];

function setToDo(event) {
  event.preventDefault();

  todos.push(toDoInput.value);

  console.log(todos);

  window.localStorage.setItem("todos", JSON.stringify(todos));

  console.log(JSON.stringify(todos));

  printToDo();
}
```

위 코드는 submit event 의 기본 동작(새로고침)을 멈춘 다음, 유저가 입력한 값을 todos 배열에 저장하고 localstorage 에 저장하는 역할을 한다.

그러나, 그 뒤 이를 삭제하기 위해서 작성한 코드와 관련해 문제가 발생했다.

```javascript
function deleteToDo(event) {
  // 버튼 click이 일어나면 remove
  const li = event.target.parentElement;
  const liContent = li.firstChild.data;

  li.remove();

  // todos 에서 liContent 를 제외해서 다시 등록하면 될 것이다!
  todos = todos.filter((todo) => todo !== liContent);

  setToDo(); // 함수를 직접 호출
}
```

위 코드를 통해 삭제 기능을 구현했는데, 삭제 요청이 일어난 버튼의 부모 요소인 `li` 내용이 todos 에 들어있으면 그 내용을 제외하고 다시 localstorage 에 저장하는 흐름을 생각했다. 따라서, filter 를 통해 이를 구현했다.

그런데, 이 경우에서는 같은 내용을 작성했을 경우 모두 삭제가 되어버리는 경우가 생겨서 새로운 구별자를 추가할 필요가 있었다.

또한, `setToDo()` 를 `deleteToDo()` 에서 직접 호출하는 상황에서는 **event 발생**이 일어나지 않아서 event handler 를 새로 추가해서 구조를 변경했다.

정리해보자면,

1. 동일한 text 를 가진 경우, 일괄 삭제가 일어나서 id 값을 적용해야한다.
2. setToDo 를 deleteToDo 에서 호출하는 경우, event 발생하는 게 아니라 event is undefined 오류가 발생함.

변경한 코드는 다음과 같다.

```javascript
function handleSubmit(event) {
  event.preventDefault();

  const todoValue = toDoInput.value;

  toDoInput.value = "";

  const newToDoObj = {
    id: Date.now(),
    todo: todoValue
  };

  todos.push(newToDoObj);
  printToDo(newToDoObj);
  setToDo();
}

function setToDo() {
  window.localStorage.setItem("todos", JSON.stringify(todos));
}
```

다음으로, localstorage 에 저장한 할 일 목록을 화면에 출력해주는 `printToDo`함수를 작성했다.

```javascript
function printToDo() {
  let storedtodos = window.localStorage.getItem("todos"); // 여기서 문자열이 됨.

  let splittodos = storedtodos.split(",");
  // <ul> 내부에 <li> 을 추가해서 보여주는 형식.
  Array.from(splittodos).forEach((element) => {
    let item = document.createElement("li");
    item.setAttribute("class", "to-do__item");

    let btn = document.createElement("button");
    btn.setAttribute("class", "to-do__remove");

    let itemContent = document.createTextNode(element);
    let btnContent = document.createTextNode("✖️");

    btn.appendChild(btnContent);
    item.appendChild(itemContent);
    item.appendChild(btn);

    toDoList.appendChild(item);
  });
}
```

처음에는 위 함수에서, 배열을 다시 불러와 `forEach`로 모두 처리하는 방식을 선택했다.
이렇게 했더니, 한 번 추가를 하면 매번 목록에 있는 모든 것을 모두 표시해서 중복이 발생했다. forEach 는 목록이 존재할 때만 딱 한 번 필요하다는 것을 알았다.

게다가, `btn` 에 `"click"` event listener 를 추가하는 API 가 빠져있었다.
그런데 기능은 또 잘 작동하길래 뭔가 싶어서 찾아보니,

```javascript
const toDoBtn = document.querySelectorAll(".to-do__remove"); // Node"List" 반환.

toDoBtn.forEach((element) => {
  element.addEventListener("click", deleteToDo);
});
```

맨 아래에 이렇게 따로 빼 놓은 것을 발견해, `printToDo` 함수에 넣어준뒤 마지막에 forEach 를 적용하는 방식으로 변경했다.

추가로, todos 배열이 단순 할 일 목록을 받는 것에서 id 를 추가한 객체를 갖는 배열로 바뀌면서 item 에 id attribute 를 설정하고, itemContent 변수 또한 변경이 생겼다.

```javascript
function printToDo(todos) {
  // <ul> 내부에 <li> 을 추가해서 보여주는 형식.
  // list item 생성
  let item = document.createElement("li");
  // css id 를 부여
  item.setAttribute("id", todos["id"]);
  // css class 부여
  item.setAttribute("class", "to-do__item");

  let btn = document.createElement("button");
  btn.setAttribute("class", "to-do__remove");
  btn.addEventListener("click", deleteToDo);

  let itemContent = document.createTextNode(todos["todo"]);
  let btnContent = document.createTextNode("✖️");

  btn.appendChild(btnContent);
  item.appendChild(itemContent);
  item.appendChild(btn);

  toDoList.appendChild(item);
}
```

내용을 삭제하는 `deleteToDo` 함수는 다음과 같이 변경했다.

```javascript
function deleteToDo(event) {
  // 버튼 click이 일어나면 remove
  const li = event.target.parentElement;

  li.remove();

  // todos 에서 li 가 같은 경우 를 제외해서 다시 등록하면 될 것이다!
  todos = todos.filter((todo) => todo["id"] !== parseInt(li.id));

  setToDo();
}
```

새로운 todos 배열을 반환받을 때 filtering 조건이 변경되어,
기존에는 `li.firstChild.data` 를 이용해서 내용이 같은 것들이 모두 지워져버리는 문제가 있었지만 id 를 기반으로 확인하도록 변경해서 해결했다.

거의 1주일 걸린 것 같다.

내일 weather API 랑 명언집 출력만 구현하면 이것도 끝!
