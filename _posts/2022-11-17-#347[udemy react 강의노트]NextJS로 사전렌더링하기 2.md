---
title: "#347[udemy react 강의노트]NextJS로 사전렌더링하기 2 "
excerpt: "NextJS"
published: true
categories:
  - Blog
tags:
  - [Authentication]

toc: true
toc_sticky: false

date: 2022-11-17
last_modified_at: 2022-11-17
---

# 백엔드에서 데이터 가져오기 시뮬레이션

<br><br>
지금은 index.js에 가져와서 MeetupList에 보내준  
meetups라는 프롭은 DUMMY_MEETUPS였다. 즉, 하드코딩된 객체였다.  
그러나 원래대로라면 백엔드에서 데이터를 가져온다.  
<br><br>
백엔드에 접근할 때 동작하는 방식대로 동작하도록 바꾼다.  
React에서는 페이지가 렌더링할때 HTTP요청을 보내려면  
이를 처리하기 위해 일반적으로 useEffect훅을 사용한다.  
deps는 비워두어 컴포넌트가 처음 렌더링 될 때만 작동하도록 한다.  
<br><br>
useState로 loadedMeetups를 만들고,  
deps가 빈 useEffect에서 setLoadedMeetups를 한다.  
거기에는 DUMMY_MEETUPS를 전달해 준다.  
<br><br>
<br><br>

```jsx
const HomePage = () => {
  const [loadedMeetups, setLoadedMeetups] = useState([]);
  useEffect(() => {
    setLoadedMeetups(DUMMY_MEETUPS);
  }, []);
  return <MeetupList meetups={loadedMeetups} />;
};
```

<br><br>
그리고 MeetupList에 전달해 주는 프롭에는 loadedMeetups를 전달해준다.  
확인해보면 이전과 별반 다를바 없이 사이트가 동작한다.  
그러나 중요한 점이 바뀐 것은 useEffect다.  
<br><br>
useEffect는 HomePage컴포넌트가 렌더링 된 이후에 동작한다.  
그러므로 loadedMeetups가 빈 배열 상태로 로딩 되었다가  
useEffect훅 안에 있는 setLoadedMeetups로 인해  
다시 DUMMY_MEETUPS가 전달 된 loadedMeetups가 되고, 그렇게 재렌더링이 된다.  
<br><br>
이런식으로 실제 백엔드 코드에서 가져온다면 로딩 화면을 볼 것이다.  
두 번의 렌더링 사이클 떄문에 검색엔진 최적화에 문제가 생기게 된다.  
소스를 확인해보면  
<br><br>
<br><br>

```html
<main class="Layout_main__NgJgX">
  <ul class="MeetupList_list__C2D8b"></ul>
</main>
```

<br><br>
위와 같이 되어있는데 ul태그만 있고 그 안의 MeetupList는 확인할 수 없다.  
서버에서 가져온 화면에 보이는 HTML페이지에는 컨텐츠가 들어있지 않은 것이다.  
<br><br>

## 자동으로 사전 렌더링된 페이지를 생성하는 NextJS의 동작방식

<br><br>
NextJS가 자동으로 생성하는 사전 렌더링된 페이지는  
두번째 사이클을 기다리지 않는다.  
언제나 첫번째 렌더링 사이클의 결과를 가져와서  
사전렌더링한 HTML코드를 반환한다.  
거기에는 MeetupList 데이터가 없다.  
<br><br>

## NextJS의 해결책

<br><br>
데이터가 있는 페이지를 사전 렌더링 해야 하지만  
데이터는 우리가 기다려야 하므로  
데이터를 갑져왔을 때 NextJS에 알려줘야 하는데  
그 기능을 NextJS는 가지고 있다.  
<br><br>
<br><br>
지금 페이지는 컴포넌트가 첫번째 렌더링 사이클을 마친 후의  
스냅샷을 콘텐츠로 가지고 있다.  
중요한 데이터가 손실된 상태다.  
<br><br>
어떤 라우터가 있다면 요청은 라우터에 전해지고 페이지로 갈 것이다.  
여기서 사전 렌더링한 페이지를 반환하지만 데이터는 손실될 것이다.  
SEO라는 것인데, 검색 엔진 최적화에 좋을 수 있지만 안좋을 때도 있다.  
HTML페이지를 받은 후에 React가 받아가서  
페이지에 hydrate라고 부르는 작업을 수행한다.  
<br><br>
즉 React가 페이지를 싱글페이지 애플리케이션으로 만들고 제어하는 것이다.  
useEffect함수를 실행할 것이고 데이터를 받아와서 페이지를  
브라우저에서 업데이트 할 것이다. 서버에서도, 사전렌더링한 페이지에서도 아니다.  
대신 이 페이지를 브라우저에서 받은 후일 것이다.  
<br><br>

