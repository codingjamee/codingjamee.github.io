---
title: "#318[udemy react 강의노트]Authentication 실습 두번째"
excerpt: "Authentication react Application"
published: true
categories:
  - Blog
tags:
  - [Authentication]

toc: true
toc_sticky: false

date: 2022-11-11
last_modified_at: 2022-11-11
---

<br><br>

# 컨텍스트로 인증 state 관리하기

<br><br>

## 토큰은 앱와이드 상태여야 한다.

<br><br>
이제 UI를 인증 idToken을 활용하여 업데이트 하려 한다.  
그 전에 해야할 것은 작동중인 리액트 앱 어딘가에 이 토큰을 저장해야 한다.  
모든 컴포넌트에서 사용할 수 있도록 말이다.  
지금은 응답을 AuthForm.js 컴포넌트에 기록하고 있지만,  
토큰에 관심이 있는 컴포넌트는 AuthForm 뿐만이 아니다.

<br><br>
토큰은 우리에게 표시되는 헤더의 메뉴 항목을 업데이트 할 때도 필요하고  
앱의 다른 영역에도 필요하다.
사용자가 로그인 상태라는 일반 정보는 많은 컴포넌트에 중요하기 때문이다.
인증상태는 자주 바뀌는 것은 아니니 성능 이슈는 없을 것이다.  
<br><br>

## Context API 또는 Redux를 사용한다.

<br><br>
지금 강의에서는 서드파티 라이브러리를 활용하지 않는 Context API를 사용한다.

1. 파일을 만들고 AuthContext 변수에 React.createContext();를 할당한다.
   그 안에는 초기 데이터로 초기화 해준다.

   컨텍스트의 전반적인 형태를 정의할 수 있고,  
    나중에 자동완성도 잘 되기 때문이다.
   <br><br>

```jsx
import React from "react";

const AuthContext = React.createContext({

```

2. token(empty string)과 isLoggedIn(boolean) 변수를 추가하고, 함수를 추가해준다.
   빈 login 함수인데 매개변수로 token을 받는다. 내용은 나중에 작성하기로 한다.
   매개변수를 받지 않고 아무것도 하지 않는 logout함수도 추가한다.

<br><br>

```jsx
import React from "react";

const AuthContext = React.createContext({
  token: "",
  isLoggedIn: false,
  login: (token) => {},
  logout: () => {},
});
```

<br><br>

3. AuthContextProvider 컴포넌트를 추가한다.
   이 컴포넌트는 인증 관련 상태를 관리하는 역할을 할 것이다.
   매개변수에는 props를 받는다.
   return값은 AuthContext.Provider안에 props.children으로 감싼다.

```jsx
const AuthContextProvider = (props) => {
  return <AuthContext.Provider>{props.children}</AuthContext.Provider>;
};
```

<br><br>
AuthContextProvider를 다른 컴포넌트를 감싸는 래퍼로 활용하면  
다른 컴포넌트가 AuthContext에 접근할 수 있게 된다.  
AuthContextProvider 컴포넌트에서 인증 데이터를 관리할 것이다.
컴포넌트 안에 토큰의 상태를 정의한다.  
<br><br>

```jsx
const AuthContextProvider = (props) => {
  const [token, setToken] = useState(null);

  return <AuthContext.Provider>{props.children}</AuthContext.Provider>;
};
```

<br><br>
이렇게 하면 이 토큰 상태를 보고 사용자의 로그인 여부를 유추할 수 있다.  
토큰이 없으면 로그인상태가 아닌 것이고, 토큰이 있으면 로그인 상태인 것이다.  
그러므로 userIsLoggeIn = !!token으로 할당한다.
<br><br>
!!이 낯설 수도 있는데 참 또는 거짓 값을 불리언 값으로 변경해 준 것이다.  
토큰이 빈 문자열이 아니면 true를 반환하고 빈 문자열이면 false를 반환할 것이다.  
<br><br>

```jsx
const AuthContextProvider = (props) => {
  const [token, setToken] = useState(null);

  const userIsLoggeIn = !!token;

  return <AuthContext.Provider>{props.children}</AuthContext.Provider>;
};
```

<br><br>
토큰 상태를 바꾸는 함수도 필요하다. loginHandler와 logoutHandler다.
logoutHandler에는 token을 null로 변경해준다.
loginHandler에는 token을 받아 token state에 저장해준다.
<br><br>

```jsx
const AuthContextProvider = (props) => {
  const [token, setToken] = useState(null);

  const userIsLoggeIn = !!token;
  const loginHandler = (token) => {
    setToken(token);
  };
  const logoutHandler = () => {
    setToken(null);
  };

  return <AuthContext.Provider>{props.children}</AuthContext.Provider>;
};
```

<br><br>
그리고 contextValue를 추가해준다.

<br><br>

