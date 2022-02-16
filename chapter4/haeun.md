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
- 주로 부모가 자식 프로세스가 종료되길 기다리게 하려고 wait 시킴

### 종료 (exit)
- 컴파일러가 자동으로 중괄호 닫히는 곳에 exit 삽입
- 자식이 부모에게 output data를 보냄(via wait)
- 자식프로세스의 각종 자원이 os로 반납됨.


### 부모가 종료시킴 (abort)
- 자식이 할당 자원의 한계를 넘어선 경우 or 그 태스크가 더이상 필요하지 않은 경우 or 부모 프로세스가 종료되는 경우
- 자식이 무조건 부모 프로세스보다 먼저 죽는다.

fork, exec, wait, exit, abort 다 **시스템 콜**


### 질문
