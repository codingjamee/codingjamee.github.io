---
title: "#339[udemy react 강의노트]NextJS "
excerpt: "NextJS"
published: true
categories:
  - Blog
tags:
  - [Authentication]

toc: true
toc_sticky: false

date: 2022-11-16
last_modified_at: 2022-11-16
---

<br><br>

# NextJS로 페이지 사전 렌더링과 페이지에 데이터 가져오기

<br><br>
강의에 첨부된 zip 폴더를 다운로드하고 npm install을 한다.  
우리의 애플리케이션에는 페이지가 세 개 있어야 하므로  
<br><br>

## 파일을 세 개 만든다.

<br><br>

pages폴더에 index.js 파일을 만든다.  
하위에 new-meetup폴더를 만들고 내부에는 index.js파일을 만든다.  
형제폴더로 [meetupId]폴더를 만들고 내부에는 index.js파일을 만든다.  
[meetupId]는 동적으로 가져올 것이기 때문에 대괄호 안에 넣는다.  
<br><br>

## 홈화면에 가져올 것은 MeetupList다.

<br><br>

```jsx
import MeetupItem from "./MeetupItem";
import classes from "./MeetupList.module.css";

function MeetupList(props) {
  return (
    <ul className={classes.list}>
      {props.meetups.map((meetup) => (
        <MeetupItem
          key={meetup.id}
          id={meetup.id}
          image={meetup.image}
          title={meetup.title}
          address={meetup.address}
        />
      ))}
    </ul>
  );
}

export default MeetupList;
```

<br><br>
MeetupList는 props로 meetups를 가지고 있는 컴포넌트다.  
그러므로 meetups props를 반드시 제공해야 한다.  
meetups에는 id와 image, title, address를 가지고 있어야 한다.  
이 파일은 components폴더 안에 있는데, 폴더명은 변경 가능하다.  
폴더명이 변경 불가능한 것은 pages 폴더이다.  
<br><br>
pages폴더랑 다른 점은 자동으로 불러오지는 못한다는 것이다.  
대신 페이지 컴포넌트같은 다른 컴포넌트 JSX코드에서 간단하게 사용할 수 있다.  
<br><br>
pages폴더의 index.js에서 JSX코드에 MeetupList를 return하도록 한다.  
물론 해당 경로에서 import도 한다.
<br><br>

```jsx
import MeetupList from "../components/meetups/MeetupList";
```

<br><br>
MeetupList를 meetups라는 props을 주는데 지금은 DUMMY_MEETUPS를 만들어  
포인트를 넣어준다.  
<br><br>

```jsx
import MeetupList from "../components/meetups/MeetupList";

const DUMMY_MEETUPS = [
  {
    id: "m1",
    title: "A First Meetups",
    image:
      "https://cdn.pixabay.com/photo/2022/11/06/13/36/architecture-7574031_960_720.jpg",
    address: "Some address 5, 1235 Some City",
    description: "This is a first meetup!",
  },
  {
    id: "m2",
    title: "A Second Meetups",
    image:
      "https://cdn.pixabay.com/photo/2018/01/21/01/46/architecture-3095716_960_720.jpg",
    address: "Some address 10 1235 Some City",
    description: "This is a Second meetup!",
  },
];
const HomePage = () => {
  return <MeetupList meetups={DUMMY_MEETUPS} />;
};
export default HomePage;
```

<br><br>

## NewMeetup 컴포넌트를 작성한다.

<br><br>
NewMeetup 컴포넌트에서 NewMeetupForm 컴포넌트를 넣는다.  
<br><br>

```jsx
import NewMeetupForm from "../../components/meetups/NewMeetupForm";

const NewMeetup = () => {
  return <NewMeetupForm />;
};

export default NewMeetup;
```

<br><br>
NewMeetupForm은 사용자가 입력한 데이터를 추출하는 역할을 한다.  
NewMeetupForm에는 onAddMeetup란 props가 있다.  
이것을 통해 데이터를 수집한 것을 부모 컴포넌트에 넘기게 된다.
<br><br>

