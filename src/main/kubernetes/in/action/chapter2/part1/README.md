### [GO TO BACK](../README.md)

## 1. 도커를 사용한 컨테이너 이미지 생성, 실행, 공유

### 도커 설치
[설치 링크](http://docs.docker.com/engine/installation/)

- busybox: 표준 UNIX 명령줄 도구를 합쳐 놓은 단일 실행파일
```shell script
$ docker run busybox echo "Hello world"
```
### 이미지를 위한 Dockerfile 생성
```text
FROM node:7
ADD app.js /app.js
ENTRYPOINT["node", "app.js"]
```
### 컨테이너 이미지 생성
- 이미지 빌드 `$ docker build -t kubia .`
- 도커는 디렉터리 내 Dockerfile을 보고 이미지를 빌드한다.
- 빌드 프로세스는 도커 클라리언트가 아닌 도커 데몬에서 수행됨.
1. 도커 클라이언트가 디렉터리의 콘텐츠를 데몬에 업로드.
2. 이미지가 아직 로컬에 저장돼 있지 않은 경우 도커가 도커 허브에서 이미지를 풀한다
3. 새로운 이미지를 빌드한다.

#### 컨테이너 이미지 실행
```shell
$ docker run --name kubia-container -p 8080:8080 -d kubia
$ docker ps
```
#### 컨테이너 상세 정보
```shell
$ docker inspect kubia-container
```
#### 실행 중인 컨테이너 내부
```shell
$ docker exec -it kubia-container bash
```
- -i: 표준 입력을 오픈 상태로 유지
- -t: 터미널 할당
#### 내부에서 컨테이너 탐색
```shell
$ ps aux
$ ps aux | grep app.js 
```
#### 컨테이너 중지와 삭제
```shell
$ docker stop kubia-container
$ docker rm kubia-container
```
### 이미지 레지스트리에 이미지 푸시
- 도커 허브에 푸시. Quay.io, Google Container Registry
- 도커 허브 ID를 생성해야한다.
#### 이미지 태그 지정
```shell
$ docker tag kubia luksa/kubia
```
#### 도커 허브에 이미지 푸시하기
```shell
$ docker push luksa/kubia
```
#### 다른 머신에서 이미지 실행하기
```shell
$ docker run -p 8080:8080 -d luksa/kubia
```

