# Lecture

---

- [운영체제 - 반효경 교수님](http://www.kocw.or.kr/home/cview.do?mty=p&kemId=1046323)
  - Process Synchronization 1
  - Process Synchronization 2

<br />

# 임계구역(Critical section)

---

- n 개의 프로세스가 공유 자원을 동시에 사용하기를 원하는 경우, `공유 자원에 접근하려 하는 코드 블럭을 의미`
- A프로세스에서 공유자원 c의 값을 1만큼 증가시키고, B프로세스에서는 동시에 c의 값을 1만큼 감소시킨다면 어떤 현상이 발생하는가?
- 이러한 경우 발생할 수 있는 문제들을 해결하기 위해 `동기화(synchronization)`가 필요하다

<br />

## 프로그램적 해결법의 충족 조건

---

**Mutual Exclusion (상호 배제)**

- 프로세스가 임계구역 부분을 수행 중이면 다른 모든 프로세스들은 그들의 임계구역에 들어갈 수 없다

**Progress (진행)**

- 아무 프로세스도 임계구역에 있지 않는 상태에서 임계구역에 들어가고자 하는 프로세스가 생긴다면 즉시 임계구역에 들어가게 해줘야한다

**Bounded Waiting (유한 대기)**

- 프로세스가 임계구역에 들어가려고 요청한 후 부터 그 요청이 허용될 때까지 다른 프로세스들이 임계구역에 들어가는 횟수에 한계가 있어야 한다

<br />

### 문제 해결 알고리즘

---

다음과 같이 가정한다.

- 모든 프로세스의 수행 속도는 0보다 크다
- 프로세스들 간의 상대적인 수행 속도는 가정하지 않는다

<br />

#### Algorithm 1

---

<br />

```c
do {
    while (turn != 0)
    critical section
    turn = 1;
    remainder section
} while (1);
```

<br />

turn이라는 변수를 가지고 자기 차례에만 임계구역에 들어가게 강제한다.

<br />

상호배제를 만족하지만, 프로세스 간의 임계구역에 들어가야하는 횟수가 다르다면 문제가 생길 수 있다.

- 프로세스 1은 임계구역에 한 번만 들어가도 되고, 프로세스 2는 임계구역에 여러 번 들어가야 되는 상황이다
- 코드가 한 번 작동한 이후, 프로세스 1이 더이상 들어갈 일이 없게 되기 때문에 프로세스 2에게는 자신의 차례가 돌아오지 않게 되버린다
- 즉, Progress(진행) 조건을 만족하지 못한다

<br />

#### Algorithm 2

---

<br />

```c
do {
    flag[i] = true;
    while(flag[j]);
    critical section
    flag[i] = false;
    remainder section
} while(1);
```

<br />

flag라는 boolean 변수를 가지고 임계구역에 들어갈 상태를 표현한다.

- 다른 프로세스의 flag를 체크하고, 프로세스의 flag가 true라면 양보, false라면 자신이 들어간다

이 또한 상호배제는 만족하므로 얼핏 보기에는 문제가 없어 보이지만, 선점 문제가 발생 할 수있다.

- 프로세스 1이 임계구역에 들어가기 위해 flag를 true로 만들어 둔 상태에서 CPU를 뺏기게 된다
- 프로세스 2도 임계구역에 들어가기 위해 flag를 true로 만들고 들어가려하는데, 다른 프로세스의 flag가 이미 true이기에 양보를 하게된다
- 프로세스 1에게 다시 CPU가 넘어와 임계구역에 들어가려하는데, 다른 프로세스의 flag가 true이므로 프로세스1도 마찬가지로 양보를 하게되버린다
- 즉, 서로 무한정으로 양보하는 상황이 발생한다 (데드락)

<br />

#### Algorithm 3(Peterson`s Algorithm)

---

<br />

```c
do {
    flag[i]= true;
    turm = j;
    while(flag[j] && turn == j);
    critical section
    flag[i] = false;
    remainder section
} while(1);
```

<br />

flag 변수와 turn 변수를 둘 다 사용하는 방법으로, 프로그램적 해결법의 충족 조건을 모두 만족하게 된다.

하지만 `Busy Waiting(Spin Lock)`문제가 발생한다.

Busy Waiting은 계속 CPU와 Memory를 사용하며 wait하고 있는 상태를 의미하며, 코드상으로 while문을 계속해서 돌고있는 상태이다.

<br />

### Synchronization Hardware

---

하드웨어적으로 Test & modify를 atomic 하게 수행할 수 있도록 지원하는 경우 앞의 문제는 간단히 해결 가능하다.

- lock을 걸고 푸는 작업을 하나의 명령어(논리)로 처리하기 때문에, 문제를 손 쉽게 해결 할 수 있다.

<br />

```c
Synchronization variable;
    boolean lock = false;

do {
    while(Test_and_Set(losk));
    critical section
    lock = false;
    remainder section
}
```

<br />

## Semaphores

---

- 앞의 방식들을 추상화 시킨 것
- 앞서 보았던 잠금을 걸고, 푸는 작업을 대신 해준다. (공유 자원을 획득하고, 다시 반납하는 작업)

<br />

`P(S)` - 잠금을 거는 연산

<br />

```c
while (S<=0) do no-op;
S--;
```

<br />

`V(S)` - 잠금을 푸는 연산

<br />

```c
S++;
```

<br />

### Semaphores Examples

---

```c
Synchronization variable:
semaphore mutex; // 1로 초기화

do{
	P(mutex);
	/* critical section */
	V(mutex); 
} whlile (1);
```

<br />

프로그래머는 위처럼 세마포어를 이용해서 더 간단하게 프로그램을 작성할 수 있다.

<br />

Block & Wakeup (Sleep Lock) 방식의 구현을 이용한다.

- 임계구역의 길이가 긴 경우 적당하다.

Busy-wait (Spin Lock)

- 임계구역의 길이가 매우 짧은 경우 오버헤드가 비교적 더 작다.
- 그렇기 때문에 일반적으로 Block & Wakeup 방식이 더 효율적으로 사용할 수 있다.

### Block & Wakeup Implementation

---

세마포어는 다음과 같이 정의 할 수있다.

<br />

```c
typedef struct
{
	int value; // 세마포어 변수
	struct process *L; // 세마포어를 사용하기 위한 대기 큐
}semaphore;
```

<br />

![image](https://user-images.githubusercontent.com/71188307/155293040-e5cc5a1a-fee0-48e6-b1ea-7bb0518f7b8d.png)

<br />

- block()
  - 커널은 block을 호출한 프로세스를 suspend하고, 이 프로세스의 PCB를 세마포어의 대기큐에 넣는다
- wakeup(P)
  - block된 프로세스를 wake up하고, 이 프로세스의 PCB를 레디큐에 넣는다

<br />

세마포어 연산은 다음과 같이 정의 및 구현한다

> P(S): 
> 
> 세마포어 변수 S를 무조건 1 줄이고, 그 변수의 값이 음수면 해당 프로세스를 대기큐로 보낸 후 Block 상태로 만든다.

<br />

```c
// P(S)
S.value--;
if (S.value < 0)
{
    add this process to S.L;
    block();
}
```

<br />

> V(S): 
> 
> 세마포어 변수 S를 무조건 1 늘리고, 그 변수의 값이 0보다 작거나 같으면 이미 기다리고 있는 프로세스가 있으므로 프로세스를 대기큐에서 꺼내 레디큐로 보낸다. 세마포어 변수 S 값이 양수면 아무나 임계 구역에 들어 갈 수 있으므로 별다른 추가 연산을 하지 않는다. V 연산은 특정 프로세스가 공유 데이터를 반납한 후 임계 구역에서 나가는 연산이다.

<br />

```c
// V(S)
S.value++;
if (S.value <= 0)
{
    remove a process P from S.L;
    wakeup(P);
}
```

<br />

- **block()**
  - 커널은 block을 호출한 프로세스를 대기 시킨다.
  - 이 프로세스의 PCB를 세마포어를 위한 대기 큐에 넣는다.
- **wakeup(P)**
  - block 된 프로세스 P를 wakeup 시킨다.
  - 이 프로세스의 PCB를 준비 큐로 옮긴다.

<br />

### Type of Semaphore

---

- Counting Semaphore
  - 도메인이 0이상인 임의의 정수값
  - 여러 개의 공유 자원을 상호 배제
  - 주로 resource counting에 사용

-Binary Semaphore
  - 0 또는 1값만 가질 수 있는 세마포어
  - 한 개의 공유 자원을 상호 배제
  - mutex와 유사하나 완전히 같지는 않다

<br />

## Deadlock & Starvation

### Deadlock

---

둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 event를 무한히 기다리는 현상으로, 예시는 다음과 같다.

<br />

**S와 Q가 1로 초기화된 세마포어라 하자.**

![image](https://user-images.githubusercontent.com/71188307/155294603-9fd76523-598b-4252-b834-ac6a00419782.png)

<br />

P0이 CPU를 얻어 P(S) 연산까지 수행하여 S 자원을 얻었다고 가정 한다. 이 때,  P0의 CPU 점유 시간이 끝나 Context Switch가 발생하고 P1에게 CPU 제어권이 넘어갔다. 

P1은 P(Q) 연산을 수행하여 Q 자원을 얻었으나 또 다시 CPU 점유 시간이 끝나 Context Switch가 발생하여 P0에게 CPU 제어권이 넘어갔다.

P0은 P(Q) 연산을 통해 Q 자원을 얻고 싶지만, 이미 Q 자원은 P1이 갖고 있는 상태이므로 Q 자원을 가져올 수가 없다. 

마찬가지로 P1도 P(S) 연산을 통해 S 자원을 얻고 싶지만, 이미 S 자원은 P0이 갖고 있는 상태이므로 S 자원을 가져올 수 없다.

이렇게 P0와 P1은 영원히 서로의 자원을 가져올 수 없고, 이러한 상황을 `Deadlock`이라고 한다.

<br />

이러한 현상을 해결하는 방법 중 하나로, 자원의 획득 순서를 정해주어 해결하는 방법이 있다. 

S를 획득해야만 Q를 획득할 수 있게끔 순서를 정해주면 프로세스 A가 S를 획득했을 때 프로세스 B가 Q를 획득할 일이 없다.

<br />

### Starvation

---

indefinite blocking(무기한적인 차단)으로, 프로세스가 자원을 얻지 못하고 무한히 기다리는 현상을 의미한다.

<br />

### Deadlock vs Starvation

---

언뜻 보기에 둘 다 자원을 가져오지 못하고 무한히 기다리니까 같은 단어라고 혼동할 수 있다.

`Deadlock`은 P0 프로세스가 A 자원을 보유하고 있고, P1 프로세스가 B 자원을 보유하고 있을 때 서로의 자원을 요구하여 무한히 기다리는 현상이다. 

반면 `Starvation`은 프로세스가 자원 자체를 얻지 못하고 무한히 기다리는 현상이다. SJF 알고리즘을 생각하면 이해가 쉬울 수 있다.

<br />
