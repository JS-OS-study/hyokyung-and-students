# Virtual Memory

전적으로 운영체제가 관여함

### Demand Paging

(요청이 있으면 해당 페이지를 메모리에 올림)

메모리 관리 기법 중 페이징 관리를 사용하는 것으로 가정(실제로도 대부분 페이징 기법을 사용함)

페이징 기법은 프로세스를 구성하는 주소 공간의 페이지를 전부 메모리를 한꺼번에 올리는 것이 아니라 Demand Paging 기법을 사용함

**실제로 필요할 때 페이지를 메모리에 올리는 것.**

장점

- I/O 양의 감소
- 메모리 사용량 감소
- 빠른 응답 시간
- 더 많은 사용자 수용

→ Valid/ Invalid bit의 사용

- Invalid 의 의미
    - 사용되지 않는 주소 영역인 경우
    - 페이지가 물리적 메모리에 없는 경우
- 처음에는 모든 page entry가 invalid로 초기화
- address translation 시에 invalid bit이 set되어 있으면
    
    → “page fault”
    

주소 변환을 통해 물리적 메모리를 얻을 수 있는 경우만 valid로 표시됨

CPU가 논리 주소를 주고 메모리 몇 번지를 보겠다 하면 먼저 주소 변환을 하러 옴. 주소 변환 시 invalid라면 이 페이지가 메모리에 없다는 얘기임. 이 때는 그 페이지를 디스크에서 메모리로 올려야함(i/o 작업으로 사용자 프로세스가 할 수 없는 일임)

이 경우 page fault라는 현상이 발생해 요청한 페이지가 메모리에 없는 것을 의미하며 CPU는 자동적으로 운영체제로 넘어가게 된다.

운영체제가 fault난 페이지를 메모리로 올리는 작업을 하게 된다.

### Page Fault

- invalid page를 접근하면 MMU가 trap을 발생시킴(page fault trap)
- Kernel mode로 들어가서 page fault handler가 invoke됨
- 다음과 같은 순서로 page fault를 처리
    1. Invalid reference? (eg. bad address, protection violation) ⇒ abort process. 
        
        트랩에 걸려 cpu가 운영체제로 넘어감
        
    2. Get an empty page frame.(없으면 뺏어온다: replace)
    3. 해당 페이지를 disk에서 memory로 읽어옴
        1. dis i/o가 끝나기까지 이 프로세스는 CPU를 preempt 당함(block)
        2. Disk read가 끝나면 page tables entry 기록, valid/invalid bit = ‘valid’
        3. ready queue에 process를 insert → dispatch later
    4. 이 프로세스가 CPU를 잡고 다시 running
    5. 아까 중단된 instruction을 재개

### Performance of Demand Paging

- page fault rate 0 ≤ p ≤ 1.0
    - if p = 0 no page fault
    - if p = 1, every reference is a fault
- Effective Access Time = (1-p) x memory access + p(OS & HW page fault overhead + [swap page out if needed] + swap page in + OS & HW restart overhead)

### Free frame이 없는 경우

- page replacement
    - 어떤 프레임을 뺏어올지 결정해야 함
    - 곧바로 사용되지 않을 페이지를 쫓아내는 것이 좋음
    - 동일한 페이지가 여러 번 메모리에서 쫓겨났다가 다시 들어올 수 있음
- replacement algorithm
    - page-fault rate을 최소화하는 것이 목표
    - 알고리즘 평가
        - 주어진 page reference string에 대해 page fault를 얼마나 내는지 조사
    - reference string의 예
        - 1,2,3,4,1,2,5,1,2,3,4,5
    

### Optimal Algorithm

- MIN(OPT) : 가장 먼 미래에 참조되는 페이지를 replace
- page fault를 가장 적게하는 알고리즘
- 미래의 참조를 어떻게 아는가?
    - Offline algorithm(미래를 알고 있다고 가정하고 운영)
- 다른 알고리즘의 성능에 대한 upper bound 제공
    - Belady’s optimal algorithm, MIN, OPT 등으로 불림

4 frames example

6번의 page faults가 발생(**빨간 숫자**)

<img width="518" alt="image" src="https://user-images.githubusercontent.com/84627144/158050951-80cbea81-d729-4774-8342-724668aa9d24.png">

### FIFO Algorithm

먼저 들어온 것을 먼저 내쫓음

