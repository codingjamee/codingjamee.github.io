---
title: "#359[udemy react 강의노트]NextJS 프로젝트 배포하기"
excerpt: "NextJS"
published: true
categories:
  - Blog
tags:
  - [NextJS, deploy, Vercel]

toc: true
toc_sticky: false

date: 2022-11-23
last_modified_at: 2022-11-23
---

# NextJS앱 배포하기

<br><br>
NextJS 앱을 배포할 때는 다양한 호스팅 공급자를 사용할 수 있고  
프로젝트를 구성하는 방법도 다양하다.  
예를 들어, 환경 변수를 사용할 수 있고  
데이터베이스의 인증 정보를 동일하게 보유할 수도 있다.  
이번에는 코드에 저장된 인증 정보를
그대로 유지하도록 한다.

<br><br>
이 코드는 절대 클라이언트에게 보이지 않기 때문에  
보안 측면에 볼 때 그대로 유지해도 괜찮다. 개발이나 제작에 있어서  
하나의 동일한 데이터베이스와 동일한 사용자를 사용하는 것은 불편할 수 있지만  
이 데모에서는 문제가 되지 않기 때문에 여기서는 그대로 진행한다.
<br><br>
<br><br>

# 호스팅 제공자 Vercel 이용하기

<br><br>
호스팅 제공자로는 Vercel을 이용한다. Vercel은 NextJS 애플리케이션을 위한  
훌륭한 호스팅 제공 업체다. 사실 Vercel은 NextJS를 개발한 팀과 같은 팀이 만든  
호스팅 서비스 업체다. 그래서 NextJS를 잘 수용하고 NextJS에 최적화되어 있다.

<br><br>
<br><br>

# Vercel에 NextJS 애플리케이션을 배포하는 방법

<br><br>
배포를 시작하려면 Git repository 공급 서비스에 등록을 해야 한다.  
Vercel을 사용하면 바로 소스 코드가 저장된 GitHub repository에 연결하여  
해당 저장소에서 소스 코드를 가져와 배포할 수 있다.  
따라서 Vercel로 배포하기 전에 Git repository 공급 서비스인  
GitHub, GitLab 또는 Bitbucke을 사용하여 거기에 코드를 저장해야 한다.

<br><br>
GitHub에서 New repository를 생성한다.  
repository가 생성되면 여기에 코드를 입력한다.  
그리고 GitHub에 코드를 push한다.

<br><br>

```
$ git push --all
```

<br><br>
이제 여기에 코드가 push 되고 암호를 입력하라는 메시지가 뜨면  
GitHub 암호를 입력하거나 인증을 위해 token을 사용할 수 있다.  
이러한 token을 생성하려면 GitHub 설정으로 가서 개발자 설정 페이지로 이동하여  
Personal access token으로 가서 새로운 token을 생성할 수 있다.  
암호로 이를 확인하면 여기에 NextJS 배포 또는 더 일반적인 목적 같은  
몇 가지 정보를 추가할 수 있다. 그다음 아래의 범위를 선택하는 Select scropes에서  
admin_repo_hook을 선택한 다음 token을 생성하여 token을 복사하여  
암호를 입력하라는 메시지가 나타나면 이것을 암호로 입력한다.

<br><br>
이 모든 작업을 완료하고 성공적으로 push 하면 main branch에서 방금 생성한 repository에  
코드가 보인다. 이제 이 repository를 Vercel에 연결하여 배포를 시작할 수 있다.  
프로덕션을 만들고 Vercel이 실제로 하는 일에 대해 알아보자.

<br><br>

# Vercel이란?

<br><br>
Vercel은 호스팅 공급 서비스로 NextJS에서만 사용하는 것은 아니다.  
다른 프로젝트에서도 사용할 수 있지만 NextJS에 최적화되어 있다.  
따라서 다른 프로젝트에서도 호스팅 할 수 있으며 다른 유형의 프로젝트에서도 최적화될 수 있다.  
단연코 NextJS에 최적화되어 있고 Vercel을 GitHub repository에 연결하기만 하면  
Vercel이 구축하고 배포하여 프로젝트를 시작할 수 있게 한다.  
즉, 우리가 구축한 명령어를 실행할 필요 없이 Vercel이 이것을 실행해 준다.  
<br><br>

# Vercel이 하는 일

<br><br>
다시 local code로 돌아와 확인해 보면 package.json에서  
이 프로젝트에서 실행할 수 있는 명령어인 scripts를 확인할 수 있다.

<br><br>

```json
 "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start"
  },
```

<br><br>
development 서버를 시작하기 위해 Dev script로 많은 작업을 했고  
local development 작업 중에 사용한 script다.  
하지만 프러덕션을 위한 프로젝트를 구축하기 위해 build script도 실행할 수 있다.
<br><br>

```
npm run build
```

<br><br>
따라서 모든 코드를 최적화하고 축소하는 등의 작업을 수행한다.  
또한 프로젝트를 구축하지 않는 다른 호스트에도 배포하여 실행할 수 있다.  
따라서 Vercel에서 수행할 필요가 없어도 이것을 호스팅하는데 계속 사용할 수도 있다.  
이 명령을 실행하면 최적화된 파일 일부를 포함하는 .next 폴더를 얻게 된다.

