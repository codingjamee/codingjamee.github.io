---
title: "[중요한점]인증 key github에 올리지 않기"
excerpt: "인증 key github에 올리지 않기"
published: true
categories:
  - Blog
tags:
  - [Authentication]

toc: true
toc_sticky: false

date: 2022-11-13
last_modified_at: 2022-11-13
---

<br><br>

# API key 숨기는법

<br><br>
강의에서 firebase로 Auth REST API를 배웠다.  
보호된 리소스로 접근하기 위해서 엔드포인트에 요청을 하는 법을 배웠는데,  
이것은 로그인으로 인증된 idToken을 활용하여 인증된 것을 확인하는 절차가 있다.  
지금은 아마도 API_KEY가 아닌 idToken일 것이다.  
<br><br>

강의 수강 중 작성한 코드를 모두 깃허브에 연동하여 올리고 있는데  
만약 이게 올라가면 모든 레퍼지토리를 삭제해야 할 수도 있다.  
그래서 API key를 숨기는 방법을 찾아 설정해 보았다.

<br><br>
다른 파일을 만든다.  
나는 파일 이름을 apikey.js로 만들었다.  
그 파일에 config라는 객체를 만들었고,  
key명을 apikey로 만들었다.  
그 key의 value에 API key를 입력하였다.  
아래의 예는 내 API key가 아니다.  
반드시 해당하는 API key를 직접 입력하자.  
<br><br>

```jsx
export const config = {
  apikey: "API_key",
};
```

<br><br>

그리고 key를 사용하는 AuthForm.js파일로 import 하였다.  
<br><br>

```jsx
import { config } from "../../apikey";
```

<br><br>
리퀘스트를 보내야 하는 엔드포인트에 api key가 들어가는 자리에 백틱을 활용하여  
import한 config라는 변수의 apikey 라는 key명을 입력하여  
해당 API key가 사용되도록 하였다.  
<br><br>

```jsx
if (isLogin) {
  url = `https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key=${config.apikey}`;
} else {
  url = `https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=${config.apikey}`;
}
```

<br><br>

# gitignore 파일에 해당 파일 추가하기

<br><br>
API key를 다른 파일로 분리하였으면 반드시 잊지말고 해당 파일명을 .gitignore에 추가하도록 하자.

<br><br>

`apikey.js`

<br><br>
라고 말이다.  
이제 이렇게 작업을 하고 나면 git에 코드를 push하여도  
아무런 문제가 없다.
