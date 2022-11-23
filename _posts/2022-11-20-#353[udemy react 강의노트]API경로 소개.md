---
title: "#353[udemy react 강의노트]API경로 소개"
excerpt: "NextJS"
published: true
categories:
  - Blog
tags:
  - [Authentication]

toc: true
toc_sticky: false

date: 2022-11-20
last_modified_at: 2022-11-20
---

# 지금까지배운것 요약

<br><br>
지금까지 getStaticProps와 getStaticPaths와 gerServerSideProps에 관해 알아봤다.  
함수들이 하는 일이 뭔지, 왜 존재하는지 잘 이해해야 한다.  
이 함수들은 프리 렌더링 페이지에 데이터를 패치하도록 한다.  
따라서 데이터로 페이지를 프리 렌더할 수 있는 것이다. 필요한 데이터 없이는 할 수 없다.
<br><br>

```jsx
export const getServerSideProps = async (context) => {
  const req = context.req;
  const res = context.res;
  return {
    props: {
      meetups: DUMMY_MEETUPS,
    },
  };
};
```

<br><br>
지금까지는 더미 데이터로 작업했다. 실제 fetch로 데이터를 가져온 건 아니다.  
그리고 meetupID 페이지가 있다. 리턴 문장에서 하드 코드 데이터도 있다.  
이건 전혀 현실적이지 않다.  
대신 meetupAddPage가 있다. 거기서 meetupData를 입력할 수 있다.

<br><br>

```jsx
export const getStaticProps = async (context) => {
  const meetupId = context.params.meetupId;
  console.log(meetupId);

  return {
    props: {
      meetupData: {
        image:
          "https://cdn.pixabay.com/photo/2018/01/21/01/46/architecture-3095716_960_720.jpg",
        id: meetupId,
        title: "First Meetup",
        address: "Some Street 5, Some City",
        description: "The meetup description",
      },
    },
  };
};
```

<br><br>

# 실제 백엔드 추가하기

<br><br>
진짜 백엔드를 추가해서 데이터가 저장될 진짜 데이터베이스에 추가하고 패치할 것이다.  
여기서 넥스트 JS의 가장 중요한 특징이 있다. 넥스트에 의해서 리액트 앱에 추가된 것이다.  
프론트 엔드 리액트 앱과 함께 같은 프로젝트에서 넥스트 JS가 백엔드 API를 만들기 쉽게 했다.

<br><br>

# 또 다른 중요한 넥스트 JS의 특징 : API 라우트

<br><br>
API 라우트는 특별한 라우트, 특별한 페이지다.  
HTML 코드를 리턴하지 않고, 대신 HTTP 요청을 받는다.  
패치를 포스트하고 요청을 삭제한다. JSON 데이터가 부착되어 있다.  
그리고 우리가 필요한 걸 하면 된다.
<br><br>
예를들어 데이터베이스에 데이터를 저장하고 JSON 데이터를 리턴하는 것이다.  
따라서 API 라우트가 넥스트 프로젝트의 일부로 우리만의 API 엔드 포인트를 만들게 한다.  
그리고 넥스트 앱과 같은 서버로 서브 될 것이다.  
API 라우트를 추가하기 위해서 특별한 폴더를 추가한다.  
페이지 폴더에 추가하고, 이름은 api로 한다.

<br><br>
페이지 폴더 이름이 pages이듯이 이 폴더 이름도 api다.  
그리고 페이지 폴더 내에 있어야만 한다.  
그럼 넥스트 JS가 여기에 저장되어 있는 자바스크립트를 골라낼 것이다.  
그리고 그 파일들을 API 라우트로 보낼 것이다.  
<br><br>
따라서 엔드 포인트에서 요청에 의해 타겟될 수 있다.  
그리고 JSON을 받고 JSON을 리턴해야 한다.  
그리고 api 폴더에 자바스크립트 파일을 추가한다.  
파일 이름은 URL에서 경로 세그먼트가 될 것이다.

