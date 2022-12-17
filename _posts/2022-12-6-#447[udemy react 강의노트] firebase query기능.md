---
title: "#447[udemy react 강의노트] firebase query 탐색기능"
excerpt: "React firebase query"
published: true
categories:
  - Blog
tags:
  - [React, firebase, query]

toc: true
toc_sticky: false

date: 2022-12-06
last_modified_at: 2022-12-06
---

# firebase query 탐색기능

<br><br>
지금 요청에는 필터링하는 부분이 없습니다
<br><br>
Search.js파일
<br><br>

```jsx
import React, { useEffect, useState } from "react";

import Card from "../UI/Card";
import "./Search.css";

const Search = React.memo((props) => {
  const [enteredFilter, setEnteredFilter] = useState("");
  const { onLoadIngredients } = props;
  useEffect(() => {
    fetch(
      "https://react-http-55f5b-default-rtdb.firebaseio.com/ingredients.json"
    )
      .then((response) => {
        return response.json();
      })
      .then((responseData) => {
        const loadedIngredients = [];
        for (const key in loadedIngredients) {
          loadedIngredients.push({
            id: key,
            title: responseData[key].title,
            amount: responseData[key].amount,
          });
        }
        onLoadIngredients(loadedIngredients);
      });
  }, [enteredFilter, onLoadIngredients]);

  return (
    <section className="search">
      <Card>
        <div className="search-input">
          <label>Filter by Title</label>
          <input
            type="text"
            value={enteredFilter}
            onChange={(event) => setEnteredFilter(event.target.value)}
          />
        </div>
      </Card>
    </section>
  );
});

export default Search;
```

<br><br>
<br><br>
search input창에 입력을 할 때마다 useEffect가 실행되고,  
firebase에 있던 데이터들이 loadedIngredients라는 array에 들어가,  
ingredients파일에 있는 onLoadIngredients함수에 해당 array를  
넣어 실행해 줍니다.  
<br><br>
Ingredients.js파일
<br><br>

```jsx
const filteredIngredientsHandler = (filteredIngredients) => {
  setUserIngredients(filteredIngredients);
};
```

<br><br>
그러면 Ingredients 파일에서 useIngredients라는 state를  
받은 loadedIngredients(filteredIngredients)로 설정하게 됩니다.  
<br><br>
그러나 현재 filter input창에 입력한 아이템 명으로  
필터가 되어야 하지만 해당 기능은 코드에 존재하지 않습니다.

<br><br>
모든 Ingredients를 가져오는 대신에, 필터링된 재료를 가져와야 하죠

<br><br>
Firebase는 고맙게도 필터링을 지원합니다  
매개변수 쿼리를 붙이면 되죠  
‘query’ 변수를 만들어서 저장하고 URL 뒤에 붙입니다.  
저는 입력된 값이 있는지 여부에 따라  
다른 쿼리를 만들어 붙이고 싶습니다

<br><br>
enteredFilter의 길이가 0이면 쿼리를 비울 겁니다,  
매개변수 쿼리를 넣지 않을 거예요  
0이 아니면 백틱 기호(`) 문자열을 만들 겁니다

<br><br>

```jsx
const query = enteredFilter.length === 0 ? "" : `?orderBy=""`;
```

<br><br>
홑따옴표(‘)가 아니라 백틱 기호예요,  
이렇게 해서 문자열 보간(String Interpolation)을 사용해  
동적으로 값을 삽입할 겁니다. 그리고 ‘?orderBy’를 붙이고요

<br><br>
등호를 넣은 뒤 쌍따옴표를 열고 닫습니다  
쌍따옴표 사이에 필터링할 필드를 넣고요  
지금은 title 필드겠죠, 뒤에 ‘&equalTo’를 붙이고요  
다시 등호 뒤에 쌍따옴표를 열고 닫습니다.

<br><br>
문자열 보간을 이용해서 달러 기호($) 뒤에 중괄호를 열고 닫은 뒤

<br><br>

```jsx
const query =
  enteredFilter.length === 0
    ? ""
    : `?orderBy="title"&equalTo="${enteredFilter}"`;
```

<br><br>
중괄호 안에 enteredFilter를 값으로 넣습니다  
query는 빈 문자열이거나 이 문자열로 설정될 거예요  
<br><br>

```jsx
`?orderBy="title"&equalTo="${enteredFilter}"`;
```

<br><br>
이 구문은 쿼리 매개변수와 결합돼 Firebase에 의해 해석될 거고  
입력한 값에 따라 title 필드를 필터링할 겁니다  
<br><br>

```jsx
&equalTo="${enteredFilter}"
```

<br><br>
이걸 요청 뒤에 붙여주면 되고요

<br><br>

```jsx
+query;
```

<br><br>

```jsx
fetch(
  "https://react-http-55f5b-default-rtdb.firebaseio.com/ingredients.json" +
    query
);
```

<br><br>
더하기표(+)를 사용하고 query를 붙이면 됩니다.  
query는 빈 문자열이거나 우리가 만든 쿼리문이겠죠  
이 쿼리가 동작하게 만들려면 Firebase 설정을 조금 변경해야 합니다

<br><br>
Database의 ‘Rules’ 항목으로 가보죠  
여기에 엔트리를 추가해야 필터링을 사용할 수 있어요  
쌍따옴표를 “.read”, “.write” 뒤에 추가하고요

<br><br>
우리가 데이터를 보내는 우리 노드 이름 ingredients죠

<br><br>

```jsx
"https://react-http-55f5b-default-rtdb.firebaseio.com/ingredients.json";
```

<br><br>
값은 객체니까 중괄호를 열고 닫고요  
객체 안에 다시 쌍따옴표를 넣고 또 다른 문자열  
“.indexOn”를 추가합니다 Firebase에 사용하는 명령어죠  
저장될 값은 문자열 배열입니다  
여기에 필터링하고 싶은 필드의 이름을 넣는 거고요

<br><br>

```json
{
  "rules": {
    ".read": true, // 2022-10-5
    ".write": true, // 2022-10-5
    "ingredients": {
      ".indexOn": ["title"]
    }
  }
}
```

<br><br>
이 설정을 Firebase의 Rules에 추가해줘야 합니다,  
이렇게 설정하면 title 필드를 필터링 할 수 있죠  
요청은 여기서 보내고요  
입력창에 뭔가 입력될 때마다 요청을 보낼 겁니다

<br><br>
<br><br>
