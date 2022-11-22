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
new-meetup/index.js파일
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

# request 보낼 URL

<br><br>
그다음 request를 보낼 URL을 입력한다. https://some-domain.com을 입력해보자.  
만약 외부 API라면 경로를 입력해야 하지만 여기에서는 내부 API를 사용하고 있으므로  
이 페이지를 제공하는데 사용되는 같은 서버에 의해 관리된다.  
<br><br>
new-meetup/index.js파일
<br><br>

```jsx
const response = await fetch("https://some-domain.com");
```

<br><br>
따라서 같은 서버에 request를 보내지만, 해당 서버에 다른 경로에도 보낼 수 있는  
절대 경로를 생성할 수 있다. 경로로 특별한 api 폴더 이름인 /api를 입력하고  
파일 확장자 없이 파일 이름인 /new-meetup을 입력한다.

<br><br>

```jsx
const response = await fetch("/api/new-meetup");
```

<br><br>
만약 자바스크립트 파일 이름을 다르게 입력했다면,  
`"/api/new-meetup"` 중에  
`"/new-meetup"`  
여기를 동일하게 사용해야 한다.  
이렇게 하면 API 폴더에 있는 new-meetup.js 파일로 request가 전송되고  
아래의 function을 trigger 한다.

<br><br>
api/new-meetup.js

```jsx
import { MongoClient } from "mongodb";

const handler = async (req, res) => {
  if (req.method === "POST") {
    const data = req.body;

    const { title, image, address, description } = data;

    const client = await MongoClient.connect(
      'mongosh "mongodb+srv://cluster0.7lqdxbj.mongodb.net/myFirstDatabase" --apiVersion 1 --username mongo-user1'
    );
    const db = client.db();

    const meetupsCollection = db.collection("meetups");

    const result = meetupsCollection.insertOne(data);
    console.log(result);
    client.close();
    res.status(201).json({ message: "Meetup inserted" });
  }
};

export default handler;
```

<br><br>

# request 구성하기

<br><br>
request가 경로에 도달하면 NextJS가 이 function을 trigger 한다.  
아래와 같이 다른 종류의 requst에는 아무것도 하지 않기 때문에 POST요청을 보내는 것이 필요하다.
<br><br>
new-meetup.js
<br><br>

```jsx
const handler = async (req, res) => {
  if (req.method === "POST") {
```

<br><br>

이 request를 구성하기 위해 fetch를 전달 할 수 있는 두 번째 인자argument를 사용해야 한다.  
예를 들어, 여기에 post를 입력해 method key를 설정할 수 있다.  
그 다음 body에도 request에 첨부된 데이터를 넣어야 하고  
그것은 데이터베이스에 저장할 제목, 이미지, 주소, 설명 필드에 대한 데이터를 전달하는  
자바스크립트 객체여야 한다.

<br><br>
new-meetup/index.js
<br><br>

```jsx
import NewMeetupForm from "../../components/meetups/NewMeetupForm";

const NewMeetupPage = () => {
  const addMeetupHandler = async (enteredMeetupData) => {
    const response = await fetch("/api/new-meetup", {
      method: "POST",
    });
  };
  return <NewMeetupForm onAddMeetup={addMeetupHandler} />;
};

export default NewMeetupPage;
```

<br><br>
여기 NewMeetupForm에서 얻은 enteredMeetupData를 보면 해당 필드들이 이미 있다.  
제목, 이미지, 주소, 설명 부분을 확인할 수 있다.  
<br><br>

<br><br>
NewMeetupForm.js

```jsx
    const meetupData = {
      title: enteredTitle,
      image: enteredImage,
      address: enteredAddress,
      description: enteredDescription,
    };

    props.onAddMeetup(meetupData);
  }

```

<br><br>
new-meetup/index.js
<br><br>

```jsx
const NewMeetupPage = () => {
  const addMeetupHandler = async (enteredMeetupData) => {
    const response = await fetch("/api/new-meetup", {
      method: "POST",
    });
  };
  return <NewMeetupForm onAddMeetup={addMeetupHandler} />;
};

export default NewMeetupPage;
```

<br><br>
이러한 경우는 enteredMeetupData를 body에 동일하게 넣으면 된다.
하지만, 대신에 JSON.stringify를 입력하여 내장된 JSON 전환 방법으로 전환해야 한다.

<br><br>
new-meetup/index.js
<br><br>

```jsx
const response = await fetch("/api/new-meetup", {
  method: "POST",
  body: JSON.stringify(enteredMeetupData),
});
```