<br><br>
api폴더 안에 에를 들어 new-meetup.js 파일을 만든다.  
그리고 여기 있는 자바스크립트 파일에서 리액트 컴포넌트 함수는 입력하지 않는다.  
API 라우트는 리액트 컴포넌트를 정의하고, 렌더링하거나 리턴하지 않는다.  
<br><br>
대신 서버 사이드 코드를 포함하는 함수를 정의할 것이다.  
API 라우트는 클라이언트가 아닌 서버에서만 실행 된다.  
디코드해도 클라이언트에게 노출되지 않을 것이다.  
<br><br>
따라서 우리는 API 라우트에서 인증서를 사용할 수 있다.  
그리고 이 함수들은 라우트에 요청이 들어올 때마다 트리거 된다.  
따라서 /api/new-meetup을 입력한다. 이게 이 파일의 URL이다.  
그리고 이 URL에 요청이 보내지면 이 파일에 정의된 함수가 트리거될 것이다.

<br><br>
그리고 함수 이름은 마음대로 handler로 하고 export default한다.

<br><br>

```jsx
const handler = () => {};

export default handler;
```

<br><br>
이 함수는 요청을 받을 것이고 객체에 return할 것이다. node.js와 express.js에서 받을 것이다.  
요청 객체는 들어오는 요청에 관한 데이터를 포함한다.  
응답 객체는 응답을 보낼 때 필요하다.

<br><br>

```jsx
const handler = (req, res) => {};
```

<br><br>
이 응답 객체에서 헤더나 요청 바디를 받을 수 있다.  
그리고 method 속성을 사용헤 req.method를 입력한다.

<br><br>

```jsx
const handler = (req, res) => {
  req.method;
};

export default handler;
```

<br><br>
이건 어떤 요청이 보내졌는지 알게 해준다.  
if문으로 포스트 요청을 받았는지 확인한다.

<br><br>

```jsx
const handler = (req, res) => {
  if (req.method === "POST") {
  }
};

export default handler;
```

<br><br>
if 조건일 때만 실행한다. 다른 종류의 요청에는 아무것도 하지 않는다.  
따라서 이 라우트에서 포스트 요청일 때만 코드를 트리거한다.  
그리고 req.body에 접속해서 데이터를 받을 수 있다.

<br><br>

```jsx
const handler = (req, res) => {
  if (req.method === "POST") {
    const data = req.body;
  }
};
```

<br><br>
바디 필드는 또 다른 빌트인 메서드다. 데이터 요청의 바디를 포함하고 있다.  
그리고 우리가 필요한 걸 할 수 있다. 이제 여기 엔드 포인트에서 뉴 미트 업을 만들 것이다.  
따라서 여기 있는 데이터에는 타이틀과 이미지, 주소와 설명 필드가 있어야 한다.  
이건 우리 페이지고, 우리 프로젝트고, 우리 API다.  
따라서 우리가 어떤 데이터가 필요한지 정할 수 있다.  
다만 우리가 요청을 보낼 때 API 라우터에 맞는 데이터를 보냈는지 확실히 해야 한다.  
<br><br>
지금은 데이터 밖에서 데이터를 받을 것이다.  
따라서 요청 바디 밖에서 객체 디스트럭쳐링을 사용할것이다.  
타이틀필드, 이미지 필드, 주소 필드, 설명 필드를 받을 예정이다.  
이 네 가지 필드를 요청 바디에서 받고 싶다.  
그리고 그것들을 데이터베이스에 저장할 수 있다.  
<br><br>

```jsx
const handler = (req, res) => {
  if (req.method === "POST") {
    const data = req.body;

    const { title, image, address, description } = data;
  }
};

export default handler;
```

<br><br>

# 몽고 DB로 작업하기

