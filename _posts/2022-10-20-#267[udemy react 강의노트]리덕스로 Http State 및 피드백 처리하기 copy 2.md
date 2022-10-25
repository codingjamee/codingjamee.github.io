---
title: "#267[udemy react 강의노트]리덕스로 Http State 및 피드백 처리하기"
excerpt: "리덕스로 Http State 및 피드백 처리하기 "
published: true
categories:
  - Blog
tags:
  - [Blog, redux, async, http, request]

toc: true
toc_sticky: false

date: 2022-10-20
last_modified_at: 2022-10-20
---

# 리덕스로 Http State 및 피드백 처리하기

<br><br>

> 응답이나 잠재적 오류를 처리하기 위한 방법을 찾아본다.

<br><br>
그러기 위해 Notification 파일을 추가하여 작성한다.

<br><br>

```jsx
import classes from "./Notification.module.css";

const Notification = (props) => {
  let specialClasses = "";

  if (props.status === "error") {
    specialClasses = classes.error;
  }
  if (props.status === "success") {
    specialClasses = classes.success;
  }

  const cssClasses = `${classes.notification} ${specialClasses}`;

  return (
    <section className={cssClasses}>
      <h2>{props.title}</h2>
      <p>{props.message}</p>
    </section>
  );
};

export default Notification;
```

<br><br>
위의 컴포넌트는 타이틀과 메시지를 표시하고  
상태 prop에 따라 다른 css클래스를 보여주는 간단한 컴포넌트다.  
이것을 사용하면 간단한 바가 맨 상단에 생기게 되며, 상단에서 요청을 보낸 뒤의 성공 혹은 오류 여부를 확인할수 있다.  
이 컴포넌트를 사용하기 위해서는 App파일에서 fetch함수 다음에 then을 작성하든지, async await를 작성하든지 둘중의 하나를 하면 되는데,  
지금 App컴포넌트에서는 useEffect를 사용하고 있기 때문에 비동기async함수를 사용하면 안된다. 그러므로 useEffect안에서 새로운 함수를 작성하여 사용하도록 한다.

```jsx
function App() {
  const showCart = useSelector((state) => state.ui.cartIsVisible);
  const cart = useSelector((state) => state.cart);

  useEffect(() => {
    const sendCartData = async () => {
      const response = await fetch("https://react-http-.com/cart.json", {
        method: "PUT",
        body: JSON.stringify(cart),
      });

      if (!reponse.ok) {
        throw new Error("Sending cart data failed");
      }

      const responseData = await response.json();
    };
  }, [cart]);
}
```

<br><br>
그리고 선택적으로 Notification 컴포넌트를 보여주기 위해 useState를 이용할 수 있다.  
그러나 이미 uiSlice가 있으므로 uiSlice를 사용해보자.

<br><br>
그러려면 uiSlice를 바꾸어  
상태에 따라서 payload를 통해 status와 title, message를 넣어 렌더링 할 수 있게 해준다.

```jsx
import { createSlice } from "@reduxjs/toolkit";

const initialUiState = {
  showCart: false,
  notification: null,
};

const uiSlice = createSlice({
  name: "ui",
  initialState: initialUiState,
  reducers: {
    toggle(state) {
      state.showCart = !state.showCart;
    },
    showNotification(state, action) {
      state.notification = {
        status: action.payload.status,
        title: action.payload.title,
        message: action.payload.message,
      };
    },
  },
});

export const uiSliceActions = uiSlice.actions;
export default uiSlice;
```

<br><br>
이제 이것을 useDispatch로 가져와서 액션객체를 만들어 전송한다.
<br><br>
http 리퀘스트를 전송하기 시작할 때 dispatch를 해주고,  
성공하고 나서 dispatch를 해주어 상태에 따라 달라지는 문구를 작성한다.
<br><br>

