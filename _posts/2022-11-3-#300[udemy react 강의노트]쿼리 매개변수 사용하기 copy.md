---
title: "#300[udemy react 강의노트]댓글기능 추가하기"
excerpt: ""
published: true
categories:
  - Blog
tags:
  - [Blog, routing, react, useHistory, useLocation]

toc: true
toc_sticky: false

date: 2022-11-3
last_modified_at: 2022-11-3
---

# 댓글달기

<br>

이제 댓글을 저장하는 코드를 작성해 본다. <br>
주어진 코드는 아래와 같다.
<br><br>

NewCommentForm.js 파일

```jsx
import { useRef } from "react";

import useHttp from "../../hooks/use-http";
import { addComment } from "../../lib/api";
import classes from "./NewCommentForm.module.css";

const NewCommentForm = (props) => {
  const commentTextRef = useRef();
  const { sendRequest, status } = useHttp(addComment);

  const submitFormHandler = (event) => {
    event.preventDefault();

    // optional: Could validate here
  };

  return (
    <form className={classes.form} onSubmit={submitFormHandler}>
      <div className={classes.control} onSubmit={submitFormHandler}>
        <label htmlFor="comment">Your Comment</label>
        <textarea id="comment" rows="5" ref={commentTextRef}></textarea>
      </div>
      <div className={classes.actions}>
        <button className="btn">Add Comment</button>
      </div>
    </form>
  );
};

export default NewCommentForm;
```

<br>

Comments 컴포넌트에는 Add a Comment 버튼이 있고 코멘트를 추가할 수 있는  
기능을 여기에 집어넣으면 되는 것이다.
NewCommentForm을 렌더링해보자.

NewCommentForm은 submitFormHandler가 있다.  
여기서 코멘트 데이터를 서버로 보낸다.

<br>

1. useHttp를 import한다. <br><br>

```jsx
import useHttp from "../../hooks/use-http";
```

2. 그리고 api 파일에서 addComment 함수를 import 한다.<br><br>

```jsx
import { addComment } from "../../lib/api";
```

3. NewCommentForm 컴포넌트에서 useHttp 커스텀 훅을 호출하고  
   그 안에 addComment를 전달한다. 그리고 sendRequest 함수와 status를 받아온다.  
   그리고 submitFormHandler안에서 sendRequest를 호출한다.

```jsx
const NewCommentForm = (props) => {
  const commentTextRef = useRef();
  const { sendRequest, status } = useHttp(addComment);

  const submitFormHandler = (event) => {
    event.preventDefault();

    // optional: Could validate here
    const enteredText = commentTextRef.current.value;

    sendRequest({ text: enteredText });
  };

```

<br><br>

4. addComment함수는 requestData를 필요로 한다.  
   <br><br>

```jsx
export async function addComment(requestData) {

```

<br><br>

5. NewCommentForm의 submitHandler안에서 sendRequest 함수 안에  
    requestData객체를 전달한다.
   단순하게 하기 위해 enteredText를 가지는 텍스트 필드만 넣는다.
   enteredText는 commentTextRef.current.value에서 가져온 것으로 한다.  
   <br><br>

```jsx
const NewCommentForm = (props) => {
  const commentTextRef = useRef();
  const { sendRequest, status } = useHttp(addComment);

  const submitFormHandler = (event) => {
    event.preventDefault();

    // optional: Could validate here
    const enteredText = commentTextRef.current.value;

    sendRequest({ text: enteredText });
```

<br><br>
commentTextRef는 textarea에 연결되어있는 값이다.  
그래서 .current.value로 textarea에 입력된 값을 가져온다.  
이것이 sendRequest로 보내는 텍스트다.  
그러면 상태도 바뀐다.  
<br><br>

```jsx
  return (
    <form className={classes.form} onSubmit={submitFormHandler}>
      {status === "pending" && (
        <div className="centered">
          <LoadingSpinner />
        </div>
      )}
```

<br><br>
리퀘스트가 진행중이면 status가 pending으로 바뀌므로 그것을 이용해  
LoadingSpinner컴포넌트를 렌더링한다.
<br><br>

<hr>

리퀘스트가 끝나면 comment 컴포넌트, 즉 NewCommentForm의 부모 컴포넌트에

