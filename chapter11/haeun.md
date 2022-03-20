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
