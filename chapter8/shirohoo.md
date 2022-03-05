# Lecture

---

- [운영체제 - 반효경 교수님](http://www.kocw.or.kr/home/cview.do?mty=p&kemId=1046323)
  - Memory Management 1
  - Memory Management 2

<br />

# Memory

---

각 프로세스는 독립적인 메모리를 점유한다 하였다.

이번에는 운영체제가 메모리를 어떻게 관리하는지 알아 볼 것이다.

<br />

## Logical Address vs Physical Address

---

![image](https://user-images.githubusercontent.com/71188307/156889420-a02b0b51-2254-48a6-a06b-26faa8315555.png)

<br />

### 주소 바인딩

---

프로세스의 논리적 주소를 물리적 메모리 주소로 연결하는 작업을 말한다.

> Symbolic Address -> `Logical Address (바로 이 시점)` -> Physical Address 

Symbolic Address는 프로그래머 입장에서 사용하는 것이며, 프로그래머는 데이터를 메모리 몇 번지에 저장하라고 코딩하지 않고, 문자로 된 변수명을 사용한다.

마찬가지로, 함수도 메모리 몇 번지로 jump하라고 코딩하지 않고, 문자로 된 함수명을 사용한다.

이를 Symbolic하다고 표현하며, 주소 바인딩 방식은 프로그램이 적재되는 물리적 메모리의 주소가 결정되는 시기에 따라 세 가지로 분류할 수 있다.

<br />

![image](https://user-images.githubusercontent.com/71188307/156890004-1ab854de-9a36-414b-93d4-25b9f785f41a.png)

<br />

- `Compile Time Binding`
  - 프로그램을 컴파일할 때 물리적 메모리 주소가 결정되는 주소 바인딩 방식
  - 컴파일 하는 시점에 해당 프로그램이 물리적 메모리의 몇 번지에 위치할 것인지를 이미 결정하므로 프로그램이 절대 주소로 적재된다는 뜻에서 절대 코드를 생성하는 바인딩 방식이라고도 부른다
  - 프로그램이 올라가 있는 물리적 메모리의 위치를 변경하고 싶다면 컴파일을 다시 해야 한다
  - 멀티프로그래밍이 없던 시절에는 사용됐으나, 현대의 시분할 컴퓨팅 환경에서 거의 사용하지 않는다

<br />

- `Load Time Binding`
  - 프로그램이 실행 될 때 물리적 메모리 주소가 결정되는 주소 바인딩 방식
  - Loader의 관리 하에 물리적 메모리 주소가 결정되며 프로그램이 종료될 때까지 물리적 메모리 상의 위치가 고정된다
    - Loader는 사용자 프로그램을 메모리에 적재하는 프로그램이다
  - 컴파일러가 재배치 가능 코드를 생성한 경우에만 가능하다

<br />

- `Execution Time Binding` or `Runtime Binding`
  - 프로그램이 실행된 후에도 프로그램이 위치한 물리적 메모리 주소가 변경 될 수 있는 바인딩 방식
  - CPU가 주소를 참조할 때마다 해당 데이터가 어느 물리주소에 위치에 존재하는지 확인해야하는데, 이때 `MMU(Memory Management Unit)`의 주소 매핑 테이블을 사용한다
    - MMU는 논리적 주소와 물리적 주소를 매핑해주는 하드웨어 장치이다 (데이터베이스 매핑 테이블을 연상하라)
  - MMU, 기준 레지스터, 한계 레지스터 등과 같은 하드웨어적인 자원이 필요하다
  - 현대의 시분할 컴퓨팅 환경에서 사용되는 방식

<br />

## MMU(Memory Management Unit)

---

![image](https://user-images.githubusercontent.com/71188307/156890264-594e4195-8654-4687-842b-a4188f4ed6ba.png)

<br />

![image](https://user-images.githubusercontent.com/71188307/156890486-86575be0-1ade-4bf1-a1de-3316ab261786.png)

<br />

- Relocation Register (Base Register)
  - 사용자 프로세스의 논리적 주소 0번지에 매핑된 물리적 주소를 의미
  - 사용자 프로세스는 항상 자신의 시작 주소가 0번지라고 생각하지만, 이는 논리 주소일 뿐이며, 실제 물리 주소는 0번지가 아닐 수 있다
  - 위 그림에서 P1은 CPU를 통해 논리 주소 346번지의 데이터를 요청했으나, 실제로 이 사용자 프로세스가 할당받은 물리 시작 주소는 14,000번지로 MMU에 저장돼있으므로 MMU는 14,000에 346을 더한 14,346번지의 주소를 반환한다
- Limit Register
  - 사용자 프로세스에게 할당된 메모리의 최대 크기를 저장하는 레지스터
  - 운영체제가 사용자 프로세스에게 할당한 메모리를 넘어 바깥의 메모리를 침범하면, 이 프로세스는 악의적인 프로세스(바이러스 등)일 가능성이 있으므로 이를 차단해야 한다
  - 위 그림에서 운영체제가 P1에게 할당한 메모리의 크기는 3,000이며, 물리 주소는 14,000~17,000 이다

<br />

![image](https://user-images.githubusercontent.com/71188307/156890581-fd4ac610-ee81-440d-9f3f-a03355577dfd.png)
