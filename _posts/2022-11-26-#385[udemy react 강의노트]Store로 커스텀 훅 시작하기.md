---
layout: post
categories: ["udemy","react-study"]
title: "#385[udemy react 강의노트] Store로 커스텀 훅 시작하기"
excerpt: "React animation"
published: true
categories:
  - Blog
tags:
  - [React, Custom hook]

toc: true
toc_sticky: false

date: 2022-11-26
last_modified_at: 2022-11-26
---

<br><br>

# Store로 커스텀 훅 시작하기

<br><br>
이를 위해서 새로운 폴더 hooks-store를 생성한다.  
이제 우리만의 전역적 state 관리 store와 솔루션을  
오직 리액트 자바스크립트와 리액트 훅만을 사용해서 구축할 것이기 때문이다.  
다른 것은 사용하지 않는다. context API 또한 사용하지 않는다.

<br><br>
이 hooks-store 안에 새로운 store.js 파일을 생성한다.  
그리고 이 파일에서 리액트와 자바스크립트만으로  
state를 전역적으로 관리하는 솔루션을 시작할 것이다.  
<br><br>
그래서 이 파일을 사용해서 globalState를 관리한다.  
일부러 이렇게 일반적인 이름을 사용하는 것이다.  
products 뿐만 아니라 이 store를 모든 종류의 state에 대해서  
사용할 수 있도록 하기 위해서다.
globalState가 객체가 될 수 있다
<br><br>
그리고 앱 안의 몇 군데에 state에 대한 변경 사항을  
듣고 따라갈 수 있는 곳이 있어야 한다  
그것들을 위한 모든 변수들이 이 배열인 listeners가 된다.
<br><br>

```jsx
let globalState = {};
let listeners = [];
```

<br><br>
이 두개는 모두 일반적인 변수로 이 파일안에 있다는 점에 유의해야한다.  
블록-범위로 전역적으로 사용 가능한 변수가 아니다
이 파일 안에서만 사용 가능하다.  
어떤 클래스에도 결부되어 있지 않다. 외부로 내보내지도 않는다.  
그래서 이제 listeners와 state가 있고 actions도 필요하다.

<br><br>

```jsx
let globalState = {};
let listeners = [];
let actions = {};
```

<br><br>
그럼 커스텀 훅을 생성한다. 이름은 useStore이라고 한다.  
리액트의 여느 훅과 마찬가지로 단지 함수다.
이 커스텀 훅 안에서, 물론 또 다른 리액트 훅을 사용할 수 있다.

<br><br>

```jsx
const useStore = () => {};
```

<br><br>
그리고 useState를 import 해온다. 왜냐하면 여기서 우리의 목표는  
다른 컴포넌트로 이끌어서 다시 렌더링 할 수 있도록 하는 메커니즘을 갖는 것이다.  
useState는 우리가 state를 관리할 수 있도록 해준다.  
이 state를 업데이트 했을 때, 이 useState를 사용하는  
모든 컴포넌트는 재-랜더링 하게 된다.

<br><br>
그리고 리액트 훅 섹션에서 배운 것인데 컴포넌트가 커스텀 훅을 사용하고,  
그 커스텀 훅이 useState를 사용하면, 그 커스텀 훅을 입력해 주었을 때  
그 커스텀 훅을 사용하는 컴포넌트가 재렌더링 된다.  
그 커스텀 훅이 재렌더링을 유발한다  
우리는 그 점을 여기서 이용하는 것이다.

<br><br>
따라서 커스텀 훅 안에 state가 있어야 한다  
이 state는 사실 우리 globalState가 된다  
즉, 훅 밖에서 정의된 변수를 사용하는 것이다.  
훅 밖에서 정의되었기 때문에 전역적이다.

<br><br>

우리가 useStore를 호출 했을 때 재생성되는 것이 아니다.  
커스텀 훅을 사용하는 모든 컴포넌트에 대해서 개별로 생성되는 것도 아니다.  
대신 파일이 어딘가에서 처음 불러와질 때 한번 생성되는 것이다.  
그런 다음 이 동일한 파일에서 import하는 다른 파일들은  
모두 같은 globalState를 사용하게 된다.  
불러오는 모든 파일 간에 데이터를 공유한다는 것이다.

