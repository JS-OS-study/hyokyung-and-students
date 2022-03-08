# Memory Management - 2

### Multilevel Paging and Performance

- address space가 더 커지면 다단계 페이지 테이블 필요
- 각 단계의 페이지 테이블이 메모리에 존재하므로 logical address의 physical address 변환에 더 많은 메모리 접근이 필요
- TLB를 통해 메모리 접근 시간을 줄일 수 있음
- 4단계 페이지 테이블을 사용하는 경우
    - 메모리 접근 시간이 100ns, TLB 접근 시간이 20nsdlrh
    - TLB hit ratio가 98%인 경우
        - effective memeory access time = 0.98 x 120 + 0.02 * 520 = 128 nanoseconds
        - 결과적으로 주소 변환을 위해 28ns만 소요(다단계 페이지 테이블 접근이 생각보다 시간이 엄청 걸리진 않음)

Valid(v) / Invalid(i) Bit in a Page Table

valid - 실제로 메모리에 해당 페이지가 올라와 있음

invalid - 이 페이지가 프로그램에서 사용되지 않거나, 항상 메모리에 올라가 있는 것은 아님을 표시(하드디스크의 backing store에 있는 상태)

### Memory Protection

page table의 각 엔트리마다 아래 bit를 둔다.

1. valid - 해당 주소의 frame에 그 프로세스를 구성하는 유효한 내용이 있음(접근 허용)
2. invalid - 해당 주소의 frame에 유효한 내용이 없음을 뜻함(접근 불허) - 프로세스가 그 주소 부분을 사용하지 않는 경우, 해당 페이지가 메모리에 올라와 있지 않고 swap area에 있는 경우
3. protection - page에 대한 접근 권한(read/write/read-only), 다른 프로세스가 접근하는 것을 막는다는 의미는 아님(애초에 불가). 어떤 연산에 대한 접근 권한을 제한하는 것임.

### Inverted Page Table

- page table이 매우 큰 이유
    - 모든 프로세스별로 그 logical address에 대응하는 모든 page에 대해 page table entry가 존재
    - 대응하는 페이지가 메모리에 있든 아니든 간에 page table에는 엔트리로 존재
- page table의 공간을 줄이기 위해 사용
    - 문제는 시간적인 오버헤드가 발생함(전부 서치를 해야 해당 페이지가 어떤 프레임에 있는지 알 수 있음)
    - 논리적인 페이지 외에 어떤 프로세스에 해당하는 것인지도 같이 저장(pid + p)
    - CPU가 logical address(pid+p+d)를 page table로 줌 → page table에서 pid + p를 쭉 search → 찾아진 위치가 위에서부터 어디에 위치한 entry인지 확인한 후 전달
    
- Inverted page table
    - page frame 하나당 page table에 하나의 entry를 둔 것(system-wide)
    - 각 page table entry는 각각의 물리적 메모리의 page frame의 담고 있는 내용 표시(process-id, process의 logical address(p))
    - 단점
        - 테이블 전체를 탐색해야함
    - 조치
        - associative register 사용(expensive)

### Shared Page

- Re-entrant Code(Pure code)
- read-only로 해 프로세스 간에 하나의 코드만 메모리에 올림(eg, text editors, compilers, window systems)
- Shared code는 모든 프로세스의 logical address space에서 **동일한** 위치에 있어야함
- Priavte code and data
    - 각 프로세스들은 독자적으로 메모리에 올림
    - private data는 logical address space의 아무곳에 와도 무방
    

### Segmentation

- 프로그램은 의미 단위인 여러 개의 segment로 구성
    - 작게는 프로그램을 구성하는 함수 하나하나를 세그먼트로 정의
    - 크게는 프로그램 전체를 하나의 세그먼트로 정의가능
    - 일반적으로는 code, data, stack 부분이 하나씩의 세그먼트로 정의됨
- segment는 다음과 같은 logical unit들 - main(), function, global variables, stack, symbol table, arrays
    
    

### Segmentation Architecture

- Logical address는 다음의 두 가지로 구성
- Segment table
    - each table entry has:
        - base - starting physical address of the segnet
        - limit - length of the segment
            
            → 의미 단위로 자르기 때문에 segment의 길이가 균일하지 않음 → segment길이를 table에 같이 갖고 있음
            
- Segment-table base register(STBR)
    - 물리적 메모리에서의 segment table의 위치
- Segment-table length register(STLR)
    - 프로그램이 사용하는 segment의 수
        
        segment number s is legal if s < STLR
        

페이징 기법은 페이지 크기가 균일해 offset의 크기가 페이지 크기로 결정됨

