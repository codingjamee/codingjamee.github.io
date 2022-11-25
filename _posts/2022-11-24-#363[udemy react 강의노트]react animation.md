---
title: "#363[udemy react 강의노트]React animation"
excerpt: "React animation"
published: true
categories:
  - Blog
tags:
  - [NextJS, deploy, Vercel]

toc: true
toc_sticky: false

date: 2022-11-24
last_modified_at: 2022-11-24
---

# React animation

<br><br>
이번 섹션 강의에서는 리액트 앱과 컴포넌트들에 애니메이션을 적용하는 방법에 대해서 알아볼 것이다.  
정적인 스타일과 컴포넌트에서 동적인 스타일과 컴포넌트로 넘어가는 방법에 대해서 배워본다.  
매끄러운 전환과 단순하지만 보기 좋은 애니메이션으로  
더 나은 사용자 경험으로 이어질 수 있도록 한다.

<br><br>
이를 위해서 우리는 css 전환 및 에니메이션에 대해 알아볼 것이다.  
그리고 어떤 경우에 이것들 만으로 충분한지와 왜 어떤 경우엔 이것들 만으로는 부족한지  
왜 오직 css 에니메이션 만으로는 때때로 해결할 수 없는지에 대해 알아볼 것이다.

<br><br>
그런 다음, 우리가 사용할 수 있는 몇몇의 타사 라이브러리에 대해서도 알아본다.  
우리는 한 특정 타사 라이브러리를 사용할 것이다.  
정확히 말하면 이것을 사용하면 리액트 컴포넌트에 에니메이션을 적용할 수 있다.  
여기까지가 이번 과정의 모듈에서 다뤄볼 것들 이다.

<br><br>
이번 모듈에서 프로젝트 데모는, 클래스 기반 컴포넌트를 사용합니다  
이유는 단순히 강사가 이프로젝트를 이미 이 과정의 구 버전에서 만들었기 때문이다.  
그 때는 클래스형 컴포넌트만을 사용했다. 하지만 이 프로젝트를 진행하는 것은 꽤 수월할 것이다.  
<br><br>
과정의 마지막에는 몇개의 리액트 컴포넌트와 함께 리액트 정규 프로젝트가 있다.  
구체적으로 이 프로젝트에서는 세개의 리액트 컴포넌트가 있는데, Backdrop, List, Modal 컴포넌트다.  
이 프젝트에서 모든 디펜던시를 npm install을 사용해서 설치한 후에 development server를 실행하면

<br><br>

```
$ npm install
$ npm start
```

<br><br>
웹사이트가 나오는데, 항상 모달 창 오버레이가 있다.  
그런 다음 다시 코드로 돌아가서 App 컴포넌트로 이동해서  
여기 Modal과 Backdrop 부분을 주석처리하고 저장한 다음 다시 사이트로 가보면  
애플리케이션 데모를 볼 수 있다. 리스트가 있는데 리스트 아이템을 이렇게 추가해 줄 수 있다.

<br><br>

```jsx
import React, { Component } from "react";

import "./App.css";
import Modal from "./components/Modal/Modal";
import Backdrop from "./components/Backdrop/Backdrop";
import List from "./components/List/List";

class App extends Component {
  render() {
    return (
      <div className="App">
        <h1>React Animations</h1>
        {/* <Modal />
        <Backdrop /> */}
        <button className="Button">Open Modal</button>
        <h3>Animating Lists</h3>
        <List />
      </div>
    );
  }
}

export default App;
```

<br><br>
그리고 List 컴포넌트에 리스트 아이템을 추가할 때마다  
내부에서 돌아가는 코드를 볼 수 있다  
그리고 이 과정 전반에서, 우리가 확실히 해야할 것이 있다  
Modal이 나타나고 사라지는 것에 애니메이션을 적용하는 것과  
List컴포넌트에서 적용이 되고 있는데,  
이를 위해서 우리는 먼저 애니메이션이 아닌 주제부터 시작할 것이다.  
이 Modal이 조건에 따라서 나타나도록 해볼 것이다

<br><br>
이제 다시 주석을 해제하여 Modal창이 계속 보이도록 한다.  
그리고 Modal.js 파일에서 Dismiss 버튼에 onClick을 사용해 준 것을 볼 수 있다.

<br><br>

```jsx
<button className="Button" onClick={props.closed}>
  Dismiss
</button>
```

<br><br>
이 Modal.js 파일 안에서 단순히 Modal을 보이거나 숨김으로써  
모달 창이 나타나도록 할지 말지를 제어해보자.  
Modal.css로 가서 스타일을 정의하면 된다.  
.ModalOpen 클래스를 추가함으로써 나타나게 만들고  
.ModalClosed 클래스로는 나타나지 않게 하는 것이다.  
display: block또는 none으로 설정한다.  
Backdrop.css에서도 똑같이 해준다.  
<br><br>

