# Lecture

---

- [운영체제 - 반효경 교수님](http://www.kocw.or.kr/home/cview.do?mty=p&kemId=1046323)
  - File Systems
  - File Systems Implementation 1
  - File Systems Implementation 2

<br />

# File and File System

---

- 파일
  - 이름이 있는 정보의 묶음
  - 일반적으로 비휘발성인 보조 기억 장치(HDD, SSD)에 저장
  - 운영 체제는 다양한 저장 장치를 파일이라는 동일한 논리적 단위로 볼 수 있게 해 줌
  - 연산자(커널 함수, 이느 모두 시스템 콜을 동반한다)
    - create, read, write, reposition(lseek), delete, open, close 등
    - reposition(lseek): 위치를 변경 및 저장
    - open: 파일의 메타데이터를 메모리에 탑재
- 파일의 속성(메타데이터)
  - 파일 자체의 내용이 아니라 파일을 관리하기 위한 각종 정보
    - 파일의 이름, 유형, 저장된 위치, 파일 사이즈
    - 접근 권한(읽기/쓰기/실행), 시간(생성/변경/사용), 소유자 등
- 파일 시스템
  - 운영 체제에서 파일을 관리하는 부분
  - 파일 및 파일의 메타데이터, 디렉토리 정보 등을 관리
  - 파일의 저장 방법 결정
  - 파일 보호 등

<br />

# Directory and Logical Disk

---

- 디렉토리 
  - 파일의 메타데이터 중 일부를 보관하고 있는 일종의 특별한 `파일`
  - 그 디렉토리에 속한 파일 이름 및 파일의 속성
  - 연산자
    - search for a file, create a file, delete a file
    - list a directory, rename a file, traverse the file system
- 파티션 (=논리 디스크)
  - 하나의 물리 디스크 안에 여러 파티션을 두는 것이 일반적
  - 여러 개의 물리 디스크를 하나의 파티션으로 구성하기도 함
  - 물리 디스크를 파티션으로 구성한 뒤 각각의 파티션에 파일 시스템을 깔거나 스와핑(page 관련) 등 다른 용도로 사용할 수 있음

<br />

# `Open()`

---

![image](https://user-images.githubusercontent.com/71188307/158370138-d7b27c81-feb4-4761-92c1-bc6ae3c59068.png)

<br />

- open(/a/b/c)
  - 디스크로부터 파일 c의 메타데이터를 메모리로 가지고 옴
  - 이를 위하여 directory path를 탐색
    - 루트 디렉토리 `/` 를 open하고 그 안에서 파일 위치 `a` 의 위치를 획득
    - 파일 `a` 를 open한 후 read하여 그 안에서 파일 `b` 의 위치를 획득
    - 파일 `b` 를 open한 후 read하여 그 안에서 파일 `c` 의 위치를 획득
    - 파일 `c` 를 open
  - Directory path의 탐색에 너무 많은 시간 소요
    - 그래서 open을 read/write와 별도로 둠
    - 한 번 open한 파일은 read / write시 directory 탐색이 불필요
  - Open file table
    - 현재 open된 파일의 in memory 메타데이터 보관소
    - 디스크의 메타데이터보다 몇 가지 정보가 추가됨
      - open한 프로세스의 수
      - file offset: 파일 어느 위치를 접근 중인지 표시 (별도 테이블 필요)
  - File descriptor
    - Open file table에 대한 위치 정보 (프로세스 별)

<br />

## `Open()` 동작 과정

---

![image](https://user-images.githubusercontent.com/71188307/158371573-eceb904e-2975-45ae-a6bc-898c6f0f30c4.png)

<br />

1. root의 위치는 고정이기 때문에 즉시 알 수 있다. 디스크에 있던 root의 메타데이터가 메모리에 올라감 
2. 메모리에 있는 root의 주소를 찾고, root에 존재하는 a의 메타데이터에 접근
3. a의 메타데이터를 메모리에 올림 (Open file table)
4. 메모리에 있는 a의 주소를 찾고, a에 존재하는 b의 메타데이터에 접근
5. b의 메타데이터를 메모리에 올림 (Open file table)
6. 메모리에 올라온 b의 메타데이터를 가르키는 포인터 값(file descriptor)를 open() 함수의 결과로 반환
7. 프로세스가 `open()` 함수의 반환 값을 전달 받음
8. `read()` 함수가 호출되면, open() 호출시 반환 받은 file descriptor 값을 이용하여 b의 컨텐츠를 읽고, 읽은 컨텐츠를 디스크 버퍼 캐시에 저장
9. 디스크 버퍼 캐시에 있는 b의 컨텐츠를 커널 메모리의 버퍼 캐시에 저장
10. 커널 메모리 버퍼 캐시에 있는 b의 컨텐츠를 사용자 메모리의 버퍼 캐시에 저장

<br />

- 파일 시스템에서는 메모리때와 다르게 LRU, LFU와 같은 알고리즘을 사용한 버퍼 캐싱이 가능하다
- 메모리 관리는 하드웨어들이 직접 하였기 때문에 운영체제가 관여할 수 없었지만, 파일 시스템의 경우 시스템 콜을 통해 진행이 되기 때문에, 운영체제가 관여할 수 있는 부분이 많다

<br />

# File Protection

---

![image](https://user-images.githubusercontent.com/71188307/158372059-1979c17e-1fe1-4419-8686-31d316047d05.png)

<br />

## Mounting of File System

---

![image](https://user-images.githubusercontent.com/71188307/158372128-b8597b49-d0d2-4169-b209-f2d549d02e1f.png)

<br />

- 각각의 파티션에 별도의 파일 시스템을 설치할 수 있다
- 한 파티션의 파일 시스템에서 다른 파티션의 파일 시스템에 접근하고 싶다면?

<br />

![image](https://user-images.githubusercontent.com/71188307/158372366-af06ede3-3b9f-4909-b238-8af701ab5c62.png)

<br />

- 마운팅을 통해 서로 다른 파티션에 있는 파일 시스템에 접근할 수 있다

<br />

# Access Method

---

![image](https://user-images.githubusercontent.com/71188307/158372593-b99041d7-9746-48b6-9915-9b13b52293b4.png)

<br />

# Allocation of File Data in Disk

---

- 모든 파일의 크기는 불규칙적이다
- 파일을 동일한 크기로 쪼개어 디스크에 sector(512byte) 단위로 저장한다
- 프로세스를 여러개의 페이지로 나눠 관리하던 메모리 관리를 떠올리면 이해가 쉽다
- 디스크에 파일 데이터를 할당하는 방식은 세가지가 존재한다
  - Contiguous Allocation
  - Linked Allocation
  - Indexed Allocation

<br />

## Contiguous Allocation

---

하나의 파일이 디스크 sector에 연속해서 저장되는 방식

<br />

![image](https://user-images.githubusercontent.com/71188307/158373315-eb9a904b-2144-42ed-86bd-02f8c7aed764.png)

<br />

- 장점
  - 매우 빠른 I/O
    - 디스크의 처리 시간은 디스크 헤드가 디스크 트랙의 특정 sector로 이동하는데 걸리는 시간이 전부
    - 파일이 연속적으로 위치하기 때문에 파일의 첫 sector에 한번만 seek/rotation하면 사실상 끝난다
    - 빠른 속도로 인해 Realtime 파일 용, 혹은 프로세스의 스와핑용으로 매우 적합(공간 효율성보다 시간 효율성이 중요한 시스템)
  - Direct Access(Random Access)가 가능
- 단점
  - 외부 단편화 발생
  - File grow가 어려움
    - 파일 생성시 얼마나 큰 hole을 할당할 것인가
    - gorw 가능 vs 낭비(내부 단편화 발생)

<br />

## Linked Allocation

---

하나의 파일이 불연속적인 디스크 sector에 나누어 저장되고, 각 sector가 하나의 노드가 되어 연결리스트 구조로 저장되는 방식

<br />

![image](https://user-images.githubusercontent.com/71188307/158374550-2291671b-b297-4e82-888b-b7059a1e4629.png)

<br />

- 장점
  -  외부 단편화 발생 안함
- 단점
  - 연결리스트 구조상 반드시 첫 요소부터 차례대로 읽어야 하므로 Random Access 불가
    - Reliability 문제
      - 한 sector가 고장나 다음 sector를 가리키는 포인터가 유실되면 많은 부분을 유실함
    - 포인터를 위한 공간(4byte)이 필요하므로 공간 효율성을 떨어뜨리게 된다 (512byte/sector - 4byte/pointer = 508byte/remaining sector)
  - 변형(Linked Allocation의 개선 버전)
    - File-allocation table (FAT) 파일 시스템
      - 포인터를 별도의 위치에 보관하여 신뢰성 문제와 공간 효율성 문제를 해결한 시스템

<br />

## Indexed Allocation

---

파일이 어디에 나눠져 있는지 인덱스를 기록 해 두는 sector(index block) 하나를 만들어 활용하는 방식

<br />

![image](https://user-images.githubusercontent.com/71188307/158375525-aa7d7fd5-e026-4c7e-92aa-e6a8da7e6b14.png)

<br />

- 장점
  - 외부 단편화가 발생하지 않음
  - Random Access 가능
- 단점
  - 작은 파일의 경우 공간이 낭비 됨(실제로 많은 파일들이 작다)
  - 매우 큰 파일의 경우 하나의 인덱스 블록이 부족할 수 있다
    - Linked Scheme
    - Multi-level Index

<br />
