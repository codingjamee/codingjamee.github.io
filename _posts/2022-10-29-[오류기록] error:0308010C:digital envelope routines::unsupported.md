---
title: "[오류기록] error:0308010C:digital envelope routines::unsupported"
excerpt: "npm start 할 때의 오류 "
published: true
categories:
  - Blog
tags:
  - [npm, error:0308010C, error, npm start, ubuntu]

toc: true
toc_sticky: false

date: 2022-10-29
last_modified_at: 2022-10-29
---

# 아래의 에러가 떴다

```
error:0308010C:digital envelope routines::unsupported
```

<br><br>
해결 참조 사이트 : https://bobbyhadz.com/blog/react-error-digital-envelope-routines-unsupported

<br><br>

# 위의 참조사이트를 보고 번역하여 오류를 해결하였기에 그대로 사이트를 번역하여 기록함.

(번역 실력 부족함)

<br><br>

## 번역글에 들어가기 앞서 오류의 이유

<br><br>
ubuntu로 넘어가고 나니, create-react-app이 잘 되지 않았다.  
그것을 해결하는 과정에서 nvm을 설치하여 npm과 node버전을 최신버전으로 설치하였는데,  
그러고 났더니 난 오류같다. 아래의 번역글에도 서두부분에 나온다.

<br><br>
To solve the "error:0308010C:digital envelope routines::unsupported", set the NODE_OPTIONS environment variable to --openssl-legacy-provider when running your development server, or change your Node.js version to the LTS by issuing the nvm install --lts command.

<br>
개발 서버를 러닝할 때 또는 nvm install --lts 명령 사안에 의해 Node.js 버전을 LTS 버전으로 바꾸었을 때, 에러 "error:0308010C:digital envelope routines::unsupported"를 해결하기 위해, NODE_OPTIONS 환경 변수를 --openssl-legacy-provider에 설정해야 한다.  
<br><br>
The first thing you should try is set the NODE_OPTIONS environment variable to --openssl-legacy-provider.
<br>
NODE_OPTIONS 환경 변수를 --openssl-legacy-provider에 설정하는 것이 가장 먼저 해야할 일이다.

<br><br>
터미널을 열고 아래의 명령어를 입력한다. <br>

# 👇️ macOS, Linux 또는 Windows Git Bash 일 때는 아래와 같이 <br>

export NODE_OPTIONS=--openssl-legacy-provider
<br><br>

# 👇️ Windows CMD 일때는 아래와 같이

set NODE_OPTIONS=--openssl-legacy-provider

<br><br>
Alternatively, you can add the --openssl-legacy-provider flag when issuing the command in your package.json file.
<br>
또는 package.json파일에서 명령을 실행할 때 --openssl-legacy-provider 플래그를 추가할 수 있다.

<br><br>

## package.json파일

<br><br>

```json
{
  "scripts": {
    "start": "react-scripts --openssl-legacy-provider start"
  }
}
```

<br><br>
If that doesn't help, try to set the NODE_OPTIONS environment variable right before issuing the command.

<br><br>
만약 해결되지 않는다면, NODE_OPTIONS 환경변수를 명령실행 바로 이전에 입력한다.
<br><br>
Make sure to update the command after the environment variable depending on your use case.

<br><br>

## package.json파일

<br><br>

```json
{
  "scripts": {
    "dev": "NODE_OPTIONS=--openssl-legacy-provider next dev",
    "build": "NODE_OPTIONS=--openssl-legacy-provider next build && next export"
  }
}
```

<br><br>
더 있지만 ubuntu이기 때문에 여기까지 하고 해결이 되었으므로 번역을 멈춤...

<br><br>
<br><br>

# 추가사항 (2022.11.12)

<br><br>
이 오류가 계속 되는것을 확인했다.  
계속 일시적으로 설정하지 않기 위해  
환경변수를 터미널에서 설정해주면 된다.  
내가 쓰는 터미널은 zsh이므로  
<br><br>
`sudo vi ~/.zshrc`
<br><br>
을 통해 설정파일로 들어간다.  
거기에 맨 마지막 부분에 커서를 내려 i(수정)를 누르고  
export NODE_OPTIONS=--openssl-legacy-provider  
한뒤, esc, `:wq`를 하면 설정이 완료된다.  
이제 재시작할 때마다 설정해야하는 번거로움을 피할 수 있다.
<br><br>
그런데 이렇게 하고 재시작한 뒤 원래 해오던 대로 터미널을 열어 code명령어로  
vs code를 실행하려고 했지만

```
-openssl-legacy-provider is not allowed in NODE_OPTIONS
```

라는 오류와 함께 실행되지 않았다.  
<br><br>
그래서 환경변수 설정한 것을 다시 지우고  
지금 내 컴퓨터의 npm 버전이 19버전은 lts버전, 즉 안정적인 버전이 아니기 때문에  
발생하는 오류의 일종이 될 가능성이 있다하여  
nvm으로 버전관리하여 lts버전인 18.12로 다운그레이드 하였다.  
그랬더니 환경변수 설정 없이도 정상 작동 하였다.

<br><br>
<br><br>

# 추가 오류사항 (2022.11.13)

<br><br>
위의 오류가 또 발생하였다. 지난번 번역 사이트에서 본 결과,  
node.js 17버전 이상일 때 사용중인 모듈 중 하나 혹은 일부가 OpenSSL 3버전 규격에  
맞지 않아 발생하는 에러였다.  
그 원인은 react-scripts의 버전이 낮아서 생기는 경우가 많았다.  
나의 react-scripts 버전은 5였는데
<br><br>
`npm audit fix --force`  
<br><br>
를 하고 npm start를 해서 보았더니 잘 작동하였다.  
<br><br>
그러나 찾아본 결과
<br><br>

`https://codingapple.com/forums/topic/npm-audit-fix-%ec%9d%98%ea%b2%bd%ec%9a%b0/`
<br><br>
코딩 애플에서는

```
작동에 문제없으면 건드리지 않는게 좋습니다

npm audit fix 하면 오히려 프로젝트가 망가지는 경우가 있어서
그냥 라이브러리들 업데이트되기까지 기다리거나 하면 자연스럽게 해결됩니다
```

라고 한다.  
하지만 작동에 이상이 있어서 어쩔수 없는 자구책으로 구동한 것인데,  
지금으로선 다른 방법이 없어보이는 것 같다.  
다른 문제가 발생하면 다시 또 찾아봐야 할 것 같다.