```jsx
const contextValue = {
  token: token,
  isLoggedIn: userIsLoggedIn,
  login: loginHandler,
  logout: logoutHandler,
};
```

<br><br>
그리고 contextValue는 AuthContext.Provider의 value로 설정해준다.

<br><br>

```jsx
return (
  <AuthContext.Provider value={contextValue}>
    {props.children}
  </AuthContext.Provider>
);
```

<br><br>
AuthContextProvider를 export해주고,  
AuthContext를 default로 export해준다.
<br><br>
<br><br>

```jsx
export const AuthContextProvider = (props) => {
  ...
export default AuthContext;
```

<br><br>
index.js파일에서 AuthContext로 앱 전체를 래핑한다.  
<br><br>

```jsx
import ReactDOM from "react-dom/client";
import { BrowserRouter } from "react-router-dom";

import "./index.css";
import App from "./App";
import { AuthContextProvider } from "./store/auth-context";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <AuthContextProvider>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </AuthContextProvider>
);
```

<br><br>
auth-context에서 export한 named export를 중괄호를 써서 named import로 import한다.  
이제 앱의 다른 영역에서도 필요할 때 컨텍스트에 접근할 수 있게 된다.  
<br><br>
AuthForm에서 사용해보자.  
useContext를 import하고 useContext를 AuthContext에 연결한다.

<br><br>

```jsx
const AuthForm = () => {
  const emailInputRef = useRef();
  const passwordInputRef = useRef();

  const authCtx = useContext();
```

<br><br>
이제 접근이 가능해졌으니 컨텍스트에 login()을 호출 할 수 있다.
로그인에 성공했으면 idToken이 생길 것이다. 파이어베이스가 응답 객체의  
idToken필드에 토큰을 반환할 것이니까 말이다.  
`.then((data) => { console.log(data); })`  
여기에서 login함수에 data.idToken을 전달해준다.

<br><br>

```jsx
.then((data) => {
        authCtx.login(data.idToken);
      })
```

<br><br>
data.idToken이 파이어베이스에게 받는 토큰이 될 것이다.  
<br><br>

# 인증상태를 UI에 반영하는 작업

<br><br>
MainNavigation.js 컴포넌트로 가서,  
로그인 여부에 맞는 메뉴 옵션 바꾸기 하자.  
<br><br>
useContext를 하고 AuthContext를 가리키고 변수에 할당해 사용가능하게 한뒤  
isLoggedIn 상태를 isLoggedIn변수에 할당해 사용한다.  
해당 상태에 따라 렌더링하는 JSX코드를 맞게 수정한다.  
<br><br>

```jsx
import { Link } from "react-router-dom";

import classes from "./MainNavigation.module.css";
import { useContext } from "react";
import AuthContext from "../../store/auth-context";

const MainNavigation = () => {
  const authCtx = useContext(AuthContext);
  const isLoggedIn = authCtx.isLoggedIn;
  return (
    <header className={classes.header}>
      <Link to="/">
        <div className={classes.logo}>React Auth</div>
      </Link>
      <nav>
        <ul>
          {!isLoggedIn && (
            <li>
              <Link to="/auth">Login</Link>
            </li>
          )}
          {isLoggedIn && (
            <li>
              <Link to="/profile">Profile</Link>
            </li>
          )}
          {isLoggedIn && (
            <li>
              <button>Logout</button>
            </li>
          )}
        </ul>
      </nav>
    </header>
  );
};

export default MainNavigation;
```

<br><br>
위와 같이 해서 Sign up 했던 아이디 비번으로 로그인요청을 하면  
정상적으로 로그인이 되었다면 Login버튼이 MainNavigation에서 사라지고  
Profile과 Logout 버튼만 나타나는 것을 알 수 있다.  
<br><br>

# 보호된 리소스에 접근하기 Profile (비밀번호 변경하기)

<br><br>
이제 로그인이 가능하게 되었으니 보호된 리소스에 요청을 보낼 경우  
인증 토큰이 어떻게 사용되는지 알아보자.  
파이어베이스 인증 REST API를 참고하자.  
어떤 엔드포인트도 보호된 API 엔드포인트가 될 수 있다.  
Realtime Database 와 소통하는 파이어베이스 REST API일 수도 있다.

<br><br>
Realtime Database에서는 Rules로 인증을 강제할 수 있다.  
우리는 기본에 충실하여 Change password의 예시를 활용해보자.  
firebase Auth REST API로 검색하여 `https://firebase.google.com/docs/reference/rest/`로 들어가보면  
Change password에서 확인할 수 있다.

<br><br>

```
Change password
You can change a user's password by issuing an HTTP POST request to the Auth setAccountInfo endpoint.

Method: POST

Content-Type: application/json

Endpoint

https://identitytoolkit.googleapis.com/v1/accounts:update?key=[API_KEY]
```

