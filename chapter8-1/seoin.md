# Memory Management

logical address(=virtual address)

- 각 프로세스마다 독립적으로 가지는 주소 공간
- 각 프로세스마다 0번지부터 시작
- CPU가 보는 주소는 logical address

physical address

- 메모리에 실제 올라가는 위치

주소 바인딩: 주소를 결정하는 것

symbolic address → logical address → physical address

logical → physical 시점이 언제일까?

- Compile time binding
    - 물리적 메모리 주소가 컴파일 시 알려짐
    - 시작 위치 변경시 재컴파일
    - 컴파일러는 absolute code 생성
    - 지금의 컴퓨터 환경에선 사용되지 않음
- Load time binding
    - Loader의 책임하에 물리적 메모리 주소 부여
    - 컴파일러가 재배치가능한코드(relocatable code)를 생성한 경우 가능
    - 프로그램이 시작된 후 메모리에 올라갈 때, 물리적인 메모리 주소가 결정됨
    - 컴파일 시점에는 논리적 주소까지만 결정된 상태
- Execution time binding(=Run time binding)
    - 수행이 시작된 이후에도 프로세서의 메모리 상 위치를 옮길 수 있음
    - 실행 시에 물리적 메모리 주소가 결정되는 것은 load time binding과 동일하나 주소가 실행 중에 변경될 수 있음
    - CPU가 메모리 주소를 참조할 때마다 바인딩을 체크(address mapping table)
    - CPU가 바라보는 주소는 logical address임(위에서도 언급)
    - 하드웨어적인 지원이 필요(e.g. base and limit register, MMU)
    - 현재의 컴퓨터 환경은 run time binding을 지원함
    

**MMU(Memory Management Unit)

- Logical address를 physical address로 매핑해주는 hardware device

운영체제 및 사용자 프로세스 간의 메모리 보호를 위해 사용하는 레지스터

- Relocation register(=base register) : 접근할 수 있는 물리적 메모리 주소의 최소값
- Limit register : 논리적 주소의 범위

Dynamic Relocation

CPU가 logical address를 들고 요청하면 MMU의 relocation register(=base register), limit register 2개를 이용해 주소 변환함.

base register에 시작 위치 저장, limit register는 프로그램의 크기를 저장하고 있음(악의적인 프로그램일 시, 메모리 주소를 넘어선 위치를 요청할 수 있음

MMU scheme

- 사용자 프로세스가 CPU에서 수행되며 생성해내는 모든 주소값에 대해 base register의 값을 더함

user program

- logical address 만을 다룸
- 실제 physical address를 볼 수 없음

Dynamic Loading

- memory utilization의 향상
- 프로세스 전체를 메모리에 미리 다 올리는 것이 아니라 해당 루틴이 불려질 때 메모리에 load하는 것
- 간헐적으로 사용되는 코드의 경우 유용
    - 예) 오류 처리 루틴
- 운영체제의 특별한 지원 없이 프로그램 자체에서 구현 가능(OS는 라이브러리를 통해 지원 가능)

** Loading : memory로 올리는 것

Overlays

- 메모리에 프로세서의 부분 중 실제 필요한 정보만을 올림
- 프로세스의 크기가 메모리보다 클 때 유용
- 운영체제의 지원 없이 사용자(프로그래머)에 의해 구현
- 작은 공간의 메모리를 사용하던 초창기 시스템에서 수작업으로 프로그래머가 구현
    - Manual Overlay
    - 프로그래밍이 매우 복잡
    

Swapping

- 프로세스를 일시적으로 메모리에서 backing store로 쫓아냄(하드디스크)

Backing store(=swap area)

- 디스크
    - 많은 사용자의 프로세스 이미지를 담을만큼 충분히 빠르고 큰 저장 공간

Swap in/ Swap out

- 일반적으로 중기 스케줄러(swapper)에 의해  swap out시킬 프로세스선정
- priority-based CPU scheduling algorithm
    - priority가 낮은 프로세스를 swapped out시킴
    - prioirity가 높은 프로세스를 메모리에 올려 놓음
