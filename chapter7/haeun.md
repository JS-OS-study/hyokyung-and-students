## Deadlock (교착 상태)

- 프로세스들이 서로가 가진 자원을 기다리며 block된 상태

## 자원

- 하드웨어, 소프트웨어 등을 포함 (io device, cpu cycle, memory space, semaphore(공유데이터도 자원임) 등)

### 프로세스가 자원을 사용하는 절차

request (요청) - allocate (할당받음, 획득) - use (사용) - release (배출)

## 데드락 발생 조건 4개

1. mutual exclusion 상호배제: 매순간 **하나의 프로세스만이** 자원을 독점적으로 사용
2. no preemption 비선점: 자원을 **빼앗기지 않음**
3. hold and wait 보유대기: 내가 가진 자원을 **가지고 있는 채로** 다른 자원을 대기
4. circular wait 순환대기: 자원을 기다리는 프로세스간에 **사이클이 형성됨**
 
<img width="300" alt="circular wait" src="https://user-images.githubusercontent.com/50111853/156170865-8b21e26d-0560-4dcf-b1dc-9480802a9d23.png"/>

## 자원 할당 그래프로 데드락이 걸렸는지 알아보자.

<img width="597" alt="자원할당그래프" src="https://user-images.githubusercontent.com/50111853/156171575-f644ca2f-ead9-493a-81c9-4b7d5a151dc6.png"/>

Process -> Resource: 프로세스가 자원 요청 (!= 획득)
Resource -> Process: 프로세스에게 자원 할당
사각형 안에 동그라미: 자원의 갯수

<img width="348" alt="데드락 상태인 자원할당그래프" src="https://user-images.githubusercontent.com/50111853/156172148-fb037163-e934-4c2d-aa7e-23fce691bc04.png"/>

위 그림을 보면 싸이클이 있음. 이때 만약 **동그라미(자원의 인스턴스)가 한개면 데드락**임.   
**이땐 R2의 자원이 2개지만 싸이클도 2개라 데드락임.**

<img width="335" alt="사이클 1개인 자원할당그래프" src="https://user-images.githubusercontent.com/50111853/156172324-e304cf8d-192c-40c2-9230-91954b4de48b.png"/>

이 그림은 싸이클이 1개, 자원이 2개라 데드락이 아님.

## 데드락 처리 방법

1. 데드락 예방: 자원 할당 시 데드락의 4조건 중 어느 하나가 만족되지 않도록 하는 방법
2. 데드락 회피: 자원 요청에 관련된 추가 정보를 모아 데드락의 가능성이 없는 경우에만 자원을 할당 / 시스템 상태가 원래 상태로 돌아올 수 있을 때만 자원 할당
3. 데드락 탐지 및 recovery: 데드락 발생은 허용하되 탐지 루틴을 두어 탐지 시 recover
4. 데드락 무시: 데드락을 시스템이 책임 안 짐. 

유닉스를 포함한 현대의 운영체제는 **대부분 데드락을 Ignorance(무시)** 함. 이유는 **데드락은 빈번하게 일어나지 않기 때문에 괜히 방지했다가 오버헤드 일어날까봐.** 사용자가 알아서 프로세스를 죽이거나 대처하도록 함.

### Deadlock prevention

<img width="552" alt="Deadlock prevention" src="https://user-images.githubusercontent.com/50111853/156172754-68636cf3-2b3b-4a3e-9b71-1dcf2182226f.png"/>

- hold and wait: 프로세스 시작할 때 필요한 모든 자원을 받아놓고 시작함. 자원을 **비효율적으로 사용함.**
- no preemption: cpu, memory는 상태를 쉽게 save, restore할 수 있어서 빼앗음으로써 자원을 사용 가능. 
- circular wait: 자원에 순서를 정해 '1번 자원을 획득해야지만 2번을 획득할 수 있다'라는 식으로 제한.

### Deadlock avoidance

<img width="539" alt="데드락 회피" src="https://user-images.githubusercontent.com/50111853/156175031-4050c5d0-58f8-4960-ab01-ce35e68eee21.png"/>

- 가장 단순한 데드락 회피 방법: 프로세스가 시작될때 그 프로세스가 쓸 **모든 자원의 수(최대사용량)를 선언하고 시작** 함으로써 데드락 회피

- 시스템이 safe state에 있으면 데드락 X
- 시스템이 unsafe state에 있으면 데드락 가능성 있음.
- 데드락 회피 방식은 2개의 회피 알고리즘을 통해 시스템이 **unsafe state** 에 들어가지 않는 것을 보장한다.

1. Resource Allocation Graph 알고리즘: 자원당 1인스턴스인 경우
2. Banker's 알고리즘: 자원당 여러 인스턴스인 경우

#### 자원 할당 그래프 알고리즘 (자원당 인스턴스가 1개인 경우)

<img width="558" alt="스크린샷 2022-03-01 오후 10 14 23" src="https://user-images.githubusercontent.com/50111853/156175465-75f2fa10-6e57-4a81-a94a-3ca015612abd.png"/>

- 위 그림에서 **점선: 이 프로세스가 이 자원을 한번이라도 요청할 가능성**이 있다.  
- 데드락 회피 방식은 최악의 상황을 가정하고, 안전한 경우에만 자원을 할당함.
 
#### Banker's algorithm (자원당 인스턴스가 여러개인 경우)

<img width="582" alt="스크린샷 2022-03-01 오후 10 24 45" src="https://user-images.githubusercontent.com/50111853/156177114-66e22ae5-d894-4e1f-850e-f0ba39da5b85.png"/>

##### 상황
5개의 프로세스, 3개의 자원타입(각각 10개,5개,7개)

- 자원 B는 프로세스 A를 최대 9개 요청한다. Max(최대 요청할 양) - Allocation(할당된 양) = Need(추가 요청할 수 있는 양)  
- 이 때 P1처럼 **Available로 Need를 충족할 수 있다면 OK**
- 다만 P0처럼 Available(가용 자원)의 수로 Need를 충족할 수 없다면 자원을 빌려줄 수 없다. (데드락 우려)

### Deadlock Detection & Recovery

<img width="537" alt="wait for graph" src="https://user-images.githubusercontent.com/50111853/156322003-126f093c-e635-4733-9952-326366b0df1f.png">

- 프로세스가 요청하면 자원을 일단 준다.
- 위에서 배운 자원할당그래프와 뱅커스 알고리즘, wait-for graph을 사용해 detection

#### Recovery

1. 프로세스 종료: 데드락 걸린 프로세스 다 죽임 or 데드락 사이클이 사라질때까지 하나씩 죽임
2. 프로세스 뺏기: 데드락 걸린 프로세스들 중 비용이 가장 작은 victim을 골라 safe state로 롤백한 후 해당 프로세스를 재시작.
- 프로세스 뺏기 시 starvation 우려: 동일한 프로세스가 계속해서 victim으로 선정되는 경우가 있음. 이를 방지하고자 cost factor에 rollback 횟수도 같이 고려해야함

### Deadlock Ignorance
- 만약 시스템에 데드락이 발생한 경우 사용자가 직접 프로세스르 죽여야함