```jsx
import NewMeetupForm from "../../components/meetups/NewMeetupForm";

const NewMeetup = () => {
  const addMeetupHandler = (enteredMeetupData) => {
    console.log(enteredMeetupData);
  };
  return <NewMeetupForm onAddMeetup={addMeetupHandler} />;
};

export default NewMeetup;
```

<br><br>
이렇게 작성하고 나면 enteredMeetupData에 수집된 내용이 console.log에 출력된다.  
그러므로 /new-meetup으로 들어가보면 NewMeetupForm이 렌더링 된다.  
여기에 내용을 입력하고 AddMeetup을 클릭하면 내가 하려고 했던것처럼  
console.log에 출력이 되는 것을 알 수 있다.  
<br><br>

## Navigation을 추가한다.

<br><br>
Layout의 구조는 MainNavigation을 렌더링 하고 있고,  
MainNavigation은 링크가 걸린 항목들을 배치하고 있다.  
Layout컴포넌트는 다른 컴포넌트를 포함할 수 있다.  
이 레이아웃 형태를 갖춰야 하는 컨텐츠를 포함할 수 있다.

<br><br>
이 레이아웃 컴포넌트는 prop.children을 사용하여  
layout 컴포넌트 사이에 있는 내용을 가져와서  
해당 컨텐츠를 이 안에 넣어준다.  
<br><br>
index.js에서 return 한 MeetupList 코드를 Layout으로 감싸주면  
해당 네비게이션 바가 렌더링될 때 같이 렌더링 되는 것을 볼 수 있다.  
<br><br>

```jsx
const HomePage = () => {
  return (
    <Layout>
      <MeetupList meetups={DUMMY_MEETUPS} />
    </Layout>
  );
};
export default HomePage;
```

<br><br>
사전에 Link컴포넌트를 import문을 작성해주고,  
Link컴포넌트 안의 속성을 to에서 href로 변경해준다.  
<br><br>

```jsx
import Link from "next/link";
import classes from "./MainNavigation.module.css";

function MainNavigation() {
  return (
    <header className={classes.header}>
      <div className={classes.logo}>React Meetups</div>
      <nav>
        <ul>
          <li>
            <Link href="/">All Meetups</Link>
          </li>
          <li>
            <Link href="/new-meetup">Add New Meetup</Link>
          </li>
        </ul>
      </nav>
    </header>
  );
}

export default MainNavigation;
```

<br><br>

## Add New Meetup

<br><br>
MainNavigation을 감싸려면 계속 일일이 하나씩 해줘야 하는 방법도 있지만  
다른 방법도 있다.  
`_app.js` 파일이다. 이 파일이 없다면 직접 만들어줘도 된다.  
<br><br>

```jsx
import "../styles/globals.css";

function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />;
}

export default MyApp;
```

<br><br>
루트 컴포넌트 같은 개념이다.  
NextJS가 렌더링하는 최상위 컴포넌트처럼 작동한다.  
여기서 프로퍼티를 받고 구조분해 할당을 해서 정보를 꺼내는데  
그 정보는 Component와 pageProps다. 그건 MyApp컴포넌트로 자동으로 전달된다.  
<br><br>
NextJS가 이 특수 컴포넌트를 사용하기 때문이다.  
Component는 렌더링 될 실제 페이지 컨텐츠를 저장하고 있는 프로퍼티다.  
그래서 전환할 때마다 변하게 될 것이다.  
pageProps는 페이지가 받는 특수 프로퍼티인데  
페이지에서 아무 프로퍼티도 받고있지 않는데,  
지금은 그런 프로퍼티를 제공하는 소스가 없다.
<br><br>
이제 \_app.js파일의 Component 컴포넌트가  
결국 여러페이지에 해당하는 실제 페이지 컨텐츠가 될 것이다.  
우리가 페이지 A에서 B로 갈 때마다 변경될 것이다.  
<br><br>

## `_app.js`파일에서 전체 Component에 적용

<br><br>
index.js의 Layout컴포넌트를 삭제하고,  
`_app.js`파일에 Component를 Layout으로 감싼다.
<br><br>