```jsx
function App() {
...
  useEffect(() => {
    const sendCartData = async () => {
      dispatch(
        uiSliceActions.showNotification({
          status: "pending",
          title: "Sending...",
          message: "Sending cart data!",
        })
      );
      const response = await fetch("https://react-http-.com/cart.json", {
        method: "PUT",
        body: JSON.stringify(cart),
      });

      if (!reponse.ok) {
        throw new Error("Sending cart data failed");
      }

      dispatch(
        uiSliceActions.showNotification({
          status: "success",
          title: "Success!",
          message: "Sent cart data successfully!",
        })
      );
    };

  }, [cart]);
}
```

<br><br>
<br><br>
오류가 있을 때도 dispatch하고 싶은데 어떻게 해야할까?

```jsx
function App() {
...
  useEffect(() => {
    const sendCartData = async () => {
      (...생략)
    }

  sendCartData().catch(error => {
      dispatch(
        uiSliceActions.showNotification({
          status: "error",
          title: "Error!",
          message: "Sending cart data failed!",
        })
      );
    });
  })
}
```

<br><br>
에러는 어디서든 날 수 있으므로 sendCartData 함수 안에서 사용하면  
모든 에러를 캐치하지 못할 수도 있다.  
그 대신, 위와 같이 sendCartData를 실행한 뒤 catch로 작성하면  
모든 에러를 캐치할 수 있게 된다.  
그 이유는 sendCartData 함수는 async함수이기 때문에  
promise를 반환하므로 catch함수로 모든 오류를 포착할 수 있게 되는 것이다.
<br><br>
<br><br>
마지막에 추가해 주어야 하는 것이 있는데, 바로  
useEffect의 dependencies다.  
현재는 cart만 되어있는데, dispatch도 추가해주어야 한다.  
리액트 리덕스가 이 useDispatch를 통해 만들어진  
dispatch 함수가 변경되지 않는 것을 보장해 준다.  
그러므로 dipendencies에 추가하더라도  
dispatch는 useEffect를 재실행하게 해주지 않을 것이다.  
cart 변경시에만 useEffect가 재실행 된다.

이제 상태에 따라 다른 status나 메시지를 보여줄  
action함수를 작성하게 되었다.  
그럼 이제 이것을 보여주어야 할 텐데,  
useSelector로 ui notification을 가져와서 state를 활용해 본다.

```jsx
const notification = useSelector(state => {
  state.ui.notification;
});

...
 return (
    <>
      {notification && (
        <Notification
          status={notification.status}
          title={notification.title}
          message={notification.message}
        />
      )}
      <Layout>
        {showCart && <Cart />}
        <Products />
      </Layout>
    </>
  );
```

<br><br>
그런데 처음부터 cart데이터를 전송하는 http 요청을 해버린다.  
어떻게 해야할까?

```jsx

let isInitial = true;

...
App() {
  //...생략
  if(isInitial) {
    isInitial = false;
    return;
  }

  sendData().catch()
}
```

이렇게 작성해주면 처음에만 isInitial이 true이기 때문에  
처음엔 sendCartData함수를 실행하지 않게 된다.  
그러면 빈 장바구니를 전송하는 요청을 하지 않게 될 수 있다.
<br><br>

# 모든 sideEffect를 컴포넌트에 넣는 대안

action creator를 사용하는 것인데,  
action creator는 이미 우리가 사용하고 있다.  
예를들자면 이런 것이다.

```jsx
useEffect(() => {
  const sendCartData = async () => {
    dispatch(
      uiActions.showNotification({
        status: "pending",
        title: "sending...",
        message: "Sending cart data!",
      })
    );
  };
});
```

uiActions.showNotification 이것이 actioncreator이다.  
리덕스 툴킷을 통해 사용했으며,  
디스패치 할 action 객체를 생성하는 일을 한다.  
이것을 통해 action creator를 만들고  
thunks를 만들 수 있다.  
<br><br>

## Thunk란 무엇인가?

<br><br>
Thunk란 액션을 무언가 끝날 때까지 작업을 지연시키는 함수다.  
우리는 액션 객체를 즉시 반환하지 않는 액션 크리에이터를 작성하기 위해  
Thunk로 액션 크리에이터를 작성할 수 있다.  
다른 액션을 반환하는 함수를 반환한다.  
실제 액션 객체를 디스패치 하기 전에 다른 코드를 실행할 수 있다.
<br><br>
컴포넌트에서 장바구니 데이터를 보내기 위한 해당 코드를 옮김으로써  
이러한 액션 크리에이터 썽크 안의 커스텀 액션 크리에이터에 대해 자세히 살펴본다.
<br><br>
<br>