# 코멘트 추가가 끝났다는걸 알려보자.

<hr>

<br><br>
useEffect를 사용하면 된다.
status가 변경될 때마다 트리거 되게 deps에 status를 추가한다.  
error가 없는지 체크하기 위해 useHttp에서 error데이터도 가져오기로 한다.  
그리고 useEffect안에서 상태체크를 if로 한다.

1. status가 completed이고 error가 없을 때 함수를 하나 호출해 준다.
   프롭을 부모컴포넌트 또는 현재 컴포넌트에 새 코멘트 추가를 완료했다고 알릴 함수다.  
   따라서 코멘트를 다시 가져와야 한다.

<br><br>

```jsx
useEffect(() => {
    if(status === 'completed' && !error) {

    }
```

<br><br>

2. props에서 prop을 가져온다. 이름은 onAddedComment로 사용한다.  
   그리고 if문 안에서 onAddedComment를 호출한다. useEffect의 deps에는  
   error와 onAddedComment를 추가한다.
   <br><br>

```jsx
const NewCommentForm = (props) => {
  const commentTextRef = useRef();
  const { sendRequest, status, error } = useHttp(addComment);
  const { onAddedComment } = props;

  useEffect(() => {
    if (status === "completed" && !error) {
      onAddedComment();
    }
  }, [status, error, onAddedComment]);
```

<br><br>

3. props에서 onAddedComment함수를 가져오고, Comment추가가 완료되면 이 함수를 불러오게 된다.  
   우리가 여기서 얻은 함수를 정의하는 부모 컴포넌트에서 위의 상태가 될 때마다 무엇이든 할 수 있게 된다.

4. 그 전에 sendRequest에서 해야할 것이 하나 더 있다.  
   addComment에 quoteId를 더 필요로 한다.
   (사실 지금 코드는 그렇지 않지만 무슨일에서인지 강사의 코드에서는 아래와 같이 quoteId를 필요로 하고 있다.)

<br><br>

지금 addComment 코드
<br><br>

```jsx
export async function addComment(requestData) {
  const response = await fetch(
    `${FIREBASE_DOMAIN}/comments/${requestData.quoteId}.json`,
    {
      method: "POST",
      body: JSON.stringify(requestData.commentData),
      headers: {
        "Content-Type": "application/json",
      },
    }
  );
  const data = await response.json();

  if (!response.ok) {
    throw new Error(data.message || "Could not add comment.");
  }

  return { commentId: data.name };
}
```

<br><br>

강사의 코드
<br><br>

```jsx
export async function addComment(commentData, quoteId) {
  const response = await fetch(`${FIREBASE_DOMAIN}/comments/${quoteId}.json`, {
    method: "POST",
    body: JSON.stringify(commentData),
    headers: {
      "Content-Type": "application/json",
    },
  });
  const data = await response.json();

  if (!response.ok) {
    throw new Error(data.message || "Could not add comment.");
  }

  return { commentId: data.name };
}
```

<br><br>
(아마도 나중에 바뀐 코드가 업로드 될 것으로 보인다 그래서 일단 따라가본다)

<br><br>

# quoteId에 대한 액세스를 얻는 방법은 두 가지가 있다.

<br><br>

1. react-router-dom에서 가져온 useParams 훅을 사용하는 방법.  
   이것은 어느 컴포넌트에서도 사용할 수 있다.  
   그래서 여기 중첩된 form컴포넌트에서도 사용할 수 있다.  
   이는 활성 quoteId에 대한 액세스를 빠르고 쉽게 부여한다.  
   물론 이것은 URL에 해당 quoteId가 포함될 위치에서만  
   NewCommentForm컴포넌트를 사용할 수 있음을 의미한다.  
   URL에 해당 quoteId가 포함되지 않을 곳에는 사용할 수 없다.  
   이것이 컴포넌트에 추가하는 제약이다.  
   <br><br>

```jsx
const NewCommentForm = (props) => {
  const commentTextRef = useRef();
  const params = useParams();
```

<br><br>

