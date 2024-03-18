---
title: JS const 에 대한 고찰
date: 2024-03-12 14:33:00 +09:00
categories: [Development, JavaScript] # 메인 카테고리 , 보조 카테고리
tags:
  [
    Development,
    JavaScript,
  ]
---

https://inpa.tistory.com/entry/%F0%9F%93%9A-null-undefined-NaN?source=post_page-----118025e417f1--------------------------------

위 블로그를 참고 했다. 휴지 걸이 예시가 나름 재밌더라 이게 개발자 유머?

시작은 typeof null 을 해보다가 object 라고 하길래 3가지를 다 비교해봤다.
메모리에서 어떻게 존재하는 건지 궁금해서 찾아보았다.

  ```Javascript
    console.log(typeof null) // --> object
    console.log(typeof undefined) // --> undefined
    console.log(typeof NaN) // --> number
  ```

휴지걸이 예시를 통한 유머가 있는데, 일단 null 은 object type으로 나오는 것을 보니 실제 참조하는 주소는 없지만 stack 에 reference 값으로서 정의가 되는 것처럼 보인다. → 휴지 걸이만 있는 예시

undefined type 은 당최 무엇인가?? → 휴지 걸이조차 없는 예시

메모리 공간 내에서 차이점이 있다고 한다!

null 은 직접 우리가 정의를 해주는 경우인데, null 로 정의된 상태에서는 주소값을 가지고 있지는 않지만 추후 값을 할당 받을 수 있으니 undefined 보다는 조금 많은 메모리 공간을 차지한다고 한다. → let 으로 정의했을 때만 그러는지 아닌지는 모르겠다.
그와 달리 undefined 는 아예 값을 받지 않기 때문에 적은 공간을 차지한다고 한다. → 확인할 방법이 있는지 모르겠다. 런타임 시에 할당된다고 해서.

NaN 는 number type 으로 나타나며 일종의 오류값이다.
숫자를 다룰 때 오류를 나타내기위해 만든 값이 아닐까?