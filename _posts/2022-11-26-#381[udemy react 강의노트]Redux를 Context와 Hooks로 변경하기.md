---
title: "#381[udemy react 강의노트]Redux를 Context와 Hooks로 변경하기 "
excerpt: "React animation"
published: true
categories:
  - Blog
tags:
  - [React, transitiongroup]

toc: true
toc_sticky: false

date: 2022-11-26
last_modified_at: 2022-11-26
---

<br><br>

# Redux의 대안 1 : ContextAPI

<br><br>
이를 위해서 이 프로젝트에 새로운 폴더 context를 추가해준다  
그 하위에 product-context.js로 이름지은 파일을 추가한다.
<br><br>
파일안에 React를 import해주고,  
ProductsContext를 만들어준다.  
그래서 Context를 만들어준다.  
이것으로 객체를 관리하는데
여기에 상품 속성으로 한다고 하면
상품에 대한 배열을 담는다.

<br><br>

```
import React from 'react';

const ProductsContext = React.createContext({products: [

]})
```

<br><br>
전체 컨텍스트의 값이 하나의 배열이다라고 할 수도 있지만,  
다른 필드값이 있을 경우에는 여기에 객체가 있을 것이다.  
초기의 products 배열은 stroe> reducers > products.js 여기에 있다.

<br><br>

```jsx
const initialState = {
  products: [
    {
      id: "p1",
      title: "Red Scarf",
      description: "A pretty red scarf.",
      isFavorite: false,
    },
    {
      id: "p2",
      title: "Blue T-Shirt",
      description: "A pretty blue t-shirt.",
      isFavorite: false,
    },
    {
      id: "p3",
      title: "Green Trousers",
      description: "A pair of lightly green trousers.",
      isFavorite: false,
    },
    {
      id: "p4",
      title: "Orange Hat",
      description: "Street style! An orange hat.",
      isFavorite: false,
    },
  ],
};
```

<br><br>
이것을 복사해 products-context.js 파일로 옮겨 products 배열을 대체해준다.

<br><br>

```jsx
const ProductsContext = React.createContext({
  products: [
    {
      id: "p1",
      title: "Red Scarf",
      description: "A pretty red scarf.",
      isFavorite: false,
    },
    {
      id: "p2",
      title: "Blue T-Shirt",
      description: "A pretty blue t-shirt.",
      isFavorite: false,
    },
    {
      id: "p3",
      title: "Green Trousers",
      description: "A pair of lightly green trousers.",
      isFavorite: false,
    },
    {
      id: "p4",
      title: "Orange Hat",
      description: "Street style! An orange hat.",
      isFavorite: false,
    },
  ],
});
```

<br><br>
이렇게 해서 리액트의 컨텍스트 객체를 생성했다  
이 컨텍스트를 export해서 내보내주고

<br><br>

```jsx
export const ProductsContext = React.createContext({

```

<br><br>
그리고 컴포넌트도 하나 default로 내보내준다.  
즉 지금 일반적인 함수형 컴포넌트를 하나 내보낸다.
그리고 여기에서 `<ProductsContext.Provider>` 컴포넌트를  
사용해, props.children을 추가해준다.  
그러면 알다시피 모든곳에 컨텍스트를 제공하는데

<br><br>

```jsx
export default (props) => {
  return <ProductsContext.Provider>{props.children}</ProductsContext.Provider>;
};
```

<br><br>
중요한것은 context의 value인데, 여기 컴포넌트 안에서 관리하는 값이어야 한다.  
따라서 그 값은 바뀔 수 있어야하기 때문에  
리액트에 반영되어 있는 useState훅을 사용해준다.

<br><br>

```jsx
import React, { useState } from "react";
```

<br><br>
그리고 아까의 컴포넌트 안에서 state를 관리해준다.

<br><br>

```jsx
export default (props) => {
  const [] = useState();
  return <ProductsContext.Provider>{props.children}</ProductsContext.Provider>;
};
```

<br><br>
여기서 관리하는 state는 결국 위의 products 배열이 된다  
더 나은 자동완성과 ide 지원이 될 수 있도록  
이 배열 부분을 제거하여 단순히 빈 배열로 초기화해준다.  
이제 products는 여기 useState에서 관리할거기 때문에 괜찮다.

<br><br>

```jsx
export const ProductsContext = React.createContext({
  products: [],
});
```

<br><br>
그리고 useState에 배열을 넣어주고,  
useState에서 productsList, setProductsList를 추출해준다.  
<br><br>

```jsx
export default (props) => {
  const [productsList, setProductsList] = useState([
    {
      id: "p1",
      title: "Red Scarf",
      description: "A pretty red scarf.",
      isFavorite: false,
    },
    {
      id: "p2",
      title: "Blue T-Shirt",
      description: "A pretty blue t-shirt.",
      isFavorite: false,
    },
    {
      id: "p3",
      title: "Green Trousers",
      description: "A pair of lightly green trousers.",
      isFavorite: false,
    },
    {
      id: "p4",
      title: "Orange Hat",
      description: "Street style! An orange hat.",
      isFavorite: false,
    },
  ]);
  return <ProductsContext.Provider>{props.children}</ProductsContext.Provider>;
};
```

