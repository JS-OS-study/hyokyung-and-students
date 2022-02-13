# process, thread

### Process란

a program in execution → 실행중인 프로그램

가장 중요한 것은 process context를 아는 것

### Process context

특정 시점에 이 프로그램이 어디까지 실행됐고, 현재 시점에 Process counter가 어딜 가리키고 있고, 메모리엔 어떤 내용이 있는지... 등

1. CPU 수행상태를 나타내는 하드웨어 문맥
    1. Process Counter
    2. 각종 register
2. 프로세스의 주소 공간(메모리)
    1. code, data, stack
3. 프로세스 관련 커널 자료 구조
    1. PCB(Process Control Block) : 프로세스 관리를 위해 운영체제가 각 프로세스 당 1개씩 보유
    2. Kernel Stack
        
        system call 발생 시 PC가 커널의 코드를 가리키며 함수를 호출 → 커널 함수는 kernel stack에 쌓임
        
        각 프로세스 별로 커널 스택을 별도로 두고 있다.
        

### Process State(상태)

1. Running
    
    CPU를 잡고 instruction을 수행중인 상태
    
2. Ready
    
    CPU를 기다리는 상태(메모리 등 다른 조건을 모두 만족한 상태)
    
3. Blocked(wait, sleep)
CPU를 줘도 당장 instruction을 수행할 수 없는 상태
    
    프로세스 자신이 요청한 event(ex) I/O)가 즉시 만족되지 않아 기다리는 상태
    
    자신이 요청한 event가 만족되면 ready로 넘어감
    
    ex) 디스크에서 file을 읽어와야 하는 경우
    
4. New
    
    프로세스가 생성중인 상태
    
5. Terminated
    
    수행이 끝난 상태
    
6. Suspended(stopped)
    
    외부 이유로 프로세스 수행이 정지됨
    
    프로세스는 통째로 디스크에 swap out된다. 
    
    외부에서 resume 해야 다시 active가 된다. 
    
    ex) 사용자가 프로그램을 일시 정지시킨 경우(break key), 시스템이 여러 이유로 프로세스를 잠시 중단시킴
    

### Process Control Block

프로세스의 메타데이터가 저장되는 공간으로 하나의 PCB 안에 하나의 프로세스 정보가 담겨있다. 

Process Metadata에는 다음과 같은 정보가 있다.

- Process ID: PID(Process Identification Number)
- Process State
- Process Priority(스케줄링 정보, 우선순위)
- CPU Registers
    - accumulator, index register, stack pointers, general purpose registers
- Program counter
- code, data, stack의 위치 정보
- 파일 관련 - open file descriptors

프로그램이 실행되면 → 프로세스가 생성되고 → 프로세스 주소 공간에 (code, data, stack)이 생성 → 이 프로세스의 메타데이터들이 PCB에 저장됨

PCB는 context switching 때문에 필요한데 앞으로 다시 수행할 block 상태의 프로세스 상태값을 PCB에 저장한다.

### Context Switch

- CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정
- CPU가 넘어갈 때 운영체제는 다음과 같은 작업을 함
    - CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB에 저장
    - CPU를 새로 얻는 프로세스 상태를 PCB에서 읽어옴
- 사용자 프로그램에서 사용자 프로그램으로 CPU를 넘겨주는 걸 context switch에 해당하지, 사용자 프로그램에서 운영체제로 CPU를 넘겨주는 것은 해당하지 않음
    
    ⇒ 즉, system call, interrupt 발생했다고 반드시 switch context라고 할 수 없음
    
    단, timer interrupt, i/o 요청 system call 같은 경우 사용자 프로세스에서 kernel mode로 넘어갔다가 다른 사용자 프로세스로 넘어가는 경우는 context switch라고 함
    
- system call 등의 운영체제로 잠시 CPU를 넘기는 경우, CPU 수행 정보 등 context 일부를 PCB에 저장해햐 하나 문맥교환을 하는 경우가 그 부담이 더 큼
- ex) cache memory flush → 문맥 교환 시 그 프로세스가 사용하던 캐시 메모리를 모두 삭제해야 함(오버헤드가 큼)

