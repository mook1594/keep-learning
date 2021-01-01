### [GO TO BACK](../../../README.md)

# HTTPS
- 암호화를 위해서 클라이언트와 서버간의 암호화 알골리즘과 키를 교환
- handshake 과정
- HTTPS 인증서를 검증하는 Online Certificate Status Protocol 을 통해 인증서 및 인증 기관이 유효한지 판단

Multiple Connection
Persistent Connection

- handshake 진행 
- 비용이 큰 RSA알고리즘을 통해 통신하면 부하 발생
- 처음에만 RSA고, 과정을 마치면 대칭키로 통신
- 여기에 Keep-Alive를 이용하면 세션이 유지되니 암호화(위과정)이 줄어든다.
### HTTP 와 HTTPS
- HTTP Layer: HTTP / TCP / IP / 네트워크 인터페이스
- HTTPS Layer: HTTP / SSL or TLS / TCP / IP / 네트워크 인터페이스

### SSL 와 TLS
- 같다고 봐도 무방
- 실제로는 TLS 안에 SSL이 속해있는 개념

### 대칭키 와 공개키
- 대칭키: 키 하나로 암,복호화가 가능한 키
- 공개키: private, public 키 두개로 private이 암호화 한건 public이, public이 암호화한건 private이 복호화할 수 있다.

### HTTPS 동작원리

##### 인증서 보증 방법
- 웹브라우저가 서버에 접속하면 서버는 제일먼저 인증서를 제공
- 브라우저는 서버가 보내준 인증서를 발급한 CA가 자신이 갖고 있는 CA 리스트에 있는지 확인
- 리스트에 있으면 CA를 해당 CA의 공개키를 이용해서 인증서를 복호화.
- 인증서 복호화 성공은 인증서가 CA 비공개키에 의해서 암호화 된거고 인증됨.

##### SSL 동작 방법
- 실제 데이터의 암호화는 대칭키 방식
- 대칭키를 서로 공유하기 위한 암호화는 공개키 방식

#### SSL 통신 과정
- 악수(handshake) -> 데이터 전송 -> 세션 종료

##### handshake
- 프로토콜 버전 호환
- pre master secret 키 (대칭키) 생성 및 교환
- 신원 인증
- 채널 암호화를 위한 임시 세션키 생
