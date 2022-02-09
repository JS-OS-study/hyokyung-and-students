# CH02

## system call

운영체제는 kernal mode(mode bit 0), user mode(mode bit 1)로 나눠 구동된다. 프로그램 구동에 있어 파일을 읽고 쓰는 등의 많은 부분이 kernal mode를 사용한다. system call은 커널 영역의 기능을 user mode에서 사용할 수 있게, 즉 프로세스가 하드웨어에 직접 접근해 필요한 기능을 사용할 수 있게 한다.

system call은 커널이 자신의 자원을 보호하기 위해 만든 함수의 집합으로 user mode에 제공되는 interface로 볼 수 있다. 


      
### system call 시 매개변수를 전달하는 방법


1. CPU 레지스터 내에 전달, 매개변수 개수가 CPU 내의 총 레지스터 개수보다 많을 수도 있음 → 매개변수는 메모리에 저장하고 메모리 주소가 레지스터에 전달
2. 매개변수는 프로그램에 의해 stack으로 push 될 수도 있음


### system call의 유형

1. 프로세스 제어(Process Control)
    1. end, abort, load, execute, create process, wait time, malloc, free, ...
2. 파일 조작(File Manipulation)
    1. create file, delete file, open, close, read, wirte, ...
3. 장치 관리(Devide Management)
    1. request devices, release device, 장치의 논리적 부착, 분리, ...
4. 정보 유지(Information Maintenance)
    1. time, date, 프로세스 파일, 장치 속성 획득 및 설정 ...
5. 통신(Communication)
    1. 메시지 송수신, 공유 메모리 기법에선 메모리 접근을 위한 system call, ...
    

### **Privilege Level**

어떤 시점의 CPU의 권한 상태, 어떤 명령을 실행시킬 수 있고, 메모리의 어떤 범위에 접근할 수 있는지를 결정

0이 권한이 가장 많고 3이 가장 적다. 레벨이 높은 쪽에서 낮은 쪽의 권한을 시도하면 운영체제에서 그 실행을 막거나 해당 프로그램을 다운시킨다. 

ex) Intel x86 프로세서에서는 레벨 0과 레벨3(Ring 0(Kernal mode), Ring 3(User Mode)만을 사용하고 있다. 

일반적으로 CPU는 **Privilege Level 0에서 운영체제를 실행** 하고 **Privilege Level 3에서 응용 프로그램을** 실행시킨다.

레벨에 따라 실행시킬 수 있는 명령어가 다른데 예를 들어 CPU의 동작 정지를 가능하게 하는 HLT 명령은 0 레벨만 가능하다. 0 레벨이 아니면 실행되지 않는 명령들을 **Privileged Instruction** 라고 한다.

명령어 뿐 아니라 메모리 영역에도 접근할 수 있는 부분이 다르다. CPU는 4GB의 주소 공간을 세그먼트로 분할해 관리할 수 있다. 각 세그먼트에 접근할 수 있는 CPU의 레벨을 정하는 **Descriptor Privilege Level** 을 설정할 수 있다.(0~3의 값을 갖음)

![protection ring](https://www.researchgate.net/profile/William-Caelli/publication/27463209/figure/fig1/AS:310063190298629@1450936233126/The-Intel-x86-Ring-Architecture.png)

### ? 궁금한 점

Q. hwp 프로세스가 excel 프로세스에 있는 foo() 함수를 호출해 서비스를 제공받을 수 있을까?

A. 기본적으로는 프로세스간 function 호출은 가능하지 않음 → 운영체제는 모든 프로세스가 각자 자기 자신의 프로세스 영역에서만 작업할 수 있게 해둠(다른 프로세스의 메모리를 읽어올 수 없음)

(IPC(Inter Process Communication)를 이용해서 우회해 할 수도 있다고 함)

http://www.intel.co.kr/content/www/kr/ko/architecture-and-technology/64-ia-32-architectures-software-developer-vol-3a-part-1-manual.html