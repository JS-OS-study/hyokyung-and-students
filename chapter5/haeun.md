# CPU Scheduling
- CPU를 어떤 프로세스에게 줄 지 결정
- CPU Job이 io bound와 cpu bound 잡이 섞여있기 때문에 필요함!
- 특히 사용자와 인터랙션 하는 IO Bound Job이 cpu 필요할 때 빨리빨리 가져야 사용자가 불편하지 않음. 

## CPU Scheduling이 필요한 경우
1. running -> blocked ('나 cpu 필요없어...' io 요청하는 시스템 콜)
2. running -> ready (할당된 시간 다씀. 타이머 인터럽트)
3. blocked -> ready (io 완료 후 인터럽트. 얘한테 바로 cpu 가는거 아님)
4. terminate (프로세스가 종료됐을 때)

1,4는 자진 반납(nonpre-emtive) / 2,3은 preemtive(강제로 빼앗음)


## CPU scheduler (독립적X, 운영체제 안의 CPU 스케줄링 담당 코드)
- ready인 프로세스 중 이번에 CPU를 받을 프로세스를 고른다.

## Dispatcher
- 실제로 CPU를 주는 놈.
- context switching을 하는 놈


# 스케줄링 성능 척도
### 시스템 입장
1. CPU Utilization: CPU에 일 많이 시켜야 꿀임
  - cpu를 사용한 시간 대비 일한 시간의 비율 (최대한 일 많이 해야 좋음)
2. Throughput (처리량)
  - CPU Burst의 갯수. 처리 완료한 갯수  


### 프로세스 입장
1. Turnaround Time (소요시간, 반환시간)  
  - cpu를 받아서 다 쓸 때까지 걸린 시간 (CPU burst 시작~끝)
2. Waiting Time (대기 시간)
  - 프로세스가 기다린 시간
  - pre emtive의 경우 waiting 타임이 여러차례 쌓여서 길어짐
3. Response Time (응답 시간)
  - 첫번째 응답이 오는데 걸린 시간 (타임 쉐어링 환경을 위한 기준)


##### 식당 예시
cpu utilization: 요리사가 요리한 양
throughput: 처리한 손님의 개수
turnaround time: 고객이 들어와 주문하고 식사하고 나가는데 걸린 시간
waiting time: 코스요리에서 다음 메뉴 나오는 거 기다린 시간의 총합
response time: 첫번째 음식이 나올때까지 기다린 시간


# CPU 스케줄링 알고리즘
### FCFS (선착순/ First come, First Served)
- 별로 효율적이진 않다. CPU를 많이 쓰는 프로세스가 cpu를 가지면 CPU를 적게 쓰는 프로그램이더라도 기다려야 한다.
- 관여한 **프로세스의 평균 웨이팅 타임**이 길어진다.
- convoy effect: 앞에 거대한 놈이 기다리고 있어서 기다리고 있어야하는 경우를 말함.


### SJF (Shortest Job First)
- 프로세스를 사용하려는 시간 즉 CPU burst가 가장 짧은 놈에게 프로세스를 줌
- preemtive: 일단 cpu 잡으면 해당 cpu burst가 완료될 때까진 cpu를 선점하지 않음
- nonpreemtive: 현재 수행중인 프로세스의 남은 burst time보다 더 짧은 cpu burst time을 가지는 프로세스가 도착하면 cpu 빼앗아서 줌
- nonpreemtive: **shortest average waiting time**


##### SJF의 문제점
1. starvation: 어떤 프로세스에겐 CPU가 계속 안 올 수도 있다. 
2. 예측이 쉽지 않음: 얼마나 CPU burst time이 필요한지 프로세스 자신도 모름
  - Exponential averaging: 


### Priority scheduling (우선순위)
- 우선순위가 **가장 높은** 프로세스에게 CPU를 준다
- preemtive는 더 우선순위가 높은 프로세스가 나타나면 cpu를 넘겨주고, nonpre는 넘겨주지 않음
- 작은 숫자가 우선순위가 높음. 
- SJF도 일종의 priority scheduling
- Starvation의 해결법: 에이징(aging) 기법. 우선순위가 낮은 프로세스라도 오래 기다리게 되면 우선순위를 높여주자. 


## 라운드 로빈

![round robin scheduling](https://user-images.githubusercontent.com/50111853/154834115-4b900873-d810-4c8b-bff2-173cbb397a53.png)

- **각 프로세스가 동일한 크기의 할당 시간(Time Quantum)**을 가짐. 
- Large Q: FCFS / Small Q: 매우 빈번한 Context Switch, 시스템 성능 저하 우려
- 응답 시간이 빨라짐.
- 굳이 예측할 필요 없이 CPU를 짧게 쓰는 프로세스가 짧게 쓰고 나갈 수 있음
- **CPU를 대기하는 시간과 쓰는 시간이 비례**
- 일반적으로 SJF보다 average turnaround time이 길지만 response time은 더 짧다.


## Multilevel Queue
- ready queue를 우선순위별로 여러개로 분할, 모두 cpu 쓰려고 기다림
- 차별적임. 그래서 multilevel feeback queue를 도입

##### multilevel feedback queue
![multilevel feedback queue](https://user-images.githubusercontent.com/50111853/154834381-cc5b1e4b-79d5-4a24-bbec-9f2cbfa6b1f2.png)

- foreground(interactive): 라운드로빈
- background(batch, no human interaction): 선착순
- Queue에 대한 스케줄링이 필요
  - Fixed priority scheduling: fore 부터 -> background / starvation 우려 있음
- **처음 들어온 프로세스는 우선순위 우선으로** 해줬다가 여러번 필요할 수록 우선순위가 뒤로 밀린다.


## multiple processor scheduling
- cpu가 여러개면 스케줄링은 더 복잡해짐
- **load sharing**: 일부 프로세서에 Job이 몰리지 않게 부하를 공유