<br><br>
Request Body Payload
<br><br>

| Property Name     | Type    | Description                                       |
| ----------------- | ------- | ------------------------------------------------- |
| idToken           | string  | A Firebase Auth ID token for the user.            |
| password          | string  | User's new password.                              |
| returnSecureToken | boolean | Whether or not to return an ID and refresh token. |

<br><br>
위의 Endpoint로 Request를 보내야 한다.  
중요한건 Request body에 있듯, 비밀번호를 바꾸는 사용자를 식별하는  
idToken을 첨부해야 한다는 것이다.  
사용자의 비밀번호를 바꾸려면 그 사용자가 누군지 알아야 하니까 당연한 말이다.  
토큰 대신 이메일주소를 명시하면 이메일주소를 알고있는 누구든지  
비밀번호 변경을 할 수 있게 되므로 그래서는 안될 것이다.
<br><br>
그러므로 로그인 한 뒤에만 가질 수 있는 idToken을 사용해야 하는 것이다.  
이제 ProfileForm 에서 user input을 얻고 요청을 보내보자.  
form submission을 처리하고 사용자 입력을 수집하는 것으로부터 시작한다.  
<br><br>
useRef를 변수 newPasswordInputRef에 할당하고 ref로 input 태그에 연결해준다.  
form을 onSubmit했을 때 submitHandler 함수가 실행되도록 하고  
preventDefault를 해주고, enteredPassword 변수에 current.value를 할당해 준다.  
<br><br>

```jsx
import classes from "./ProfileForm.module.css";
import { useRef } from "react";

const ProfileForm = () => {
  const newPasswordInputRef = useRef();
  const submitHandler = (event) => {
    event.preventDefault();

    const enteredNewPassword = newPasswordInputRef.current.value;
  };
  return (
    <form className={classes.form} onSubmit={submitHandler}>
      <div className={classes.control}>
        <label htmlFor="new-password">New Password</label>
        <input type="password" id="new-password" ref={newPasswordInputRef} />
      </div>
      <div className={classes.action}>
        <button>Change Password</button>
      </div>
    </form>
  );
};

export default ProfileForm;
```

<br><br>
<br><br>
이제 추출한 비밀번호를 사용해 요청을 보낸다.  
요청은 우리가 있는 ProfileForm 컴포넌트나 UserProfile컴포넌트에서  
event를 발생시켜서 보낼 수 있다.  
즉, 함수를 prop으로 받아 ProfileForm.js에서 그 함수를 호출하면 된다.  
여기서는 ProfileForm컴포넌트에서 바로 요청을 보내보자.

<br><br>

```jsx
import classes from "./ProfileForm.module.css";
import { useRef } from "react";
import { config } from "../../apikey";

const ProfileForm = () => {
  const newPasswordInputRef = useRef();
  const submitHandler = (event) => {
    event.preventDefault();

    const enteredNewPassword = newPasswordInputRef.current.value;
    fetch(
      `https://identitytoolkit.googleapis.com/v1/accounts:update?key=${config.apikey}`
    );
  };
  return (
    <form className={classes.form} onSubmit={submitHandler}>
      <div className={classes.control}>
        <label htmlFor="new-password">New Password</label>
        <input type="password" id="new-password" ref={newPasswordInputRef} />
      </div>
      <div className={classes.action}>
        <button>Change Password</button>
      </div>
    </form>
  );
};