- compile time 혹은 load time binding에서는 원래 메모리 위치로 swap in해야 함
- execution time binding에서는 추후 빈 메모리 영역 아무 곳에나 올릴 수 있음
- swap time은 대부분 transfer time(swap되는 양에 비례하는 시간)임

Dynamic Linking

- linking을 실행 시간(execution time)까지 미루는 기법
- static linking
    - 라이브러리가 프로그램의 실행 파일 코드에 포함됨
    - 실행 파일의 크기가 커짐
    - 동일한 라이브러리를 각각의 프로세스가 메모리에 올리므로 메모리 낭비(eg. printf 함수의 라이브러리 코드)
- Dynamic linking
    - 라이브러리가 실행시 연결됨 - shared library(dll, shared object ...)
    - 라이브러리 호출 부분에 라이브러리 루틴의 위치를 찾기 위한 stub이라는 작은 코드를 둠
    - 라이브러리가 이미 메모리에 있으면 그 루틴의 주소로 가고 없으면 디스크에서 읽어옴
    - 운영체제의 도움이 필요
    

### Allocation of Physical Memory

메모리는 일반적으로 두 영역으로 나눠 사용

- OS 상주 영역
    - interrupt vector와 함께 낮은 주소 영역 사용
- 사용자 프로세스 영역
    - 높은 주소 영역 사용
    