2. useParams를 사용하지 않고 props를 통해 quoteId를 얻는 방법.  
    이 방법은 컴포넌트를 더 재사용 가능하고 URL과 독립적으로 만들 수 있다.  
   이것은 대안이고, 얼마나 유연하게 컴포넌트를 만들고 싶은지에 달려있다.  
   만약 컴포넌트를 완전히 유연하게 만들고 싶다면 props를 사용하는 본 방법이 맞을 것이다.  
   이제 이 방법을 사용해서 진행하자.  
   <br><br>

```jsx
const NewCommentForm = (props) => {
const commentTextRef = useRef();
const params = useParams();
const { sendRequest, status, error } = useHttp(addComment);
const { onAddedComment } = props;

useEffect(() => {
if (status === "completed" && !error) {
onAddedComment();
}
}, [status, error, onAddedComment]);
const submitFormHandler = (event) => {
event.preventDefault();

    // optional: Could validate here
    const enteredText = commentTextRef.current.value;

    sendRequest({ text: enteredText }, props.quoteId);

};

```

sendRequest에 두 번째 인수를 추가하고, props.quoteId를 값으로 넣어준다.
이제 onAddedComment와 같이 당연히 quoteId를 prop으로 얻으리라 예상할 수 있다.
<br><br>

2. -1 Comments.js파일로 가서 이 prop들을 추가하자.

<br><br>

```jsx
      {isAddingComment && <NewCommentForm quoteId={} onAddedComment={}/>}

```

<br><br>

위와같이 Comments파일에서 NewCommentForm jsx코드에 quoteId와 onAddedComment프롭을 추가해준다.  
onAddedComment프롭은 함수인데, 이 함수는 코멘트가 추가될 때 실행될 것이다.  
quoteId 프롭은 quoteId를 전달해야 한다.

<br><br>
먼저 quoteId부터 출발해본다.  
useParams를 react-router-dom에서 import해오고,  
Comments 컴포넌트 안에서 useParams를 사용한다.  
이것은 URL에 파라미터가 포함된 위치에서만 이 컴포넌트를 사용할 것을 알기 때문에  
사용해도 괜찮다.  
그리고 jsx코드에 params.quoteId를 추가해준다.
<br><br>

```jsx
const params = useParams();
...
  {isAddingComment && <NewCommentForm quoteId={params.quoteId} onAddedComment={}/>}
```

<br><br>
그리고 addedCommentHandler 함수를 만들어준다.  
jsx코드에는 onAddedComment의 포인터로 addedCommentHandler를 작성해준다.  
addedCommentHandler에는 코멘트를 가져올 로직을 추가해야 한다.  
주어진 quoteId에 대한 코멘트를 가져오는 로직이다.
<br><br>
특정 quoteId에 대해 코멘트를 가져오기 위해 useHttp훅과 api파일의 함수를 사용한다.
그리고 useHttp훅에 getAllComments함수를 포인트해주고 데이터조각들을 추출한다.  
데이터 조각은 useHttp의 반환값에서 추출한 것들이다.

<br><br>

```jsx
import useHttp from "../../hooks/use-http";
import { getAllComments } from "../../lib/api";
...
const {
    sendRequest,
    status,
    data: loadedComments,
    error,
  } = useHttp(getAllComments);


```

<br><br>
이제 이 컴포넌트가 로드되거나 추출될 때마다 코멘트를 가져오도록 리퀘스트를 보낸다.  
리퀘스트 함수는 코멘트를 로드하는 요청을 보내야 한다.  
<br><br>

```jsx
const Comments = () => {
  const [isAddingComment, setIsAddingComment] = useState(false);
  const params = useParams();

  const {
    sendRequest,
    status,
    data: loadedComments,
    error,
  } = useHttp(getAllComments);

  useEffect(() => {
    sendRequest();
  }, []);
```

<br><br>
이 getAllComments 함수는 quoteId를 필요로 한다.  
 어떤 quoteId 코멘트를 가져와야 하는지 알아야 한다.  
 따라서 그 데이터를 sendRequest함수에 전달해보자.  
 quoteId를 params에서 얻어온다.  
 useEffect에 sendRequest안에 params.quoteId를 전달해준다.  
 deps에 params를 추가해준다.

<br><br>

