## Bounded-Buffer Problem (Producer-consumer Problem)

- Bounded Buffer = 유한하다.

<img width="564" alt="스크린샷 2022-02-25 오후 2 06 16" src="https://user-images.githubusercontent.com/50111853/155657222-846c743e-a75c-4253-bbd9-e1f148998db1.png">

- 주황색이 데이터가 들어있는 버퍼들 / 회색이 비어있는 버퍼들. 생산자가 버퍼에 데이터를 집어넣는다.
- producer, consumer 프로세스로 총 2 종류의 프로세스가 있음. 이때 프로듀서 프로세스 여러개, 컨수머 프로세스 여러개 가능
- 생산자: Empty buffer에 데이터를 만들어 집어넣는 역할 
- 소비자: Full buffer에 데이터를 꺼내감 

### 동기화 관련해 일어날 수 있는 문제

1. 공유버퍼이므로 생산자 둘이 한 버퍼에 데이터를 넣으려고 할 수도 있음
2. 소비자 둘이 동시에 데이터를 꺼내가려할 수도 있음
-> 데이터를 넣고 꺼내기 전에 Lock을 걸음으로써 해결가능

### 이 문제에서 semaphore 용도

1. shared data의 mutual exclusion을 위해 binary semaphore 필요
2. 가용 자원(empty buffer)의 개수를 센다 (counting semaphore)


### Bounded-buffer 코드로 해결하기

![바운디드 버퍼 문제 코드](https://user-images.githubusercontent.com/50111853/155870391-2ba9b914-bc80-4b99-b532-2db360b4b13b.png)

- mutex: 락을 걸기 위한 변수 (세마포어)
- P(mutex), V(mutex)가 락을 걸고 푸는 연산임. 

## Readers-Writers Problem
![reader-writer problem](https://user-images.githubusercontent.com/50111853/155870504-e706a0ac-4497-4804-b246-db9e4025e43b.png)

- 공유 데이터인 DB의 데이터를 읽고 쓸 때 문제
- **write는 동시에 하면 안 되지만 read는 동시에 가능**

![읽고 쓸 때 문제 해결](https://user-images.githubusercontent.com/50111853/155870566-2f8f3018-ff33-4ecd-91d1-74527f4a74ff.png)

- reader도 writer처럼 배타적으로 해도 되지만 비효율적임
- Reader에선 readcount++함으로서 읽고 있는 자원의 수를 카운트할 수 있음. 
- 다만 readcount도 공유 변수라 이거 값 바꿀 때 락 걸고 변경해야함!!!
- 내가 마지막으로 읽고 나가는 프로세스면(if readcount==0) DB에 걸어둔 락을 풀어야함
- reader가 계속 올 경우 writer starvation 될 수도.




문제
1. bounded-buffer problem에서 소비자 프로세스가 데이터를 꺼내갈 때 데이터를 꺼낼 Full 버퍼에만 락을 건다. (O,X) 
정답: X, 공유 버퍼 전체에 락을 건다.
