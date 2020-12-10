# Docker

### 도커 소개
##### 도커의 정의
- 도커는 OS 수준 가상화를 통해 프트웨어를 컨소테이너라는 패키지로 제공하는 서비스형 PaaS
- 컨테이너 유닛으로 패키징하여 구축, 테스트 배포할 수 있는 서비스
- 도커는 도커 엔진이며 이는 컨테이너를 생성하고 관리하는 주체.
##### 도커의 장점
- 환경에 따른 제약이 감소한다
- 신속한 구현 및 확장이 가능하다
- 동일한 하드웨어에서 더 많은 워크로드를 실행할 수 있다.
##### Docker VS VM
- 도커는 hypervisor 없이 kernel 위에 도커엔진에서 바로 실행되어 가볍다.
##### 도커를 사용해야하나?
- 반드시 도커를 사용해야하는건 아니지만 컨테이너 개념은 알아야 도움이 된다.
##### 도커 이미지와 컨테이너 
- 도커 이미지는 어떤 틀
- 틀안에 여러개의 컨테이너를 구성하여 찍어

### 도커 개발 환경 설정냄
##### CGP?
- Google에서 만든 Cloud Platform
- 가난한 개발자한테는 GCP free tier
- [링크](https://medium.com/@jwlee98/gcp-가난한-개ㅐ발잘를-위)
- [깃헙](https://github.com/seunghokimj/docker-hands-on)

```shell script
$ sudo apt-get remove docker docker-engine docker.io containerd runc
$ sudo apt-get update
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$ sudo apt-key fingerprint 0EBFCD88
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
$ sudo apt-get update
$ sudo apt-get install docker-ce docker-ce-cli containerd.io
$ sudo docker -v
Docker version 19.03.13, build 4484c46d9d
```
##### [맥 도커 설치](https://docs.docker.com/docker-for-mac/install/)
##### [윈도 도커 설치](https://docs.docker.com/docker-for-windows/install/)

### 도커 컨테이너 실습
##### 도커 접근 권한 설정
```shell script
$ sudo groupadd docker
$ sudo usermod -aG docker $USER
```
##### 도커 컨테이너 생성 (실행)
- docker container run [OPTION] IMAGE [COMMAND] [ARG...]
- docker container --help로 확인 가능
- docker "hello word" 실행 
```shell script
$ docker container ps
$ docker container run hello-world
```

##### 도커 리눅스 컨테이너 생성
```shell script
$ docker container run ubuntu
```
- 컨테이너가 실행되지만 할일이 없어 바로 종료됨

##### 도커를 실행하여 컨테이너 안으로 들어감 
```shell script
$ docker container run -it ubuntu bash
$# touch testfile
$# ls
$# rm -rf testfile
$# exit
```
- 우분투를 만들고 bash를 실행해 해당 환경에 들어옴
- 컨테이너 종료 exit (Ctrl + D), 임시 종료 (Ctrl + P,Q)

##### 버전달린 우분투 생성
```shell script
$ docker container run -it ubuntu:18.04 bash
$# uname - a
```

##### Detached(background) vs Foreground
- 옵션이 없으면 default foreground
- run 명령어에 -d 옵션을 주면 background 모드 실행
```shell script
$ docker container run -d nginx
```

##### 떠있는 도커를 재접속
- $ docker container attach [CONTAINER_ID]
```shell script
$ docker container run -it ubuntu:18.04 bash 
$ docker container ps
$ docker container attach 44fjg23gjw
```

### 도커 이미지 실습
##### 컨테이너와 머신 사이에 포트 연결
```shell script
$ docker containter run -p 80:80 -d nginx
```

### Dockerfile

### 간단한 웹 어플리케이션 배포