```jsx
const Comments = () => {
  const [isAddingComment, setIsAddingComment] = useState(false);
  const params = useParams();

  const {
    sendRequest,
    status,
    data: loadedComments,
    error,
  } = useHttp(getAllComments);

  useEffect(() => {
    sendRequest(params.quoteId);
  }, [params]);

```

<br><br>
그러나 이렇게 하면 불필요한 재실행이 되기 때문에 다른방법을 사용하자.
객체 디스트럭처링을 통해 quoteId를 얻어오고 deps에 quoteId를 추가하자.  
sendRequest도 추가해준다. 그것도 effect에서 사용하고 있는 의존성이기 때문이다.
<br><br>

```jsx
const Comments = () => {
  const [isAddingComment, setIsAddingComment] = useState(false);
  const params = useParams();

  const { quoteId } = params;

  const {
    sendRequest,
    status,
    data: loadedComments,
    error,
  } = useHttp(getAllComments);

  useEffect(() => {
    sendRequest(quoteId);
  }, [quoteId, sendRequest ]);

```

<br><br>
가져온 코멘트와 status를 처리하기 위해 현재 상태를 체크한다.  
현재 상태가 pending이라면 로딩 스피너를 보여줄 것인데, if문에서 return하진 않을 것이다.  
그 대신 로딩스피너를 아래에 반환되어 있는 전체 jsx컨텐츠의 일부로 표시하자.  
이 폼을 제거하고 싶지 않거나 코멘트를 기다리고 있으므로 Add a Comment버튼을  
제거하고 싶지 않을 수 있다.  
만약 코멘트를 로딩중이라면 로딩스피너를 노출시키고,  
코멘트를 얻고 나면 코멘트 리스트가 보이게 할 것이다.

<br><br>

```jsx
const addedCommentHandler = () => {};

let comments;
```

<br><br>
comments라는 변수를 addedCommentHandler 함수 아래부분에 추가하고  
원래 값이 없었던 comments에 값을 할당한다.  
comments에 jsx코드를 저장할 것이다.
<br><br>

```jsx
let comments;

if (status === "pending") {
  comments = (
    <div className="centered">
      <LoadingSpinner />
    </div>
  );
}
```

<br><br>
그리고 status가 pending이 아닌 completed일 때 comments를 변경하여준다.
이 때 loadedComments가 있는 것을 추가조건으로 한다. 데이터가 있을 때인 것이다.

<br><br>

```jsx
if (status === "completed" && loadedComments) {
  comments = <CommentsList />;
}
```

<br><br>
CommentsList에 comments 프롭에 loadedComments를 전달한다.
그런데 만일 loadedComments가 없거나 loadedComments.length가 0일 때는  
(그러므로 위의 조건은 loadedComments && loadedComments.length > 0 로 변경하는 것이 더 맞다. 위의 코드도 함께 수정)
No Comments were added yet! 을 렌더링한다.
<br><br>

```jsx
if (status === "completed" && loadedComments && loadedComments.length > 0) {
  comments = <CommentsList comments={loadedComments} />;
}

if (
  status === "completed" &&
  (!loadedComments || loadedComments.length === 0)
) {
  comments = <p className="centered">No Comments were added yet!</p>;
}
```

<br><br>
이제 comments 변수를 사용하여 아래에서

<br><br>

```jsx
return (
  <section className={classes.comments}>
    <h2>User Comments</h2>
    {!isAddingComment && (
      <button className="btn" onClick={startAddCommentHandler}>
        Add a Comment
      </button>
    )}
    {isAddingComment && (
      <NewCommentForm
        quoteId={params.quoteId}
        onAddedComment={addedCommentHandler}
      />
    )}
    <p>Comments...</p>
  </section>
);
```

<br><br>
밑에서 두번째 줄인 Comments jsx코드를 수정한다.

