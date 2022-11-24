---
title: "#358[udemy react 강의노트]head 메타데이터 추가하기 "
excerpt: "NextJS"
published: true
categories:
  - Blog
tags:
  - [NextJS, deploy]

toc: true
toc_sticky: false

date: 2022-11-23
last_modified_at: 2022-11-23
---

# 배포하기

<br><br>
이제 앱이 완성되었는데 빠진 것은 depoly 하는 것이다.  
development 서버가 있는 로컬 기기에만 설치하는 것이 아니라  
원격 기기에 deploy 하여 전 세계 사용자들이  
애플리케이션을 방문할 수 있도록 하는 것이다.
<br><br>

# 배포 전 해야 할 일

<br><br>

그 전에 해야 할 것이 있다. 구축하고 있는 NextJS 프로젝트나
웹 프로젝트가 있는지 전반적으로 다시 한번 확인해야 한다.
그리고 메타데이터를 페이지에 추가해야 한다.
<br><br>

내용은 페이지에 있고 화면에서 확인할 수 있다.  
이것이 가장 중요한 부분이긴 하지만 아직 메타 데이터를 추가하지 않은 상태이기 때문에  
렌더링 된 HTML 코드를 검사해 보면 head section이 상대적으로 비어있다.  
여기 몇 개의 meta tags가 있지만 검색 엔진에 중요한 meta tag의  
description이 빠져있다.

<br><br>

```html
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width" />
<meta name="next-head-count" content="2" />
```

<br><br>
description 설정을 허용하는 description meta tag를 말하는 건데,  
예를 들어, 구글에 검색하면 나타나는 설명을 말한다.  
페이지 이름도 빠져있어서 여기 탭 이름에서 주소만 보인다.  
나중에 검색 결과로 페이지가 나타나면 이 경우처럼 Academind 같은  
웹사이트 이름이 나타나도록 하길 원할 것이다.

<br><br>
따라서 이러한 meta tags를 설정하고  
Head elements를 추가하는 것은  
사이트를 만들 때 반드시 해야 할 일이며  
이번 강의에서 해야 할 작업이기도 하다.

<br><br>

# NextJS로 head elements를 페이지에 추가하기

<br><br>
NextJS로 head elements를 페이지에 추가하는 것은 매우 간단하다.  
홈페이지에서 시작한다. (pages > index.js파일의 HomePage 컴포넌트 )

<br><br>

## Head 컴포넌트

<br><br>

이름을 설정하고 설명을 추가하려면  
NextJS에서 제공되는 특수 컴포넌트를 import 하면 된다.  
즉, Next/head에서 import 할 수 있는 Head 컴포넌트다.

<br><br>
여기 Head는 페이지의 head section에 head elements를 추가하는 컴포넌트다.  
어떻게 사용할 수 있을까?  
return 된 JSX 코드에 이것을 추가하기만 하면 된다.  
그러면 여기 MeetupList 옆에  
Head element와 Head component를 추가하여 입력한다.  
인접한 JSX 코드가 허용되지 않으므로  
React에서 import 한 React Fragment로 감싼다.

<br><br>

```jsx
import Head from "next/head";
import { MongoClient } from "mongodb";

import MeetupList from "../components/meetups/MeetupList";

const HomePage = (props) => {
  return (
    <>
      <Head></Head>
      <MeetupList meetups={props.meetups} />
    </>
  );
};
```

<br><br>
Fragment를 사용하여 Head와 Meetuplist를 감싼다.  
이제 opening과 closing의 Head component tag 사이의 Head tags 안에  
Head elements를 넣을 수 있다.  
Head section에 추가할 수 있는 모든 HTML 요소들을 추가할 수 있다.  
예를 들어, 이름을 설정하기 위해 title을 입력하고 React Meetups나 원하는 이름 입력한다.  
<br><br>

```jsx
const HomePage = (props) => {
  return (
    <>
      <Head>
        <title>React Meetups</title>
      </Head>
      <MeetupList meetups={props.meetups} />
    </>
  );
};
```

<br><br>
<br><br>
그리고 실제로 이렇게 반환되는지 확인한다. 이걸 저장한 다음 시작 페이지를 가서 보면  
여기 탭 이름에서 방금 설정한 이름으로 변경된 것을 확인할 수 있다.  
물론 page source code에서도 확인할 수 있다. Head section에서 이름을 찾을 수 있다.

<br><br>

```html
<title>React Meetups</title>
```

<br><br>
그리고 Dev tools를 열어서 렌더링 된 요소를 검사해 보면 여기서도 title을 확인할 수 있다.
이런 방법으로 제목을 추가할 수 있고 구글과 같은 검색엔진에서 선택되어 나타나는 설명을 가지는 meta tag도 추가할 수 있다.  
예를 들어, content속성에  
'Browse a huge list of highly active React meetups!'를 입력하여  
이 페이지의 meta description을 설정하면  
검색 엔진에서 검색 결과로 나타나는 웹 페이지의 텍스트 내용이 된다.

<br><br>

```jsx
const HomePage = (props) => {
  return (
    <>
      <Head>
        <title>React Meetups</title>
        <meta
          name="description"
          content="browse a huge list of highly active React meetups!"
        />
      </Head>
      <MeetupList meetups={props.meetups} />
    </>
  );
};
```

