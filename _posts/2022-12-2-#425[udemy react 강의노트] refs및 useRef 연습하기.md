---
title: "#425[udemy react 강의노트] refs 및 useRef 연습하기"
excerpt: "React typescript ref useRef"
published: true
categories:
  - Blog
tags:
  - [React, typescript, useRef]

toc: true
toc_sticky: false

date: 2022-12-02
last_modified_at: 2022-12-02
---

<br><br>

# useRef 사용하기

<br><br>
리액트에서 useRef를 가져와 사용합니다.  
레퍼런스(Ref)를 생성할 수 있습니다  
todoTextInputRef 같은 걸 만들 수 있죠

<br><br>

```tsx
import React, { useRef } from "react";

const NewTodo = () => {
  const todoTextInputRef = useRef();
```

<br><br>
그리고 useRef()를 호출해 레퍼런스를 생성할 수 있습니다  
그런 다음 만든 레퍼런스와 연결할 HTML 요소로 갈 건데  
아마 입력창이나 텍스트 영역 같은 곳이 되겠죠  
해당 요소에서 ref 프로퍼티에 우리가 만든  
레퍼런스를 지정해 줍니다  
여기서는 todoTextInputRef를 넣을 겁니다

<br><br>

```tsx
<input type="text" id="text" ref={todoTextInputRef} />
```

<br><br>
이렇게 하면 아래 쪽에서 오류가 발생하죠
<br><br>

```
Type 'MutableRefObject<undefined>' is not assignable to type 'LegacyRef<HTMLInputElement> | undefined'.
  Type 'MutableRefObject<undefined>' is not assignable to type 'RefObject<HTMLInputElement>'.
    Types of property 'current' are incompatible.
      Type 'undefined' is not assignable to type 'HTMLInputElement | null'
```

<br><br>
ref에 잘못된 데이터 타입이 들어왔다는 내용입니다  
ref에는 userRef()로 생성한 레퍼런스를 넣어야 합니다  
그 부분은 잘못된 게 없어요, 하지만 여기서 생성한  
레퍼런스는 명확하지가 않죠

<br><br>
바닐라 자바스크립트에서는  
이렇게 코드를 작성해도 동작합니다,  
타입 표기의 개념이 없었기 때문이죠  
<br><br>
하지만 타입스크립트로 작업할 때는  
우리가 만든 레퍼런스에 대해 더 많은 정보를 알려줘야 합니다

<br><br>
여기서 레퍼런스를 만들 때 타입스크립트는  
이 레퍼런스가 입력창에 연결될 거라는 사실을 알지 못합니다

<br><br>

```tsx
const todoTextInputRef = useRef();
```

<br><br>
아래서 지정한 뒤에 알게 되죠

<br><br>

```tsx
<input type="text" id="text" ref={todoTextInputRef} />
```

<br><br>
하지만 이 시점에는 알 수 없기 때문에 이 레퍼런스를 사용하거나  
원하는 HTML 요소에 연결할 수는 있어도  
결국엔 이런 오류가 발생하는 겁니다

<br><br>
이 input 요소의 ref에 레퍼런스를 주긴 했지만,  
명확하게 이 HTML 요소에 맞는 레퍼런스가 아닌 거죠  
button 요소의 ref에도 넣을 수도 있는 겁니다

<br><br>
그래서 이 레퍼런스에 저장될 데이터가  
어떤 타입인지 명확히 밝혀야 합니다  
이를 위해 또 다시 제네릭 타입을 사용할 거예요  
useRef 자체가 제네릭 타입으로 정의되어 있는 이유죠

<br><br>

```
(alias) useRef<undefined>():
```

<br><br>
여기 홑화살괄호를 추가하고 여기서 생성할 레퍼런스가  
어떤 타입인지 명확히 설정해야 합니다  
useRef는 이 레퍼런스에 저장되는 모든 종류의 데이터에 사용될 수 있고  
이 레퍼런스와 연결할 수 있는 모든 HTML 요소에 사용할 수 있죠

<br><br>

```tsx
const todoTextInputRef = useRef<>();
```

<br><br>
하지만 우리는 여기서 생성하는 레퍼런스 객체에  
어떤 값 또는 HTML 요소를 저장할 건지 구체적으로  
설정해야 합니다

<br><br>
여기서는input 요소를 저장할 거고  
이 요소는 미리 정의된 타입을 갖습니다  
HTMLInputElement 타입이죠

<br><br>

```tsx
const todoTextInputRef = useRef<HTMLInputElement>();
```

<br><br>
모든 돔(DOM) 요소들은 미리 정의된 타입을 가집니다  
타입스크립트에서 해당 요소를 참조할 때 사용할 수 있죠  
input 요소의 타입은 HTMLInputElement이고요

<br><br>
button 요소의 타입은 HTMLButtonElement입니다  
paragraph 요소의 타입은 HTMLParagraphElement죠  
그 밖에도 많고요

