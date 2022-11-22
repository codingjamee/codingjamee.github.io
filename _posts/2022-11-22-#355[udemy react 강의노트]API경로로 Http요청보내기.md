---
title: "#355[udemy react 강의노트]API경로로 Http요청보내기"
excerpt: "NextJS"
published: true
categories:
  - Blog
tags:
  - [NextJS, API, Http]

toc: true
toc_sticky: false

date: 2022-11-22
last_modified_at: 2022-11-22
---

# API 경로로 Http 요청 보내기

<br><br>
api 경로로 request를 보내는 것은 어떤 api나 백엔드로 request를 보내는 작업과 같다.  
NewMeetupForm.js 컴포넌트에서 수집한 데이터를 전달하기 위해 props.onAddMeetup을 호출했다.
<br><br>

따라서 해당 데이터로 작업하기 위해 NewMeetupForm을 사용하는 컴포넌트인  
NewMeetupPage로 이동해야 한다. 거기엔 수집한 데이터를 가져오는 addMeetupHandler가 있다.
이제 여기서 api 경로로 request를 보낸다.

<br><br>
우리가 알고 있듯이 request를 보내면 되는데,  
예를 들어, 내장된 fetch function이나 axios와 같은 타사 패키지를 사용하여 보낼 수 있다.  
이번에는 fetch function과 async await를 사용해 보자.
함수를 async 함수로 변경하고 const response = await를 입력한다.

<br><br>

```jsx
import NewMeetupForm from "../../components/meetups/NewMeetupForm";

const NewMeetupPage = () => {
  const addMeetupHandler = async (enteredMeetupData) => {
    const response = await fetch();
  };
  return <NewMeetupForm onAddMeetup={addMeetupHandler} />;
};

export default NewMeetupPage;
```

<br><br>
그다음 request를 보낼 URL을 입력한다. https://some-domain.com을 입력해보자.  
만약 외부 API라면 경로를 입력해야 하지만 여기에서는 내부 API를 사용하고 있으므로  
이 페이지를 제공하는데 사용되는 같은 서버에 의해 관리된다.  
따라서 같은 서버에 request를 보내지만, 해당 서버에 다른 경로에도 보낼 수 있는  
절대 경로를 생성할 수 있다. 경로로 특별한 api 폴더 이름인 /api를 입력하고  
파일 확장자 없이 파일 이름인 /new-meetup을 입력한다.

<br><br>

```jsx
const response = await fetch("https://some-domain.com");
```

<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