보통 메모리 크기를 늘리면 성능이 좋아져야 하는데 FIFO 알고리즘은 오히려 성능이 떨어지는 경우가 발생하기도 함. 이걸 FIFO Anomaly라고 한다.

잘 사용하지 않음

<img width="518" alt="image" src="https://user-images.githubusercontent.com/84627144/158050955-a4949335-c3dc-468f-ba4f-b1884bfccca0.png">

### LRU(Least Recently Used) Algorithm

LRU: 가장 오래 전에 참조된 것을 지움

<img width="511" alt="image" src="https://user-images.githubusercontent.com/84627144/158050957-21113aec-46c3-428b-91f2-750ade0d9b8b.png">

### LFU(Least Frequently Used) Algorithm

- LFU: 참조 횟수(reference count)가 가장 적은 페이지를 지움
    - 최저 참조 횟수인 page가 여럿 있는 경우
        - LFU 알고리즘 자체에서는 여러 페이지 중 임의로 선정함
        - 성능 향상을 위해 가장 오래 전에 참조된 page를 지우게 구현할 수도 있다.
    - 장단점
        - LRU처럼 직전 참조 시점만 보는 것이 아니라 장기적인 시간 규모를 보기 때문에 page의 인기도를 좀 더 정확히 반영할 수 있음
        - 참조 시점의 최근성을 반영하지 못함
        - LRU보다 구현이 복잡함
        

<img width="493" alt="image" src="https://user-images.githubusercontent.com/84627144/158050964-ab141af0-04f8-44f3-876e-6d21b72d4341.png">

### LRU와 LRU 알고리즘의 구현

<img width="504" alt="image" src="https://user-images.githubusercontent.com/84627144/158050981-4df38c45-fae5-4cc3-abee-4c555ad1bb77.png">

LRU

한 줄로 세우고 위에가 가장 예전에 참조된 페이지, 아래쪽이 가장 최근에 참조한 페이지. 어떤 페이지가 다시 참조되면 아래쪽으로 이동시킴

LFU

참조 횟수로 한 줄로 줄을 세움. 어떤 페이지가 다시 참조됐다고 바로 맨 아래로 이동하는게 아니라 밑에 있는 것과 한 칸씩 비교하면서 내려감. 극단적인 케이스엔 O(n)의 구현이 됨. 그래서 heap 자료구조를 이용해 트리 형태로 만들어서 O(log n)으로 복잡도를 줄임(자손들보다는 참조 횟수가 적게 트리 구조를 만듦)

### 다양한 캐싱 환경

- 캐싱 기법
    - 한정된 빠른 공간(=캐시)에 요청된 데이터를 저장해 두었다가 후속 요청시 캐시로부터 직접 서비스하는 방식
    - paging system 외에도 cache memory, buffer caching, web caching 등 다양한 분야에서 사용
- 캐시 운영의 시간 제약
    - 교체 알고리즘에서 삭제할 항목을 결정하는 일에 지나치게 많은 시간이 걸리는 경우 실제 시스템에서 사용할 수 없음
    - Buffer caching이나 web caching의 경우
        - O(1)에서 O(log n) 정도까지 허용
    - paging system인 경우
        - page fault인 경우에만 OS가 관여함
        - 페이지가 이미 메모리에 존재하는 경우 참조시각 등의 정보를 OS가 알 수 없음
        - O(1)인 LRU의 list 조작조차 불가능
        

LRU, LFU 알고리즘이 여기서 사용되진 않음(?)

실제 사용되는 알고리즘은 Clock Algorithm

### Clock Algorithm

- LRU의 근사 알고리즘
- 여러 명칭으로 불림
    - second chance algorithm
    - NUR(Not Used Recently) or NRU(Not Recently Used)
- Reference bit을 사용해 교체 대상 페이지 선정(circular list)
- reference bit가 0인 것을 찾을 때까지 포인터를 하나씩 앞으로 이동
- 포인터 이동하는 중에 reference bit 1은 모두 0으로 바꿈
- Reference bit가 0인 것을 찾으면 그 페이지를 교체
- 한 바퀴 되돌아와서도(=second chance) 0이면 그 때에는 replace 당함
- 자주 사용되는 페이지라면 second chance가 올 때 1
- 개선
    - reference bit과 modified bit(dirty bit)을 함께 사용
    - reference bit = 1 : 최근 참조된 페이지
    - modified bit = 1 : 최근에 변경된 페이지(i/o를 동반하는 페이지)
    