<br><br>
MDN의 input element 페이지에서도 볼 수 있습니다  
여기 input 요소가 있죠 물론 여기 있는 내용들은  
타입스크립트 중심으로 작성된 게 아닙니다

<br><br>
그래도 여기에서 이런 정보를 확인할 수 있어요  
input 요소의 기반이  
HTMLInputElement 인터페이스라는 내용입니다  
우리가 이 값을 타입으로 사용하는 이유죠

<br><br>
그래도 여기에서 이런 정보를 확인할 수 있어요
input 요소의 기반이 HTMLInputElement 인터페이스라는 내용입니다

<br><br>

```
모든 <input> 요소는 유형에 상관하지 않고
HTMLInputElement (en-US) 인터페이스에 기반하므로...
```

<br><br>
우리가 이 값을 타입으로 사용하는 이유죠  
이렇게 하면 여기서 생성하는 레퍼런스가  
HTMLInputElement와 연결된다는 게 명확해졌습니다

<br><br>
그런데도 오류 표시가 보이네요 이 오류가 뜨는 이유는  
기본값을 직접 설정하지 않았기 때문입니다  
이 레퍼런스에는 다른 요소가 할당되어 있을 수 있거든요  
생성됐을 때 자동으로 지정되는 거죠  
<br><br>
그래서 시작 값을 직접 넣어줘야 합니다  
처음에 만들 때는 연결할 게 없기 때문에
시작 값은 null로 지정합니다

<br><br>

```tsx
const todoTextInputRef = useRef<HTMLInputElement>(null);
```

<br><br>
시작 값을 추가하면 에러가 사라집니다.  
여기서 생성되는 레퍼런스는 HTMLInputElement와 연결될 거고  
시작 값으로 null을 가집니다
useRef를 사용할 때는 이런 내용을 명확하게 설정해야 합니다

<br><br>
이것이 타입스크립트를 사용해 레퍼런스를 생성하고  
요소와 연결하는 방법입니다  
이후에는 우리가 배운 대로 하면 됩니다

<br><br>
이제 submitHandler 안에서  
enteredText를 가져올 수 있죠  
todoTextInputRef.current를 통해서요  
레퍼런스를 통해 늘 했던 거예요

<br><br>
레퍼런스에는 항상 current 프로퍼티가 있고  
여기에 실제 값이 들어있죠. 점을 찍어볼게요  
<br><br>

```tsx
const enteredText = todoTextInputRef.current.;
```

<br><br>
좋은 점은 레퍼런스가 들고 있는 요소를 명확하게 지정했기 때문에  
HTMLInputElement의 자동 완성 기능을 사용할 수 있습니다  
input 요소 객체의 프로퍼티를 전부 볼 수 있어요

<br><br>
input 요소의 객체가 자바스크립트에서 가질 수 있는  
모든 프로퍼티죠 그리고 우리는  
자동 완성으로 ‘value’도 가져올 수 있습니다

<br><br>

```tsx
const enteredText = todoTextInputRef.current?.value;
```

<br><br>
current 뒤에 여기 물음표가 붙은 게 보이실 겁니다  
이 경우에는 개발 툴이 자동으로 붙인 겁니다  
이건 어디서 온 걸까요?  
개발 툴이 자동으로 물음표를 추가한 이유는  
사용하려는 시점에 레퍼런스에 아직  
값이 설정되지 않았을 수도 있기 때문입니다

<br><br>
물론 우리는 개발한 사람이니까 submitHandler가 호출되는 시점에  
todoTextInputRef가 요소와 연결된다는 걸 압니다  
이 함수는 폼이 제출될 때만 호출될 거고  
폼이 제출되는 건 레퍼런스가 연결된 다음이니까요

<br><br>
하지만 이론적으로는 여기서 value를 가져올 수도 있습니다  
레퍼런스를 정의한 직후에요
이 경우, 레퍼런스에는 아직 연결된 값이 없죠

<br><br>

```tsx
const NewTodo = () => {
  const todoTextInputRef = useRef<HTMLInputElement>(null);

  const enteredText = todoTextInputRef.current?.value;

```

<br><br>
타입스크립트는 우리 코드를 정밀하게 분석하지 않습니다  
연결이 완료되기 전에 이 코드를 호출해선 안된다는 걸 이해하지 못하죠  
그래서 여기에 추가된 물음표가 타입스크립트에게 일단 값에 접근해보고  
접근이 가능하다면, 그때 입력된 값을 가져와  
enteredText에 저장하라고 알리는 겁니다

<br><br>
만약 접근이 불가능하면 아직 연결되지 않은 상태일 거고  
null이 enteredText에 저장되겠죠  
그래서 enteredText의 추론된 타입이  
string 또는 undefined로 나타나는 거고요  
여기에서는 null과 같은 의미니까요  
<br><br>

```
const enteredText: string | undefined

```