<br><br>
이제 데이터베이스 시스템인 MongoDB를 사용해 보자.  
더 정확히 말하면, 무료로 사용할 수 있는 클라우드 기반의 MongoDB 데이터베이스 서비스인  
MongoDB Atlas를 사용해 보도록 한다. 무료 체험 버튼을 클릭하기만 하면 된다.  
버튼을 클릭하여 MongoDB Atlas 페이지로 이동하고 무료로 계정을 만들 수 있다.  
<br><br>
계정 생성이 모두 끝나고 로그인을하면 새로운 Cluster를 생성할 수 있는 페이지가 뜬다.  
Cluster Wizard 생성은 자동으로 종료될 수도 있다.  
여기의 모든 기본 설정은 변경하지 않아도 된다.  
<br><br>
다만, 돈을 지불하지 않으려면 free tier 설정을 선택하는 것을 잊지 말아야 한다.  
예를 들어, aws와 여기 있는 기본 영역을 선택하면 기본 설정을 그대로 유지하고 진행한다.  
특히 M0 Sandbox를 사용하고 있는지 확인해야 한다.  
강사의 경우는 해당 Sandbox를 기반으로 생성된 cluster를 이미 가지고 있으므로  
이걸 선택할 수 없지만, 아직 cluster가 없다면 M0 Sandbox 선택이 가능하다.  
거기에 사용하는 cluster tier는 모든 기본 설정을 그대로 두고 cluster를 생성한다.

<br><br>
이제 MongoDB 데이터베이스 cluster를 사용하여 연결한 다음  
데이터를 저장할 데이터베이스를 생성해 본다.  
이 cluster에 연결하려면 MongoDB Atlas 콘솔에서 두 가지 작업을 수행해야 한다.

<br><br>
Network Access에서 IP 주소 추가를 클릭하여 local IP를 추가하고  
현재 IP를 선택하여 local 컴퓨터가 MongoDB에 요청을 보낼 수 있게 한다.  
그다음 Database Access에서 데이터베이스에 대한 읽기 및 쓰기 권한을 가지는  
최소한 1명의 사용자를 생성하여 사용자 이름을 지정해야 한다.

<br><br>
나중에 이 cluster에 연결할 수 있는 권한을 가지는 사용자다.  
이 모든 과정을 마친 후 애플리케이션을 연결하여  
이 cluster에 연결할 방법을 배워야 한다.

<br><br>
Node.js MongoDB 드라이버를 사용하여 이 MongoDB Atlas cluster에  
조회를 보내는 api 경로에 코드를 작성해야 한다.  
이 작업을 수행하기 위해, development 서버를 종료하고  
MongoDB 패키지에 npm을 추가 설치하는 추가 패키지를 설치해야 한다.

<br><br>

```
npm install mongodb
```

<br><br>
이 패키지는 공식 MongoDB 드라이버로 MongoDB에 쉽게 queries를 보낼 수 있게 해준다.  
이것을 설치한 후 Development 서버를 다시 시작한다.

<br><br>

```
npm run dev
```

<br><br>
이제 MongoDB 드라이버는 이 cluster에 접속하여 데이터를 삽입하거나 가져올 수 있게 해준다.  
이 api 경로 파일에서는 연결을 가능하게 해주는  
MongoClient object 같은 것을 MongoDB로부터 가져올 수 있다.

<br><br>
new-meetup.js파일

```jsx
import { MongoClient } from "mongodb";
```

<br><br>

# MongoClient.connect메서드

<br><br>
new-meetup.js파일의 handler함수 안에서 코드를 실행할 수 있는데  
MongoClient를 사용하여 connect method를 호출한다.

<br><br>
new-meetup.js파일

```jsx
import { MongoClient } from "mongodb";

const handler = (req, res) => {
  if (req.method === "POST") {
    const data = req.body;

    const { title, image, address, description } = data;

    MongoClient.connect();
  }
};

export default handler;
```

<br><br>

# connect 하기위한 문자열

<br><br>
connect method는 아래의 연결 문자열을 필요로 한다.  
mongodb 사이트의 Database섹션에서 connect를 눌렀을 때 나온 문자열이다.  
이 문자열을 복사하여 connect method의 매개 변수로 붙여 넣기 한다.

<br><br>

```
mongodb+srv://mongo-user1:<password>@cluster0.7lqdxbj.mongodb.net/?retryWrites=true&w=majority
```