```css
.ModalOpen {
  display: block;
}
.ModalClose {
  display: none;
}
```

```css
.BackdropOpen {
  display: block;
}
.BackdropClose {
  display: none;
}
```

<br><br>
그리고 div에 대해서는 두 파일에다 코드를 조금 추가해주면 된다.  
여기 이 Backdrop 컴포넌트 함수를 재정의 하도록 한다.  
함수를 중괄호로 감싸서, 나중에 jsx를 반환하도록 만들겠지만  
그렇게 하기 전에 먼저 클래스를 생성한다.  
여기에 추가하려는 cssClasses는 배열 형태여야 한다.  
그리고 항상 Backdrop 이어야 하는데  
BackdropOpen 또는 BackdropClosed다.
어떤 props를 받느냐에 따라서 말이다.
<br><br>

```css
const backdrop = (props) => {
  const cssClasses = ["Backdrop",];
  return <div className="Backdrop"></div>;
};

```

<br><br>
<br><br>
그래서 props.show가 참인지를 확인해서  
이것이 참인 경우에만 ‘BackdropOpen’이고
그렇지 않으면 ‘BackdropClosed’라고 한다.

<br><br>

```css
const backdrop = (props) => {
  const cssClasses = [
    "Backdrop",
    props.show ? "BackdropOpen" : "BackdropClosed",
  ];
  return <div className="Backdrop"></div>;
};

```

<br><br>
이제 마지막으로 해야하는 것은 여기의 이 클래스를 달아줘야 한다.  
cssClasses. 를 입력하고 join() 함수를 호출해서 join('') 문자열로 연결한다

<br><br>

```js
return <div className={cssClasses.join("")}></div>;
```

<br><br>
이렇게 함으로써 Backdrop의 화면 표시를 제어할 수 있다  
이 코드를 Modal.js파일에 붙여넣는다. 그리고 여기에서 Modal로 재정의 한다.  
함수가 있다는 이점을 활용해서 무언가를 그냥 반환하지 않는 것이 아니라  
실제로 무언가를 반환하도록 한다.  
그렇게 하기 전에 여기의 cssClasses 또한 조정할 수 있도록 해준다.  
따라서 여기서도 jsx를 반환하도록 해준다.  
그리고 css className을 cssClasses로 사용한다.
이번에도 join메서드로 하나의 문자열로 join 해준다.

<br><br>

```jsx
import React from "react";

import "./Modal.css";

const modal = (props) => {
  const cssClasses = ["Modal", props.show ? "ModalOpen" : "ModalClosed"];
  return (
    <div className={cssClasses.join("")}>
      <h1>A Modal</h1>
      <button className="Button" onClick={props.closed}>
        Dismiss
      </button>
    </div>
  );
};
export default modal;
```

<br><br>
그럼 이제 우리는 Backdrop과 Modal에 props.show를 전달해주기만 하면 된다.  
이것을 App.js 파일에서 할 수 있다. 여기는 클래스 기반 컴포넌트다.  
그럼 state = {} 에 modalIsOpen을 추가해준다.  
초반에 false로 설정한다.

<br><br>

```jsx
class App extends Component {
  state = {
    modalIsOpen: false,
  };
```

<br><br>
그런 다음에, 이 상태를 바꿔준다.
이 클래스에서 함수인 showModal을 추가해 준다.  
<br><br>

```jsx
showModal = () => {
  this.setState({ ModalIsOpen: true });
};
closeModal = () => {
  this.setState({ ModalIsOpen: false });
};
```

<br><br>
Dismiss 버튼을 클릭 했을 때 props.closed를 호출한다.  
closed를 여기 closeModal 메소드 바인딩 해줘야 한다

<br><br>

```jsx
class App extends Component {
  state = {
    modalIsOpen: false,
  };

  showModal = () => {
    this.setState({ modalIsOpen: true });
  };
  closeModal = () => {
    this.setState({ modalIsOpen: false });
  };
  render() {
    return (
      <div className="App">
        <h1>React Animations</h1>
        <Modal closed={this.closeModal} />
```

<br><br>
그리고 backdrop과 Modal 두 개 다 show프롭이 필요하다.  
show프롭에 들어가는 것은 this.state.moodalIsOpen다.  
왜냐하면 이것으로 우리가 모달 창을 보이게 할지 숨길지 정할 것이다.  
<br><br>

