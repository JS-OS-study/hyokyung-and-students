세마포어: 프로그래머 관점에서 프로세스를 잘 동기화하는 방법을 보여주는 추상적 접근법.

고전적인 프로세스 동기화 관련 문제에 대해 알아본다.

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

## Dining-Philosophers 문제
<img width="412" alt="식사하는 철학자 문제" src="https://user-images.githubusercontent.com/50111853/155872584-47cf73f4-e7a9-423c-995d-6c34f20c7bc0.png">

- 철학자들은 밥을 먹거나 생각하거나 둘 중에 하나를 한다.
- 젓가락이 각각 공유자원이고, initially all values are 1 (하나씩 있다)
- 위 코드는 데드락 가능성이 있다.(=모든 철학자가 동시에 젓가락을 든 경우)

### 위 문제 해결 방안 3가지
1. 4명의 철학자만 테이블에 동시에 앉도록 한다
2. 젓가락을 두개 잡을 수 있을 때에만 젓가락을 집게 한다
3. 비대칭 (짝수-왼쪽 젓가락, 홀수-오른쪽 젓가락부터 잡을 수 있도록 제한)

<img width="570" alt="식사하는 철학자 문제" src="https://user-images.githubusercontent.com/50111853/155874415-c1390407-f806-4d66-8b22-a7c6939c9515.png">

#### pickup(int i): 젓가락을 잡는 함수

#### test(): 젓가락 들 수 있는 환경인지 확인하는 함수
- 왼쪽, 오른쪽 철학자가 밥 먹고 있으면 if문 만족하지 않아 락 풀지 못함



## 세마포어의 문제점과 그를 보완해주는 모니터
<img width="705" alt="세마포어의 문제점" src="https://user-images.githubusercontent.com/50111853/155872690-5a04d48a-ea2f-4efa-ae5d-04003b0af62d.png">

- 세마포어는 동기화하는 법을 알려주고 프로그래머가 제어할 수 있도록 함.
- 모니터: 프로그래밍 언어 차원에서 공유 자원에 접근하는 일을 모니터가 직접 하게 해 프로그래머의 부담을 줄여줌.
- 세마포어는 정확도를 검증하기 어려움

<img width="501" alt="모니터" src="https://user-images.githubusercontent.com/50111853/155874579-7c3a92a3-f2ce-4af4-8c28-ef16f9b8d7de.png">

- 공유데이터에 접근하려면 내부의 프로시저인 모니터를 통해야만함
- 공유 데이터에 접근하는 프로시저를 함수로 구현해두었음
- 모니터 내부 프로시저는 동시에 여러개가 실행이 불가능하도록 모니터가 막고 있음 => **락을 걸 (P,V연산 할) 필요 없음**
- 모니터 안에선 한 프로세스만 활성화된다. 액티브 프로세스가 1개만 있도록 모니터 자체에서 제어한다!! 

<img width="509" alt="condition variable" src="https://user-images.githubusercontent.com/50111853/155874631-c2f1adf3-33ea-41d9-a063-304b4d983c26.png">

### condition variable
<img width="500" alt="바운디드 버퍼 문제와 조건 변수" src="https://user-images.githubusercontent.com/50111853/155874643-18666c98-a0e3-481a-a93c-e032686834d6.png">

- 위 bonded buffer 문제를 condition var로 해결하자면 공유버퍼가 모니터 안에 있음
- 그래서 생산자든 소비자든 모니터 안에서 활성화되므로 락 걸 필요 X
- 만약 다른 놈이 있을 경우 큐에 줄을 서고 producer->produce, consumer-> consume 함수 앞에서 기다리며 잠든다. 


## Concurrency Control (병행 제어)
- 프로세스 동기화와 같은 얘기임. 병행 작업할때 잘 제어 하자^^ 
- 



문제
1. bounded-buffer problem에서 소비자 프로세스가 데이터를 꺼내갈 때 데이터를 꺼낼 Full 버퍼에만 락을 건다. (O,X) 
정답: X, 공유 버퍼 전체에 락을 건다.

2. dining philosophers 문제에서 데드락이 발생하는 경우는?
정답: 모든 철학자가 동시에 젓가락을 잡으려고 하는 경우
