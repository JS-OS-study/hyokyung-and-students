## File and File system

### 파일

- "A named collection of related information", 정보를 모아 한 이름으로 저장 / 일반적으로 비휘발성의 보조기억장치(디스크 류)에 저장
- os는 다양한 저장장치를 file이라는 동일한 논리적 단위로 볼 수 있게 해줌
- 파일관련 연산: create, read, write, reposition(lseek), delete, open, close 등

### 파일의 속성 (metadata)

- 파일을 관리할때 쓰는 정보: 파일의 이름, 유형, 저장위치, 파일사이즈, 소유자 등

### 파일 시스템

- 운영체제에서 파일과 그 메타데이터 등을 관리함. 파일의 저장방법을 결정하고 파일을 보호함

## Directory and Logical Disk

### Directory: 파일의 메타데이터 중 일부를 보관하는 특별한 종류의 파일
- 디렉토리 관련 연산: 파일 찾기, 파일 생성, 파일 삭제, listing a 디렉토리, 파일 이름 바꾸기 등

### Partition(= logical disk)

- 1 물리 디스크 안에 여러 논리 디스크(파티션)을 두는게 일반적임.
- 여러 물리 디스크를 하나의 파티션으로 구성하기도 함.
- 물리 디스크를 파티션으로 구성하고, 각각의 파티션에 파일 시스템을 깔거나 스와핑용도 등으로도 사용가능

## Open()

![open의 동작 과정1](https://user-images.githubusercontent.com/50111853/159148053-b095cd35-927d-4e82-9f85-664bfaa28968.png)

![동작 과정 시각화](https://user-images.githubusercontent.com/50111853/159148051-41af0d61-13ea-4ce1-af6b-ae97ca6bbb47.png)

- 이때 root는 고정이라 위치를 바로 알 수 있음. 디스크에 있던 root의 metadata가 메모리로 올라감
- 메모리에 있는 root의 주소를 찾고, root에 있는 a의 메타데이터에 접근
- 위 두 줄을 a,b 반복
- 그 후 b의 메타데이터를 가리키는 포인터 값(file descriptor)를 open 함수의 결과로 리턴
- 프로세스가 리턴값을 써서 read()함수로 컨텐츠를 읽음
- 읽어온 컨텐츠를 디스크 버퍼 캐시에 저장 -> 커널 메모리의 버퍼 캐시에 저장 -> 사용자 메모리의 버퍼 캐시에 저장

**file system에서는 메모리랑 다르게 LRU, LFU와 같은 알고리즘을 사용해 버퍼캐싱 가능**
**메모리 관리는 하드웨어들이 직접 했어서 운영체제가 관여 안 했지만 파일 시스템의 경우 시스템 콜을 통해 진행하므로 운영체제 관여 가능함**