```jsx
<Modal show={this.state.modalIsOpen} closed={this.closeModal} />
<Backdrop show={this.state.modalIsClosed}  />
```

<br><br>
마지막으로, 이 모달 창을 보여줄 방법이 필요하다, 우리에겐 이 Open Modal 버튼이 있다.  
해야할 것은 this.showModal 이벤트를 추가해주는 것이다.

<br><br>

```jsx
<button className="Button" onClick={this.showModal}>
  Open Modal
</button>
```

<br><br>
그러면 우리가 구현한 대로, 클릭하면 modal창이 보이고 dismiss버튼을 클릭하면  
modal창이 사라지는 것을 알 수 있다.  
<br><br>

# modal창 보이거나 숨기기

<br><br>
일반적으로 리액트 앱에서 애니메이션 작업을 하는 경우 할 수 있는 다양한 방법이 있다  
가장 쉬운 것은 css 전환과 애니메이션을 사용하는 것이다.

<br><br>

## 1. css 전환과 애니메이션을 사용하기

<br><br>
이것을 모달 창을 예시로 보자.  
예를 들어서 창이 위에서 내려오고 또  
창을 닫을때도 동일하게 투명도도 변하도록 해 보자  
ModalOpen이 연결된 경우에 투명도를 1로 설정하고  
전환 속성을 translateY(0)으로 설정해서 y축에서 0만큼  
슬라이딩 하도록 한다.

<br><br>

```css
.ModalOpen {
  display: block;
  opacity: 1;
  transform: translateY(0);
}
```

<br><br>
이 의미는 가지고 있는 위치를 사용 하라는 것이다.  
우리가 html 코드에서 지정한 위치대로.  
따라서 이 경우엔 기본으로 설정된 위치다.  
그리고 똑같은 것을 ModalClosed에도 연결한다.  
하지만 여기서 투명도는 0이고 translateY(-100%)으로  
설정해서 위로 100% 이동하도록 한다.

<br><br>

```css
.ModalClosed {
  display: none;
  opacity: 0;
  transform: translateY(-100%);
}
```

<br><br>
이제 이것을 저장하고 Open Modal을 클릭하고 Dismiss를 클릭하면,  
아무런 변화도 보이지 않는다.
<br><br>
Modal에 transition 전환 속성을 추가해 본다.  
기본적인 css 속성으로 즉시 적용되어선 안되고 시간이 지남에 따라  
애니메이션이 적용되도록 해야하는 몇몇의 css 속성들을 지정해준다.
다시 말하지만 이것은 리액트와 전혀 상관이 없다.
여기에서 우리는 모든 속성을 전환하고 싶다고 해본다.  
따라서 투명도를 변경하거나 이것을 변형한다 하더라도  
이것에 애니메이션을 적용하고 싶다는 의미다.  
그런 다음 지속시간을 정의할 수 있다.  
그 다음엔 해당 시간 동안 애니메이션이 배포되는 방식을 정의한다.

<br><br>

```css
.Modal {
  position: fixed;
  z-index: 200;
  border: 1px solid #eee;
  box-shadow: 0 2px 2px #ccc;
  background-color: white;
  padding: 10px;
  text-align: center;
  box-sizing: border-box;
  top: 30%;
  left: 25%;
  width: 50%;
  transition: all 0.3s ease-out;
}
```

<br><br>
linear를 사용해서 항상 일정한 속도로 움직이도록 할 수 있고,  
ease-out으로는 종료 때보다 시작 때가 더 빠르도록 한다.  
아직 적용이 안되었는데, 우리가 화면 표시도 변경하기 때문이다.  
block과 none 인데 이렇게 하면 어떤 애니메이션도 전환 시에  
발생하지 못하도록 한다.  
css가 애니메이션을 적용해야 한다는 것을 인식하지 못하기 때문이다.  
여기 display를 지워본다.

<br><br>

```css
.ModalOpen {
  opacity: 1;
  transform: translateY(0);
}
.ModalClosed {
  opacity: 0;
  transform: translateY(-100%);
}
```

<br><br>

그러면 된다고 한다...(? 나의경우는 되지 않았다. css가 적용되지 않았다. )  
이러한 방식으로 애니메이션을 적용하는 것은 전적으로 괜찮고  
이런 종류의 애니메이션의 경우엔 이 방법이 가능한 최적의 방법일 수 있다.  
이 방법은 css의 기본 속성을 사용하는 것인데 꽤 잘 작동된다.  
이 이상의 조정이 필요한 것이 아니라면 여기에 필요한 모든게 다 있다.  
창을 열고 닫는데 필요한 애니메이션이 다 있다.
<br><br>
<br><br>
