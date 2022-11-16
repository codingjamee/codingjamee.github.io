---
title: "#322[udemy react 강의노트]Authentication 실습 세번째"
excerpt: "Authentication react Application NavigationGard"
published: true
categories:
  - Blog
tags:
  - [Authentication]

toc: true
toc_sticky: false

date: 2022-11-14
last_modified_at: 2022-11-14
---

<br><br>

# 로그인 하지 않은 유저와 로그인 한 유저에게 선택적으로 렌더링하기

<br><br>
문제는 비밀번호를 변경하는 페이지에서 로그아웃을 하더라도  
프로필페이지를 벗어나지 않고 있고,  
프로필페이지의 url인 /profile로 직접 입력해도 그 장소로 들어갈 수 있는 것이다.  
만약 그 화면에서 비밀번호를 변경하여도 실패하게 되기 때문에  
그렇게 큰 문제는 아닐 수 있다.  
유효하지 않은 토큰, 정확히는 null을 제공했기 때문이다.  
그래서 피해갈 일은 없지만, 로그인 상태가 아닐 땐 이 페이지를 방문할 수 없게 하고 싶다.
<br><br>
<br><br>

# 네비게이션 가드

<br><br>
로그인 여부에 따라 라우트 설정을 동적으로 바꾸는 기능이다.  
App.js파일에 정의된 라우트만 방문할 수 있다.  
<br><br>
특정 조건에 맞춰 이 라우트들을 렌더링한다면  
조건이 참일 때만 특정 라우트를 방문할 수 있게 될 것이다.  
사용자의 현재 인증 상태를 특정 라우트의 조건으로 삼을 수 있다는 것이다.

<br><br>
사용자 미인증시엔 이 UserProfile 라우트 설정을 아예 렌더링하지 않을 수도 있다.  
그러면 미인증 사용자는 프로필 페이지에 방문할 수 없다.

<br><br>
사용자가 로그인했는지 안했는지 알기 위해 App.js 파일에서 useContext를 import한다.  
그리고 사용자가 로그인하지 않았다면 /profile은 렌더링되면 안된다.  
로그인 여부에따라 선택적으로 렌더링하게 만든다.  
<br><br>

```jsx
{
  authCtx.isLoggedIn && (
    <Route path="/profile">
      <UserProfile />
    </Route>
  );
}
```

<br><br>
로그인하지 않은 사용자에게만 AuthPage 접근을 허가해야 한다.  
로그인 상태라면 인증 페이지를 방문하는 의미가 없다.

<br><br>

```jsx
{
  !authCtx.isLoggedIn && (
    <Route path="/auth">
      <AuthPage />
    </Route>
  );
}
```

<br><br>
다음으로, 사용자가 임의로 /profile로 접속했을 때  
비밀번호 변경 화면이 렌더링되지 않도록 설정해야 할 것이다.  
또는 유효하지 않은 경로로 접속할 경우를 처리해야 한다.  
<br><br>
마지막 Route 컴포넌트를 사용해 와일드카드를 포함한다.  
사용자가 어떤 것을 입력하였든지 위의 라우트로 처리되지 않았을 시  
404페이지를 렌더링하거나, 사용자를 리다이렉트 할 수 있다.  
아래의 예는 사용자를 홈화면으로 리다이렉트 하는 방법이다.  
<br><br>

```jsx
<Route path="*">
  <Redirect to="/" />
</Route>
```

<br><br>
이제 위의 라우트는 사용자가 유효하지 않은 경로로 접속하거나  
로그인 없이 /profile에 접속할 때마다 작동할 것이다.  
이렇게 라우트를 보호할 수 있다.  
<br><br>
혹은 /profile이 언제나 렌더링되게 만들고,  
UserProfile 컴포넌트가 선택적으로 렌더링 될 수 있게 만들 수 있다.  
만약 로그인 한 상태라면 UserProfile 컴포넌트가 렌더링 되고,  
로그인하지 않은 상태라면 /auth로 가게 만들 수 있다.
<br><br>
<br><br>

```jsx
<Route path="/profile">
  {authCtx.isLoggedIn && <UserProfile />}
  {!authCtx.isLoggedIn && <Redirect to="/auth" />}
</Route>
```

<br><br>

# 새로고침 했을 때 인증상태 잃지 않게 유지하기

