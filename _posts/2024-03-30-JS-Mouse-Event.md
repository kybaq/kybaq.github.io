---
title: JS Mouse Event (mouseenter, mouseleave, mouseover, mouseout)
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

1. mouseenter, mouseleave  
 "mouseenter", "mouseleave" 이벤트에 이벤트 리스너를 추가한 요소의 hotspot에 처음으로 마우스를 올렸을 때, 발생하는 이벤트.

2. mouseover, mouseout
 1번의 두 이벤트와 유사하게, "mouseover", "mouseout" 이벤트에 리스너를 추가한 요소에 마우스를 올렸을 때 발생하는 이벤트. 그러나, 2번의 차이는 이벤트 버블링이 적용된다는 것이다. 

예시를 통해 확인해보자.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        #enter-and-leave {
            width: 300px;
            height: 100px;
            border: 1px solid red;
        }

        #over-and-out {
            width: 300px;
            height: 100px;
            margin-top: 10px;
            border: 1px solid green
        }

        #enter-and-leave div {
            width: 150px;
            height: 50px;
            margin: 25px 70px;
            border: 1px solid orange;
        }

        #over-and-out div {
            width: 150px;
            height: 50px;
            margin: 25px 70px;
            border: 1px solid violet;
        }

    </style>
</head>
<body>
    <div id="enter-and-leave">
        <div></div>
    </div>
    <div id="over-and-out">
        <div></div>
    </div>

    <script>
        const enterDiv = document.querySelector("#enter-and-leave");
        const overDiv = document.querySelector("#over-and-out");
        const enterInnerDiv = document.querySelector("#enter-and-leave div");
        const overInnerDiv = document.querySelector("#over-and-out div");

        let enterCount = 0;
        let leaveCount = 0;
        let overCount = 0;
        let outCount = 0;
        
        function handleMouseEnter(params) {
            enterCount += 1;
            enterInnerDiv.innerText = `Mouse enter ${enterCount}`;
        }

        function handleMouseLeave(params) {
            leaveCount += 1;
            enterInnerDiv.innerText = `Mouse leave ${leaveCount}`;
        }

        function handleMouseOver(params) {
            overCount += 1;
            overInnerDiv.innerText = `Mouse over ${overCount}`;
        }

        function handleMouseOut(params) {
            outCount += 1;
            overInnerDiv.innerText = `Mouse out ${outCount}`;
        }

        enterDiv.addEventListener("mouseenter", handleMouseEnter);
        enterDiv.addEventListener("mouseleave", handleMouseLeave);
        overDiv.addEventListener("mouseover", handleMouseOver);
        overDiv.addEventListener("mouseout", handleMouseOut);
    </script>
</body>
</html>
```

아래 사진은 내부에 있는 div 박스에 마우스를 올리고 내리는 행동을 1번만 한 뒤 결과다.

<img src="https://kybaq.github.io/assets/img/posts/2024-03-30-JS-Mouse-Event-1.png" alt="1번과 2번 이벤트의 결과 비교">

같은 동작을 수행했는데, 1번의 경우는 각각 카운트가 1씩만 증가했다.  
반면에 2번의 경우는 카운트가 3이 증가했다.

조금 더 자세히 동작을 살펴보면, 아래와 같다.

<img src="https://kybaq.github.io/assets/img/posts/2024-03-30-JS-Mouse-Event-2.png" alt="2번의 동작 원리">

두 코드 모두, 바깥 쪽의 div 에 eventListener 를 추가했는데 2번의 경우에는 내부에 있는 div 에 마우스를 올렸을 때에도 카운트가 증가함을 확인했다.

이는 이전에 정리한 이벤트 버블링에 의해 일어나는 현상이다.