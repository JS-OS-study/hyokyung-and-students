### CPU-burst Time의 분포

- 여러 종류의 job(process)이 섞여 있기 때문에 CPU 스케줄링이 필요함
    - Interactive job(사람)에게 적절한 response 제공 요망
    - CPU와 I/O 장치 등 시스템 자원을 골고루 효율적으로 사용
    - CPU는 CPU bound job이 많고 I/O bound job은 빈도는 적은데 시간이 김

### 프로세스의 특성 분류

- 프로세스는 그 특성에 따라 다음 두 가지로 나뉨
    - I/O bound process
        - CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job
        - (many short CPU bursts)
    - CPU-bound process
        - 계산 위주의 job
        - (few very long CPU bursts)

### CPU Scheduler & Dispatcher

- CPU Scheduler ← 운영체제 안에 일부 코드(기능)
    - Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다.
- Dispatcher ← 위와 동일
    - CPU의 제어권을 CPU Scheduler에 의해 선택된 프로세스를 고름
    - 이 과정을 context switch라 함

⇒ CPU 스케줄링이 필요한 경우는 프로세스에 다음 같은 상태 변화가 있을 때

1. Running → Blocked(ex: I/O 요청 system call)
2. Running → Ready(ex: time interrupt)
3. Blocked → Ready(ex: I/O 요청 후 interrupt)
4. Terminate

- 1, 4에서의 스케줄링은 nonpreemptive(=강제로 빼앗지 않고 자진 반납)
- 그 외 모든 다른 경우는 preemptive(=강제로 빼앗음)

즉, CPU Scheduling은 현재 ready queue에 들어와 있는 CPU를 얻고자 하는 프로세스 중에서 어떤 프로세스에게 CPU를 줄지 결정하는 메커니즘이다. 이 때 2가지 중요한 이슈는 아래와 같다.

1. 누구에게 CPU를 줄 것인지
2. 사용을 보장할 것인지(= 중간에 다시 뺏을 것인지)

### 스케줄링 알고리즘 성능척도

Scheduling Criteria(Performance index)

1. 시스템 입장
    - CPU Utilization 이용률 : CPU가 놀지 않는 비율
    - Throughput 처리량 : 주어진 시간 동안 몇 개의 job을 처리하는지
2. 프로그램 입장
    - Turnaround time 소요 시간 : CPU를 쓰기 시작 ~ CPU burst가 종료될 때까지 시간
    - waiting time : ready queue에서 기다리는 시간
    - response time : ready queue에서 ~ CPU를 얻을 때까지 시간 → time sharing 환경에서 중요
    

### 스케줄링 알고리즘

1. FCFS(First-Come First-Served) - Non-Preemptive, 효율적이지 않음(CPU 사용이 길지 않은 interaction job이 너무 길게 기다릴 수 있음)
    
    → Convoy effect: short process behind long process
    
2. SJF(Shortest-Job-First)
    
    각 프로세스의 CPU burst time을 스케줄링에 적용
    
    minimum average waiting time을 보장 → SJF is optimal (preemptive에 해당)
    
    preemptive 라면 CPU를 줬다가도 짧은게 나타면 거기로 넘겨주는 거고, non-preemptive는 일단 짧은 것에 넘겨주고 그 사용 시간을 보장
    
    **SJF의 문제점**
    
    - Starvation 기아 현상
    - CPU 사용 시간(cpu burst)을 미리 알 수 없음 → 과거 사용량을 기반으로 추정
        
        exponential averaging : 실제 길이 x 비율 + 예측치 x (1-비율)
        

1. Priority Scheduling : 우선순위 높은 것에 CPU를 할당
    
    SJF도 일종의  priority scheduling이다. (predicated next CPU burst time)
    
    **문제점** 
    
    - Starvation(기아 현상) → never execute
    
    **해결책**
    
    - Aging(시간 흐름에 따라 increase the priority)
    
2. Round Robin(RR)
    
    현대 운영체제, preemptive
    
    CPU를 줄 때 할당 시간(time quantum, q)을 부여 → 응답시간이 빨라짐
    
    어떤 프로세스도 (n-1)q time unit 이상 기다리지 않음
    
    q large → FCFS
    
    q small → context switch 오버헤드가 커짐
    
    일반적으로 SJF보다 average turnaround time은 길지만 responsive time은 더 짧음
    

### Multilevel Queue

1. 다른 큐로 이동 불가
2. ready queue를 여러 개로 분할
    1. foreground(interactive)
    2. background(batch - no human interaction) - CPU 오래 사용하지 않는 것(context switch cost 큼)
3. 각 큐는 독립적인 스케줄링 알고리즘을 가짐
    1. foreground - RR
    2. background - FCFS
4. 큐에 대한 스케줄링이 필요
    1. Fixed priority scheduling - starvation이 생길 수 있음
    2. time slice - 각 큐에 CPU time을 적절한 비율로 할당
    

### Multilevel Feedback Queue

1. 다른 큐로 이동 가능
2. aging을 이와 같은 방식으로 구현 가능
3. CPU 사용시간이 짧은 프로세스에 더 기회를 줌 → 미리 추측할 필요 없음
4. Multilevel-feedback-queue 스케줄러를 정의하는 파라미터들
    1. Queue의 수
    2. 각 큐의 스케줄링 알고리즘
    3. 프로세스를 상위 큐로 보내는 기준
    4. 프로세스를 하위 큐로 보내는 기준
    5. 프로세스가 CPU 서비스를 받으려 할 때 들어갈 큐를 결정하는 기준

### Multiple-Processor Scheduling

- CPU가 여러 개인 경우 스케줄링이 더 복잡해짐
- Homogeneous processor인 경우
    - 큐에 한 줄로 세워 각 프로세서가 알아서 꺼내가게 할 수 있다
    - 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우 더 복잡해짐
- Load sharing
    - 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 메커니즘 필요
    - 별개의 큐를 두는 방법 vs 공동 큐를 사용하는 방법
- Symmetric Multiprocessing(SMP)
    - 각 프로세서가 각자 알아서 스케줄링 결정
- Asymmetric multiprocessing
    - 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세스는 거기에 따름

### Real-Time Scheduling

- Hard real-time systems
    - 정해진 시간 안에 반드시 끝내도록 스케줄링
- Soft real-time computing
    - 일반 프로세스에 비해 높은 priority를 갖도록 해야 함

### Thread Scheduling

- Local Scheduling
    - User level thread의 경우 사용자 수준의 스레드 라이브러리에 의해 어떤 스레드를 스케줄할지 결정
- Global Scheduling
    - kernel level thread의 경우 일반 프로세스와 마찬가지로 커널의 단기 스케줄러가 어떤 스레드를 스케줄할지 결정

### Algorithm Evaluation

- Queueing models
    - 확률 분포로 주어지는 arrival rate와 service rate 등을 통해 각종 preformace index 값을 계산
- Implementation(구현) & Measurement(성능 측정)
    - 실제 시스템에 알고리즘을 구현해 실제 작업에 대해 성능을 측정 비교
- Simulation(모의 실험)
    - 알고리즘을 모의 프로그램으로 작성 후 trace를 입력으로 해 결과 비교