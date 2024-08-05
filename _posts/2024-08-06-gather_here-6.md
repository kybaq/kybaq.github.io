---
title: Gather here - 6
date: 2024-08-05 21:00:00 +09:00
categories: [Development, Project] # 메인 카테고리 , 보조 카테고리
tags: [Development, React, 회고]
---

```tsx
useEffect(() => {
  // 채팅 내역 불러오기
  const getAllMessages = async () => {
    const { data, error } = await supabase
      .from("Messages")
      .select(`*, Users (nickname, profile_image_url)`) // user_id 를 통해 관계가 맺어져 있어서 참조가 가능한 듯?
      .order("sent_at", { ascending: true });

    if (error) {
      console.error(error);
      return;
    }

    setMessages(data as MessageRow[]);
  };

  getAllMessages();
}, []);

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
    (payload) => {
      setMessages((prevMessages) => {
        return [...prevMessages, payload.new as MessageRow];
      });
      // setState 자체가 비동기적으로 동작해서 handleSubmit 함수 내부에서 동작하는 setNewMessages() 가 제대로 실행될 거라고 보장할 수 없다.
      // 따라서, 함수형으로 작성하고 데이터가 존재하는 것이 확실히 보장된 payload 객체를 이용하자!
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
```
