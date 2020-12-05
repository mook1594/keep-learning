#MongoDB

### 로컬 설치
##### [참고 블로그](https://medium.com/@gwakhyoeun/mac-os%EC%97%90%EC%84%9C-mongodb-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0-79e473cc9000)
#### 설치
```shell script
$ brew tap mongodb/brew
$ brew install mongodb-community@4.2
```
#### 데이터 저장 공간 마련 (에러)
```shell script
$ sudo mkdir -p /data/db
mkdir: /data/db: Read-only file system
```
#### 해결방법
````shell script
$ whoami
$ sudo mkdir -p /Users/[username]/data/db.
````
#### 서비스 실행
```shell script
$ brew services start mongodb-community@4.2
```
#### PATH 설정
```shell script
export MONGO_PATH=/usr/local/Cellar/mongodb-community@4.2/4.2.9
export PATH=$PATH:$MONGO_PATH
```
#### 실행확인
```shell script
mongo -version
```
#### 프롬프트 실행
```shell script
mongo
```
