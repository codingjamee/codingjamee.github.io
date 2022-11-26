---
title: "#375[udemy react 강의노트]List 컴포넌트 애니메이션 "
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

<br><br>

# List 컴포넌트 애니메이션 추가

<br><br>
지금은 Add Item을 클릭하면 그냥 추가되고  
제거하면 그냥 사라진다  
여기에 애니메이션을 추가해본다.

<br><br>
하지만 Transition과 CSSTransition 단독으로는 할 수 없다.  
리액트의 TransitionGroup 패키지에서 불러오는  
추가적인 컴포넌트를 사용해 준다.

<br><br>

```jsx
import TransitionGroup from "react-transition-group/TransitionGroup";
```

<br><br>
TransitionGroup은 동적 리스트를 반환하는 곳에서 사용할 수 있다.

<br><br>

```jsx
return (
  <div>
    <button className="Button" onClick={this.addItemHandler}>
      Add Item
    </button>
    <p>Click Item to Remove.</p>
    <ul className="List">{listItems}</ul>
  </div>
);
```

<br><br>
이 `<ul className=“List”>` 요소를 `<TransitionGroup>`로  
대체할 수 있다.

<br><br>

```jsx
return (
  <div>
    <button className="Button" onClick={this.addItemHandler}>
      Add Item
    </button>
    <p>Click Item to Remove.</p>
    <TransitionGroup>{listItems}</TransitionGroup>
  </div>
);
```

<br><br>
그리고 기본적으로 이것은 그 자리에 div를 렌더링한다  
ul태그를 렌더링 하도록 컴포넌트의 속성을 편리하게 정의할 수 있다  
listItems를 위아래로 감싸서 의미상 올바르도록 알아서 ul태그로 대체할 것이다.

<br><br>

```jsx
<TransitionGroup component="ul">{listItems}</TransitionGroup>
```

<br><br>
그런 다음, 물론 리스트 클래스도 있다.  
이것은 여느 요소들 처럼 단순히 className을 입력해줌으로써  
추가할 수 있다

<br><br>

```jsx
<TransitionGroup component="ul" className="List">
  {listItems}
</TransitionGroup>
```

<br><br>
className=“List”라고 입력해서 말이죠 이것을 저장하고 다시 돌아가서,  
여전히 작동하는 것을 볼 수 있으나,  
몇가지 경고가 뜬다  
알 수 없는 이벤트 핸들러 ‘onExited’ 경고와  
불리언 속성이 아닌 ‘in’에 대해 ’true’ 값을 입력받았다는 경고다

<br><br>

```
proxyConsole.js:56 Warning: Unknown event handler property `onExited`. It will be ignored.
```

<br><br>

```

Warning: Received `true` for a non-boolean attribute `in`
```

<br><br>
이런 경고가 뜨는 이유는 지금 우리 상태의 TransitionGroup으로는  
여전히 ListItem의 리스트를 감싸고 있기 때문이다.  
TransitionGroup은 리스트에 애니메이션을 적용할 때 사용할 수 있지만,  
반드시 Transtion 또는 CSSTransition 컴포넌트와 함께 사용해야한다.

<br><br>
그러면 두개 모두 우리가 배운 것처럼 작동한다  
그럼 여기에 CSSTransition을 불러와서 자동으로  
css 클래스들을 생성할 수 있도록 사용가능하다.

<br><br>

```jsx
import CSSTransition from "react-transition-group/CSSTransition";
```

<br><br>
이것을 사용해서 ListItem을 위아래로 감싼다.

<br><br>

```jsx
  render() {
    const listItems = this.state.items.map((item, index) => (
      <CSSTransition>
        <li
          key={index}
          className="ListItem"
          onClick={() => this.removeItemHandler(index)}
        >
          {item}
        </li>
      </CSSTransition>
    ));
```

<br><br>
이제 key={index}를 CSSTransition 요소로 이동시킨다.  
왜냐하면 이것이 사실상 우리가 반복하는 아이템이기 때문이다.

<br><br>

```jsx
<CSSTransition key={index}>

```

<br><br>
이제 이것을 저장하면, timeout이 없다는 경고가 뜨는 것을 볼 수 있다.
<br><br>

