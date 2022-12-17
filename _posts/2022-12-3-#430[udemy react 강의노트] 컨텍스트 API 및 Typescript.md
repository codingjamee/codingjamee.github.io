---
title: "#430[udemy react 강의노트] 컨텍스트 API 및 Typescript"
excerpt: "React typescript context api"
published: true
categories:
  - Blog
tags:
  - [React, typescript, Context API]

toc: true
toc_sticky: false

date: 2022-12-04
last_modified_at: 2022-12-04
---

<br><br>

# 타입스크립트와 컨텍스트 API

<br><br>
타입스크립트와 컨텍스트 API에 대해 알아보겠습니다  
먼저 ‘store’ 폴더를 추가할게요  
폴더 안에 ‘todos-context.tsx’ 파일을 추가하겠습니다  
.tsx로 만든 건, 여기에 JSX 코드가 들어가기 때문이고요

<br><br>

그럼 TodosContext부터 생성해 보겠습니다  
React.createContext()를 호출해서요

<br><br>

```tsx
const TodosContext = React.createContext();
```

<br><br>
이제 createContext() 함수를 호출할 건데  
createContext()에는 초깃값을 넣어야 합니다  
인수를 한 개 넣어야 해요 인수에는 객체도 넣을 수 있죠  
이 객체에 TodosContext의 형태를 정의할 겁니다

<br><br>

```tsx
const TodosContext = React.createContext({});
```

<br><br>
이 객체에는 items가 있을 거고요 처음에는 빈 배열이겠죠  
addTodo() 함수도 있을 겁니다 여기서는 아무것도 수행하지 않는 함수입니다  
그리고 removeTodo() 함수도 있고요  
문자열 타입의 id를 받지만, 하는 일은 없죠

<br><br>

```tsx
const TodosContext = React.createContext({
  items: [],
  addTodo: () => {},
  removeTodo: (id: String) => {},
});
```

<br><br>
여기까지 봤을 때, 타입스크립트라면 해줘야 할 게 있습니다  
여기에 items을 빈 배열이라고 해 뒀는데  
이 경우 전에 봤던 문제가 발생할 겁니다,  
이 배열에 어떤 타입의 값이 들어올지 명확하지 않다는 문제죠

<br><br>
이 때문에 createContext도 제네릭 타입으로 정의돼 있습니다  
그래서 홑화살괄호를 여기 createContext와 매개변수 목록 사이에 추가해  
이 컨텍스트 객체를 좀더 자세히 정의해야 합니다

<br><br>

```tsx
const TodosContext = React.createContext<>({
```

<br><br>
우리는 이 괄호 안에서 타입을 정의하지 않았으니까요  
이건 실제로 동작하는 자바스크립트 코드죠
자바스크립트 객체를 만들고 프로퍼티를 추가한 겁니다  
객체의 타입을 정의한 게 아니죠  
여기서 타입을 지정한 건 이 인수의 타입뿐입니다

<br><br>

```tsx
  removeTodo: (id: String) => {},

```

<br><br>
진짜 함수를 구현한 거니까요 이 객체의 타입을 정의하려면  
제네릭 타입 기능을 사용해야 합니다  
홑화살괄호를 추가하고 이 컨텍스트 객체의 타입을 정의해야 하죠

<br><br>

```tsx
const TodosContext = React.createContext<{

}>({
```

<br><br>
이제 여기에 명확하게 items를 Todo 배열로 정의할게요
여기에는 순수하게 타입만 정의했습니다  
여기 명확하게 items는 Todo 배열 타입이라고 표시했죠

<br><br>

```tsx
const TodosContext = React.createContext<{
  items: Todo[];
}>;
```

<br><br>
이를 위해 Todo를 models 폴더에 있는 todo 파일에서 import합니다

<br><br>

```tsx
import Todo from "../models/todo";
```

<br><br>
다음으로 addTodo는 함수 타입일 거고 반환값은 없습니다

<br><br>

```tsx
import React from "react";
import Todo from "../models/todo";

const TodosContext = React.createContext<{
  items: Todo[];
  addTodo: () => void;
}>;
```

<br><br>
이게 아래 있는 코드와의 다른 점이죠

<br><br>

