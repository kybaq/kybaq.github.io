---
title: 24/05/21 React useState hook의 batch update
date: 2024-05-21 21:00:00 +09:00
categories: [Development, React] # 메인 카테고리 , 보조 카테고리
tags: [Development, React, JavaScript]
---

## 목적

리액트에서, `setState()` 를 여러번 사용해서 작업을 처리하려고 했더니 한 번만 적용이 되길래 찾아보았다.

## batch update

```javascript
const App = () => {
  const [count, setCount] = useState(0);
  // const handleCountPlus = () => {
  //   setCount((state) => state + 1);
  // };

  return (
    <>
      <div>{count}</div>
      {/* <button onClick={handleCountPlus}>클릭</button> */}
      <button
        onClick={() => {
          setCount(count + 1);
          setCount(count + 1);
          setCount(count + 1);
          setCount((prev) => prev + 1);
          setCount((prev) => prev + 1);
          setCount((prev) => prev + 1);
        }}
      >
        클릭
      </button>
    </>
  );
};
```

위 코드를 작성했는데, 내가 예상한 결과는 한 번에 6씩 증가할 것이라 생각했다.
하지만, 클릭 한 번에 4씩 증가했다.

주석을 하나 씩 처리하면서 확인해보니, 함수 형태로 사용할 때만 `setState` 를 여러 번 호출하는 것이 가능했다.
일반적으로, 리액트가 `setState` 를 처리할 때 효율성을 위해 상태가 변한 것들을 한 번에 갱신을 시키기 때문이란다.
setCount(count + 1)` 처럼 작성했을 경우에는 여러 번 작성하면 동일한 행동을 단 한 번만 적용하도록 바꿔서 생기는 현상이라는 것을 알았다.

그러나, 함수 형태로 작성하면 매 번 업데이트가 가능했다.
또한, 함수 형태로 작성을 하니 `setState` 로 기존의 `state` 에도 접근이 가능한 것을 알았다.

이를 통해, 자식 컴포넌트에 state 를 전달할 때도 응용할 수 있었다.

```javascript
// TodoContainer.jsx

const [toDos, setToDos] = useState(initialState);
// ToDoForm 컴포넌트에서 제어함.

const workingToDos = toDos.filter((toDo) => !toDo.isDone);
const doneToDos = toDos.filter((toDo) => toDo.isDone);

return (
  <>
    <h2>My To Do List</h2>
    <ToDoForm setToDos={setToDos} /> {/* ⭐ 이 부분 */}
    <ToDoList title="Working" toDos={workingToDos} setToDos={setToDos} />
    {/* isDone === true */}
    <ToDoList title="Done!" toDos={doneToDos} setToDos={setToDos} />
    {/* isDone === false */}
  </>
);
```

```javascript
// TodoForm.jsx

const [title, setTitle] = useState("");
const [content, setContent] = useState("");

const onSubmit = (evt) => {
  if (!title || !content) {
    Swal.fire({
      title: "이런..",
      text: "빈 칸을 모두 채워주세요!",
      icon: "error"
    });
  }

  evt.preventDefault();

  setTitle("");
  setContent("");

  const nextToDo = {
    id: uuidv4(),
    title,
    content,
    isDone: false
  };

  setToDos((toDo) => [...toDo, nextToDo]);
  {
    /* ⭐ 이 부분 */
  }
  setTitle("");
  setContent("");
};
```

부모 컴포넌트인 `TodoContainer.jsx` 에서 자식 컴포넌트인 `TodoForm.jsx` 으로 `setState` 만을 props 로 내려보내 상태 변경을 한 예시다.

앞으로 알아가야할 게 많아진다~~~
