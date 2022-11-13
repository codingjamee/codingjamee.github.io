---
title: "#314[udemy react 강의노트]Authentication실습"
excerpt: "Authentication react Application"
published: true
categories:
  - Blog
tags:
  - [Authentication]

toc: true
toc_sticky: false

date: 2022-11-10
last_modified_at: 2022-11-10
---

<br><br>

# firebase Auth REST API

<br><br>
위의 단어를 구글에 검색해보면 reference가 나오는데,  
거기서 보호된 리소스 접근 요청을 어떤 API 엔드포인트에 보내야 하는지 알 수 있다.  
지금 강의에서는 프론트엔드 강의이니 가짜 백엔드를 사용한다.  
<br><br>
reference에서 오른쪽에 Sign up with email/password 를 클릭하면  
이메일과 비밀번호로 사용자를 생성할 때 어느 URL로 요청을 보내야 하는지  
이 작업에 어떤 데이터를 추가해야하는지가 나와있다.

<br><br>
로그인 엔드포인트나 비밀번호 변경 엔드포인트도 찾을 수 있다.  
참고로 비밀번호 변경은 보호된 리소스가 될 것이다.  
로그인한 상태에서만 비밀번호를 바꿔야 하니까 말이다.  
그래서 API 엔드포인트에는 요청 페이로드 즉, 요청 데이터의 일부로서 idToken이 필요하다.
우리는 가짜 백엔드로 해서 이번 섹션의 가짜 프로젝트에 활용할 것이다.

<br><br>
이제 강의에 첨부된 자료에 Authentication코드를 추가해 보자.  
firebase에서 Authentication으로 들어간다.  
firebase authentication REST API를 사용할 것이기 때문이다.  
API를 사용해 요청을 보내려면 파이어베이스 프로젝트 중 하나에서  
인증 잠금을 해제해야 할 것이다.  
<br><br>
Authentication 화면에서 시작하기를 누르고 인증을 설정할 수 있다.  
그러면 Sign-in method 페이지가 뜨며 로그인 방법을 선택할 수 있다.  
파이어베이스는 다양한 인증 서비스로 로그인을 지원한다.  
하지만 지금은 사용자의 이메일/비번 조합이 담긴 자체 데이터베이스와  
자체 API를 갖고 있다고 가정하고 진행할 것이라서 Email/password를 선택하자.  
그리고 Enable을 선택한다. 그리고 저장한다.  
이렇게 하면 사용자 로그인을 할 수 있다.
<br><br>
이번 강의 섹션에서는 UI에 로그인 여부도 반영할 것이다.  
그러므로 로그인 상태에서만 로그아웃 버튼을 표시하게 될 것이다.  
이제 우리가 인증을 활성화한 파이어베이스 프로젝트에서 새 계정을 생성하고  
파이어베이스가 제공하는 REST API에 요청을 보낼 수 있는지 확인해 보자.

<br><br>
AuthForm.js파일에서 Sign up 모드인지 Login 모드인지 확인한다.  
Sign up모드면 파이어베이서 REST API를 통해 입력 데이터를  
적절한 API 엔드포인트로 보낼 것이다.  
그러려면 AuthForm.js 컴포넌트에 함수를 추가해야 한다.  
모드에 상관없이 Form이 submit되었을 때 실행되는 함수다.

<br><br>

그리고 입력된 데이터를 추출한다. 방법엔 두가지가 있었다.  
useState로 모든 키 입력을 추출해주는 방법,  
useRef로 키 입력을 저장해서 입력 데이터를 가져오는 방법.  
<br><br>
useRef를 사용해 저장하자.
그리고 input태그에 ref 속성을 연결하여 포인트를 지정해준다.  
그걸 submitHandler에 enteredEmail등의 변수로 할당하여  
ref의 current.value를 저장해준다.  
<br><br>

```jsx
const AuthForm = () => {
  const emailInputRef = useRef();
  const passwordInputRef = useRef();

...
 const submitHandler = (event) => {
    event.preventDefault();
    const enteredemail = emailInputRef.current.value;
    const enteredpassword = passwordInputRef.current.value;
  };
...


<form onSubmit={submitHandler}>
  <div className={classes.control}>
    <label htmlFor="email">Your Email</label>
    <input type="email" id="email" required ref={emailInputRef} />
  </div>
  <div className={classes.control}>
    <label htmlFor="password">Your Password</label>
    <input
      type="password"
      id="password"
      required
      ref={passwordInputRef}
    />
  </div>
```

