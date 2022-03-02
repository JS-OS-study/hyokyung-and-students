# DeadLock 교착상태

### Deadlock

- 일련의 프로세스들이 서로 가진 자원을 기다리며 block된 상태

Resource

- 하드웨어, 소프트웨어 등을 포함하는 개념
- I/O Device, CPU cycle, memory space, semaphore
- 프로세스가 자원을 사용하는 절차 : request → allocate → use → release

### Deadlock 4가지 발생 조건

- Mutual exclusion(상호배제) - 그 순간엔 한 프로세스만이 해당 자원을 쓸 수 있음
- No preemption(비선점) - 빼앗기지 않음
- Hold and wait(보유대기) - 다른 자원을 기다릴 때 내가 갖고 있는 자원을 놓지 않음
- Circular wait(순환대기) - 자원을 기다리는 프로세스간 사이클이 형성된 상태

데드락 발생 여부를 확인하려고 자원할당그래프(Resource-Allocation Graph)를 그리기도함

- 데드락이 아닌 경우(사이클이 없음)

![image](https://user-images.githubusercontent.com/84627144/156346827-5266090a-8cb5-4dbe-befb-d3520511e4e6.png)


- 사이클은 있으나 자원이 1개씩만 있는게 아니라 여유자원이 있어서 데드락이 아님

![image](https://user-images.githubusercontent.com/84627144/156348190-3bdfd1b3-f85b-4c26-ade2-e9ea5bdb033b.png)


- 데드락인 경우(사이클이 있음)

![image](https://user-images.githubusercontent.com/84627144/156348252-e8994462-529f-44ea-bbfc-3c22671bf2c8.png)


### Deadlock 처리 방법

예방 차원

- Deadlock Prevention
    - 자원 할당 시 데드락 필요 조건 중 1개가 만족되지 않게 함
    - Mutual Exclusion - 이건 반드시 성립해야 하므로 이걸론 해결 못함
    - Hold and wait
        - 프로세스가 자원을 요청할 때 다른 어떤 자원도 갖고 있지 않게 함
            - 방법1 : 프로세스 시작 시 모든 필요한 자원을 할당
            - 방법2 : 자원이 필요한 경우 보유 자원을 모두 놓고 다시 요청
    - No Preemption
        - 프로세스가 자원을 기다리는 경우 이미 보유한 자원이 선점됨
        - 모든 필요한 자원을 얻을 수 있을 때 그 프로세스는 다시 시작됨
        - state를 쉽게 save하고 restore할 수 있는 자원에서 주로 사용(CPU, memory)
    - Circular Wait
        - 모든 자원 유형에 할당 순서를 정해서 정해진 순서대로만 자원 할당
        - 예를 들어, 순서가 3인 자원 a를 보유 중인 프로세스가 순서가 1인 자원을 할당받으려면 우선 갖고 있는 자원 a를 release해야 함
    
    → 이용률 저하, 스루풋 감소, starvation 감소
    
- Deadlock Avoidance
    - 자원 요청에 대한 부가 정보를 이용해 데드락 가능성이 없을 때만 자원을 할당함
    - → 데드락으로부터 안전한지를 동적으로 조사해서 안전한 경우에만 할당
    - 시스템 state가 원래 state로 돌아올 수 있는 경우에만 할당
    - safe state - 시스템 내 프로세스들에 대한 safe sequence가 존재하는 상태
    - safe sequence - 가용자원 + 모든 프로세스의 보유 자원에 자원 요청이 충족되어야 함
    - 조건을 만족하면 다음 방법으로 모든 프로세스의 수행을 보장
        - 프로세스의 자원 요청이 즉시 충족될 수 없으면 모든 프로세스가 종료될 때까지 기다림
    
    - 자원에 대한 인스턴스가 1개만 있을 때
        - Resource Allocation Graph 알고리즘
            - 어떤 프로세스가 어떤 자원을 언젠가 요청할 수 있음을 점선 표시
            - 프로세스가 해당 자원을 요청하면 실선으로 바꿈
            - 자원이 release되면 assignment edge는 다시 claim edge로 바꿈
            - 사이클이 생기지 않는 경우에만 요청 자원을 할당
            - 사이클 생성 여부 조사시 프로세스 수가 n일 때 O(n^2) 시간이 걸림
    - 자원에 대한 인스턴스가 여러개인 경우
        - Banker’s Algorithm
            - maximum 자원을 미리 선언
            - Max(최대 요청 가능한 양) - Allocation(할당된 양) = Need(추가 요청 가능양)
            - 현재 할당 가능한 자원이라도 Need를 고려해서 자원 요청을 처리함

데드락 발생 후 조치 

- Deadlock Detection and recovery
    - 데드락 발생은 허용하되, 그에 대한 detection 루틴을 두어 데드락 발견 시 recover
    - 자원당 인스턴스가 1개 - 자원할당그래프
    - 자원당 인스턴스가 여러개 - corresponding wait-for graph
        - 오버헤드 - 프로세스가 n개일 때, O(n^2)
    - 자원의 최대 사용량을 미리 알릴 필요 없음 → 그래프에 점선 없음
    
- Deadlock Ignorance
    - 데드락을 시스템이 책임지지 않음
    - 데드락은 가끔 발생하기 때문에 조치를 하는게 오버헤드가 큼
    - 만약 시스템에 데드락이 발생하면 시스템이 비정상적으로 작동해 사람이 직접 프로세스를 죽이는 등의 방법으로 대처
    - 현재 운영체제에서 대부분 채택한 방법