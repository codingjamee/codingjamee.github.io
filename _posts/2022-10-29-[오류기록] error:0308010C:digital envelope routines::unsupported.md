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
