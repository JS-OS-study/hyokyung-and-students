# Process synchronization

#### 데이터의 접근
Storage Box: Memory 주소 공간  
Execution Box: CPU 프로세스

![컴퓨터 시스템 내 데이터의 접근](https://user-images.githubusercontent.com/50111853/154946794-c1e3a6c1-358d-4b1b-9432-3283ff4300d1.png)

- 저장소(스토리지)<->연산(실행)하는 곳 / 어디서든 컴퓨터 시스템에서 이런 경로를 거침
- 데이터가 왔다갔다하고 **여러 execution box가 한 스토리지를 공유하기도** 함. 이때 동기화는 어떻게? 

![race condition](https://user-images.githubusercontent.com/50111853/154947007-68147f7c-589d-48d1-a10e-e50a6e4aa0eb.png)

**race condition**: 달리기 레이스처럼 경쟁상태/ 다른 execution box에서 하나의 데이터를 동시에 접근하는 상황
- 공유메모리를 사용하는 프로세스들간 / 커널 내부 데이터를 접근하는 루틴들 간 (e.g. 커널모드 수행 중에 인터럽트로 커널모드의 다른 루틴 수행)

#### 운영체제에서 레이스 컨디션(동시접근)이 발생하는 순간
1. 커널모드 실행중인데 인터럽트가 발생해서 인터럽트 처리루틴 수행될때
  - 둘 다 커널 코드라 커널 주소 공간을 공유

2. 프로그램이 실행될때 유저모드였다가 커널모드였다가 한다.  
  - 커널모드에 있는 프로그램은 CPU를 빼앗는지 않는다.
  - time quantum system은 real system이 아니라 좀 늦게 뺏어도 상관없음.
 
### Critical Section (임계 구역)
- **다수의 프로세스가 공유 데이터를 동시에** 사용하기 원하는 경우, 각 프로세스의 code segment에는 공유 데이터에 접근하는 코드인 critical section이 존재.
- 한 프로세스가 이미 **critical section**에 있으면 다른 모든 프로세스는 그 안에 들어갈 수 없음.

#### critical section 문제를 위한 알고리즘
```c
do {
  entry section
  critical section
  exit section
  remainder section
} while(1);
```
알고리즘의 충족 조건
1. Mutual exclusion(상호 배제) : 한 놈이 이미 안에 있으면 다른 애들은 못 들어감
2. Progress: 아무도 critical section에 없는데 들어가고자하는 프로세스가 있으면 들어가게 해줘야함
3. Bounded waiting(유한 대기): 프로세스가 들어가려고 요청한 후로 언젠간 들어가야한다.


## 알고리즘 1
![고급언어로 짠 알고리즘](https://user-images.githubusercontent.com/50111853/154952318-6d2ac8c4-9604-4719-8b5d-797cd5dda2d0.png)

- 본인의 차례가 아닌 동안 while 돌다가 상대가 critical section에서 나올 때 (=자기 차례가 되면) turn이 1이 됨
- 이 경우 본인이 크리티컬 섹션에 들어갔다가 나와야 상대 차례고 vice versa
- --> p1은 크리티컬 섹션에 여러번, P1는 한번만 들어가고 싶으면 어떡함? 
- -> **progress를 잘 충족하지 못한** 알고리즘임.


## 알고리즘 2
![스크린샷 2022-02-21 오후 9 09 35](https://user-images.githubusercontent.com/50111853/154952631-2ead4d13-0160-45bb-955d-6ac696055f77.png)

- 내가 크리티컬 섹션 들어갈 때 플래그를 1로 바꿈. 
- 문제: 둘다 깃발만 들고 끊임없이 양보할 수도 있음.


## 알고리즘 3 (피터슨의 알고리즘)
![스크린샷 2022-02-21 오후 9 12 25](https://user-images.githubusercontent.com/50111853/154953064-5932358f-95d3-4cf6-ac52-ac2fce97a769.png)

- 둘다 들어가고싶을때만 니턴 내턴을 따지고, 한 프로세스만 관심 있으면 걔가 들어가는 식
- 문제: busy waiting(=spin lock): 계속 while 문을 돌면서 / CPU와 메모리를 쓰면서 대기함.

하지만 **하드웨어적으로 test&modify를 atomic 수행**하면 간단히 해결 가능
![test and set을 통한 상호 배제](https://user-images.githubusercontent.com/50111853/154954226-8547f53d-fed3-4188-b0f9-67b62b463468.png)


## 세마포어(Semaphores)
- Semaphore S: 정수형 변수 / 앞의 방식들을 추상화
- S = 자원의 개수 (5면 자원이 5개 있는거)
- 추상 자료형(논리적 자료형): object, operation
- 공유 자원을 획득, 반납할때 이 처리를 담당
 
![세마포어](https://user-images.githubusercontent.com/50111853/154954534-b1138b44-d7c4-4e23-bcce-d4198e4c0d84.png)

**p 연산: 공유 데이터를 획득하는 과정** p연산해서 자원 가져가면 S가 1 줄어듦
**v 연산: 반납하는 과정**

### P(S): 락을 거는 과정
- while 돌다가 S가 양수가 되면(=누군가 자원을 내어놓으면) S--를 하고 자원을 냉큼 획득.
- 계속 돌면 busy waiting과 다를 바 없다

### V(S): 자원 반납

이 두 연산은 atomic 연산을 통해서만 접근 가능

## Critical Section of n Processes
![p, v 연산이 있는 임계구역 알고리즘](https://user-images.githubusercontent.com/50111853/155309323-a8a0dee5-7ae2-437c-839c-d4deaa05625c.png)

**busy-wait 방식**
- 프로그래머는 세마포어 지원이 되면 P, S 연산만 구현하면 된다. (추상적으로 지원)
- Sleep Lock: Block and wake up 방식 (락 없는 프로세스는 CPU를 쓰는게 아니라 아예 blocked 상태다)

![block/ wakeup implementation](https://user-images.githubusercontent.com/50111853/155310277-c83b8c1e-1f51-4a75-b95c-8f8de3ebc806.png)

L: 프로세스를 담고 있는 리스트

![스크린샷 2022-02-23 오후 8 25 11](https://user-images.githubusercontent.com/50111853/155310450-0925d238-74dd-40ce-ab1d-e30d50911e27.png)

#### P연산
- if문 안으로 못 들어가면 S.L 리스트에 프로세스를 넣고 block 해둔다.
- 변수값 1 빼고 시작. 

#### V연산
- 여기서 S는 자원의 개수를 세는거랑은 좀 다름. 음수면 누가 자원을 기다리고 있다. 양수면 자원을 쓰고 있다. busy-wait의 경우랑 좀 다르다.
- 자원을 내놨는데도 S의 값이 0이라는건 P연산에서 누가 -를 했다는 것 
- V연산에는 자고 있는 걸 깨워주는 wakeup 함수를 포함하고 있음.


## Busy wait vs. Block/wakeup
- 보통은 block/wakeup이 더 효율적 but 프로세스의 상태를 block/wake할 때도 오버헤드 든다.
- critical section의 길이가 긴 경우 block / wakeup이 적당
- critical section의 길이가 매우 짧은 경우 busy-wait이 나을 수도 있음


## 2 Types of Semaphores
#### Counting semaphore
- 세마포어 변수 값(자원의 개수)이 1 이상으로 갈 수 있을 때
- 여분의 자원 개수를 셀때 

#### Binary semaphore (= mutex)
- 0 또는 1값만 가질 수 있는 semaphore

#### 데드락: 둘 이상의 프로세스가 서로 상대방이 충족할 수 있는 이벤트를 무한히 기다리는 현상
![데드락](https://user-images.githubusercontent.com/50111853/155311534-bf48d4b9-dab6-4039-8db0-d9048fe4a556.png)

해결법: 자원 획득 순서를 똑같이 맞추면 됨. P0, P1 부분을 이렇게 수정하면 된다!

![데드락 해결법](https://user-images.githubusercontent.com/50111853/155312017-70fa1eff-98fe-46a2-b2d9-9f7e6e7d6127.png)