<br><br>

# 회원가입 요청하기

<br><br>

submitHandler에 인증 유효확인 절차를 넣을 수도 있겠지만 지금은 생략하고 넘어간다.  
로그인 모드가 아닐 땐 적절한 엔드포인트에 요청을 보내본다.  
firebase reference에서 Sign up with email / password부분을 살펴보면  
API라우트로 보내는 요청의 방법을 알 수 있다.

<br><br>

```jsx
Sign up with email / password
You can create a new email and password user by issuing an HTTP POST request to the Auth signupNewUser endpoint.

Method: POST

Content-Type: application/json

Endpoint

https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=[API_KEY]
```

<br><br>

| Property Name     | Type    | Description                                                              |
| ----------------- | ------- | ------------------------------------------------------------------------ |
| email             | string  | The email for the user to create.                                        |
| password          | string  | The password for the user to create.                                     |
| returnSecureToken | boolean | Whether or not to return an ID and refresh token. Should always be true. |

<br><br>

EndPoint로 POST요청을 보내야 하며 위 표와 같은 데이터를 첨부해야 한다.  
그래야 새 사용자를 생성할 수 있게 된다.

<br><br>
그 URL을 복사하여 코드에서 fetch 함수로 HTTP요청을 보낸다.

<br><br>

```jsx
const submitHandler = (event) => {
  event.preventDefault();
  const enteredemail = emailInputRef.current.value;
  const enteredpassword = passwordInputRef.current.value;

  if (isLogin) {
  } else {
    fetch(
      "https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=[API_KEY]"
    );
  }
};
```

<br><br>

<br><br>
우리가 이 URL에서 눈여겨 볼 것이 있는데 [API_KEY] 부분이다.  
URL의 일부지만 파이어베이스 프로젝트의 특정 API키로 바꿔 써야 한다.  
이 주소가 어떤 프로젝트에 속하고 어떤 프로젝트에 사용자를 생성하려는 건지  
파이어베이스에게 알리려면 프로젝트의 API키를 넣어야 한다.  
API키는 파이어베이스 프로젝트 콘솔에서 설정 아이콘을 눌러  
프로젝트 설정에 들어가면 웹 API키에서 찾을 수 있다.  
<br><br>
우리 요청은 POST 리퀘스트라고 되어있었다. 지금의 요청은 기본값으로 GET요청이다.  
컨텐트 타입은 json데이터다.  
요청을 변경해보자.  
<br><br>

```jsx
{ method: "POST",
  body: JSON.stringify({}) }
```

<br><br>
fetch함수의 두번째 인자로 객체를 전송하는데 method는 POST로 하고 body에는  
위와 같이 한다. 그 안의 객체는 나중에 json 데이터로 변환할 것이다.  
객체 안에는 email과 password, returnSecureToken 키를 넘겨야 한다.  
요청에 headers도 추가한다 json데이터가 들어온다는 것을 인증 REST API에 알리기 위해서다.  
<br><br>

```jsx
{ method: "POST",
  body: JSON.stringify({
    email: enteredEmail,
    password: enteredPassword,
    returnSecureToken: true,
  })
  headers: {
    "Content-Type": "application/json",
  },
}
```

<br><br>
이렇게 하면 가입 요청이 가게 될 것이다.  
<br><br>

# 오류 처리와 응답 다루기

<br><br>

```jsx
fetch("https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=", {
  method: "POST",
  body: JSON.stringify({
    email: enteredEmail,
    password: enteredPassword,
    returnSecureToken: true,
  }),
  headers: {
    "Content-Type": "application/json",
  },
}).then((res) => {
  if (res.ok) {
  } else {
  }
});
```

<br><br>
여기에서 오류가 난 응답이 있다면
<br><br>

```jsx
else{
  res.json();
}
```

<br><br>
res.json을 호출해 응답과 함께 온 데이터를 살펴볼 수 있다.  
res.json()도 프로미스를 반환하므로 실제 응답데이터에 접근해 이 안에서  
오류창을 띄우는 작업을 수행하면 될 것이다.  
이 중첩된 프로미스 체인을 return 한다.  
<br><br>