<br><br>
이는 타입스크립트가 실제 value를 가져올 수 있는지  
반드시 알아야 할 필요는 없다는 뜻입니다  
만약 여러분이 개발자로서 이시점에 값이  
null이 아니라는 걸 확신하고,  
레퍼런스와 요소가 연결되었다는 걸 알고 있다면  
물음표 대신 느낌표를 사용할 수 있습니다

<br><br>

```tsx
const NewTodo = () => {
  const todoTextInputRef = useRef<HTMLInputElement>(null);
  const submitHandler = (event: React.FormEvent) => {
    event.preventDefault();

    const enteredText = todoTextInputRef.current!.value;
  };
```

<br><br>
이 특수 기호는 타입스크립트에게 이 값이 null이 될 수 있다는 건 알지만  
이 시점에는 절대 null이 아니라고 알려줍니다  
그래서 이 기호는 null이 아니라는 걸  
100% 확신하는 경우에만 사용해야 합니다  
여기서는 확실히 연결이 완료된 경우를 뜻하죠

<br><br>  
이렇게 하면 추론된 타입에 string만 나옵니다  
string이나 undefined가 아니죠  
이 시점에는 연결이 완료될 거고  
언제나 value를 가져올 수 있다고 표시했으니까요

<br><br>
여기서는 그렇게 해도 됩니다  
우리는 submitHandler가 기술적으로 지금 코드에서는  
호출될 수 없다는 걸 알기 때문입니다

<br><br>

```tsx
const NewTodo = () => {
  const todoTextInputRef = useRef<HTMLInputElement>(null);
  const submitHandler = (event: React.FormEvent) => {
    event.preventDefault();

    const enteredText = todoTextInputRef.current?.value;
  };
  return (
    <form onSubmit={submitHandler}>
      <label htmlFor="text">Todo text</label>
      <input type="text" id="text" ref={todoTextInputRef} />
      <button>Add Todo</button>
    </form>
  );
};
```

<br><br>
이 부분이 처리되어 연결이 완료되기 전에는요

<br><br>

```tsx
<input type="text" id="text" ref={todoTextInputRef} />
```

<br><br>

<br><br>
이건 아주 중요한 개념이라서 이 부분에서 잠시 살펴본 겁니다  
하지만 사실 이 물음표나 느낌표 연산자는  
레퍼런스와 크게 상관이 없습니다  
리액트에만 있는 기능도 아니고요  
그냥 일반적인 연산자로 타입스크립트에서도 사용할 수 있어요  
null일 수도 있는 값을 다룰 때는  
물음표를 사용해서 먼저 해당 값에 접근해보고  
null일 경우, 상수 또는 값을 저장할 곳에 null을 저장합니다  
느낌표를 사용하면 이 위치에서는 해당 값이 절대 null이 될 수 없으니  
바로 객체의 프로퍼티로 들어가서  
null이 아닌 실제 값을 가져오라는 뜻이죠

<br><br>
언제나 실제 값을 가져올 수 있도록요 하지만 설명한 것처럼  
값이 null이 아니라는 걸 확신할 때만 사용해야 합니다  
이 둘은 타입스크립트에서 중요한 연산자이며,  
특히 레퍼런스를 다룰 때 매우 중요한 기능을 합니다  
레퍼런스는 null일 수도 있고 어떤 시점에는  
연결된 값이 없을 수도 있기 때문이죠

<br><br>
그럼 이제 enteredText를 가져왔으니  
가져온 값을 검증해 봐야 합니다  
enteredText 값에서 공백을 제거했을 때 아무것도 없다면  
입력된 내용이 없다는 뜻이겠죠

<br><br>

```tsx
const enteredText = todoTextInputRef.current!.value;
if (enteredText.trim().length === 0) {
}
```

<br><br>
띄어쓰기만 잔뜩 입력되거나 그런 경우일 겁니다  
이런 경우에는 오류를 return합니다  
저는 그냥 함수를 빠져나갈게요
<br><br>

```tsx
if (enteredText.trim().length === 0) {
  return;
}
```

<br><br>
이 if문을 통과할 경우 뭔가 유효한 값이 있다는 거겠죠  
확실히 하자면 이 부분은 레퍼런스 연결 여부와 상관이 없습니다  
사용자가 입력한 텍스트를 검증할 뿐이에요  
타입스크립트와 아무런 관련이 없죠

<br><br>
어쨌든 이 if문을 통과하면 입력된 Todo의 내용을  
todos 목록에 추가할 겁니다 여기서 할 작업은 아닙니다  
여기는 NewTodo 함수 컴포넌트 안이니까요

<br><br>
대신 Todo 목록을 관리하는  
App 컴포넌트에 할 겁니다  
그래서 새로 추가된 Todo를 App 컴포넌트로  
보내기 위한 방법이 필요한 거고요  
다음 강의에서는 그 작업을 해 볼 겁니다

<br><br>
