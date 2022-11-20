---
title: "#350[udemy react 강의노트]SSG데이터를 가지고 오기 위한 Params작업하기 "
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

# MeetupDetails페이지 수정

<br><br>

new-meetup페이지에서는 getServerSideProps나 getStaticProps가 필요 없다.  
데이터가 없기 때문이다.
getServerSideProps나 getStaticProps는 프리제너레이트 페이지에  
데이터를 fetch하는 일을 한다.  
하지만 MeetupDetails페이지에서는 데이터가 필요하다.  
<br><br>
지금은 하드코드 데이터지만 곧 바뀔 것이다.  
그리고 MeetupDetails에서 getServerSideProps나 getStaticProps을 이용한다.  
데이터가 얼마나 자주 바뀌는가에 따라, 요청 객체에 접속하는지에따라  
무엇을 사용할지 결정된다.  
<br><br>
Meetup 데이터는 자주 바뀌지 않는걸로 가정한다.  
그리고 이 앱은 Meetup 데이터를 바꾸는 특징도 가지고 있지 않다. Meetups만 추가한다.  
하지만 특징을 바꾸더라도 Meetup이 매초 여러번 바뀌지는 않을 것이다.  
그러므로 MeetupDetails페이지에서는 getStaticProps가 더 나을 것이다.  
<br><br>
이제 getStaticProps를 작성하고 export 한다음 async하여준다.  
그리고 SingleMeetup에 데이터를 패치한다.  
그리고 props와 함께 return 한다. props에는 meetupData프랍을 입력하고  
중첩 객체 안에 image, title, address, description키를 설정해 값을 입력해 준다.
<br><br>

```jsx
export const getStaticProps = async () => {
  return {
    props: {
      meetupData: {
        image:
          "https://cdn.pixabay.com/photo/2018/01/21/01/46/architecture-3095716_960_720.jpg",
        id: "m1",
        title: "First Meetup",
        address: "Some Street 5, Some City",
        description: "The meetup description",
      },
    },
  };
};
```

<br><br>
이건 컴포넌트 함수에 보낸 프랍 데이터가 될 수 있다.  
문제가 있을 수 있다.
<br><br>

## 이 페이지는 동적 페이지다.

<br><br>
따라서 API에 가서 싱글미트업에 데이터를 fetch할 때  
meetup을 확인할 방법이 필요하다. 예를들어 id같은 것이 필요하다.  
ID가 URL에 인코드 되어있다.  
<br><br>
useRouter훅을 사용하여 라우터 객체에 접속하고 쿼리 프로퍼티를 사용한다.  
useRouter라는 컴포넌트 함수에서만 쓸 수 있는 것이 문제다.  
getStaticProps에서는 안된다. 여기서는 리액트 훅을 사용할 수 없다.  
useRouter를 사용해서 URL을 얻을 수 없다.

<br><br>
이럴 때 context를 사용하면 된다.  
context가 getServerSideProps에만 있는 것이 아니라 getStaticProps에도 있다.  
getStaticProps를 이용할 때 콘텍스트는 요청과 응답을 저장하지 않을 것이다.  
하지만 매개변수가 있다.

<br><br>
context.params를 사용하면 된다. 이것이 ID객체가 될 것이다.  
그리고 대괄호 사이에 있는 건 프로퍼티가 될 것이다.  
값은 URL에 인코드 되어 있다.  
이것을 meetupId에 할당하면 context.params.meetupId에 접속할 수 있다.

<br><br>

```jsx
export const getStaticProps = async (context) => {
  const meetupId = context.params.meetupId;
```

<br><br>
meetupId는 대괄호 사이에 가지고 있는 ID다.  
그리고 이게 구체적인 meetupId가 될 것이다.  
이 meetup을 표시한다.  
따라서 meetupId로 ID를 설정한다.  
컴포넌트 함수에 노출하고 싶다면 말이다.  
<br><br>

```jsx
export const getStaticProps = async (context) => {
  const meetupId = context.params.meetupId;
  return {
    props: {
      meetupData: {
        image:
          "https://cdn.pixabay.com/photo/2018/01/21/01/46/architecture-3095716_960_720.jpg",
        id: meetupId,
        title: "First Meetup",
        address: "Some Street 5, Some City",
        description: "The meetup description",
      },
    },
  };
};
```

