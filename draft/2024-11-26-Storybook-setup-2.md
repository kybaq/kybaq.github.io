---
title: Storybook 튜토리얼 - 2
date: 2024-11-26 21:00:00 +09:00
categories: [Development, TIL] # 메인 카테고리 , 보조 카테고리
tags: [Development, Storybook]
---

앞선 게시글에서, 프로젝트에 대한 스토리북을 어떻게 구성하는지 간단히 살펴보았다.
조금 더 자세히 이해하기 위해서, 튜토리얼을 따라 컴포넌트를 하나 만들어보자.

## 튜토리얼 따라 예시 컴포넌트 만들기

### Task 컴포넌트
`Task` 라는 컴포넌트를 만들어보도록 하자.

![Task 컴포넌트 예시, 출처: https://storybook.js.org/tutorials/intro-to-storybook/react/ko/simple-component/](../assets/img/posts/2024-11-26-Storybook-setup-2-1.png)

이 컴포넌트는 체크박스를 생각하면 되고, 즐겨찾기(또는 북마크)를 할 수 있는 것 처럼 보인다.
그리고, 이에 따라 3개의 상태가 있다.


1. 기본(DEFAULT): 아무것도 선택하지 않은 기본 상태
2. 고정됨(PINNED): 즐겨찾기를 한 상태
3. 보관됨(ARCHIVED): 체크박스를 클릭해 완료한 상태

스토리북 앱에서, 이 3가지 상태를 모두 확인하고 싶다면 컴포넌트의 props 를 활용해야한다.

여기에서는 2개의 prop 을 정의해보자.

- title: task 를 설명해주는 문자열
- state: 현재 어떤 task 가 목록에 있으며, 선택이 되었는지 여부

음.. 이게 무슨 말일까?

다시 이해해 보자면, title 은 task 의 내용이 되는 것 같고 state 는 앞서 말한 3가지 상태를 말하는 것 같다.

### Task 컴포넌트의 스토리 파일 만들기

튜토리얼을 따라 직접 만들어 보면서 이해해보자.

일단, `src/components/Task.jsx` 와 `src/components/Task.stories.jsx` 을 생성해보자.

```jsx
// src/components/Task.jsx

export default function Task({ task: { id, title, state }, onArchiveTask, onPinTask }) {
  return (
    <div className="list-item">
      <label htmlFor={`title-${id}`} aria-label={title}>
        <input type="text" value={title} readOnly={true} name="title" id={`title-${id}`} />
      </label>
    </div>
  );
}
```

여기서 눈 여겨볼 것은, `Task` 컴포넌트에 정의한 prop 들이다.

`task` prop 은 id, title, state 총 3개를 갖는다. 사실 이것보다는 나머지 2개가 더 중요하다.

`onArchiveTask`, `onPinTask` 라는 함수가 추가로 있다.

음.. 설명을 제대로 해주지 않고, 뭔가 갑자기 새로운 것들이 나오기 시작했다.
일단 계속 진행해보자.

```jsx
// src/components/Task.stories.jsx

import { fn } from "@storybook/test";

import Task from './Task';

export const ActionsData = {
  onArchiveTask: fn(),
  onPinTask: fn(),
};

export default {
  component: Task,
  title: 'Task',
  tags: ['autodocs'],
  //👇 "Data"로 끝나는 export들은 스토리가 아닙니다.
  excludeStories: /.*Data$/,
  args: {
    ...ActionsData,
  },
};

export const Default = {
  args: {
    task: {
      id: '1',
      title: 'Test Task',
      state: 'TASK_INBOX',
    },
  },
};

export const Pinned = {
  args: {
    task: {
      ...Default.args.task,
      state: 'TASK_PINNED',
    },
  },
};

export const Archived = {
  args: {
    task: {
      ...Default.args.task,
      state: 'TASK_ARCHIVED',
    },
  },
};

```

코드가 조금 길어지니, 보기가 어렵다..
부분부분 나눠서 살펴보자.

여기서 먼저 눈여겨볼 것은 아래 `ActionsData` 다.

```jsx
import { fn } from "@storybook/test";

export const ActionsData = {
  onArchiveTask: fn(),
  onPinTask: fn(),
};
```

여기에 정의한 Actions 는 상호작용으로 생각하면 된다.
이 컴포넌트가 어떤 상호작용을 할 수 있는지 스토리북 앱으로 볼 수 있게 해주는 것이다.

`Task` 컴포넌트를 예로 든다면, `PINNED` 상태와 `ARCHIVED` 상태로 변하는 함수를 정의해주면 되는 것이다.

`fn()` 은 더미 함수로 `fn()` 을 사용하면 일단 자체적으로 콜백함수를 만들어줘, 스토리북 앱에서 빠르게 테스트할 수 있다.
나중엔 실제 이벤트 핸들러로 대체해주면 될 것 같다.

다음으로 볼 부분은 **`export default`** 를 사용한 부분이다.

```jsx
export default {
  component: Task,
  title: 'Task',
  tags: ['autodocs'],
  //👇 "Data"로 끝나는 export들은 스토리가 아닙니다.
  excludeStories: /.*Data$/,
  args: {
    ...ActionsData,
  },
};

```

여기에 정의해둔 것들을 통해, 스토리북 앱에서 컴포넌트를 확인할 수 있는 것이다.
차례차례 어떤 것들인지 살펴보자.

1. `component`: 우리가 만든 UI 컴포넌트를 import 해서 넣어주면 된다. 튜토리얼에서는 `Task.jsx` 를 import 하면 된다.
2. `title`: 스토리북 앱에서 표기할 컴포넌트의 이름이다. 특별한 게 없으면, 다른 이름을 쓸 필요는 없어보인다.
3. `tag`: 컴포넌트에 대한 문서를 자동으로 생성하기 위한 태그라고 하는데, 일단 기본으로는 `[autodocs]` 라고 하면 될 것 같다.
4. `excludeStories`: 스토리를 만들때는 `export` 키워드를 사용하는데, 여기에 포함된 단어들을 사용하면 스토리북 앱에서 제외하게 만들 수 있다.
예를 들면, `export const DefaultData` 라고 추가하면 스토리북 앱에서 나타나지 않는다.
5. `args`: 컴포넌트가 사용자 정의 이벤트를 모킹하기 위해 기대하는 액션 args 를 정의 ... 라고 하는데, 그냥 ActionsData 를 넣어주면 되는 것 같다. 컴포넌트에게 기대하는 동작? 정도로 표현할 수 있겠다.


