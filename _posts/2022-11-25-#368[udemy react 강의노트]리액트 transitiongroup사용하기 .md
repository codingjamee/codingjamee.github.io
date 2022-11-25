---
title: "#368[udemy react 강의노트]리액트 transitiongroup사용하기"
excerpt: "React animation"
published: true
categories:
  - Blog
tags:
  - [React, transitiongroup]

toc: true
toc_sticky: false

date: 2022-11-25
last_modified_at: 2022-11-25
---

# 리액트 transitiongroup사용하기

<br><br>
그럼 리액트의 transition-group에 대해서 한번 살펴보자  
구글링을 해서 이 패키지에 대한 github 저장소를 찾을 수 있다.  
그런데 이것은 공식적인, 리액트 팀에서 배포한 리액트 패키지가 아니지만  
더 넓은 리액트 커뮤니티에서 생성된 패키지로,  
요소들이 DOM에 추가되거나 제거될 때 자연스러운 애니메이션을 적용할 수 있게 해준다.

<br><br>
메인 다큐멘테이션을 보면 설치, 작동방법을 볼 수 있다.  
어떤것을 할 수 있는지 확인해 볼 수 있다.  
강의에서도 확인할 수 있을 것이다.  
<br><br>

# 설치방법

<br><br>

```
# npm
npm install react-transition-group --save
```

<br><br>
설치를 완료한 다음 npm start를 다시 시작한다.  
<br><br>

# 실행

<br><br>
실행을 해보기 위해 버튼을 하나 추가한다.  
이름은 toggle로 한다.

<br><br>

```jsx
render() {
  return (
    <div className="App">
      <h1>React Animations</h1>
      <button>toggle</button>
```

<br><br>
그 Toggle 밑에는 div를 렌더링 한다.  
먼저 showBlock을 state에 추가해주고  
초기값은 false로 설정한다.

<br><br>

```jsx

class App extends Component {
  state = {
    modalIsOpen: false,
    showBlock: false,
  };
```

<br><br>
그리고 여기서, 버튼 아래에는 {this.state.showBlock}을 확인한다.  
만약 참이라면 `<div>`를 추가할 것이고 false면 null을 렌더한다.

<br><br>

```jsx
  render() {
    return (
      <div className="App">
        <h1>React Animations</h1>
        <button>button</button>
        {this.state.showBlock ? <div></div> : null}
```

<br><br>
div에는 인라인스타일을 준다.  
중첩 중괄호로 자바스크립트 객체를 전달한다.  
<br><br>

```jsx
  render() {
    return (
      <div className="App">
        <h1>React Animations</h1>
        <button>button</button>
        {this.state.showBlock ? (
          <div
            style={{ backgroundColor: "red", width: 100, height: 100 }}
          ></div>
        ) : null}
```

<br><br>
이것을 버튼에 연결시킨다.  
메서드 안에서는 this.setState()를 실행시킨다.  
함수형으로 해서 prevState를 가져온다.  
<br><br>

```jsx
  render() {
    return (
      <div className="App">
        <h1>React Animations</h1>
        <button
          onClick={() =>
            this.setState((prevState) => ({ showBlock: !prevState.showBlock }))
          }
        >
          button
        </button>

        {this.state.showBlock ? (
          <div
            style={{ backgroundColor: "red", width: 100, height: 100 }}
          ></div>
        ) : null}
```

<br><br>
그리고 약간의 수정을 해준다.  
button style을 적용하기 위해 className="Button"으로 하고  
div태그의 inline style에 margin: "auto"를 추가해준다.  
<br><br>

```jsx
render() {
    return (
      <div className="App">
        <h1>React Animations</h1>
        <button
          className="Button"
          onClick={() =>
            this.setState((prevState) => ({ showBlock: !prevState.showBlock }))
          }
        >
          toggle
        </button>
        <br />
        <br />
        <br />
        <br />
        {this.state.showBlock ? (
          <div
            style={{
              backgroundColor: "red",
              width: 100,
              height: 100,
              margin: "auto",
            }}
          ></div>
```

<br><br>
이렇게 하고 저장해주면, 이 Toggle 버튼이 이 빨간 블록을 보였다 안보였다  
토글하는 것을 볼 수 있다.  
이제 이 버튼을 사용해서 이것저것 해볼 수 있다.  
우리가 방금 설치한 react-transition-group 패키지와 말이다.  
문서를 살펴보자.

<br><br>

# Transition 컴포넌트

<br><br>
이 패키지는 한가지 중요한 컴포넌트를 내보내는 것을 알 수 있다.  
사실 이것보다 더 많은 것을 내보내지만, 이것부터 시작해보자.  
바로 Transition 컴포넌트다.

<br><br>
App.js에서 이것을 import한다.  
<br><br>

```jsx
import Transition from "react-transition-group";
```

<br><br>
이제 이 `<Transition>` 컴포넌트로 우리가 애니메이션을 적용하고 싶은 것을 감싸준다  
우리의 경우에는 이 `<div>`다.  
임시로 여기 렌더링 할지 말지를 조건문을 제거한다.