```
proxyConsole.js:56 Warning: Failed prop type: The prop `timeout` is marked as required in `CSSTransition`, but its value is `undefined`
```

<br><br>
그리고 이것을 클릭하면 ‘enter’를 찾지 못한다고 나온다.  
enter 클래스가 기본적으로 css 클래스에 추가하려고 시도한다  
이 모든 오류들은 CSSTranstion이 올바르게 설정되지 않았기 때문이다

<br><br>
“fade”라고 임의로 지정한 classNames를 전달해주어야 한다  
그리고 timeout 또한 전달해줘야 한다

<br><br>

```jsx
<CSSTransition key={index} classNames="fade" timeout={300}>
```

<br><br>
이제 CSSTransition을 올바르게 설정해주면,  
실제로 무언가에 애니메이션을 적용한다.  
음 아무것에도 애니메이션이 적용되지는 않았지만 더 이상 오류를 던지지 않는다

<br><br>
중요한 것은 이제부터 우리가 애니메이션 적용을 시작할 수 있다는 것이다  
여기서 우리가 전에 사용했던 in 속성을 사용해야한다고 생각할 수 있다,  
Transition 상태를 제어하기 위해 사용했었다.

<br><br>
TransitionGroup이 특별한 점 이자 유일하게 이것이 하는 일은  
여러 아이템을 취급할 수 있다는 것이다.  
이것이 리스트의 변경된 요소를 알아낸다.  
제거 되었는지 추가 되었는지를 말이다.
그리고는 일일이 in 속성을 설정 해준다,  
감싸 놓은 Transition 또는 CSSTransition 컴포넌트에.  
우리가 in 속성을 제어하지 않아도 되도록 한다,  
왜냐하면 우리는 당연히 in 속성을 제어 할 수 없기 때문이다.  
끝내 취급하게 되는 것이 동적 리스트다.  
그런 점에서, in 속성에 대한 관리가 주요 차이점이라고 할 수 있습니다

<br><br>
그럼 이제 우리는 fade 애니메이션을 정의할 수 있다.  
”fade”를 몸통으로 설정한다.  
이제 List.css 파일에서 .fade-enter{}를 추가한다.  
이것이 구성의 시작이라고 하고 투명도 0으로 설정한다.

<br><br>

```css
.fade-enter {
  opacity: 0;
}
```

<br><br>
따라서 애니메이션이 지속되는 동안 투명도가 1이 되도록 하고,  
이것을 CSSTransition으로 하도록 한다.
여기 transition은 투명도 0.3초 라고 한다.  
timeout에서 300밀리초라고 정의했기 때문에.  
ease-out timing 함수도 입력한다.  
나머지 css 코드도 입력해준다.

<br><br>

```css
.fade-enter {
  opacity: 0;
}

.fade-enter-active {
  opacity: 1;
  transition: opacity 0.3s ease-out;
}

.fade-exit {
  opacity: 1;
}

.fade-exit-active {
  opacity: 0;
  transition: opacity 0.3s ease-out;
}
```

<br><br>
저장해주고 Add Item을 클릭하면, 애니메이션 효과가 적용된다.
<br><br>

# Transition의 대안, React motion

<br><br>
그런데 React Motion은 완전히 다른 접근 방식을 따른다.  
React Motion에서는 일례로 애니메이션 효과가 걸리는 시간을  
수동으로 정의하지 않는다.  
대신 React Motion은 현실의 물리학을 모방해서  
애니메이션 효과가 걸리는 최적의 시간을 구하려고 한다.  
단순히 종료 및 시작 상태만을 여러분이 정의할 수 있게 하고,  
현실의 물리학을 최대한 이용해서  
그 종료와 시작 사이의 상태를 모방하거나 보간하기를 시도한다.  
다양한 데모를 이 페이지에서 확인해 볼 수 있다,  
효과가 우리가 원하는 것인지.
그리고 물론 이 페이지에 링크된 문서를 확인해 볼 수도 있다

<br><br>

```
https://github.com/chenglou/react-motion
```

<br><br>
애니메이션에 대한 매우 비정통적인 사고방식 때문에  
React Motion을 처음 사용하려고 하는 것은 어려울 수 있다.

