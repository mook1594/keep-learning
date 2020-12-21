#### [GO TO BACK](../../../README.md)
# 인증과 인가

### 인증과 인가에 대한 접근
- 팬미팅에 참석했을때, 입장전 팬클럽 회원인지 회원증, 신분증을 확인하고 입장한다. (인증)
- 입장후엔 정해진 장소만 이동할 수 있으며 Staff Only 혹은 대기실에는 입장 불가하다. (인가)

### 인증이란
- 서비스에서 식별가능한 정보로 등록된 유저인지 신원을 증명하는 것

### 인가란
- 증명된 사용자가 특정 자원을 사용할 수 있는지 확인하는 것

### 프로그램에서 인증을 하는 방법 
#### 인증하기(Request Header)
- 클라이언트에서 request header에 정보를 담아 인증한다
- request header에는 [Authorization: 토큰정보]를 추가하여 인증한다.
ex) http://user:1q2w3e!@www.hompage.com/login
ex) Authorization: Basic dG9uZXk6cGfzc3dvcmQ

#### 인증 유지하기 (Browser)
- RequestHeader를 사용하면 매번 인증을 거쳐야 한다.
- 브라우저에 쿠키, storage(local, session)를 사용하여 브라우저에 저장. 요청할때마다 같이 첨부해서 보낸다.
- 단점 : 해커가 조작하기 쉽다. (보안 취약)

#### 안전하게 인가하기 (Server)
- 세션을 사용하여 데이터는 서버에, 매핑되는 세션키 값을 클라이언트가 가지고 있는다
- Response Header에 [Set-Cookie: JESSIONID=aFa28X1ka1d] 를 전송.
- 키가 탈취되어도 정보를 알 수 없다.
- 단점: 서버인스턴스가 증가하여 로드밸런서가 서버 접속을 관리할 때 세션 로드에 문제가 됨.
(서버 하나하나 자체에 세션을 관리하고 있어서 문제가 됨)
⇒ 세션 storage를 이용해서 문제를 피함.  ⇒ 서버 증대로 세션 storage가 포화됨.

#### 효율적으로 인가하기 (Token)
- 위 단점을 해소하기 위해 request, response내에 사용자 식별정보를 담아보자
토큰을 사용해서 http 요청 stateless를 statable 하게 바꿔보자.
- secret키로 JWT 토큰을 만들고 secret를 사용하여 JWT를 인증한다.
토큰은 해독하기 쉬워 민감정보는 담지 않는다.
- respnse에 [SET-Cookie: access=dkjsdf.salkfjdf.slkfjsdf]
- 장점: 서버가 여러대 여도 별도의 세션스토리지가 필요없고 각 token으로 확인가능
- 단점: 해킹에 대한 문제, accessToken을 탈취당하면 문제가 됨.
⇒ 만료기간을 정해두어 처리. (refreshToken을 사용하여 토큰을 갱신)
- 토큰으로 상태관리를 하기에 따로 세션이 필요없음,

#### 다른 채널을 통해 인증하기 (OAuth)
- 생활코딩 이고잉 OAuth 강의
- 다른 웹사이트 상의 인증을 하기위한 표준 절차

### 인증인가에 대한 추가 고민사항
- 보안 관련
- JWT
- OAuth
- 인증 서버
- HttpOnly 서버 → 클라이언트 정보인데 스토리지에 저장된 정보를 함부로 접근할 수 없음?
- Sliding Session
- Refresh Token
- SSL / TLS1.3 (접근하기 쉽고 가성비 좋은 보안방법) - https
- 해킹방법

### 보안과 사용자의 편의성 밸런스...    
- 보안에 치중하면 사용자가 불편
- 사용자가 편하면 보안이 약할 수 있다.