<br><br>

```jsx
<Transition>
  <div
    style={{
      backgroundColor: "red",
      width: 100,
      height: 100,
      margin: "auto",
    }}
  ></div>
</Transition>
```

<br><br>
이것 만으로는 별로 하는 것이 없지만,  
이제 이 Transition 컴포넌트를 사용해서  
특히 이 요소의 애니메이션 등의  
화면표시 엘리먼트들을 제어할 수 있다
<br><br>
<br><br>

# Transition in 속성

<br><br>
document를 보면 in 속성을 설정해줌으로써 할 수 있는 것을 알 수 있다.  
이 in 속성이 Transition 컴포넌트에 감싸진 요소들을 보일지 말지 결정한다.

<br><br>

```jsx
<Transition nodeRef={nodeRef} in={inProp} timeout={duration}>

```

<br><br>
그리고 Transition 컴포넌트는  
entering, entered, exiting, exited 이렇게  
네가지 내부 state를 관리한다.  
그리고 이것을 나열해서 요소들이 어떻게 보여질 것인지도 결정할 수 있다

<br><br>

## in 속성 사용법

<br><br>
in 속성을 Transition 컴포넌트 안에 추가한다.  
in은 감싸진 요소가 보이게 할지 여부를 결정한다
이 Transition 컴포넌트 속의 `<div>`가 렌더링 되도록 해야한다.

<br><br>

```jsx
<Transition in={this.state.showBlock}>

```

<br><br>

<br><br>

# Transition의 timeout 속성

<br><br>
Transition 컴포넌트 안에 설정해 주어야하는 또다른 중요한 속성이 있는데,  
바로 timeout이다.  
timeout은 단순한 시간 값으로 애니메이션이 재생되는 시간을 결정한다.  
<br><br>
또는 정확히 말하면, Transition만으로는 아무것도 재생하는게 없고,  
단지 이 네가지 상태를 관리하는 것이기 때문에  
(entering, entered, exiting, exited)  
timeout은 단순히 entering에서 entered로 그리고  
eixiting에서 exited로 전환할 때 시간이 얼마나 걸리는지 결정한다.

<br><br>
그래서 이들 사이에서 애니메이션을 적용하는 것이다.  
timeout을 300이라고 해보자. 단위는 밀리초다.  
이렇게 해주면, 이 Transition 컴포넌트 안에서 중괄호로  
동적인 무언가를 렌더링할 수 있다.

<br><br>

```jsx
<Transition in={this.state.showBlock} timeout={300}>

```

<br><br>
왜냐하면 이 안에 실제로 함수를 렌더링해야하기 때문이다.  
이것이 결국에는 Transition 컴포넌트가 우리에게 반환하는 것이 된다.  
우리가 이 함수 안에 사용할 수 있는 값들 또는 정확히 말하자면  
단 하나의 값은 state다.
따라서 가장 간단한 형태로 우리가 할 수 있는 것은,  
단순히 화살표 함수를 사용하는 것이다.  
이것이 우리가 중괄호가 필요한 이유다.

<br><br>

```jsx
<Transition in={this.state.showBlock} timeout={300}>
  {state => }
  <div
    style={{
      backgroundColor: "red",
      width: 100,
      height: 100,
      margin: "auto",
    }}
  ></div>
</Transition>
```

<br><br>
이 화살표 함수는 우리가 렌더링 하길 원하는 jsx를 반환 한다.  
이것이 반환하는 것은 state를 출력하는 문단이 될 수 있다.  
jsx코드 안이기 때문에 중괄호로 감싸줘야 한다.

<br><br>

```jsx
{
  (state) => <p>{state}</p>;
}
```

<br><br>
그 아래의 div태그는 임시적으로 잘라낸다.  
제거하기 보다는 메모리에 유지되도록 한다.

<br><br>

```jsx
<div
  style={{
    backgroundColor: "red",
    width: 100,
    height: 100,
    margin: "auto",
  }}
></div>
```

<br><br>

```jsx
<Transition in={this.state.showBlock} timeout={300}>
  {(state) => <p>{state}</p>}
</Transition>
```

<br><br>
이렇게 해주고 저장한 다음 다시 애플리케이션으로 돌아가면  
여기 exited가 있는 것을 볼 수 있다.  
여기 문단에 그냥 텍스트를 출력하도록 해서 그렇다.  
이것은 state 값을 출력해야하는 건데, 이 Transition 컴포넌트에 의해서  
우리한테 주어진 것이다.

<br><br>
<br><br>
그럼 다른 값들, 이 state 값 또는 인자가 어떤 것이 있을 수 있는지 본다.  
Toggle을 클릭하면 entering이었다가 다음엔 entered로 바뀐다.  
다시 토글해보면 처음 exiting에서 exited로 바뀐다.
이것이 Transition 컴포넌트가 우리에게 해줄 수 있는 전부다

<br><br>
우리가 가지는 이 state 인자를 관리하는 것이다.  
Transition 컴포넌트의 자식으로 전달하는 이 함수 안의 state다.