<br><br>
새로고침 하면 리액트 앱이 다시 시작이 됨으로써  
모든 인증상태를 잃어버리게 된다. 즉 데이터를 잃어버리게 된다.  
적어도 일정 시간동안은 인증상태를 유지하게 하고 싶다.  
<br><br>
로그인 상태는 일정 시간이 지나면 만료된다.  
로그인하거나 사인업 할 때 응답의 일부로 만료기간을 얻게 되기 때문이다.  
그 때 만료시간을 초 단위로 받는다.  
기본은 한 시간이 정해져 있다.  
즉 파이어베이스에서 받은 아이디 토큰은 한 시간 뒤에 만료될 것이다.  
<br><br>
페이지를 다시 로드해도 로그인 상태가 되게 하고 싶다.  
즉, 토큰을 리액트 상태 바깥 어딘가에 저장해야 한다는 말이다.  
자바스크립트에 저장하면 페이지가 리로드되면 사라질 것이다.  
브라우저에는 저장 매커니즘이 있는데, 예를들어 쿠키같은 것이다.  
<br><br>
하지만 더 쉬운 저장 매커니즘은 로컬저장소다.  
우리 페이지가 XSS공격에 취약할 떄만 문제가 생긴다.  
어쨌든 여기서는 로컬스토리지 매커니즘을 사용할 것이다.  
<br><br>

## 로컬스토리지에 토큰 저장하기

<br><br>

우리가 로그인 하면 token 스테이트에만 저장하는 것이 아니라  
토큰을 로컬스토리지에 저장한다.  
그리고 로그아웃 하면 로컬스토리지도 비울 것이다.  
페이지가 로드되면 저장소를 보고 토큰이 있으면 토큰을 이용해서  
사용자가 처음에 새 요청을 보내지 않게 해보자.

<br><br>
로그인해서 token을 로컬스토리지에 저장하기
<br><br>

```jsx
const loginHandler = (token) => {
  setToken(token);
  localStorage.setItem("token", token);
};
```

<br><br>
로컬스토리지에는 문자열이나 숫자같은 기본 데이터만 저장할 수 있다.
객체를 저장하고 싶다면 데이터를 JSON으로 바꿔야 한다.  
그럼 문자열이 된다.  
그리고 로그아웃하면 removeItem을 입력해서 모든 데이터를 지운다.  
혹은 특정 키를 타겟으로 하여 지운다.
지금은 토큰 키를 제거했다.  
<br><br>

```jsx
const logoutHandler = () => {
  setToken(null);
  localStorage.removeItem("token");
};
```

<br><br>
그리고 새로고침을 할 때 로컬저장소를 살펴보고 토큰이 있는지 확인해보자.  
토큰을 발견하면 토큰을 사용해서 사용자를 자동으로 인증한다.  
initialToken변수에 localStorage에서 token을 할당한다.  
<br><br>

```jsx
export const AuthContextProvider = (props) => {
  const initialToken = localStorage.getItem("token");
  const [token, setToken] = useState(null);

```

<br><br>
여기서는 useEffect나 다른건 필요 없다.  
그냥 로컬저장소를 찾아 초기 토큰 밸류를 설정하는 것이다.  
왜냐하면 localStorage가 동기식 API이기 때문이다.  
그리고 초기 밸류는 상태가 초기화 되었을 때 리액트가 한 번 사용할 것이다.  
따라서 이후에는 이 토큰에 어떤 상태변화도 다시 쓰지 않을 것이다.  
<br><br>
이제 페이지 로그인을 했을 때 새로고침 해도 로그인 상태가 계속되는 것을 알 수 있다.  
이 로그인 상태는 아이디 토큰에 의해 일정시간이 지난 뒤에 만료가 되는데,  
기본설정은 한 시간이다.  
<br><br>

# 만료시간이후 로그아웃 자동으로 설정하기

<br><br>

따라서 우리는 자동으로 로컬 저장소에서 토큰을 제거해야 한다.  
토큰이 만료되면 자동으로 사용자를 로그아웃 하는 것이다.  
타이머를 설정할 수도 있고, 로컬 저장소에 남은 시간을 저장해도 된다.  
아니면 로컬 저장소에 만료 시간을 저장하는 것이다.  
그럼 타이머를 바르게 설정할 수 있다.  
<br><br>
작업을 위해서는 loginHandler에 token만이 아니라  
expiration Time이 있어야 한다.  
그럼 남은 시간을 계산할 수 있다.  
<br><br>

