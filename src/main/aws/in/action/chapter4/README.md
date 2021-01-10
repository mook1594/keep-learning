#### [GO TO BACK](../README.md)

# 4. 인프라 프로그래밍: CLI, SDK, CloudFormation
> 코드와 인프라의 개념 이해하기
> 명령줄을 사용하여 가상 서버 시작하기
> Node.js용 자바스크립트 SDK를 사용ㅎ여 가상 서버 시작하기
> CloudFormation을 사용하여 가상 서버 시작하기

### 4.1 코드로 인프라 관리하기
#### 자동화 및 데브옵스
- DevOps: 개발과 운영을 가깝게.
- 코드를 커밋하면, 소스 코드가 자동으로 빌드되고 자동화된 테스트가 수행된다.
- 빌드가 테스트를 통화하면 자동으로 테스트 환경에 설치, 일부 통합 테스트가 구동됨
- 통합 테스트를 통과 후 변경 사항은 운영 쪽 프러덕션에 전파.
- 시스템을 모니터링하고 실시간으로 로그분석

#### 새로운 인프라 언어: JIML
##### 인프라의 구성
- 로드 밸런서(LB)
- 가상 서버
- 데이터베이스(DB)
- DNS 항목
- 콘텐트 전송 네트워크(CDN)
- 정적 파일을 담을 버킷
##### JIML로 기술한 인프라
```json
{
  "region": "us-east-1",
  "resources": [{
    "type": "loadbalancer",
    "id": "LB",
    "config": {
      "server": {
        "cpu": 2,
        "ram": 4,
        "os": "ubuntu",
        "waitFor": "$DB"
      },
      "servers": 2
    }
  }, {
    "type": "cdn",
    "id": "CDN",
    "config": {
      "defaultSource": "$LB",
      "sources": [{
        "path": "/static/*",
        "source": "$BUCKET"
      }]
    }
  }, {
    "type": "database",
    "id": "DB",
    "config": {
      "password": "***",
      "engine": "MySQL"
    }
  }, {
    "type": "dns",
    "config": {
      "from": "www.mydomain.com",
      "to": "$CDN"
    }
  }, {
    "type": "bucket",
    "id": "BUCKET"
  }]
}
```
##### JIML 인터프리터가 생성한 종속성
- DNS --`참조`--> CDN --`참조`--> BUCKET
                     --`참조`--> LB --`참조`--> Server --`대기`--> DB  
                                   --`참조`--> Server  
##### 의사 언어 명령 흐름
```json5
$DB = database create {"password": "***", "engine": "MySql"}
$BUCKET = bucket create {}

await $DB
$SERVER1 = server create {"cpu": 2, "ram": 4, "os": "ubuntu"}
$SERVER2 = server create {"cpu": 2, "ram": 4, "os": "ubuntu"}

await [$SERVER1, $SERVER2]
$LB = loadbalancer create {"servers":  [$_SERVER1, $_SERVER2]}

await [$LB, $BUCKET]
$CDN = cdn create {...}

await $CDN
$DNS = dns create {...}

await $DNS
```

### 4.2 명령줄 인터페이스 사용
#### CLI 설치
- https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html
- http://mng.bz/N8L6 에 접속하여 설치
##### 리눅스와 맥OS
```shell script
$ curl "https://bootstrap.pypa.io/get-pip.py" -o "get-pip.py"
$ sudo python get-pip.py
```
```shell script
$ sudo pip install awscli
```
##### 윈도우
1. http://aws.amazon.com/cli 에서 AWS CLI 설치 프로그램 다운
2. 내려받은 설치 프로그램 실행 후 설치
3. [시작] 메뉴에서 PowerShell 항목을 검색하고 `관리자 권한으로 실행`
4. 파워셸에서 `Set-ExecutionPolicy Unrestricted`
5. 다시 파워셸 시작
6. aws --version을 실행하여 동작하는지 확인

#### CLI 구성하기
##### 계정 그룹 생성 및 계정 생성
- 관리자 루트 AWS 계정을 사용하지 않는걸 권장
- 콘솔에서 IAM 선택
- 왼쪽 `Groups` 선택 `[Create New Group]`을 클릭
- `Group Name` 입력 후 `[Next Step]` 클릭
- `AdministratorAccess` 정책 부여 `[Next Step]` 클릭
- 왼쪽 `Users` 선택 `[Add user]` 클릭
- `User Name` 입력 후 `Programmatic access`를 선택 후 다음단계
    * Programmatic access: CLI 접근
    * AWS Management Console access: 콘솔 접근
- 권한부여 `Add user to group` 선택 후 생성한 그룹 선택
    * Copy permission from existing user: 다른 사용자 권한 복사
    * Attach existing policies directly: 정책 사용자 부여