그것을 위해서 cartSlice 파일에서 액션 크리에이터를 작성해야 한다.  
cart를 관리하는 파일이기 때문이다.  
슬라이스 객체 밖에서 sendCartData이름으로 작성한다.
<br><br>
<br><br>

<br><br>

```jsx
const sendCartData = (cartData) => {
  return {type: '', payload: ... }
```

action object를 리턴하는 액션 크리에이터 함수를 직접 작성한다.  
리덕스 툴킷은 이러한 액션 크리에이터를 자동으로 생성해 주기 때문에  
직접 수행한 적은 없었다.  
리듀서 객체의 모든 메서드는 리듀서 함수 이름을 사용하여 호출되는  
액션 크리에이터를 받는다.
그러므로 위와같이 작성할 필요는 없다.
<br><br>

```jsx
const sendCartData = (cartData) => {
  return (dispatch) => {
    dispatch();
  };
};
```

위와같이 그 함수 안에 다른 함수를 반환하는 코드를 작성한다.
실제로 우리가 수행하고자 하는 액션을 디스패치 하는 함수다.
<br><br>

자바스크립트는 이 코드를 작성할 수 있다.  
이 함수는 dispatch를 인자로 받는다. 어디서 오는지는 조금 뒤에 알아본다.
<br><br>
함수 안에서는 수행하려는 실제 작업을 디스패치 할 수 있다.  
이 함수는 리듀서 함수에서 하지 않고 독립된 자바스크립트 함수로 작성한다.  
<br><br>

디스패치를 호출하기 전에 비동기 코드와 사이드이펙트도 수행할 수 있다.  
그리고 요청을 보내는 것과 응답을 받는 코드를 작성한다.
sendCartData함수는 비동기 코드로 작성할 수 있다.
이 디스패치 함수를 App.js파일에서 실행한다.
sendCartData를 export해서 import한 것을 인자로 cart를 넣어  
dispatch해준다.

<br><br>
<br><br>

cartslice.js파일

```jsx
export const sendCartData = (cart) => {
  return (dispatch) => {
    dispatch(
      uiSliceActions.showNotification({
        status: "pending",
        title: "Sending...",
        message: "Sending cart data!",
      })
    );
  };
};
```

<br><br>
그리고 요청을 보내는 코드를 잘라내어 그 밑에 붙여넣는다.  
sendCartData가 리턴하는 함수는 async 함수로 바꿀 수 있다.

```jsx
const sendCartData = cart => {
  return async dispatch => {
```

<br><br>
그리고 API를 가져오는 방법 때문에 추가적인 중첩이 필요하다.  
try catch문을 사용해 sendRequest 함수를 실행하고 성공시 할 액션객체를 보내주고,  
에러가 있을 때 캐치해 보낼 액션 객체를 작성한다.
<br><br>

코드는 아래와 같다.  
<br><br>
cart-slice.js 파일

