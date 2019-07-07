# ----process synchronization = concurrency control(병행제어)----
> - 공유데이터 동시 접근(race condition) 시 데이터 불일치 발생
>
> - 해결책 
>   - 실행 순서 메커니즘 필요
>   - 동기화 필요
- 데이터 접근
    - 데이터 저장위치에서 읽음 ---> 연산 ---> 연산된 결과를 원래 위치에 저장
- OS에서 race condition
    - 커널 수행 중 interrupt 발생 시 
        - 커널이 CPU에서 실행중일 때 interrupt 발생 ---> interrupt handler(커널 내 코드)가 interrupt 처리 ---> 원래상태로 되돌아옴 
        - 해결책
            - 실행중인 작업 끝난 후 interrupt 처리 
    - process가 시스템콜을 하여 커널모드로 수행중인데 context switch가 일어나는 경우
        - 해결책
            - 프로세스가 커널 모드에 있을 때는 할당 시간이 끝나도 CPU 압수당하지 않고 사용자 모드로 돌아올 때 압수
    - multiprocessor에서 shared memory내의 커널데이터
        - 해결책
            - 1번 1개 CPU만 커널에 입장
            - 커널 내부에 있는 공유 데이터 접근 때마다 데이터에 대한 lock/unlock 사용
- critical-section problem
    - n개의 프로세스가 공유 데이터 동시 접근시 발생
    - critical-section = 공유데이터로 접근하는 코드
    - 하나의 프로세스만 critical-section 접근, 나머지는 접근 못하도록 해야함
- critical-section 문제 해결법의 충족조건
```
가정
- 모든 프로세스 수행 속도 = 0
- 프로세스들 간의 상대적ㅇ니 수행속도 가정X
```
    - mutual exclusion(상호배제)
        - critical-section:프로그램 = 1:1 
    - progress(진행)
        - critical-section내에 아무도 없을 때 들어가고자 하는 프로세스가 있으면 들어가게 해야함
    - bounded waiting(유한대기)
        - 특정 프로세스 입장에서 critical-section에 들어가고자 할 때 지나치게 오래 기다리는 starvation이 없어야 함
---
### S/W적으로 lock을 푸는 알고리즘
1. mutual exclusion 만족 & progress 불만족
```C++
// 교대로 critical section에 들어가도록 설게되있음
// 어떤 프로세스가 critical section에 들어갓다가 나와야지만 다른 프로세스 차례가 됨
int turn; //누구차례인지 알려주는 것
initially turn = 0; //0번 프로세스(P0)차례이다.

do{
    while(turn!=0);
    critical section //turn=0일 때 critical section에 들어옴
    turn = 1; //상대방에게 넘겨줌
    remainder section
}while(1);
```
2. mutual exclusion 만족 & progress 불만족
```C++
//각 프로세스마다 flag 소유
//들어가겠다고 선언만 한 프로세스들이 많으면 눈치만 살핌
boolean flag[2]; // flag == critical section에 들어가고 싶은지에 대한 의중 여부 
initailly flag[모두] = false; // 모두 들어가고 싶어하지 X

do{
    flag[i]=true; // Pi가 들어갈거다라고 선언
    while(flag[j]); // 다른 프로세스 들어가있는 여부 확인, flag[j] == true라고 생각하고 Pi는 대기
    critical section
    flag[i]=false; // CS 빠져나오면 상대방이 들어갈 수 있게 함
    remainder section
}while(1);
```
3. peterson's
    - turn + flag 사용
    - 모든 조건 만족
    - 문제점
        - busy waiting(spin lock)
            - while 돌면서 lock ---> 주어진 CPU 시간동안 check만 하다가 반납할 수도 있음
            - 비효율적인 방법
```C++
do{
    flag[i]=true;
    turn=j;
    while(flag[j] && turn==j);
    CS
    flag[i]=false;
    remainder section
}while(1);
```
---
### <font color=red>synchronization hardware(찾아보기)</font>
- 메모리를 읽고 데이터W/R이 하나의 instruction으로 동시에 가능하면 간단하게 lock 문제 해결 가능(H/W instruction 지원)
- Test_and_set(a)
    1. 읽기 후
    2. 데이터 write
```C++
//1 ---> 누가 이미 lock을 걸어놓은것
boolean lock=false;//0으로 진입

do{
    while(Test_and_Set(lock));//0으로 먼저 읽히고 아무도 없는거니까 CS에 진입 ---> 1로 write
    CS
    lock=false;//lock을 풂
    remainder section;
}
```
---
### 세마포(semaphores)
- 앞의 방식들 추상화
- semaphore S
    - 종류
        - counting
            - 자원 갯수 여러개
            - 여분의 자원 counting
        - binary(mutex)
            - 0 또는 1
            - 자원의 갯수 = 1일 경우
    - integer var
    - 자원의 갯수라고 생각
    - 두가지 연산만 가능
        - P(S)
            - while(S<0) do no-op;
                S--;
            - semaphore 획득하는 과정 ---> 자원가져가는 것
            - S=1 ---> lock 걸기
        - V(S)
            - S++
            - 반납하는 과정 ---> 자원반납
            - S=1 ---> lock 풀기
