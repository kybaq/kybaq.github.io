---
title: JS Mouse Event (mouseenter, mouseleave, mouseover, mouseout, mousebutton)
date: 2024-03-30 10:33:00 +09:00
categories: [Development, JavaScript] # 메인 카테고리 , 보조 카테고리
tags:
  [
    Development,
    JavaScript,
  ]
---

JS를 간단하게 다루어보면서 여러 Event를 사용해보았고, 가장 최근에 MouseEvent를 다루었다.  
그런데, 겉보기에는 기능이 동일해보이는 이벤트가 있어서 정리해보려고 한다.

1. mouse enter  
 "mouseenter" 이벤트에 이벤트 리스너를 추가한 요소의 hotspot에 처음으로 마우스를 올렸을 때, 발생하는 이벤트.

2. mouse over  
 1번의 mouseenter 이벤트와 유사하게, "mouseover" 이벤트에 리스너를 추가한 요소에 마우스를 올렸을 때 발생하는 이벤트. 그러나, mouse over 의 차이는 자식 요소에도 적용되는 것이 있다.

3. 