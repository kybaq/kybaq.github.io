---
title: 쓰리피트(3-feet) 코드 회고
date: 2024-06-22 21:00:00 +09:00
categories: [Development, React] # 메인 카테고리 , 보조 카테고리
tags: [Development, React, 회고]
---

> 내가 작성했던 코드도 한 번 회고해보는 시간을 가져보려고 한다!
> 개인적으로 정리해둘만한 부분을 선별해보았다.

## 프로젝트에서 내가 구현했던 기능

- 네이버 지도 API 활용해 좌표 기반으로 위치 표현하기
- Page 내에서 사용하는 별도의 Header 를 만들어 홈 구장 위치를 전환할 수 있도록 설정
- 팀원 분이 작성해주신 TourAPI 호출 코드 이용해 홈 구장 근처 식당 / 숙소 정보 지도에 표현

## 중심 좌표를 전역 상태로 관리

![좌표 기반으로 지도에 Marker 표시](../assets/img/posts/2024-06-22-naverMap-1.png)

```javascript
import { create } from "zustand";
import { immer } from "zustand/middleware/immer";
import { useNavermaps } from "react-naver-maps";

const useStore = create(
  immer((set) => ({
    centerCoords: null,
    setCenterCoords: (latitude, longitude, naverMaps) =>
      set((state) => {
        state.centerCoords = new naverMaps.LatLng(latitude, longitude);
      })
  }))
);

// 지도에서 사용하는 위도 경도 객체를 생성하기 위함.
const useCenterCoordsStore = () => {
  const naverMaps = useNavermaps();

  useStore.setState((state) => {
    if (!state.centerCoords) {
      state.centerCoords = new naverMaps.LatLng(37.3595704, 127.105399);
    }
  });

  return useStore((state) => ({
    centerCoords: state.centerCoords,
    setCenterCoords: (latitude, longitude) =>
      state.setCenterCoords(latitude, longitude, naverMaps)
  }));
};

export default useCenterCoordsStore;
```

나는 지도 API 활용을 맡으면서 중심 좌표를 전역 상태 관리로 관리할 필요가 있었다.

하지만, zustand store 를 create 시에는 hook 호출을 제한하고 있어 중심 좌표를 설정 경우에는 별도의 함수를 통해 이루어지도록 작성했다.
별 것 아닌 단순한 것인데 그때는 떠올리지 못했다.

코드를 간단히 살펴보자.

먼저, store 로 관리할 대상을 create 로 생성한다.

```javascript
const useStore = create(
  immer((set) => ({
    centerCoords: null, // 초기에는 중심 좌표가 존재하지 않으니 null 로 설정,
    setCenterCoords: (latitude, longitude, naverMaps) => // naverMaps 객체의 메서드를 사용해 지도에서 사용하는 좌표 형태로 한 번 바꿔줘야 할 필요가 있어서 사용했다.
      set((state) => {
        state.centerCoords = new naverMaps.LatLng(latitude, longitude);
      })
  }))
```

`create` 내에서는 `naverMaps` 객체를 호출하는 것이 불가해서 아래 함수를 작성했다.

```javascript
// 지도에서 사용하는 위도 경도 객체를 생성하기 위함.
const useCenterCoordsStore = () => {
  const naverMaps = useNavermaps(); // 여기에서 객체 생성

  useStore.setState((state) => {
    if (!state.centerCoords) {
      state.centerCoords = new naverMaps.LatLng(37.3595704, 127.105399); // 중심 좌표 설정
    }
  });

  return useStore((state) => ({
    centerCoords: state.centerCoords,
    setCenterCoords: (latitude, longitude) =>
      state.setCenterCoords(latitude, longitude, naverMaps) // 생성한 객체를 좌표와 함께 전달함
  }));
};

export default useCenterCoordsStore;
```

이외에도 새로 알게된 `<Suspense>` 도 있지만, 별도의 포스팅으로 작성해보려고 한다!
