---
title: "#427[udemy react 강의노트] state및 typescript 관리하기"
excerpt: "React typescript ref useRef"
published: true
categories:
  - Blog
tags:
  - [React, typescript, useRef]

toc: true
toc_sticky: false

date: 2022-12-03
last_modified_at: 2022-12-03
---

<br><br>

# state및 typescript 관리하기

<br><br>
이제 ‘useState’를 사용해 봅시다  
우리는 이 todos 배열을 관리하는 데 state가 필요합니다  
state를 변경하면 App 컴포넌트는 다시 렌더링되고  
이 Todos의 items 역시 다시 렌더링되어 화면에 반영될 겁니다

<br><br>
앞에서 배운 것처럼 react에서 useState를 import합니다.  
그리고 todos를 변형할 겁니다 테스트용으로 넣은 내용은 지우고요  
대신 여기서 useState()를 호출할 겁니다

<br><br>

```tsx
import { useState } from "react";
import NewTodo from "./components/NewTodo";
import Todos from "./components/Todos";
import Todo from "./models/todo";

function App() {
  const todos = useState();
```

<br><br>
그리고 ‘todos’를 다시 여기 쓰는 게 아니라 디스트럭처링을 할겁니다.  
이 안에 todos와 상태 업데이트 함수를 넣고요

<br><br>

```tsx
const [todos, setTodos] = useState();
```

<br><br>
우리는 TypeScript 덕분에 setTodos에 마우스 커서를 가져가서  
타입이 React.Dispatch인 걸 볼 수 있습니다

<br><br>

```
React.Dispatch<React.SetStateAction<undefined>>

```

<br><br>
잘 기억나지 않겠지만 이 타입은 상태 업데이트 함수가 갖는 타입으로  
이 함수를 호출해 state를 변경할 수 있습니다  
state 업데이트를 요청하는 거죠  
useState()에는 인수로 초깃값을 넣을 수 있습니다  
배열도 넣을 수 있고요 빈 배열을 넣을게요  
처음엔 Todo가 없을 테니까요

<br><br>

```tsx
const [todos, setTodos] = useState([]);
```

<br><br>
이런 식으로 썼을 때 작은 문제가 있습니다  
뭐가 문제인지 보이시나요?  
문제는 제가 그냥 빈 배열을  
기본값으로 전달하고 있다는 겁니다  
타입스크립트는 나중에 이 배열에  
어떤 타입의 값이 들어올지 알 수 없습니다

<br><br>
그게 지금 todos의 타입이 ‘never’ 배열인 이유죠

<br><br>

```
const todos: never[]
```

<br><br>
이건 이 배열이 언제나 비어있어야 한다는 의미입니다  
어떤 값도 추가될 수 없죠 그게 이 특이한  
‘never’라는 타입의 의미입니다  
당연히 그건 우리가 원하는 게 아니죠  
하지만 타입스크립트는 타입을 이렇게 추론합니다  
타입스크립트에게는 이 배열이 Todo로 구성돼야 한다는 걸  
알 수 있는 방법이 없으니까요  
처음에 넣어준 값에 그런 정보가 없기 때문이죠

<br><br>
지금은 배열이 비어있지만 나중에 이 배열을 Todo로 채울 거라는 걸  
확실히 하려면 어떤 식으로든 타입스크립트에게 알려줘야 합니다  
우리는 타입스크립트에게 알릴 수 있죠  
이 useState() 함수가 원래 제네릭 함수이기 때문입니다

<br><br>

```
(alias) useState<never[]>(initialState: never[] | (() => never[])):
[never[], React.Dispatch<React.SetStateAction<never[]>>] (+1 overload)
```

<br><br>
사실 이를 위해 제네릭으로 만들어진 거죠  
우리가 이 state에 정말로 저장하려고 하는  
데이터의 타입을 지정할 수 있도록요  
<br><br>
홑화살괄호를 추가하고요  
이제 이 state를 통해 관리할 데이터의 타입은  
Todo로 구성된 배열이라고 정의할 겁니다

<br><br>

```tsx
const [todos, setTodos] = useState<Todo[]>([]);
```

<br><br>
models 폴더에서 가져온 거죠, 그리고 대괄호를 추가합니다  
이 state가 Todo 배열을 관리한다는 의미입니다
처음에는 빈 배열이죠 그렇게 정의했으니까요  
이 타입 정의에는 문제가 되지 않습니다  
<br><br>
하지만 나중에 배열에 항목을 추가할 때는  
문자열도, 숫자형도 아닌 Todo 타입이어야 합니다  
추가될 항목의 형태는 우리가 만든 Todo 클래스의 형태여야 합니다

<br><br>
이게 state를 정의하는 올바른 방법이죠  
이제 addTodoHandler 안에서 새로운 Todo를 생성할 겁니다  
new Todo()를 사용하고, 인수로는 todoText를 넣어  
생성자에게 전달합니다

