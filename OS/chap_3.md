# ----프로세스----
### 프로세스 문맥
- 특정 시점을 놓고 봤을 때 이 프로세스 수행 정도, 상태를 나타내기 위해 사용되는 개념
- 프로세스 시작 --> 프로세스 주소공간 생성 ---> 프로세스 CPU 잡으면 ---> pc가 code 어느 부분 잡고 ---> 기계어를 읽어서 CPU 내부로 불러들여서 ---> 레지스터에 어떤 값 넣고 --> 산술 논리장치에서 연산 --> 그 결과 레지스터 저장 or 바깥 메모리에 저장 ---> 어느 시점에 어느 지점까지 와 있는가?
- 문맥을 나타내기 위해서는 pc가 어딜 가리키고 있는가? 프로세스 메모리에 어떤 내용을 담고 있는가? 함수 호출했으면 stack에 쌓아놧겟지? 데이터 공간에 변수에 든 값이 들어있는지, 어떤 instruction까지 담겨 있는가?
- 종류
    - CPU 관련 하드웨어 문맥
        - PC counter  --> 어디까지 갔는지?
        - 각종 register  ---> 무슨 값을 가지고 있는지?
    - 프로세스 주소 공간
        - code
        - data
        - stack
    - 프로세스 관련 커널 자료 구조
        - PCB(process control block)  
            - 프로세스 시작 될 때마다 운영체제는 프로세스를 관리하기 위해서 데이터 영역에 자료구조를 하나 두고 있다
            - 메모리, CPU 얼마를 줘야 될지? 등등의 관리 역할, 운영체제가 이 프로세스를 어떻게 평가하는지?
        - kernel stack
--- 
### 프로세스 상태
> 운영체제 상태와는 관련X
- running 
    - CPU 사용상태
- ready
    - CPU만 주면 바로 실행 가능 상태
    - CPU는 1개뿐 ---> CPU를 기다리는 상태, 다른 필요한 것들은 준비완료(조건만족)
    - ready상태인 프로세스들이 CPU를 번갈아 사용 ---> time sharing
    - queue 구조 ---> scheduling관련
- blocked(sleep, wait)
    - CPU를 얻어도 당장 수행이 불가능한 상태
    - CPU 대기하면서 자기 일도 하고 있고 CPU 나름 다른일도 하고 있는 상태
    - ex) I/O장치, 디스크 W/R 이후 수행해야 할 때 ...
    - queue 구조 ---> scheduling관련
- suspended(stopped)
    - 중기 scheduler 때문에 생김
    - CPU + 외부에서 강제로 정지시킨 상태
    - I/O 작업은 진행 가능(suspended ready)
    - ex) ctrl+Z ---> 사람이 재개시켜야 위의 상태로 올라감
- new
    - 프로세스 생성중인 상태
- terminated
    - 수행이 끝난 상태
    - 프로세스 자체는 끝났지만 정리 작업은 남아있는 상태
```
공유데이터(s/w)
- 1개의 프로세스가 공유데이터 사용하면 다른 프로세스 대기
- blocked 상태
- queue 상태
----------------------------------------------------------------
blocked : 자신이 요청한 event가 만족되면 ready
suspended : 외부에서 resume해 주어야 active
```
---
### PCB
- OS가 각 프로세스를 관리하기 위해 프로세스당 유지하는 정보
- 구성요소
    - os가 관리상 사용하는 정보
        - process state
        - process ID
        - scheduling information
        - priority
    - 문맥 표현을 위한 정보(CPU 수행 관련 H/W 값)
        - program counter
        - registers
    - 메모리 관련
        - code 위치
        - data 위치
        - stack 위치
    - 파일 관련
        - 열려있는 file에 대한 정보들
---
### 문맥교환 **중요**
> **one 사용자 프로세스 TO another 사용자 프로세스**
- CPU가 사용자 프로세스로부터 다른 프로세스로 넘어가는 과정
- 필요한 작업
    - CPU 뺏기려 할 때 ---> 해당문맥의 레지스터들값, PC값, 메모리 map 정보 저장(실제 CPU 내 값들) in 해당프로세스 PCB(커널이 관리하는 주소공간 내)
    - CPU 얻을 때  ---> 얻으려 한 순간부터 실행 ---> 그 문맥의 값들 복원