### Page Frame의 Allocation

- allocation problem: 각 프로세스에 얼마만큼의 페이지 프레임을 할당할 것인가?
- allocation의 필요성
    - 메모리 참조 명령어 수행시 명령어, 데이터 등 여러 페이지 동시 참조
        - 명령어 수행을 위해 최소 할당되어야 하는 프레임 수가 있음
    - Loop를 구성하는 페이지들은 한꺼번에 allocate되는 것이 유리함
        - 최소한의 allocation이 없으면 매 루프마다 page fault
- allocation scheme
    - equal allocation: 모든 프로세스에 똑같은 개수 할당
    - proportional allocation: 프로세스 크기에 비례해 할당
    - priority allocation: 프로세스의 priority에 따라 다르게 할당
    

### Global cs Local Replacement

- Global replacement
    - replace 시 다른 프로세스에 할당된 프레임을 빼앗아 올 수 있다
    - 프로세스별 할당량을 조절하는 또 다른 방법임
    - FIFO, LRU, LFU 등 알고리즘을 global replacement로 사용시에 해당
    - working set, PFF 알고리즘 사용
- Local replacement
    - 자신에게 할당된 프레임 내에서만 replacement
    - FIFO, LRU, LFU 등의 알고리즘을 프로세스 별로 운영시
    

### Thrashing

- 프로세스의 원활한 수행에 필요한 최소한의 페이지 프레임 수를 할당 받지 못한 경우 발생
- page fault rate가 매우 높아짐
- cpu utilizaiton이 낮아짐
- OS는 MPD(Multiprogramming degree)를 높여야 한다고 판단
- 또 다른 프로세스가 시스템에 추가됨(higher MPD)
- 프로세스 당 할당된 프레임의 수가 더욱 감소
- 프로세스는 page의 swap in/swap out으로 매우 바쁨
- 대부분의 시간에 CPU는 한가함
- low throughput

### Working-Set Model

- Locality of reference
    - 프로세스는 특정 시간 동안 일정 장소만 집중적으로 참조한다
    - 집중적으로 참조되는 해당 페이지들의 집합을 locality set이라 한다.
- Working-set Model
    - Locality에 기반해 프로세스가 일정 시간 동안 원활하게 수행되기 위해 한꺼번에 메모리에 올라와 있어야 하는 page들의 집합을 working set이라 정의
    - Working Set 모델에서는 프로세스의 working set 전체가 메모리에 올라와 있어야 수행되고 그렇지 않은 경우 모든 프레임을 반납한 후 swap out(suspend)
    - thrashing을 방지함
    - Multiprogramming degree를 결정함
    

### Working-Set Algorithm

- working set의 결정
    - working set window를 통해 알아냄

<img width="510" alt="image" src="https://user-images.githubusercontent.com/84627144/158050989-0c96a8a7-648b-463b-8d4d-1e8740af52c4.png">

- working-set algorithm
    - process들의 working set size의 합이 페이지 프레임의 수보다 큰 경우
        - 일부 프로세스를 swap out시켜 남은 프로세스의 working set을 우선 충족시킴(MPD를 줄임)
    - working set을 다 할당하고도 page frame이 남는 경우
        - swap out 되었던 프로세스에게 working set을 할당(MPD를 키움)
    - working size delta
        - working set을 제대로 탐지하기 위해 window size를 잘 결정해야 함
        - delta 값이 너무 작으면 locality set을 모두 수용하지 못할 우려
        - delta 값이 크면 여러 규모의 locality set 수용
        - delta 값이 무한대면 전체 프로그램을 구성하는 페이지를 working set으로 간주

### PFF(Page-Fault Frequency) Scheme

<img width="502" alt="image" src="https://user-images.githubusercontent.com/84627144/158050993-20ae0003-4cfe-4db9-b7b3-1b18514e8a9c.png">

### Page size의 결정

- page size를 감소시킬 경우
    - 페이지 수 증가
    - 페이지 테이블 크기 증가
    - internal framentation 감소
    - disk transfer의 효율성 감소
        - seek/rotation vs transfer
    - 필요한 정보만 메모리에 올라와 메모리 이용이 효율적
        - locality의 활용 측면에서는 좋지 않음
        
- 최근 트렌드
    - page size를 키우고 있음