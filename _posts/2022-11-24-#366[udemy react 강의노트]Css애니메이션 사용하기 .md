---
title: "#363[udemy react 강의노트]React animation keyframes"
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

# keyframes

<br><br>
전환 prop으로는 우리가 단지 속성이 바뀔 때 그 변화된 속성에  
애니메이션을 적용 하라고 명령하는 것 뿐이고,  
애니메이션 적용 방법은 알아서 알아내게 하는 것이나 마찬가지다.  
애니메이션은, css 애니메이션은 상세하게 설명된 실행하려는  
단계들의 집합이라 할 수 있다
<br><br>

그러기 위해서 @keyframes를 입력해서 소위말하는 keyframes을 생성한다.  
그 다음은 각자가 원하는 이름을 붙여준다.
<br><br>

```css
@keyframes openModal {
}
```

<br><br>
그리고 이 안에는 애니메이션 내부에 백분율로 단계를 정의한다.  
또는 단순히 from 어디부터 to 어디까지를 사용할 수 있다
어디까지만 있다면 to만 사용할 수 있다.  
<br><br>

0%에서 시작한다고 해보면 이렇게 써주는 것이 문법이다.  
특정한 지점도 있길 원한다고 해보자.  
예를 들어 애니메이션의 50%라고 말이다.  
그리고 또 다른 지점으로 100%가 있다고 해보자.
<br><br>

```css
@keyframes openModal {
  0% {
  }
  50% {
  }

  100% {
  }
}
```

<br><br>
이제 이 상태 안에는 일반적인 css 속성을 사용해서 객체들이  
그 특정한 시점에 어떻게 보여야 하는지를 기술해준다.  
지금 이것은 창을 여는 openModal 애니메이션이므로,  
창을 열 때 투명도 0에서 시작하고  
창이 위로 움직이도록 tranform을 설정한다  
<br><br>

```css
@keyframes openModal {
0% {
opacity: 0;
transform: translateY(-100%);
}
```

<br><br>
시간의 50% 지점에서는 투명도 1로 설정한다
완전히 표시되도록 하는 것이나,  
창을 아래로 20% 이동하도록 설정해준다,  
최종 위치에 있는 것은 아니지만
약간은 아래에 가깝게 이동하도록 한다
<br><br>

```css
50% {
  opacity: 1;
  transform: translateY(20%);
}
```

<br><br>

그리고 100%가 되어서야 마지막 위치 translateY(0)로 이동한다.

<br><br>

```css
@keyframes openModal {
  0% {
    opacity: 0;
    transform: translateY(-100%);
  }
  50% {
    opacity: 1;
    transform: translateY(20%);
  }

  100% {
    opacity: 1;
    transform: translate(0);
  }
}
```

<br><br>
이렇게 해주면 이 openModal을 .ModalOpen 클래스에서 사용할 수 있게 된다.  
이 클래스가 모달 창이 보여질 때 연결되는 클래스이기 때문이다  
그럼 여기에는 더이상 opacity와 transform을 설정해 줄 필요가 없다,  
대신 animation을 호출한다.  
이것은 특별한 속성으로 우리가 @keyframes 세트를 정의할 수 있게 해준다.

<br><br>
어떤 요소에 이 ModalOpen css 클래스가 있을 때마다 이 세트가 실행된다.
물론 그 요소에 클래스가 추가 될 때도 포함된다.
따라서 여기에서는 openModal 애니메이션이 실행되게 한다.  
그리고 다시 타이밍도 정의 해주어야 한다.  
0.4초 동안 보여진다고 설정한다.  
그리고 ease 함수 또한 정의해 줄 수 있는데,  
이것으로 여기 각 단계 사이에서 어떻게 움직일 지를 정의한다.

<br><br>

```css
.ModalOpen {
  animation: openModal 0.4s ease-out;
}
```

<br><br>
그리고 또 이 애니메이션을 반복할 것인지  
아니면 이 애니메이션이 끝나는 지점에서 끝낼 것인지도  
정의 해줘야 한다. 만약 아무것도 정의해 주지 않는다면,  
애니메이션이 다 재생 되었을 때,다시 시작 지점으로 돌아가게 된다.  
마지막 지점 즉 100%의 상태를 유지하고 싶다면, forwards라는 값을 추가한다.

<br><br>

```css
.ModalOpen {
  animation: openModal 0.4s ease-out forwards;
}
```

<br><br>
이렇게 파일을 저장하고 Open Modal을 클릭해보면,  
창이 약간 아래로 내려갔다가 위로 올라간다.  
50% 지점 이후에 우리가 20% 아래로 이동 시켰기 때문이다

<br><br>
지금 우리는 클래스가 추가됐을 때 css 애니메이션을 사용하면  
이 animation 속성이 단순히 추가 또는 재생된다는 이점을 활용하는 것이다
창을 닫는 것도, 우리가 @keyframes 세트를 정의해 줄 수 있다.

<br><br>
이번에도 이 모든 것을 css로 조정하는 것이지만  
transition 속성보다는 더 세심히 조정할 수 있다는 점에서 좋다  
css 애니메이션 속성은 우리가 직접 keyframes 세트를 재생할 수 있기 때문이다.  
다시 말하지만 이것은 리액트와는 전혀 상관이 없다,  
<br><br>
하지만 중요한 것은 css 애니메이션을 사용한다고 해서  
더 나쁘거나 안좋지 않다는 것이다  
css 애니메이션도 충분히 사용할 수 있고,  
많은 경우에 특정 애니메이션이나 비주얼을 구현하는데 필요한  
모든 기능을 css 애니메이션이 제공할 수 있다

