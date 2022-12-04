---
title: "#429[udemy react 강의노트] Todo 제거하기"
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

# Todo 제거하기

<br><br>
먼저 TodoItem.tsx로 가서 클릭될 수 있게 만들 겁니다  
그리고 클릭되면 Todo를 삭제하는 거죠  
이를 위해 list item 즉, TodoItem에  
onClick 프로퍼티를 추가하겠습니다  
이 onClick 프로퍼티가 항목이 클릭되었을 때  
특정 코드를 동작시킬 겁니다

<br><br>
그리고 여기에 함수를 지정할 겁니다  
함수는 props를 통해 받게 될 거예요  
보통 그렇게 하니까요, 지금까지 계속 그렇게 해왔고요,  
대부분의 demo 프로그램에서요  
<br><br>
그래서 여기 onClick에서 props를 통해 받은 함수를 호출할 겁니다  
아마도 여기서는 ‘onRemoveTodo’라는 프로퍼티를 받을 거고요  
그런데 여기 빨간 밑줄이 생겼네요 왜 생긴 걸까요?

<br><br>

```tsx
import classes from "./TodoItem.module.css";

const TodoItem: React.FC<{ todoText: string }> = (props) => {
  return (
    <li className={classes.item} onClick={props.onRemoveTodo}>
      {props.todoText}
    </li>
  );
};
export default TodoItem;
```

<br><br>
프로퍼티를 사용하고 있는데, 제가 사용 중인 이 프로퍼티를  
함수형 컴포넌트의 타입 정의에 포함시키지 않았기 때문입니다

<br><br>

```tsx
const TodoItem: React.FC<{ todoText: string }> = (props) => {

```

<br><br>
정리하자면 이 컴포넌트 함수는 props 객체를 매개변수로 받을 거고  
그 props에는 children 프로퍼티 같은 기본 프로퍼티와  
제가 만든 프로퍼티가 모두 포함돼 있어야 합니다  
하지만 지금은 text 프로퍼티 밖에 없죠  
onRemoveTodo 프로퍼티는 없습니다

<br><br>
저는 onRemoveTodo라는 프로퍼티도 받고 싶으니까  
여기 추가로 정의해야 하죠  
onRemovedTodo 프로퍼티도 받겠다고 표시해야 합니다  
프로퍼티 타입도 지정해야 하고요  
<br><br>
여기서는 함수 타입이죠 onClick에 연결할 함수요  
그럼 함수 타입으로 정의해 주고요  
반환값은 필요 없으니까 이 함수는 아무것도 반환하지 않습니다

<br><br>

```tsx
const TodoItem: React.FC<{ todoText: string; onRemoveTodo: () => void }> = (

```

<br><br>
매개변수도 사실 필요하진 않아요 하지만 onClick의 경우  
onRemoveTodo 함수를 onClick에 넘기는 거니까  
event 객체를 인수로 받게 될 겁니다

<br><br>

```tsx
const TodoItem: React.FC<{
  todoText: string;
  onRemoveTodo: (event: React.MouseEvent) => void;
}> = (props) => {
  return (
    <li className={classes.item} onClick={props.onRemoveTodo}>
      {props.todoText}
    </li>
  );
};
export default TodoItem;
```

<br><br>
그 타입을 여기에 지정하면 되겠죠  
이건 onClick에 넣어야 할 함수의 형태를  
아주 정확하게 구현한 겁니다

<br><br>
사실, 인수의 타입을 정의하는 건 선택 사항이에요  
여기서 인수를 사용하지 않으니까요 그래서 생략할 수 있죠  
함수 정의가 좀더 짧아지도록 이렇게만 정의하겠습니다

<br><br>

```tsx
const TodoItem: React.FC<{
  todoText: string;
  onRemoveTodo: () => void;
}> = (props) => {
```

<br><br>
이제 이런 형태의 함수를 onRemoveTodo에서 받을 겁니다  
이렇게 생긴 함수가 onClick에 연결되는 거죠  
이제 TodoItem을 사용하는 곳으로 가서  
onRemoveTodo 프로퍼티에 이런 형태의 함수를 연결하겠습니다

<br><br>
그럼 Todos.tsx 파일로 갑시다  
지금은 text 프로퍼티만 넘기고 있지만

<br><br>

```tsx
const Todos: React.FC<{ items: Todo[] }> = (props) => {

```

<br><br>
이제 onRemoveTodo 프로퍼티도 넘겨야 합니다  
고맙게도 타입스크립트 덕분에 자동 완성 기능을 쓸 수 있죠

<br><br>

```tsx
<TodoItem key={item.id} todoText={item.text} onRemoveTodo={} />
```

<br><br>
이제 onRemoveTodo에 우리가 받아야 할 형태의 함수를 넣어줄 겁니다  
그런데 저는 Todos 컴포넌트 안에 그 함수를 정의하고 싶지 않습니다  
Todos는 App.tsx에서 관리되고 있으니까요

<br><br>
다시 말해, 여기서는 프로퍼티를 전달만 하고 싶습니다. 이 컴포넌트에서 받은 다음에요  
프로퍼티 전달 통로를 만드는 거죠 그럼 이 Todos 컴포넌트에서도  
매개변수로 프로퍼티를 한 개 더 받겠습니다  
이름도 똑같이 ‘onRemoveTodo’로 하고요  
하지만 타입은 역시 함수여야 하죠 아무것도 반환하지 않을 겁니다

<br><br>

```tsx
const Todos: React.FC<{ items: Todo[]; onRemoveTodo: () => void }> = (

```