- 사용자 프로세스 영역의 할당 방법
    1. contiguous allocation
        - 각각의 프로세스가 메모리의 연속적인 공간에 적재되도록 하는 것
        - fixed partition allocation(고정분할방식)
            - 물리적 메모리를 몇 개의 영구적 분할로 나눔
            - 분할의 크기가 모두 동일한 방식과 서로 다른 방식이 존재
            - 분할당 하나의 프로그램 적재
            - 융통성이 없음
                - 동시에 메모리에 load되는 프로그램 수가 고정됨
                - 최대 수행 가능 프로그램 크기 제한
            - Internal, external fragment 발생
            - 외부조각
                - 올리려는 프로그램 크기보다 메모리가 더 작아서 사용이 안된 조각
            - 내부조각
                - 프로그램 크기가 메모리보다 작아서 그 안에서 남는 메모리 조각
        - variable partition allocation(가변분할방식)
            - 프로그램 크기를 고려해 할당
            - 분할의 크기, 개수가 동적으로 변함
            - 기술적 관리 기법 필요
            - external fragment 발생
            
        - Dynamic Storage-Allocation Problem : 가변 분할 방식에서 size n인 요청을 만족하는 가장 적절한 hole을 찾는 문제
            - First-fit
                
                사이즈가 n이상인 것 중 최초로 찾은 hole에 할당
                
            - Best-fit
                
                size가 n 이상인 가장 작은 hole을 찾아서 할당
                
                hole들의 리스트가 크기순으로 정렬되지 않은 경우 모든 hole의 리스트를 탐색
                
                많은 수의 아주 작은 hole들이 생성됨
                
            - Worst-fit
                
                가장 큰 hole에 할당
                
                모든 리스트를 탐색해야 함
                
                상대적으로 아주 큰 hole들이 생성됨
                
            - first fit과 best-fit이 worst-fit보다 속도와 공간 이용률 측면에서 효과적인 것으로 알려짐(실험결과)
            
        - hole
            - 가용 메모리 공간
            - 다양한 크기의 hole들이 메모리 여러 곳에 흩어져 있음
            - 프로세스가 도착하면 수용가능한 hole을 할당
            - 운영체제는 다음 정보를 유지
                - 할당공간
                - 가용공간(hole)
            
        - compaction
            - external framentation 문제를 해결하는 방법
            - 사용중인 메모리 영역을 한군데로 몰고 hole들을 다른 한 곳으로 몰아야 큰 block을 만드는 것
            - 매우 비용이 많이 드는 방법
            - 최소한의 메모리 이동으로 compaction하는 방법(매우 복잡)
            - runtime bind가 지원되어야만 실행 가능
            
    2. Noncontiguous allocation
        - 하나의 프로세스가 메모리의 여러 영역에 분산되어 올라갈 수 있음
        - 현재의 컴퓨터 환경은 불연속 할당방법을 사용함
        - paging
            - process의 virtual memory를 동일한 사이즈의 page 단위로 나눔
            - virtual memeory의 내용이 page 단위로 noncontiguous하게 저장됨
            - 일부는 backing storage에, 일부는 physical memory에 저장
            - basic method
                - physical memory를 동일한 크기의 frame으로 나눔
                - logical memeory를 동일 크기의 page로 나눔(frame과 같은 크기)
                - 모든 가용 frame들을 관리
                - page table을 사용해 logical address를 physical address로 변환(table은 배열로 생각하면 됨)
                - external fragmentation 발생 안함
                - internal fragmentation 발생 가능
                
                ![Untitled](https://user-images.githubusercontent.com/84627144/156912859-a0257002-405b-44fb-8104-6e096b35fa79.png)
                                
                page table은 어디에 들어가있을까?
                
                → page 크기는 4KByte 정도하는데 실제 100만개 정도 필요함
                
                → CPU 안의 레지스터 안은 불가(용량이 크기 때문)
                
                → 메모리에 page table이 있음
                
                Page table은 main memory에 있음
                
                → Page-table base register(PTBR)가 page table을 가리킴
                
                → Page-table length register(PTLR)가 테이블 크기를 보관
                
                모든 메모리 접근 연산에는 2번의 메모리 access가 필요
                
                page table 접근 1번, 실제 data/instruction 에 1번
                
                2번 읽다보니 속도 개선이 필요 → TLB(메모리 주소 변환을 위한 캐시 메모리. 페이지 테이블에서 빈번히 참조되는 데이터 일부 정보를 담음)
                
                Associative register(TLB) : parallel search가 가능
                
                Address translation
                
                - page table 중 일부가 associative register에 보관됨
                - 만약 해당 페이지 #이 associative register에 있는 경우 곧바로 frame #를 얻음
                - 그렇지 않으면 main memory에 있는 page table로 부터 frame#를 얻음
                - TLB는 context switch 때 flush(remove old entries)
            
            - Two-level page table
                - 속도는 줄어들지 않으나 page table을 위한 공간을 줄이기 위함
                - 현대의 컴퓨터는 address space가 매우 큰 프로그램 지원
                - 32bit address 사용시: 2^32(4GB)의 주소 공간
                    - page size가 4K시 1M개의 page table entry 필요
                    - 각 page entry가 4B시 프로세스당 4M의 page table 필요
                    - 그러나, 대부분의 프로그램은 4G의 주소 공간 중 지극히 일부만 사용하기 때문에 page table 공간이 심하게 낭비됨
                    
                    → page table 자체를 page로 구성
                    
                    → 사용되지 않은 주소 공간에 대한 outer page table의 엔트리 값은 NULL(대응하는 inner page table이 없음)
                    
                
                - logical address(on 32-bit machine with 4K page size)의 구성
                    - 20bit의 page number
                    - 12bit의 page offset
                - page table 자체가 page로 구성되기 때문에 page number는 다음과 같이 나눔(각 page table entry가 4B)
                    - 10bit page number
                    - 10bit page offset
                - logical address
                    - P1 → outer page table의 index
                    - P2 → outer page table의 page에서의 변위(displacement)
                
                ![Untitled](https://user-images.githubusercontent.com/84627144/156912877-218d8aea-6e32-4e40-b81e-2a33ad692983.png)
                
                ![Untitled](https://user-images.githubusercontent.com/84627144/156912885-2e727c5a-2846-4fe4-abf7-976b6a01a39c.png)
                
            
            2단계 페이징을 사용하는 이유
            
            → 사실상 공간도 더 많이 씀
            
            → 프로그램을 구성하는 상당 수가 안쓰기도 하는데 page table은 모두 써야함
            
            바깥쪽 페이지 테이블은 전체 논리적 주소에 따라 만들어지나 안쪽 페이지 테이블은 사용되는 것만 만들고 사용되지 않는 프로그램은 안쪽 테이블을 사용되지 않음
            
        - segmentation
        - paged segementation