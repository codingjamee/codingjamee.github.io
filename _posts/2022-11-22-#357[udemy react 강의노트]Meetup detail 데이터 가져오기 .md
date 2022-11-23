---
title: "#357[udemy react 강의노트]Meetup detail 데이터 가져오기"
excerpt: "NextJS"
published: true
categories:
  - Blog
tags:
  - [NextJS, API, Http]

toc: true
toc_sticky: false

date: 2022-11-22
last_modified_at: 2022-11-22
---

#

<br><br>
이제 서버 측 코드를 getStaticProps에서 실행해 본다.  
이것이 표준 방식이라는 점을 강조한다.  
이 코드는 클라이언트 측 번들로 끝나지 않기 때문에  
인증 정보는 노출되지 않고 번들은 bloated 되지 않는다.  
이 코드는 페이지가 pre-generated 될 때 실행된다.

<br><br>
MeetupDetail 페이지의 경우 선택한 특정 meetup뿐만 아니라  
생성한 경로를 결정하기 위해 데이터베이스에서 데이터를 가져와야 한다.

<br><br>

```jsx
export const getStaticPaths = async () => {
  return {
    fallback: false,
    paths: [
      {
        params: {
          meetupId: "m1",
        },
      },
      {
        params: {
          meetupId: "m2",
        },
      },
    ],
  };
};
```

<br><br>
결국 mongodb에서 특정 ID를 가지는 제한된 meetups 목록만 있으므로  
meetupID가 지원되어야 한다. 따라서 여기에 있는 모든 ID에 대한 경로를  
미리 생성 해야 하므로 MongoDB에 다시 연결해야 한다.  
<br><br>
이를 위해, index.js에서 연결 코드를 다시 복사하고  
(중복을 피하려면 refactor 할 수 있다)

```jsx
const client = await MongoClient.connect(
  "mongodb+srv://mongo_user2:61Im8fO9sFso28LW@cluster0.7lqdxbj.mongodb.net/meetups?retryWrites=true&w=majority"
);
const db = client.db();

const meetupsCollection = db.collection("meetups");

const meetups = await meetupsCollection.find().toArray();
```

meetupId의 index.js 파일에서  
import {MongoClient } from 'mongodb' 을 하고  
getStaticPaths를 시작할 수 있다.  
<br><br>
거기서 detabase에 연결하고 meetupsCollection에 엑세스해서  
모든 meetup 데이터를 얻는다.
<br><br>
이를 위해, meetups = await meetupCollection.find(); 를 입력한다.
find는 모든 meetups에 엑세스할 수 있게 해준다.

<br><br>

```jsx
export const getStaticPaths = async () => {
  const client = await MongoClient.connect(
    "mongodb+srv://mongo_user2:61Im8fO9sFso28LW@cluster0.7lqdxbj.mongodb.net/meetups?retryWrites=true&w=majority"
  );
  const db = client.db();

  const meetupsCollection = db.collection("meetups");

  const meetups = await meetupsCollection.find()
```

<br><br>
여기에서는 ID에만 관심이 있으므로 첫 번째 argument로  
빈 객체를 찾아 전달할 수 있다.

<br><br>

```jsx
const meetups = await meetupsCollection.find({});
```

<br><br>
모든 문서를 찾는 것이 아니고 특정 필드 값을 필터링 하는 것이라면  
필터 기준을 정의할 수 있다.  
하지만 우리는 모든 문서를 찾길 원하기 때문에 빈 객체를 사용하여  
필터에 기준은 없지만 모든 객체를 가져온다.
모든 문서가 추출되어야 하는 필드를 정의하는  
두 번째 argument를 전달한다.

<br><br>

```jsx
const meetups = await meetupsCollection.find({}, {});
```

<br><br>
기본적으로 모든 필드는 다시 반환된다. 모든 필드 값, 이름, 이미지 등의 모든 것이 있지만  
ID에만 관심이 있으므로 여기에 `_id:`를 추가하고 1로 설정한다.

<br><br>

```jsx
const meetups = await meetupsCollection.find({}, { _id: 1 });
```

<br><br>
`_id: 1`은 ID만 포함하고 다른 필드 값은 포함하지 않는다는 뜻이다.  
이렇게 설정하고 ID를 가져오기만 하면 된다.  
즉, 문서 객체들을 가져오지만 각각의 객체는 ID만 포함하고 다른 것은 포함하지 않는다.

<br><br>
이제 다시, 이것을 JavaScript 객체 배열로 변환하기 위해
toArray를 여기에 입력하여 호출한다.

<br><br>

```jsx
const meetups = await meetupsCollection.find({}, { _id: 1 }).toArray();
```

<br><br>

여기에(pages> [meetupId] > index.js) meetups를 가지고 있고  
이제 동적으로 경로를 생성할 수 있다. 이 배열을 hard coding 하는 대신에  
여기에 meetups 그리고 id가 있는 문서인 각각의 meetup 아이템을 map하여