```jsx
then((res) => {
  if (res.ok) {
  } else {
    return res.json().then((data) => {
      //show an error modal
      console.log(data);
    });
  }
});
```

<br><br>
이렇게 하고 화면에서 Sign up을 눌러 계정을 생성해보면  
비밀번호를 4자리만 입력하게 되면 오류메시지가 뜨는데,  
이는 firebase가 강제하는 제약 내지는 보안 매커니즘이다.  
계정 생성 버튼을 클릭하고 firebase에 있는  
Authentication으로 들어가보면 사용자가 가입한 ID가 나온다.  
고유 ID도 보이게 된다.  
<br><br>

# 오류 메시지 사용자에게 보여주기

<br><br>
error메시지의 객체의 모양을 잘 살펴보면,  
error.message안에 에러메시지가 있으므로 그것을 errorMessage에 할당한다.

<br><br>

```jsx
else {
    return res.json().then((data) => {
      const errorMessage = data.error.message;
    });
```

<br><br>
그런데 이 때 error 프로퍼티가 존재하고,  
error가 message 프로퍼티를 가진 객체인지 검사부터 해야한다.  
조건이 거짓이라면 errorMessage를 다르게 설정해야 할 것이다.
<br><br>

```jsx
then((res) => {
  if (res.ok) {
  } else {
    return res.json().then((data) => {
      let errorMessage = "Authentication failed!";
      if (data && data.error && data.error.message) {
        errorMessage = data.error.message;
      }
      alert(errorMessage);
    });
  }
});
```

<br><br>
위와같이 하면 data나 data.error나 data.error.message 중 하나가 없다면  
errorMessage는 data.error.message로 설정되지 않을 것이다.  
그리고 지금은 단순히 alert를 errorMessage를 보여주도록 호출하자.  
그렇게 하고 오류를 발생시켜 보면 오류메시지가 alert창으로 잘 뜨는 것을 볼 수 있다.

<br><br>
요청이 오는 동안 로딩 상태를 표시할 수도 있다.  
useState를 사용해서다. isLoading을 if문 위에 적는다.  
setIsLoading을 true로, 로딩이 시작 되었다는 것을 알린다.  
<br><br>

```jsx
const submitHandler = (event) => {
    event.preventDefault();
    const enteredEmail = emailInputRef.current.value;
    const enteredPassword = passwordInputRef.current.value;

    setIsLoading(true);
```

<br><br>
그리고 응답이 오면 에러여부와 상관없이 setIsLoading을 false로 변경한다.  
로딩중이 아닐 때만 계정 생성 버튼이 보이도록,  
로딩중일 때는 특정 문구가 보이도록 설정한다.

<br><br>

```jsx
{
  !isLoading && <button>{isLogin ? "Login" : "Create Account"}</button>;
}
{
  isLoading && <p>Sending request...</p>;
}
```

<br><br>

# 유저 로그인 기능 구현해보기

<br><br>
이제 if문으로 login 상태일 때를 확인하는 코드에 로그인 기능을 구현해본다.  
여기에는 다른 URL과 다른 URL로 HTTP요청을 보내야 한다.  
정확히 말하면 파이어베이스 auth REST API 문서의  
Sign in with email / password 항목에 있는 URL로 보내야 한다.  
<br><br>

```jsx
Sign in with email / password
You can sign in a user with an email and password by issuing an HTTP POST request to the Auth verifyPassword endpoint.

Method: POST

Content-Type: application/json

Endpoint

https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key=[API_KEY]
```

<br><br>
| Property Name | Type | Description |
| ----------------- | ------- | ------------------------------------------------------------------------ |
| email | string | The email the user is signing in with.create. |
| password | string | The password for the account.|
| returnSecureToken | boolean | Whether or not to return an ID and refresh token. Should always be true. |

<br><br>
위의 코드는 Sign up 에서 코드와 유사하므로 if문에서 빠져나와 공통적으로  
실행시키는 것으로 하고 url만 바뀌므로 url변수를 만들어 if문에 바꾸기만 하자.
<br><br>

