지난 번엔 논리 주소를 물리 주소로 변환하는 것에 대해 배웠는데, 이 작업은 운영체제가 전혀 관여하지 않는다.
이번엔 운영체제가 관여하는 가상 메모리에 대해 배운다.

## Demanding Paging: 실제로 필요할때 페이지를 메모리에 올림
- IO 양 감소, 메모리 사용량 감소(한정된 메모리를 효율적으로 사용 가능), 빠른 응답시간, 더 많은 유저 수용

[](vm1)

- 처음엔 모든 페이지 엔트리가 invalid로 초기화되어있음.
- Valid bit(당장 필요한 부분)은 demanding paging으로 메모리에 이미 올라가 있다 (4,6,9)
- invalid bits는 backing store(스왑 영역)에 있다.
- 주소 변환시에 invalid bit이 set되어있으면 page fault 났다고 함. (그럴때 os가 처리함)

### page fault

[](vm2)
[](vm3)

- refer(1)하려고 했는데 invalid(메모리에 올라와있지않으면) trap 발생해 cpu가 운영체제로 넘어감
- 운영체제가 스왑영역에서 페이지 올림. 그다음 invalid를 valid로 바꾸고(5) restart(6)
- page fault는 자주 발생하지 않는다. 하지만 한번 발생하면 운영체제가 넘어가고, 하드웨어적으로 처리하느라 엄청난 시간(오버헤드)이 소모됨

### 빈 페이지가 없는 경우(=Free frame이 없는 경우)

- Page replacement 필요함. 어떤 프레임을 뺏어올지 결정해야함
- Rplacement algorithm: page fault rate을 최대한 낮추는게 목표