- `[Create User] 클릭으로 유저 생성`
- 보안자격증명을 확인 할 수 있으며 내려받을 수 있는 마지막 기회. `Download.csv` 받기
##### aws config 변경
- AWS access key ID
- AWS secret access key
- Default region name
- Default output format: (json)
```shell script
$ aws configure
AWS Access Key ID [None]: ALKDFJLWGNLFKSDJF
AWS Secret Access Key [None]: SSKIslkdgjwELIJsldweigls4slkg
Default region name [None]: us-east-1
Default ouput format [None]: json
```
##### CLI 동작 테스트
```shell script
$ aws ec2 describe-regions
```
##### CLI 사용
```shell script
$ aws ec2 describe-regions-instances --filters "Name=instance-type,Values=t2.micro"
```
```shell script
$ aws <service> <action> [--key value ...]
```
- aws help: 이용할 수 있는 모든 서비스를 나열
- aws <service> help: 특정 서비스에서 사용할 수 있는 액션 목록
- aws <service> <action> help: 특정 서비스 액션에서 사용할 수 있는 옵션 목록을 나열

#### CLI를 이용한 서버 생성 및 종료
##### 리눅스와 맥 OS
```shell script
#!/bin/bash -e # 명령이 실패하면 스크립트 중지
AMIID=$(aws ec2 describe-images --filters "Name=description, Values=Amazon Linux AMI 2014.03.? x86_64 HVM GP2" -- query "Images[0].ImageId" --output text)
VPCID=$(aws ec2 describe-vpcs --filters "Name=isDefault, Values=true" --query "Vpcs[0].VpcId" --output text)
SUBNETID=$(aws ec2 describe-subnets --filters "Name=vpc-id, Values=$VPCID" --query "Subnets[0].SubnetId" --output text)
SGID=$(aws ec2 create-security-group --group-name mysecuritygroup --description "My security group" --vpc-id $VPCID --output text)
aws ec2 authorize-security-group-ingress --group-id $SGID --protocol tcp --port 22 --cidr 0.0.0.0/0 # 들어오는 SSH 연결 허용
INSTANCEID=$(aws ec2 run-instances --image-id $AMIID --key-name-mykey --instance-type t2.micro --security-group-ids $SGID --subnet-id $SUBNETID --query "Instances[0].InstanceId" --output text)
echo "waiting for $INSTANCEID ..."
aws ec2 wait instance-running --instance-ids $INSTANCEID # 서버 시작할때까지 대기
PUBLICNAME=$(aws ec2 describe-instances --instance-ids $INSTANCEID --query "Reservations[0].Instances[0].PublicDnsName" --output text)
echo "$INSTANCEID is accepting SSH connections under $PUBLICNAME"
echo "ssh -i mykey.pem ec2-user@$PUBLICNAME"
read -p "Press [Enter] key to terminate $INSTANCEID ..."
aws ec2 terminate-instances --instance-ids $INSTANCEID ## 서버 종
echo "terminating $INSTANCEID ..."
aws ec2 wait instance-terminated --instance-ids $INSTANCEID # 서버가 종료될 때 까지 대기
aws ec2 delete-security-group --group-id $SGID # 보안 그룹 삭제
```
##### 윈도우
```shell script
$ErrorActionPreference = "Stop" # 명령이 실패하면 스크립트 중지
$AMIID=aws ec2 describe-images --filters "Name=description, Values=Amazon Linux AMI 2015.03.? x86_64 HVM GP2" --query "Images[0].ImageId" --output text
$VPCID=aws ec2 describe-vpcs --filters "Name=isDefault, Values=true" --query "Vpcs[0].VpcId" --output text
$SUBNETID=aws ec2 describe-subnets --filters "Name=vpc-id, Values=$VPCID" --query "Subnets[0].SubnetId" --output text
$SGID=aws create-security-group --group-name mysecuritygroup --description "My security group" --vpc-id $VPCID --output text
aws ec2 authorize-security-group-ingress --group-id $SGID --protocol tcp --port 22 --cidr 0.0.0.0/0
$INSTANCEID=aws ec2 run-instances --image-id $AMIID --key-name mykey --instance-type t2.micro --security-group-ids $SGID --subnet-id $SUBNETID --query "Instances[0].InstanceId" --output text
Write-Host "waiting for $INSTANCEID"
aws ec2 wait instance-running --instance-ids $INSTANCEID
$PUBLICNAME=aws ec2 describe-instances --instance-ids $INSTANCEID --query "Reservation[0].Instance[0].PublicDnsName" --output text
Write-Host "$INSTANCEID is accepting SSH under $PUBLICNAME"
Write-Host "coonnect to $PUBLICNAME via SSH as user ec2-user"
Write-Host "Press[Enter] key too terminate $INSTANCEID..."
Read-Host
aws ec2 terminate-instances --instance-ids $INSTANCEID
Write-Host "terminating $INSTANCEID"
aws ec2 wait instance-terminated --instance-ids $INSTANCEID
aws ec2 delete-security-group --group-id $SGID
```

##### 스크립트를 이용해야하는가?
- 인프라 생성을 자동화 하므로써 배포 파이프라인 자동화 개선

### 4.3 SDK로 프로그래밍하기