<br><br>
그다음에는 JSON 데이터를 보내는 것을 명확히 하기 위해 headers를 추가하고  
Content-Type을 입력하여 콘텐츠 유형의 header를 추가 하고  
이를 애플리케이션 JSON으로 설정하기 위해 'application/json'을 입력한다.

이렇게 하면 request가 구성되고 전송된다.
<br><br>

```jsx
const response = await fetch("/api/new-meetup", {
  method: "POST",
  body: JSON.stringify(enteredMeetupData),
  headers: {
    "Content-Type": "application/json",
  },
});
```

<br><br>

# response로 부터 얻은 데이터

<br><br>

궁극적으로 응답을 받고 원한다면 응답response로부터 데이터data를 얻게 된다.  
항상 그렇듯이, fetch function으로부터 응답을 얻기 위해  
const data = await response.json( ); 을 입력한다.  
그런 다음 해당 데이터로 작업할 수 있다.

<br><br>

```jsx
const NewMeetupPage = () => {
  const addMeetupHandler = async (enteredMeetupData) => {
    const response = await fetch("/api/new-meetup", {
      method: "POST",
      body: JSON.stringify(enteredMeetupData),
      headers: {
        "Content-Type": "application/json",
      },
    });

    const data = await response.json();
  };
  return <NewMeetupForm onAddMeetup={addMeetupHandler} />;
};
```

<br><br>
하지만 여기서는 그냥 데이터를 console.log한다.

<br><br>

```jsx
const data = await response.json();
console.log(data);
```

<br><br>
모든 파일이 모두 저장되었고 MongoDB가 우리 local IP를 화이트리스트로 하고,  
데이터베이스에 연결하기 위한 인증 정보가 올바른지 확인했다면  
React앱의 Meetups form으로 가서 다시 로딩하고 Dev Tools를 열어서  
`'A first Meetup'`같은 데이터를 입력한다.

<br><br>
그런 다음, 이전에 선택한 jpg URL을 가져다가 Meetup image 칸에 넣는다.  
Address에는 Some Street S. Some City,  
Description에는 This is a first meetup! 을 입력한다.  
이제 Add Meetup 버튼을 클릭하면 응답을 볼 수 있다.  
Meetup inserted는 API 경로에서 다시 보낸 메시지이므로 잘된 것 같다.

<br><br>
MongoDB Clusters로 가서 Collections를 클릭하면 meetups 데이터베이스가 있다.  
그리고 안을 보면 방금 추가한 데이터로 하나의 meetup, 자동으로 생성된 ID가 있다.

이것이 작동 중인 API 경로이며 request를 보내는 방법이다.  
meetup을 추가한 후에는 다른 곳으로 이동해야 하는데  
여기의 new-meetup 페이지로 돌아간다.  
<br><br>

API 경로가 아닌 맨 처음의 마지막 페이지다.
응답받은 후에는 자동으로 다른 페이지로 이동하고 작동되었는지 확인하고 싶을 것이다.
useRouter( )를 입력하여 next/router를 import 하면  
해당 router 객체에 액세스할 수 있다.

<br><br>

```jsx
// our-domain.com/new-meetup
import { useRouter } from "next/router";

import NewMeetupForm from "../../components/meetups/NewMeetupForm";

function NewMeetupPage() {
  const router = useRouter();

  async function addMeetupHandler(enteredMeetupData) {
    const response = await fetch("/api/new-meetup", {
      method: "POST",
      body: JSON.stringify(enteredMeetupData),
      headers: {
        "Content-Type": "application/json",
      },
    });

    const data = await response.json();

    console.log(data);

    router.push("/");
  }

  return <NewMeetupForm onAddMeetup={addMeetupHandler} />;
}

export default NewMeetupPage;
```

<br><br>
console.log 이후에 router에 push나 replace를 입력하면  
되돌아가기 버튼으로 다시 돌아가지 않도록 하고 다른 페이지로 이동할 수 있다.  
가능하지 않은 것을 확인할 수 있지만  
 그렇게 하지 않고 그냥 push('/)만 입력하여 시작 페이지로 설정한다.

<br><br>
이것을 저장한 다음 React Meetups Form 정보에 제목은 A second meet로 입력하고  
주소에는 Second Street 2를 설명에는 This is the second street라고 입력한다.  
모든 정보 입력을 마치고 Add Meetup 버튼을 클릭하면 다른 페이지로 이동한다.

<br><br>
물론 loadingspinner와 다른 모든 것들을 볼 수 있지만  
모든 일반적인 클라이언트 사이드이며 NextJS에 특정한 react는 보이지 않는다.
NextJS의 구체적인 부분은 API 루트였고  
보는것과 같이, 경로는 이제 잘 작동되고 있다.
