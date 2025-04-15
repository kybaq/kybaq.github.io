---
title: CSS 선택자 우선순위에 대해
date: 2024-07-31 21:00:00 +09:00
categories: [Development, Project] # 메인 카테고리 , 보조 카테고리
tags: [Development, React, 회고]
---

지금 하는 프로젝트에서 fullcalender 를 이용하고 있다.

엄청난 호환성을 자랑하며 기능도 다양한데, 기본 디자인을 바꾸려면 별도의 css 파일을 작성해 css 속성 오버라이딩이 필요했다.

`<div class="class1 class2 class3"> TEST </div>` 이렇게 다양한 클래스 이름을 가지고 있었지만, `class1` 만 선택해서 바꾸면 되겠지 싶었다.

```css
class1 {
  background-color: #fff;
}
```

이렇게 설정했더니, 전혀 적용이 되지 않는 것이다.

이와 관련해서 조언을 받으니, `!important` 라는 값을 넣어주면 우선 순위를 높여 강제로 적용시킬 수 있다는 것이었다.

그제서야 'css 선택자에 우선 순위가 있었던 것 같은데...' 하는 생각이 들어 찾아보았다.

점수 표 정리하고 내용 보완하기