경로는 객체의 배열이어야 하므로 객체로 입력하고  
여기 아래에 보이는 것처럼 모든 객체가 가지고 있는 params key를  
params: 입력하고

그 안에 nested object가 있으므로 meetupId: 을입력하여  
meetupId 값을 정의하는 곳을 지정한다.  
 meetupId 값은 여기의 ID가 되어야 하므로  
meetup을 입력하여 엑세스한다.

<br><br>

```jsx
paths: meetups.map((meetup) => ({ params: { meetupId: meetup } }));
```

<br><br>
이 매개 변수는 자동으로 map을 주기 때문에 이렇게 `._id.toString`을 입력한다.
<br><br>

```jsx
paths: meetups.map((meetup) => ({
  params: { meetupId: meetup._id.toString() },
}));
```

<br><br>
이제 아래의 배열을 제거한다.

<br><br>

```jsx
[
  {
    params: {
      meetupId: "m1",
    },
  },
  {
    params: {
      meetupId: "m2",
    },
  },
],
```

<br><br>
이렇게 설정하여 경로 배열을 동적으로 생성한다.  
작업한 결과를 저장하고 시작 페이지를 다시 로딩한 다음  
Show Details 버튼을 클릭하면 특정 meetup과  
특정 객체에 대한 페이지로 이동하게 된다.

<br><br>  
주소창에 보면 암호화 ID를 확인할 수 있는데  
이 암호화 ID는 MongoDB가 생성한 자동으로 생성된 ID다.  
작업이 성공적으로 완료되었지만 아직은 동적으로 데이터를 가져올 수 없다.

<br><br>

```jsx
export const getStaticProps = async(context) => {
  //fetch data for a single meetup

  const meetupId = context.params.meetupId;

  console.log(meetupId)

  return {
    props : {
```

<br><br>
그 작업을 해본다.
먼저, 여기 연결을 끊는다.

<br><br>

```jsx
export const getStaticPaths = async () => {
  const client = await MongoClient.connect(
    "mongodb+srv://mongo_user2:61Im8fO9sFso28LW@cluster0.7lqdxbj.mongodb.net/meetups?retryWrites=true&w=majority"
  );
  const db = client.db();

  const meetupsCollection = db.collection("meetups");

  const meetups = await meetupsCollection.find({}, { _id: 1 }).toArray();

  client.close();
```

<br><br>
작업이 끝나면 ID를 console.log 하는 대신  
위의 코드를 getStaticPaths에서 복사하여  
meetupId를 얻은 후에 getStaticProps에 추가하는 것이 좋다.  
다시 연결하여 컬렉션에 엑세스 한다.

<br><br>
그리고 meetupsCollection을 사용하고  
그다음 findOne method를 위해 findOne( ); 을 사용한다.

<br><br>

```jsx
const selectedMeetup = meetupsCollection.findOne();
```

<br><br>

# findOne 메서드

<br><br>
findOne은 하나의 문서를 찾는다는 것을 의미한다.  
그리고 findOne에 어떻게 필터링하고 어떻게 문서를 검색하는지  
정의하는 객체를 전달해야 한다.

<br><br>
이 객체에서 이름, 이미지, 주소 또는 설명 같은
필드 이름을 키로 전달하고
값으로 검색하려는 값을 전달할 수 있다.
따라서 key-value pair로서의 title: A First Meetup을
여기 객체에 전달하여
title is A First Meet에서 이 first meetup을 찾을 수 있다.
<br><br>

```jsx
const selectedMeetup = meetupsCollection.findOne({});
```

<br><br>
<br><br>
이제 여기서 title이 아니라 id로 검색하길 원하므로 `_id: meetupID`를 입력하여  
자동으로 추가되고 생성된 ID 필드가 meetupId 값을 가지는지 확인한다.  
이 meetupId는 위에 있는 `meetupId = context.params.meetupId`에서 온 것이다.

<br><br>
그러면 단일 meetupd을 찾아준다.
이것은 비동기 작업이기 때문에 promise를 반환한다.
따라서 이것을 await하고 function 앞에 async를 추가했는지 확인하고
최종적으로 selectedMeetup을 얻었는지 확인한다.

<br><br>

```jsx
const selectedMeetup = await meetupsCollection.findOne({ _id: meetupId });
```

<br><br>
이제 selectedMeetup을 meetupData로 반환한다.

<br><br>

```jsx
return {
  props: {
    meetupData: selectedMeetup,
  },
};
```

<br><br>

```jsx
export const getStaticProps = async (context) => {
  //fetch data for a single meetup
  const client = await MongoClient.connect(
    "mongodb+srv://mongo_user2:61Im8fO9sFso28LW@cluster0.7lqdxbj.mongodb.net/meetups?retryWrites=true&w=majority"
  );
  const db = client.db();

  const meetupsCollection = db.collection("meetups");

  const selectedMeetup = await meetupsCollection.findOne({ _id: meetupId });

  const meetupId = context.params.meetupId;

  console.log(meetupId);

  return {
    props: {
      meetupData: selectedMeetup,
    },
  };
};
```

