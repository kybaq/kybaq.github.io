---
title: Supabase Realtime 을 통한 채팅 구현 - 3
date: 2024-07-29 21:00:00 +09:00
categories: [Development, Project] # 메인 카테고리 , 보조 카테고리
tags: [Development, React, 회고]
---

Supabase 에서 Realtime 을 통해, DB에 변화가 생기면 이를 감지해 원하는 동작을 수행하도록 만드는 방법을 알았다.

따라서, 몇 가지 상태를 추가해보고 기능을 구현해보았다.

내가 직접 구현한 부분에서 문제가 있던 부분만 살펴보자.

```tsx
type MessageRow = Tables<"Messages">;
type MessageInsert = TablesInsert<"Messages">;

const Chat = () => {
  const [messages, setMessages] = useState<MessageRow[]>([]);
  const [newMessages, setNewMessages] = useState<any>(); // NOTE: type 수정하기
  const [inputValue, setInputValue] = useState<string>("");
  const [deletedMessageId, setDeletedMessageId] = useState<string>("");
  const supabase = createClient();

  const getAllMessages = async () => {
    const { data, error } = await supabase
      .from("Messages")
      .select("*")
      .order("sent_at", { ascending: true });

    if (error) {
      console.error(error);
      return;
    }

    setMessages(data as MessageRow[]);
  };

  // customHook 으로 따로 빼서 코드 정리
  useEffect(() => {
    // 채팅 내역 불러오기
    getAllMessages();
    // INSERT 이벤트 감지
    const openTalkSubscription = supabase
      .channel("openTalk") // realtime 이라는 명칭만 아니면 아무 문자열이나 가능함
      .on(
        "postgres_changes",
        {
          event: "INSERT",
          schema: "public",
          table: "Messages"
        },
        // (payload) => console.log(payload), // 여기 콜백에 setState 함수 사용
        () => {
          setMessages([...messages, newMessages]);
        }
      )
      .on(
        "postgres_changes",
        {
          event: "DELETE",
          schema: "public",
          table: "Messages"
        },
        () => {
          setMessages((prevMessages) => {
            return prevMessages.filter(
              (prevMessage) => prevMessage.message_id !== deletedMessageId
            );
          });
        }
      )
      .subscribe();
  }, [message]);
};
```

전체 코드 중에서 전반적으로 이 부분에 문제가 있었다.

데이터가 잘 보내지지만, 정확히는 `INSERT` 이벤트를 핸들러가 동작하지 않았던 것이다.

```tsx
const openTalkSubscription = supabase
  .channel("openTalk") // realtime 이라는 명칭만 아니면 아무 문자열이나 가능함
  .on(
    "postgres_changes",
    {
      event: "INSERT",
      schema: "public",
      table: "Messages"
    },
    // (payload) => console.log(payload), // 여기 콜백에 setState 함수 사용
    () => {
      setMessages([...messages, newMessages]);
    }
  );

const handleSubmit = async (evt: FormEvent<HTMLFormElement>) => {
  evt.preventDefault();
  const { data, error } = await supabase
    .from("Messages")
    .insert({
      // channel_id 하드코딩 할 필요 없도록 해야함
      channel_id: "214322ba-1cbd-424c-9ef1-e4b281f71675",
      user_id: "2e47ab8c-da7a-4590-b114-b0512b1b22cd",
      content: `${inputValue}`
    })
    .select("*");

  if (error) {
    console.error("에러: ", error);
    return;
  }
  setNewMessages(data);
  setInputValue("");
};
```

`newMessages` 는 submit 이벤트가 발생할 때, `handleSubmit` 함수 에서 input 태그의 값을 갖는다.

현재 내 코드가 정상적으로 동작하려면, `insert()` 가 일어난 다음엔 이벤트를 감지하는 것 보다 앞서 `setNewMessages()` 가 실행 되어야 하는 것이다.
그런데, 실제로 이 코드는 내 의도대로 동작하지 않았다.

`INSERT` 이벤트 핸들러가 `setNewMessages()` 보다 먼저 동작하기 때문으로 이해했다.

그래서, 이 부분을 아래처럼 변경했다.

이전에 작성했던 코드를 발전해 이렇게 구현했었다.

```tsx
const openTalkSubscription = supabase
  .channel("openTalk") // realtime 이라는 명칭만 아니면 아무 문자열이나 가능함
  .on(
    "postgres_changes",
    {
      event: "INSERT",
      schema: "public",
      table: "Messages"
    },
    (payload) => {
      setMessages((prevMessages) => {
        return [...prevMessages, payload.new as MessageRow];
      });
      // setState 자체가 비동기적으로 동작해서 handleSubmit 함수 내부에서 동작하는 setNewMessages() 가 제대로 실행될 거라고 보장할 수 없다.
      // 따라서, 함수형으로 작성하고 데이터가 존재하는 것이 확실히 보장된 payload 객체를 이용하자!
    }
  );

const handleSubmit = async (evt: FormEvent<HTMLFormElement>) => {
  evt.preventDefault();
  const { data, error } = await supabase
    .from("Messages")
    .insert({
      // channel_id 하드코딩 할 필요 없도록 해야함
      channel_id: "214322ba-1cbd-424c-9ef1-e4b281f71675",
      user_id: "2e47ab8c-da7a-4590-b114-b0512b1b22cd",
      content: `${inputValue}`
    })
    .select("*");

  if (error) {
    console.error("에러: ", error);
    return;
  }
  // setNewMessages(data); // 얘가 원인?, 실질적으로 필요하지 않을 수 있음.
  setInputValue("");
};
```

`postgres_changes` 에서 이벤트 핸들러를 정의할 때 `payload` 객체를 통해, 실제로 DB 에 전송한 값을 가져올 수 있는 것으로 확인했다.

이를 통해 간편하게 처리해줄 수 있었다.