<br><br>
``jsx
return (

  <section className={classes.comments}>
    <h2>User Comments</h2>
    {!isAddingComment && (
      <button className="btn" onClick={startAddCommentHandler}>
        Add a Comment
      </button>
    )}
    {isAddingComment && (
      <NewCommentForm
        quoteId={params.quoteId}
        onAddedComment={addedCommentHandler}
      />
    )}
    {comments}
  </section>
);
```
<br><br>
저장하고 로드하면 코멘트를 가져오는 동안 로딩스피너가 렌더되는 것을 알 수 있다.   
코멘트를 추가하면 id가 undefined로 되어 오류가 났다.  
그래서 api  파일의 addComment함수를 살짝 수정한다.  
<br><br>

```jsx
export async function addComment(requestData) {

```

<br><br>
매개변수부분에는 requestData만 넣는다.  
그리고 NewCommentForm 컴포넌트에서는 리퀘스트를 보냈을 때  
commentData필드를 가지는 하나의 객체를 보낸다.  
api파일에서 requestData안에서 commentData를 추출하므로.
<br><br>

```jsx
body: JSON.stringify(requestData.commentData),
```

<br><br>

그리고 quoteId가 있는 quoteId필드를 추출한다.
그러므로 quoteId 필드를 추가해 props.quoteId를 저장한다.
<br><br>

```jsx
sendRequest({ commentData: { text: enteredText }, quoteId: props.quoteId });
```

<br><br>
그러면 댓글 작성이 정상작동 되는 것을 볼 수 있다.  
여기서 addedCommentHandler를 아직 덜 작성한 것을 알 수 있다.

<br><br>
Comments에서 addedCommentHandler를 useCallback으로 감싼다.  
그리고 deps를 추가한다.
왜냐하면 addedCommentHandler가 onAddedComment prop을 통해  
새 코멘트 폼으로 전달되기 때문이다.  
그리고 그걸 NewCommentForm에 이 prop을 useEffect에 대한 deps로 사용했기 때문이다.  
우리가 addedCommentHandler를 useCallback으로 감싸지 않는다면,  
NewCommentForm의 useEffect의 deps로 활용된 것이 재생성되어 계속 재렌더링 될 것이므로
무한 루프에 빠지고 말 것이다.  
그것을 방지하기 위해서라도 useCallback으로 감싸는 것이다.

<br><br>

```jsx
const addedCommentHandler = useCallback(() => {}, []);
```

<br><br>
우리가 comment를 추가할 때마다 화면에 렌더링 하게 만들고 싶다.  
그러려면 새로운 요청을 보내자. addedCommentHanlder에 sendRequest를 호출한다.  
deps에 quoteId와 sendRequest를 추가한다.

<br><br>

```jsx
import { useState, useEffect, useCallback } from "react";
import { useParams } from "react-router-dom";
import useHttp from "../../hooks/use-http";
import { getAllComments } from "../../lib/api";
import LoadingSpinner from "../UI/LoadingSpinner";
import CommentsList from "./CommentsList";

import classes from "./Comments.module.css";
import NewCommentForm from "./NewCommentForm";

const Comments = () => {
  const [isAddingComment, setIsAddingComment] = useState(false);
  const params = useParams();

  const { quoteId } = params;

  const {
    sendRequest,
    status,
    data: loadedComments,
    error,
  } = useHttp(getAllComments);

  useEffect(() => {
    sendRequest(quoteId);
  }, [quoteId, sendRequest]);

  const startAddCommentHandler = () => {
    setIsAddingComment(true);
  };

  const addedCommentHandler = useCallback(() => {
    sendRequest(quoteId);
  }, [quoteId, sendRequest]);
  let comments;

  if (status === "pending") {
    comments = (
      <div className="centered">
        <LoadingSpinner />
      </div>
    );
  }

  if (status === "completed" && loadedComments && loadedComments.length > 0) {
    comments = <CommentsList comments={loadedComments} />;
  }

  if (
    status === "completed" &&
    (!loadedComments || loadedComments.length === 0)
  ) {
    comments = <p className="centered">No Comments were added yet!</p>;
  }

  return (
    <section className={classes.comments}>
      <h2>User Comments</h2>
      {!isAddingComment && (
        <button className="btn" onClick={startAddCommentHandler}>
          Add a Comment
        </button>
      )}
      {isAddingComment && (
        <NewCommentForm
          quoteId={quoteId}
          onAddedComment={addedCommentHandler}
        />
      )}
      {comments}
    </section>
  );
};

export default Comments;
```

<br><br>
이렇게 해놓으면 addedCommentHandler가  
sendRequest나 quoteId가 바뀌면 재생성될 것이다.