여기까지 하고 npm run dev를 하고 디테일 페이지에 가보면 에러가 난다.  
<br><br>

```
Error: getStaticPaths is required for dynamic SSC pages and is missing for '/[meetupId]'.
```

<br><br>
라는 에러다.
getStaticPaths란 무엇일까?
<br><br>

# getStaticPaths란

<br><br>
getStaticPaths는 NextJS가 이해할 수 있는 또다른 함수다.  
getStaticProps와 getServerSideProps처럼 말이다.  
우리는 페이지 컴포넌트 파일에서 getStaticPaths 함수를 export해야한다.  
만약 동적 페이지라면 getStaticProps를 이용한다.  
<br><br>
만약 getServerSideProps를 이용하는게 아니고 getStaticPaths도 이용하지 않는다면  
getStaticProps를 이용해야 한다.  
이제 다른 곳에서 getStaticPaths도 export 해야 한다. async await 도 사용할 수 있다.  
<br><br>
getStaticPaths를 이해하기 위해, getStaticProps를 다시 생각해 봐야 한다.  
getStaticProps를 사용해 페이지는 빌드 프로세스 중에 프리제너레이트 되었다.  
지원되는 모든 ID에서 말이다.  
<br><br>
동적이기 때문에 NextJS는 어떤 ID밸류가 프리제너레이트 페이지가 되어야 하는지 알아야 한다.  
프리제너레이트 페이지가 아니면 어떻게 될까? ID는 여기있는 URL에서 얻을 수 있다.

<br><br>

```jsx
export const getStaticProps = async (context) => {
  const meetupId = context.params.meetupId;
  console.log(meetupId);
```

<br><br>
하지만 사용자가 URL의 특정 밸류로 페이지에 방문했을 때 프리제너레이트 되는 게 아니다.  
빌드 프로세스에서 되는거다. 모든 URL, 모든 meetupId밸류에서 프리제너레이트 해야 한다.
만약 프리 제너레이트 하지 않은 페이지인 ID로 입장하면 404에러를 보게 될 것이다.  
하지만 그런식으로 작동하기 때문에 getStaticPaths를 추가해야 한다.  
<br><br>
getStaticPaths는 객체를 리턴하는 일을 한다. 객체는 모든 동적 세그먼트 밸류가 있다.  
지금의 경우 모든 meetupId가 될 것이다. 그 페이지는 프리 제너레이트 되어야 한다.  
이 객체에는 path키가 있어야 한다. 밸류는 array다. 거기엔 객체가 또 여러개 있어야 한다.  
동적 페이지 버전당 객체가 하나여야 한다.  
<br><br>

```jsx
export const getStaticPaths = async () => {
  return {
    paths: [{}],
  };
};
```

<br><br>
이 객체는 params 키를 가진다. 꼭 필요한 것이고, 모든 키밸류 쌍을 가진 객체인데  
동적 페이지로 이끌 것이다.  
따라서 여러 동적 페이지 세그먼트가 있다면 중첩 객체에 키가 여러 개 있을 것이다.  
여기서는 싱글 다이내믹 세그먼트로 meetupId만 있기때문에

<br><br>

```jsx
export const getStaticPaths = async () => {
  return {
    paths: [{ params: {} }],
  };
};
```

    여기 params객체에 meetupId키를 추가하기로 한다. 그리고 구체적 값을 입력한다.
    meetupId를 위해 이페이지는 프리제너레이트 되어야 한다.

<br><br>

```jsx
export const getStaticPaths = async () => {
  return {
    paths: [
      {
        params: {
          meetupId: "m1",
        },
      },
    ],
  };
};
```

<br><br>
그리고 value가 여러 개라면 (m1, m2처럼) 아래와 같이 두개의 params객체를 가져야 하고  
meetupId에 m2를 입력해야 한다.  
<br><br>

```jsx
export const getStaticPaths = async () => {
  return {
    paths: [
      {
        params: {
          meetupId: "m1",
        },
      },
      {
        params: {
          meetupId: "m2",
        },
      },
    ],
  };
};
```

