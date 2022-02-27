# Lecture

---

- [운영체제 - 반효경 교수님](http://www.kocw.or.kr/home/cview.do?mty=p&kemId=1046323)
    - Process Synchronization 3
    - Process Synchronization 4

<br />

# Bounded-Buffer-Problem (Producer-Consumer Problem)

---

![image](https://user-images.githubusercontent.com/71188307/155873331-0a01a555-9ef6-489c-9825-206a87b758f8.png)

<br />

- 주황색: Full buffer
- 회색: Empty buffer

<br />

- Producer
    - empty buffer가 있는지 확인하고 없다면 기다린다
    - 공유 데이터에 lock을 건다
    - empty buffer에 데이터를 입력한다
    - lock을 푼다
    - full buffer가 하나 증가한다

<br />

- Consumer
    - full buffer가 있는지 확인하고 없다면 기다린다
    - 공유 데이터에 lock을 건다
    - full buffer에서 데이터를 꺼낸다
    - lock을 푼다
    - empty buffer가 하나 증가한다

<br />

## Synchronization Problem

---

- 공유 버퍼이기 때문에 여러 생산자가 동시에 한 버퍼에 데이터를 입력 할 수 있다
- 마찬가지로 여러 생산자가 동시에 한 버퍼의 데이터를 꺼내가려 할 수 있다

<br />

## Uses of semaphores

---

- 공유 데이터의 상호배제를 위해 이진 세마포어가 필요
- 가용 자원의 갯수를 세기 위해 계수 세마포어가 필요

<br />

## Solution example using semaphore

---

![image](https://user-images.githubusercontent.com/71188307/155873468-ac262942-97d6-42cc-8000-7489622c2dec.png)

- Producer
  - P 연산을 통해 empty buffer가 있는지 확인하고 없다면 대기한다
  - P 연산을 통해 empty buffer가 있다면 mutex를 0으로 만들고 임계 구역에 진입한다
  - empty buffer에 데이터를 입력한다
  - V 연산을 통해 mutex 값을 1로 만든다
  - V 연산을 통해 full buffer를 1 증가하고 임계 구역에서 빠져나온다

<br />

- Consumer
  - P 연산을 통해 full buffer가 있는지 확인하고 없다면 대기한다
  - P 연산을 통해 full buffer가 있다면 mutex를 0으로 만들고 임계 구역에 진입한다
  - full buffer에서 데이터를 빼 간다
  - V 연산을 통해 mutex 값을 1로 만든다
  - V 연산을 통해 empty buffer를 1 증가하고 임계 구역에서 빠져나온다

<br />

# Readers-Writers Problem

---

- 한 프로세스가 DB에 쓰기 중일 때 다른 프로세스가 접근하면 안 된다
- 읽기는 동시에 여러 명이 해도 된다

<br />

## Solution

---

![image](https://user-images.githubusercontent.com/71188307/155874002-5f6c09b3-f678-463f-9a45-06dad453668a.png)

![image](https://user-images.githubusercontent.com/71188307/155873906-a4b88260-c74d-477f-8e2a-13a8b757e400.png)

<br />

이진 세마포어 두 개를 사용한다

- semaphore mutex = 1
  - 상호 배제를 보장해야 하므로 각각의 세마 포어 값은 1로 지정하며, 다수의 Reader들이 readCount에 동시 접근하면 데이터 불일치의 위험이 있으므로 mutex를 정의
- db = 1
  - db는 Reader와 Writer가 공통 데이터베이스에서 서로 상호 배제를 보장해야하므로 정의

<br />

- Reader
  - readCount도 공유 변수이기 때문에 P(mutex) 연산을 통해 readCount를 상호 배제 처리하고 값을 1 증가시킨다 (readCount++)
  - Reader가 없고, Reader가 오로지 본인 혼자라면 (if(readCount == 1)) db에 lock을 건다 (P(db))
  - V(mutex) 연산을 통해 임계 구역에서 빠져 나온다
  - db를 원하는 만큼 읽는다
  - P(mutex) 연산을 통해 readCount 상호 배제 처리하고 값을 1 감소시킨다 (readCount--)
  - 마지막으로 read를 그만하고 나가려는 프로세스가 나 하나라면 (if(readCount == 0)) db의 lock을 해제한다 (V(db))
  - V(mutex) 연산을 통해 임계 구역에서 빠져나온다.

<br />
  
- Wrtier
  - P(db) 연산을 통해 db에 lock이 걸려있는지 확인한다
  - lock이 걸려 있지 않으면 db에 lock을 걸고 임계 구역에 진입한다
  - 쓰기 작업이 끝나면 V(db) 연산을 통해 db의 lock을 해제하고 임계 구역에서 빠져 나온다

<br />

- 문제점
  - Writer가 쓰기 작업을 하고 싶어도 무조건 Read 하고 있는 프로세스인 Reader가 없어야 한다
  - 만약 Reader가 무한히 read 작업을 실행한다면 Writer는 영영 임계 구역에 진입 하지 못하는 Startvation에 빠질 수도 있다

<br />

# Dining-Philosophers Problem

---

철학자 다섯이서 원형 식탁에 둘러앉아 생각에 빠져있다가, 배가 고파지면 밥을 먹는다. 

그들의 양쪽엔 각각 젓가락 한 짝씩 놓여있고, 밥을 먹으려 할 땐 다음의 과정을 따른다.

1. 왼쪽 젓가락부터 집어든다. 다른 철학자가 이미 왼쪽 젓가락을 쓰고 있다면 그가 내려놓을 때까지 생각하며 대기한다
2. 왼쪽을 들었으면 오른쪽 젓가락을 든다. 들 수 없다면 1번과 마찬가지로 들 수 있을 때까지 생각하며 대기한다
3. 두 젓가락을 모두 들었다면 일정 시간동안 식사를 한다
4. 식사를 마쳤으면 오른쪽 젓가락을 내려놓고, 그 다음 왼쪽 젓가락을 내려놓는다
5. 다시 생각하다가 배고프면 1번으로 돌아간다

<br />

## Example

---

```c
do {
    P(chopstick[i]);
    P(chopstick[(i + 1) % 5]);
    ...
    eat();
    ...
    V(chopstick[(i + 1) % 5]);
    V(chopstick[i]);
    ...
    think();
    ...
} while (1);
```

<br />

모두가 동시에 왼쪽 젓가락을 드는 순간부터 환형대기에 빠져 다 같이 오른쪽 젓가락을 들 수 없으므로 Deadlock이 발생할 수 있다

Deadlock의 필요 조건은 다음과 같으며, 아래의 조건이 단 하나라도 만족하지 않는다면 Deadlock은 발생하지 않는다.
- 상호 배제
- 비선점
- 점유와 대기
- 원형 대기

<br />

## Solution

---

- 4명의 철학자만 테이블에 동시에 앉을 수 있게 한다
- 젓가락을 두 개 모두 집을 수 있을 때만 젓가락을 집을 수 있게 한다
- 비대칭
  - 짝수(홀수) 철학자는 왼쪽(오른쪽) 젓가락부터 집도록 방향을 정해준다

<br />

## 세마포어 예제

---

```c
enum {thinking, hungry, earting} state[5]; // 5명의 철학자들이 가질 수 있는 상태
semaphore self[5] = 0; // 5명의 철학자들이 젓가락 2개를 모두 들 수 있는 지를 판단 (0이면 불가, 1이면 가능)
semaphore mutex = 1; // 5명의 철학자들이 state에 동시에 접근하지 못하도록 상호 배제

do {
    pickup(i); // 젓가락을 든다
    eat(); // 먹는다
    putdown(i); // 젓가락을 내린다
    think(); // 생각한다
} while (1);

void pickup(int i) {
    P(mutex); // state에 lock을 걸고 임계구역에 진입
    state[i] = hungry; // 철학자의 상태를 hungry로 변경
    test(i); // 젓가락 2개를 들 수 있는지 확인하고 가능하면 상태를 eating으로 변경한다
    V(mutex); // state의 lock을 풀고 임계 구역을 빠져나옴
    P(self[i]); // 젓가락 2개를 들 수 없는 상태로 변경
}

void putdown(int i) {
    P(mutex); // state에 lock을 걸고 임계구역에 진입
    state[i] = thinking; // 철학자의 상태를 thinking으로 변경
    test((i + 4) % 5); // 양 옆 철학자가 식사할 수 있는 상태인지 확인하고
    test((i + 1) % 5); // 식사할 수 있는 상태라면 상태를 eating으로 변경해줌 
    V(mutex); // state의 lock을 풀고 임계 구역을 빠져나옴
}

void test(int i) {
    if (state[(i + 4) % 5] != eating && state[i] == hungry && state[(i + 1) % 5] != eating) {
        state[i] = eating;
        V(self[i]); // 젓가락 2개를 들 수 있는 상태로 변경
    }
}
```

<br />

# Monitor

## The problem with semaphores

---

![image](https://user-images.githubusercontent.com/71188307/155874777-d0c177ef-3e39-404a-9197-071fced9340d.png)

<br />

## Concept

---

모니터는 동시 수행중인 프로세스 사이에서 추상 데이터 타입의 안전한 공유를 보장하기 위한 고수준의 동기화 도구이다.

<br />

```c
monitor monitor-name
{
    shared variable declarations
    procedure body P1 (...) {
        ...
    }
    procedure body P2 (...) {
        ...
    }
    procedure body Pn (...) {
        ...
    }
    {
        initialization code
    }
}
```

<br />

프로세스가 공유 데이터에 접근하기 위해서는 위와 같이 모니터 내부의 프로시저를 통해서만 공유 데이터를 접근할 수 있도록 설계한다.

<br />

![image](https://user-images.githubusercontent.com/71188307/155874896-4cbd61d1-35aa-4ccd-860b-63957f0cab39.png)

<br />

예를 들어 공유 데이터들이 있으면 밖에서 아무나 접근할 수 있는 것이 아니라 모니터 내부에 공유 데이터에 접근하는 프로시저를 정의해 놓고 이 프로시저를 통해서만 접근할 수 있게 제어한다.

모니터가 내부에 있는 프로시저는 동시에 여러 개가 실행되지 않도록 통제하는 권한을 준다.

즉, 모니터 내에서는 한 번에 하나의 프로세스만이 활동이 가능하도록 제어하므로 프로그래머 입장에서 lock을 임의로 걸 필요가 없다는 장점이 있다.

<br />

![image](https://user-images.githubusercontent.com/71188307/155874970-5d99706d-9baf-47eb-b92e-fad18ceabf32.png)

<br />

### Producer-Consumer Problem

---

![image](https://user-images.githubusercontent.com/71188307/155875039-5169f69b-f4db-4280-a3c0-f332dc83b82f.png)

<br />

- Producer
  - empty buffer가 없으면 empty buffer를 기다리는 큐에서 대기
  - empty buffer가 있다면 empty buffer에 데이터를 집어넣는다
  - 작업이 끝나면 `full.signal()`을 통해 full buffer를 기다리는 큐에 프로세스 하나를 깨우라고 알림

<br />

- Consumer
  - full buffer가 없으면 full buffer를 기다리는 큐에서 대기
  - full buffer가 있다면 full buffer의 데이터를 꺼내서 *x에 값을 저장한다
  - 작업이 끝나면 `empty.signal()`을 통해 empty buffer를 기다리는 큐에 프로세스 하나를 깨우라고 알림

<br />

## Dining-Philosopher Problem

---

```c
monitor dining_philosopher
{
    enum {thinking, hungry, eating} state[5]; // 5명의 철학자들이 가질 수 있는 상태
    condition self[5]; // 5명의 철학자들이 젓가락 2개를 모두 들 수 있는 지를 판단

    void pickup(int i) {
        state[i] = hungry; // 철학자 자신의 상태를 hungry로 변경
        test(i); // 철학자 자신에게 식사할 수 있는 기회를 준다 (test 함수 참고)
        
        // 식사를 하지 못했다면 상태가 eating으로 변경되지 않았음을 의미한다
        // 따라서 wait 큐에서 대기한다
        if (state[i] != eating) {
            self[i].wait();
        }
    }

    void putdown(int i) {
        state[i] = thinking; // 식사를 마쳤으므로 상태를 thinking으로 변경한다
        test[(i + 4) % 5]; // 자신의 왼쪽 철학자에게 식사할 수 있는 기회를 준다 (test 함수 참고)
        test[(i + 1) % 5]; // 자신의 오른쪽 철학자에게 식사할 수 있는 기회를 준다 (test 함수 참고)
    }

    void test(int i) {
        // 자신의 왼쪽 젓가락과 오른쪽 젓가락을 모두 들 수 있는지 확인
        if ((state[(i + 4) % 5] != eating) && (state[i] == hungry) && (state[(i + 1) % 5] != eating) {
            // 모두 들 수 있다면 수행
            state[i] = eating; // 상태를 eating으로 변경한다 
            self[i].signal(); // wait 큐에서 자신을 깨운다
        }
    }
}

Each Philosopher:
{
    pickup(i);
    eat();
    putdown(i);
    think();
} while (1)
```

<br />
