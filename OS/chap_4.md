# ----프로세스 관리----
### 프로세스 생성(복제)
> 부모프로세스가 자식 프로세스 생성(복제, code/data/stack/PCB)
- 각각 별도 프로세스 --> 경쟁발생
- 프로세스 트리 형성
- 자원 필요
    - OS로부터 받음
    - 부모와 공유 OR 공유X
- 자원 공유
    - 원칙적, 일반적으로는 공유X
    - 가능한 공유할 수 있으면 효율적
- 수행
    - 부모, 자식 공존하며 수행되는 모델
    - 자식이 끝날때까지 부모 대기 ---> wait 시스템 콜
- 운영체제에게 부탁해서 실행
    - processID는 data공간에 저장
    - **<font color="lightgreen">fork() 시스템 콜</font>**
        - 자식이 부모 프로세스 주소 공간, OS 데이터 복제 생성
        - 부모 context(PC) 그대로 copy
        - 문제점
            - 자식이 부모라고 하는 경우
            - 모두다 똑같은 제어흐름을 따름
        - 해결방안
            - 자식하고 부모 구분
                - fork()을 실행 후 return값이 다름
                    - 부모 processID > 0
                    - 자식 processID == 0
    ```C
    <fork 관련 코드>
    int main(){
        int pid;
        printf("\n hello, who i am? \n") //부모만 실행
        pid = fork(); //복제 생성해라 ---> 복제된 자식 프로세스는 이 다음줄부터 실행
        if(pid==0) // 자식만 실행하는 부분
            printf("\n hello, i am children.\n")
        else if(pid>0) // 부모만 실행하는 부분
            printf("\n hello, i am parent.\n")

    }
    ============================
    <결과값>
    부모
    hello, who i am?
    hello, i am parent.

    자식
    hello, i am children.
    ```
    - **<font color="lightgreen">exec() 시스템 콜</font>**
        - 복제된 것에 다른 프로그램으로 덮어씌우는 것
        - 새로운 프로세스로 바꿈
        - 다시 이전상태로 복원X(돌아가기X)
        - 꼭 자식을 만들지 않아도 됨
        - 따옴표 안에 "프로그램 이름 2번" + "argument 나열" + null pointer 쓰기
    ```C
    <exec 관련 코드>
    int main(){
        int pid;
        pid = fork(); 
        if(pid==0){ 
            printf("\n hello, i am children.\n");

            //exec() 시스템 콜 ---> 이순간부터 이전의 기억은 없어지고 새로운 프로세스(/bin/date)로 바뀜 ---> /bin/date 프로세스의 main부터 다시 시작
            // "/bin/date"(날짜, 시각 출력해주는 프로그램 이름) 두번 넣어주고 프로그램에 넘겨줄 argument 적고 맨 마지막에 null pointer 넣어줌 ---> 실행
            execlp("/bin/date", "/bin/date", (char*)0); 
        }
        else if(pid>0) // 부모만 실행하는 부분
            printf("\n hello, i am parent.\n");
    }
    =================
    <결과값>
    부모 
    hello, i am parent.

    자식
    hello. i am children.
    /bin/date 프로그램 실행

    *************************************************************
    int main(){
        printf("\n hello, i am children.\n");
        execlp("/bin/date", "/bin/date", (char*)0); 
        printf("\n hello, i am parent.\n"); // 프로그램이 바뀌어서 영원히 실행X.
    }
    ===============
    <결과값>
    hello, i am children.
    /bin/date 프로그램 실행

    *****************************************************************
    int main(){
        printf("1");
        execlp("echo", "echo", "hello", "3", (char *)0); //echo라는 명령어 실행, printf랑 비슷
        printf("2"); //영원히 출력X
    }
    ==================
    <결과값>
    1
    hello 3
    ```
---
### 프로세스 종료
- **<font color="lightgreen">exit 시스템 콜</font>**
    > - 마지막 명령 수행후 OS에게 알려줌
    > - 자발적 종료
    - 자식이 부모에게 output data 보냄(wait 시스템콜)
    - 자식이 먼저 종료
    - exit(); 코드 밑으로 실행 안되고 종료되버림
- abort
    > - 부모가 자식 수행 프로세스 강제 종료
    > - 비자발적 종료
    - 자원 한계치 초과시
    - 자식에게 할당된 task 없음 ---> 존재, 필요 이유x
    - 부모 exit된 경우
    - ex) ctrl+Z
- **<font color="lightgreen">wait 시스템 콜</font>**
    - 이 프로세스를 sleep 시키는 것(blocked 상태)
    - 자식 프로세스 생성 후 wait 시스템 콜
        - 자식 프로세스가 종료되길 기다리면서 부모 blocked 상태
        - 자식이 종료되면 ready 상태 ---> CPU 얻음
```C
<wait 관련 코드>
int main(){
    int childPID;
    printf("1 \n");
    childPID = fork();
    if(childPID == 0)
        printf("2 \n");// 출력하고 종료하면 ---> wait 다음부터 실행
    else{
        printf("3 \n");//부모라서 여기 먼저 출력했다가
        wait(); // blocked 상태 ---> 자식 끝나길 대기
    }
    printf("4 \n");
}
================
<결과값>
부모
1
3
2
4
```
```
유닉스의 경우
- 바로 복제 생성X 
- 일반적인 상황
- 일단은 부모 주소공간(code, data, stack)을 공유, 공유할 수 있는거 다 공유
- PC만 하나 더 만들어서 같은 위치 가리키는 정도만 유지
- 수정시, 서로 다른 것 생길 때
- copy on write기법(COW)
    > write 할 때 copy해서 새로운 내용 생성(수정시)
    - 부모, 자식이 달라질 수 있음
        - data 변수값 달라질 수 있음
        - 각자 instruction을 실행 ---> 함수 호출 다름 ---> stack에 쌓인 내용 다름 ---> 서로 다른길 
    - 그제서야 부모의 공유하던 메모리 공간을 일부 copy(write된 부분만)해서 자식이 가짐
```
---
### 프로세스 간 협력
- 독립적 프로세스
    - 원칙적으로 하나의 프로세스가 다른 프로세스 수행에 영향X
    - cf) 부모가 자식 kill
- 협력 프로세스
    - 서로 정보 주고받음
- 협력 메커니즘(interprocess communication;IPC)
    - messaing passing
        > 메세지 전달
        - 프로세스끼리 직접 전달X ---> 다른 프로세스에게 커널을 통해 메세지 전달
        - interface 측면
            - direct communication
                - send(받게될 프로세스이름, 보낼메세지) ---> (커널통해 이동) ---> receive(받게된 프로스세 이름, 받은 메세지)
            - indirect communication
                - random, 받을 프로세스 받아라
                - send(메일박스(M), 보낼메세지) ---> (커널통해 이동) ---> receive(받게된 프로스세 이름, 받은 메세지)
    - shared memory
        > 주소공간 공유
        - 일부 주소 공간을 두 프로세스가 공유(물리적인 메모리)
        - A,B 각자 code, data, stack 有 ---> (커널에 shared memory 하겠다는 시스템 콜) ---> 매핑을 해놓음(shared)
    ```
    thread
        - 1개 프로세스 내에 여러 thread끼리 공유
        - 다른 프로세스 협력X
    ```