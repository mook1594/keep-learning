#### [GO TO BACK](../README.md)
 
# 5. 배포 자동화: CloudFormation, ElasticBeanstalk, OpsWorks
> 서버 시작시 일반적인 애플리케이션을 배포하는 스크립트 실행하기
> AWS ELB를 이용하여 일반적인 웹 어플리케이션 배포하기
> AWS OpsWorks를 이용하여 다중 계층 애플리케이션 배포하기
> AWS에 사용할 수 있는 다양한 서비스 비교

### 5.1 유연한 클라우드 환경에 애플리케이션 배포하기
- 서버 증가로 수동배포가 점차 불가능, 실수로 인해 실패할 위험이 커짐

### 5.2 CloudFormation을 활용하여 서버 시작시 스크립트 실행하기
#### 서버 구동 시 스크립트를 실행하기 위해 사용자 데이터 사용하기
- 가상서버에 사용자 데이터 삽입
#### 가상 서버에 VPN 서버로 OpenSwan 배포하기
- 가상 서버의 OpenSwan을 사용하여 PC 트래픽 터널링 하기
```shell script
$ VpcId=$(aws ec2 describe-vpcs --query Vpcs[0].VpcId --output text)
$ SubnetId=$(aws ec2 describe-subnets --filters Name=vpc-id, Values=$VpcId --query Subnets[0].SubnetId --output text)
$ SharedSecret=$(openssl rand -base64 30)
$ Password=$(openssl rand -base64 30)
$ aws cloudformation create-stack --stack-name vpn --template-url https://s3.awazons.com/awsinaction/chapter5/vpn-cloudformation.json 
  --parameters ParameterKey=KeyName, ParameterValue=mykey
              ParameterKey=VPC, ParameterValue=$VpcId
              ParameterKey=Subnet, ParameterValue=$SubnetId
              ParameterKey=IPSecSharedSecret, ParameterValue=$SharedSecret
              ParameterKey=VPNUser, ParameterValue=vpn
              ParameterKey=VPNPassword, ParameterValue=$Password
$ aws cloudformation describe-stacks --stack-name vpn --query Stacks[0].Outputs 
```

##### CloudFormation 이용하여 사용자 데이터로 가상 서버 구동하기

