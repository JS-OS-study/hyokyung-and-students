# File and File System

### File and File System

- File
    - A named collection of related information
    - 일반적으로 비휘발성의 보조기억장치에 저장
    - 운영체제는 다양한 저장 장치를 file이라는 동일한 논리적 단위로 볼 수 있게 해줌
    - Operation
        - create, read, write, reposition(lseek), delete, open, close 등
- File attribute(혹은 파일의 metadata)
    - 파일 자체의 내용이 아니라 파일을 관리하기 위한 각종 정보들
        - 파일 이름, 유형, 저장된 위치, 파일 사이즈
        - 접근 권한(읽기, 쓰기, 실행), 시간(생성, 변경, 사용), 소유자 등
- File System
    - 운영체제에서 파일을 관리하는 부분
    - 파일 및 파일의 메타데이터, 디렉토리 정보 등을 관리
    - 파일의 저장 방법 결정
    - 파일 보호
    

### Directory and Logical Disk

- Directory
    - 파일의 메타데이터 중 일부를 보관하고 있는 일종의 특별한 파일
    - 그 디렉토리에 속한 파일 이름 및 파일 attribute들
    - operaiton
        - search for a file, create a file, delete a file
        - list a directory, rename a file, traverse the file system
- Partition(=Logical Disk)
    - 하나의 (물리적) 디스크 안에 여러 파티션을 두는게 일반적
    - 여러 개의 물리적인 디스크를 하나의 파티션으로 구성하기도 함
    - (물리적) 디스크를 파티션으로 구성한 뒤 각각 파티션에 file system을 깔거나 swapping 등 다른 용도로 사용할 수 있음

### open()

파일의 메타데이터를 디스크에 올려놓는 것

- open(”/a/b/c”)
    - 디스크로부터 파일 c의 메타데이터를 메모리로 올려 놓는 작업
    - 이를 위해 directory path를 search
        - 루트 디렉토리 “/”를 open하고 그 안에서 파일 “a”의 위치 획득
        - 파일 “a”를 open한 후 read해 그 안에서 파일 “b”의 위치 확인
        - 파일 “b”를 open한 후 read해 그 안에서 파일 “c”의 위치 확인
        - 파일 “c”를 open
    - open도 system call에 해당
    - Directory path의 search에 너무 많은 시간이 소요
        - open을 read/write와 별도로 두는 이유
        - 한 번 open한 파일은 read/write 시 directory search 불필요
    - Open file table
        - 현재 open된 파일들의 메타데이터 보관소(in memory)
        - 디스크의 메타데이터보다 몇 가지 정보가 추가
            - open한 프로세스의 수
            - File offset: 파일 어느 위치 접근 중인지 표시(별도 테이블 필요)
    - File descriptor(file handle, file control block)
        - open file table에 대한 위치 정보(프로세스 별)

<img width="511" alt="image" src="https://user-images.githubusercontent.com/84627144/158566744-94c7724b-4747-4dcf-8504-ed477f461564.png">

open() - retrieves metadata from disk to main memory

1. 사용자 프로그램이 system call(open())
2. cpu 제어권이 운영체제로 넘어감
3. 운영체제가 root의 metadata를 먼저 메모리에 올림
4. root의 content에서 a의 metadata를 찾아 메모리에 올림
5. a의 content에서 b의 metadata를 찾아 메모리에 올림
6. 시스템 콜 끝(fd = open(”/a/b”)) (여기서 fd는 file descriptor(일종의 인덱스))
7. fd에 b의 fd 값이 담김
8. read(fd) 실행
    1. 다시 시스템 콜 실행
9. b의 content를 읽어서 사용자 프로그램에 직접 주는게 아니가 일단 운영체제의 메모리 영역에 읽어두고 그걸 카피해서 사용자 프로그램에 전달함.
    1. 이를 버퍼 캐싱이라고 함
    2. 요청 내용이 버퍼에 있든 없든 일단 cpu 제어권은 운영체제에 넘어감

- file descriptor table은 프로세스별로 존재
- open file table은 글로벌하게 1개 있음
- 구현에 따라서 테이블이 여러 종류가 될 수도 있음
- 메타데이터가 디스크가 아닌 메모리에 올릴 때 추가적으로 offset 값(이 값이 어디에 위치하는지)을 운영체제가 갖고 있어야함. 그러다보니 offset을 별도로 두는 테이블이 생기고 그것과 무관한 테이블이 생기게 됨

### File Protection

