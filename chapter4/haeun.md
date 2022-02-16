# Process Management
### 생성
- 부모 프로세스가 자식 프로세스를 만듦. (**트리 구조**)
- 프로세스는 자원을 필요로 함 (os로부터 받음, 부모와 공유)


### 복제 생성 (fork, exec)
- 자식 프로세스는 부모의 문맥(주소공간, 운영체제 등 자원, code,data, stack etc.)을 모두 복사
- fork: 자식이 그대로 복사. 일단 복제해놓고 
- exec: 그 공간에 새 프로그램을 올림
- 자원은 공유 하기도, 안 하기도 (보통은 공유 X) 함. 공유를 해야 자원 등 절약이 가능하니까. 리눅스같은 효율적인 운영체제에서는 무작정 카피하지 않음. 스택이 달라지거나 분리가 필요할 때 **Copy-On-Write (cow). 즉, write가 발생할때 카피한다**.

### fork 코드 예시
![C언어 fork](https://user-images.githubusercontent.com/50111853/154212591-5918ba6c-ea96-453a-961b-0fdc3c967d3d.png)
- fork()한 후 반환된 pid가 새로 생기는 자식 프로세스의 pid.
- Pid로 자식/부모 프로세스 구분함. pid가 0인게 자식 프로세스
- 자식 프로세스의 경우도 부모 프로세스의 문맥(especially **프로그램 카운터)을 그대로 받기 때문에 fork()이후 코드부터** 계속 실행함. (무한루프 아님!)


### execlp 코드 예시
![C언어 exec](https://user-images.githubusercontent.com/50111853/154213451-ca76f59a-5c66-4342-ab2c-ab02ff0defa7.png)
- exec으로 덮어써서 execlp 안에 있는 함수를 실행하는 프로세스가 된다.
- 예시의 경우 리눅스 명령어임
- fork 안 하고 그냥 execlp만도 가능함

### wait
![wait 예시](https://user-images.githubusercontent.com/50111853/154214087-90459328-e68e-4712-9650-37727407008a.png)
- 프로세스를 blocked 상태로 바꿔서 잠들게 함
- 부모가 자식 프로세스가 종료되길 기다리려고 sleep

### 종료 (exit)
- 자식이 부모에게 output data를 보냄(via wait)
- 자식프로세스의 각종 자원이 os로 반납됨.

#### 종료 종류
자발적: 마지막 문장 실행 후 exit(), 컴파일러가 자동으로 중괄호 닫히는 곳(main 함수 리턴될때)에 exit 삽입
비자발적: 부모가 자식프로세스 종료(abort), 키보드로 kill or break 클릭, 부모가 종료됨

### 부모가 종료시킴 (abort)
- 자식이 할당 자원의 한계를 넘어선 경우 or 그 태스크가 더이상 필요하지 않은 경우 or 부모 프로세스가 종료되는 경우
- 자식이 무조건 부모 프로세스보다 먼저 죽는다.

fork, exec, wait, exit, abort 다 **시스템 콜**

### 프로세스간 협력
독립적 프로세스:
협력 프로세스:

### 프로세스간 협력 매커니즘 (Interprocess Communication, IPC)
![IPC](https://user-images.githubusercontent.com/50111853/154215397-d639f937-1832-4728-958c-0df27e3e2636.png)

- **shared memory**: 일부 주소공간을 두 프로세스가 공유 (커널한테 그 공간 쓴다고 얘기한 후에는 자기들끼리 알아서)
- **message passing**: 공유변수 일체 없이 **운영체제 커널을 통해** 메시지를 보냄.
- 1. direct: 메시지 받을 프로세스 이름을 직접 명시
- 2. indirect: mailbox 또는 port 이름을 통해 간접 전달

쓰레드는 사실상 프로세스이므로 **동일한 프로세스를 구성하는 쓰레드간 협력 당연히 가능함.**


### CPU and I/O Burst
![스크린샷 2022-02-16 오후 4 22 42](https://user-images.githubusercontent.com/50111853/154215769-6e0246a6-cd25-475f-abd3-c18162082784.png)
프로그램이 실행된다는건 CPU burst+ io burst가 반복되는 것이다.

![스크린샷 2022-02-16 오후 4 24 22](https://user-images.githubusercontent.com/50111853/154216000-0e217f07-43dc-491d-8e92-0c96fd767d79.png)
- IO-bound Job: 사람이 많이 끼어들고 **인터랙션이 많으면** io burst가 잦고 길다 
- CPU-bound Job: 과학 연산 등은 주로 CPU를 많이 쓰고 CPU burst가 길다 

-> **인터랙션이 많은 IO bound 잡한테 CPU를 많이 줘야 사용자가 안 답답함** -> CPU 스케줄링의 주요한 이슈

프로세스는 io bound / cpu bound 프로세스 두개로 나뉜다.

### 질문