<br><br>

```jsx
{
  (state) => <p>{state}</p>;
}
```

<br><br>
그리고 이 관리되는 state 속성에 우리가 설정한 timeout 타이밍을 반영한다  
이렇게 하고, 물론 이제 여기 안에 다시 div를 렌더링 하도록 할 수 있다.  
사실 이 안에 이렇게는 아니고 대신 state를 인자로 받는 함수에 해준다.

<br><br>

```jsx
<Transition in={this.state.showBlock} timeout={300}>
  {(state) => (
    <div
      style={{
        backgroundColor: "red",
        width: 100,
        height: 100,
        margin: "auto",
      }}
    ></div>
  )}
</Transition>
```

<br><br>
state를 가지는 이 함수 안에서 div를 반환하도록 한다  
이제 우리는 state가 entering, entered, exiting, exited인 것을 알기 때문에  
이것을 활용해서 이제 스타일을 state에 따라서 조정할 수 있다.
예를 들어서 여기에 이 요소의 투명도를 설정할 때 아래와 같이 해준다.

<br><br>

```jsx
<div
  style={{
    backgroundColor: "red",
    width: 100,
    height: 100,
    margin: "auto",
    opacity: state === "exited" ? 0 : 1,
  }}
></div>
```

<br><br>
이제 보면 div가 여전히 화면에 있는 것을 확인할 수 있다,  
보이지는 않지만 자리를 차지하고 있다.  
Toggle을 누르면 다시 보이고, 다시 Toggle을 누르면 1초 후에 사라진다.  
여전히 지금 애니메이션은 없다.  
하지만 이 Transition 컴포넌트를 어떻게 사용할지는 볼 수 있다.

<br><br>
분명히 애니메이션이 있다면 훨씬 좋을 것이다  
애니메이션을 적용하려면 약간 수정해주어야 한다.
transition 속성을 추가해준다.  
all을 추가할 수도 있지만 지금은 opacity만 추가한다.  
timeout에서 사용했던 것과 똑같이 1초동안 효과를 주게 하고,  
ease-out도 쓴다. 모든 것이 문자열로 들어가야 한다.  
이것은 문자열로 작성해야 한다.  
<br><br>

```jsx
<div
  style={{
    backgroundColor: "red",
    width: 100,
    height: 100,
    margin: "auto",
    transition: "opacity 1s ease-out",
  }}
></div>
```

<br><br>
저장해주고 이제 Toggle을 누르면  
애니메이션이 적용되면서 나타나고  
적용되면서 사라진다.

<br><br>
이제 중요한 사실은 애니메이션을 Transition으로 제어한다는 것이다.  
하지만 이것은 여전히 항상 DOM에 존재한다.  
그건 Transition 컴포넌트에 더 많은 props들을 추가함으로써 조정할 수 있다  
<br><br>

# Transition 컴포넌트의 mountOnEnter와 unmountOnExit 속성

<br><br>
mountOnEnter를 추가할 수 있다, 이것은 단순한 불리언 prop이다.  
만약 in 속성이 참이면 DOM에다 wrap 요소를 추가하고,  
반대로 unmountOnExit을 추가하면 false인 경우 DOM에서 완전히 제거한다

<br><br>

```jsx
  <Transition
    in={this.state.showBlock}
    timeout={1000}
    mountOnEnter
    unmountOnExit
  >
```

<br><br>
이제 빈 공간이 없다.  
toggle을 클릭하면 다시 생긴다.

<br><br>
하지만 여전히 애니메이션은 없다.  
우리가 이것을 제어하는 방법 때문이다.  
우리가 변경하는 것은 단지 opacity 뿐이다.  
state가 ‘exited’일 때인데 너무 늦다.  
이것을 ‘exiting’으로 바꾸면,  
우리가 없애기 위해서 Toggle 버튼을 누르는 바로 처음에 입력된다.  
그럼 이제 효과가 없어진다

<br><br>

```jsx
<div
  style={{
    backgroundColor: "red",
    width: 100,
    height: 100,
    margin: "auto",
    transition: "opacity 1s ease-out",
    opacity: state === "exting" ? 0 : 1,
  }}
></div>
```

<br><br>
이제 전에 있던 문제를 수정했다  
여전히 보여줄 때는 즉각적으로 애니메이션이 나타난다.  
이 부분의 스타일은 설정하지 않았기 때문이다.  
하지만 없앨 때는 이 네가지 다른 state의 이점을 활용했다.  
사실 두가지 다른 state다.  
요소를 없앨 때는 exiting과 exited다.

<br><br>
우리가 exiting을 시작하자 마자는, 우리가 이미 투명도를 0으로 설정하고  
애니메이션을 적용하지만 전체 DOM에서는 이 timeout이 끝날때 까지  
애니메이션이 실제로 제거되지 않는다.  
이제는 Transition 컴포넌트의 도움으로 리액트 한테 이 요소는  
이 주어진 시간이 지난 후에만 제거 하라고 명령하는 것이다.
