## disk의 구조

- 로지컬 블럭: 디스크의 외부에서 보는 디스크의 최소 단위 (주소를 가진 1차원 배열처럼 취급)
- 섹터: 디스크를 내부에서 관리하는 최소 단위 (원 모양에서 잘려있는 작은 조각)
  - logical block이 물리적인 디스크에 매핑된 위치 
  - sector 0은 최외곽 실린더의 첫 트랙에 있는 첫번째 섹터

## Disk management

### 물리 formatting (low-level formatting)
- 디스크를 컨트롤러가 읽고 쓸 수 있게 섹터들로 나눔
- 각 섹터는 header + 실제 데이터(보통 512바이트) + trailer로 구성됨
- 헤더와 트레일러는 sector number, ECC(Error correcting code) 등의 정보가 저장되며 controller가 직접 접근 및 운영

### 파티셔닝
- 디스크를 하나 이상의 실린더 그룹으로 나누는 과정
- os는 이걸 독립적 disk로 취급 (logical disk)

### 논리 formatting
- 파일 시스템을 만드는 것
- FAT, inode, free space 등의 구조 포함

### 부팅

- ROM(전원이 꺼져도 정보가 유지됨)에 있는 'small bootstrap loader'를 실행 
- sector 0 (boot block이 있는 곳)을 로드해 실행
- sector 0은 full bootstrap loader program
- os를 디스크에서 load해 실행 (운영체제 커널의 파일을 메모리에 올림)

## disk scheduling

![디스크 스케줄링](https://user-images.githubusercontent.com/50111853/159148036-c372d5a1-4e93-49bb-83b9-56b666e17eb5.png)

- 디스크 스케줄러는 논리 블럭 번호를 보고 스케줄링함.

## FCFS 디스크 스케줄링 알고리즘

![fcfs](https://user-images.githubusercontent.com/50111853/159148126-67014b53-b456-4cae-965d-319f3d59f4a4.png)
- 딱 봐도 헤드가 이동을 많이 하고 비효율적임

## SSTF(Shortest Seek Time First) : 헤드에서 가장 가까운 요청을 먼저 처리

![sstf](https://user-images.githubusercontent.com/50111853/159148152-ae0d187a-24b5-40a2-9afd-21037beca46a.png)
- 현재 헤드에서 가까운 순서대로 요청 처리. 디스크 헤더의 이동 거리가 줄어들지만 starvation 우려가 있다.

## SCAN에 기반한 디스크 스케줄링. (엘리베이터 스케줄링 방식이라고도 함)

![scan](https://user-images.githubusercontent.com/50111853/159148209-6229b149-51f2-46ac-8f0b-d31673125e01.png)

- 큐에 어떤 요청이 들어왔는지에 상관없이 디스크 암이 항상 한쪽 끝에서 다른쪽으로 이동하며 가는 길목에 있는 모든 요청을 처리
- 다른 한쪽에 도달하면 역방향으로 이동하며 다시 이동.
- 문제: 실린더 위치에 따라 대기 시간이 다르다. (가운데 부분은 항상 대기를 짧게 하는데, 극에 있으면 대기 시간이 길어짐)

## C Scan (Circular Scan)

![스크린샷 2022-03-20 오후 1 30 01](https://user-images.githubusercontent.com/50111853/159148224-f807cbe2-c942-41a4-8202-c1076afd4e77.png)

- 헤드가 한쪽->다른쪽으로 이동. 끝에 도달했으면 요청 처리 않고 곧바로 출발점으로 이동
- SCAN보다 균일한 대기시간을 제공

## N Scan

- 출발하면서 기존에 큐에 있던 요청들은 처리하되, 중간에 들어온 요청은 건너뜀. 그리고 돌아갈때 지난번에 들어온 요청들만 처리함

## Look & C Look

![스크린샷 2022-03-20 오후 1 32 19](https://user-images.githubusercontent.com/50111853/159148289-64d182aa-7f29-47c1-b2e9-6d91e269f42e.png)

- 요청이 없어도 끝까지 가고 보는 Scan, C scan의 단점을 개선.
- 한쪽 방향으로 가다가 요청이 더 없으면 방향을 바꿈
- 위 사진의 경우 183 이상의 요청이 없으니 199까지 안 가고 유턴함

 ## 디스크 스케줄링 알고리즘 결정
 
 - 디스크 스케줄링 알고리즘은 주로 SCAN에 기반한 알고리즘들을 사용함. 룩, 씨룩이 일반적으로 디스크 IO가 많은 시스템에서 효율적
 - 파일 할당 방법에 따라 디스크 요청이 영향을 받음
 - 디스크 스케줄링 알고리즘은 필요할 경우 다른 알고리즘으로 쉽게 교체할 수 있도록 os와 별도의 모듈로 작성되는 것이 바람직

## Swap-Space Management

![swap space](https://user-images.githubusercontent.com/50111853/159148421-51e543d6-8155-40d1-abcf-156941ffb872.png)

- 프로그램 실행을 위한 메모리의 연장공간으로 Swap area로서 디스크를 사용
- 물리적 디스크를 파티셔닝해 로지컬 디스크를 만들수있다. 그럼 운영체제는 각각의 디스크로 간주하고, 파일시스템을 깔거나 swap area로 사용가능.
- swap in out할때 빠르게 해야하는데 디스크 접근 시간의 대부분은 seek time이라 이걸 줄이는게 중요. 스왑 에리아는 공간 효율성보다 속도 효율성이 중요 (공간은 어차피 프로세스 사라지면 다 지워짐) 
- seek time을 줄이기 위해 큰 단위를 순차적으로 할당 (file system은 512 bytes, swap area는 512 kilobytes)

## RAID (Redundant Array of Independent Disks)

- 저렴한 여러 디스크를 묶어서 사용함. 레이드 방식은 여러개다. (중복 저장 또는 분산 저장)

### 사용 목적

- 디스크 처리 속도 향상 :여러 디스크에 내용을 분산 저장해 병렬적으로 읽어옴 (interleaving, striping)
- 신뢰성 향상: 동일 정보를 여러 디스크에 중복 저장. 하나의 디스크가 고장 시 다른 디스크에서 읽어옴 (mirroring, shadowing)
- 단순한 중복 저장이 아니라 일부 디스크에 parity를 저장해 공간의 효율성을 높일 수 있음
