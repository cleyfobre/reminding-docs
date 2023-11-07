## Authentication and Authorization

### 로그인 완료 후 토큰을 응답
 - 로그인이 정상적으로 완료되었다는 건 인증이 완료되었음을 의미(Authentication)
 - 로그인 완료 후 토큰을 응답메시지로 보냄
 - 유저는 토큰을 가지고 서버에게 요청할 수 있음(Header.Authorization=Bearer Token)

### 토큰의 유효성 검사
 - 토큰을 가지고 있는 요청에 대해 유효성을 검사함(Authorization)
 - 권한에 맞는 요청인지와 유효시간 범위 안에 있는 토큰인지 검사
 - 권한에 맞는 요청이면 적절한 응답을 주고 안되면 권한 없음을 응답함(403)
