#### [GO TO BACK](../README.md)

# Index

### 인덱스란?
- 빠르게 찾아갈 수 있는 주소록 같은 개념

### 인덱스를 왜 사용하는가?
- 빠르게 쿼리 검색을 하기 위함

### 어떤 컬럼을 인덱스로 잡는게 좋은가?
- 카디널리티가 높은 순 (구분을 잘지을 수 있는)
- 선택도가 낮을 수록 (전체 데이터에 비해서 검색양이 적을 경우)
- 활용도 높을 수록
- 중복도 

### 인덱스를 사용하면 무조건 좋은가?
- 아니다
- INSERT 성능이 떨어진다. 저장하면서 인덱스 순서대로 정렬 저장하기 때
- DELETE 는 사용하지 않는 처리만 할뿐 실제 지워지지 않는다.

### 인덱스 종류
#### B-TREE 인덱스 (Binary)
- 실시간으로 데이터 입력과 수정이 일아는 환경에 주로 사용 
- Root, Branch, Leaf Block
###### Unique Index
```sql
CREATE UNIQUE INDEX {인덱스명}
ON {테이블명}({컬럼명} ASC|DESC, {컬럼명} ASC|DESC)
```
###### Non Unique Index
```sql
CREATE INDEX {인덱스명}
ON {테이블명}({컬럼명} ASC|DESC, {컬럼명} ASC|DESC)
```
##### Function Based Index
```sql
CREATE INDEX {인덱스명}
ON {테이블명}(함수식)
```
##### Descending Index
- 내림차순 인덱스
##### Composite Index
- 두개이상 컬럼으로 인덱스 생성

#### BITMAP 인덱스
- 데이터 종류가 ㅁ많고 데이터가 많을 경우 많이 사용함
```sql
CREATE BITMAP INDEX {인덱스명}
ON {테이블명}(컬럼)
```