<br><br>
그리고 여기서 props.onRemoveTodo를  
TodoItem의 onRemoveTodo 프로퍼티 값으로 넘깁니다  
이 모든 건 앞선 강의에서 배운 것들이죠  
함수를 다른 컴포넌트에 전달하는 것도
프로퍼티 전달 통로를 만들어  
여러 층의 컴포넌트를 거쳐 프로퍼티를 전달하는 것도요

<br><br>
마지막으로 App.tsx에서 Todos에도 onRemoveTodo를 추가합니다  
그리고 여기에 최종적으로 호출될 함수를 구현할 겁니다  
‘removeTodoHandler’ 함수죠, 이 함수는 이런 형태를 가질 거고요  
인수도 없고 반환하는 것도 없죠

<br><br>
이 removeTodoHandler() 함수를 onRemoveTodo 프로퍼티에 값으로 넣습니다

<br><br>

```tsx
<Todos items={todos} onRemoveTodo={removeTodoHandler} />
```

<br><br>
하지만 이 함수 정의에는 부족한 게 있습니다  
삭제할 Todo를 구분할 방법이 없다는 거죠  
식별할 수 있는 방법이 필요합니다  
모든 Todos에는 고유한 id가 있기 때문에  
그 id를 사용하면 해결할 수 있죠

<br><br>
그래서 App.tsx의 removeTodoHandler에서는  
삭제할 Todo의 id를 받아야 합니다
todoId의 타입은 문자열이죠

<br><br>

```tsx
  const removeTodoHandler = (clickedId: string) => {

```

<br><br>
타입이 문자열인 이유는 우리가 id에 사용한 타입이
이 Todo 객체에서 문자열이기 때문입니다

<br><br>

```tsx
class Todo {
  id: string;
  text: string;

  constructor(todoText: string) {
    this.text = todoText;
    this.id = new Date().toISOString();
  }
}

export default Todo;
```

<br><br>
todoId를 받으면 이 함수 형식을 사용해 setTodos()를 호출합니다  
이전 state를 기반으로 현재 state를 업데이트 할 거니까요  
그런 다음 prevTodos.filter() 호출합니다  
삭제할 Todo를 제거하기 위해서죠  
모든 Todo를 살펴보면서 특정 Todo의 id가 todoId와 같으면 삭제하고
다른 Todo들은 남겨둡니다

<br><br>

```tsx
const removeTodoHandler = (clickedId: string) => {
  setTodos((prevtodos) => {
    return prevtodos.filter((prevTodo) => prevTodo.id !== clickedId);
  });
};
```

<br><br>
이렇게 구현하면 되죠 이런 식으로 Todo 목록에서 Todo를 삭제하고,  
Todo를 삭제할 때는 여기서 받은 id를 이용하는 겁니다  
여기서는 id가 다른지 확인하죠  
id가 일치하지 않는 Todo들은 남겨두고 싶으니까요

<br><br>
하지만 문제가 있습니다  
RemoveTodoHandler()는 문자열 타입의 todoId가 필요한데  
문자열 타입은 onRemoveTodo()의 매개변수 타입이 아닙니다
Todos 컴포넌트에서는 이 함수가 매개변수를 받지 않는다고 정의했어요

<br><br>

```tsx
const Todos: React.FC<{ items: Todo[]; onRemoveTodo: () => void }> = (

```

<br><br>

이건 벌써 잘못됐죠 우리는 id가 필요하고 id는 문자열 타입이니까요

<br><br>
Todos.tsx
<br><br>

```tsx
const Todos: React.FC<{ items: Todo[]; onRemoveTodo: (id: string) => void }> = (

```

<br><br>
이제 TodoItem에 있는 onRemoveTodo가  
잘못 정의됐다고 나오죠  
여기에는 여전히 매개변수가 없다고 정의되어 있으니까요

<br><br>
TodoItem.tsx
<br><br>

```tsx
const TodoItem: React.FC<{
  todoText: string;
  onRemoveTodo: () => void;
}> = (props) => {
```

<br><br>
이 역시도 잘못됐습니다 여기서도 정의를 수정하면 되겠죠  
id 타입도 맞게 설정해 주고요

<br><br>

```tsx

const TodoItem: React.FC<{
  todoText: string;
  onRemoveTodo: (id: string) => void;
}> = (props) => {
```

<br><br>
그러려면 또 프로퍼티를 한 개 더 받아야겠지만요  
아니면 Todos.tsx에서 onRemoveTodo.bind()를 호출해도 됩니다
<br><br>
bind()는 자바스크립트에서 제공하는 메서드로  
이 메서드를 사용하면 실행할 함수를 미리 설정할 수 있습니다  
앞선 강의에서도 몇 번 본 적이 있을 거예요  
호출될 함수 안에서 먼저 이 키워드가 무엇을 가리키는지 지정합니다  
이 값은 중요하지 않으니까 null로 설정하고요

<br><br>

```tsx
const Todos: React.FC<{ items: Todo[]; onRemoveTodo: (id: string) => void }> = (
  props
) => {
  return (
    <ul className={classes.todos}>
      {props.items.map((item) => (
        <TodoItem
          key={item.id}
          todoText={item.text}
          onRemoveTodo={props.onRemoveTodo.bind(null, item.id)}
        />
      ))}
    </ul>
  );
};
```

<br><br>
그 다음, 우리가 여기에 인수에 넣는 값을  
onRemoveTodo()가 매개변수로 받습니다  
이 값이 id가 되어야겠네요 item.id를 넣겠습니다  
이렇게 하면 모든 오류 표시가 사라집니다  
이제 onRemoveTodo() 함수는 여기서 id를 받을 테니까요

<br><br>
이를 통해 Todo를 삭제할 수 있게 됩니다
