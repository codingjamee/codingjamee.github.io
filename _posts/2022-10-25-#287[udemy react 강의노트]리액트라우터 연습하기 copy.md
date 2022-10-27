---
title: "#296[udemy react 강의노트]쿼리 매개변수 사용하기"
excerpt: "리액트 라우터 연습하기"
published: true
categories:
  - Blog
tags:
  - [Blog, routing, react, Switch, exact, redirect, useParams, Prompt]

toc: true
toc_sticky: false

date: 2022-10-25
last_modified_at: 2022-10-26
---

## 라우터가 작동하는 방식

<br><br>
App에서 라우트를 정의하고 있지만,
기본적으로 이러한 라우트는 이 경로 중 하나만 로드되도록 parse되지 않는다.
라우트는 현재 경로와 일치하는 모든 라우트가 로드된다.  
<br><br>
내가 원하고자 하는 방법으로 하려면  
Switch 컴포넌트를 import하여 라우트 컴포넌트를 래핑하면 된다.  
그러면 한번에 하나만 매칭되는 경로가 로드된다.
<br><br>

```jsx
<Switch>
  <Route path="/welcome">
    <Welcome />
  </Route>
  <Route path="/products" exact>
    <Products />
  </Route>
  <Route path="/products/:productId">
    <ProductDetail />
  </Route>
</Switch>
```

<br><br>

가장먼저 매칭되는 라우트가 될 것이다.  
원하는 바는 아니지만, 겹치는 라우트들이 한꺼번에 보여지진 않았다.  
우리가 원하는 프로덕트 디테일 컴포넌트를 로드하게 하려면  
<br><br>

1. 위에서 아래로 리액트 라우터가 로드하고 있으므로, 위치를 바꾸면 된다.  
   일치하는 항목을 찾는다는 것은 전체경로가 아니라  
   경로의 시작부분과 일치할 때를 의미한다.  
   그러므로 우리가 아까 디테일 페이지를 클릭해도 products로 시작된  
   화면에서 디테일 페이지로 넘어가지 않았던 것이다.  
   <br><br>

2. 다른 prop을 추가하면 된다.  
    exact 프롭이다. 이것은 리액트 라우터에 정확히 일치하는 경우에만  
    일치 여부를 알려주는 것이다.  
   <br><br>

# 중첩 라우트

우리가 뭘 만들고 있는지에 따라 몇 가지 다른 기본 페이지가 아닌 페이지  
내부에도 라우트가 필요한 경우가 있다.  
라우트를 정의하는 것은 한 곳에서 할 필요가 없다.  
현재 활성화된 컴포넌트에 Route가 있는 경우에는  
react-router-dom에 의해 평가된다.

```jsx
import { Route } from "react-router-dom";

const Welcome = () => {
  return (
    <section>
      <h1>The Welcome Page</h1>
      <Route></Route>
    </section>
  );
};

export default Welcome;
```

<br><br>

위와같이 Welcome 컴포넌트에 있는 경우면 Welcome 컴포넌트가  
활성화 될 때 Route가 평가된다.  
Route에 컨텐츠를 보여주기 위해 path를 설정하면 이 라우트는  
Welcome페이지에 있지 않으므로 활성화될 수 없다.

<br><br>

```jsx
import { Route } from "react-router-dom";

const Welcome = () => {
  return (
    <section>
      <h1>The Welcome Page</h1>
      <Route path="/products"></Route>
    </section>
  );
};

export default Welcome;
```

<br><br>
반면, 아래와 같이 하면 활성화 된다.  
welcome페이지에 있기 때문이다.
<br><br>

```jsx
<Route path="/welcome/new-user"></Route>
```

<br><br>
여기 안에 다른 컴포넌트를 사용하거나 일부 JSX코드를 정의할 수 있다.  
<br><br>
<br><br>

# Redirect

```jsx
     <Switch>
         <Route path="/" exact>
           <Redirect to="/welcome"/>
```