```C++
semaphore mutex; // initially 1: 1개가 CS에 들어갈 수 있음

do{
    P(mutex);
    CS
    V(mutex);
    remainder section
}while(1);
```
- block/wakeup 방식(sleep lock)
    - 세마포 정의
        ```C++
        typeof struct
        {
            int value; //세마포
            struct process *L; //프로세스 대기 queue 생성
        }semaphore
        ```
    - block
        - 세마포 획득 불가 ---> 그 세마포 block
    - wakeup(P)
        - 세마포 반납 ---> block 프로세스 중 하나 깨움
    - 획득 못하면 세마포 기다리는 프로세스드를 queue에 연결해놓음
    - P(S)
        - 자원획득
        - 자원여분이 있으면 획득, 없으면 block(sleep)
        ```C++
        S.value--;
        if(S.value <0){
            add this process to S.L;
            block();
        }
        ```
    - V(S)
        - 자원반납
        - 
        ```C++
        //S.value != 자원갯수 ---> 상황을 나타내는 것
        S.value--;
        if(S.value <= 0){
            // 자원반납을 했는데도 음수이면 잠들어 있는 프로세스 존재
            remove a process P from S.L;
            wakeup(P);
        }
        ```
- 세마포 구현 방식 비교
    - 보통은 block/wakeup이 효율적
        - 계속 CPU 쓸 필요없이 누군가 자원을 가지고 있으면 CPU 반납하고 block 되는 것---> 이용률 향상
    - block/wakeup overhead VS CS 길이
        - block/wakeup 
            - ready to block, block to wakeup overhead 발생
            - CS길이가 짧으면 busy-wait가 효율적
        - CS 길이 
            - 길이가 길면 한번 lock을 걸고 풀지 않는데도 while문만 돌면서 대기
            - 낭비되는 CPU 시간 증가
            - block/wakeup 필수
---
### Deadlock
- 어떤일을 하려고 할 때 두 개를 다 보유해야 할 수 있는 일이 있을 때 ---> 세마포 S,Q는 배타적으로 프로세스 하나만 사용할 수 있음 --> 문제발생
```
P0-P(S) CPU 얻은다음 뺏겨서 P1-P(Q) CPU 얻고
P0 ---> P(S); P(Q);
P1 ---> P(Q); P(S);

각자 쓰려는 자원을 상대가 가지고 있는 상태 ---> 영구적 기다림 ---> 영원히 가지지 못함 ---> deadlock
```
- 해결방법
    - 자원획득 순서 획일화
        - 모든 프로세스 S 얻고 Q 얻도록

- starvation
    - 특정 프로세스가 자원 얻지 못하고 영원히 기다림
    - deadlock도 포함됨
---
### synchronization 문제들
1. bounded-buffer(producer-consumer)
    - 유한한 버퍼 크기 환경에서 생산자-고객 문제
    - 공유데이터
        - 버퍼
        - 버퍼조작변수
            - empty
            - full 버퍼 시작위치
    - 동기화 변수
        - 상호 배타적
            - binary 세마포 필요
            - 동시 접근시
        - 자원counting
            - integer 세마포
            - 남은 자원 counting시
    ```
    - 프로세스 종류
        - 생산자
            - 공유 버퍼에 데이터 생성 후 집어넣음
        - 소비자
            - 데이터 꺼냄
    - 프로세스 다수 존재
    ```
    - 생산자 2명이 한곳에 동시에 데이터 생성 후 넣으면 문제발생 ---> 생산자가 데이터 넣으려할 때 버퍼에 lock 걸어서 생산자, 소비자 접근 못하도록 막아야함(소비자 2명이 동시에 한 곳 데이터 빼려할 때도 마찬가지)
        - 공유 전체에 lock을 걸어서 배타적으로 접근하도록 하는 세마포
    - 버퍼가 다 찬 상황에서 생산자가 또 도착해서 데이터 생성 후 넣으려고 할 때 ---> 생산자는 자원이 없는 상태 ---> 소비자가 데이터 꺼내면 데이터 생성 가능 
        - n(비어있는 버퍼) = n(사용가능한 자원)
        - 자원 counting 할 세마포 변수 필요
    ```C++
    <생산자가 해야될일(소비자는 반대로)>
    semaphore full=0, empty=n, mutex=1;
    do{
        produce an item in x
        P(empty); //1. empty 버퍼 존재? 없으면 기다림(소비자가 데이터 꺼낼때까지)
        P(mutex); //2. 공유 데이터 lock
        
        add x to buffer//3. empty 버퍼에 데이터 입력 및 버퍼조작
        
        V(mutex);//4. unlock
        V(full);//5. full 버퍼 하나 증가
    }while(1);


    <소비자>
    do{
        P(full);//1. full 버퍼 존재? 없다면 기다림(자원생성될때까지) 
        P(mutex);//2. 공유 데이터 lock
        
        remove an item from buffer to y//3. 버퍼에서 데이터 꺼냄
        
        V(mutex);//4. unlock
        V(empty);//5. empty 버퍼 갯수 하나 증가
        consume the item in y
    }while(1);
    ```