<br><br>
이제 사용자의 이름과 비밀번호를 입력해야 한다.  
database의 Access에서 id와 password를 copy하고 위 url의 password부분에 붙여넣는다.  
여기에 적혀있는 myFirstDatabase도 원하는 데이터베이스 이름으로 변경해야 해야 한다.  
원하는대로 meetups로 변경할 수 있다. 이렇게 하면 연결이 설정된다.  
<br><br>

```jsx
const client = MongoClient.connect(
  "mongodb+srv://mongo-user1:<password>@cluster0.7lqdxbj.mongodb.net/meetups?retryWrites=true&w=majority"
);
```

<br><br>
이 코드는 클라이언트 측에서 절대 실행되지 않도록 해야 한다.  
방문객에게 우리 인증 정보와 같은 보안 문제가 노출될 수 있기 때문이다.  
하지만 api 경로에서는 문제가 되지 않는다.  
<br><br>
전에 강의에서처럼, 여기서 정의된 코드는 클라이언트 측에서 끝나지 않기 때문에  
인증 정보를 저장하기에 안전한 장소다.  
이제 연결은 promise로 되돌아오므로 await를 입력하여  
handler 함수를 async function으로 전환한다.

<br><br>

```jsx
const handler = async (req, res) => {
  if (req.method === "POST") {
    const data = req.body;

    const { title, image, address, description } = data;

    await MongoClient.connect(
      "mongodb+srv://mongo-user1:<password>@cluster0.7lqdxbj.mongodb.net/meetups?retryWrites=true&w=majority"
    );
  }
};
```

<br><br>

이것은 NextJS에서 지원되고 실행할 수 있다.  
이렇게 하면 연결된 client를 제공하게 된다.

<br><br>

```jsx
const client = await MongoClient.connect(
  "mongodb+srv://mongo-user1:<password>@cluster0.7lqdxbj.mongodb.net/meetups?retryWrites=true&w=majority"
);
```

<br><br>

# client.db 메서드

<br><br>
이제 client 객체에서 .db method를 통해  
여기 meetups에 연결 중인 데이터베이스를 확보할 수 있다.

<br><br>

```jsx
const handler = async (req, res) => {
  if (req.method === "POST") {
    const data = req.body;

    const { title, image, address, description } = data;

    const client = await MongoClient.connect(
      "mongodb+srv://mongo-user1:<password>@cluster0.7lqdxbj.mongodb.net/meetups?retryWrites=true&w=majority"
    );
    const db = client.db();
  }
};
```

<br><br>
만약 아직 데이터베이스가 존재하지 않는다면 즉시 생성될 것이다.  
그렇게 되면, 이 데이터베이스에서 meetupsCollection에 접근할 수 있게 된다.  
<br><br>

```jsx
const handler = async (req, res) => {
  if (req.method === "POST") {
    const data = req.body;

    const { title, image, address, description } = data;

    const client = await MongoClient.connect(
      'mongodb+srv://mongo-user1:<password>@cluster0.7lqdxbj.mongodb.net/meetups?retryWrites=true&w=majority'
    );
    const db = client.db();

    const meetupsCollection =
  }
};

```

<br><br>

# db.collection 메서드

<br><br>
이제 MongoDB는 문서들로 가득 찬 컬렉션을 작동시키는 NoSQL 데이터베이스가 된다.  
collection은 SQL 데이터베이스에 있는 tables이고 문서는 해당 tables의 항목일 것이다.  
<br><br>
다시 말해, 여러 문서를 보관하고 있는 단일 meetup은 컬렉션을 가지게 되는 것이다.  
단일 meetup은 하나의 문서가 되고 전체 컬렉션은  
여러 meetup인 여러 meetup 문서들을 보관하는 거라고 보면 된다.  
또한, 데이터베이스와 컬렉션 메소드를 사용하여 컬렉션을 보관한다.  
<br><br>

<br><br>

```jsx
const meetupsCollection = db.collection();
```

<br><br>
컬렉션의 이름은 원하는 대로 설정할 수 있는데  
데이터베이스처럼 아직 설정되지 않았다면 바로 생성할 수 있고  
meetups로 동일하게 설정할 수 있다.  
이처럼 데이터베이스와 같은 이름을 입력해도 되지만 다른 이름을 사용해도 된다.