export default ProfileForm;
```

<br><br>
우리는 POST요청에 json데이터를 첨부해 이 URL에 보내야 한다.  
그러므로 fetch의 두번째 인자에 method: "POST"와 JSON.stringify객체가 담긴 body,  
headers: { "Content-Type": "application/json" }가 들어있는 객체를 추가한다.  
body의 JSON.stringify안에는 idToken과 password, returnSecureToken 필드가 있는  
객체를 전달해야 한다.  
idToken filed에는 인증된 idToken과 새로 설정할 비밀번호인 password,  
returnSecureToken이 있어야 한다.  
returnSecureToken은 참일 필요가 없지만 응답으로 새 토큰을 받으려면 참이 될 수도 있다.  
<br><br>

```jsx
fetch(URL, {
  method: "POST",
  body: JSON.stringify({
    idToken: ,
    password: ,
    returnSecureToken:
  }),
  headers: { "Content-Type": "application/json" },
});
```

<br><br>
idToken은 Context에 저장되어있으므로 import 해준다.  
그리고 각각의 자리에 맞는 내용을 입력해준다.  
idToken은 authCtx로 가져온 token을 입력해주고  
password는 enteredNewPassword로 form 태그에서 가져온 값을 입력한다.

<br><br>

```jsx
fetch(URL, {
  method: "POST",
  body: JSON.stringify({
    idToken: authCtx.token,
    password: enteredNewPassword,
    returnSecureToken: false,
  }),
  headers: { "Content-Type": "application/json" },
});
```

<br><br>
여기서 비밀번호를 4자리 미만으로 입력하면 비밀번호가 너무 짧아  
파이어베이스가 오류를 throw하는지도 반드시 확인해야 한다.  
그런 경우에는 비밀번호 변경에 실패할 것이다.  
<br><br>
이런 경우 catch로 오류를 처리할 수도 있지만 이미 다뤄본 것이므로  
넘어가기로 한다. 그리고 오류가 나지 않게 하기 위해  
input 비밀번호 입력 부분에 minLength라는 빌트인 속성을 추가하기로 한다.  
이 방식은 데브툴로 비활성화 될 수 있으므로 다른 방식으로 유효성을 검사해도 된다.  
그러나 이게 기본적인 검사방식이다.

<br><br>
이제 오류가 나지 않는것을 가정하고 then블록을 추가하도록 한다.  
이제 곧 사용자를 리다이렉트 할 수 있다.  
지금은 아무 작업도 하지 않고 이대로 저장한 뒤 앱에서 직접 해 보도록 한다.  
<br><br>

```jsx
fetch(URL, {
  method: "POST",
  body: JSON.stringify({
    idToken: authCtx.token,
    password: enteredNewPassword,
    returnSecureToken: false,
  }),
  headers: { "Content-Type": "application/json" },
}).then((res) => {
  //assumption: always Succeeds!
});
```

<br><br>
이제 network 탭을 열어놓고 비밀번호 변경을 해보면  
POST Request가 가는 것을 알 수 있고,  
idToken도 보내진 것을 알 수 있다.  
그러나 응답 데이터는 없다.  
아마도 returnSecureToken을 false로 설정했기 때문이다.  
<br><br>
비밀번호 변경은 성공적으로 되었으며,  
재 로그인을 바뀐 비밀번호로 시도하면 알 수 있다.  
이런 식으로 idToken을 활용해 인증 API 엔드포인트로 요청을 보낼 수 있다.  
토큰을 어떻게 추가하는지는 엔드포인트에 따라 다르다.  
<br><br>
우리는 body에 추가했지만 다른 엔드포인트에는  
query 매개변수로 토큰을 추가해야 할지도 모른다.  
또 어떤 API 엔드포인트는 headers에 추가해야 할 수도 있다.

<br><br>

# 사용자에게 피드백 제공하기 (리다이렉트, useHistory를 사용하여)

<br><br>
비밀번호 변경 및 로그인 후에 사용자를 리다이렉트 해보자.  
로그인 리다이렉트부터 해본다.  
<br><br>
AuthForm 컴포넌트에서 우리는 token도 있고  
사용자가 로그인되어있는 상태에 리다이렉트를 하려고 한다.  
useHistory를 import한다.  
그리고 replace를 통해 사용자를 다른 페이지로 리다이렉트 한다.  
replace는 사용자가 뒤로가기를 눌러 이전 페이지로 갈 수 없게 하는 것이다.

<br><br>
AuthForm.js 파일
<br><br>

```jsx
 .then((data) => {
  authCtx.login(data.idToken);
  history.replace('/')
```

<br><br>
이렇게 하면 로그인에 성공하면 홈화면으로 리다이렉트 된다.  
똑같이 비밀번호를 변경 후에도 리다이렉트 해보자.

<br><br>
ProfileForm.js파일
<br><br>

```jsx
.then((res) => {
  history.replace("/");
})
```

<br><br>

# Logout button 활성화 하기

<br><br>
현재는 logout버튼은 아무런 기능도 하고 있지 않다.  
버튼을 눌렀을 때 어떤 동작을 해야 할까?  
파이어 베이스에 요청을 보내야 한다고 생각하겠지만,  
인증 토큰 기법의 핵심은 로그인 한 클라이언트의 어떤 정보도  
서버에 저장되지 않는다는 것이다.  
따라서 어떤 요청도 보낼 필요가 없다.  
파이어베이스가 우리가 로그인 중이거나 로그인 했었는지  
알지 못하고 관심도 없다.  
로그아웃 시 바꿔야 할 것은 우리 상태이다.  
token을 비우면 된다. 빈 문자열이나 null로 설정하면 된다.  
<br><br>

```jsx
<button onClick={logoutHandler}>Logout</button>
```

<br><br>
logoutHandler를 작성하여 Logout버튼과 onClick 이벤트를 연결해주고,
logoutHandler안에는 useContext의 logout함수를 호출해 준다.  
그리고 로그아웃 시 useHistory를 사용하여 홈화면으로 리다이렉트 해준다.  
그런데 프론트엔드를 보호해줄 방법을 다음 강의에서 배워본다.
<br><br>
<br><br>
<br><br>
