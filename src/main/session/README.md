#### [GO TO BACK](../README.md)

# Session

### Load balancer 가 있을때 로컬 세션의 문제점
- A 서버에서 세션에 값을 저장해두고 SessionID를 받았는데 C서버로 가서 SessionID로 조회하면 해당 데이터가 없다.

### 해결책?
- sticky session


### Sticky Session?
- 첫 request에 대한 응답을 준 서버에 계속 요청을 보내는것

##### 어떻게 가능하지?
- 일반적으로 Cookie를 사용하거나, 클라이언트의 IP를 tracking

##### 요청을 한서버로 고정해 놔서 단점은?
- 로드밸런싱이 잘 동작 하지 않을 수 있다.
- 특정 서버만 과부하가 올 수 있다.
- 특정 서버 Fail시 Fail이 안난 서버로 재연결 하는데, 이때 세션 데이터가 소실된다.

##### 이걸 해결하려면?
- Session Clustering

### Session Clustering
- 여려 WAS의 세션을 하나의 세션으로 관리하는 것

##### 단점은?
- 새로운 서버가 하나 뜰 때마다 기존 WAS에 새로운 서버의 IP/Port를 입력해서 클러스터링 해줘야한다.

##### 이것도 불편하다. 해결점은?
- Session Server 를 분리한다!

### Session Server 를 분리한다!
- Redis를 활용하여 Session 서버를 별도로 둔다. 그러면 위 두가지 단점이 사라진다.


