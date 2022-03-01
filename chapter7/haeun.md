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
 
<img width="200" alt="circular wait" src="https://user-images.githubusercontent.com/50111853/156170865-8b21e26d-0560-4dcf-b1dc-9480802a9d23.png">

## 자원 할당 그래프로 데드락이 걸렸는지 알아보자.

<img width="597" alt="자원할당그래프" src="https://user-images.githubusercontent.com/50111853/156171575-f644ca2f-ead9-493a-81c9-4b7d5a151dc6.png">

Process -> Resource: 프로세스가 자원 요청 (!= 획득)
Resource -> Process: 프로세스에게 자원 할당
사각형 안에 동그라미: 자원의 갯수

<img width="248" alt="데드락 상태인 자원할당그래프" src="https://user-images.githubusercontent.com/50111853/156172148-fb037163-e934-4c2d-aa7e-23fce691bc04.png">

위 그림을 보면 싸이클이 있음. 이때 만약 **동그라미(자원의 인스턴스)가 한개면 데드락**임.   
**이땐 R2의 자원이 2개지만 싸이클도 2개라 데드락임.**

<img width="235" alt="사이클 1개인 자원할당그래프" src="https://user-images.githubusercontent.com/50111853/156172324-e304cf8d-192c-40c2-9230-91954b4de48b.png">

이 그림은 싸이클이 1개, 자원이 2개라 데드락이 아님.