<br><br>
이전에 우리가 커스텀 훅으로 했던 것과 다르다. 전에 했던 것에서는 반대였다.  
논리의 흐름을 공유하고 데이터는 공유하지 않았다.  
지금은 훅 밖에서 데이터를 관리함으로써 로직과 데이터를 공유한다.  
훅 안에서는 공유되지 않는다. 각 컴포넌트는 각각의 데이터를 갖는다.  
그렇지만 훅 밖에서 관리함으로써 이 파일을 import하는  
모든 파일 또는 그 파일 중 어떤 파일은 똑같은 공유된 데이터를 가진다

<br><br>
여기서 우리는 useState를 사용하고, 그렇기 때문에 두개의 값을 가지는데,  
여기서 디스트럭쳐링 해줄 수 있다.  
그렇지만 우리가 관심 있는 것은 오직 setState 값이다.  
원하는 이름으로 설정해 줄 수 있다.  
여기서 globalState라는 추상적 이름을 사용했기 때문에  
이것은 setState라고 이름지어본다.

<br><br>

```jsx
const setState = useState(globalState)[1];
```

<br><br>
그리고 setState 함수만 갖도록 할 것이다.  
useState가 반환하는 배열의 두번째 값에만 관심이 있기 때문이다.  
현재 state에 대한 캡쳐에는 관심이 없고 두번째 값에만 관심이 있다.  
즉 업데이트 하는 함수다.

<br><br>
그렇다면 왜 이것에 관심이 있는것일까.  
이것에 관심이 있는 이유는, 이 함수가 호출될때마다  
커스텀 훅을 사용하는 컴포넌트가 재렌더링 되고  
우리는 나중에 컴포넌트를 재렌더링 할 때 이것이 필요하게 된다.  
state가 변할때 말이다.  
<br><br>
아직 그 단계에 이르진 못했지만, 그 이유로 이 함수가 있는 것이다.  
이제 이 actions 함수를 listeners 배열에 추가해 줄 것이다.

<br><br>
listeners를 함수로 가득찬 배열로 만들어서 호출 했을때  
훅을 사용하는 모든 컴포넌트들을 업데이트 하도록 하기 위해서다.  
globalState에 맞춰 업데이트 되는 listener 리스트,  
컴포넌트 리스트가 있도록 하는 것이다.  
이 함수에 대한 포인터를 listeners로 push 한다.
<br><br>
즉, 커스텀 훅을 사용하는 모든 컴포넌트들이  
각각의 setState 함수를 가지게 되고  
그런 다음 공유되는 listeners 배열에 추가되는 것이다.
<br><br>

```jsx
import { useState } from "react";

let globalState = {};
let listeners = [];
let actions = {};

const useStore = () => {
  const setState = useState(globalState)[1];
  listeners.push(setState);
};
```

<br><br>
따라서 이 listeners = [] 배열은 컴포넌트들이 더 추가 될때마다 증가한다  
그렇기 때문에 당연히 컴포넌트가 더이상 마운트되지 않으면  
listeners 배열에서도 없어지는 것이 좋다.  
<br><br>
그래서 useEffect 훅도 여기에 불러온다.  
그리고 여기서 useEffect를 사용해준다.  
useEffect안에 있는 항목을 listeners로 push하도록 해서  
훅을 사용하는 컴포넌트가 업데이트 될때마다 이 부분이 수행된다.
두번째 인자에 deps를 정의해준다.  
빈배열을 넣어주게 되면 이 useEffect 부분이  
커스텀 훅을 사용하는 컴포넌트에 대해서,  
이 컴포넌트가 마운트 될 때에만 수행하도록 해준다

<br><br>

```jsx
import { useState, useEffect } from "react";

let globalState = {};
let listners = [];
let actions = {};

const useStore = () => {
  const setState = useState(globalState)[1];

  useEffect(() => {
    listners.push(setState);
  }, []);
};
```

