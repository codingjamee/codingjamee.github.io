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

# getStaticProps를 사용할 때 생길 수 있는 문제

<br><br>

## 1. 이 데이터에 최신 정보는 없을 수 있다는 것.

<br><br>
이 페이지는 빌드 프로세스에서 생성된다. 그러고 배포한다.  
데이터베이스에 더 많은 모임 정보를 추가해도  
사전에 생성된 페이지는 그걸 모를 것이다.  
클라이언트 쪽에서 데이터를 가져오지 않는다면 항상 예전 모임만 보게 될 것이다.  
<br><br>

### 해결 1. 데이터 변경시 빌드해서 재배포

<br><br>
이 문제를 해결하기 위해 데이터가 변할 때마다 빌드해서 재배포하는 방법도 있다.  
개인 블로그같은 웹사이트에서는 괜찮은 방법이다.  
데이터가 자주 바뀌지 않기 때문이다.  
<br><br>

### 해결 2. 반환된 객체에 프로퍼티 추가

<br><br>

그러나 데이터가 자주 변한다면 반환된 객체에 프로퍼티를 하나 추가하는 방식도 있다.  
revalidate 프로퍼티다.

<br><br>
getStaticProps에서 반환된 객체에 이 프로퍼티를 추가하면 점진적 정적 생성이라는  
기능을 사용할 수 있다.  
revalidate에는 숫자가 필요하는데, 10이라고 하면 이 숫자는 요청이 들어올 때  
이 페이지를 다시 생성할 때까지 NextJS가 대기하는 시간을 초단위로 표기한 것이다.

<br><br>
즉, revalidate에 숫자가 설정되어있으면 페이지는 빌드프로세스중에 바로 생성되지 않는다.  
생성은 되지만 바로는 아니다. 적어도 페이지에 요청이 있다면 서버에서 몇 초 간격으로  
생성될 것이다.  
<br><br>

```jsx
const HomePage = (props) => {
  return <MeetupList meetups={props.meetups} />;
};

export const getStaticProps = () => {
  //fetch data from an API
  return {
    props: {
      meetups: DUMMY_MEETUPS,
    },
    revalidate: 10,
  };
};
export default HomePage;
```

<br><br>
이렇게 revalidate에 설정이 되어있으면,  
적어도 10초마다 서버에서 페이지를 다시 생성한다는 의미다.  
다시 만들어진 페이지들은 사전에 생성했던 오래된 페이지를 대체한다.  
이렇게 하면 데이터가 절대 10초보다 오래되지 않을 것이다.  
<br><br>
여기서 사용하는 숫자는 데이터 업데이트 빈도에 따라 결정하면 된다.  
데이터가 한 시간마다 변하는 경우 3600으로 설정하면 될 것이다.  
항상변하면 1로 하면 될 것이다.
<br><br>
이 페이지는 배포후 서버에서 때때로 다시 사전 생성할 것이다.  
그러니 일부 데이터가 변경됐다고 해서 매번 다시 빌드하고 배포할 필요가 없다.  
<br><br>

## getStaticProps를 사용해 서버측 렌더링(SSR) 탐색하기

<br><br>
getStaticProps는 페이지 컴포넌트에서 export해서  
데이터를 포함하고 있는 프리렌더 페이지를 기다려야 한다.  
<br><br>
revalidate로는 페이지가 배열 다음에 규칙적으로 업데이트 되게 할 수 있다.  
하지만 주기적 업데이트로는 부족할 수 있다.  
<br><br>

## 요청이 있을 때마다 페이지 재생성 하기

<br><br>
요청이 들어올 때마다 페이지를 다시 만들어야 할 때가 있다.  
따라서 페이지를 동적으로 사전 생성(pre-generate)해야 한다.  
서버에 배포한 후 즉석에서 말이다.  
빌드프로세스 중도 아니고 매초마다도 아니라, 요청이 있을 때만이다.  
getStaticProps의 대안이 있다.  
<br><br>

### getServerSideProps 함수

<br><br>
getStaticProps처럼 지정된 이름이며, NextJS가 찾을 것이다.  
getStaticProps와 차이점은 getServerSideProps는 빌드프로세스 중에 실행되지 않는다.  
하지만 대신 배포 다음에 서버에서 실행될 것이다.  
<br><br>

```jsx
export const getServerSideProps = async () => {};
```

<br><br>
여기에 객체를 return한다. 비슷하게 객체 안에 props 프로퍼티도 있다.
<br><br>

```jsx
export const getServerSideProps = async () => {
  //fetch data from an API
  return {
    props: {},
  };
};
```

