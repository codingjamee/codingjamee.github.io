---
title: "#331[udemy react 강의노트]NextJS 풀스택앱 빌드하기"
excerpt: "NextJS"
published: true
categories:
  - Blog
tags:
  - [Authentication]

toc: true
toc_sticky: false

date: 2022-11-15
last_modified_at: 2022-11-15
---

<br><br>
<br><br>

# NextJS 프로젝트 시작하기

<br><br>
`npx crate-next-app`  
or  
`yarn create next-app`
<br><br>
nextJS에서는 create-react-app과 달리 public 폴더에 index.html이 없다.  
nextJS는 사전 렌더링 기능을 내장하기 때문이다.  
NextJS는 싱글페이지 애플리케이션을 제공하여  
서버에 요청이 오면 싱글페이지에 동적으로 사전 렌더링을 거쳐 콘텐츠를 포함한  
초기 페이지를 보여주게 된다.  
<br><br>
여기서 pages 폴더가 가장 중요하다  
여기서 파일기반 라우팅을 설정하고 애플리케이션을 설정할 여러 페이지를 정의하기 때문이다.  
<br><br>

# 첫 페이지 시작하기

<br><br>
이제 NextJS를 이용해서 세 개의 페이지를 만들어보자.  
시작페이지, 뉴스 목록페이지, 뉴스 세부정보 페이지이다.  
그러려면 NextJS의 pages폴더에서 세 개의 파일을 만든다.  
<br><br>
첫번째 파일은 index.js파일이다. 도메인 요청이 '/'까지만 되어있을 때  
index.js파일을 읽어들이도록 할 것이다.  
index라는 파일 명은 중요한데 /뒤에 아무것도 없을 때 불러오게 되지만,  
다른 파일이름은 그것이 바로 경로 이름으로 사용된다.  
내가 news.js파일을 만들었는데 그러면 /news라는 도메인 요청이 있을 때  
해당 파일을 읽어들이게 된다.  
<br><br>
index.js파일과 news.js파일에 들어가야 할 것은 기본적인 React컴포넌트다.  
해당 페이지에서 불러들여야 하는 React 컴포넌트다.  
<br><br>
React앱에서 하듯이 그대로 함수형 컴포넌트를 사용하고 export를하면 된다.  
그런데 여기서 빠진 부분은 react에서 import하는 부분이다.  
NextJS프로젝트는 이미 최신 React 설정을 지원해서 생략 가능하다.  
<br><br>
이제 `npm run dev`를 하면  
페이지가 나오는데, 거기서 page source를 보면  
지난번처럼 비어있는 껍데기 HTML 코드가 아닌, 모든 코드가 다 보이는 것을 알 수 있다.  
파일기반 라우팅외에도 특별한 추가 설정없이 내장된 서버사이드 렌더링 기능도  
이미 사용된다. 기본으로 제공되고 있는 것이다.  
이제 이것으로 무엇을 할 수 있을까?  
<br><br>

# 파일 기반 라우팅 시스템

<br><br>
파일이나 폴더 구성에 대해 알아야 할 방법이 있다.  
index.js파일은 루트페이지, 즉 /까지만 있는 페이지를 불러온다.  
index.js파일이 pages 폴더 바로 하위에 있기 때문이다.  
반면, news.js파일은 /news일때 불러온다.  
이렇게 index파일과 달리 따로 이름 붙인 파일을 대체하는  
다른 방법이 있다.

<br><br>
pages폴더 안에 news라는 새 하위 폴더를 만들고  
news.js파일을 옮겨, 해당 파일명을 index.js로 바꾼다.  
그래도 똑같이 our-domain.com/news에서 news.js였던 파일을  
불러오는 것을 확인할 수 있다.  
<br><br>
pages폴더 안에 만든 폴더도 경로 세그먼트에 들어간다.  
루트 페이지의 news.js로 쓸지 news폴더의 index.js로 쓸지는  
선택의 자유다.  
<br><br>

## 중첩 경로 만들기

<br><br>
중첩 경로를 만들기 시작하면 문제가 될 수 있다.  
`/news/something-important`
경로는 특정 뉴스 아이템에 대한 식별자다.  
그러려면 현재 폴더 하위에 하나의 파일을 또 만들어야 한다.  
그렇지 않으면 중첩 경로를 설정할 수 없을 것이다.  
<br><br>
pages폴더 바로 아래에 파일을 만들면 파일 이름을 딴 세그먼트 하나만 생길 것이다.  
이제 pages폴더 하위에 news폴더 그 하위에 index.js와 형제파일인  
something-important.js파일을 만든다.

