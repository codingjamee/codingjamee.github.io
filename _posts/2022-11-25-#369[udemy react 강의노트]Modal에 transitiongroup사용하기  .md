---
title: "#369[udemy react 강의노트]Modal컴포넌트에 transitiongroup사용하기"
excerpt: "React animation"
published: true
categories:
  - Blog
tags:
  - [React, transitiongroup, Modal]

toc: true
toc_sticky: false

date: 2022-11-25
last_modified_at: 2022-11-25
---

# Modal컴포넌트에 Transition 컴포넌트 사용하기

<br><br>
사용한 Transition 컴포넌트 밑에 Transition컴포넌트를 추가해준다.  
그 사이에 함수를 입력해준다.  
<br><br>

```jsx
  <Transition>
    {state => }
  </Transition>
```

<br><br>
state 인자를 갖는데, 이 state 인자에는 'entering', 'entered' ,  
'exiting', 'exited' 네 가지가 있었다.  
이제 이 사이에 무엇이 렌더링 되어야 하는지 정의해준다.  
Modal jsx코드를 cut해서 넣어준다.  
<br><br>

```jsx
<Transition>
  {(state) => <Modal show={this.state.modalIsOpen} closed={this.closeModal} />}
</Transition>
```

<br><br>
아래의 삼항연산자는 삭제해준다.  
Backdrop에 대한 확인 부분은 놔둔다.  
왜냐하면 지금처럼 애니메이션 없이 Bakdrop을 보이거나  
숨길 수 있게 하고 싶기 때문이다.  
따라서 그냥 이렇게 `<Backdrop show/>` 설정해줄 수 있습니다  
그러면 항상 참일 수 있다.
더이상 보이지 않기를 원하면 어쨌든 제거될 수 있다.

<br><br>

```jsx
{
  this.state.modalIsOpen ? <Backdrop show={this.state.modalIsOpen} /> : null;
}
```

<br><br>
다시 Modal로 돌아와서 여기서 Transition을 조정한다.
in프로퍼티를 this.state.modalIsOpen으로 설정한다.  
<br><br>

```jsx
<Transition in={this.state.modalIsOpen}>
  {(state) => <Modal show={this.state.modalIsOpen} closed={this.closeModal} />}
</Transition>
```

<br><br>
modalIsOpen이 참이면 함수에 반환되는 요소가 보여지거나  
‘entered’로 처리된다.  
timeout과 함께 적용한다.

<br><br>

```jsx
<Transition in={this.state.modalIsOpen} timeout={300}>
  {(state) => <Modal show={state} closed={this.closeModal} />}
</Transition>
```

<br><br>
이제 할 수 있는 것은 state를 전달할 수 있다.  
여기서 state라는 것은 이 애니메이션 state에 저장된 인자를 말한다.  
Modal 안에 show={}라는 속성 안에다 전달한다.

```jsx
{
  (state) => <Modal show={state} closed={this.closeModal} />;
}
```

<br><br>
그러면 Modal.js 안에서 우리는 이것을 사용할 수 있다.
<br><br>

props.show가 ‘entering’과 일치 하는지를 확인하여  
Toggle modal을 선택했을 때의 초깃값을 설정해준다.  
초깃값은 ModalOpen 애니메이션을 실행할 것이고,  
그렇지 않으면, props.show가 'exiting'인지 확인하여  
ModalClosed 클래스를 추가하여 준다.  
그것도 아니라면 어떤 CSS 클래스도 추가하지 않는다.

<br><br>
Modal.js 파일
<br><br>

```jsx
const modal = (props) => {
  const cssClasses = [
    "Modal",
    props.show ? "ModalOpen" : props.show === "exiting" ? "ModalClosed" : null,
  ];

```

<br><br>
이제 이것을 저장한 다음 다시 애플리케이션으로 돌아가서,  
Open Modal을 클릭하면 애니메이션이 재생되는 것을 볼 수 있다
그리고 App.js에서 아까의 Transition 컴포넌트에  
mountOnEnter와 unmountOnExit를 추가한다.

<br><br>
Open Modal을 클릭하면 애니메이션이 재생되고, 사라지게 하는 것도  
우리는 지금 이 Transition 속성의 이점을 활용해서  
DOM에서 제거가 되기 전에 시간적 지연을 추가해 주었다.  
그렇게 해서 애니메이션이 재생될 수 있는 시간을 버는 것이다.

<br><br>
이는 우리가 있게 되는 state 때문이다.  
Modal 컴포넌트 안에서 이 state를 우리가 사용해 주었기 때문이다.  
<br><br>
지금 우리가 있는 곳을 확인한다면, ‘exiting’이다.  
DOM에서 제거되기 직전의 상태다.  
그렇기 때문에 전과는 다르게
너무 늦게 일어나지 않는 것이다.

<br><br>

# Transition 컴포넌트의 중첩사용

<br><br>
이것을 우리가 원하는 컴포넌트에 중첩시킬 수도 있다는 것이다.  
따라서 Transition을 App.js 파일에서 Modal.js 파일로도 옮길 수 있다.  
그리고 간단히 그 안에다 Modal 전체를 `<Transition>`으로 감싸줄 수 있다
import도 해준다.  
<br><br>
Modal.jsx파일
<br><br>