```jsx
import Layout from "../components/layout/Layout";
import "../styles/globals.css";

function MyApp({ Component, pageProps }) {
  return (
    <Layout>
      <Component {...pageProps} />
    </Layout>
  );
}

export default MyApp;
```

<br><br>
이제 컴포넌트마다 Layout컴포넌트를 랩핑하지 않아도 렌더링이 된다.  
<br><br>

## 동적경로 meetupId페이지 읽어들이기

<br><br>
MeetupItem.js파일에서 useRouter를 사용한다.  
<br><br>

```jsx
import { useRouter } from "next/router";
```

<br><br>
그러나 여기에는 query메서드만 있는게 아니다.  
<br><br>

```jsx
router.push();
```

<br><br>
이 router.push를 사용하면 새 페이지를 페이지 더미에 연결한다.  
Link컴포넌트를 사용하는 것이나 마찬가지이게 된다.  
Link컴포넌트 대신 프로그래밍적으로 탐색하는 것이다.  
<br><br>

```jsx
router.push("/" + props.id);
```

<br><br>
MeetupList를 렌더링할 때 ID프로퍼티를 MeetupItem으로 보냈으므로  
위와 같이 props로 받은 id를 합쳐준다.

<br><br>

# pages > [meetupId] > index.js 파일 코드 추가하기

<br><br>

```jsx
const MeetupDetails = () => {
  return (
    <>
      <img
        src="https://cdn.pixabay.com/photo/2018/01/21/01/46/architecture-3095716_960_720.jpg"
        alt="second Meetups"
      />
      <h1>A First Meetup</h1>
      <address>Some Street 5, Some City</address>
      <p>The meetup description</p>
    </>
  );
};
export default MeetupDetails;
```

<br><br>
img를 가져올 것인데 DUMMY_MEETUPS에서 가져온 img이다.  
그런데 우리는 더미에서 가져오지 않고 조금 후에 백엔드에서 가져올 것이다.  
그러므로 위와같이 하드코딩만 한다.
<br><br>
components>meetups>MeetupDetail.js을 만들어서 조금 수정해 보면

<br><br>

```jsx
const MeetupDetail = (props) => {
  return (
    <>
      <img src={props.img} alt={props.title} />
      <h1>{props.title}</h1>
      <address>{props.address}</address>
      <p>{props.desription}</p>
    </>
  );
};
export default MeetupDetail;
```

<br><br>
그리고 MeetupDetails파일은 다음과 같이 수정하여 준다.
<br><br>

```jsx
import MeetupDetail from "../../components/meetups/MeetupDetail";

const MeetupDetails = () => {
  return (
    <MeetupDetail
      img="https://cdn.pixabay.com/photo/2018/01/21/01/46/architecture-3095716_960_720.jpg"
      title="A First Meetup"
      address="Some Street 5, Some City"
      description="The meetup description"
    />
  );
};
export default MeetupDetails;
```

<br><br>

## CSS 스타일링 module.css

<br><br>
이렇게 수정하는 이유는 스타일링 때문이다.  
페이지에있는 컴포넌트에도 CSS파일을 임포트 할 수 있다.  
그러나 페이지 폴더는 깔끔하게 남겨두고 페이지 JS파일들만 넣어준다.

<br><br>
다른 폴더에는 자바스크립트 파일과 CSS파일이 쌍으로 같이 있다.  
이제 meetups폴더에 MeetupDetail.module.css파일을 만들자.  
<br><br>
css module을 이용하면 css 클래스 스타일의 유효범위를  
리액트 컴포넌트로 제한하고 NextJS 프로젝트에서 바로 사용할 수 있다.  
<br><br>
MeetupDetail.module.css 파일
<br><br>

```css
.detail {
  text-align: center;
}

.detail img {
  width: 100%;
}
```

<br><br>
MeetupDetail.js파일
<br><br>

```jsx
import classes from "./MeetupDetail.module.css";

const MeetupDetail = (props) => {
  return (
    <section className={classes.detail}>
      <img src={props.img} alt={props.title} />
      <h1>{props.title}</h1>
      <address>{props.address}</address>
      <p>{props.desription}</p>
    </section>
  );
};
export default MeetupDetail;
```

<br><br>