<br><br>
우리는 컴포넌트가 마운트 될 때에 listener가 업데이트 되길 원한다.  
또한 cleanup 함수도 넣어준다.  
컴포넌트가 더이상 마운트 되어있지 않을 때 해당 listener를 제거하도록 해준다.  
비어있는 deps는 단순히 한번 수행되고  
컴포넌트가 제거될 때 cleanup 함수가 실행된다.

<br><br>
listener를 제거할 함수를 작성해준다.  
그러기 위해서 listeners = listeners라고 설정해주고  
방금 입력한 listeners를 필터링 해준다.  
이 안에는 li를 입력 해주고 filter()에 전달한 이 함수는  
listeners 배열 안의 모든 요소들에 대해서 수행되고  
우리는 setState와 일치하지 않는 모든 listeners들을 남겨둔다.
우리가 여기서 listeners에 setState를 전달했기 때문이다.

<br><br>

```jsx
const useStore = () => {
  const setState = useState(globalState)[1];

  useEffect(() => {
    listners.push(setState);

    return () => {
      listners = listners.filter((li) => li !== setState);
    };
  });
};
```

<br><br>
그리고 `listners.push(setState);`이부분은 폐쇄되어있기 때문에  
커스텀 훅을 사용하는 컴포넌트에 대한 setState 값은 캡쳐된다.  
따라서 컴포넌트가 더이상 마운트 되지 않을 때와,  
컴포넌트가 마운트 될 때의 setState는 같게 된다.

<br><br>
그렇기 때문에 여기서 !== 비교 연산자를 사용할 수 있다.  
비록 setState는 함수이고, 다른 객체이지만  
컴포넌트가 마운트 됐을 때 listeners와 같게 된다.  
여기 listeners.push(setState)의 setState와 같은 것이다.

<br><br>
그래서 우리는 컴포넌트가 더이상 마운트 되지 않을 때  
이 부분을 통해서 listener를 제거한다.

<br><br>

```jsx
return () => {
  listners = listners.filter((li) => li !== setState);
};
```

<br><br>  
지금 우리는 사실 useEffect의 deps를 정직하게 사용하고 있는 것은 아니다.  
[]에 deps가 없어서가 아니다. setState에 대한 deps가 존재한다.

<br><br>

```jsx
useEffect(() => {
  listners.push(setState);

  return () => {
    listners = listners.filter((li) => li !== setState);
  };
}, [setState]);
```

<br><br>
하지만 이것이 문제 되진 않는다.  
왜냐하면 setState는 useState에서 오는 것이고,  
리액트는 이 setState가 주어진 컴포넌트에 대해 절대 변하지  
않을 것을 보장한다.  
따라서 우리는 결코 변하지 않는 하나의 종속성을 가지는 것이고  
그렇다는 것은 이 useEffect 부분은 오직 한번만 수행되고  
cleanup 함수 또한 한번만 수행된다

<br><br>
참고로, 우리가 만약 디스트럭처링 문법을 사용했더라면  
deps의 이 setState는 지울 수 있다.  
그랬다면 린팅 도구가 이것이 업데이트 하는 함수라는 것을  
알아냈을 것이다.  
이 함수는 절대 변하지 않는다.  
그렇지만 이런식으로 두번째 값을 뽑아내게 되면 알아낼 수 없다

<br><br>

```jsx
useState(globalState)[1];
```

<br><br>
하지만 이 setState가 다시 수행될 일은 없기 때문에 문제되지 않는다.
이제 우리는 이 컴포넌트가 마운트 될 때  
커스텀 훅을 사용하는 컴포넌트에 대해서  
setState 함수를 listeners에 추가하도록 해주었다.
그리고 더이상 마운트 되어있지 않으면 제거하도록 해주었다.

<br><br>

# 스토어 훅 완성하기

<br><br>
이제 listeners를 관리해보자.  
이제 우리는 state를 변경할 수 있어야 한다.  
<br><br>
그러기 위해서는 컴포넌트에 있는 액션들을 날릴 수 있어야 한다.  
우리가 리덕스를 사용했을 때와 같은 개념이다.  
그렇기 때문에 여기 useStore안에 새로운 상수 const를 추가한다.  
dispatch라고 이름 짓는다. 이것은 실제로 함수여야 한다.  
액션을 취하고 그리고 이 액션이 발동하면 무언가 일어나야 한다.

