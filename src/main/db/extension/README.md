#### [GO TO BACK](../README.md)

# 데이터베이스 확장

### Clustering 
- 서버가 죽을때를 방지하는 것
#### Clustering(Active - Active) 구성
- Database Server Active
- Database Server Active
- Database Storage
##### 장점
- 시스템 전체가 정지하는 것을 방지할 수 있다
- 성능이 향상된다
##### 단점
- 병목되는 상황에 대한 고려가 필요하다
- 비용이 비싸다

#### Clustering(Active - StandBy) 구성
- Database Server Active
- Database Server Stand by
- Database Storage
##### 장점
- 비용이 저렴하다
##### 단점
- Active가 죽었을때 전환시 시간이 오래 걸린다.

### Replication
- 데이터 손실 방지를 위한 것
#### 단순 백업
- Master: Database Server, Database Storage
- Slave: Database Server, Database Storage
- Master에 CRUD가 일어나고, Slave에 데이터 동기화 한다
#### 부하 분산
- Master에 CRUD가 일어나고, Slave에 데이터 동기화 한다 
- 저장용도만으로 아까움으로 Slave에도 R할수 있게 하여 부하 분산

### Sharding
- 데이터가 많아 검색이 느린데 빠르게 할 수 있는 방법?
- 테이블을 ROW 단위로 나누어 검색
#### Shard key
- 나눠진 Shard 중 어떤 Shard를 선택할지 결정하는 키
- Shard key 결정 방식에 따라 Sharding 방법이 나뉨
##### Hash Sharding
Hash 함수로 샤딩키를 나누어 캐싱. key-value 구조에 적합
##### 장점
- 구현이 간단하다
##### 단점
- 확장성이 떨어진다
- 키를 기준으로 공간을 나누었기 떄문에 공간에 대한 효율을 고려하지 않는다.

##### Dynamic Sharding
Locator service에 shard키를 저장하여 사용.  key-value 구조에 적합
##### 장점
- 확장에 유연하다
##### 단점
- 데이터를 재배치시 Locator Service도 동기화 해야한다
- Locator에 의존적이라 Locator가 문제 생기면 DB도 문제가 생긴다

##### Entity Group
관계형에 적합한 sharding. 연관된걸 같은 shard 내에 관리하도록함
##### 장점
- 단일 Shard내에서 쿼리가 효율적이다
- 단일 Shard내에서 강한 응집도를 가진다
##### 단점
- 다른 Shard의 Entity와 연관이 되는 경우 비효율적이다

