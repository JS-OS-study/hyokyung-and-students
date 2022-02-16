# Process Management

### 프로세스 생성과 수행

- 부모 프로세스(parent process)가 자식 프로세스(children process)를 생성
- 프로세스의 트리(계층 구조) 형성
- 프로세스 실행에는 자원이 필요 → 운영체제가 할당
    - 부모와 자식이 모든 자원을 공유하는 모델
    - 일부 공유하는 모델
        - 원칙은 공유하지 않는 것이나 자식 프로세스가 부모 프로세스를 복제해서 생성하면 사실상 같은 내용이 2개 생성되어 메모리 낭비가 생김
        - 자원을 공유하다가 후에 각자 프로세스의 작업이 달라지게 되면 부모와 공유하던 일부 메모리를 카피하기도 함(일부 자원 공유)
        - ⇒ copy-on-write(COW) 기법이라고 함 → write가 발생하기 전까진 부모의 자원을 공유하고 write가 발생 시 필요한 일부만 copy함(공유하는 것도 일부 존재)
    - 전혀 공유하지 않는 모델(부모와 자식 프로세스가 경쟁) → 원칙적
- 생성할 때는 부모 프로세스의 주소 공간, 운영체제의 data(자원, PCB, PC register 등)을 자식 프로세스가 복제함
    
    복제 후 새로운 프로그램으로 덮어씌움(2단계)
    
    유닉스의 예시
    
    - fork() system call이 새로운 프로세스를 생성
        - 부모를 그대로 복사(OS data except PID + binary)
        - 주소 공간을 할당
    - fork 후 exec() system call을 통해 새로운 프로그램을 메모리에 올림
    
    ⇒ 사용자 프로세스가 프로세스를 생성하는 것이 아니라 system call을 통해 운영체제가 새로운 프로세스를 생성함
    
- 수행(Execution)
    - 부모와 자식이 공존해 수행되는 모델
    - 자식이 종료될 때까지 부모가 기다리는(wait(blokced)) 모델

### 프로세스 종료

- 프로세스가 마지막 명령 수행 후 운영체제에 이를 알림(exit, 자발적 종료)
    - 자식이 부모에게 wait system call을 통해 output data를 보냄
    - 프로세스의 각종 자원들을 운영체제에 반납
- 부모 프로세스가 자식의 수행을 종료시킴(abort, 비자발적 종료)
    - 자식이 할당 자원의 한계치를 넘어섬
    - 자식에게 할당된 task가 더이상 필요하지 않음
    - 부모가 종료(exit)하는 경우
        - 운영체제는 부모 프로세스가 종료하는 경우 자식도 수행 종료시킴
        - 단계적인 종료

### fork() system call

- caller의 복제본인 새로운 address 공간을 생성한다

```c
int main()
{ int pid;
	pid = fork(); // 자식 프로세스 생성
// 자식 프로세스는 fork() 이후 코드부터 실행됨 
// -> 부모 프로세스의 PC를 복제하기 때문에 이 위치를 알고 있어서 가능함
	

// 부모 프로세스는 fork()의 결과값으로 pid가 양수, 자식 프로세스는 pid로 0을 받음
	if(pid == 0) // 자식과 부모를 구분하기 위해 pid 이용 -> return value 달라짐
		printf("\n Hello, I am child!\n");
	else if (pid > 0)
		printf("\n Hello, I am parent!\n");
}
```

### exec() system call

- caller의 메모리 이미지를 새로운 프로그램으로 대체함

```c
int main()
{ int pid;
	pid = fork(); 
	if(pid == 0)
{		printf("\n Hello, I am child!\n");
		execlp("/bin/date", "/bin/date", (char *) 0);
}
	else if (pid > 0)
		printf("\n Hello, I am parent!\n");
}
```

- 반드시 자식 프로세스를 만들고 exec()를 해야하는 것은 아님

```c
int main()
{ 
		printf("1");
		execlp("echo", "echo", "3", (char *) 0);
		printf("3"); // 영원히 실행 안됨
}
```

### wait() system call

- parent 프로세스가 wait() system call을 호출하면
    - 커널은 child가 종료될 때까지 parent 프로세스를 sleep시킴(block 상태)
    - Child process가 종료되면 커널은 parent 프로세스를 깨움(ready)
- 리눅스 예시
    - 프로그램을 입력하는 쉘도 하나의 프로세스인데, 쉘에 어떤 프로세스를 실행시키려고 입력하면 입력한 프로세스는 쉘의 자식 프로세스가 되고 쉘은 sleep 되고, 그 동안 쉘에 다른 프로그램을 입력할 수 없게 되며 자식 프로세스가 실행됨


### exit() system call

- 프로세스의 종료
    - 자발적 종료
        - 마지막 statement 수행 후 exit() system call을 통해 실행
        - 프로그램에 명시적으로 적지 않아도 main 함수가 리턴되는 위치에 컴파일러가 넣어줌
    - 비자발적 종료
        - 부모 프로세스가 자식 프로세스를 강제 종료시킴
            - 자식 프로세스가 한계치를 넘어서는 자원 요청
            - 자식에 할당된 태스크가 더 이상 필요하지 않음
        - 키보드로 kill, break 등을 친 경우
        - 부모가 종료하는 경우 → 부모 종료 전 자식이 먼저 종료됨

### 프로세스와 관련된 시스템 콜

- fork() - create a child(copy)
- exec() - overlay new image
- wait() - sleep until chidl is done
- exit() - frees all the resources, notify parent

### 프로세스 간 협력

- 독립적 프로세스(Independent process)
    - 프로세스는 각자의 주소 공간을 가지고 수행되므로 원칙적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못함
- 협력 프로세스(Cooperation process)
    - 프로세스 협력 메커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있음
- 프로세스 간 협력 메커니즘(IPC: Interprocess Communication)
    - 메시지를 전달하는 방법
        - message passing: **커널을 통해** 메시지 전달
            - 프로세스 사이에 공유 변수(shared variable)를 일체 사용하지 않고 통신하는 방법
            - Direct Communication
                - 통신하려는 프로세스의 이름을 명시적으로 표시
            - Indirect Communication
                - mailbox(or port)를 통해 메시지를 간접 전달(통신하려는 프로세스의 이름을 표시하지 않음)
    - 주소 공간을 공유하는 방법
        - shared memory: 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 하는 shared memory 메커니즘이 있음
            - 이것도 system call을 통해 shared memory를 쓰겠다고 커널에 요청을 함 → 이후엔 프로세스 간의 일
        - thread: thread는 사실상 하나의 프로세스이므로 프로세스 간 협력으로 보기 어려우나, 동일한 process를 구성하는 thread들 간에는 주소 공간을 공유하므로 협력이 가능