<br><br>
현실에서는 params의 meetupId를 하드코드하진 않을 것이다.  
하지만 데이터베이스에서 지원되는 ID를 패치하거나 API에서 패치하고  
배열을 동적으로 만들어야 한다.  
이렇게 getStaticPaths를 위의 코드를 써서 추가하고 파일을 저장하였고,  
getStaticProps가 있고 이 상태로 리로드 하면 더이상 getStaticPaths에러는 나오지 않는다.  
그대신 폴백에 관한 다른 에러가 떴다.

<br><br>

## 또다른 에러, "The `'fallback'` key must be returned from getStaticPaths in /[meetuId]

<br><br>
이 에러를 없애려면 다른 설정을 해야 한다. 리턴 객체안의 paths이전에 뭔가를 추가해야 한다.  
fallback키다. 이 키가 NextJS에게 paths배열이 모든 지원되는 매개변수를 저장할지  
아니면 일부만 저장해줄지 말해준다.  
fallback에 false라고 설정하면 paths에게 모든 지원되는 meetupId 밸류를  
포함하라고 하는것이다.  
즉 사용자가 지원하지 않는 걸 입력하면(예를들어 m3같은것) 404에러가 뜰 것이다.  
반면 fallback을 true라고 설정하면 NextJS가 페이지를 만들 것이다.  
<br><br>

```jsx
export const getStaticPaths = async () => {
  return {
    fallback: false,
    paths: [
      {
        params: {
          meetupId: "m1",
        },
      },
      {
        params: {
          meetupId: "m2",
        },
      },
    ],
  };
};
```

<br><br>
들어오는 요청에 관해서 서버에서 meetupId로 동적으로 만들 것이다. 폴백은 훌륭한 도구다.  
왜냐하면 특정 미트 업 ID 밸류에 관해서 페이지 중 일부를 프리 제너레이트하기 때문이다.  
예를 들어, 주기적으로 방문 되는 페이지에서 요청이 들어올 때 잃어버린 부분을  
동적으로 프리 제너레이트한다.  
<br><br>
여기서는 폴백을 false로 설정할 것이다. 지정되는 모든 paths를 정의하기 위해서.  
폴백에 관해 더 알고 싶다면, 전체 코스에서 자세하게 다룰 것이다.  
요약한다면 모든 paths가 아니라 특정 paths를 정의한다는 것이다.  
만약 수백 개의 페이지가 있는데 그 모든 걸 프리 제너레이트하고 싶지 않고  
인기 있는 몇 개만 하고 싶을 수 있으니까 말이다.

<br><br>
이제 폴백을 추가했으니까 저장하고 리로드하면 첫 번째 미트 업을 성공적으로 로드했다.
m2를 입력해도 성공한다. 그런데 m3를 입력하면 404페이지가 나온다.  
폴백을 false로 설정했기 때문이다. m3는 지원되는 매개변수가 아닌 것이다.
<br><br>
getStaticPaths는 또 다른 중요한 함수다. 동적 페이지에서 필요하고,  
넥스트 JS에게 어떤 동적 매개변수 밸류의 어떤 페이지가 프리 제너레이트되어야 하는지 말해준다.  
getStaticProps는 모든 페이지를 실행한다.  
따라서 모든 미트 업 ID 밸류가 데이터를 패치하고 미트 업의 프랍을 리턴하도록 한다.  
<br><br>
그리고, 여기서 미트 업 ID를 console.log했는데 터미널에서 볼 수 있다.  
개발자 도구에서도, 브라우저에서도, 콘솔에서도 볼 수 없다.  
이건 코드기 때문이다. getStaticProps 대신 오는 코드고 빌드 타임에 실행되는 것이다.  
그리고 개발 서버에서 돌아갈 때 모든 들어오는 요청이 있을 때마다 실행된다.  
<br><br>
하지만 개발자 서버 사이드에서만 실행된다.  
따라서 브라우저에서는 볼 수 없고 개발자 서버의 터미널에서만 볼 수 있는 것이다.
이런 식으로 getStaticProps가 동적 페이지에서 작동하고 getStaticPaths가 작동한다.

<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
