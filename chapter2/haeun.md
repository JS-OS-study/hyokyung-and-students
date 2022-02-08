# System Structure & Program Execution
컴퓨터 하드웨어의 동작에 대해 배움!

<img src='https://user-images.githubusercontent.com/50111853/152885901-d04bf6f3-fdf2-483c-8bcb-1621ce59cc35.png' width='400px' alt='컴퓨터시스템구조' />
컴퓨터 시스템구조: 컴퓨터+ IO device
컴퓨터: CPU+memory

## CPU (일만 함)
- 역할: 메모리에서 instruction 읽어서 실행
- disk보다 훨배 빠름
- memory에서 instruction 읽어서 실행 (ONLY memory에만 접근. io device에 접근X)
  - scanf 해야된다던가 io device 작업 필요하면? cpu가 inst로 io컨트롤러한테 일 시키고 자기는 다른 inst 실행함 (입력값 버퍼에 들어오면 나한테 연락때려라 그러고 지 할일함. 굉장히 빠른데 일만 한다. 쉬지않음)
- 구성요소
  - register: 메모리보다 빠른 정보 저장공간
  - mode bit: 실행중인게 os인지 유저프로그램인지 구분.
  - interrupt line: cpu는 inst 실행 끝나면 메모리 주소값 증가시켜서 다음 인스트럭션 실행 반복함. 그 사이에 cpu가 다른 일 처리해야하는거 있단걸 알려주는 역할

## OS (CPU 전체적인 통제)
- OS는 대부분 사용자 프로그램이 쓴다.

**이때 만약 io 작업없이 CPU 무한루프 도는 프로그램이 있다면? 그 프로그램이 cpu 독점하게 될 수도 있음** <br/>
**이걸 막기 위해 존재하는 하드웨어가 '타이머'**

## 타이머 (CPU 독점 방지)
- 컴 켜면 os가 cpu 가지고 있다가 **타이머 값을 세팅한 후** 사용자프로그램한테 넘겨줌. 프로그램은 그 시간동안만 cpu 사용가능
- 만약 할당된 시간 끝나면 씨피유한테 타이머가 인터럽트 보냄 + cpu사용권이 사용자프로그램에서 os로 넘어감
  - 사용자프로그램도 io device 접근 불가 . os를 통해서만 접근가능

## interrupt
-  io device buffer에 인풋 들어옴-> io controller가 "버퍼에 입력 들어왔다"하고 CPU한테 io interrupt 보냄 -> CPU 제어권이 OS로 넘어감 -> os가 io buffe의 데이터 본인 메모리에 카피
- 시스템 콜: 사용자 프로그램이 운영체제의 서비스를 받고자 운영체제(커널)을 호출하는 것. 소프트웨어 인터럽트의 일종
  - 방식: 사용자 프로그램이 인터럽트 라인 세팅 (=인터럽트를 건다) -> CPU 제어권이 OS에게 넘어감 
  - 예시: '할 일 다했다~'
- 소프트웨어 인터럽트(트랩) : Exception, System Call
- 하드웨어 인터럽트(인터럽트라고 할때 대부분이 하드웨어인터럽트): 타이머 인터럽트, io controller 인터럽트 등
  - 예시: '할 일 다했다~'- 타이머 인터럽트

<img src='https://user-images.githubusercontent.com/50111853/152990728-1932dd77-8606-4532-8dc0-0c89ced96bf4.png'/>

## 모드빝 ( 사용자가 cpu 악용하는거 제한)
0 모니터모드 커널모드 :  os 코드 수행중. 메모리접근, io 접근 등 다 가능 
1 사용자 프로그램 모드 : 모든 명령은 커널모드에서만 사용가능하므로 사용자 프로그램은 '시스템 콜'로 os에게 IO 요청함. 바로 os에 접근할 수 없으므로 인터럽트를 일으켜 cpu의 제어권을 Os로 보낸다.
 

**만약 io device에서 계속 CPU한테 인터럽트를 걸어서 메모리에 있는 데이터 보낸다면?** <br/>
**cpu가 인터럽트를 너무 많이 당함 -> 비효율적. 그래서 있는게 DMA Controller**

## DMA Controller (Direct Memory Access Controller)
- Memory에 CPU, DMA Ctrl 둘다 접근 가능. 
- CPU 대신 DMA가 io device에게 직접 가서 로컬 버퍼의 데이터를 메모리에 복사해주고 CPU는 최종 인터럽트만 받도록 함
- DMACtrl 덕분에 CPU는 instruction 메모리 주소를 메모리에서 PC(Program Counter) 형태로 받아와서 실행하는데 집중 가능
- DMA가 버퍼에 있는 내용을 메모리로 카피, 모아서 block 단위로 직접 메모리로 전송 & CPU에게 인터럽트

## Memory Controller (교통정리)
- DMACtrl과 CPU가 동시에 특정 메모리에 접근하는 것 막는 역할

### Device Driver
- 소프트웨어. os 코드중 각 장치에 접근하는 처리루틴

### Device Controller
- 하드웨어. io device의 CPU 역할
- local buffer 있음

