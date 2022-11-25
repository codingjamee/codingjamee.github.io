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