<br><br>
예를 들어, 이 .next 폴더는 미리 생성된 모든 페이지 파일과  
시작 페이지 또는 개별 meetup 세부 정보 페이지와 같은 해당 HTML 파일을 포함한다.  
이것이 npm run build가 해주는 일이다.  
Vercel이 서버를 대신하여 명령을 실행하기 때문에 작업을 따로 할 필요가 없다.  
하지만 직접 관리하는 다른 서버에서 프로젝트를 호스트하려면  
npm run build를 실행해야 npm start로 프로덕션 서버를 시작할 수 있다.

<br><br>

```
$ npm start
```

<br><br>
다시 말해, 실행 중인 서버가 있지만 이것은 원격 기기에서 시작할 수 있는 프로덕션 서버이며  
다음 애플리케이션을 위해 실행하는 사용자가 직접 관리하는 서버다.  
따라서 애플리케이션을 외부에 공개하려는 경우 애플리케이션을 배포한 기기에서 해당 서버는  
계속 실행되고 있어야 한다.  
여기서 작업하고 있지만 이제 모든 작업을 Vercel이 수행할 것이다.  
이제 Vercel로 돌아가서 GitHub과 함께 진행해보자.

<br><br>
github에 코드를 올리고 verce에서 프로젝트를 배포해 본다.  
그러고 나서 배포된 사이트에서 새로운 meetup을 추가하고 detail페이지를 확인해보면  
에러가 난다. 이것은 예정되어있는 에러였다.  
<br><br>
문제는 단일 meetup 페이지에서 미리 생성되어야 하는 meetup페이지를 지정하고  
build time에서만 설정하여 이후로는 설정되지 않았기 때문이다.  
fallback이 false로 설정되었으므로 이전에 미리 생성된 페이지가 없다는  
meetups의 requests도 실패하게 된 것이다.
<br><br>

```jsx
return {
  fallback: false,
  paths: meetups.map((meetup) => ({
    params: { meetupId: meetup._id.toString() },
  })),
};
```

<br><br>
getStaticPaths function이 들어오는 모든 request를 실행했기 때문에  
새로운 meetup을 추가하지 않고 development 동안은 작동되었지만,  
deployment 후에는 실패하였다.
<br><br>
fallback을 true 또는 더 낫게 하기 위하여 blocking으로 설정한다.

<br><br>

```jsx
return {
  fallback: blocking,
  paths: meetups.map((meetup) => ({
    params: { meetupId: meetup._id.toString() },
  })),
};
```

<br><br>
fallback을 true나 blocking으로 설정하면 여기서 지정한 경로 목록이  
완전하지 않을 수 있고 더 유효한 페이지가 있을 수 있음을 NextJS에 알려준다.

<br><br>
따라서 fallback을 true 또는 blocking으로 설정하면 NextJS가 바로  
페이지를 찾을 수 없는 경우 404페이지로 응답하지는 않는다.  
대신에 fallback을 true나 blocking으로 설정하면  
요청 시 페이지를 생성한 후에 캐시에 저장하여 필요할 때 이것을 미리 생성합니다.

<br><br>

# fallback : true와 blocking의 차이점

<br><br>
true로 설정하면 빈 페이지가 즉시 반환되고 동적으로 생성된 콘텐츠를 풀다운한다.  
따라서 페이지에 데이터가 아직 없는 경우를 처리해야 한다.  
blocking으로 설정할 경우는 페이지가 미리 생성될 때까지  
사용자는 아무것도 볼 수 없고 완성된 페이지가 제공된다.

<br><br>
이처럼 다른 추가 작업이 필요하지 않기 때문에  
여기에서는 blocking을 사용한다.  
<br><br>

```jsx
return {
  fallback: blocking,
  paths: meetups.map((meetup) => ({
    params: { meetupId: meetup._id.toString() },
  })),
};
```

<br><br>

# vercel에서의 redeployment

<br><br>

이제 이걸 redeploy 하기 위해 해야 일은 git add를 입력하여  
새로운 코드 스냅샷을 저장하고 git commit와  
fixed fallback이나 다른 문자를 입력한 다음  
git push를 입력하는 것으로 branch를 다시 push 하여  
GitHub에 repository를 다시 push 한다.

<br><br>

```
git add .
git commit -m "메시지"
git push
```

<br><br>
놀라운 점은, Vercel이 push한 main branch를 주시하여  
변화가 감지될 때마다 자동으로 building과 redeploying을 시작한다.  
building이 다시 진행 중인 것을 볼 수 있다.  
페이지를 업데이트하고 redeploy 하려면 새 commit를 생성하여  
연결된 GitHub repository에 push 하기만 하면  
자동으로 redeployment가 작동된다.

<br><br>
또한, redeploying 하는 동안에도 이전 페이지는 계속 실행된다.  
작업이 완료되면 새 페이지로 교체된다.  
그리고 Add New Meetup을 클릭하여 Title에 Another Meetup을 입력하고  
image는 붙여넣고, Address와 Description에 내용을 입력하면  
이제 변경 사항이 적용되며 오류가 나지 않는다.

<br><br>
그럼 이제 시작 페이지를 몇 번 다시 로딩하여 재확인하고  
meetup 페이지를 다시 확인해 보면 빠르게 생성되어 모든 작업을 수행한다.  
프로젝트에서 추가 도메인을 구성할 수도 있다.
