### [GO TO BACK](../../../README.md)

# WEBSOCKET

### 웹 소켓 이전의 비슷한 기술
- Polling (불필요한 요청이 많음)
- Long Polling (서버 대기시간을 늘려서 해결했지만 클라이언트 요청이 불편, 양이 많아지면 polling과 유사)
- Streaming (클라이언트 요청이 불편)

### 웹 소켓 동작 방법
#### 핸드 쉐이킹
: HTTP 통신으로 
- request
```
GET /chat HTTP/1.1
Host:xxxxxxx
Upgrade: websocket (현재프로토콜에서 다른 프로토콜로 업그레이드)
Connection: Upgrade (connection 설정)
        Sec-WebSocket-Key: 신원 인증
        Origin:xxxx
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
```
- response
```
HTTP/1.1 101 Switching Protocols
Upgrade:websocket
Connection:Upgrade
Set-WebSocket-Accept: Sec-WebSocket-Key값으로 계산한 값 클라이언트 값과 일치해야만 연결
```
#### 데이터 전송
: 프로토콜 ws로 변경
- message: 단위 frame이 모여 구성
- frame: 가장 작은 단위, 작은해더 + payload로 구성
- 프레임 헤더 구조
```
0x00 <UTF8 payload> 0xff
END, RSV1, RSV2, RSV3, Opcode, MASK, Length
```
- Opcode 종류
    - Continue: 전체 메시지 일부
    - Text: UTF-8 텍스트
    - Binary: 이진데이터
    - Close: Close 핸드쉐이크 시
#### 연결 종료 Close 프레임

### 웹 소켓 프로토콜 특징
- 최초 접속에서만 http 프로토콜로 handshaking을 하기 때문에 http header 사용
- 웹 소켓을 위한 별도 포트는 없고 80, 445 이용
- 프레임으로 구성된 메시지라는 논리 단위로 송수신
- 메시지에 포함될 수 있는 교환 가능 메시지는 텍스트와 바이너리 뿐

### 웹 소켓 한계
- HTML5 이전 기술은 사용이 힘듬. 
    - Socket.io, SockJS 웹 소켓처럼 사용할 수 있도록 도와주는 기술
    - Javascript를 사용하여 시간 웹을 구현실
    - WebSocket, FlashSocket, Ajax Long Polling, Ajax Multi part Streaming, Iframe, JSNP Polling을 하나의 API로 추상화
    - 브라우저와 웹 서버 종류와 버전을 파악하여 가장 적합한 기술을 선택하는 방식 
- 문자열만 주고 받을 수 있음, 문자열 해독은 어플리케이션이
    - WebSocket은 형식이 정해져있지 않기때문에 앱에서 해석이 쉽지 않음
    - sub protocol을 사용 => STOMP(Simple Text Oriented Message Protocol)
    - STOMP는 채팅 통신을 하기 위한 형식 정의
    - HTTP와 유사하게 정의, 일반적으로 웹 소켓 위에서 사용
    ```
        COMMAND (CONNECT, SEND, SUBSCRIBE, DISCONNECT)
        header1:value1
        header2:value2
  
        BodyBodyBody^@
    ```


