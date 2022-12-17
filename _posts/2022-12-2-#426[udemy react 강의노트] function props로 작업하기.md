---
title: "#426[udemy react 강의노트] function props로 작업하기"
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

# function props로 작업하기

<br><br>
NewTodo 컴포넌트가 추가되었으니 App 컴포넌트로 가봅시다  
저는 여기서 NewTodo 컴포넌트를 사용할 겁니다

<br><br>

```tsx
import NewTodo from "./components/NewTodo";
import Todos from "./components/Todos";
import Todo from "./models/todo";

function App() {
  const todos = [new Todo("Learn React"), new Todo("Learn TypeScript")];
  return (
    <div className="App">
      <NewTodo />
      <Todos items={todos} />
    </div>
  );
}

export default App;
```

<br><br>
그래서 여기 import 시켰고요 JSX 코드에 NewTodo도 추가했죠  
이제 여기 있는 Todo 배열은 NewTodo 컴포넌트의 도움을 받아 관리될 겁니다  
새로운 Todo가 추가될 때마다 이 배열에 추가될 거예요

<br><br>

```tsx
const todos = [new Todo("Learn React"), new Todo("Learn TypeScript")];
```

<br><br>
이제 이 배열의 값은 고정돼 있지 않고 동적으로 변할 겁니다  
그러려면 몇 가지 해줘야 할 게 있죠  
‘state’를 사용해 이 배열의 상태를 관리해야 합니다  
<br><br>
그러면 배열이 변경됐을 때 App 컴포넌트가 화면을 다시 렌더링할 수 있죠  
배열이 바뀌면 Todos 컴포넌트가 업데이트하니까요  
그리고 데이터를 다시 넘겨줄 방법이 필요합니다  
NewTodo에서 App 컴포넌트로요
그것부터 시작해 보도록 하죠

<br><br>
이를 위해 NewTodo 컴포넌트의 submitHandler 안에서
함수를 하나 호출할 겁니다 그 함수는 App 컴포넌트에 있을 거고요  
앞에서 여러 번 사용했던 패턴입니다  
우리는 함수 포인터를 전달하는 방법을 배웠죠  
컴포넌트의 props를 통해서요  
여기에 그 작업을 해줘야 합니다

<br><br>
NewTodo의 매개변수로 props 객체를 받을 겁니다  
submitHandler 안에서 props.onAddTodo() 같은 걸 호출하고 싶거든요
onAddTodo에는 함수를 값으로 받을 겁니다  
함수를 받아서 실행할 수 있도록요  
그리고 onAddTodo()의 인수로 enteredText를 전달하고요

<br><br>

```tsx
const NewTodo = (props) => {
  const todoTextInputRef = useRef<HTMLInputElement>(null);
  const submitHandler = (event: React.FormEvent) => {
    event.preventDefault();

    const enteredText = todoTextInputRef.current!.value;
    if (enteredText.trim().length === 0) {
      return;
    }

    props.onAddTodo(enteredText);
  };
```

<br><br>
여기서 가져온 문자열이죠 todoTextIputRef에서요

<br><br>

```tsx
const todoTextInputRef = useRef<HTMLInputElement>(null);
```

<br><br>
이런 식으로 작성할 거예요 그런데 여기 오류 표시가 뜨네요

<br><br>

```
Parameter 'props' implicitly has an 'any' type.ts(7006)

```

<br><br>
물론 이 오류는 우리가 이미 여러 번 봤던 겁니다  
제가 아직 NewTodo를 함수형 컴포넌트로 지정하지 않았거든요  
당연히 지정해 줘야 하고요  
여기에 ‘React.FC’를 타입으로 추가해야 합니다

<br><br>

```tsx
const NewTodo: React.FC = (props) => {

```

<br><br>
그리고 직접 만든 props를 사용해야 한다면  
FC 타입이 제네릭 타입이라는 특성을 이용해  
제가 만들 프로퍼티 객체를 구체적으로 정의해야 합니다  
여기 추가하고 싶은 프로퍼티를요  
그 프로퍼티는 기본 props와 합쳐질 거고요

<br><br>
저는 NewTodo 컴포넌트의 매개변수로  
onAddTodo 프로퍼티를 받고 싶습니다

<br><br>

```tsx
const NewTodo: React.FC<{onAddTodo}> = (props) => {

```

<br><br>
타입은 뭘로 해야 될까요? 타입은 함수로 해야 합니다  
이를 위해 타입스크립트에서  
‘함수 타입’이라 불리는 타입으로 지정해야 하죠  
구현하는 건 굉장히 간단합니다  
여기에 화살표 함수를 사용하면 되죠

<br><br>

```tsx
const NewTodo: React.FC<{ onAddTodo: () => }> = (props) => {

```