```jsx
import { createSlice } from "@reduxjs/toolkit";
import { uiSliceActions } from "./ui-slice";

const initialItemState = {
  items: [],
  totalQuantity: 0,
  totalAmount: 0,
};

const cartSlice = createSlice({
  name: "cart",
  initialState: initialItemState,
  reducers: {
    add(state, action) {
      const newItem = action.payload;
      const existingItem = state.items.find((item) => item.id === newItem.id);
      state.totalQuantity++;
      if (!existingItem) {
        state.items.push({
          id: newItem.id,
          price: newItem.price,
          quantity: 1,
          totalPrice: newItem.price,
          name: newItem.title,
        });
      } else {
        existingItem.quantity++;
        existingItem.totalPrice = existingItem.totalPrice + newItem.price;
      }
    },
    remove(state, action) {
      const removeItemId = action.payload;
      const existingItem = state.items.find((item) => item.id === removeItemId);
      state.totalQuantity--;
      if (existingItem.quantity === 1) {
        state.items = state.items.filter((item) => item.id !== removeItemId);
      } else {
        existingItem.quantity--;
        existingItem.totalPrice = existingItem.totalPrice - existingItem.price;
      }
    },
  },
});

export const sendCartData = (cart) => {
  return async (dispatch) => {
    dispatch(
      uiSliceActions.showNotification({
        status: "pending",
        title: "Sending...",
        message: "Sending cart data!",
      })
    );

    const sendRequest = async () => {
      const response = await fetch(
        "https://react-http-55f5b-default-rtdb.firebaseio.com/cart.json",
        {
          method: "PUT",
          body: JSON.stringify(cart),
        }
      );

      if (!response.ok) {
        throw new Error("Something goes wrong!");
      }
    };
    try {
      await sendRequest();
      dispatch(
        uiSliceActions.showNotification({
          status: "success",
          title: "Success!",
          message: "Sent cart data successfully!",
        })
      );
    } catch (error) {
      dispatch(
        uiSliceActions.showNotification({
          status: "error",
          title: "Error!",
          message: "Sending cart data fatiled!",
        })
      );
    }
  };
};

export const cartActions = cartSlice.actions;
export default cartSlice;
```

<br><br>
<br><br>

우리가 하고 있는 것은 sendCartData함수를 작성해 cart데이터를 보내는 일이다.  
이 데이터는 다른 작업을 수행하지 않고 다른 함수인 비동기 함수를 즉시 반환한다.
그 함수에서 notification 공지 액션을 디스패치 한다.  
그리고 또다른 즉석에서 만든 중첩함수를 작성하는데 sendRequest함수다.
비동기식을 포함하고 있어 그 안에서 request를 보낸다.  
단순히 오류를 처리하기 위해 try catch 대신 sendRequest함수를 불러온다.  
에러가 없으면 성공알림을 위해 디스패치 하고, 에러가 나면 오류 알림을 디스패치 해준다.  
이 함수는 어디서 호출할까?
<br><br>
<br><br>
sendCartData를 export해서 App.js파일에서 import해준다.

App.js파일
<br><br>

```jsx
import { sendCartData } from "./store/cart-slice";
...

useEffect(() => {
  if (isInitial) {
    isInitial = false;
    return;
  }

  dispatch(sendCartData(cart));
}, [cart, dispatch]);
```

<br><br>
우리가 디스패치 한 것은 항상 액션 크리에이터였지만,
지금은 type등의 액션 객체를 반환하는 함수기 때문에 약간 이상해보일 수도 있다.  
<br><br>
리덕스 툴킷을 사용할 때 리덕스의 가장 큰 장점은  
이에대한 준비가 되어있다는 것이다.  
type 프로퍼티가 있는 작업 객체만 허용하는 것이 아니라,  
함수를 반환하는 액션 크리에이터도 허용한다.  
실제로 액션 객체가 아닌 함수인 액션을 디스패치 하는 것으로 확인되면  
해당 함수를 자동으로 실행한다.  
리덕스는 그 함수를 실행할 것이다.  
그리고 sendCartData함수에서 리턴하는 함수의 인자로 받는 dispatch는
<br><br>

```jsx
export const sendCartData = cart => {
  return async dispatch => {
```

<br><br>
자동으로 디스패치 인수를 줄 것이다.  
실행된 함수에서 다시 디스패치 할 수 있게 된다.  
왜냐하면 sideEffect를 일으킬 수 있는 액션 크리에이터가 필요하다는  
공통 패턴이 있기 때문이다.  
그런다음 다른 작업을 디스패치 할 수 있으며 이는 결국 sideEffect 흐름의 일부로  
또는 우리가 취해야 하는 단계 흐름의 일부로 리듀서에 도달하게 된다.
sendCartData에서 하는 것이 바로 그것이다.  
우리는 함수를 반환하는 함수를 액션으로도 사용할 수 있다.
리덕스 툴킷을 사용한다면 리덕스에 이미 내장되어 있다.
<br><br>
<br><br>