Switch 안에 /만 있는 path가 있는 Route를 만들고 exact 프롭을 넣어준다.  
 그렇게 하지 않으면 모든 페이지가 이 path로부터 시작될 것이기 때문이다.  
 그리고 이 Route 안에 Redirect 컴포넌트를 넣어준다.
to 프롭 안에는 /welcome을 작성해준다.  
 그러면 아무것도 아닌 url에 방문했을 경우, /welcome 페이지로  
 방문하게 리다이렉트 해줄 것이다.

<br>
<br>
<br>

# 동적 중첩 라우트

만일 동적으로 중첩 라우트를 만들고 싶다면 아래와 같이 사용하면 된다.

<br>
<br>

```jsx
import { Route, useParams } from "react-router-dom";

const QuoteDetail = () => {
  const params = useParams();

  return (
    <>
      <h1>Quote Detail Page</h1>
      <p>{params.quoteId}</p>
      <Route path={`/quotes/${params.quoteId}/comments`}>
        <p>Some comments</p>
      </Route>
    </>
  );
};

export default QuoteDetail;
```

<br>
<br>

Route 안에 path를 {} 로 묶은 뒤 백틱을 이용하면 된다.  
또는 아래의 방법도 있다.
<br>
<br>

```jsx
import { Route, useParams } from "react-router-dom";

const QuoteDetail = () => {
  const params = useParams();

  return (
    <>
      <h1>Quote Detail Page</h1>
      <p>{params.quoteId}</p>
      <Route path="/quotes/:quoteId/comments">
        <p>Some comments</p>
      </Route>
    </>
  );
};

export default QuoteDetail;
```

<br>
<br>

# 찾을 수 없음 페이지 추가하기

<br>
<br>
아무것도 찾을 수 없는 URL에 들어가보면 아무것도 나오지 않는다.   
즉, localhost:3000/hello 로 들어가보면 아무것도 나오지 않는다.     
이제 일치하지 않는 링크를 인식하여 찾을 수 없음 페이지를 렌더링 해보자.   
접근 방식은 일반적으로 마지막에 하나의 Route를 추가하는 것인데,  
들어오는 모든 요청과 일치 여부를 확인하는 것이다.   
path에 별을 추가한다. 
이 문자는 리액트 라우터에 모든 경로, 모든 URL이 이 라우트와   
일치해야 한다는 신호다.  
실제 라우트 중 하나에 대한 요청을 소비하지 않도록 이 라우트는 마지막에 위치해야 한다.  
이 시점까지 일치하는 항목이 없으면, 모든 URL과 이 라우트가 일치되며, 그러면 Not Found 페이지를 렌더링 하게 된다.

<br>
<br>

```jsx
<Route path="*">
  <NotFound />
</Route>
```

<br>
<br>

# 프로그램적으로 탐색하기 useHistory 훅을 사용하여

<br>
<br>

Add a Quote 버튼을 눌렀을 때 전송작업이 완료되면  
All Quotes페이지로 가게 만들고 싶다.  
페이지에를 전환하고 싶다고 리액트 라우터에 알리는 방법은 무엇일까?

useHistory 훅을 사용하는 것이다.  
import 한 다음, history 상수에 할당한다.  
상수 히스토리는 히스토리 변경 작업을 트리거 하는데 사용할 수 있는 히스토리 객체를 확장한다.  
기록 페이지를 변경하는 것은 예를 들어 우리가 새 페이지를 추가하거나  
새 페이지로 이동하면 여기 페이지 스택에 새 페이지를 푸시하는 push 메소드를  
사용하여 탐색 가능하므로, 페이지 기록에 새 페이지가 표시된다.  
또는 현재 페이지를 대체하는 replace 메서드로 탐색할 수도 있다.  
푸시를 사용하면 백버튼을 사용하여 원래 페이지로 돌아갈 수 있고  
replace 버튼을 사용하면 그럴수 없다.  
replace는 리디렉션과 같고 push는 새 페이지를 추가하는 것이다.