<br><br>
Provider에 전달하는 value는 products 키를 가지는 객체다  
왜냐하면 이것이 컨텍스트에 있길 원하기 때문이다.  
그리고 이 products 키에 대한 값은 productsList가 된다.

<br><br>

```jsx
return (
  <ProductsContext.Provider value={{ products: productsList }}>
    {props.children}
  </ProductsContext.Provider>
);
```

<br><br>
여기 이 state를 업데이트 할 때마다 결과적으로 이 컴포넌트는 재구성된다.  
그럴때마다 Provider가 새로운 값을 가지게 되고  
그러면 Provider를 따르는 모든 자식들도 그 새로운 값을 가지게 된다.  
<br><br>
이 Provider를 여기 index.js에서 사용할 수 있다  
전에는 여기에 react-redux를 제공해 주었다.  
그러나 그렇게 하지 않고 여기 두개의 import 문은 없앤다.

<br><br>
index.js에서 삭제
<br><br>

```jsx
import { Provider } from "react-redux";
import { combineReducers, createStore } from "redux";
```

<br><br>
그 대신 ProductsProvider를 import 해준다.
<br><br>

```jsx
import ProductsProvider from "./context/product-context";
```

<br><br>
아래의 내용도 필요없으므로 지워준다.
<br><br>
index.js에서 삭제
<br><br>

```jsx
const rootReducer = combineReducers({
  shop: productReducer,
});

const store = createStore(rootReducer);
```

<br><br>

```jsx
ReactDOM.render(
  <ProductsProvider>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </ProductsProvider>,
  document.getElementById("root")
);
```

<br><br>
Products를 위와같이 ProductsProvider로 수정해주고, store은 삭제해준다.  
store폴더는 더이상 필요없으므로 삭제해줘도 된다.  
<br><br>
그래서 여기서 지금 컨텍스트를 제공하고 있고  
이 컴포넌트 트리 안의 아무 곳에서 즉 여기 아무 자식 컴포넌트 안에서  
아니면 이 자식 컴포넌트의 자식 컴포넌트 안에서  
컨텍스트를 구독할 수 있다.  
products에서 우리가 관심이 있는 한 곳은 이 Products.js 파일이다  
여기서 ProductsContext를 import 해준다.  
우리는 이것을 useContext와 함께 사용할 수 있다.
<br><br>
src > containers > Products.js
<br><br>

```jsx
import React, { useContext } from "react";

import { ProductsContext } from "../context/product-context";
```

<br><br>
react-redux에서 import한 것은 삭제한다.  
<br><br>
삭제할 것
<br><br>

```jsx
import { useSelector } from "react-redux";
...
  const productList = useSelector((state) => state.shop.products);

```

<br><br>
이제 useContext를 사용해서 Products를 가져올 수 있다.
useContext에 ProductsContext를 전달해서 컨텍스트에 접근할 수 있도록 해준다.  
그런 다음 이것을 productList에 할당해준다.  
useContext다음에는 우리 컨텍스트에 존재하는 products에 접근한다.  
.products는 리스트에 무언가가 변경될 때마다 재생성된다.

<br><br>

```jsx
const productList = useContext(ProductsContext).products;
```

<br><br>
이제 여기에 productList도 있으니 이 productList를  
여기서 사용해서 products를 렌더링할 수 있다

<br><br>

```jsx
    <ul className="products-list">
      {productList.map((prod) => (
```

<br><br>
이제 components > Products> ProductItem.js로 이동한다.  
여기서 useDispatch를 사용했는데, import문을 제거한다.  
그리고 dispatch를 사용한 부분도 지워서(주석 처리한 부분)
react-redux 속성을 사용하는데 store를  
제공하지 않아서 생기는 오류가 뜨지 않도록 한다.

<br><br>

```jsx
const ProductItem = props => {
  const dispatch = useDispatch();

  const toggleFavHandler = () => {
    // dispatch(toggleFav(props.id));
  };

```

<br><br>
그렇게 해주고, 모든 파일을 저장한 뒤  
이 페이지를 새로고침 하면 모든 상품을 렌더링 하게 된다.  
다시 말하지만 좋아요 표시는 지금 작동하진 않는다.  
하지만 컨텍스트를 사용해서 데이터를  
다른 컴포넌트에 배포할 수 있다

<br><br>
왜냐하면 우리는 products를 products-context.js 파일에서 관리하고  
products에 대한 접근을 containers 폴더 안에 있는  
Products.js 파일에서 관리하기 때문이다

<br><br>
<br><br>