# 애플리케이션이 로드될 때 장바구니 가져오기

<br><br>

애플리케이션 액션 크리에이터 빌드하기  
<br><br>
지금까지는 데이터만 보내고 있고,  
애플리케이션이 로드될 때 fetch를 하지 않았다.  
두 번째 cartSlice를 추가하자.  
cart-slice에서 작성할 수 있지만, 다른 파일cart-actions.js를 만들어 파일이 너무 방대해지는 것을 막는다.  
그리고 sendCartData를 cart-actions에 가져다 넣는다.
또다른 함수 fetchCartData를 작성한다.
<br><br>

매개변수로 전달 된 dispatch를 반환한다음  
그 안에서 비동기 함수인 fetchData를 만든다.  
try, catch 문을 작성하고 싶기 때문에 별도로 함수를 만드는 것이다.
<br><br>
get request를 작성해야 하므로 두 번째 인자로 객체를 추가할 필요가 없다.  
get request가 기본값이기 때문이다.

<br><br>

```jsx
export const fetchCartData = () => {
  return (dispatch) => {
    const fetchData = async () => {
      const response = await fetch(
        "https://react-http-.firebaseio.com/cart.json"
      );

      if (!response.ok) {
        throw new Error("Could not fetch cart data!");
      }

      const data = await response.json();
    };
  };
};
```

<br><br>
error가 발생하면 그것을 포착해 메시지를 전달해주고,
error없이 정상적으로 데이터를 가져 와서 data에 전달되었으면  
return 해준다.  
fetchData라는 별도의 중첩된 함수이기 때문에 return 해 줘야 한다.

<br><br>
try 문에 fetchData를 실행하고 catch에는 error가 있을 시 보여줄 문구를 작성한  
notification 액션 객체를 넣어준다.  
<br><br>

```jsx
export const fetchCartData = () => {
  return async (dispatch) => {
    const fetchData = async () => {
      const response = await fetch(
        "https://react-http-.firebaseio.com/cart.json"
      );

      const data = await response.json();

      return data;
    };

    try {
      const cartData = await fetchData();
    } catch (error) {
      dispatch(
        uiSliceActions.showNotification({
          status: "error",
          title: "Error!",
          message: "Fetching cart data fatiled!",
        })
      );
    }
  };
};
```

<br><br>
try문 안의 fetchData를 await로 바꾼다면  
fetchCartData를 return 하는 함수를 async로 바꿀 수 있다.  
리덕스는 반환된 함수가 비동기 인것을 지원한다.  
try 문에 가져온 데이터를 cartData 변수에 할당한다.  
이 데이터는 firebase의 cart에 저장된다.  
cart에는 items 키와 총 수량 키가 있다.  
그것은 cart-slice에서 initialItemState로 가거나 add 등으로 간 것이다.  
이미 firebase데이터가 cart-slice에서 변환되므로 따로 데이터를 변환할 필요는 없다.  
<br><br>
데이터를 보내기 위해 post를 사용했기 때문에  
firebase에서 가져올 때 객체로 판명된 데이터 목록을 생성한다.  
여기서 put을 사용하여 데이터 스냅샷을 firebase로 보내고  
firebase는 그대로 가져와서 변경하지 않고, 있는 그대로 저장한다.  
<br><br>

```jsx
const sendRequest = async () => {
      const response = await fetch(
        "https://react-http-55f5b-default-rtdb.firebaseio.com/cart.json",
        {
          method: "PUT",
          body: JSON.stringify(cart),
        }
      );

```

<br><br>

즉 fetch할 때 구조도 같아야 하므로  
이 장바구니 데이터의 형식은 올바르게 지정된 것이다.  
<br><br>

```jsx
export const fetchCartData = () => {
  return async (dispatch) => {
    const fetchData = async () => {
      const response = await fetch(
        "https://react-http-55f5b-default-rtdb.firebaseio.com/cart.json"
      );

      const data = await response.json();

      return data;
    };

    try {
      const cartData = await fetchData();
    } catch (error) {
      dispatch(
        uiSliceActions.showNotification({
          status: "error",
          title: "Error!",
          message: "Fetching cart data fatiled!",
        })
      );
    }
  };
};
```

