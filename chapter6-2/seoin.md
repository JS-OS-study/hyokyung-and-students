# Process Synchronization - 2

### 고전적인 Process Synchronization 문제점들

1. Bounded-Buffer Problem(Producer-Consumer Problem)
    
    producer 스레드와 consumer 스레드가 있고, 사이에 shared buffer가 있다고 가정
    
    producer : empty buffer에 데이터를 넣음, 둘 이상의 producer가 비어있는 버퍼에 동시 접근할 수 없게 락을 걸어줌
    
    consumer : full buffer에서 데이터를 꺼냄, 둘 이상의 consumer가 동일한 버퍼 데이터를 사용하려하면 락을 걸어줌
    
    synchronization variables(**락을 걸고 푸는 용, 자원 개수 카운팅하는 용으로 세마포어 변수 사용**)
    
    - mutex(mutual exclusion) → binary semaphore가 필요(shared data의 mutual execlusion을 위함)
    - resource count → integer semaphore가 필요(남은 full/empty buffer 수 표시)
    
    Producer
    
    ```c
    do {
    		produce an item in x
    		P(empty);
    		P(mutex);
    		...
    		add x to buffer
    		...
    
    		V(mutext);
    		V(full);
    } while(1);
    ```
    
    Comsumer
    
    ```c
    do {
    		P(full);
    		P(mutex);
    		...
    		remove an item from buffer to y
    
    		V(mutext);
    		V(empty);
    		...
    		comsume the item in y
    		...
    } while(1);
    ```
    
2. Readers-Writers Problem
    - 한 프로세스가 DB에 write 중일 때 다른 프로세스가 접근하면 안됨. (read는 동시 접근 가능)
    - writer는 대기 중인 reader가 1개도 없을 때 DB 접근 허용됨
    - writer가 DB 접근 허가를 얻지 못한 상태에서 reader는 모두 DB 접근 가능
    - 일단 writer가 DB에 접근 중이면 reader는 접근이 금지됨
    - writer가 DB에서 빠져나가야 reader 접근 허용
    
    Shared Data → DB 자체를 의미, readcount(현재 DB에 접근 중인 reader 수)
    
    변수 - DB 접근을 위한 db, 공유 변수 readcount를 접근하는 코드의 mutal exclusion 보장을 위한 mutex
    
    하나의 lock을 사용하면 데이터 일관성을 지킬 수 있지만, 여러 프로세스를 동시에 읽을 수 있음에도 한 프로세스만 읽도록 강제하는 것은 비효율적임
    
    → 현재 수정이 필요한 프로세스가 없다면 모든 대기중인 reader 접근이 가능하고, writer는 reader가 하나도 없는 경우 접근가능함.
    
    이 해결책은 starvation 가능성이 있는데, 계속해서 reader가 들어오거나, writer가 들어올 경우 한 쪽이 계속 수행되지 않을 가능성이 있다.
    
    → 큐에 우선순위를 부여하거나, 일정 시간이 지나면 자동으로 write or read 되게 해서 해결할 수 있음
    

3. Dining-Philosophers Problem
    
    철학자들은 밥을 먹거나, 생각을 하거나 둘 중 하나를 함
    
    젓가락은 공유자원이고, 최초에 모든 값은 1로 한 개 씩 갖고 있음
    
    모든 철학자가 동시에 젓가락을 들 경우, 데드락 상태가 발생할 수 있음(circle wait)
    
    
    → 4명의 철학자만 테이블에 동시에 앉거나
    
    → 젓가락을 두 개 잡을 수 있을 때만 젓가락을 집을 수 있게 함
    
    → 비대칭으로 (짝수-왼쪽 젓가락, 홀수 - 오른쪽 젓가락부터 잡을 수 있게 제한)
    

### Monitor

세마포어의 문제는 1) 코딩이 힘들어 실수하기 쉽고, 2) 정확성 입증이 어려움

 V(mutex)와 P(mutex)의 순서에 따라 데드락이 발생하거나 mutual exclusion이 깨질 수 있다. 이 단점을 보완하는 구조가 모니터다.

모니터는 동시 수행중인 프로세스 사이에서 추상 데이터의 안전한 공유를 보장한다. high-level 동기화 구조로 공유 데이터를 접근하기 위해선 모니터 내부의 procedure를 통해서만 접근할 수 있다.

세마포어와 가장 큰 차이점은 모니터는 락을 걸어줄 필요가 없다는 점

세마포어는 프로그래머가 직접 wait, signal을 사용해 race condition을 해결하나, 모니터는 자체 기능으로 해결할 수 있다. 프로세스가 공유 데이터를 사용하려면 반드시 모니터 내의 procedure를 통해야 하고, 동일한 시간에 오직 1개의 프로세스나 스레드만 모니터에 들어갈 수 있다.

모니터에 진입하려 할 때, 내부에 이미 프로세스가 있으면 진입하지 못하고 모니터 큐에 들어간다. condition variable을 사용해 wait, signal 연산을 위해 사용될 수 있다. 변수가 어떤 값을 갖진 않으면서 자기 큐에 프로세스를 매달아 sleep 시키거나 큐에서 프로세스를 깨우는 역할을 한다.