- 각 파일에 대해 누구에게 어떤 유형 접근을 허락할 것인지?(read/write/execution)
- Access Control 방법
    - Access control Matrix
        - Access control list: 파일별로 누구에게 어떤 접근 권한이 있는지 표시
        - Capability: 사용자별로 자신이 접근 권한을 가진 파일 및 해당 권한 표시
        - 파일별로 모두 테이블을 만드면 낭비가 될 수 있음.
    - Grouping
        - 전체 user를 owner, group, public의 세 그룹으로 구분
        - 각 파일에 대해 세 그룹의 접근 권한(rwx)을 3비트씩으로 구분
        - 일반적인 운영체제가 선택한 방법
        - ex) UNIX
    - Password
        - 파일마다 password를 두는 방법(디렉터리 파일에 두는 방법도 가능)
        - 모든 접근 권한에 대해 하나의 password: all or nothing
        - 접근 권한별 password: 암기 문제, 관리 문제

### File System의 Mounting

- 하나의 물리적 디스크를 파티셔닝을 통해 여러개의 논리적 디스크로 나눌 수 있음
- 각각의 논리적 디스크에 파일 시스템을 설치해 사용할 수 있음
- root file system - 특정 운영체제에 대해 파일 시스템 1개가 접근이 가능한데 다른 파티션에 있는 파일 시스템에 접근하려 할 때 어떻게 해야할까?
    - 그걸 제공하기 위한 연산으로 Mounting이란 연산이 있음
    - 루트 파일 시스템의 특정 디렉토리 이름에다가 또 다른 파티션의 파일 시스템을 마운트해줌
    - 그 마운트된 디렉토리에 접근하면 다른 파티션의 파일 시스템에 접근할 수 있게 함

### Access Methods

- 시스템이 제공하는 파일 정보의 접근 방식
    - 순차접근(sequential access)
        - 카세트 테이프를 사용하는 방식처럼 접근
        - 읽거나 쓰면 offset은 자동적으로 증가
    - 직접접근(direct access, random access)
        - LP 레코드 판과 같이 접근하도록 함
        - 파일을 구성하는 레코드를 임의의 순서로 접근할 수 있음



# File System Implementations

## Allocation of File Data in Disk

- Contiguous Allocation(연속 할당)
- Linked Allocation(링크를 둔 연결 할당)
- Indexed Allocation(인덱스를 둔 할당)

### Contiguous Allocation(연속 할당)

<img width="488" alt="image" src="https://user-images.githubusercontent.com/84627144/158566949-5428e942-e2a9-47af-a042-581db7904766.png">

- 동일한 크기의 섹터 단위로 나누어 저장(논리적인 block이라 부름)
- 임의의 크기의 파일을 동일 크기의 블록 단위로 나누어 저장
- 하나의 파일이 디스크 상에 연속해서 저장이 되는 방식 - 저장되는 모습을 보면 인접한 블록이 연속해서 저장되는 것을 사진에서 확인할 수 있음

단점

- external framentation 외부 조각 발생 가능(free block 발생)
- File grow가 어려움(파일 크기를 키우는데에 제약이 있음)
    - 파일 생성시 얼마나 큰 hole을 배당할 것인지? (미리 큰 파일을 대비해 할당해 놓을 수도 있음 그럼 얼마나 큰 hole을 할당할 건지? 더 큰 파일이 생기면? 등 당장 사용하지 않는 공간 낭비도 존재)
    - grow 가능 vs 낭비(internal fragmentation)

장점

- Fast I/O
    - 한 번의 seek/rotation으로 많은 바이트 transfer
    - Realtime file 용으로, 또는 이미 run 중이던 process의 swapping용으로 사용
- Direct access(=random access) 가능

### Linked Allocation

<img width="512" alt="image" src="https://user-images.githubusercontent.com/84627144/158567050-e577577d-9bca-403e-a0c6-dd1d2f166a06.png">

그 다음 위치를 각각 기록해둠

장점

- 외부 조각 발생 안 함

단점

- Direct access(=random access) 불가능(no random access)
- Realiablity 문제
    - 한 sector가 고장나 pointer가 유실되면 많은 부분을 잃음
    - bad sector가 발생 시 그 다음 위치부터 모두 접근이 불가능해짐
- Pointer를 위한 공간이 block의 일부가 되어 공간 효율성을 떨어뜨림
    - 512 bytes/sector, 4 bytes/pointers
    

Linked Allocation의 변형

- File-allocation table(FAT) 파일 시스템
    - 포인터를 별도의 위치에 보관해 reliablity와 공간 효율성 문제 해결
    

### Indexed Allocation

<img width="509" alt="image" src="https://user-images.githubusercontent.com/84627144/158567100-d630b34b-5547-4196-8572-b4d349710473.png">

인덱스를 가리키게 하고, 인덱스 블록 하나에 위치 정보를 모두 열거함

장점

- 외부 조각 발생 안함
- direct access 가능

단점

- 작은 파일이라도 무조건 2개 블록이 필요해 공간 낭비(실제로 많은 file들이 작음)
- Too Large file의 경우에는 하나의 블록으로 index를 저장하기 부족함
    - 해결 방안
        - linked scheme
        - multi-level index
        - 또 다른 인덱스 블록을 만듦