## 데이터가 있는 페이지를 사전 렌더링 하려면

<br><br>

따라서 이 경우에는 필요한 모든 데이터가 있는 완전한 대화형 페이지나 앱을 갖게 된다.  
하지만 초기에 반환된 HTML코드에 이미 데이터가 포함되도록  
데이터가 있는 페이지를 사전 렌더링 하려면  
이 내장된 사전 렌더링 프로세스를 미세 조정해야하고  
그에 맞는 설정을 해야 한다.

<br><br>
이를 위해 NextJS는 페이지 렌더링 방법을 제어하는 데 사용할 수 있는  
두 가지 형태의 사전렌더링을 제공한다.
<br><br>

```
1. 정적 생성 (Static Generation)
2. 서버사이드 렌더링 Server-side Rendering
```

<br><br>
두가지는 비슷해 보이지만 서로 다른 시점에서 코드가 실행된다.

<br><br>

# 1. Static Generation

<br><br>

## 작동방식

<br><br>

이 방식은 일반적으로 사용하는 접근법이다.
Static Generation에서 페이지 컴포넌트가 사전렌더링 되는 시점은  
애플리케이션을 빌드하거나 Next프로젝트를 빌드하는 시점,  
즉 프로덕션용으로 빌드하는 시점이다.  
<br><br>
기본적으로 요청이 서버에 도달했을 때 서버에서  
즉각적으로 페이지를 사전렌더링하는 것이 아니다.  
대신 개발자가 프로덕션용 사이트를 빌드할 때 사전 렌더링을 한다.  
<br><br>
적어도 기본적으로 사이트가 배포되고 나면 사전렌더링 페이지는 변경되지 않는다는 의미다.  
데이터를 업데이트 했는데 사전렌더링한 페이지를 변경해야 한다면  
해당 빌드 프로세스를 다시 시작하고 다시 배포해야 한다.  
<br><br>
자주 바뀌어야 한다면 대안이 있다. 그건 추후에 설명할 것이다.  
<br><br>
NextJS는 페이지를 마련한다.  
기본적으로 이미 정적 페이지를 생성하고 기본적으로 빌드 프로세스 중에 페이지를 생성한다.  
그러나 데이터를 기다려야 한다면 페이지 컴포넌트 파일안에서 특수 함수를 export로 내보내면 된다.

<br><br>
이건 오직 페이지 컴포넌트 파일에서만 작동한다. 다른 컴포넌트 파일에서는 안된다.
반드시 함수 이름을 getStaticProps라고 해야 한다.  
NextJS가 이 이름을 가진 함수를 찾을 것이다.  
발견하면 사전 렌더링 프로세스 중에 이 함수를 실행한다.

<br><br>
<b>index.js파일중 일부</b>
<br><br>

```jsx
const HomePage = () => {
  const [loadedMeetups, setLoadedMeetups] = useState([]);
  useEffect(() => {
    setLoadedMeetups(DUMMY_MEETUPS);
  }, []);
  return <MeetupList meetups={loadedMeetups} />;
};

export const getStaticProps = () => {};
export default HomePage;
```

<br><br>

# getStaticProps함수 호출

<br><br>

따라서 컴포넌트 함수를 바로 호출하지 않고 반환된 JSX스냅샷을 HTML 컨텐츠로 사용한다.  
그러나 우선 컴포넌트 함수를 호출하기 전에 getStaticProps함수를 호출한다.

<br><br>
이 함수는 실제로 이 페이지에서 사용할 props를 준비한다.  
props는 페이지에서 필요한 데이터를 포함할 수 있다.  
getStaticProps는 비동기적으로 설정될 수 있어서 유용하다.  
<br><br>

```jsx
export async const getStaticProps = () => {};
```

<br><br>
여기서 promise를 반환할 수 있다.  
NextJS는 이 promise가 해결될 때까지 기다린다.  
그 다음에 이 컴포넌트 함수에서 사용할 props를 반환한다.  
이렇게 하면 이 컴포넌트 함수가 실행되기 전에 데이터를 읽어들일 수 있어서  
이 컴포넌트를 필요한 데이터와 함께 렌더링 할 수 있다.  
<br><br>
getStaticProps 안에서는 일반적으로 서버에서만 돌아가는  
어떤 코드든지 전부 실행할 수 있다.  
파일 시스템에 접근할 수도 있고 데이터베이스에 안전하게 연결할 수도 있다.

<br><br>
이 함수 안에서 작성하면 클라이언트 측에 들어가지 않기 때문에  
클라이언트 측에서 절대 실행되지 않는다.  
이 코드는 빌드 프로세스 중에 실행되기 때문이다.  
서버에서도 특히 방문자인 클라이언트 측에서도 실행되지 않는다.

