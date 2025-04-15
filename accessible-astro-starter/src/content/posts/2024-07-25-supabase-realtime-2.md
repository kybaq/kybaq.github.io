---
title: Supabase Realtime 을 통한 채팅 구현 - 2
date: 2024-07-25 21:00:00 +09:00
categories: [Development, Project] # 메인 카테고리 , 보조 카테고리
tags: [Development, React, 회고]
---

오늘은

```tsx
const handleSubmit = async (
  evt: FormEvent<HTMLFormElement>,
  text: string | undefined
) => {
  evt.preventDefault();
  const { data, error } = await supabase
    .from("Messages")
    .insert({
      channel_id: "214322ba-1cbd-424c-9ef1-e4b281f71675",
      user_id: "2e47ab8c-da7a-4590-b114-b0512b1b22cd",
      content: `${text}`
    })
    .select("*");

  if (error) {
    console.error("에러: ", error);
  }
};

const openTalkChannel = supabase
  .channel("openTalk") // realtime 이라는 명칭만 아니면 아무 문자열이나 가능함
  .on(
    "postgres_changes",
    {
      event: "*",
      schema: "public",
      table: "Messages"
    },
    (payload) => console.log(payload)
  )
  .subscribe();
```

처음엔 이렇게 작성해서, 어떤 일이 벌어지는 건지 파악하려고 했다.

![payload 객체 확인](../assets/img/posts/2024-07-25-supabase-realtime-2-1.png)

내가 입력한 데이터는 `new` 라는 프로퍼티에 입력이 되고 있었다.

전체 메시지 내역은 서버에서 가져온 뒤, `INSERT` 이벤트의 콜백 함수 내에서 `payload.new` 를 기존 메시지 내역에 추가해주면 여기에서 상태를 더 추가하지 않아도 될 것 같다!

일단 전체 코드 완성은 내일 해야겠다..

머리가 아프다..