```jsx
const submitHandler = (event) => {
  event.preventDefault();
  const enteredEmail = emailInputRef.current.value;
  const enteredPassword = passwordInputRef.current.value;

  setIsLoading(true);
  let url;
  if (isLogin) {
    url =
      "https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key=";
  } else {
    url = "https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=";
  }
  fetch(url, {
    method: "POST",
    body: JSON.stringify({
      email: enteredEmail,
      password: enteredPassword,
      returnSecureToken: true,
    }),
    headers: {
      "Content-Type": "application/json",
    },
  }).then((res) => {
    setIsLoading(false);
    if (res.ok) {
    } else {
      return res.json().then((data) => {
        let errorMessage = "Authentication failed!";
        if (data && data.error && data.error.message) {
          errorMessage = data.error.message;
        }
        alert(errorMessage);
      });
    }
  });
};
```

<br><br>

# 로그인 성공했을 때 받는 데이터를 다루는 법

<br><br>
이번엔 성공사례를 다른 방식으로 처리해볼 것이다.  
로그인이 성공하면 아래와 같은 데이터가 온다.

<br><br>
| Property Name | Type | Description |
| ----------------- | ------- | ------------------------------------------------------------------------ |
| idToken | string | A Firebase Auth ID token for the authenticated user. |
| email | string | The email for the authenticated user.|
| refreshToken | boolean | A Firebase Auth refresh token for the authenticated user. |
|expiresIn|string|The number of seconds in which the ID token expires.|
|localId |string|The uid of the authenticated user.|
|registered |boolean|Whether the email is for an existing account.|

<br><br>
우리가 주목할 것은 idToken이다. 이것을 보호된 리소스에 후속요청을 보낼 때 첨부해야 한다.  
또한 리액트 앱의 프론트엔드에서 사용자가 로그인 헀다는 증명으로 활용할 수 있다.  
이건 파이어베이스에 유효한 이메일/비밀번호 조합을 보냈을 때만 보낼 수 있다.  
다른 데이터는 일단 넘어가기로한다.

<br><br>
조금 다르지만 Sign up에도 idToken이 있다.
그래서 로그인과 가입 모두 같은 방식으로 응답을 처리할 수 있다.  
성공응답이 오면 res.json();로 처리한다.  
else문과 달리 중첩 프로미스가 아닌 그냥이다.
<br><br>
그리고 else문에는 throw new Error를 하여 에러메시지를 활용하고,  
그 다음에는 .then으로 성공시 돌아오는 data를 받거나 catch를 통해 err를 받는다.  
그리고 alert를 catch 블럭 안에 넣어 error message를 띄운다.  
<br><br>

```jsx
.then((res) => {
    setIsLoading(false);
    if (res.ok) {
      return res.json();
    } else {
      return res.json().then((data) => {
        let errorMessage = "Authentication failed!";
        // if (data && data.error && data.error.message) {
        //   errorMessage = data.error.message;
        // } 이렇게 에러메시지를 활용할 수 있다.

        throw new Error(errorMessage);
      });
    }
  })
  .then((data) => {})
  .catch((error) => {alert(error.message)});
```

<br><br>
코드를 재구성해 throw new Error를 통해 오류메시지를 catch블록으로 보낸 것이다.  
`return res.json().then((data) => { `
이 promise와 외부 프로미스는 거절될 것이다.  
그러면 `.then((data) => {})`여기로 와서 오류가 없어 요청 성공시 도달하여 then블록을 수행할 것이다.

<br><br>
로그인과 가입의 로직은 같다. URL만 다르다.  
앱으로 돌아가서 가입이 작동하는지 확인하여보자.  
data를 console.log해놓았기 때문에 console.log에 한 객체가 뜬다.  
<br><br>
여기서 중요한 것은 idToken이다.  
인증 토큰이자 인코딩 된 데이터가 담긴 긴 문자열로  
파이어베이스 서버가 생성하였기 때문에  
파이어베이스 서버만 확인할 수 있다.

<br><br>
이렇게 idToken이 담긴 응답을 받았다면 사용자가 유효한 자격 증명을 제공한 것이다.  
그렇지 않았다면 오류 메시지가 떴을 것이다.  
이제 idToken을 보호된 리소스에 후속 요청을 보낼 때 첨부할 수 있을 뿐 아니라  
사용자가 인증됐다는 증명으로 활용해 적절하게 UI를 업데이트 할 수 있다.

<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
