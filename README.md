﻿# User_Authentification_Nodejs_JWT

## 생각해볼 만한 것
If you're building your own authentication system, it's a really good idea to include a flag in your payloads, to indicate whether that token was generated by authenticating with user credentials, or by using a refresh token. You can use this flag to authorize sensitive operations, such as changing your password or making payments - so if the user didn't log in recently, you can prompt them to log in again for sensitive operations. I would say this is a must for most applications.

## 사용한 라이브러리
access token 만드는데 crypto library 사용
> require('crypto').randomBytes(64).toString('hex')\
> 나머지는 package.json 참고

- **server**.**js**, **server2**.**js**- 각각 3000번, 3001번으로 설정 \
같은 ACCESS_TOKEN_SECRET을 공유하는 2개의 서로 다른 서버

- **authServer**.**js**: authServer.js에서는 TOKEN create, deletion, refreshment만 처리- 4000번으로 설정\
**workServer**.**js**: //!workServer는 post 생성, 삭제 등 api요청과 관련된 일만 처리(토큰 검증은 함) - 4001번으로 설정

## refresh token이 필요한 이유

- accessToken에 expire date X & refresh 토큰 X\
누가 accessToken 탈취함 + api주소까지 알아냄\
=> 영원히 그 유저처럼 request 날릴 수 있음

- accessToken에 expire date O & refresh 토큰 O\
누가 accessToken 탈취함 + api주소까지 알아냄\
=> 하지만 곧 expire되기 때문에 유저는 refresh token을 가지고 새로운 토큰을 받아야 함 => 곧 탈취한 계정에 대한 접근이 제한됨

- 근데 만약 refresh 토큰 탈취 당함 + 누가 내 토큰 refresh 해버림\
=> invalidate refresh token 해야함 == logout route == delete the refresh token == 유효한 refresh token 목록에서 사라져서 탈취당한 refresh token 의미 없어짐

- reasons!
1. can invalidate users that should not have access
2. if you have lot about authorization, can remove all authentification and authorization code from *normal server* to *auth server*

### api
>**login(POST)**: access toekn 발급(유효기간 있음)\
**token(POST)**: access token이 만료됐을 때 refresh token으로 새로운 accessToken 발급, 재로그인(login(POST) 할 필요 없이 최신화된 acces token으로 workServer 작업 가능)... 자동 로그인이라고생각해도 되는건가?\
**logout(DELETE)**: refresh token 무효화 -> 다시 login(POST) 해야함
