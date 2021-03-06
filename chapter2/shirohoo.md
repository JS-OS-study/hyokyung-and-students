# Lecture

---

- [운영체제 - 반효경 교수님](http://www.kocw.or.kr/home/cview.do?mty=p&kemId=1046323)
  - System Structure & Program Execution 1
  - System Structure & Program Execution 2

<br />

# System Structure & Program Execution

---

이번 챕터는 머릿속에 운영체제의 전체적인 그림을 그리기 위한 챕터로 본 운영체제 강의에서 가장 어려운 부분 중 하나라고 할 수 있다.

<br />

![image](https://user-images.githubusercontent.com/71188307/152781815-721cbda4-a97b-41a0-a0dc-852cde73e3db.png)

<br />

## CPU

---

CPU는 명령어의 인출, 실행을 담당하며, 매 클럭당 `프로그램 카운터(PC)`가 가리키는 메모리 주소에 위치한 `명령어(instruction)`를 `메모리 컨트롤러(Memory Controller)`를 통해 인출하고, 인출 된 명령어를 처리(=연산)한 후 결과를 다시 메모리 컨트롤러에게 알려준다.

또한 CPU는 매번 하나의 명령어를 처리한 후 인터럽트 라인을 체크하여 인터럽트가 있다면 `Mode bit`를 0으로 변경(커널 모드)하며 CPU의 제어권을 사용자 프로세스에서 운영체제로 넘긴다. 

<br />

## Operating System

---

운영체제도 하나의 프로세스로서 메모리에 올라가며, 컴퓨터에 전원이 인가돼있는 동안 항상 메모리에 상주해야만 하는, 운영체제 프로그램에서 가장 중요한 부분(코드들)을 좁은 의미로서의 `커널(Kernel)`이라고 부른다. 그리고 이 좁은 의미의 커널이 일반적으로 컴퓨터 과학 전공에서 말하는 운영체제이기도 하다.

운영체제는 어떻게 해야 컴퓨터의 하드웨어 리소스를 가장 효율적으로 사용할 수 있는가를 제 1목적으로 동작한다.

예를 들어 중요한 프로세스가 CPU를 더 많이 점유하고, 상대적으로 덜 중요한 프로세스가 CPU를 더 적게 점유하도록 하는 CPU 스케쥴링(엘리베이터를 연상하자), 실행되는 프로세스들이 메모리를 낭비하지 않고(메모리 단편화) 효율적으로 사용할 수 있게끔 하는 메모리 관리(테트리스와 하드 디스크 조각 모음을 연상하자) 등을 담당한다.

또한, 인터럽트 발생 시 해당 인터럽트가 정확히 어떤 요인에 의해 발생한 인터럽트인지를 파악하고 CPU에게 적절한 처리를 지시해주는 `인터럽트 서비스 루틴(ISR, Interrupt Service Routine)`을 제공한다.

인터럽트 서비스 루틴은 어려운게 아니고, 코드를 뜯어보면 내부적으로 if 혹은 switch를 통해 구현돼있다.

<br />

## Memory Controller

---

저장장치에는 물리적으로 임의의 공간에 임의의 데이터가 들어있을 뿐이며, 저장장치 스스로 데이터를 읽고 쓸 수 없다. 

따라서 저장장치에 데이터를 읽고 쓰기 위해서는 저장장치의 구조에 대해 빠삭하게 알고 있으면서도 CPU의 요구사항도 이해할 수 있는 중간 계층이 필요한데, 이 중간 계층에 속하는 하드웨어가 메모리 컨트롤러이다.

기본적으로 메모리 컨트롤러는 메모리에 대한 전반적인 관리를 담당하며, 부가적으로 CPU의 요구사항에 맞춰 메모리를 조작하는 역할도 한다.

<br />

- 메모리가 동작할 수 있게 적당한 전압을 인가한다
- 기본적으로 휘발성인 메모리의 데이터가 사라지지 않도록 주기적으로 refresh 한다
- 메모리에 올라와있는 프로그램의 명령어를 CPU로 인출해준다(읽기)
- CPU가 메모리에 데이터 쓰기 요청을 해오면 해당 요청을 받아 메모리에 데이터를 저장한다(쓰기)

<br />

이를 카페에 비유하자면, 카페에는 계속해서 커피만 내리는 커피머신이 있을 것인데 이 커피머신이 CPU라고 볼 수 있으며, 고객의 요청을 받아 처리하는 직원이 메모리 컨트롤러라고 볼 수 있고, 직원에게 요청을 하는 고객이 메모리에 올라와있는 사용자 프로세스라고 볼 수 있겠다.

메모리는 메모리 컨트롤러에 종속적이기 때문에 메모리의 성능이 아무리 좋더라도, 메모리 컨트롤러의 처리속도나 메인보드 메모리 버스의 대역폭이 따라주지 못한다면 메모리의 성능을 온전히 다 사용할 수 없다.

<br />

## Mode bit

---

현재 CPU를 사용하고 있는 프로세스의 권한을 의미하며, Mode bit가 0이라면 커널 모드, 1이라면 사용자 모드이다.

커널 모드라는 것은 CPU를 운영체제가 제어하고 있다는 의미이며, 커널 모드일 경우 컴퓨터의 모든곳에 접근할 수 있다. (즉, 관리자 권한)

사용자 모드라는 것은 CPU를 운영체제가 아닌 다른 프로세스(일반적으로 사용자 프로세스)가 점유하고 있다는 의미이며, 이 경우 CPU는 사용자 프로세스가 할당받은 메모리에만 접근할 수 있다.

이는 기본적으로 사용자 프로세스를 믿을 수 없기 때문에(악성 프로그램 등) 보안을 위한 조치이다.

<br />

## DMA(Direct Memory Access) Controller

---

모든 I/O 장치들이 CPU에 직접적으로 인터럽트를 걸어대면 CPU가 자주 방해받아 오버헤드가 커지기 때문에 DMA 컨트롤러가 I/O 장치들의 인터럽트를 받고 데이터를 메모리에 읽고 쓴 다음(메모리 컨트롤러에 요청), CPU에 인터럽트를 한번 발생시킨다.

즉, 일종의 일괄배치 시스템으로, 비유하자면 삽질을 한번 할 때마다 흙을 삽에 올려둔채로 왔다갔다 하면 삽질하는 것보다 왔다갔다 하는데 체력적인 비용이 더 크게 발생하는데 반해, 삽질을 해서 흙을 퍼 올려 수레에 쌓아둔 후 수레가 가득 찼을 때 수레를 끌고 한 번 다녀오면 전자의 경우에 비해 더 삽질에 집중할 수 있게 된다.

<br />

## Timer

---

우리가 사용하는 컴퓨터는 동시에 여러개의 프로그램이 실행되는 것처럼 보인다.

하지만 실제로 하나의 CPU는 동시간대에 정확히 하나의 일만을 하고있다.

단지, 하는 일을 수시로 바꿔가면서 하고 있을 뿐이며, 이 작업이 너무 빠르기 때문에 동시에 실행되고 있는 것 처럼 보이는 것이다. (동시성)

![image](https://user-images.githubusercontent.com/71188307/147462440-ad1aacc3-6b98-41aa-937a-9df5b45f24f1.png)

<br />

예를 들어 커다란 담벼락을 3분할하여 각각 `빨간색`, `초록색`, `파란색` 페인트를 칠해야한다고 가정해보자.

이때 담벼락을 칠해야 하는 작업자 입장에서 가장 효율적인 방법은 다음과 같다.

<br />

1. 빨간색 페인트를 모두 칠하고, 페인트를 초록색으로 바꾼다. (페인트 교체 1회)
2. 초록색 페인트를 모두 칠하고 페인트를 파란색으로 바꾼다. (페인트 교체 2회)
3. 파란색 페인트를 모두 칠한 후 작업을 종료한다.

<br />

이렇게 하면 페인트를 단 두 번만 교체하고도 모든 작업을 끝낼 수 있다.

<br />

![image](https://user-images.githubusercontent.com/71188307/147462586-b94821b0-4cea-4b17-9ecf-ee02f99115df.png)

<br />

![image](https://user-images.githubusercontent.com/71188307/147462664-0e9f70a6-0022-42e0-b0fe-87c38088364e.png)

<br />

![image](https://user-images.githubusercontent.com/71188307/147462724-f5582960-147b-44c0-a04d-182c6f84c583.png)

<br />

하지만 위와 같이 처리 할 경우 한가지 문제가 있다.

작업자 입장에서는 페인트를 교체하는 비용이 최대로 적게 들면서 오로지 담벼락을 칠하는데만 집중할 수 있게 되지만, 담벼락을 구경하는 사람 입장에서는 빨간색이 다 칠해지기 전까지는 초록색과 파란색 벽면을 빠르게 볼 수 없다.

심지어 파란색은 빨간색과 초록색이 모두 칠해지기 전까지 볼 수 없다.

이러한 상황을 대입해 생각하면 예를 들어 키보드를 눌렀는데 입력한 값이 10초 후에 화면에 표시되는 상황을 상상할 수 있다. 즉, 사용자 경험이 크게 나빠진다.

이러한 문제를 해결하기 위해 다음과 같은 방식을 사용한다.

<br />

1. 빨간색을 조금 칠한 후 페인트를 초록색으로 바꾼다.
2. 초록색을 조금 칠한 후 페인트를 파란색으로 바꾼다.
3. 파란색을 조금 칠한 후 페인트를 빨간색으로 바꾼다.
4. 1~3을 반복한다.

<br />

![image](https://user-images.githubusercontent.com/71188307/152805576-1cefe8c4-779f-41c1-98b2-2ba210e8ad96.png)

<br />

![image](https://user-images.githubusercontent.com/71188307/152805450-6d9df712-7740-4a9f-b45f-8b2916116ab0.png)

<br />

...

<br />

![image](https://user-images.githubusercontent.com/71188307/152805925-61c9f5fd-a8c1-4acd-b720-7b245b58535d.png)

<br />

처음에는 페인트 교체를 단 두 번만 해서 모든 벽을 칠했는데, 여기서는 벽을 조금 칠하고 페인트를 바꿔서 다른 벽을 칠하는 식으로 모든 벽을 동시에 칠했다.

그래서 페인트를 14번이나 교체하고서야 벽을 다 칠할수 있었다.

작업자 입장에서는 참으로 거지같은 상황이지만, 구경하는 사람 입장에서는 담벼락에 모든 색이 칠해지는 광경을 실시간으로 볼 수 있었다.

이러한 방식을 `시분할(Time Sharing)` 방식이라 하며, 페인트를 교체하는 작업을 컴퓨터 과학에서 말하는 `컨텍스트 스위칭(Context Switching)`이라고 볼 수 있다.

CPU는 사용자 경험 증대를 위해 매번 하는 작업을 변경하는데, 이렇게 CPU가 매번 작업을 변경 할 경우 이전에 한 작업을 이어서 해야만 하기 때문에 이전에 한 작업을 모두 기억해야만 한다.

때문에 현재까지의 작업 상황을 `PCB(Program Controll Block)`에 저장해두고 레지스터를 초기화하는 작업을 진행한다.

그리고 나중에 다시 작업을 재개 할 때 PCB를 참고하여 이전에 진행했던 작업 진행 상황들을 다시 불러온 후, 해당 부분부터 작업을 재개하게 된다.

운영체제는 이 시분할 시스템을 위해 사용자 프로세스에게 CPU를 점유할 수 있는 시간을 할당(CPU 스케쥴링)하는데, 사용자 프로세스가 무한 루프등의 이유로 CPU를 반환하지 않고 계속 점유하는 것을 방지하기 위해 타이머를 사용한다.

즉, 사용자 프로세스가 CPU를 점유한 시간이 운영체제가 타이머에 지정한 시간을 넘길 경우 운영체제는 사용자 프로세스에게서 CPU를 강제로 회수해 다른 사용자 프로세스에게 할당해준다.

<br />

## Device Controller

---

I/O 디바이스를 제어하는 작은 CPU라고 볼 수 있으며, CPU와 I/O 디바이스 간의 모든 처리는 이 디바이스 컨트롤러가 담당한다.

<br />

## Local Buffer

---

I/O 디바이스에게 주어진 작은 저장장치이다. 

예를 들어 키보드를 입력하면 입력한 키의 코드 값이 키보드 버퍼에 담기고, 디바이스 컨트롤러가 키보드 버퍼에 쌓인 데이터들을 인터럽트와 함께 CPU를 향해 보낸다. (실제로는 DMA 컨트롤러가 받아 처리할 것으로 예상된다)

<br />

## I/O 수행

---

모든 I/O 명령은 커널 모드로만 가능하다. 

그렇다면 사용자 모드로 실행되는 사용자 프로세스는 어떻게 I/O 작업을 할 수 있을까? 

바로, `시스템 콜(System Call)`을 활용한다.

시스템 콜은 사용자 프로세스가 인터럽트를 통해 운영체제에게 작업을 대신 처리해줄 것을 요청하는 것을 말한다. 

사용자 프로세스는 기본적으로 사용자 모드로 실행되고, 사용자 모드로는 권한이 없어 운영체제 메모리에 직접적으로 접근할 수 없기 때문에, 인터럽트를 통해 운영체제에 정의된 커널 함수를 간접적으로 호출하는 것이다.

즉, 프로그램이 직접 인터럽트를 발생시켜 버리면, CPU는 사용자 프로세스의 명령어를 한 개 처리한 후 인터럽트를 체크 할 것이다.

그리고 CPU는 사용자 프로세스가 발생시킨 인터럽트를 감지하여 다음 명령어를 바로 수행하는 대신 하던 작업을 저장하고 Mode bit를 0으로(커널 모드) 변경한 후 제어권을 운영체제에 넘기게 된다.

그리고 인터럽트에 의해 CPU 제어권을 넘겨받게 된 운영체제는 `인터럽트 서비스 루틴(ISR)`을 통해 해당 인터럽트가 어떤 경로로 발생했는지를 파악한 후 후속 처리를 진행하게 된다.

<br />

## 동기식 I/O와 비동기식 I/O

---

`동기식 I/O(Synchronous I/O)`은 사용자 프로세스에서 I/O 작업 요청이 발생하면, 발생한 I/O 작업이 디바이스 컨트롤러에서 완료될 때까지 모든 CPU의 작업 흐름이 멈추고, I/O 작업이 완료됨과 동시에 CPU의 작업 흐름이 재개되는 것을 말한다.

예를 들어 자바로 `하드디스크(I/O 디바이스)`에 텍스트 파일을 생성하는 프로그램을 작성했고, 이 프로그램이 운영체제로부터 메모리와 CPU를 할당받아 프로세스가 되어 하드디스크에 텍스트 파일을 생성하는 부분의 코드가 실행될 때, 자바 프로세스는 직접 I/O 작업을 할 수 없기 때문에 시스템 콜을 위해 인터럽트를 발생시킬 것이다.

그리고 CPU는 자바 프로세스에서 발생시킨 인터럽트를 감지하여 커널 모드로 변경한 후 운영체제에 자신의 제어권을 넘기고, 운영체제는 자바 프로세스에서 요청한 I/O 작업을 하드디스크 디바이스 컨트롤러에게 위임할 것이다.

그리고 디바이스 컨트롤러가 로컬 버퍼를 통해 모든 작업을 완료하는 동안 자바 프로세스와 CPU는 대기상태에 들어간다.

디바이스 컨트롤러가 모든 작업을 완료하여 인터럽트를 발생시키면 CPU는 다시 사용자 모드로 변경되어 자바 프로세스에 할당 되어 작업 흐름이 돌아가기 시작 할 것이다.

이러한 방식을 다음과 같이 두 가지 방법으로 구현할 수 있다.

<br />

- 구현 방법 1

- I/O가 끝날 때까지 기다렸다가, 작업이 끝나 인터럽트가 발생되면 사용자 프로세스에게 CPU의 제어권을 넘긴다.
- 즉, 매 시점 하나의 I/O만 일어날 수 있다.
- I/O가 끝날 때까지 CPU가 연산할 수 있는 시간을 낭비한다는 커다란 결함이 있다.

<br />

- 구현 방법 2

- I/O가 완료될 때까지 운영체제는 해당 프로세스에서 CPU의 제어권을 빼앗고 진행상태를 저장한다.
- 다른 사용자 프로세스에게 CPU를 할당한다.
- 현대 동기식 I/O는 보통 이 방법으로 구현한다.

<br />

`비동기식 I/O(Asynchronous I/O)`는 I/O 작업이 시작되면 시작된 I/O 작업이 끝나기를 기다리지 않고 CPU 제어권을 I/O 작업을 요청했던 사용자 프로그램에게 즉시 넘기는 것을 말한다. 

즉, 호출자와 피호출자의 작업 싱크가 일치하지 않게 동작하는 것이다.

<br />

![image](https://user-images.githubusercontent.com/71188307/152796573-1d3438f0-3e2e-4f70-85f3-6847ee3623c3.png)

<br />

위 그림은 동기식 I/O와 비동기식 I/O를 비교해 보여 준다. 

사용자가 I/O 요청을 하면 동기식 I/O에서는 먼저 운영체제로 CPU의 제어권이 넘어와서 I/O 처리와 관련된 커널 함수가 실행된다. 

이때 I/O를 호출한 프로세스의 상태를 `Blocked` 상태로 바꾸어 I/O가 완료될 때까지 CPU를 다시 할당받지 못하도록 한다. 

그리고 I/O가 완료되면 디바이스 컨트롤러가 CPU에게 인터럽트를 발생시켜 I/O가 완료됐음을 알려주고, `Blocked` 상태인 프로세스의 상태를 복원하여 CPU를 할당받을 수 있는 자격을 준다. 

반면 비동기식 I/O에서는 CPU의 제어권이 I/O를 요청한 프로세스에게 곧바로 다시 주어지며, I/O가 완료되는 것과 무관하게 처리 가능한 작업부터 처리한다.

한편 두 방식 모두 I/O 연산이 완료되면 인터럽트를 통해 CPU에게 알려준다.

<br />

## 절대 주소(물리 주소) 와 상대 주소(논리 주소)

---

![image](https://user-images.githubusercontent.com/71188307/152803596-d5629e5d-654a-4a2c-974e-5fcc67a2a45c.png)

<br />

개발자가 프로그래밍 언어로 개발할 때 메모리 주소를 크게 신경쓰지 않고 개발한다.

이게 가능한 이유는 개발자가 개발하는 모든 사용자 프로세스의 시작 메모리 주소는 0x0이라고 가정하기 때문이다.

프로그램이 실행되어 운영체제에게서 메모리를 할당받을 때에서야 랜덤한 시작 메모리 주소가 생겨난다(메모리에 남아있는 공간이 어떻게 되어있을지 모르기 때문에). 그리고 이 랜덤한 시작 메모리 주소를 `MMU(Memory Management Unit)`가 0x0으로 매핑해준다.

즉, 메모리에 남아있는 영역의 시작 주소가 0x2000이며, 이 주소부터 사용자 프로세스 x가 메모리를 할당받는다고 가정하면, MMU는 사용자 프로세스 x의 실제 시작 메모리 주소를 0x2000으로 저장하고 이를 외부에는 사용자 프로세스 x의 시작 메모리 주소는 0x0이라고 반환해준다.

이후 CPU와 프로그램 카운터에 의해 사용자 프로세스 x의 200번지 주소에 위치한 명령어를 인출해달라는 요청이 들어오면 MMU는 매핑해둔 0x2000을 더해 0x2200라는 주소를 반환해준다.

<br />

## 저장장치 계층

---

![image](https://user-images.githubusercontent.com/71188307/152800086-9e9ee7d0-9374-4ef6-8b34-a4d1c0e3502f.png)

<br />

위로 갈수록 속도가 빠르지만, 단위 공간 당 가격이 비싸고 용량이 적다. 

반면 아래로 갈수록 단위 공간 당 가격이 싸고 용량이 많지만, 속도가 느려진다.

또한, CPU는 바이트 단위로 접근 가능한 매체여야 접근이 가능하다.

HDD는 섹터 단위로 접근하기 때문에 CPU에서 직접 접근할 수 없다.

<br />

- Primary Storage

CPU에서 직접 접근할 수 있는 저장장치를 말하며, Executable(실행 가능한) 라고 부른다. 즉, 해당 저장장치는 바이트 단위로 CPU 접근이 가능하다. Primary 라고 부르며, 전원이 꺼지면 데이터가 사라지는 휘발성 특징을 갖는다. `RAM`이 대표적이다.

<br />

- Secondary Storage

CPU가 직접 접근하지 못하는 저장장치를 말한다. 대표적으로 `HDD`는 섹터 단위를 사용하기 때문에 바이트 단위를 사용하는 CPU가 접근하지 못한다. Secondary 라고 부르며, 해당 저장장치는 전원이 꺼져도 데이터가 보존되는 비휘발성 특징을 갖는다.

<br />

- 캐시 메모리

CPU와 메인 메모리(RAM)간에는 분명한 물리적인 거리가 존재하기 때문에 속도가 크게 저하되며, 이러한 문제를 해결하기 위해 초고성능의 메모리를 CPU에 내장하는데 이것이 캐시 메모리이다.

캐시 메모리는 매우 빠르고 CPU와 물리적으로 가까운곳에 위치하지만, 가격이 비싸 개인용 컴퓨터에는 작은 용량의 캐시 메모리를 사용하기 때문에 RAM에 있는 모든 데이터를 캐시 메모리로 복사 할 수는 없다.

그래서 빈번히 사용되는 데이터를 선별적으로 캐시 메모리에 복사하고 CPU가 RAM이 아닌 캐시 메모리에 접근해 이를 재사용하는 기법(캐싱)을 통해 시스템의 성능을 높일 수 있다.

<br />

간단하게 계산을 해보자.

<br />

![image](https://user-images.githubusercontent.com/71188307/152811315-f9335855-20ab-4098-ac6d-d46d65ffcca2.png)

<br />

![image](https://user-images.githubusercontent.com/71188307/152811347-e270e3d1-3090-40c8-8044-86456ce93dd6.png)

<br />

대충 검색을 통해 찾은 1TB HDD의 가격은 52,500이다.

역시, 대충 검색을 통해 찾은 64GB RAM의 가격은 406,930이다.

1TB는 1,024GB이므로, 64GB RAM이 16개 있다면 1TB의 용량이 된다.

즉, HDD로 1TB를 채우면 52,500의 비용이 필요하며, RAM으로 1TB를 채우면 406,930 * 16 = 6,510,880 이라는 천문학적인 비용이 필요하다.

<br />
