### [GO TO BACK](../README.md)

## 3. 쿠버네티스에 첫 번째 애플리케이션 실행하기

### node.js 어플리케이션 구동하기
```shell
$ kubectl run kubia --image=luksa/kubia --port=8080 --generator=run/v1
```
- --image: 컨테이너 이미지
- -port: 쿠버네티스에 포트 수신 대기 사실
- --generator: 보통 사용하지 않음
#### 파드 소개
- 쿠버네티스는 개별 컨테이너를 직접 다루진 않음
- 파드: 컨테이너 그룹, 함께 배치된 다수의 컨테이너, 같은 워커노드에서 실행
#### 파드 조회
```shell
$ kubectl get pods
$ kubectl describe pod
```
#### 백그라운드에 일어난 동작 이해하기
1. 이미지를 빌드해 도커 허브에 푸시
2. kubectl 명령어를 실행하면 쿠버네티스의 API서버로 REST HTTP 요청 전달 클러스터에 새로운 레플리케이션컨트롤러 오브젝트를 생성.
3. 레플리케이션 컨트롤러는 새 파드를 생성하고 스케줄러에 의해 워커 노드 중 하나에 스케줄링 된다.
4. 워커 노드의 Kubelet은 파드가 스케줄링 됐다는 것을 보고 도커에게 레지스트리에서 특정 이미지를 풀 받도록 한다
5. 이미지를 다운로드한 후 도커는 컨테이너를 생성하고 실행

### 웹 애플리케이션에 접근하기
- 파드에 접근하려면 로드밸런서의 퍼블릭 IP를 통해 파드에 연결
#### 서비스 오브젝트 생성하기
- 레플리케이션 컨트롤러 노출
```shell
$ kubectl expose rc kubia --type=LoadBalancer --name kubia-http
```
#### 서비스 조회하기
```shell
$ kubectl get services
$ kubectl get svc
```
#### External IP를 이용해 서비스 접근하기
```shell
$ curl {external IP}
```
- Minikube를 사용하면 minikube service kubia-http를 실행해 서비스에 접근 가능한 IP와 포트를 얻을 수 있다.

### 시스템의 논리적인 부분
#### 레플리케이션컨트롤러(RC), 파드(POD), 서비스가 서로 동작하는 방식 이해
- 컨테이너, 파드를 직접 생성하지 않음
- kubectl run 명령을 수행하면 rc를 생성하고 rc가 파드를 생성한다.
- 클러스터 외부에서 접근 하기위해 모든 파드를 단일 서비스로 노출하도록 명령.
#### 파드와 컨테이너
- 파드는 원하는 만큼 컨테이너를 갖는다. 
- 컨테이너 내부에는 Node.js 프로세스가 있고 포트에 바인딩 돼 HTTP요청을 기다린다.
- 파드는 자체 고유한 IP 주소와 호스트 이름을 갖는다.
#### 레플리케이션컨트롤러의 역할
- 하나의 파드 인스턴스를 실행하도록 지정
- 레플리케이션컨트롤러는 파드를 복제 실행
- 레플리케이션은 파드가 사라지면 새 파드 생성
#### 서비스가 필요한 이유
- 파드는 일시적이라 언제든 사라질 수 있다. 사라진 파드는 레플리케이션컨트롤러가 보강해준다.
- 새로운 파드는 새 IP주소를 할당받는다. 서비스는 파드의 IP변화에 상관없이 정적인 IP를 할당 받고 클라이언트에서 요청오면 아무 파드 하나로 연결해 요청을 처리하도록 한다.

### 애플리케이션 수평 확장
- 실행중인 애플리케이션은 레플리케이션컨트롤러에 의해 모니터링 되고 실행되며 서비스를 통해 외부에 노출됨.
```shell
$ kubectl get replicationcontrollers
```
- DESIRED: 레플리케이션컨트롤러가 유지해야할 파드의 레플리카 수
- CURRENT: 현재 실행 중인 파드 실제 수
#### 의도하는 레플리카 수 늘리기
```shell
$ kubectl scale rc kubia --replicas=3
```
- 쿠버네티스는 어떤 액션을 수행해야 하는지 정확하게 알려줌
- 시스템의 의도하는 상태(desired state)를 변경
- 쿠버네티스가 현재 상태(current state)를 검사
- 의도한 상태로 조정(reconcile)
#### 스케일 아웃 결과 보기
```shell
$ kubectl get rc
$ kubectl get pods
```
#### 서비스 호출 시 모든 파드가 요청을 받는지 확인
- 클라이언트에서 서비스를 호출하면(curl) 무작위 파드를 호출한다.
- 하나 이상의 파드가 존재하면 서비스는 로드밸런스 역할을 한다.
#### 시스템의 새로운 상태 시각화

### 애플리케이션이 실행 중인 노드 검사하기
#### 파드를 조회할 때 파드 IP와 실행 중인 노드 표시하기
```shell
$ kubectl get pods -o wide
```
#### kubectl describe로 파드 세부 정보 살펴보기
```shell
$ kubectl describe pod kubia-hcaji
```

### 쿠버네티스 대시보드
#### GKE에서 쿠버네티스를 사용할 때 대시보드 접근하기
```shell
$ kubectl cluster-info | grep dashboard
```
```shell
$ gcloud container clusters describe kubia | grep -E "(username|password):"
```
#### Minikube를 사용할 때 대시보드 접근하기
```shell
$ minikube dashboard
```