<br><br>
페이지 컴포넌트에서 프랍을 받기 때문이다.  
그리고 여전히 API에서 데이터를 fetch한다.  
어떤 코드를 쓰더라도 서버에서 실행될 것이다.  
클라이언트에서는 절대 실행되지 않는다.  
따라서 여기서 서버사이드 코드를 실행할 수 있다.  
<br><br>
또한 사용자에게 노출되어서는 안 되는 자격 증명을 사용하는 작업을 수행할 수도 있다.  
서버에서만 오로지 실행되기 때문이다.  
그리고 프랍 객체를 리턴한다.  
객체에는 meetups key가 있고, 값에는 DUMMY_MEETUPS가 저장된다.  
<br><br>

```jsx
export const getServerSideProps = async () => {
  return {
    props: {
      meetups: DUMMY_MEETUPS,
    },
  };
};
```

<br><br>
여기에는 revalidate를 설정할 수는 없다.  
이 getServerSideProps함수는 요청이 들어올 때마다 실행되기 때문이다.  
따라서 시간을 지정해서 revalidate할 필요가 없다.  
<br><br>
그리고 해야할 일은 context 매개변수를 지정한다.
<br><br>

```jsx
export const getServerSideProps = async (context) => {

```

<br><br>
이 context 매개변수를 통해 request 객체에 접근할 수 있다.  
req키로 request객체에 접근할 수 있고,  
res키로 response객체에 접근할 수 있다.  
이를 변수에 각각 할당한다.  
<br><br>

```jsx
export const getServerSideProps = async (context) => {
  const req = context.req;
  const res = context.res;
  return {
    props: {
      meetups: DUMMY_MEETUPS,
    },
  };
};
```

<br><br>
만약 전에 노드 JS와 익스프레스로 작업한 적이 있다면 익숙할 것이다.  
거기에는 request객체와 response객체가 있고, 미들웨어에서 함께 작업한다.  
그리고 구체적인 요청 객체에 접근하는건 도움이 될 수 있다.  
예를들어 인증작업을 할 때나 세션쿠키를 확인할 때나 그런 때 말이다.  
이건 NextJS를 모두 다루는 코스에서 확인할 수 있을 것이다.  
<br><br>
들어오는 요청에 접근하고 헤더와 필요하다면 요청바디에도 접근한다.  
그럼 추가 데이터나 정보를 줄 것이다.  
getServerSideProps에서 실행되는 코드를 위해 필요한 정보다.

<br><br>
해당 응답 객체에서 작업하여 응답을 반환하지 않고  
대신 props키로 객체를 리턴한다.  
이 props 키가 페이지 컴포넌트 함수 프랍을 저장하고 있다.  
이렇게 getServerSideProps를 이용할 수 있다.  
페이지를 위한 데이터를 준비하는 것이다.  
<br><br>
그리고 getServerSideProps를 사용해서 다 저장한 뒤에 시작페이지를 리로드하면  
작동하는 것을 볼 수 있다.  
페이지 소스를 확인해보면 데이터가 존재한다.  
이제는 요청이 들어올 때마다 프리 제너레이트 될 것이다.  
<br><br>

## 둘 중 어느것을 선택할 것인가?

<br><br>

### getServerSideProps vs getStaticProps

<br><br>
모든 요청을 실행하므로 getServerSideProps가 좋아보일 수도 있겠다.  
하지만 단점이 될 수도 있다.  
요청이 들어오기 전까지 페이지가 만들어지기 기다려야 한다는 뜻이다.  
항상 바뀌는 데이터가 없다면, 매초 여러번 바뀔 것이다.  
<br><br>

## Authentication과 같이 요청객체에 접속할 필요가 없다면 getStaticProps

<br><br>
여기서는 HTML파일을 프리 제너레이트 하기 때문이다.  
그 파일은 CDN에 저장되고 서브 될 것이다.  
그리고 요청이 들어올 때마다 데이터를 다시 만들고 fetch하는 것보다 빠르다.  
<br><br>
그러므로 우리 페이지는 getStaticProps일 때 더 빠를 것이다.  
항상 다시 만드는 대신 캐시하고 다시 사용하니까 말이다.  
<br><br>

## 구체적 request 객체에 접속해야 한다면 getServerSideProps

<br><br>

구체적 요청 객체에 접속해야 한다면 getServerSideProps를 사용해야 한다.
getStaticProps에서는 요청과 응답에 접속하지 않기 때문이다.  
<br><br>

## 매초 여러번 바뀌는 데이터를 가지고 있다면 getServerSideProps

<br><br>

revalidate도 도움이 되지 않을 것이기 때문이다.  
그리고 meetup list에서 좋은 선택이 아닌데, 주기적으로 바뀌는 데이터가 아니기 때문이다.  
들어오는 request에 작업할 필요도 없다.
<br><br>

## 캐싱을 사용하기 위해 getStaticProps

<br><br>
그리고 getStaticProps로는 페이지를 불필요하게 여러번 프리제너레이트 할 필요도 없다.

<br><br>
<br><br>
