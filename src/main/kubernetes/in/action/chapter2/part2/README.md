### [GO TO BACK](../README.md)

## 2. 쿠버네티스 클러스터 설치

: http://kubernetes.io
- kubeadm : http://github.com/kubernetes/kops

### Minikube를 활용한 단일 노드 쿠버네티스 클러스터 실행하기
- 가발용 단일노드 쿠버네티스
#### Minikube 설치
- [GITHUB 설치 방법](http://github.com/kubernetes/minikube)
- `$ curl -Lo minikube https://storage.googleapis.com/minikube/releases/`
- Minikube는 VirtualBox나 KVM을 통해 실행된 가상머신 내부에 실행하므로 둘중하나 설치
#### 쿠버네티스 클라이언트 설치
- kubectl: CLI 클라이언트
- `$ curl -LO https://storage.googleapis.com/kubernetes-release/release`
- 설치 확인 `$ kubectl cluster-info`

### 구글 쿠버네티스 엔진을 활용한 관리형 쿠버네티스 클러스터 사용하기
- 구글 쿠버네트스 엔진: GKE 클러스터
- [Quick Start](https://cloud.google.com/kubernetes-engine/docs/quickstart)
1. 구글 클라우드 플랫폼 콘솔 프로젝트 생성
2. 빌링 활성화
3. 쿠버네티스 엔진 API 활성화
4. 구글 클라우드 SDK 다운 설치
5. gcloud components. install kubectl 명령으로 kubectl 명령행 도구 설치
#### 노드 세개를 가진 쿠버네티스 클러스터 생성
```shell
$ gcloud container clusters create kubia --num-nodes 3
```
#### 클러스터 노드를 조회해 클러스터 동작 상태 확인
```shell
$ kubectl get nodes
```
#### 오브젝트 세부 정보 가져오기
```shell
$ kubectl describe node gke-kubia-84f6-node-0rrx
```

### kubectl의 alias와 명령줄 자동완성 설정하기
#### 별칭 설정하기
- ~/ .bashrc에 추가 `alias k=kubectl`
#### kubectl의 탭 완성 설정하기
- bash-completion 패키지를 설치하고 `$ source <(kubectl completion bash)` 실행
- 전체이름을 사용하지 않아도 되는 설정 `$ source <(kubectl completion bash | sed s/kubectl/k/g)`

