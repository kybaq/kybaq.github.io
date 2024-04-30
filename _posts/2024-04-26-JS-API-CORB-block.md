<!-- ---
title: Cross-Origin Read Blocking(CORB) 해결
date: 2024-04-26 21:00:00 +09:00
categories: [Development, JavaScript] # 메인 카테고리 , 보조 카테고리
tags: [Development, Web, JavaScript]
--- -->

## 목적

개인 과제를 위해, API 를 이용해서 데이터를 가져오려고 시도했는데 갑자기 CORB 오류가 발생했다.

<img src="https://kybaq.github.io/assets/img/posts/2024-04-26-CORB-해결-1.png" alt="CORB에러">

처음에 검색했을 때, 유명한(?) CORS 와 관련한 포스팅이 잔뜩 뜨길래 읽어보았다.

어느정도 읽고, 정리해보면서 이 문제를 해결하기 위한 여정을 떠나기로 했다..

## CORB란?

[크로미움 프로젝트][1]에서 찾아본 결과, Cross-Origin Read Blocking 의 약자로, Spectre(들어본 적은 있다)와 같은 위협을 막기위한 보안 기능이라고 한다. Origin 이 다른 네트워크에서 오는 응답을 막는 역할을 한단다.

자세히는 `<script>` 태그나 `<img>` 태그에서 Origin 이 다른 네트워크로 부터 xml, html, json 등의 데이터를 가져오지 못하게 만드는 것이다.
내가 정보를 요청해서 response 를 받았지만 중간에 CORB 가 response 를 empty response 로 만들어버리기 때문에 안되는 거라고 한다.

## Origin 이란??

내가 지금 Origin 이 다른 곳에서 가져오는 것이 문제라면, 일단 Origin 이 뭔지나 알고 넘어가야할 것이니 간단히 알아보자.

내가 `fetch()` 를 사용한 url 은 다음과 같다.
`https://api.themoviedb.org/3/movie/top_rated?language=en-US&page=1`;

이 url 속에 Origin 이 있다.

Origin === `https://` + `api.themoviedb.org` + Port Number(여기엔 생략됨. 보통 80번)

바로, 프로토콜 + 도메인 + 포트번호 세 가지의 조합이 Origin 이다.

셋 중 하나만 틀려도 다른 Origin 으로 판별한다.
나는 프로토콜도 다르고, 도메인과 포트번호까지 모두 다르니 당연히 다른 Origin 이다.

이 상황에서 내가 tmdb 외부에서 tmdb 의 이미지나, 영상 리소스를 가져오려고 하니까 **브라우저**에서 차단하는 것이다.

## 현재 상황

```javascript
import config from "./apikeys.js";
// api key import

const url = `https://api.themoviedb.org/3/movie/top_rated?language=en-US&page=1`;
const { API_KEY } = config;

const options = {
  method: "GET",
  headers: {
    accept: "application/json",
    Authorization: `Bearer ${API_KEY}`
  }
};

fetch(url, options)
  .then((response) => response.json())
  .then((data) => {
    const testImage = document.querySelector(
      ".movie-collection__card__image > img"
    );

    const testTitle = document.querySelector(".movie-collection__card__title");
    testTitle.innerText = `${data["results"][0]["title"]}`;

    const imgUrl = `${url}${data["results"][0]["poster_path"]}`;
    testImage.setAttribute("src", imgUrl);
  }) // 1개만 가져와서 일단 해보기.
  .catch((err) => console.error(err));
```

위와 같은 코드를 통해 데이터를 받아오는 코드를 작성했다.

`testTitle.innerText = `${data["results"][0]["title"]}`;` 이부분은 문제 없이 작동하는데, `imgUrl` 에서 말썽이 생긴 것이다.

네트워크 탭을 살펴보았는데, 요청에 대한 응답이 없는 것으로 나온다.
브라우저에 의해 empty response 가 된 것이다.

<img src="https://kybaq.github.io/assets/img/posts/2024-04-26-CORB-해결-2.png" alt="response 가 없는 것으로 나타나는 사진">

내가 Origin 이 다르면서, CORS 정책을 지키지 않은 채로 `<img>` 태그의 `src` 에 결과를 받아오려고 하니, 문제가 생기는 것이다.

## 해결 방법..?

동일한 Origin 에서 요청하면 되겠네! 싶지만 당연히 말도안된다.

일단 CORS 의 기본 동작을 살펴보자.

1.  클라이언트가 HTTP 프로토콜로 요청을 보낼 때, 나의 Origin 정보를 보낸다.
    현재는 Referer 만 나타나지만, 내 경우에서 Origin 은 `http://127.0.0.1:5500` 이다.
    <img src="https://kybaq.github.io/assets/img/posts/2024-04-26-CORB-해결-3.png" alt="response 가 없는 것으로 나타나는 사진">

2.  서버는 응답시 응답 헤더에 Access-Control-Allow-Origin 을 담아 클라이언트에게 보낸다.
    이는, 서버의 리소스에 접근하는 것이 허용된 origin 의 url 을 담는 것이다.
    근데 내 사례에서는 이것도 조회가 안됐다.

    그대신 이것을 확인했다.
    <img src="https://kybaq.github.io/assets/img/posts/2024-04-26-CORB-해결-4.png" alt="리퍼러 정책이 strict-origin-when-cross-origin 으로 나옴">

    리퍼러 정책은 서버에 요청을 보낸 페이지의 절대 주소, 쉽게 말해 Origin 의 종류에 따라 제한을 두는 것이다.

    - strict-origin-when-cross-origin: same-origin일 때는 Full URL, cross-origin일 때는 프로토콜 보안 수준이 유지될 때에 한해 Origin만 전송, 보안 수준이 낮아진다면 No data.

    나는 cross-origin 에 해당하는데, 동시에 프로토콜 보안 수준이 다르기 때문인지(http 환경) 제대로 response 를 받아올 수 없는 것 같았다.

    [1]: https://www.chromium.org/Home/chromium-security/corb-for-developers/