```jsx
import React from "react";
import { Transition } from "react-transition-group";

import "./Modal.css";

const modal = (props) => {
  const cssClasses = [
    "Modal",
    props.show === "entering"
      ? "ModalOpen"
      : props.show === "exiting"
      ? "ModalClosed"
      : null,
  ];
  return (
    <Transition
      mountOnEnter
      unmountOnExit
      in={this.state.modalIsOpen}
      timeout={1000}
    >
      <div className={cssClasses.join(" ")}>
        <h1>A Modal</h1>
        <button className="Button" onClick={props.closed}>
          Dismiss
        </button>
      </div>
    </Transition>
  );
};
export default modal;
```

<br><br>
그럼 이제 코드만 업데이트 하면 된다.  
기본적으로, 다시 말하지만 Modal은 항상 우리 App.js 파일에 존재한다  
그렇기 때문에 show={}에는 다시 this.state.modalIsOpen으로 설정해준다.  
그리고 Modal안의 show는 이제 다르게 사용되어야한다,

<br><br>

```jsx
{
  (state) => <Modal show={this.state.modalIsOpen} closed={this.closeModal} />;
}
```

<br><br>
이제 show는 아래의 in={} 속성을 제어하는 속성이다.

<br><br>
Modal.js파일
<br><br>

```jsx
<Transition
  mountOnEnter
  unmountOnExit
  in={this.state.modalIsOpen}
  timeout={1000}
>
```

<br><br>
그렇기 때문에 in={}에는 props.show에 바인딩 해야한다.  
그럼 이제다시 true 또는 false가 될 것이다.

<br><br>

```jsx
<Transition
  mountOnEnter
  unmountOnExit
  in={props.show}
  timeout={1000}
>
```

<br><br>
그러므로, 우리가 여기에서 cssClasses를 제어할 때 함수 안에,  
Transition 컴포넌트 사이에서 해줘야한다  
태그를 열고 닫고, state를 입력해준다.  
state는 Transition 컴포넌트에 의해 관리된다.
이 div는 Modal을 보유하고 있다.  
<br><br>

```jsx
return (
  <Transition mountOnEnter unmountOnExit in={props.show} timeout={1000}>
    {(state) => (
      <div className={cssClasses.join(" ")}>
        <h1>A Modal</h1>
        <button className="Button" onClick={props.closed}>
          Dismiss
        </button>
      </div>
    )}
  </Transition>
);
```

<br><br>
그렇게 되면 “ModalOpen”과 “ModalClosed”를  
cssClasses에 연결해줄 필요가 없다  
따라서 그냥 “Modal”만 있는 배열이 된다
<br><br>

```jsx
const modal = (props) => {
  const cssClasses = ["Modal"];
```

<br><br>

그 대신 밑의 코드의 Transition 컴포넌트 안에서,  
cssClasses를 바꿔 주어야한다.  
그리고 조건부로 애니메이션을 적용해준다  
따라서 이것의 구조를 무언가를 반환하지 않도록  
본문이 있는 함수안의 함수로 바꾼다.  
왜냐하면 이전에 jsx를 반환하도록 했다.  
이것을 여기 return() 함수 안으로 넣어준다.

<br><br>

```jsx
<Transition mountOnEnter unmountOnExit in={props.show} timeout={1000}>
  {(state) => {
    return (
      <div className={cssClasses.join(" ")}>
        <h1>A Modal</h1>
        <button className="Button" onClick={props.closed}>
          Dismiss
        </button>
      </div>
    );
  }}
</Transition>
```

<br><br>
물론 cssClasses 또한 조정해줘야한다.  
const cssClasses 부분을 끌어다가 함수 안에 놓는다

<br><br>

```jsx
const modal = (props) => {
  return (
    <Transition mountOnEnter unmountOnExit in={props.show} timeout={1000}>
      {(state) => {
        const cssClasses = ["Modal"];
        return (
          <div className={cssClasses.join(" ")}>
            <h1>A Modal</h1>
            <button className="Button" onClick={props.closed}>
              Dismiss
            </button>
          </div>
        );
      }}
    </Transition>
  );
};
```

<br><br>
그리고 설정을 그냥 “Modal”만이거나  
state가 “entering”과 일치하는지 확인해서,  
참이면 “ModalOpen”이라고 한다.  
<br><br>
그렇지 않을 경우, state가 “exiting”과 일치한다면,  
(지금 이 “entering”과 “exiting” 클래스는  
우리가 애니메이션을 시작하는 시점에  
바로 이동하도록 하려고 사용하는 것)
그래서 “exiting”과 일치할 경우,  
“ModalClosed”라고 하고 그외의 경우엔 null로 설정한다.

<br><br>

```jsx
{(state) => {
  const cssClasses = [
    "Modal",
    state === "entering"
      ? "ModalOpen"
      : state === "exiting"
      ? "ModalClosed"
      : null,
  ];
```

<br><br>
이전과 같은 논리의 흐름이지만 Modal 안에서 이루어지고 있다.  
실제로 해보면, 여전히 이전과 동일하게 동작하게 된다.