그리고 몇몇의 애니메이션은 이것으로는 하기가 좀 어려울 수 있다.  
하지만 다른 많은 경우에는 굉장할 수 있고  
특정한 종류의 애니메이션을 원할 경우에  
더 자연스러워 보이는 애니메이션을 효과를 줄 수도 있다

<br><br>

# 또 다른 대안 React Move

<br><br>

또 다른 대안 중 하나는 React Move다.  
이것 또한 공식페이지를 찾을 수 있다.  
React Move는 제공하는 두가지 기본 컴포넌트가 있다  
단일 아이템 및 그룹 아이템과 노드 그룹에 애니메이션을 적용할 수 있다

<br><br>
React Move에서는 항상 객체로 작업을 하게 되고,  
이 객체는 애니메이션의 상태를 설명한다  
React Move는 Three.js Transition의 영향을 상당히 받았기 때문에  
이것에 대해서 안다면 React Move를 사용함에 있어서 우위를 가질 수 있다.  
그리고 끝 부분에 더 자세한 문서와 설명을 확인 할 수 있다  
작동 방식과 도큐멘테이션에 대해서.

<br><br>
여기에서는 여러분이 효과의 시간을 정의해 주기는 하지만  
단지 투명도와 같은 스타일 등을 정의하는 것이 아니라  
더 많은 것을 조정할 수 있다.  
때문에 더 복잡한 애니메이션을 만들 수 있다
또 그룹 애니메이션을 적용하는 요소도 있다

<br><br>
이것은 상당히 복잡하기 때문에 우리가 필요 없는 기능들이 있을 수 있다  
더 많은 제어가 필요하다면 React Move가 우리가 찾는 대안이 될 수 있다

<br><br>

# 또 다른 대안, react-router-transition

<br><br>
이것보다 더 많은 라이브러리가 있고 새로운 리액트 애니메이션 라이브러리가  
매주 새로 나오는 것 같은 느낌 마저 받을 수 있지만,  
react-router-transition은 아주 흥미롭고 특별하다.

<br><br>
이름에서 느껴지듯이 route 전환을 쉽게 생성할 수 있도록 해준다,  
서로 다른 route 간의 애니메이션 효과인 것이다.  
route 전환을 쉽게 생성할 수 있도록 해준다,  
서로 다른 route 간의 애니메이션 효과인 것이다.

<br><br>

## AnimationSwitch

<br><br>

React Motion을 기본으로 하고, 기본적으로  
이 AnimationSwitch라는 컴포넌트를 제공한다,  
일반 RouteSwitch를 대체하는 것이다,  
이것은 React Router 버전 4와 작동할 수 있도록 만들어 졌다
제가 이 과정 또는 이상에서 다뤘었다.  
<br><br>
AnimatedSwitch는 일반 switch가 하는 모든 것을 동일하게 한다
하지만 atEnter, atLeave, atActive  
그리고 className을 정의할 수 있도록 되어 있다

페이지 전환 애니메이션 같이 route에 대한  
애니메이션 효과를 줄 수 있도록 되어있다.

<br><br>
페이지 전환 애니메이션 같은 것은  
리액트의 TransitionGroup으로는 꽤 까다로울 수 있다

<br><br>

# 이번 모듈의 핵심

<br><br>

cSS, animation, Transition 만으로도 많은 것을 이룰 수 있다  
그리고 설령 react-transition-group과 같은 라이브러에 의지한다 하더라도  
우리가 보았듯이 그 이면에서는 CSSTransition으로 작업이 이루어진다
다양한 추가적인 css 스타일을 조화롭게 조율하기 위해서
단지 자바스크립트 레이어가 있을 뿐이다

<br><br>

## 무엇을 선택할까?

<br><br>
조그마한 자바스크립트 레이어를 제공해 주어서  
우리가 애플리케이션에 추가할 수 있도록 해준다.  
언급했듯이, 여러분의 css 애니메이션을 조율해 주는 것이다.  
여러분이 css 독립적인 솔루션을 원한다면  
React Motion과 같은 패키지를 알아볼 수도 있다  
현실의 물리학을 모방하지만 그 이면에서는 css를 사용한다.

<br><br>
<br><br>
<br><br>
<br><br>