> - "시스템 콜(본인이 필요해서 운영체제한테 요청할 때), 인터럽트(H/W, I/O 컨트롤러들이 정보를 전달하고 싶을 때) 발생 시 CPU 사용권한 move 사용자 프로세스 to OS 커널" != 문맥교환
> - 시스템콜, 인터럽트 이후 OS가 다른 프로세스에게 넘겨주는 경우 == 문맥교환
> - 운영체제가 처리한 후 다시 CPU에게 넘겨지는 경우 != 문맥교환
> - 커널에 잠시 갔다가 돌아오는 경우 --> CPU context 정도만 저장하고 사용 가능 ---> 상대적 overhead 적음
- 프로세스가 바뀌는 경우 해당 프로세스가 사용하던 캐시메모리를 다 삭제해야함 ---> overhead
---
### 프로세스 scheduling queue
> ready, device queue ⊂ job queue
- PCB 내의 포인터로 가리키며 연결
- job
    - 현재 시스템 내에 있는 모든 프로세스들
- ready
    - 현재 메모리 내에 있으면서 CPU를 기다리는 프로세스들
- device
    - I/O 장치마다 서비스 기다리는 프로세스들
---
### scheduler
- 장기(job scheduler)
    - 어떤 프로세스에게 memory 줄 지 결정(admitted 결정)
    - 프로세스 시작되서 new ---> ready로 넘어올 때 메모리에 올라갈 것을 admitted 필요 ---> cpu 얻음
    - 시작 프로세스 중 어떤 것을 ready queue로 보낼지 결정
    - degree of multiprogramming 제어(메모리에 동시에 올려놓은 갯수?)
    - time sharing 시스템(일반적인 컴퓨터)에는 보통 없음 ---> 무조건 ready ---> 중기scheduler
    - 디스크에서 메모리로 옮기는 과정이 길어서
- 단기(CPU scheduler)
    - 잦은 주기
    - 어떤 프로세스에게 cpu 줄 지 결정(running)
    - ready 상태에서 running으로 넘어갈 때 짧아서
- 중기(swapper)
    - 우리가 사용하는 컴퓨터는 중기로 관리
    - 메모리에 너무 많은 프로그램 동시에 올라가 있으면 성능 저하 ---> 프로세스를 통째로 메모리(I/O)에서 디스크로 쫓아냄
    - degree of multiprogramming을 제어
    - suspended state
---
### thread(lighweight process)
> - CPU 내에 프로세스 수행 단위
> - 프로세스 하나에 CPU 수행단위만 여러개
- 프로세스 실행되면 프로세스마다 주소공간, PCB 생성(code, data, stack)
- 프로세스 주소공간 : PC = 1:N --->프로세스 주소공간 1개 띄워놓고 현재 CPU가 CODE 어느 부분을 가리킬지 PC을 여러개 
- 프로세스 1개 내에서 thread들끼리 공유하는 것(task)
    - 메모리 주소공간(code, data)
    - 프로세스 상태 ---> PCB 내의 PC, 레지스터 제외 모든것
    - 자원들
- 구성(thread간에 별도로 독립적으로 가지고 있는 것)
    - CPU 수행과 관련된 정보
        - PC
        - 레지스터
        - 스택...
- 장점(하나의 프로세스 내에 여러개 thread)
    - 빠른 응답성 가능
        - 1개 thread == blocked ---> 다른 thread == running 가능
        - ex) 웹사이트 불러올 때 텍스트라도 불러오면 계속 불러오면서 사용자에게는 텍스트 보여주기
    - 자원 공유
        - 같은 작업을 다중 프로세스로 각각 처리 ---> 각각 주소공간 생성 ---> 메모리 공간 낭비/<br>
            ---> 하나의 프로세스 내에 여러 thread ---> 성능 향상, 자원 절약
    - 경제적(!= 빠른 응답성)
        - (새로운 프로세스 생성, 문맥교환 overhead) > (기존 프로세스에 1개 thread 추가 overhead)
    - multiprocessor 구조에서 병렬성 상승 ---> CPU 여러개일때만 해당