## Program Counter (프로그램 카운터 라는 레지스터)
CPU는 메모리 주소를 가리키는 레지스터(프로그램카운터레지스터)가 가리키는 메모리 위치에서 인스트럭션을 읽어와실행함
그다음 프로그램카운터레지스터는 다음 주소를 가리킨다. 
인스트럭션 하나가 주로 4바이트. 다음 인스트럭션 주소는 pc가 4 증가.
왠만하면 순차적으로 실행. 함수/if문 쓰루하는 경우 등 다른 메모리주소로 점프해야할 땐 메모리 위치가 점프 

- 명령 끝나고 새로 시작할때마다 인터럽트 라인 확인.
- 인터럽트 벡터: 해당 인터럽트를 처리할 수 있는 처리 루틴(커널 함수) 주소를 가짐

<img src='https://user-images.githubusercontent.com/50111853/152990537-dae6e3fd-93db-4751-b112-a586702e6f93.png' width='600px' />

## 동기식 입출력 synchronous IO
- io 요청 후 입출력이 끝나길 기다린 후 제어가 사용자 프로그램으로 넘어감


## Asynchronous IO
- io 작업 끝나는거 안 기다리고 제어가 사용자 프로그램으로 넘어감 
- e.g. 디스크에서 뭐읽어오는 작업할 경우 그 데이터를 몰라도 할 수 있는 작업을 한다. / 주로 '쓰기'작업

동기비동기 io 둘다 입출력작업 끝난건 인터럽트로 알려줌. 

## IO 방법
- 일반적으로는 메모리, IO device에 접근하는 명령 따로따로 
- memory mapped io: io 장치도 메모리 주소에 연장 주소 붙임


## 저장장치의 계층 구조
CPU-register-cache memory-main memory(d ram)-... 
- 위로 갈수록 속도가 빠르고 비싸 용량이 적음 / 대부분 휘발성
- secondary: cpu가 직접 접근 불가. cpu는 바이트 단위로 접근하는데 세컨더리는 바이트 단위로 불가해 unexecutable
- 캐싱은 재사용을 목적으로함. 미리 위까지 읽어놓으면 아래에서 읽어올 필요없음.
<img src='https://user-images.githubusercontent.com/50111853/152996435-6a71f464-cc76-4a42-9e74-febbe7e0b34f.png' width='500px' />

## 프로그램의 실행
<img src='https://user-images.githubusercontent.com/50111853/152998172-78c42928-fb16-4c96-8120-e2740d736b35.png' width='450px'/>
- 주로 .exe 
- 프로그램을 실행시키면 그 실행파일의 독자적인 주소공간(virtual memory)이 생긴다. 프로그램 종료시 사라짐
- 주소공간(Address space): 코드, 데이터, 스택으로 이루어짐 / 0번지부터 시작
- 이 주소공간을 **물리적인 메모리**에 올려 실행. 근데 주소공간의 전부를 다 올리지 않고 당장 필요한 코드 부분만 물리적인 메모리에 올림
- 어느 부분은 physical memory에 일부는 swap area에 ... 나눠져있기도 함. (virtual memory 기법)
- swap area: 전원 나오면 다 지워지는 부분임. 메모리 연장 공간으로 사용됨.

## 커널 주소공간(버츄얼 메모리)의 내용
<img src='https://user-images.githubusercontent.com/50111853/152998323-c2a47006-f31c-4006-ad9e-93c00e7f101a.png' width='450px'>
- 커널도 하나의 프로그램이므로 주소공간으로 구성돼있음. 
- os는 자원을 효율적으로 관리하는 역할 하므로 관련 된 코드가 code 안에 있음
- os는 인터럽트가 들어오면 cpu를 가지므로 인터럽트, 시스템콜 처리 코드가 code 안에
- os는 cpu, memory, disk 등 하드웨어를 관리함. 관리를 위한 자료구조 필요
- os는 프로세스를 관리. 프로세스 관리 위한 자료구조: PCB(Process Control Block) / 프로세스마다 PCB가 하나씩 만들어짐. 
- 운영체제의 코드는 사용자 프로그램이 막 불러 실행하기 때문에 어떤 사용자 프로그램이 커널에 시스템콜하고 있는지 저장하는 커널 스택도 있음.

## 함수
- 사용자 정의 함수: 내가 만들고 내가 불러서쓰는 함수
- 라이브러리 함수: 누군가 만들어놓은 함수
- > 내 실행 파일 안에 둘다 포함되어있음. **사용자 프로세스의 주소 공간**에 있음
- 커널 함수: os 안에서 정의된 함수. 시스템 콜을 해서 쓸 수 있음. **커널의 주소공간** 안에 들어있음.
- 위2함수->커널 함수 는 메모리 주소가 점프해야하므로 불가능. 그렇기에 커널 함수 호출할땐 시스템콜로 cpu 제어권을 os로 보내서 실행함. 




# 질문
Q. 영상1 9:55에 일을 시킨다고 했는데 cpu-memory까지만 간다. 이떄 cpu->io device에 일을 시키는 과정/연관된 장비는 무엇인가? (line 15 쯤과 관련)
a? data는 io local 버퍼에, inst는 io register에 넣는다고함

Q. 영상2 33:15에서 'cpu 안에 레지스터'라고 했는데 안에 있는거면 ..?