여기에다가 이 액션에 대한  
이름 혹은 식별 Identifier를 입력해준다.

<br><br>

```jsx
const dispatch = (actionIdentifier) => {};
```

<br><br>
왜냐하면 우리는 구체적인 액션들을 서로 다른 곳에 정의해줄 것이기 때문이다.  
이 부분은 여전히 전체적인 store 관리 시스템에 관한 추상적 정의다  
그래서 여기에선 actionIdentifier를 취할 것이다.  
그리고 여기 위에 actions ={} 객체가 있다는 것을 염두에 둔다.  
나중에 store에 대한 구체적인 용도를 각각의 액션으로 정의 해주는 것이다.  
그래서 그런 액션들은 여기 actions 객체에 곧 등록될 것이다.

<br><br>

```jsx
import { useState, useEffect } from "react";

let globalState = {};
let listners = [];
let actions = {};

const useStore = () => {
  const setState = useState(globalState)[1];

  const dispatch = (actionIdentifier) => {};

```

<br><br>
action을 가져오는 곳에는 actions에 접근해서  
actionIdentifier를 가져오도록 해준다.  
actions가 등록되면 actionIdentifier가 나중에  
actions 객체에 대한 키가 된다.

<br><br>

```jsx
const dispatch = (actionIdentifier) => {
  actions[actionIdentifier];
};
```

<br><br>
이 액션은 함수여야 한다.  
그래서 actions = {} 이것은 객체여야 한다.  
actionIdentifier와 일치하는 키를 가지고  
값은 구체적인 함수가 된다.  
여기에 괄호를 추가해주어 호출해준다.

<br><br>

```jsx
actions[actionIdentifier]();
```

<br><br>
여기서 호출하는 함수에는 globalState를 전달한다.

<br><br>

```jsx
const dispatch = (actionIdentifier) => {
  const newState = actions[actionIdentifier](globalState);
};
```

<br><br>
이 모든 것이 사실 리덕스에서 일어났던 일들이다
리덕스에서, 결국에는 Reducer 함수에서 action을 취하고,  
그런 다음 그 action을 확인했고, 결국에는 새로운 state를 반환했다.  
여기서도 동일한 개념을 따르고 있는 것이다.  
<br><br>
actionIdentifier를 취하고 그 액션을 actions 객체에서 찾고  
그 액션은 함수로, 이 함수를 실행하면 Reducer 함수 같은  
이 `actions[actionIdentifier](globalState)` 함수가
새로운 state인 newState를 반환한다.  
<br><br>
이 newState는 이전의 state와 병합한 state인 것이다.  
그래서 globalState는 이전의 globalState를 복사한것과  
newState 안의 모든 것을 병합한 것이 된다.

<br><br>

```jsx
const dispatch = (actionIdentifier) => {
  const newState = actions[actionIdentifier](globalState);
  globalState = { ...globalState, ...newState };
};
```

<br><br>
따라서 globalState는 이전의 데이터 ...globalState에  
새로운 데이터 ...newState가 추가된 새로운 객체다.  
그럼 이제 해야하는 것은 단지 모든 listeners에  
이 업데이트된 state를 알리는 것이다.

<br><br>
listeners의 모든 listener를 돌아야 한다.  
for 루프문을 사용하여 여기 listeners는 물론  
여기의 전역적 listeners 배열이다.  
listeners에 우리가 추가해준게 setState라는 함수다.  
여기서 listener를 호출한다  
그리고 globalState를 전달한다.

<br><br>

```jsx
const dispatch = (actionIdentifier) => {
  const newState = actions[actionIdentifier](globalState);
  globalState = { ...globalState, ...newState };

  for (const listener of listeners) {
    listener(globalState);
  }
};
```