<br><br>
그렇게 되면 /news뒤에 아무것도 붙어있지 않으면 index.js파일의 컴포넌트를 불러오고  
/news/something-important가 있다면 something-important.js파일의  
컴포넌트를 불러오게 될 것이다.
<br><br>

<br><br>
<br><br>

# 동적 페이지 (매개변수 포함) 만들기

<br><br>
news폴더 하위에 something-important폴더를 만들어  
그 안에 index.js파일을 생성할 수 있다.  
문제는 없지만 news 사이트에 하나 이상의 뉴스 항목을 넣으려면 문제가 생길 것이다.  
<br><br>
현실적으로는 news 폴더의 index.js에서 뉴스 항목의 목록을 출력해야 한다.  
뉴스 항목들을 선택하면 세부 페이지로 들어가서  
해당 뉴스의 구체적인 내용을 보여줘야 할 것이다.  
여러 뉴스 항목과 컨텐츠에 대해 같은 페이지를 반복해서 사용할 것이다.  
사용자가 세부 페이지로 들어갈 때 데이터베이스에서  
구체적인 컨텐츠를 받아와서 화면에 띄워야 한다.  
<br><br>
그래서 기술적으로는 같은 컴포넌트지만 콘텐츠는 다른 거다. 현실적으로 흔한 상황이다.  
그래서 하드코딩, 즉 식별자에 something-important라고 파일이름을  
지정하는 것은 현실적이지 않다.  
대신 동적 페이지라는 것을 만들어야 한다.  
거기서는 경로 세그먼트, 즉 경로에 있는 구체적 값이 동적이다.  
<br><br>
/news 다음에 다양한 뉴스 아이템의 식별자 경로가 있을 수 있다.  
항상 새로운 이름을 추가해야 한다.  
하지만 여기 구체적인 값이 무엇이든 항상 같은 페이지를 불러올 것이다.  
하지만 페이지 내부에서는 해당 경로의 값에 접근해서  
올바른 데이터를 가져올 수 있다.  
이런것들을 동적 경로로 구현할 수 있다.  
<br><br>
그러려면 아까의 news 하위 파일 중 something-important.js파일의 이름을  
다른 이름으로 바꿔야 하는데, NextJS에서 사용하는 특수 문법을 적용하자.  
<br><br>
<br><br>

# 1. `[ ]` 파일명에 대괄호 붙이기

<br><br>
확장자 앞에` [ ]`를 사용한다. 이렇게 하면 NextJS는 이것을  
동적 페이지로 인식해 경로에 여러 값을 불러온다.  
이 괄호 안에 식별자를 추가하면 되는데, 식별자 이름은 무엇이든 상관없다.  
예를들어 newsId라고 해보자.  
<br><br>

## `[newsId].js`

<br><br>
이러면 NextJS는 이 페이지를 여러 다른 값으로 불러온다.  
news/something으로 해도, 어떤것을 붙여도 같은 DetailPage가 렌더링 된다.  
이렇게 NextJS를 사용하여 동적이고 유연한 웹사이트를 구축할 수 있다.  
사용자가 페이지에 접속했을 때 컴포넌트 내에서 어떻게 입력된 경로값을 추출하여  
올바른 뉴스 항목을 가져올 수 있을까?  
<br><br>

# 2. URL에 입력된 구체적 값 추출하기

<br><br>
NextJS에서 제공하는 훅을 사용하자. import를 하는데 next/router에서 한다.  
이것은 next패키지의 하위 패키지다. 특정 라우팅 기능을 사용할 수 있다.  
거기서 가져온 useRouter 훅을 사용한다.
<br><br>

```
import {useRouter} from 'next/router'
```

<br><br>
기본 React훅인데 React에 내장된 것은 아니고 Next에서 만들어서 흔히 쓰이는 훅이다.  
DetailPage훅에서 useRouter를 호출할 수 있게 된다.  
이러면 라우터 객체에 접근하고 그 라우터 객체에서  
특정 데이터나 호출할 수 있는 특정 메서드를 얻을 것이다.

<br><br>

```jsx
const DetailPage = () => {
  const router = useRouter();
```

<br><br>
여기서 URL에 인코딩된 값, 즉 이 동적 경로 세그먼트의 구체적 값에 접근할 수 있다.  
router.query라는 메서드로 접근하면 중첩 객체에 접근할 수 있게 된다.  
query 객체에서 대괄호 안에 썼던 식별자를 속성 이름으로 넣으면 된다.  
<br><br>

