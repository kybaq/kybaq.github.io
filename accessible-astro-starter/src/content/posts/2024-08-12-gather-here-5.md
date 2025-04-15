---
title: Gather here - 5
date: 2024-08-12 21:00:00 +09:00
categories: [Development, Project] # 메인 카테고리 , 보조 카테고리
tags: [Development, React, 회고]
---

## `layout.tsx` 를 이용해 컴포넌트 마운트 수 줄이기

전반적인 리팩토링을 진행했다.

먼저 Chat, Calender 컴포넌트가 페이지 이동마다 새로 마운트 되고 있는 것이 비효율적이라 생각했다.

그래서 useEffect 를 통해 페이지 이동 마다 mount 되는 것을 확인했다.
내가 담당한 Sidebar 영역도 하나의 컴포넌트로 만들어 코드를 더 세세히 나누고 싶어 이전에 구현되어 있던 부분을 `layout.tsx` 로 옮겨두었다.

그런데, MainLayout 내에서 모바일 브라우저인지 확인하는 부분이 있는데 useEffect 를 통해 검사하고 있어 상태를 최소화했다.

화면 사이즈는 이벤트 핸들러보다 css 를 통해 관리하는 것이 효율적이라고 생각했다.
그래서 모달을 위한 상태만 남기고 모두 정리했다.

또한 레이아웃이 클라이언트 컴포넌트 인것이 마음에 들지 않았었는데,
상태를 정리하고 나니 레이아웃으로 만들 수 있게 되었다.

그제서야 MainSideBar 라는 컴포넌트로 분리가 가능해졌다!!!

```tsx
"use client";

import Calender from "@/components/MainPage/MainSideBar/Calender/Calender";
import Chat from "@/components/MainPage/MainSideBar/Chat/Chat";
import { useState } from "react";
import Image from "next/image";
import ChatModal from "./Chat/ChatModal";

const MainSideBar = () => {
  const [isModalOpen, setIsModalOpen] = useState(false);

  const openModal = () => {
    setIsModalOpen(true);
  };

  const closeModal = () => {
    setIsModalOpen(false);
  };
  return (
    <>
      <div className="col-span-1 m:hidden">
        <div className="sticky top-4">
          <h4 className="flex items-center ml-2 mb-4 text-labelStrong">
            아래에 새로운 요소 들어갈 자리임
          </h4>
          <div className="w-full h-full bg-primary">
            <Image
              src="/assets/blank.svg"
              width={322}
              height={256}
              alt="빈 이미지"
            />
          </div>
          <Calender />
        </div>
      </div>
      {isModalOpen ? (
        <button
          onClick={closeModal}
          className="fixed bottom-4 right-1 z-10 hover:animate-bounce"
        >
          <Image
            src="/Chat/close.svg"
            alt="채팅창 닫기 버튼"
            width={90}
            height={60}
            priority
            className="w-auto h-auto"
          />
        </button>
      ) : (
        <button
          onClick={openModal}
          className="fixed bottom-4 right-1 z-10 hover:animate-bounce"
        >
          <Image
            src="/Chat/chat.svg"
            alt="채팅창 열기 버튼"
            width={90}
            height={60}
            priority
            className="w-auto h-auto"
          />
        </button>
      )}
      <ChatModal isOpen={isModalOpen} onRequestClose={closeModal}>
        <Chat />
      </ChatModal>
    </>
  );
};

export default MainSideBar;
```

아예 이렇게 컴포넌트로 분리하고 나니, 정말로 한 번만 마운트가 발생했다.

컴포넌트 분리 전에는 매번 마운트가 발생했다.

![컴포넌트 분리 전후 마운트 수 비교](../assets/img/posts/2024-08-12-gather-here-5.gif)

layout 으로 분리하고 난 뒤에는 페이지를 계속 이동해도 마운트가 일어나지 않았다!

![컴포넌트 분리 전후 마운트 수 비교](../assets/img/posts/2024-08-12-gather-here-5-2.gif)

## UI 와 비즈니스 로직 분리

다음으로는 채팅 부분의 비즈니스 로직을 커스텀 훅으로 분리해줬다!

```tsx
import { useState, useEffect, useRef, FormEvent } from "react";
import { useUser } from "@/provider/UserContextProvider";
import { createClient } from "@/utils/supabase/client";
import { MessageRow } from "@/types/chats/Chats.type";

const useChat = () => {
  const { user } = useUser();
  const [messages, setMessages] = useState<MessageRow[]>([]);
  const [inputValue, setInputValue] = useState<string>("");
  const [isModalOpen, setIsModalOpen] = useState<boolean>(false);
  const chatContentDiv = useRef<HTMLDivElement>(null);

  useEffect(() => {
    const supabase = createClient();

    const getAllMessages = async () => {
      const { data: messages, error } = await supabase
        .from("Messages")
        .select(`*, Users (nickname, profile_image_url)`)
        .order("sent_at", { ascending: true });

      if (error) {
        console.error(error);
        return;
      }

      setMessages(messages as MessageRow[]);

      if (chatContentDiv.current) {
        requestAnimationFrame(() => {
          chatContentDiv.current!.scrollTop =
            chatContentDiv.current!.scrollHeight;
        });
      }
    };

    getAllMessages();

    const openTalkSubscription = supabase
      .channel("openTalk")
      .on(
        "postgres_changes",
        {
          event: "INSERT",
          schema: "public",
          table: "Messages"
        },
        () => {
          getAllMessages();
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
          getAllMessages();
        }
      )
      .subscribe();

    return () => {
      openTalkSubscription.unsubscribe();
    };
  }, []);

  const handleSubmit = async (evt: FormEvent<HTMLFormElement>) => {
    evt.preventDefault();

    if (!user) return;

    const supabase = createClient();

    const { error } = await supabase
      .from("Messages")
      .insert({
        channel_id: "214322ba-1cbd-424c-9ef1-e4b281f71675", // 예제에서는 하드코딩된 채널 ID 사용
        user_id: `${user.id}`,
        content: inputValue
      })
      .select("*");

    if (error) {
      console.error("에러: ", error);
      return;
    }

    setInputValue("");
  };

  const handleDelete = async (message_id: string) => {
    const supabase = createClient();

    const { error } = await supabase
      .from("Messages")
      .delete()
      .eq("message_id", message_id);

    if (error) {
      console.error("에러: ", error);
      return;
    }
  };

  return {
    user,
    messages,
    inputValue,
    setInputValue,
    isModalOpen,
    setIsModalOpen,
    chatContentDiv,
    handleSubmit,
    handleDelete
  };
};

export default useChat;
```

여기 `useChat` 이라는 hook 을 하나 만들어 UI 와 비즈니스 로직을 분리해냈다!

이번엔 여기까지!