## UNIX 파일시스템의 구조

<img width="524" alt="image" src="https://user-images.githubusercontent.com/84627144/158567148-7fb56751-7b27-455e-8e99-99a4b8d9f663.png">

위의 사진이 기본 구조임

- 유닉스 파일 시스템의 중요 개념
    - Indexed Allocation을 갖다 씀 + 변형
    - Boot block
        - 부팅에 필요한 정보(bootstrap loader)
    - Superblock
        - 파일 시스템에 관한 총체적인 정보를 담고 있음
    - Inode
        - 파일 이름을 제외한 파일의 모든 메타 데이터를 저장
        - 파일 하나랑 Inode 1개를 할당
        - 파일 이름은 디렉토리가 직접 갖고 있다(디렉토리엔 file 이름, Inode 번호를 갖고 있음)
    - Data block
        - 파일의 실제 내용을 보관

## FAT File System

<img width="520" alt="image" src="https://user-images.githubusercontent.com/84627144/158567209-7ab171cd-0e3b-4827-8da3-83cbc409ad7d.png">

- bad sector가 있더라도 FAT에 담겨 있어 Realiablity 개선
- FAT 데이터는 중요하므로 여러 copy

## Free-Space Management

- Bit map or bit vector
    - Bit map은 부가적인 공간을 필요로 함
    - 연속적인 n개의 free block을 찾는데 효과적

- Linked List
    - 모든 free block들을 링크로 연결(free list)
    - 연속적인 가용공간을 찾는 것은 쉽지 않음
    - 공간 낭비가 없음
    - 실제 사용하긴 어려운 방법
    
- Grouping
    - linked list 방법의 변형
    - 첫 번째 free block이 n개의 pointer를 가짐
        - n-1 pointer는 free data block을 가리킴
        - 마지막 pointer가 가리키는 block은 또 다시 n pointer를 가짐
        
- Counting
    - 프로그램들이 종종 여러 개의 연속적인 block을 할당하고 반납한다는 성질에 착안
    - first free block, # of contiguous free blocks)을 유지

## Directory Implementation

- Linear list
    - <file name, file의 metadata>의 list
    - 구현이 간단
    - 디렉토리 내에 파일이 있는지 찾기 위해서는 linear search 필요(time-consuming)
- Hash Table
    - linear list + hashing
    - Hash table은 first name을 이 파일의 linear list의 위치로 바꿔줌
    - search time을 없앰
    - Collision 발생 가능
- File의 metatdata의 보관 위치
    - 디렉토리 내에 직접 보관
    - 디렉토리에는 포인터를 두고 다른 곳에 보관
        - inode, FAT 등
- Long file name의 지원
    - <file name, file의 meatdata>의 list에서 각 entry는 일반적으로 고정 크기
    - file name이 고정 크기의 entry 길이보다 길어지는 경우 entry의 마지막 부분에 이름의 뒷부분이 위치한 곳의 포인터를 두는 방법
    - 이름의 나머지 부분은 동일한 directory file의 일부에 존재
    

## VFS and NFS

- Virtual File System(VFS)
    - 서로 다른 다양한 file system에 대해 동일한 시스템 콜 인터페이스(API)를 통해 접근할 수 있게 해주는 OS의 layer
- Network File System(NFS)
    - 분산 시스템에서는 네트워크를 통해 파일이 공유될 수 있음
    - NFS는 분산 환경에서의 대표적인 파일 공유 방법임

## Page Cache and Buffer Cache

- Page Cache
    - Virtual memory의 paging system에서 사용하는 page frame을 caching의 관점에서 설명하는 용어
    - Memeory-Mapped I/O를 쓰는 경우 file의 I/O에서도 page cache 사용
- Memory-Mapped I/O
    - 파일의 일부를 virtual memory에 mapping시킴
    - 매핑시킨 영역에 대한 메모리 접근 연산은 파일의 입출력을 수행하게 함
- Buffer Cache
    - 파일시스템을 통한 I/O 연산은 메모리의 특정 연산인 buffer cache 사용
    - file 사용의 locality 활용
        - 한 번 읽어온 블록에 대해 후속 요청 시 buffer cache에서 즉시 전달
    - 모든 프로세스가 공용으로 사용
    - Replacement algorithm 필요(LRU, LFU 등)
- Unified Buffer Cache
    - 최근의 OS에서는 기존의 buffer cache가 page cache에 통합됨
    

## Page Cache and Buffer Cache

<img width="521" alt="image" src="https://user-images.githubusercontent.com/84627144/158567254-5cc66305-a9f1-4c19-b85e-5d8d80827b14.png">

- Unified Buffer Cache를 이용하지 않는 File I/O
    - page cache가 분리되어 있음
- Unified Buffer Cache를 이용한 File I/O
    - page cache 자체가 사용자 프로그램에 매핑이 되어 있음