```jsx
const loginHandler = (token, expirationTime) => {
  setToken(token);
  localStorage.setItem("token", token);
};
```

<br><br>
현재 시간을 만료시간에서 빼주는 것이다.  
그리고 만료 시간을 로컬스토리지에 저장해야 한다.  
따라서 앱을 리로드 하더라도 만료시간을 정확하게 다시 계산할 수 있다.  
<br><br>
우선 로그인 핸들러가 호출되는 시간을 설정한다.  
AuthContextProvider 밖에서 헬퍼 함수를 작성한다.  
currentTime을 입력하고 newDate를 호출한다.  
그러면 현재 타임 스탬프를 줄 것이다.  
getTime 메서드를 입력하면 현재 타임 스탬프를 알려줄 것이고,  
그건 밀리초일 것이다.

<br><br>
그리고 adjExpirationTime에 만료시간을 새 데이터에 보낸다.  
날짜 객체로 바꾸는 일이다. 그리고 거기에 getTime을 입력한다.  
타임 스탬프를 밀리초로 받는 것이다.

<br><br>

```jsx
const calculateRemainingTime = (expirationTime) => {
  const currentTime = new Date().getTime();
  const adjExpirationTime = new Date(expirationTime).getTime();
};
```

<br><br>
그럼 adjExpirationTime는 미래의 어떤 시간일 것이고,  
currentTime은 현재 타임 스탬프가 될 것이다.  
<br><br>
remainingDuration에 adjExpirationTime에서  
currentTime를 뺀 시간을 할당하도록 한다.  
<br><br>

```jsx
const calculateRemainingTime = (expirationTime) => {
  const currentTime = new Date().getTime();
  const adjExpirationTime = new Date(expirationTime).getTime();

  const remainingDuration = adjExpirationTime - currentTime;
};
```

<br><br>
이미 만료시간을 지났다면 음수가 나올 것이지만 상관없다.  
remainingDuration을 return하는 함수를 완성한다.  
<br><br>

```jsx
const calculateRemainingTime = (expirationTime) => {
  const currentTime = new Date().getTime();
  const adjExpirationTime = new Date(expirationTime).getTime();

  const remainingDuration = adjExpirationTime - currentTime;
  return remainingDuration;
};
```

<br><br>
이 함수는 loginHandler함수에서 사용할 수 있다.  
그 함수에서 calculateRemainingTime을 호출하고  
그 안에는 expirationTime을 인자로 보낸다.

<br><br>

```jsx
const loginHandler = (token, expirationTime) => {
  setToken(token);
  localStorage.setItem("token", token);
  const remainingTime = calculateRemainingTime(expirationTime);
};
```

<br><br>
로그인을 조정했으므로 remainingTime이 양수가 나올 것이다.  
한시간이 밀리초로 나올 것이다.  
타이머를 설정한다. 타이머가 만료되면 사용자를 로그아웃 하는 것이다.

<br><br>
여기서 할 일은 스위치 명령이다.  
로그인 핸들러를 로그아웃 핸들러 다음으로 옮긴다.  
그리고 setTimeout을 호출하여 시간이 지나면 logoutHandler가 실행되도록 하고  
setTimeout인자에는 remainingTime을 입력하기로 한다.  
<br><br>
시간이 만료되면 자동으로 사용자를 로그아웃 할 것이다.  
로그인 핸들러에도 만료시간을 입력해야 한다.  
AuthForm에서 로그인함수를 호출하는데,  
idToken만으로는 부족하며 만료시간도 호출해야 한다.
<br><br>
우리가 로그인 하여 Sign in 요청을 보낼 때 또는 API 라우트에 Sign up할 때  
만료시간을 응답으로 받게 된다.  
idToken이 만료될 때까지의 초다.  
그건 우리가 기대하는 타임 스탬프는 아니다.  
우리가 기대하는 것을 바꿔보거나 데이터를 바꿔보자.  
밀리초 타임 스탬프에서 새 날짜 객체를 만드는 것이다.  
밀리초로 현재 시간을 받아서 data.expiresIn을 더해준다.

