원자성
-모든 트랜잭션내의 모든 쿼리는 성공해야함
-하나의 실패는 트랜재션이 롤백되어야한다

atomicity 
-하나 이상의 쿼리가 실패할 경우 모든 쿼리를 롤백시킴
-트랜잭션은 하나의 단위이고 나눌수없다

isolation
- 여러 트랜잭션이 동시에 동일한 데이터를 씀
- 읽으려고 경합하는 동시성이 발생
- read phenomena 
- isolation levels

isolation -read phenomena
- dirty reads
-현재 실행중인 트랜잭션에서 발생하는 읽기 현상
(flush 완전히 커밋되지 않음)
- Non-repeatable reads
- phantom reads : 아직 존재하지 않아서 실재로 읽을 수 없음
- lost updates 잃어버린 업데이트



Non-repeatable read
- 중복되지 않는 읽기
- select sum(QNT*PRICE)FROM SALES

BEGIN TX1
- SELECT PID, QNT*PRICE FROM SALE
- SELECT SUM(QNT*PRICE)FROM SALES
->DIRTY READ
-불일치하는 값을 얻음


BEGIN TX2
- UPDATE SALES SET QNT = QNT+WHERE PID =1

Phantom read
- select pid , qnt*price from sales
- insert into sales values
- select sum(qnt*price)from sales

Lost upadatpes
- update sales set qnt = qnt+19 where pid =1
- update sales set qnt = qnt+5 where pid =1
- select sum(qnt*price)from sales
->Read uncommited
->repeatable read = 반복되지 않는 읽기는 나쁜 읽기 현상이다

isolation
-read uncommitted :dirty read
-read commited =데이터베이스가 엔진을 읽기 워크로드에 최적화 
-repeatable read = 반복되는 읽기는 읽기를 반복 가능하게 만드는 고립수준 
-snapshot = 모든 읽기 현상을 제거/ 여기서 이 순간을 읽겠다
-serializable = 더이상 동시성이 없다 
-dbms 고립수준을 서로 다르게 구현 
스냅샷 - 트랜잭션 시작 시점에서 데이터베이스 버전을 가져옴
		- 데이터베이스를 최적화된 방식으로 구현


database implementation of isolation
- 각 dbms는 고립 수준을 서로 다르게 구현
- 비관적 접근방식 - 비관적 동시성 제어 의미
- 낙관적 방식 - 일반적으로 잠금을 사용하지 않음

일관성(consistency)
- 속도, 성능 확장성을 위해 일관성을 희생시킴
- Consistency in data 
- Consistency in reads 

Consistency in Data
- Defined by the user
- 참조무결성
- Atomicity
- isolation =데이터의 불일치


spot inconsistency in this data 
-사진을 삭제했지만, 좋아요 이벤트는 남아있다
->데이터베이스/ 응용프로그램 수준에서 처리하도록 할 수 있다

consistency in reads 
-> update x 
-> read x

일관된 읽기또한 발생할 수 있다

-만약 트랜잭션이 변경을 커밋했다면, 
새로운 트랜잭션이 즉시 변경 사항을 볼 수 있을까?
-이러한 종류의 불일치가 전체 시스템에 영향을 미침
-mongodb문서간 참조 무결성을 보장하지 않는다면,동일한 문제가 발생

summary consistency :업데이트 되고 커밋된 후에 해당값을 읽으려는 트랜잭션에서 이전 버전을 얻는다 : 불일치 -> 이러한 불일치로부터 최종 일관성을 통해 회복한다

Durability
지속성
- 강력한 관계형 데이터 베이스
- 커밋된 transactions must be persisted in a duarable non-volatile storage.
- durability technique
	- WAL -write ahead log
	- Asynchronous snapshot
	- AOF - wql과 비슷한 또다른 저장방법


WAL - writing a lot of data to disk is expensive (indexes, data files, columns, rows, etc)
-최적화되고 조밀한 방법이 필요함

durability - os cache 
-os usually goes to the Os cache
-> 처리하면  i/o가 줄어들고, i/o가 줄어들면 성능이 향상된다
-작성한 내용이 os로 전송 os가충돌 기계가 다시시작됐을때 데이터가 충돌
-os캐시를 우회하고 데이터베이스에게, 
데이터베이스는 "이 멍청한 OS야, 나는 너를 믿지 않아"라고 말하는 명령이 있다

- "커밋된"transactions must ne persisted in a duarble non-volatile storage.
- WAL -write ahead log
- Asynchronous snapshot
- AOF- 변경사항을 추적하고 이러한 사항을 기록

WAL 
	- 인덱스 , 데이터 파일, 열 , 행이 있다
	- 시스템 파일, 시스템 테이블 등 많은 것들을 쓰고 있다.
	-더 최적화되고 조밀한 방법이 필요하다
OS
	- 데이터베이스가 디스크에 쓰도록 윈도우나 리눅스같은 운영체제에 요청하면, os는 실제로 약간 수상한 일을한다
	- 디스크에 쓰지 않고 자신의 메모리 캐시에 쓴다
	- A write request in os usually goes to the os cache
	- 데이터는 캐시에 잇고, 재시작하면 캐시는 당연히 ram에 있다
	- 결과적으로 데이터베이스는 지속성이 없다.
	- 트랜잭션이 커밋되엇다고 말했지만 실제로는 지속성이 없다

summary
- 지속성은 변경사항을 지속하는 능력이다.
- 트랜잭션이 커밋될 때 변경 사항을 지속하는 능력이다
- 트랜잭션이 커밋되면 디스크에 지속되도록 원한다





	
