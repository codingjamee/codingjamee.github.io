---
title: "#284[udemy react 강의노트]리액트 Switch 및 exact"
excerpt: "Switch, exact 란 ?"
published: true
categories:
  - Blog
tags:
  - [Blog, routing, react, Switch, exact]

toc: true
toc_sticky: false

date: 2022-10-24
last_modified_at: 2022-10-24
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