<br><br>
즉 listener에 전달하는 것은 새로운 globalState다.  
이것이 하는 일은 이 리액트의 state를 업데이트하는 것이다.  
왜냐하면 새로운 globalState와 함께 listener에서 나오는 것이  
결국엔 setState이기 때문이다.  
새로운 globalState가 전달되면 리액트가  
여기서 커스텀 훅을 사용하는 컴포넌트를 재렌더링한다.

<br><br>
우리는 이제 추상적 dispatch 함수도 있고  
listeners도 등록할 수 있다  
이것이 우리 커스텀 훅인데,
이제 이 커스텀 훅도 무언가를 반환해야 한다.
이 무언가는 두개의 요소를 가지는 배열이다

<br><br>

```jsx
return [globalState, dispatch];
```

<br><br>
바로 globalState와 dispatch다.  
이것은 바로 내장된 useReducer 함수가 항상 반환하던 것이다

<br><br>

```jsx
import { useState, useEffect, useReducer } from "react";
```

<br><br>

useReducer 함수는 항상 state와 dispatch 함수를 반환한다.  
단지 전역의 컴포넌트에 대한 state를 관리하는데 적합하지 않을 뿐이다.  
우리만의 버전을 구축해야하는 이유다.  
여기에서는 globalState와 dispatch를 반환하도록 한다.

<br><br>
여기 이 한가지가 이 커스텀 훅이 export해서 내보낼 것 중 하나다.
그렇지만 다른 무언가가 필요하다
<br><br>

```jsx
export const useStore = () => {

```

<br><br>
이 커스텀 훅이 좋기는 하지만 여전히 너무 포괄적이다.  
예를 들어 지금 이 action들을 바꿀 수 있는  
방법이 없다. 호출 할 수만 있고 설정할 수는 없다.

<br><br>

# initStore 함수 내보내기

<br><br>
<br><br>
따라서 이 파일에서 내보낼 또 다른 항목이 있다.  
바로 initStore 함수다.

<br><br>

```jsx
export const initStore = () => {};
```

<br><br>
export해서 내보내줄 이 함수는 action을 취한다.  
이 userAction은 개발자(우리)에 의해 정의된다.  
그리고 초기 state인 initialState도 취할 수 있다  
지금의 globalState는 항상 비어있는 state이기 때문이다.  
그렇지만 우리처럼 초기 state가 있을 수 있다

<br><br>

```jsx
export const initStore = (userActions, initialState) => {};
```

<br><br>
사실 우리는 초기 state로 예를 들면 상품이 있다.  
이 initalState가 null이 아니라면 현재 globalState로 복사한다.  
왜냐하면 유념할 것이 그 파일을 공유할 것이기 때문이다.  
그 state를 공유하기 때문이다  
<br><br>

```jsx
export const initStore = (userActions, initialState) => {
  if (initialState) {
    globalState = { ...globalState };
  }
};
```

<br><br>
리덕스에서처럼, 하나의 전역적 store를 구성하는  
여러개의 reducers들이 있어서  
리덕스에서 결합된 reducers들을 호출해야 했었다.  
하나의 큰 store에 대한 state 객체를 생성하기 위해서.

<br><br>
여기서 하는 것도 똑같이 현재 가지는 globalState를  
state에 대한 이 구체적인 인스턴스화에서의 초기 initialState와 결합해준다  
그렇게 해서 이 전역적 store로 많은 것을 관리할 수 있게 하는 것이다.  
그래서 여기에서 initialState를 병합해주고

<br><br>

```jsx
export const initStore = (userActions, initialState) => {
  if (initialState) {
    globalState = { ...globalState, ...initialState };
  }
};
```

<br><br>
actions가 현재의 actions이도록 해주고 userActions를 병합해준다.

<br><br>

```jsx
export const initStore = (userActions, initialState) => {
  if (initialState) {
    globalState = { ...globalState, ...initialState };
  }
  actions = { ...actions, ...userActions };
};
```

<br><br>
그럼 이제 globalState도 됐고  
userActions도 모두 여기에 등록되어 있다  
이러한 store 설정을 기반으로  
이제 구체적인 products store를  
생성할 수 있는 곳이 필요하다

<br><br>