<br><br>
이렇게 해서 이제 시작 페이지에 meta data와 Head data가 추가되었다.  
이 방법은 모든 페이지에 동일하게 적용될 수 있다.  
예를 들어, new-meetup 페이지를 보면  
NewMeetupForm return 할 뿐만 아니라 React에서 import 한 Fragment도  
추가하기 위해 return에 Fragment를 입력한다.

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
그다음 Fragment의 opening과 closing tags 사이에 Head section과  
NewMeetupForm을 포함하는 인접한 JSX 코드를 가진다.

<br><br>

```jsx
return (
  <>
    <Head>
      <title>React Meetups</title>
      <meta
        name="description"
        content="Browse a huge list of highly active React meetups!"
      />
    </Head>
    <NewMeetupForm onAddMeetup={addMeetupHandler} />
  </>
);
```

<br><br>
Head는 next/head에서 import하는 구성요소다.  
그러므로 import해준다.  
<br><br>

```jsx
import { Head } from "next/head";
```

<br><br>
그리고 title을 Add a New Meetup으로 수정해본다.  
content도 Add your own meetups로 변경한다.  
<br><br>

```jsx
return (
  <>
    <Head>
      <title>Add a New Meetup</title>
      <meta
        name="description"
        content="Add your own meetups and create amazing networking opportunities"
      />
    </Head>
    <NewMeetupForm onAddMeetup={addMeetupHandler} />
  </>
);
```

<br><br>
이제 여기에 내용도 추가되었으므로 New Meetup 페이지를 가보면  
이름을 확인할 수 있다. Dev tools에서도 여기 Head section에서  
description meta tag를 볼 수 있다.

<br><br>
마지막으로 하나의 meetup에서 보이도록 이름을 설정하고 설명이 나타나도록 해야 한다.  
[meetupId]에서의 단일 meetup 페이지로 가서 Fragment로 wrap하고  
Head를 입력해 Head 구성 요소를 추가한 다음  
Fragment 사이에 MeetupDetail을 가져온다.  
그런 다음, import Fragment from 'react';와  
import Head from 'next/head'; 을 입력해 import 해야 한다.  
<br><br>

```jsx
mport Head from "next/head";
import { MongoClient, ObjectId } from "mongodb";
import MeetupDetail from "../../components/meetups/MeetupDetail";

const MeetupDetails = (props) => {
  return (
    <>
      <Head></Head>
      <MeetupDetail
        image={props.meetupData.image}
        title={props.meetupData.title}
        address={props.meetupData.address}
        description={props.meetupData.description}
      />
    </>
  );
};
```

<br><br>

그다음 Head의 opening과 closing tags 사이에  
title을 추가한다. 여기에 입력하는 이름은  
다른 페이지처럼 항상 이름이 동일한 고정된 변경할 수 없는 코드 이름이 아니다.  
여기 MeetupDetails 페이지에 로딩된 단일 meetup 항목에는  
이 페이지의 이름으로 해당 meetup 이름을 사용할 수 있도록 해서  
서로 다른 meetups 페이지에서 다른 이름을 갖도록 해보자.

<br><br>

```jsx
const MeetupDetails = (props) => {
  return (
    <>
      <Head>
        <title></title>
      </Head>
      <MeetupDetail
        image={props.meetupData.image}
        title={props.meetupData.title}
        address={props.meetupData.address}
        description={props.meetupData.description}
      />
    </>
  );
};
```

<br><br>
그러면 검색 엔진에서도 다른 이름과 다른 설명을 가진 다른 meetups이 나타난다.  
title을 하나로 고정하는 대신 다양하게 만드는 것은 매우 간단하다.  
어쨌든 규칙적으로 반환된 JSX 코드 조각 블록에 있는 것이기 때문이다.  
따라서 모든 표준 React 기능을 사용할 수 있다.

<br><br>
예를 들어, title에 중괄호{ }를 입력하여 정보가 변경될 수 있는 결과값을 output하고  
중괄호 안에 이름인 props.meetupData.title을 입력하고  
meta name="description"을 입력하여 설명에 대한 meta tag를 추가하고  
content를 입력한 다음 여기에 다른 내용으로 변경될 수 있도록  
props.meetupData.description 또는  
원하는 설명을 입력하여 설명 정보를 output 한다.

<br><br>

```jsx
return (
  <>
    <Head>
      <title>{props.meetupData.title}</title>
      <meta name="description" content={props.meetupData.description} />
    </Head>
    <MeetupDetail
      image={props.meetupData.image}
      title={props.meetupData.title}
      address={props.meetupData.address}
      description={props.meetupData.description}
    />
  </>
);
```

<br><br>
Add a New Meetup 페이지에서 했던 것처럼  
데이터 정보가 변경되지 않도록 하드 코딩된 설명으로 설정할 수 있지만  
여기서 방금 설정한 것처럼 페이지마다 다른 설명으로  
변경될 수 있도록 설정할 수도 있다.

<br><br>
이제 모든 작업을 마쳤으니  
실제로 웹사이트를 deployment 할 준비가 되었다.  
이제 검색 엔진이 모든 추가 메가 데이터를 확보하였고  
사용자에게 멋진 title을 보여줄 수 있게 되었다.

<br><br>