<br><br>

```tsx
function App() {
  const [todos, setTodos] = useState<Todo[]>([]);

  const addTodoHandler = (todoText: string) => {
    const newTodo = new Todo(todoText);
  };
```

<br><br>
그런 다음 setTodos()를 호출해 state를 업데이트할 거고요  
여기에 현재 상태의 todos를 사용할 겁니다

<br><br>

```tsx
function App() {
  const [todos, setTodos] = useState<Todo[]>([]);

  const addTodoHandler = (todoText: string) => {
    const newTodo = new Todo(todoText);

    setTodos();
  };
```

<br><br>
지금 있는 배열이요, 거기에 newTodo를 추가하고요  
앞에서 배운 것처럼, 이전 state를 기반으로
state를 업데이트하려면
이렇게 함수 형태를 사용해 state를 업데이트해야 합니다

<br><br>

```tsx
setTodos(() => {});
```

<br><br>
이 함수에서 currentState나 prevState같은 이름으로  
보시는 관점에 따라 state에 이름을 붙이고요  
그런 다음 업데이트된 state를 여기서 반환하는 거죠

<br><br>

```tsx
setTodos((prevState) => {});
```

<br><br>
그럼 저는 prevTodos에 newTodo를 추가해 반환하겠습니다  
Concat() 메서드를 사용하면 새로운 배열을 생성할 수 있는데,  
우린 새로운 배열이 필요하죠 기존에 있던 배열은 건드리면 안됩니다

<br><br>

```tsx
setTodos((prevTodos) => {
  return prevTodos.concat(newTodo);
});
```

<br><br>
여기서는 새로운 배열을 반환해야 하죠  
그러면 리액트는 이 배열을 새로운 state로 사용합니다  
이 배열에는 Concat() 덕분에 newTodo가 포함되어 있고요  
이런 식으로 state를 업데이트하는 겁니다  
모든 게 갖춰졌으니 변경 사항을 저장하고 다시 로드하면  
스타일이 적용되지 않은 폼이 나오죠  
지금은 Todo 목록이 비어있지만  
여기 ‘Learn React’를 입력하면 추가되죠 잘 동작하네요

<br><br>

# 스타일링 추가하기

<br><br>
스타일을 좀 적용할게요 이를 위해 module.css 파일을 3개 준비했습니다  
첨부 파일을 받으시면 되고요 Todos.module.css 파일,  
TodoItem.module.css NewTodo.module.css 파일입니다  
그냥 추가하면 됩니다 앞선 강의에서 했던 것처럼요  
css 파일을 컴포넌트에 추가하는 데 타입스크립트라서  
특별히 해줘야 할 건 없습니다  
Glabal CSS를 사용하든, CSS 모듈(module)을 사용하든 똑같죠

<br><br>
저는 CSS 모듈을 사용합니다, 그래서 css 파일 이름을 이런 거죠
우리는 Creat-React-App으로 생성한 프로젝트에 있기 때문에
내장된 CSS 모듈을 사용할 수 있습니다
이제 여기에 클래스를 import 할게요  
타입스크립트가 아닐 때 썼던 방식과 같습니다  
클래스를 추가하겠습니다, ‘todos’ 클래스네요  
Todos.module.css 파일에 정의해둔 거죠

<br><br>

```tsx
import React from "react";
import Todo from "../models/todo";
import TodoItem from "./TodoItem";
import classes from "./Todos.module.css";

const Todos: React.FC<{ items: Todo[] }> = (props) => {
  return (
    <ul className={classes.todos}>
      {props.items.map((item) => (
        <TodoItem key={item.id} todoText={item.text} />
      ))}
    </ul>
  );
};

export default Todos;
```

<br><br>
다른 파일에도 같은 작업을 해줄게요  
TodoItem.tsx 파일에서는 클래스를  
‘./TodoItem.module.css’에서 가져오고요  
그런 다음 className에 classes.item을 넣어  
list item 요소에 추가합니다
<br><br>

```tsx
import classes from "./TodoItem.module.css";
...
  return <li className={classes.item}>{props.todoText}</li>;

```

<br><br>
‘.item’인 이유는 제가 TodoItem.module.css 파일에  
이 item 클래스를 갖고 있기 때문이죠

<br><br>
그리고 NewTodo.tsx 파일에서도 클래스를 import 합니다

<br><br>

```tsx
import classes from "./NewTodo.module.css";
```

<br><br>
이렇게 가져온 다음, 폼(form)으로 가서  
여기 className에 ‘classes.form’을 넣습니다  
다 하셨으면 작업한 걸 저장하고요 바뀐 스타일을 확인해 보세요  
여러분 화면에서요 ‘Learn React’를 넣고 ‘LeanTypeScript’도 넣고요  
이런 식으로 동작합니다  
이전과 동일한 앱이지만 이제 스타일이 적용됐죠

<br><br>
<br><br>
<br><br>
<br><br>