2. reader-writer
    - 프로세스 종류
        - reader 여러개
        - writer 여러개
    - 공유 데이터
        - DB
        - readcount
            - 몇개의 reader가 읽고있는지?
    - 동기화 변수
        - mutex 세마포
            - binary
            - readcount lock 용도
        - db 세마포
            - binary
            - db lock 용도
    - read 동시에 가능
    - writer는 배타적으로 가능

    ```C++
    int readcount = 0;
    DB자체;
    semaphore mutex=1, db=1;

    <writer>
    // reader lock 상태면 끝날 때까지 대기
    // reader가 계속 들어오면 starvation 발생
    // 어느정도 read되면 writer에게 넘겨주면 해결
    P(db);//db lock
    writing DB is performed
    V(db);

    <reader>
    // reader lock 걸려 있어도 그 이후 읽기만 가능하도록 함
    P(mutex);//mutex lock ---> readcount lock
    readcount++;//공유 변수라서 많이 몰리면 1만 증가된 상황 가능 ---> lock 필요
    if(readcount == 1) P(db);//처음 reader가 lock(writer 접근X) ---> 이미 lock되어있어 다른 reader는 lock 필요X
    V(mutex);// readcount unlock
    reading DB is performed
    P(mutex);//readcount lock
    readcount--;
    if(readcount==0) V(db)//마지막 프로세스는 db unlock ---> writer 접근 가능
    V(mutex);//readcount unlock
    ```
3. dining-philosophers
    - 5명의 철학자 업무
        - 생각
            - 젓가락 놓음
        - 식사시간
            - 왼쪽, 오른쪽 젓가락을 이용
    ```c++
    semaphore chopstick[5];

    do{
        P(chopstick[i]);
        P(chopstic[(i+1)%5]);

        eat();

        V(chopstick[i]);
        V(chopstick[(i+1)%5]);

        think();
    }while(1);
    ```
    - 문제점
        - deadlock 발생가능 ---> 모두 왼쪽 젓가락 잡음 ---> 식사때까지 가지고 있음 --> 아무도 오른쪽 젓가락 사용X
    - 해결방안
        - 4명의 철학자만 테이블에 동시착석
        - 젓가락을 두개 모두 잡을 수 있을 때만 젓가락 잡을 수 있음 ---> 하나만 잡을 수 있으면 아예 못잡음
        - 비대칭
            - 짝수(홀수) 철학자는 왼쪽(오른쪽)부터 잡도록
            - 순위 존재
---
```
- 세마포 문제점 ---> monitor 사용
    - 어려움 코딩
    - 정확성 입증 어려움
    - 자발적 협력 필요
    - 한번의 실수가 모든 시스템에 치명적 영향
        - V - CS - P
            - mutual exclusion 불만족
            - 동시에 접근하고자 함
        - P - CS - P
            - deadlock
            - 아무도 자원반납 안하고 대기
```
### monitor
- 객체 지원하는 코드에서 사용가능
- 공유데이터 접근하려면 monitor라고 정의된 내부 프로시저를 통해서만 접근 가능
- 내부 프로시저는 동시에 실행, 접근X --> lock 필요X
- 나머지 프로시저 queue에서 대기
- 자원counting 필요
    - condition variable
        - 여분 有 --> 접근
        - 여분 無 --> 대기(sleep) wait()
        - 접근하고 빠져나갈때 기다리는 프로세스 깨워줌 ---> signal()
1. bounded-buffer
```C++
monitor bounded_buffer{
    int bufer[N];
    //condition var은 값이 없고 자신의 queue에 프로세스를 매달아서 sleep, 깨우는 역할만 함
    condition full, empty;

    void produce(int x){
        if there is no empty buffer
            empty.wait();
        add x to an empty buffer
        full.signal();
    }

    void consume(int *x){
        if there is no full buffer
            full.wait();//quque에서 대기
        remove an item from buffer and store it to *x
        empty.signal();
    }
}
```
---
###<Q&A>
1. mutex와 세마포차이
mutex - 하나의 프로세스 내의 충돌 방지를 위한 lock
		  (thread 관련)
세마포 - 여러 프로세스들 중 하나의 프로세스만 사용하도록 lock

2. Semaphore는 Mutex가 될 수 있지만 Mutex는 Semaphore가 될 수 없습니다
