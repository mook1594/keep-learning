#### [GO TO BACK](../README.md)

# 3. 가상 서버 사용하기: EC2
> 리눅스 가상 서버 실행하기  
> SSH를 이용하여 가상 서버 원격 제어하기  
> 가상 서버 모니터링 및 디버깅하기  
> 가상 서버 비용 절감하기

### 3.1 가상 서버 둘러보기
- 물리적 서버: 호스트 서버
- 하이퍼바이저: 하드웨어 요청을 스케줄링 하고 격리
- 게스트: 호스트 서버 위에서 실행되는 가상 서버

##### 가상 서버에 로그인 하려면 키가 필요함
- aws console에서 ec2 서비스
- [Key Pairs]를 선택
- Key pair name 입력 후 생성
- 다운로드 폴더에서 `chmod400 mykey.pem` 권한 변경

##### pem 파일을 ppk 파일로 변환
1. PuTTYgen을 실행한다. 
2. `Type of key to generate` 에서 `SSH-2 RSA`를 선택
3. `Load` 버튼 클릭
4. PuTTYgen은 .pkk 파일만 표시하므로 파일형식을 모든파일로 변경
5. mykey.pem 파일을 선택하고 [열기]를 클릭
6. 대화 상자에서 [확인]을 클릭
7. `Key comment`를 `mykey`로 변경
8. `Save private key` 버튼을 클릭. 비밀번호 없이 키를 저장한다는 경고 무시

#### 가상 서버에 연결하기
##### 리눅스와 맥 OS
```shell script
ssh -i $PathToKey/mykey.pem ubuntu@$PublicIp
```
##### 윈도우
1. key.ppk 파일을 연다
2. PuTTY Pageant가 아이콘 작업 표시줄에 표시.
3. PuTTY를 시작. 공인 IP 주소를 입렭하고 [Open] 클릭

### 3.2 가상 서버 모니터링 및 디버깅
#### 가상 서버 로그 확인
1. aws 콘솔에서 EC2 서비스를 열고 Instances를 선택
2. 가상 서머 목록에서 실행중인 가상서버가 있는 행을 클릭하여 선택
3. `Actions` 메뉴에서 `Instnace Settings > Get System Log` 선택
##### 가상 서버 부하 모니터링
1. aws 콘솔에서 EC2 서비스를 열고 Instances를 선택 
2. 가상 서버 목록에서 실행중인 가상서버가 있는 행을 클릭하여 선택
3. 오른쪽 아래에서 `Monitoring` 탭을 선택
4. Network In 차트를 클릭하여 세부 사항을 확인

### 3.3 가상 서버 종료하기
- 시작
- 중지 (임시 중지)
- 재부팅
- 종료 (삭제)

### 3.4 가상 서버 크기 변경
- 서버 확인
```shell script
$ cat /proc/cpuinfo
$ free -m
```

### 3.5 다른 데이터센터에서 가상 서버 시작하기
### 3.6 공인 IP 주소 할당하기
- 일래스틱 IP 주소: 고정 IP 주소를 할당
##### 할당 방법
1. 관리 콘솔에서 EC2 이동
2. 하위 메뉴에서 `Elastic IPs`를 선택 
3. `Allocate new address`를 클릭하여 공인 IP 주소를 할당
##### 가상 서버와 공인 IP 연결
1. 공인 IP 주소를 선택하고 `Actions` 메뉴에서 `Associate address`를 선택
2. `Instance`필드에 가상 서버의 인스턴스 ID를 입력.
3. `Associate`를 클릭

### 3.7 가상 서버에 네트워크 인터페이스 추가
1. 관리 콘솔에서 EC2 이동
2. 하위메뉴에서 `Network Interfaces`를 선택
3. `Create Network Interface`를 클릭하면 대화 상자가 열림
4. `Description`에 `2nd interface`를 입력
5. 새로운 네트워크 인터페이스의 서브넷으로 가상 서브넷을 섯ㄴ택
6. `Privatte IP` 주소는 그대로 둔다.
7. `Security groups`는 앞서 만든 webserver를 선택
8. `Yes, Create`를 클릭한다.

##### 인터페이스 확인
- ssh 연결한 후 확인
```shell script
$ ipconfig
```

### 3.8 가상 서버 비용 최적화하기
- 스팟 인스턴스, 예약 인스턴스