```tsx
const TodosContext = React.createContext<{
  items: Todo[];
  addTodo: () => void;
}>({
  items: [],
  addTodo: () => {},
```

<br><br>
아래는 진짜 함수를 정의한 겁니다 본문이 비어있을 뿐이죠  
다음으로 removeTodo는 역시 함수 타입이고 반환값은 없습니다  
하지만 인수로 문자열을 받죠

<br><br>

```tsx
import React from "react";
import Todo from "../models/todo";

const TodosContext = React.createContext<{
  items: Todo[];
  addTodo: () => void;
  removeTodo: (id: string) => void;
}>({
  items: [],
  addTodo: () => {},
  removeTodo: (id: String) => {},
});
```

<br><br>
TodosContext가 완성됐습니다  
이제 컨텍스트를 제공해주는 컴포넌트를 만들 거예요  
앞선 강좌에서 여러 번 했던 겁니다  
컨텍스트의 상태(state)를 관리하는 역할을 하죠  
TodosContextProvider 컴포넌트를 추가합니다

<br><br>  
이건 함수형 컴포넌트일 테니까 타입을 React.FC로 설정해야 합니다.  
함수형 컴포넌트를 저장한다는 표시로요

<br><br>

```tsx
const TodosContextProvider: React.FC = () => {};
```

<br><br>
여기서는 렌더링이 가능한 콘텐츠를 반환해야 합니다  
그게 함수형 컴포넌트의 구성 요소니까요  
그래서 저는 JSX 코드를 반환할 겁니다  
정확히는 TodosContext.Provider를 반환하는 거죠
안에는 props.children를 넣고요;

<br><br>
todos-context.tsx
<br><br>

```tsx
const TodosContextProvider: React.FC = (props) => {
  return <TodosContext.Provider>{props.children}</TodosContext.Provider>;
};
```

<br><br>
children은 기본 프로퍼티라서 사용하기 위해  
props에 추가로 정의할 필요가 없습니다  
TodosContextProvider 안에서 이 컨텍스트의 state를 관리할 겁니다  
그럼 먼저 여기서 useState()를 호출하고요  
그러려면 ‘react’에서 useState를 불러와야겠죠  
Todo의 state를 관리하기 위해  
App.tsx에서 썼던 방식을 사용할 겁니다
<br><br>
todos-context.tsx
<br><br>

```tsx
const TodosContextProvider: React.FC = (props) => {
  useState();
  return <TodosContext.Provider>{props.children}</TodosContext.Provider>;
};
```

<br><br>
Todo의 state를 관리하기 위해 여기 App.tsx에서 썼던 방식을 사용할 겁니다  
App 컴포넌트에서 이런 상태 관리 코드와 함수들을 모두 복사하거나 잘라내고요  
그런 다음 해당 코드를 TodosContextProvider 컴포넌트에 넣습니다
<br><br>
<br><br>
todos-context.tsx
<br><br>

```tsx
import React, { useState } from "react";
import Todo from "../models/todo";

const TodosContext = React.createContext<{
  items: Todo[];
  addTodo: () => void;
  removeTodo: (id: string) => void;
}>({
  items: [],
  addTodo: () => {},
  removeTodo: (id: String) => {},
});

const TodosContextProvider: React.FC = (props) => {
  const [todos, setTodos] = useState<Todo[]>([]);

  const addTodoHandler = (todoText: string) => {
    const newTodo = new Todo(todoText);

    setTodos((prevTodos) => {
      return prevTodos.concat(newTodo);
    });
  };

  const removeTodoHandler = (clickedId: string) => {
    setTodos((prevtodos) => {
      return prevtodos.filter((prevTodo) => prevTodo.id !== clickedId);
    });
  };
  return <TodosContext.Provider>{props.children}</TodosContext.Provider>;
};
```

<br><br>
이 코드들은 아직 유효하고 사용하는 데도 문제가 없으니까요  
이제 코드가 추가되었으니 ‘contextValue’를 만들겠습니다

<br><br>
todos-context.tsx
<br><br>

```tsx
  const contextValue =

```

<br><br>
이 상수는 객체형이고, 형태는 이와 같을 겁니다 이런 타입과 값들을 갖겠죠

<br><br>

```tsx
  items: Todo[];
  addTodo: () => void;
  removeTodo: (id: string) => void;
```