<br><br>
따라서 여기에 쓴 코드는 절대 방문자의 컴퓨터에 도달하지 못한다.  
즉 방문자 컴퓨터에서 실행될 수 없다.  
이제 여기 getStaticProps에서 원하는 모든 작업을 수행할 수 있다.

<br><br>
예를들어 API나 데이터베이스에서 데이터를 가져오거나  
파일시스템의 일부 파일에서 데이터를 읽어올 수도 있다.  
<br><br>
하지만 일단 필요한 데이터를 얻는 작업을 모두 완료했으면  
항상 getStaticProps에서 객체를 반환return해야 한다.

<br><br>

```jsx
export const getStaticProps = () => {
  //fetch data from an API
  return {};
};
```

<br><br>
<br><br>
return된 객체에서 많은 항목들을 구성할 수 있다.
이름이 반드시 props인 프로퍼티를 설정한다.  
거기에 다른 객체를 저장하는데 이건 HomePage 컴포넌트 함수에서 받는  
props 객체가 될 것이다.

<br><br>

```jsx
const HomePage = (props) => {
  const [loadedMeetups, setLoadedMeetups] = useState([]);
  useEffect(() => {
    setLoadedMeetups(DUMMY_MEETUPS);
  }, []);
  return <MeetupList meetups={loadedMeetups} />;
};

export const getStaticProps = () => {
  //fetch data from an API
  return {
    props: {},
  };
};
export default HomePage;
```

<br><br>
HomePage컴포넌트에서 받은 props객체를  
getStaticProps에서 props로 return 하는 것이다.  
그 안에 meetups키를 넣을 수 있다.  
구조는 마음대로 정할 수 있다.  
meetups의 값에 DUMMY_MEETUPS를 설정한다.  
<br><br>

```jsx
export const getStaticProps = () => {
  //fetch data from an API
  return {
    props: {
      meetups: DUMMY_MEETUPS,
    },
  };
};
export default HomePage;
```

<br><br>
그러면 getStaticProps에서 DUMMY_MEETUPS를 읽어들이고 준비한다음
이 페이지 컴포넌트에서 사용할 props로 설정된다.  
따라서 이 페이지 컴포넌트는 상태관리할 필요가 없고,  
useEffect도 필요없다. import도 삭제한다.
MeetupList 컴포넌트의 meetups도 props.meetups로 바꾼다.

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
const HomePage = (props) => {
  return <MeetupList meetups={props.meetups} />;
};

export const getStaticProps = () => {
  //fetch data from an API
  return {
    props: {
      meetups: DUMMY_MEETUPS,
    },
  };
};
export default HomePage;
```

<br><br>
meetups는 아래에서 추가한 meetups프로퍼티에서 온 것이다.  
이렇게 하면 클라이언트에서 서버쪽으로, 빌드프로세스 과정쪽으로 데이터를 가져올 수 있다.  
<br><br>
전부 저장하고 페이지를 읽어들이면 meetups가 보인다.  
그리고 페이지소스를 확인하면 비어있던 ul태그 안의 li는 보이지 않고  
이미지, 제목 등이 들어있는 항목들이 ul안에 있는 것을 알 수 있다.  
<br><br>

```html
<ul class="MeetupList_list__C2D8b">
  <li class="MeetupItem_item__pvgsv">
    <div class="Card_card__73YTa">
      <div class="MeetupItem_image__0jYm_">
        <img
          src="https://cdn.pixabay.com/photo/2022/11/06/13/36/architecture-7574031_960_720.jpg"
          alt="A First Meetups"
        />
      </div>
      <div class="MeetupItem_content__fvTRB">
        <h3>A First Meetups</h3>
        <address>Some address 5, 1235 Some City</address>
      </div>
      <div class="MeetupItem_actions__IeeH4"><button>Show Details</button></div>
    </div>
  </li>
  <li class="MeetupItem_item__pvgsv">
    <div class="Card_card__73YTa">
      <div class="MeetupItem_image__0jYm_">
        <img
          src="https://cdn.pixabay.com/photo/2018/01/21/01/46/architecture-3095716_960_720.jpg"
          alt="A Second Meetups"
        />
      </div>
      <div class="MeetupItem_content__fvTRB">
        <h3>A Second Meetups</h3>
        <address>Some address 10 1235 Some City</address>
      </div>
      <div class="MeetupItem_actions__IeeH4"><button>Show Details</button></div>
    </div>
  </li>