<br><br>
이 위치에서 중괄호를 사용해도 객체가 생성되지 않는 것처럼  
여기서 화살표 함수 구문을 사용한다고 함수가 생성되는 게 아닙니다  
우리가 지금 있는 곳은 타입을 정의하는 영역이니까요  
이건 함수의 형태를 정의하는 타입스크립트의 문법입니다  
함수의 타입을 정의하고 있는 거죠  
<br><br>
우리는 onAddTodo가 함수 타입이어야 한다는 걸 압니다  
그래서 여기에 함수의 형태를 정의하는 거고요

<br><br>
괄호 사이에는 이 함수가 받게 될 매개변수를 정의합니다  
화살표 다음에는 이 함수의 반환 타입(리턴 타입)을 정의합니다

<br><br>
onAddTodo는 값을 반환할 필요가 없습니다
이 함수를 호출하고 반환값을 받아 처리할 일이 없으니까요

<br><br>

```tsx
props.onAddTodo(enteredText);
```

<br><br>
반환값을 상수에 저장하지도, 출력하지도 않을 겁니다  
여기서는 반환값이 필요하지 않죠  
그래서 함수의 반환 타입을 ‘void’ 로 설정하고  
onAddTodo의 반환값으로 void 값을 받을 겁니다

<br><br>

```tsx
const NewTodo: React.FC<{ onAddTodo: () => void }> = (props) => {

```

<br><br>
하지만 onAddTodo 함수에는 인수 즉, 매개변수가 있어야 합니다  
아래 쪽에서 이 함수를 호출할 때 인수를 전달했으니까요  
지금 정의된 바에 의하면 onAddTodo는 매개변수를 받지 않는 함수입니다
그게 위쪽에 정의된 함수 타입의 현재 상태입니다  
그래서 아래 쪽에 빨간 밑줄이 표시된 거고요

<br><br>

```tsx
props.onAddTodo(enteredText);
```

<br><br>
onAddTodo 함수는 지금 인수를 받지 않아요  
하지만 여기서는 인수를 넘기죠, enteredText요  
우리는 매개변수를 받아야 하니까  
위쪽에 정의된 함수 타입을 수정해야 합니다  
<br><br>
이 부분에서 인수를 받을 겁니다  
이름은 원하는 대로 하시면 되는데 지금은 일단 ‘text’로 할게요
당연히 문자열 타입이고요

<br><br>

```tsx
const NewTodo: React.FC<{ onAddTodo: (text: string) => void }> = (props) => {

```

<br><br>
이제 모든 오류 표시가 사라졌습니다, 모든 게 명확해졌으니까요  
onAddTodo는 함수일 거고 이 함수는 아무것도 반환하지 않지만  
문자열 타입의 매개변수를 한 개 받습니다  
이제 이런 식으로 함수를 호출해도 문제가 없죠

<br><br>
타입을 숫자형으로 바꾸면 당연히 오류가 나고요  
enteredText는 문자열이지 숫자가 아니니까요  
어차피 우리가 원하는 건 문자열이니까 괜찮습니다

<br><br>
여기까지 하고 늘 그랬던 것처럼 NewTodo를 사용하는 곳  
App 컴포넌트에서 프로퍼티에 적절한 값을 전달해야 합니다  
onAddTodo를 프로퍼티로 추가한 다음  
프로퍼티의 값으로 함수를 넣어야 합니다  
이런 형태를 가진 함수를요

<br><br>
App.tsx
<br><br>

```tsx
<NewTodo onAddTodo={} />
```

<br><br>
이제 이런 형태의 함수를 App.tsx 안에 정의해야 합니다  
새로운 상수를 추가하고 함수 이름은 ‘addTodoHandler’로 할게요  
그런 다음 화살표 함수 연산자를 넣고요  
이제 함수를 구체적으로 구현해야 합니다  
여기에는 진짜 함수를 만드는 겁니다  
타입만 정의하는 게 아니죠  
나중에 정식으로 자바스크립트 코드를 추가할 거니까요

<br><br>

```tsx
function App() {
  const todos = [new Todo("Learn React"), new Todo("Learn TypeScript")];

  const addTodoHandler = (text) => {};
  return (
    <div className="App">
      <NewTodo onAddTodo={addTodoHandler} />
      <Todos items={todos} />
    </div>
  );
}
```

<br><br>
아무것도 반환하지 않아야 합니다 그러니 return 문은 없어도 되겠죠  
하지만 매개변수는 받아야 하고요 todoText 또는 text로 받을게요  
그리고 이건 중요하네요, 문자열 타입이어야 합니다

<br><br>

```tsx
const addTodoHandler = (text: string) => {};
```

<br><br>
이런 형태의 함수가 NewTodo 컴포넌트에서 바라는 함수니까요  
그러니까 이 함수를 onAddTodo를 통해 NewTodo로 전달하려면  
동일한 형태로 함수를 만들어야 합니다 이건 형태가 일치하죠  
이제 addTodoHandler 함수 포인터를 newTodo에 전달할 수 있습니다

<br><br>
이제 addTodoHandler 안에서 Todo 배열을 변형해 볼 겁니다  
state를 사용해 배열을 관리할 수 있도록요

<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