<br>
<br>

```jsx
import { useHistory } from "react-router-dom";

import QuoteForm from "../components/quotes/QuoteForm";

const NewQuote = () => {
  const history = useHistory();

  const addQuoteHandler = (quoteData) => {
    console.log(quoteData);

    history.replace("/quotes");
  };
  return <QuoteForm onAddQuote={addQuoteHandler} />;
};

export default NewQuote;
```

<br>
<br>

# 실수로 작성 화면을 나가는 것 방지하기

<br>
<br>
form이 포커스를 얻을 때를 결정한다.    
그리고 사용자가 페이지를 떠나려고 하면 경고를 표시한다.

그러려면 useState를 사용하여 isEntered 를 세팅하고,  
form 태그에 onFocus 속성을 사용하여 formFocusdHandler 함수를 포인트 한다.  
formFocusedHandler에서는 setIsEntered(true)로 설정해준다.
사용자가 폼에서 작업하고 있다는 상태를 알게 된다.

<br>
<br>

```jsx
import { useRef, useState } from "react";

import Card from "../UI/Card";
import LoadingSpinner from "../UI/LoadingSpinner";
import classes from "./QuoteForm.module.css";

const QuoteForm = (props) => {
const [isEntered, setIsEntered] = useState(false);
const authorInputRef = useRef();
const textInputRef = useRef();

function submitFormHandler(event) {
event.preventDefault();

    const enteredAuthor = authorInputRef.current.value;
    const enteredText = textInputRef.current.value;

    // optional: Could validate here

    props.onAddQuote({ author: enteredAuthor, text: enteredText });

}

const formFocusedHandler = () => {
setIsEntered(true);
console.log("Focus!");
};

return (
<Card>
<form
        onFocus={formFocusedHandler}
        className={classes.form}
        onSubmit={submitFormHandler}
      >

```

<br>
<br>

사용자가 양식 페이지를 떠나려고 할 때 경고를 표시하는 것은 곧바로 라우트 되는 것이다.  
prompt컴포넌트를 react-router-dom 에서 가져온다.  
이 컴포넌트는 우리가 다른 곳으로 이동할 때 자동으로 감시한다.  
그리고 특정 조건이 충족되면 떠나기 전에 경고를 준다.

```jsx
return (
    <>
      <Prompt />
```

<br>
<br>

jsx 코드에 Prompt 컴포넌트를 렌더링해준다.  
Prompt 컴포넌트는 두가지 prop을 필요로 한다.
<br>
<br>

1. when 프롭  
   사용자가 URL을 변경하는 경우, 이 프롬프트가 표시되어야 하는지 여부를  
   찾기 위해 true또는 false를 전달하는 프롭이다.
   isEntering일 경우 표시한다고 설정한다.  
   <br>
   <br>

```jsx
return (
  <>
    <Prompt when={isEntering} />
```

<br>
<br>

2. message 프롭  
    보여주고 싶은 텍스트가 포함된 문자열을 반환해야 하는 함수를 실제로 취하는 프롭이다.
   이는 함수의 일종으로 봐야하는데, 우리가 가고자 하는 페이지에 대한 정보를 담고 있는 어떤 위치 객체를 얻기 때문이다.
   그러면 메시지에 가고자 하는 경로를 포함할 수 있다.

<br>
<br>

```jsx
  return (
    <>
      <Prompt
        when={isEntering}
        message={(location) =>
          "Are you sure you want to leave? All your data will be lost"
        }
      />
```

<br>
<br>

그런데 Add Quote버튼을 클릭해도 그 경고창이 뜬다. 어떻게 해결해야 할까?
Add Quote 버튼을 클릭하면 함수를 실행시켜 isEntering 스테이트를  
false로 변경시키면 된다.

```jsx
<div className={classes.actions}>
  <button onClick={finishEnteringHandler} className="btn">
    Add Quote
  </button>
```