<br><br>

```jsx
const meetupsCollection = db.collection("meetups");
```

<br><br>

# insertOne 메서드

<br><br>
이제 meetupsCollection을 확보했다. 이 meetupsCollection에서는  
컬렉션에 새 문서를 삽입하기 위해 구축된 query 명령 중 하나인 insertOne을 호출할 수 있다.

<br><br>

```jsx
meetupsCollection.insertOne();
```

<br><br>
MongoDB의 훌륭한 점은 문서가 결국 object, 즉 자바스크립트의 object라는 것이다.  
따라서 제목, 이미지, 주소, 설명이 들어있는 객체다.  
<br><br>
그러므로 이런 경우에는 직접 데이터를 삽입할 수 있으므로  
전체 데이터 객체를 데이터베이스에 저장할 수 있게 된다.  
아래의 destructuring을 사용할 필요도 없으므로 삭제한다.

<br><br>

```jsx
const { title, image, address, description } = data;
```

<br><br>
이제 이 데이터 객체를 데이터베이스에 삽입해 보자.  
이것도 역시 Async 작업이다. insertOne은 promise를 되돌리므로  
여기에서도 await를 입력하여 이 작업의 결과값을 돌려받을 수 있다.
result는 예를들어 자동으로 생성된 ID 가진 객체가 된다.
원한다면 console.log를 입력해 결과값을 얻을 수 있다.

<br><br>

```jsx
const result = await meetupsCollection.insertOne(data);
console.log(result);
```

<br><br>
여기에 오류 처리를 추가하여 연결하거나 삽입하는 데 실패하는 경우를 처리할 수 있다.  
강의 전반적인 부분에서 이것을 실행하겠지만,  
우선 여기서 이것이 잘 작동되는지 살펴보자.  
원한다면 오류 처리를 추가하기 위해 try, catch로 감쌀 수 있다.  
<br><br>

# client.close 데이터베이스 연결 차단

<br><br>
이제 작업을 마쳤기 때문에 client.close를 불러서 데이터베이스 연결을 차단한 다음  
여기의 res 객체를 사용하여 응답을 다시 보내야 한다.  
요청이 들어오면 데이터베이스에 데이터를 저장하고 다시 응답을 보내야 하기 때문이다.  
이 res 객체로 이것을 수행한다. 이 작업은 Node Express를 사용하는 것과 매우 유사할 수 있다.

<br><br>

```jsx
const handler = async (req, res) => {
  if (req.method === "POST") {
    const data = req.body;

    const { title, image, address, description } = data;

    const client = await MongoClient.connect(
      'mongosh "mongodb+srv://cluster0.7lqdxbj.mongodb.net/myFirstDatabase" --apiVersion 1 --username mongo-user1'
    );
    const db = client.db();

    const meetupsCollection = db.collection("meetups");

    const result = meetupsCollection.insertOne(data);
    console.log(result);
    client.close();
  }
};
```

<br><br>

# res 객체를 사용하여 응답다시 보내기

<br><br>
되돌아오는 응답의 HTTP status 코드를 설정하기 위해  
응답을 호출할 수 있는 status method를 갖게 되는데  
예를 들어, 201 status 코드는 어떤 것이 성공적으로 삽입되었음을 나타낸다.

<br><br>

```jsx
res.status(201);
```

<br><br>

# 응답에 추가될 JSON 데이터

<br><br>
그런 다음, 여기서 JSON 호출을 연결하여 발신 응답에 추가될 JSON 데이터를 준비할 수 있다.  
또한, 예를 들어, Meetup inserted라는 메시지 키를 입력할 수도 있다.

<br><br>

```jsx
res.status(201).json({ message: "Meetup inserted" });
```

<br><br>
물론, 어떤 응답을 되돌아오게 할지는 선택해서 입력하면 된다.  
데이터베이스에 meetups를 삽입할 기본 api 경로를 가지고 있으므로  
다음 단계에서는 이 api 경로를 trigger하고 코드를 사용할 수 있도록  
이 React Meetups 정보를 작성 및 제출하여 이 api 경로에 request를 보내본다.

<br><br>
