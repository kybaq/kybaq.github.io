---
title: box-sizing:border-box; 에 대해
date: 2024-02-13 23:11:00 +09:00
categories: [Development, Web] # 메인 카테고리 , 보조 카테고리
tags:
  [
    Development,
    HTML,
    CSS,
  ]
---

클론코딩 강의를 수강하면서 얻은 magic line 2줄에 대해 정리했다

  ```CSS
    .nav {
      background-color: #f9f9fa;
      padding: 20px 50px;
      position: fixed;
      border-top: 1px solid rgba(128, 128, 128, 0.3);
    }
    
    /*
    width: 100%;
    box-sizing:border-box;
    */
  ```

위와 같은 nav 를 화면에 고정시키고 싶을 때, position: fixed; 만 사용하면 nav 의 요소들이 갑자기 위치가 바뀔 때가 있다.

이때, 주석처리된 아래 두 줄을 사용하면 잘 해결되는데 그 이유가 있다.
바로, padding 을 설정해준 것이 이유다.

nav 에 대해서 width, height 를 설정하지 않는다고 하더라도,
기본 값을 가지고 있을 것이다. 이 기본 값을 200px 로 가정해보자.
이때, padding 을 설정해주게 되면 설정값 만큼 width 는 줄어들게 될 것이다.

여기에서, 자동적으로 padding size 만큼 줄어든 width, height 를 유지하기 위해 기본값에서 다시 크기가 추가가 될것이다.
그러면, 화면 밖을 벗어나버리는 문제가 발생하게 된다!

이것을 방지하기 위해, 줄어든 width 나 height 를 보충하지 않도록 하는게 바로 box-sizing: border-box; 다.