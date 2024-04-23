---
title: JS Geolocation API 사용해 현재 위치 불러오기
date: 2024-04-23 21:00:00 +09:00
categories: [Development, Javascript] # 메인 카테고리 , 보조 카테고리
tags: [Development, Web, Javascript]
---

## 목적

> Momentum clone 진행 중 openweather api 로 날씨 정보를 제공하고 싶었다.
> 이를 위해 geolocation api 를 활용해서 위치 정보를 받아오기로 했다.

1. Geolocation API란?

mdn 문서의 설명을 가져왔다.

> Geolocation API는 사용자의 현재 위치를 가져오는 API로, 지도에 사용자 위치를 표시하는 등 다양한 용도로 사용할 수 있습니다.

2. Geolocation API 살펴보기
   `navigator.geolocation` 객체가 가진 메소드를 활용하면 가능하다!

mdn 예제를 보자.

```Javascript
navigator.geolocation.getCurrentPosition((position) => {
  doSomething(position.coords.latitude, position.coords.longitude);
});
```

위 코드를 콘솔에 입력하면, 브라우저에서 위치 정보 사용 권한 요청이 발생한다.
이때, 허용해주고 위치정보를 받아오면 doSomething 함수가 동작하는 것이다.

간단히 `console.log()` 로 바꿔주면 바로 위도, 경도를 받아볼 수 있다.

3. position 객체

그런데, 위에서 예제를 볼때 `getCurruntPosition()`의 인자로 사용된 것이 있다.
바로 position 객체인데, 그렇다면 position 객체가 담고 있는 내용은 무엇일까??

<img src="https://kybaq.github.io/assets/img/posts/2024-04-23-JS-Geolocation-1.png" alt="position 객체의 내용">

coords 속성이 있고, 위도와 경도 정보 등 다양한 것들이 함께 있는 것을 볼 수 있다.
나는 위도와 경도만 이용할 것이다.

4. 직접 사용

```Javascript
const weatherUrl = `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&units=${units}&appid=${API_KEY}`;

  fetch(weatherUrl)
    .then((res) => res.json())
    .then((data) => {
      const weatherDiv = document.querySelector(".weather");
      const temperature = data["temp"];

      console.log(data);

      weatherDiv.innerText = `${temperature} 입니다.`;
    });
```

처음에는 url 을 `https://api.openweathermap.org/data/3.0/onecall?` 이렇게 했더니, 결제 해야한다고 오류가 뜨더라...

사이트 뒤져서 겨우 무료버전 찾아서 변경했다.

위와 같이 구성했더니, data 객체를 또 얻을 수 있었다.

온도를 얻기 위해서는, main 속성을 찾아서, temp 변수를 찾아가야했다.

```Javascript
function success(position) {
  // 날씨 표현
  const lat = position.coords.latitude;
  const lon = position.coords.longitude;
  console.log(`You live in ${lat} ${lon}`);
  const units = "metric";
  const weatherUrl = `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&units=${units}&appid=${API_KEY}`;

  fetch(weatherUrl)
    .then((res) => res.json())
    .then((data) => {
      const weatherDiv = document.querySelector(".weather");
      const temperature = data.main["temp"];

      weatherDiv.innerText = `현재 온도는 ${temperature} ℃ 입니다.`;
    });
}

function fail(params) {
  weatherDiv.innerText =
    "위치 정보 사용이 불가능해 날씨 정보를 제공할 수 없습니다.";
}

navigator.geolocation.getCurrentPosition(success, fail);
```

최종적으로 완성된 코드다.

`getCurrentPosition()` 은 2개의 콜백함수를 받는다. 이를 통해 예외처리도 진행하나보다.

드디어 완성!!!