segmentation은 segment 크기가 다 다르고, offset은 미리 결정되어야 함 → 세그먼트 길이는 offset으로 표현할 수 있는 길이를 넘지 못함(segment길이를 제한하는 요소가 offset)

### Segmentation Architecture(Cont.)

- Protection
    - 각 세그먼트 별로 protection bit가 있음
    - Each entry:
        - Valid bit = 0 ⇒ illegal segment
        - Read/Write/Execution 권한 bit
- Sharing
    - shared segment
    - same segment number
    - segment는 의미 단위이기 때문에 공유와 보안에 있어 페이징보다 훨씬 효과적
- Allocation
    - first fit/ best fit
    - external fragmentation 발생
    - segment의 길이가 동일하지 않으므로 가변분할 방식에서와 동일한 문제점들이 발생
    

### Segmentation

프로세스 주소를 의미 단위인 segment 단위로 나눠 관리하는 것

table의 엔트리 개수가 프로그램이 사용하는 세그먼트 개수로 정해짐.

총 세그먼트 수 → 총 엔트리 개수

세그먼트가 물리적 메모리 어디에 올라와 있을까?

세그먼트 테이블의 엔트리로 가면 시작위치(base) 값에 얼만큼 떨어져 있는지(offset)를 더해주면 물리적 메모리 공간을 찾아 주소 변환이 가능하다.

세그먼트는 크기가 균일하지 않기 때문에(의미 단위로 자르기 때문), 세그먼트의 길이가 얼마인지가 세그먼트 테이블에 같이 담고 있다. 

세그먼트 안에서 얼마만큼 떨어져있는지의 값이 세그먼트의 길이보다 크다면 적절하지 않은 메모리 참조임

그걸 방지하기 위해서 세그먼트 내에서 얼마만큼 떨어져있는지를 나타내는 offset값이 세그먼트 길이보다 작은 값인지 확인을 하고 그렇지 않으면(offset이 세그먼트 길이보다 크면) trap을 발생시켜 메모리 참조를 못하게 함.

연속할당기법에서는 프로그램 시작 위치를 base register에 담고, 길이를 length 레지스터에 담았음

페이징 기법에선 페이지 테이블의 시작 위치와 페이지 길이를 담고 있었음

세그먼테이션에서는 테이블의 시작 위치(STBR)와 프로그램이 사용하는 세그먼트의 수(STLR)가 담김

페이지 낭비가 심한 쪽은 세그멘테이션보다 페이징기법임

### Segmentaion with Paging

pure segmentation과의 차이점

- segment-table entry가 segment의 base address를 가지고 있는 것이 아니라 segment를 구성하는 page table의 base address를 갖고 있음
- segmentation의 hole이 생기는 문제가 없어짐 - allocation 문제 해결
    - 메모리에 올라가는 건 페이지 단위로 올라감
- 의미 단위로 올라가는 공유나 보안 같은건 segment table 레벨에서 처리
- 두 가지 방법의 장점을 모두 누릴 수 있음
- original segmentation 기법을 쓰는 시스템은 별로 없음 내부에서 paging을 같이 사용한다고 함
- 주소 변환은 2단계를 거쳐야 함. segment table를 통해 page table base(시작 위치)가 나옴
- 세그먼트 당 page table이 존재하게 됨

### 챕터 정리

이 챕터는 메모리 관리 중 물리 메모리 관리 중점임

이 챕터에서 운영체제의 역할은?

프로세스의 논리적인 주소를 물리적인 주소로 변환해 참조하는 것

주소 변환에 있어서 운영체제의 역할은?

없음!! 전부 하드웨어가 해주는 역할임. MMU 부터 시작해서, segmentation, paging 등등 모두 일련의 과정에서 하드웨어적으로 해결됨

왜 그럴까?

프로세스가 CPU를 잡고 실행하면서 프로세스의 주소를 요청

메모리 참조를 해야하는데, 어떤 프로세스가 CPU를 잡고 있으면서 메모리 접근을 하는 것은 운영체제의 도움을 받지 않음.

주소 변환을 할 때마다 운영체제가 중간에 개입해야 한다면, CPU가 운영체제로 넘어가야함.

이건 말도 안되는 비효율:(

매 CPU 클럭 사이클마다 instruction을 읽어서 CPU에서 실행하고 주소 변환을 통해서 메모리 접근이 이뤄지는데 이때마다 운영체제로 CPU를 넘겼다가 다시 받는 것은 비효율적이고.

결국 주소 변환은 하드웨어적으로 해결되어함

운영체제가 접근하는건 메모리가 아닌  I/O 장치에 접근할 때!!

사용자 프로세스가 직접 I/O 접근을 못하기 때문에 그 때 운영체제에 요청을 함.