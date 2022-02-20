# CPU Scheduling
- CPU Bound job에게 더 CPU를 오래 주기 위함

## CPU Scheduling이 필요한 경우
1. running -> blocked ('나 cpu 필요없어...' io 요청하는 시스템 콜)
2. running -> ready (할당된 시간 다씀. 타이머 인터럽트)
3. blocked -> ready (io 완료 후 인터럽트. 얘한테 바로 cpu 가는거 아님)
4. terminate (프로세스가 종료됐을 때)

1,4는 자진 반납 / 2,3은 preemtive(강제로 빼앗음)

## CPU scheduler (독립적X, 운영체제 안의 CPU 스케줄링 담당 코드)
- ready인 프로세스 중 이번에 CPU를 받을 프로세스를 고른다.

## Dispatcher
- 실제로 CPU를 주는 놈.
- context switching을 하는 놈