<br><br>

```jsx

.then((data) => {
  const expirationTime = new Date(new Date().getTime() + data.expiresIn);
  authCtx.login(data.idToken);
  history.replace("/");
      })
```

<br><br>
그럼 expiresIn 키와 응답이 초로 나올 것이고,  
하지만 사실은 문자열일 것이다.  
그러므로 +를 붙여주어 숫자로 바꾸고 \* 1000을 하면 초에서 밀리초로 바뀐다.  
이제 밀리초로 결과를 받고, 현재 타임 스탬프를 밀리초로 추가하고  
두개를 더한 뒤 밀리초타임 스탬프에서 새 날짜객체를 만드는 데 보낸다.

<br><br>
그럼 만료 시간이 Date Object 날짜 객체가 된다.  
expirationTime을 로그인 함수로 보낸다. 이걸 문자열로 보낸다.  
<br><br>

```jsx
.then((data) => {
  const expirationTime = new Date(
    new Date().getTime() + +data.expiresIn * 1000
  );
  authCtx.login(data.idToken, expirationTime.toISOString());
  history.replace("/");
})
```

<br><br>
방법은 여러가지가 있지만 만료시간은 설정해야 한다.  
타이머도 설정해야 하는데 기본시간은 한시간이다.  
그리고 타이머를 클리어 해야 하는데, 이유는  
사용자가 시간이 되지 않았는데 로그아웃 할 수 있기 때문이다.

<br><br>

# 타이머 방법을 개선해본다.

<br><br>
사용자가 직접 로그아웃 했을 때 타이머를 clear해야한다.

<br><br>

```jsx
let logoutTimer;
```

<br><br>
초기에는 정의되지 않는다. 모든 함수 밖에서 정의된다.  
따라서 현재 auth-context파일 안에서 글로벌 변수이다.  
우리는 변수에 있는 타이머에 레퍼런스를 저장할 수 있다.

<br><br>
setTimeout은 레퍼런스와 id를 리턴한다. 그것을 logoutTimer 변수에 저장한다.  
그리고 logout 버튼을 사용자가 눌렀을 때 실행되는 logoutHandler에서  
만약 logoutTimer가 있다면 clearTimeout을 실행시키고 logoutTimer를 전달해 준다.  
<br><br>

```jsx
const logoutHandler = () => {
  setToken(null);
  localStorage.removeItem("token");

  if (logoutTimer) {
    clearTimeout(logoutTimer);
  }
};
```

<br><br>
그리고 이 타이머 즉, 만료시간을 저장한다.  
따라서 사용자가 로그인하면 로컬 저장소에서 토큰을 받고  
토큰에 남은 시간이 있다면 그걸 최초의 토큰으로 사용한다.  
<br><br>
만약 사용자가 3시간 전에 로그인 헀다면, 토큰은 저장되었을 것이다.  
그리고 더 이상은 가치가 없을 것이다.  
따라서 여기서 로컬저장소를 살펴보고 거기서 토큰을 받는다.  
토큰이 유효할 때만 말이다. 그리고 더이상 유효하지 않을 때 삭제할 것이다.  
<br><br>

# 로컬저장소에 있는 유효하지 않은 토큰 삭제하기

<br><br>
또다른 헬퍼함수를 입력한다.  
<br><br>

```jsx
const retrieveStoredToken = () => {
  const storedToken = localStorage.getItem("token");
  const storedExpirationDate =
};
```

<br><br>
storedToken을 가져오고, 저장된 만료 시간도 storedExpirationDate에 받는다.  
loginHandler에서 expirationTime을 시간을 계산하고 타이머를 설정하는데 썼는데,  
토큰과 함께 저장도 하고 싶다.  
따라서 localStorage.setItem을 입력한다.  
<br><br>

```jsx
const loginHandler = (token, expirationTime) => {
  setToken(token);
  localStorage.setItem("token", token);
  localStorage.setItem("expirationTime", expirationTime);

  const remainingTime = calculateRemainingTime(expirationTime);

  logoutTimer = setTimeout(logoutHandler, remainingTime);
};
```

<br><br>
그리고 중요한 것은 문자열이어야 한다는 것이다.  
이는 우리가 설정한 방법이다.  
이제 로그인할 때마다 설정될 것이다.  
<br><br>
그걸 retrieveStoredToken에서 가져와서 storedExpirationDate에 할당한다.  
<br><br>