### 프로세스 스케줄링을 위한  queue

1. job queue
    
    현재 시스템 내에 있는 모든 프로세스의 집합
    
2. ready queue
    
    현재 메모리 내에 있으면서 CPU를 잡아서 실행되길 기다리는 프로세스의 집합
    
3. device queue
    
    I/O device 처리를 기다리는 프로세스의 집합
    

### 스케줄러(Scheduler)

- Long-term scheduler(장기 스케줄러, job scheduler)
    
    시작 프로세스 중 어떤 것들을 ready queue로 보낼지 결정
    
    프로세스에 memory 및 각종 자원을 어디에 줄 지
    
    degree of multiprogramming(메모리에 여러 프로그램이 올라감)을 제어
    
    time sharing system에는 보통 장기 스케줄러가 없음(무조건 ready)
    
- Short-term scheduler(단기 스케줄러, CPU scheduler)
    
    어떤 프로세스를 다음번에 running시킬지 결정
    
    프로세스에 CPU를 주는 문제
    
    충분히 빨라야 함(millisecond 단위)
    
- Medium-term scheduler(중기 스케줄러, Swapper)
    
    여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냄
    
    프로세스에게서 memory를 뺏는 문제
    
    degree of Multiprogrammming을 제어
    
    프로세스 상태에 suspended가 추가함
    

### Thread : CPU의 수행 단위

프로세스 내부에 CPU의 수행 단위가 여러 개인 경우 Thread라고 함

1개의 프로세스 내에 여러 개의 thread(여러 개의 PC, registers, stack)

lightweight process라고도 함( ↔ heavyweight process는 전통적인 개념으로 1개의 스레드를 가진 task를 말함)


### Thread의 구성

1. Program counter
2. register set
3. stack space

Thread 간 공유하는 영역

1. code section
2. data section
3. OS resources

### Thread 사용 장점

1. 응답성 Responsiveness
    
    1개의 스레드가 blocked 상태일 때, 다른 스레드가 CPU를 잡고 running되어 빠른 처리가 가능하다.
    
    예를 들어, 웹 브라우저가 페이지를 불러올 때, 이미지를 불러오는 동안 텍스트를 먼저 띄우는 등
    
    동일한 일을 수행하는 다중 스레드가 협력해 높은 처리율(throughput)과 성능 향상을 얻을 수 있음(자원 공유, 절약)
    
2. Resource Sharing
    
    n개의 스레드는 프로세스의 code, data, resource를 공유한다. 
    
3. 경제성(효율성)
    
    프로세스를 생성하거나 컨텍스트 스위칭이 발생하는 것은 스레드를 생성하거나 스레드 간 전환보다 오버헤드가 크다.
    
    솔라리스의 경우 위 두 가지 오버헤드가 각각 30배, 5배 차이가 발생
    
4. Multi Processor 아키텍처에서 이용(CPU가 여러 개인 경우)
    
    각기 다른 프로세서에서 각 스레드가 병렬적으로 실행될 수 있다. 
    

### kernel thread, user thread, real-time thread

1. 커널이 스레드를 알고 그 스레드를 지원하고 있음 → kernel thread
2. 커널이 해당 스레드를 모르고 라이브러리가 그 스레드를 지원하고 있음 → user thread
3. real-time 기능을 지원하는 스레드 → real-time thread




### 궁금한 점

Q. PCB가 프로세스 관련 커널 자료구조라고 적혀 있는데 어떻게 관리될까?

A. Linked List 방식으로 관리가 된다. PCB List Head에 PCB들이 생성될 때마다 붙게 되는데 주소값으로 연결이 된 연결 리스트 형태로 삽입, 삭제가 용이하다.

프로세스가 생성되면 해당 PCB가 생성되고 프로세스 완료 시 제거된다. 

(수행중인 프로세스를 변경할 때, CPU의 레지스터 정보가 변경되는 것이 Context switch)

Q. Context switch에 발생하는 다른 cost에는 뭐가 있을까?

1. cache memory flust(cache 초기화)
2. Memory mapping 초기화
3. 더 있다면 뭐가 있을까요? ← 모르겠음


### 문제

Q. context switching에 대해서 설명하세요