<br><br>
그럼 items에 todos를 할당하고요

<br><br>

```tsx
const contextValue = {
  items: todos,
};
```

<br><br>
todos는 여기서 받을 겁니다

<br><br>

```tsx
const TodosContextProvider: React.FC = (props) => {
  const [todos, setTodos] = useState<Todo[]>([]);
```

<br><br>
그리고 addTodo에는 addTodoHandler를 넣고  
removeTodo에는 removeTodoHandler를 넣습니다

<br><br>

```tsx
const contextValue = {
  items: todos,
  addTodo: addTodoHandler,
  removeTodo: removeTodoHandler,
};
```

<br><br>
이제 value에 contextValue를 넣으면 되겠네요

<br><br>

```tsx
    <TodosContext.Provider value={contextValue}>

```

<br><br>
설정하고 나면 value에 빨간 밑줄이 생깁니다  
오류 표시가 생긴 이유는 여기 있는 contextValue 객체가  
생성된 객체의 형태가 위쪽에 정의한 형태와 일치하지 않기 때문입니다

<br><br>

```tsx
const TodosContext = React.createContext<{
  items: Todo[];
  addTodo: () => void;
  removeTodo: (id: string) => void;
}>;
```

<br><br>
뭐가 다른 걸까요? 차이는 addTodo()에 있습니다  
설명을 위해 일부러 이렇게 만들었어요  
처음에 컨텍스트를 정의할 때는  
addTodo()가 매개변수를 받지 않을 거라고 했죠  
하지만 이 객체에 사용할 실제 함수를 구현했을 때는 매개변수를 받았습니다

<br><br>
매개변수로 todoText를 받죠

<br><br>

```tsx
  const addTodoHandler = (todoText: string) => {

```

<br><br>
그런 다음 addTodoHandler 함수를 addTodo의 값으로 할당한 겁니다
그래서 아래 쪽에 이런 오류가 생긴 거고요  
그러니까 이 오류가 발생한 건 여기에서 contextValue 객체를 생성할 때  
어떤 타입의 데이터를 받을 건지  
contextValue 객체 안에 정의하지 않았기 때문입니다

<br><br>

```tsx
    <TodosContext.Provider value={contextValue}>

```

<br><br>
타입을 지정해서 이 오류를 해결해야 할 것 같네요  
여기에도 타입 정의를 추가하면 되겠죠  
받고 싶은 타입을 명확하게 표시하는 겁니다  
추론된 타입을 사용하지 않고요

<br><br>
이런 상황은 타입을 명시적으로 설정하면 해결할 수 있습니다  
잘못된 타입을 추론하지 않도록요 지금 문제되는 게 그거니까요

<br><br>
제가 설정하고 싶은 건 위쪽에 있는 것과 같은 타입입니다  
복사해서 아래 쪽에 붙여 넣으면 되겠죠  
addTodo에 오류 표시가 생겼습니다

<br><br>

```tsx
const contextValue: {
  items: Todo[];
  addTodo: () => void;
  removeTodo: (id: string) => void;
} = {
  items: todos,
  addTodo: addTodoHandler,
  removeTodo: removeTodoHandler,
};
```

<br><br>
고칠 수 있긴 하겠지만 복사해서 붙여 넣는 건 답이 아닌 것 같네요  
이 타입 정의를 복사해서 사용하는 대신 타입은 한 번만 정의하고  
정의한 값을 그 타입이 필요한 모든 위치에 사용할 겁니다

<br><br>
이 기능을 구현하기 위해 다시 한번 타입 별칭(Type alias)을 사용할 겁니다
여기 ‘TodosContextObj’라는 타입을 만들겠습니다  
그리고 타입 별칭에 정의할 타입을 저장합니다

<br><br>

```tsx
type TodosContextObj = {
  items: Todo[];
  addTodo: () => void;
  removeTodo: (id: string) => void;
};
```

<br><br>
홑화살괄호 안에 별칭을 넣고요 그리고 타입 별칭을 여기에도 쓰겠습니다

<br><br>

```tsx
type TodosContextObj = {
  items: Todo[];
  addTodo: () => void;
  removeTodo: (id: string) => void;
};
```

<br><br>

```tsx
const TodosContext = React.createContext<TodosContextObj>({

```