```jsx
import { useRouter } from "next/router";

const DetailPage = () => {
  const router = useRouter();

  router.query.newsId;
  return <h1>Detail Page</h1>;
};
export default DetailPage;
```

<br><br>
파일 명에 newsId라고 대괄호 안에 썼으므로 위와 같이 router.query.newsId라고 쓴다.  
그러면 이 페이지에 접속할 수 있었떤 동적 세그먼트의 URL에 있는 구체적 값이 저장된다.

<br><br>
router.query.newsId를 console.log해서 무엇이 있는지 한번 보면,  
처음엔 undefined와 그다음엔 news다음에 내가 임의로 입력한 경로를 확인할 수 있다.  
지금의 경우 something-i가 나왔다.

<br><br>
두개의 로그가 보이는 것은 라우터가 작동하는 방식 때문이다.  
라우터는 페이지를 처음 렌더링할 때 즉시 실행되는데 이시점에서는 URL이 무엇인지 아직 모른다.  
하지만 해당 정보가 들어오면 컴포넌트는 다시 렌더링하고  
구체적 값을 얻는다. 훅은 이런 방식으로 작동한다.  
<br><br>
이렇게 URL에 인코딩된 구체적인 값을 얻을 수 있다.  
이것을 newsId에 할당하여 뉴스를 가져올 수 있는 데이터 베이스나 백엔드 API가 있다면  
백엔드에 API요청을 보내서 newsId에 해당하는 뉴스 항목을 가져올 수 있다.
이제 이 페이지를 방문할 때 사용한 id를 기반으로 데이터베이스에서  
해당하는 뉴스 항목을 가져올 수 있다.  
<br><br>

# 페이지간 연결하기

<br><br>
news폴더의 index.js로 돌아와서 탐색을 알아보도록 하자.  
index.js에서 탐색을 위해 news아이템의 목록을 띄워야 한다.  
ul 태그안에 li태그 여러개를 넣고 그 사이에는 목록을 넣는다.  
이것은 클릭할 수 있어야 하고 클릭하면 newsId 페이지로 이동해야 하는데,  
이 페이지는 각각 ID즉, URL에 인코딩 된 구체적 값이 다른 페이지가 될 것이다.  
<br><br>
a태그로 해서 href 속성에 URL을 연결해주면 되겠지만,  
그렇게 하면 싱글페이지 앱이 될 수 없다.  
지금은 사용자가 탐색할 때마다 새요청을 백엔드에 보내고 새로운 HTML페이지를 받는다.  
<br><br>
싱글페이지 애플리케이션을 유지하면  
페이지 전체에 걸쳐 상태를 보존하면서 더 빠르게 반응하여  
사용자들이 만족할 수 있을 것이다.  
이런점 때문에 React를 사용하는 것이다.  
대화형interactive UI를 구축해서 새 HTML 페이지를 요청할 필요가 없고,  
자바스크립트와 React를 사용해 화면을 업데이트하기만 하면 되는 것이다.

<br><br>
위와같이 a태그로 link를 연결하면 새로운 HTML페이지를 가져오게 될 것이고,  
그러면 저장했던 상태들이 없어질 수 있다.  
리덕스 상태, 컨텍스트 상태등 말이다.

<br><br>

## next/link에서 import한 Link 컴포넌트 사용하기

<br><br>
next/link에서 Link를 import한다.  
<br><br>

```jsx
import Link from "next/link";
```

<br><br>
그리고 href속성으로 해당 내용을 보여줄 경로를 입력한다.  
<br><br>

```jsx
import Link from "next/link";
const NewsPage = () => {
  return (
    <>
      <h1>The News Page</h1>
      <ul>
        <Link href="/news/nextjs-is-a-great-framework">
          NextJS is A Great Framework
        </Link>
      </ul>
    </>
  );
};
export default NewsPage;
```

<br><br>
이렇게 하면 앵커요소도 기본적으로 렌더링할 것이다.  
클릭해보면 새로고침되지 않고(HTML페이지를 새로 가져오지 않고) 이동한다.
<br><br>
특수 컴포넌트 Link가 앵커태그를 렌더링하고 이 앵커태그 클릭을 감지해서  
클릭하면 브라우저가 기본 동작으로 새 HTML페이지를 받는 요청을 보내지 못하도록 한다.  
대신 불러올 컴포넌트를 읽어들이고 URL을 변경하여 페이지가 바뀐 것처럼 보이게 한다.  
실제로는 싱글페이지 애플리케이션에 있다.  
<br><br>