<br><br>
<br><br>

# CSS 전환 및 애니메이션 제한 사항

<br><br>
지난 강의를 통해 css 전환과 애니메이션을 사용하는 방법에 대해서 알아보았다  
말했듯이, 이것들을 충분히 사용할 수 있다  
그렇지만 이런 애니메이션들이 가지는 한계점을  
개발자 도구에서 확인해보자.

<br><br>
Modal의 div와 Backdrop의 div는 항상 존재한다.  
단지 눈에 보이지 않을 뿐이다,  
실제로 이것들에 애니메이션을 적용하는 것이기 때문이다

<br><br>
그렇다는 것은, 모든 코드 모든 html 코드는 항상 DOM에 존재하고  
투명도가 0이므로 단지 화면에 표시가 되지 않는다는 것이다.
우리는 그렇게 되는 것을 원치 않는다.  
그 말은 DOM이 많은 요소들로 채워진다는 것이고,  
그렇게 되면 속도가 약간 느려질 수 있고,  
접근성 면에서 최선의 경우 또는 최선의 방법이 아니다,  
또한 그렇게 되는 것은 리액트스럽지 못하다.

<br><br>
css 만으로 요소들을 화면에 표시하는 것을 제어하는 것이  
그렇게 최악은 아니다. 나중에 버거 빌더를 만들 때는 이렇게 하기도 한다.  
그리고 특히나 이런 모달 창이나 측면 서랍식 메뉴의 경우,  
이대로도 충분히 괜찮을 수 있다.  
하지만 지금 이 창을 보이고 숨기는 것을  
확실히 다른 방식으로 해보자.

<br><br>
아래와 같이 show의 속성을 전달해서 보이고 숨기는 것이 아니라  
modalIsOpen과 같은 state를 사용해서  
Backdrop과 Modal 요소가 보이고 없애도록 말이다.  
물론 이것은 삼항 연산자를 사용해서 할 수 있다

<br><br>

```jsx
<Modal show={this.state.modalIsOpen} closed={this.closeModal} />
```

<br><br>
this.state.modalIsOpen이 참이라면  
Modal을 렌더링하고, 그렇지 않으면 null을 렌더링 한다,  
그리고 이와 동일한 논리 흐름을 Backdrop에도 적용한다  
Backdrop을 보이게 하거나 null을 렌더링 하도록 말이다.  
이것이 조건부 내용의 렌더링이다

<br><br>

```jsx
{
  this.state.modalIsOpen ? <Modal closed={this.closeModal} /> : null;
}
{
  this.state.modalIsOpen ? <Backdrop show={this.state.modalIsOpen} /> : null;
}
```

<br><br>
저장하고 다시 켜보면 그냥 사라지는 것을 볼 수 있다,  
그 이유는 우리가 Modal 안에서는 여전히 props.show가  
ModalOpen 또는 ModalClosed 클래스를 가지도록 하고 있지만,  
여기서 아무런 props도 전달하지 않기 때문이다

<br><br>

```jsx
const modal = (props) => {
  const cssClasses = ["Modal", props.show ? "ModalOpen" : "ModalClosed"];
```

<br><br>
따라서 항상 거짓이라고 판단하게 되고, 그럼 Modal 안에서  
항상 ModalClosed 클래스가 연결되면서 애니메이션이 없어지는 것이다.

<br><br>
물론 이것은 두군데 모두 show={this.state.modalIsOpen}을 전달함으로써  
 해결할 수 있다.

<br><br>

```jsx
{
  this.state.modalIsOpen ? (
    <Modal show={this.state.modalIsOpen} closed={this.closeModal} />
  ) : null;
}
{
  this.state.modalIsOpen ? <Backdrop show={this.state.modalIsOpen} /> : null;
}
```

<br><br>
여전히 애니메이션이 나타나는 것은 보인다,  
그리고 이 요소가 DOM에 추가되는 것도 물론 볼 수 있지만  
Dismiss를 클릭하면 out애니메이션은 보이지 않는다,  
왜냐하면 요소가 즉각적으로 제거되기 때문이다

<br><br>
이 이유는 리액트에서 App.js 파일 안의 이 재렌더링하는  
jsx 코드 부분을 기다려주지 않기 때문이다  
따라서 상태가 변하면 애니메이션이 완료되기 전에  
Modal과 Backdrop을 제거해버린다.  
이 애니메이션을 인식하지 못했기 때문이다.  
<br><br>
하지만 제거될 예정인 요소를 스캔하지 않는다는 것은,  
아마도 실행 중인 css 애니메이션을 위해서는 좋은 것이다.
만약에 스캔했다면 성능이 최악일 것이다

<br><br>
여기서 css 애니메이션과 전환의 한계점에 대해 보았다.  
애니메이션을 적용해서 창을 여는 것, 보여주는 것은  
사실 여전히 작동한다. Modal에 요소를 추가하면  
css 클래스에도 추가하게 되기 때문에  
애니메이션이 재생된다, 하지만 Modal이 즉각적으로 제거되게 되고  
그렇기에 별로 좋지 못한 동작 또는 결과를 얻는다  
이것을 관리하려면 또 다른 도구가 필요하다

<br><br>
<br><br>
<br><br>
<br><br>