</ul>
```

<br><br>
이제 사전렌더링도 했고 전체 HTML코드도 포함하고 있다.  
당연히 검색 엔진에도 좋다.  
이제 클라이언트 측 컴포넌트의 두 번째 렌더링 사이클에서 데이터를 받는 것이 아니라  
초기에 이 페이지를 사전 렌더링하기 전에 빌드 프로세스에서 받으니까 말이다.  
NextJS의 주요기능 중 하나다.
<br><br>
getStaticProps를 완전히 이해하고 무엇을 할 수 있는지 알아보기 위해  
dev서버를 끊고 npm run build를 실행해본다.  
<br><br>

```
npm run build
```

<br><br>
이 명령어는 배포하기 전에 실행해야 하는 것이다.  
아래와 같은 내용이 출력된다.

<br><br>

```
info  - Linting and checking validity of types
info  - Creating an optimized production build
info  - Compiled successfully
info  - Collecting page data
info  - Generating static pages (5/5)
info  - Finalizing page optimization

Route (pages)                              Size     First Load JS
┌ ● /                                      923 B          82.5 kB
├   └ css/901ad268adf760c9.css             408 B
├   /_app                                  0 B            81.6 kB
├ ○ /[meetupId]                            593 B          82.2 kB
├   └ css/181180052d07d175.css             81 B
├ ○ /404                                   212 B          81.8 kB
└ ○ /new-meetup                            997 B          82.6 kB
    └ css/6b06f286d8345586.css             360 B
+ First Load JS shared by all              82 kB
  ├ chunks/framework-0f397528c01bc177.js   45.7 kB
  ├ chunks/main-e71c796c4d7cc2e4.js        31.8 kB
  ├ chunks/pages/_app-117c1b64f5fa6e8c.js  3.07 kB
  ├ chunks/webpack-2369ea09e775031e.js     1.02 kB
  └ css/555e77c84496ecf7.css               464 B

○  (Static)  automatically rendered as static HTML (uses no initial props)
●  (SSG)     automatically generated as static HTML + JSON (uses getStaticProps)
```

<br><br>
static pages를 5개 생성했다.

```
info  - Generating static pages (5/5)
```

<br><br>

어떤 페이지를 만들었는지도 볼 수 있다.  
슬래시 뒤에 아무것도 없는 루트페이지를 만들었고,  
/[meetupId]는 동적 페이지고 404페이지는 기본값으로 자동으로 만들어진다.  
유요하지 않은 URL을 입력한 경우에 사용한다.  
그리고 /new-meetup페이지도 있다.  
<br><br>

```
Route (pages)                              Size     First Load JS
┌ ● /                                      923 B          82.5 kB
├   └ css/901ad268adf760c9.css             408 B
├   /_app                                  0 B            81.6 kB
├ ○ /[meetupId]                            593 B          82.2 kB
├   └ css/181180052d07d175.css             81 B
├ ○ /404                                   212 B          81.8 kB
└ ○ /new-meetup                            997 B          82.6 kB
    └ css/6b06f286d8345586.css             360 B

```

<br><br>

그리고 페이지 옆에 아이콘들이 있다.  
하나는 채워진 원이고 나머지는 비어있는 원이다.  
밑을 보면 범례가 있는데, 채워진 원은 정적으로 생성된 사이트를 의미하고,  
<br><br>

# SSG 정적 사이트 생성(Static Site Generation)

<br><br>
SSG는 정적 사이트 생성(Static Site Generation)의 약자다.  
HTML로 자동 생성된 것이다.  
JSON은 페이지가 싱글 페이지 애플리케이션으로 전환되면 데이터를 미리 가져오는데 사용된다.  
<br><br>

```
●  (SSG)     automatically generated as static HTML + JSON (uses getStaticProps)

```

<br><br>

<br><br>

# 비어있는 원은 정적 생성

<br><br>

```
○  (Static)  automatically rendered as static HTML (uses no initial props)

```

<br><br>
거의 비슷하지만 유일한 차이점은 초기엔 props가 없다는 것이다.  
따라서 가져온 초기 데이터가 있고 루트 페이지에서만 데이터를 가져온다.  
getStaticProps를 추가한 페이지니까 채워진 원으로 표시되어 있는 것이다.  
<br><br>

```
┌ ● /                                      923 B          82.5 kB
├   └ css/901ad268adf760c9.css             408 B
```

<br><br>
new-meetup페이지에는 아무 데이터도 가져올 필요가 없다.  
거기서는 form을 렌더링할 뿐이다.  
아무 데이터도 필요 없고 서버에서 어떤 데이터도 받아오지 않는다.  
<br><br>
new-meetup>index.js 파일
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
그래서 new-meetup페이지에는 아무 컨텐츠가 없는 정적 페이지로 남아있다.  
[meetupId]페이지는 나중에 SSG페이지로 바꿀 것이다.
하지만 지금은 시작페이지, 즉 루트 페이지만 정적으로 생성된 사이트다.  
<br><br>