<br><br>
이전에 잠깐 언급했던 replaceCart 리듀서를 사용하여 firebase에서  
로드하는 장바구니로 프론트엔드 장바구니를 교체할 수 있다.
여기는 firebase에서 가져오는 데이터 구조와 일치하는  
총수량과 아이템이 들어있다.

```jsx
const cartSlice = createSlice({
  name: "cart",
  initialState: initialItemState,
  reducers: {
    replaceCart(state, action) {
      state.totalQuantity = action.payload.totalQuantity;
      state.items = action.payload.items;
    }
```

<br><br>

```jsx
try {
  const cartData = await fetchData();
  dispatch(cartActions.replaceCart(cartData))
}
```

<br><br>
이렇게 replaceCart로 가져온 cartData를 전달하면  
페이로드로 전달되어 state가 변경되게 된다.  
이제 이 함수를 어딘가에서 실행하여 firebase에서 cart 데이터를 가져오면 된다.
<br><br>
App.js에서 가져오면 되는데, useEffect가 이미 있으므로  
함께 사용해도 좋지만, 분리하는 것이 좋다.  
cart 데이터를 가져오는 것은 처음에만 실행되면 되기 때문이다.
<br><br>

```jsx
useEffect(() => {
  dispatch(fetchCartData());
}, [dispatch]);
```

<br><br>

그러면 새로고침을 하더라도 cart에 들어있는 item들이 그대로  
있는 것을 볼 수 있다.
<br><br>
<br><br>

<img src="../Capture.jpg">
<br><br>
<br><br>

그런데 새로고침 할 때 장바구니에 저장된 데이터를 가져오고는,  
다시 firebase로 보내는 것처럼 보인다. 왜그럴까?  
app.js에는 장바구니가 변경될 때마다 cart를 보내는  
useEffect가 있기 때문이다.  
문제는 장바구니 데이터를 가져올 때다.  
가져오기가 완료되면 장바구니를 replace하는 문제다.  
그래서 리덕스 내부의 장바구니가 달라지게 되는 것이다.  
cart가 변경되었으니 다른 effect도 시도해보고 cartData도 다시 전송해보자.  
<br><br>
<br><br>

# 약간의 오류 수정하기

cartSlice에서 initialState에 changed 프로퍼티를 false로 넣어준다.  
replaceCart를 사용한 경우에는 changed 프로퍼티를 그대로 놓지만,  
장바구니에 항목을 추가하거나 장바구니에서 제거하는 경우  
changed를 true로 변경해준다.  
remove나 add 리듀서는 로컬 애플리케이션에서만 실행된다.  
firebase에서 데이터를 가져오고  
replaceCart에서 실행할 때 changed는 false에서 변경되지 않는다.

<br><br>

```jsx
useEffect(() => {
  if (isInitial) {
    isInitial = false;
    return;
  }

  if (cart.changed) {
    dispatch(sendCartData(cart));
  }
}, [cart, dispatch]);
```

<br><br>
위와 같이 cart.changed일 때만 dispatch하면 된다.
<br><br>
<br><br>
그런데 여기서 또 에러가 생겼다.  
장바구니를 모두 비우고 다시 cart에 아이템을 추가하면 오류가 난다.  
장바구니가 비워지고, firebase에서 장바구니 데이터를 가져왔기 때문에  
오류가 나는 것이다.
다 비우고 firebase의 데이터베이스를 보면  
더이상 item 키가 없는 것을 알 수 있다.  
<br><br>
firebase에서 로컬 장바구니를 fetch한 장바구니로 설정하면  
item이 빈 배열이 아닌, 정의되지 않게 되기 때문에  
undefined를 가져오려고 하니 에러가 난 것이다.  
그러면 replaceCart할 때 가져올 데이터들이 살짝 수정되면 된다.

<br><br>

```jsx
    try {
      const cartData = await fetchData();
      dispatch(
        cartActions.replaceCart({
          items: cartData.items || [],
          totalQuantity: cartData.totalQuantity,
        })
      );
```

<br><br>