<br><br>
이렇게 하고 전부 저장한 다음 meetup을 다시 로딩하면  
성공적으로 로딩이 되어 모든 데이터를 디스플레이되어 보여집니다.
그러나 지금 여기서 보이는 데이터는 여전히 JSX 코드로 하드 코딩되어있다.

<br><br>

```jsx
const MeetupDetails = () => {
  return (
    <MeetupDetail
      img="https://cdn.pixabay.com/photo/2018/01/21/01/46/architecture-3095716_960_720.jpg"
      title="A First Meetup"
      address="Some Street 5, Some City"
      description="The meetup description"
    />
  );
};
```

<br><br>
이제 props 데이터를 사용해야 한다. JSX 코드에 있는 actual fetch data도 함께 말이다.
이를 위해, 컴포넌트에 meetupData prop을 노출 시켜서 props를 받아들이고  
해당 데이터를 여기서 사용한다.

<br><br>

```jsx
const MeetupDetails = (props) => {

```

<br><br>

```jsx
const MeetupDetails = (props) => {
  return (
    <MeetupDetail
      img="https://cdn.pixabay.com/photo/2018/01/21/01/46/architecture-3095716_960_720.jpg"
      title="A First Meetup"
      address="Some Street 5, Some City"
      description="The meetup description"
    />
  );
};
```

<br><br>
image의 경우는 props.image에 전달하고  
다른것들도 아래와 같이 적용한다.
사실 props. 이 아니라 props.meetupData.을 입력해야 한다.

<br><br>
<br><br>

```jsx
const MeetupDetails = (props) => {
  return (
    <MeetupDetail
      img={props.meetupData.image}
      title={props.meetupData.title}
      address={props.meetupData.address}
      description={props.meetupData.description}
    />
  );
};
```

<br><br>
왜냐하면 이 meetupData prop은  
meetup item, meetup object를 가지기 때문이다.  
따라서 이미지, 이름, 주소 그리고 설명에 엑세스 하기 전에  
meetupData 객체다음에 넣어야 한다.

<br><br>
여기 findOne의 경우는 URL에서 얻는 같은 id를 찾지만 문자열로 보인다.  
사실 MongoDB에서 ID는 이상한 객체 ID라는 것을 명심하자.  
<br><br>

```
id: ObjectId("603cf2bb7c...")
```

<br><br>
특정 ID를 정확하게 찾으려면 이것을 문자열에서 객체 ID로 전환해야 한다.  
이를 위해서는 MongoDB로부터 이렇게 마지막을 소문자 d로 입력하여  
ObjectId를 import 해야 한다.
<br><br>

```jsx
import { MongoClient, ObjectId } from "mongodb";
```

<br><br>

그리고 문자열을 wrap 합니다.  
여기 meetupId 앞에 ObjectId를 입력하여 wrap 하여

<br><br>

```jsx
const selectedMeetup = await meetupsCollection.findOne({
  _id: ObjectId(meetupId),
});
```

<br><br>
ObjectId 객체로 문자열을 전환한다. 작업이 완료되면 selectedMeetup으로 이동하여  
이 `_id` 필드인 ID를 문자열로 변환해야 한다.  
<br><br>

그렇지 않으면 이전에 보았던 serialization error가 발생할 수 있기 때문이다.
<br><br>

```jsx
Error: Error serializing `.meetupData._id` returned from `getStaticProps` in "/[meetupId]".
Reason: `object` ("[object Object]") cannot be serialized as JSON. Please only return JSON serializable data types.
```

<br><br>

meetupData을 객체로 설정하기 위해 id:를 입력하여 id 필드를 추가하고  
`selectedMeetup._id.toString( )`, 을 입력한 다음 모든 다른 데이터를 추가하기 위해  
title: selectedMeetup. title,  
address: selectedMeetup. address,  
image: selectedMeetup. image,  
그리고 마지막으로  
description: selectedMeetup. description,을 입력한다.

<br><br>

```jsx
return {
  props: {
    meetupData: {
      id: selectedMeetup._id.toString(),
      title: selectedMeetup.title,
      address: selectedMeetup.address,
      image: selectedMeetup.image,
      description: selectedMeetup.description,
    },
  },
};
```

<br><br>

모든 작업을 완료한 후 이제 이것을 저장하고 단일 meetup 페이지를 다시 로딩하면  
단일 meetup의 데이터를 확인할 수 있다.  
이제 모든 meetups이 작동됩니다. 이제 meetup detail 페이지는  
코드가 활성화된 상태에서 서버에서 동적으로 pre-rendered 되고  
코드가 데이터베이스를 연결을 설정하여 거기서 데이터를 가져옵니다.
