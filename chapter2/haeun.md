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

**이때 만약 io 작업없이 CPU 무한루프 도는 프로그램이 있다면? 그 프로그램이 cpu 독점하게 될 수도 있음**
** 이걸 막기 위해 존재하는 하드웨어가 '타이머'**

## 타이머 (역할: Time sharing)
- 컴 켜면 os가 cpu 가지고 있다가 **타이머 값을 세팅한 후** 사용자프로그램한테 넘겨줌. 프로그램은 그 시간동안만 cpu 사용가능
- 만약 할당된 시간 끝나면 씨피유한테 타이머가 인터럽트 보냄 + cpu사용권이 사용자프로그램에서 os로 넘어감
  - 사용자프로그램도 io device 접근 불가 . os를 통해서만 접근가능

## interrupt
-  io device buffer에 인풋 들어옴-> io controller가 "버퍼에 입력 들어왔다"하고 CPU한테 io interrupt 보냄 -> CPU 제어권이 OS로 넘어감 -> os가 io buffe의 데이터 본인 메모리에 카피



## 질문
q. 영상1 9:55에 일을 시킨다고 했는데 cpu-memory까지만 간다. 이떄 cpu->io device에 일을 시키는 과정/연관된 장비는 무엇인가? (line 15 쯤과 관련)