<br><br>
동일한 코드가 반복되지 않도록요  
여기서도 별칭을 사용하겠습니다
<br><br>

```tsx
  const contextValue: TodosContextObj = {

```

<br><br>
이제 이 오류를 해결해야겠네요

<br><br>

```tsx
const contextValue: TodosContextObj = {
  items: todos,
  addTodo: addTodoHandler,
  removeTodo: removeTodoHandler,
};
```

<br><br>
타입 정의를 수정해서 오류를 해결할 수 있죠  
지금은 코드를 복사해서 사용한 게 아니기 때문에  
한 곳에서만 수정하면 되고요  
여기 있는 addTodo() 함수는 매개변수로  
문자열을 받아야 합니다, 매개변수가 없으면 안되죠

<br><br>

```tsx
type TodosContextObj = {
  items: Todo[];
  addTodo: (text: string) => void;
  removeTodo: (id: string) => void;
};
```

<br><br>
이게 주요 수정사항입니다 이제 contextValue에 있던 문제는 해결됐고  
문제없는 컨텍스트를 Provider 컴포넌트의 value 프로퍼티 값으로 지정했죠  
TodosContextProvider가 준비됐습니다 이제 Provider를 export하고요
default키워드를 사용했습니다.
<br><br>

```tsx
export default TodosContextProvider;
```

<br><br>
그리고 TodosContext도 내보내겠습니다  
타입 정의 말고 컨텍스트 객체만요  
나중에 컨텍스트 훅(Hook)을 사용할 때 필요할 테니까요

<br><br>

```tsx
export const TodosContext = React.createContext<TodosContextObj>({

```

<br><br>

# 만든 컨텍스트 다른 컴포넌트에서 사용하기

<br><br>
제가 만든 컨텍스트를 다른 컴포넌트에 연결할 겁니다  
먼저, 우리는 더 이상 Todo를 App.tsx 파일에서 관리할 필요가 없습니다  
여기 있는 onAddTodo도 NewTodo 컴포넌트에서 지우고  
Todos 컴포넌트의 프로퍼티들도 지울 겁니다

<br><br>

```tsx
function App() {
  return (
    <div className="App">
      <NewTodo />
      <Todos />
    </div>
  );
}
```

<br><br>
models 폴더에서 Todo를 가져오는 부분과 useState를 가져오는 부분도 지울 거고요

<br><br>

```tsx
import NewTodo from "./components/NewTodo";
import Todos from "./components/Todos";

function App() {
  return (
    <div className="App">
      <NewTodo />
      <Todos />
    </div>
  );
}

export default App;
```

<br><br>
App 컴포넌트가 훨씬 가벼워졌죠  
이제 여기 있는 컴포넌트에서 컨텍스트를 쓸 수 있기 때문입니다  
Todos 컴포넌트를 예로 들어 보겠습니다  
이제 react에서 useContext를 가져올 수 있습니다  
우리가 만든 TodosContext도요  
중괄호를 사용해 이름을 붙여서 가져오겠습니다  
제가 여기 import한 건 ‘Context’입니다 Provider가 아니죠  
그래서 여기에 해당 컨텍스트를 활용할 수 있는 거고요

<br><br>
useContext()를 호출하고, 앞에서 배운 것처럼  
TodosContext를 넣습니다  
그럼 이렇게 todosCtx에 컨텍스트를 받아 접근할 수 있죠

<br><br>

```tsx
const todosCtx = useContext(TodosContext);
```

<br><br>
이 상수에 마우스 커서를 올려보면 해당 값의 타입에 자동적으로  
우리 컨텍스트의 타입이 지정된 걸 볼 수 있습니다

<br><br>

```tsx
const todosCtx: TodosContextObj;
```

<br><br>
타입스크립트 덕분에 모든 게 정상적으로 연결되었죠  
이제 todosCtx를 사용해서 컨텍스트에 있는 items에 접근할 수 있습니다  
Todo 항목들이 저장된 곳이죠  
그리고 onRemoveTodo 프로퍼티 대신에  
todosCtx.removeTodo를 사용할 수 있습니다

<br><br>

```tsx
onRemoveTodo={todosCtx.removeTodo.bind(null, item.id)}

```