```jsx
const retrieveStoredToken = () => {
  const storedToken = localStorage.getItem("token");
  const storedExpirationDate = localStorage.getItem("expirationTime");
};
```

<br><br>
여기에 remainingTime에 calculateRemainingTime을 호출하여  
저장된 만료시간을 전달해 남아있는 시간을 계산해본다.  
그리고 남은 시간이 0보다 작으면 혹은 어떤 지점을 입력할 수도 있다.  
예를 들어 60000밀리초(1분)로 지정할 수 있다.  
그 때는 사용자를 로그인하지 않는다. return null 하도록 한다.  
그리고 token과 expirationTime도 로컬저장소에서 삭제한다.  
<br><br>

```jsx
const retrieveStoredToken = () => {
  const storedToken = localStorage.getItem("token");
  const storedExpirationDate = localStorage.getItem("expirationTime");

  const remainingTime = calculateRemainingTime(storedExpirationDate);

  if (remainingTime <= 60000) {
    localStorage.removeItem("token");
    localStorage.removeItem("expirationTime");
    return null;
  }
};
```

<br><br>
만약 체크했는데 시간이 남아있고 유효한 토큰이 있다면 저장된 토큰을 리턴한다.
그리고 남은 시간을 return하여 타이머로 설정할 수 있다.  
<br><br>

```jsx
const retrieveStoredToken = () => {
  const storedToken = localStorage.getItem("token");
  const storedExpirationDate = localStorage.getItem("expirationTime");

  const remainingTime = calculateRemainingTime(storedExpirationDate);

  if (remainingTime <= 60000) {
    localStorage.removeItem("token");
    localStorage.removeItem("expirationTime");
    return null;
  }

  return { token: storedToken, duration: remainingTime };
};
```

<br><br>
이것을 AuthContextProvider안에서 다른 변수로 호출한다.  
<br><br>

```jsx

export const AuthContextProvider = (props) => {
  const tokenData = retrieveStoredToken();
```

<br><br>
그러면 tokenData는 객체이고,  
initialToken은 localStorage에서 얻어온 것이므로 아래와 같이  
tokenData.token으로 할당할 수 있게 된다.  
이건 null이 return되지 않고 객체가 return될 때  
사용할 수 있게 된다.  
<br><br>

```jsx

export const AuthContextProvider = (props) => {
  const tokenData = retrieveStoredToken();
  const initialToken = tokenData.token;
```

<br><br>
그러므로 체크해야 하는데, tokenData가 truthy일 경우에만  
token을 initialToken에 할당해 주는 것으로 한다.  
만일 tokenData가 없다면 initialToken은 설정되지 않을 것이다.  
<br><br>

```jsx
export const AuthContextProvider = (props) => {
  const tokenData = retrieveStoredToken();
  let initialToken;
  if (tokenData) {
    initialToken = tokenData.token;
  }
  const [token, setToken] = useState(initialToken);
```

<br><br>
토큰 데이터가 있고 토큰을 설정했다면 타이머도 설정해야 한다.  
useEffect를 import하고
<br><br>

```jsx
const logoutHandler = () => {
  setToken(null);
  localStorage.removeItem("token");

  if (logoutTimer) {
    clearTimeout(logoutTimer);
  }
};

useEffect(() => {}, [tokenData]);

const loginHandler = (token, expirationTime) => {
  setToken(token);
  localStorage.setItem("token", token);
  localStorage.setItem("expirationTime", expirationTime);

  const remainingTime = calculateRemainingTime(expirationTime);

  logoutTimer = setTimeout(logoutHandler, remainingTime);
};
```

<br><br>
dependencies에 tokenData를 설정한다.  
그리고 setTimeout을 설정한다.  
여기서 두번째 인자인 timer가 설정될 시간은 tokenData.duration이 된다.  
만료되지 않은 로그인 세션이 있다고 하면  
자동으로 로그인 될 것이며  
남은 시간 이후에는 자동으로 만료되어 로그아웃될 것이다.

<br><br>

```jsx
useEffect(() => {
  logoutTimer = setTimeout(logoutHandler, tokenData.duration);
}, [tokenData]);
```

<br><br>
<br><br>
<br><br>