<br><br>
bind()는 남겨둬야 합니다 나중에 ID를 가져올 때  
사용할 함수를 미리 설정해 놓고 싶으니까요  
이제 여기 있는 props 인수는 삭제해도 됩니다  
그리고 우리가 만든 프로퍼티에 대한  
타입 정의도 React.FC에서 삭제하고요

<br><br>
이 Todos 컴포넌트는 더 이상  
props를 사용하지 않고 컨텍스트를 사용하죠  
그래서 깔끔하게 사용하지 않는 타입 정의는 지울 겁니다

<br><br>

```tsx
const Todos: React.FC = () => {

```

<br><br>
마찬가지로 Todo 타입을 models 폴더에서 가져오는 부분도 지우고요  
컨텍스트 API 덕분에 Todos 컴포넌트가 깔끔하게 정리됐습니다  
이제 컨텍스트 API를 사용해서 NewTodo 컴포넌트도 수정하겠습니다

<br><br>
Todo를 추가하는 데 props를 사용하지 않도록 수정할 겁니다  
이제 더 이상 부모 컴포넌트에서 Todos를 관리하지 않으니까요  
이제 컨텍스트를 이용할 겁니다

<br><br>
react에서 useContext를 가져오고
TodosContext도 가져오겠습니다

<br><br>

```tsx
import React, { useRef, useContext } from "react";
import { TodosContext } from "../store/todos-context";
```

<br><br>
다른 컴포넌트에서 했던 것처럼 그리고 앞선 강의에서 했던 것처럼  
useContext()를 호출해 TodosContext를 넣고요  
todosCtx로 컨텍스트를 받습니다  
그리고 props.onAddTodo()를 호출하는 대신  
todosCtx.addTodo()를 호출하고  
인수로 enteredText를 넘깁니다  
컴포넌트 안에 있는 나머지 코드는 그대로 둘 겁니다

<br><br>

```tsx
import React, { useRef, useContext } from "react";
import { TodosContext } from "../store/todos-context";
import classes from "./NewTodo.module.css";

const NewTodo: React.FC = () => {
  const todoTextInputRef = useRef<HTMLInputElement>(null);
  const todoCtx = useContext(TodosContext);
  const submitHandler = (event: React.FormEvent) => {
    event.preventDefault();

    const enteredText = todoTextInputRef.current!.value;
    if (enteredText.trim().length === 0) {
      return;
    }
    todoCtx.addTodo(enteredText);
  };
  return (
    <form onSubmit={submitHandler} className={classes.form}>
      <label htmlFor="text">Todo text</label>
      <input type="text" id="text" ref={todoTextInputRef} />
      <button>Add Todo</button>
    </form>
  );
};
export default NewTodo;
```

<br><br>
한 가지 고쳐야 할 건 컴포넌트 정의 부분입니다  
이제 props를 넘겨받지 않으니까 매개변수는 지워야 합니다  
그러고 나면 함수형 컴포넌트의 프로퍼티 정의도 지워야겠죠  
이제 프로퍼티를 만들어 사용할 필요가 없으니까요
이 부분도 지워서 컴포넌트를 정리해야 합니다

<br><br>
이제 딱 한 가지가 남았습니다 TodosContextProvider 컴포넌트를 적용하는 부분이죠.  
useContext()를 사용하긴 했지만, 정상적으로 동작하려면  
해당 컨텍스트를 사용하는 컴포넌트들을 컨텍스트 Provider로 감싸야 합니다

<br><br>
이 작업은 App.tsx 파일에서 할 수 있죠
코드에서 div 태그를 TodosContextProvider로 바꿉니다
<br><br>
그러려면 ‘./store/todos-context’에서 가져와야 하고요  
 아주 중요한 사항입니다, 이게 없다면 동작하지 않을 거예요  
 하지만 여기까지 했다면 되겠죠  
 ‘Learn React’, ‘Learn TypeScript’, 다른 것도 되고요

<br><br>
App.tsx파일
<br><br>

```tsx
import NewTodo from "./components/NewTodo";
import Todos from "./components/Todos";
import TodosContextProvider from "./store/todos-context";

function App() {
  return (
    <TodosContextProvider className="App">
      <NewTodo />
      <Todos />
    </TodosContextProvider>
  );
}

export default App;
```
