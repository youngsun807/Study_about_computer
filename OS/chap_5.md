# ----CPU scheduling----
> - 필요이유 : I/O bound job, CPU bound job 혼재되어 있기 때문
> - 필요성
>> - 공평보다 효율성 높이기
>> - interactive job이 오래 기다리지 않게 하는 것
- 프로그램은 CPU, I/O burst 연속 ---> 각각 시간은 다름
- I/O bound job 
    > CPU를 짧게, 자주 쓰는 것
    - CPU 많이 쓰는 JOB 아님
    - 중간에 I/O가 너무 자주 끼어들어서 CPU 시간이 너무 짧은 것 ---> I/O빈도 높음 ---> CPU burst 짧음
    - interactive job --> 사람과 계속 interaction ---> CPU를 우선적으로 주는 것이 필요
- CPU bound job
    - CPU를 굉장히 오랫동안 써서 빈도는 적을 수 밖에 없음 ---> 빈도 적음 ---> CPU burst 긺
- 문제(issue)
    - 누구한테 우선권?
    - 얼마나 시간을 주고 언제 뺏을 것이냐?
- CPU scheduler
    - OS 커널 코드로 구현되어있음
    - CPU를 누구한테 줄지 결정
- dispatcher
    - OS 커널 코드로 구현되어있음
    - 누구한테 줄지 결정하면 CPU를 넘겨주는 역할(문맥교환)
- 필요한 경우
    1. RUNNING ---> BLOCKED
        - CPU를 잡고 있다가 오래걸리는 작업(I/O)을 하러 간 경우
        - 자진해서 CPU 반납
    2. RUNNING ---> READY
        - CPU를 계속 쓰고 싶은데 무한정 CPU를 잡을 수 없으니 timer interrupt를 걸어서 일정시간 지나면 CPU 압수 ---> 대기줄로 보냄 ---> 다른 프로세스에게 CPU 넘김
    3. BLOCKED ---> READY
        - 오래걸리는 작업(I/O) 종료 후 device controller가 interrupt를 걸어서 ready로 바꿔 CPU를 다시 얻을 수 있는 상태로 바꿈
        - 일반적으로 I/O가 끝났다고 해서 바로 CPU 넘기지 않음
        - 넘기는 경우도 있음
            - 우선순위 기반 scheduling
    4. TERMINATE
        - 프로세스가 종료되서 다른 프로세스에게 CPU를 넘기는 경우
        - 자진반납
    ```
    1,4번의 경우
    - 비선점형, nonpreemptive(빼앗지않고 자진반납)
    - CPU를 가지고 있어도 더이상 instruction 실행X ---> CPU 자진 반납

    2,3번의 경우
    - 선점형, preemptive(빼앗음)
    - CPU를 계속 쓰고 싶지만 번갈아 써야 되기 때문에 강제 압수
    - 넘겨주고 싶지 않지만 우선순위가 떨어져서 강제 압수
    ```
---
### CPU 성능 척도
- CPU 이용률
    - 전체 시간 중에서 CPU가 놀지 않고 일한 시간 비율
    - 최대한 CPU 이용비율 높이기
- 처리량
    - 주어진 시간동안 몇개의 일을 처리했느냐?
    - 최대한 많이 처리하기
- 소요시간, 반환시간
    - CPU를 쓰러 들어와서 다 쓰고 나갈때까지 걸린 총 시간
    - CPU burst가 끝나고 나갈 때까지 걸린 총 시간
    - 기다렸다가 CPU 얻어서 running 하고 나갈 때까지 걸린 총 시간
- 대기시간(waiting time)
    - 순수하게 기다린 시간
    - CPU를 쓰고자 하더라도 ready queue에 대기하는 시간
- 응답시간(response time)
    > time sharing에서 사용자 응답과 관련되서 별도로 도입한 개념
    - ready queue 들어와서 처음으로 CPU를 얻기까지 걸린 시간
---
### scheduling alrorithms
1. CPU가 1개
    1. 한줄 서기(ready queue 한 줄)
        - FCFS(first-come first-served)
            - 일반적인 queue와 비슷
            - 비선점형 ---> 비효율적
            - 앞에 어떤 프로세스가 있냐에 따라 기다리는 시간에 영향을 미침
            - convoy effect 
                > 긴 프로세스가 도착해서 짧은 프로세스들이 기다려야 하는 경우
        - SJF(shortest-job first)
            - CPU를 사용하고자 하는 시간(CPU burst time)이 가장 짧은 프로그램에게 우선권(priority 방법에 포함)
            - 전체적인 queue 길이 축소 ---> waiting time 축소 ---> average waiting time 축소
            - 방식
                - 선점형(SRTF)
                    - CPU 필요시간이 더 짧은 프로세스가 들어오면 압수해서 넘겨줌
                    - <font color="lightcoral">min(average waiting time)에 최적화 ---> 이 시간보다 짧은 average 값 X</font>
                    - 프로세스 도착 때마다 scheduling
                - 비선점형
                    - 더 짧은 프로세스 들어와도 현재 처리하는 프로그램 종료까지 기다림
                    - CPU 끝난 시간마다 scheduling
            - 문제점
                - stavation 현상
                    > CPU 사용시간이 긴 프로세스는 CPU 사용 못할 수 있음
                - CPU 사용 시간 예측 불가
                    - CPU burst 추정은 가능 ---> 과거 흔적으로 추정 --> SJF 구현 가능
                        - tn = 실제 cpu 사용 시간 --> 과거데이터
                        - τn+1 = CPU 사용 예측 시간
                        - τn+1 = α*tn + (1-α)*τn
        - SRTF(shortest-remaining-time first)
        - priority
            - 작은 숫자 = 우선순위 높음
            - 선점형
            - 비선점형
            - 문제점
                - starvation
                    > 우선순위가 낮으면 실행 못함
            - 해결방법
                - aging
                    > 오래 기다리면 우선순위 높여주기
        - RR(round robin)
            - 선점형
            - CPU를 줄 때 동일한 시간을 할당함(q)
            - 응답시간 속도 향상
            - wating time ∝ CPU burst
            - q large -> FCFS
            - q small -> context switch overhead 커짐
            - SJF보다 waiting time 길지만 response time 짦음
            - 일반적으로는 CPU 사용시간을 모르는 여러 프로세스 혼재 ---> RR이 유리
    2. 여러줄 세우기(ready queue 분할)
        - multilevel queue
            - 줄 결정 scheduling --> 줄 내애서 scheduling
            - 각 queue는 독립적인 scheduling 알고리즘 채택
            - 줄별로 CPU 시간을 적정 비율로 할당 ---> starvation 해결
            - 우선순위 높은 순 ---> 낮은 순
                - system process
                - interactive process
                - interactive editing process
                - batch process
                - student process
            - 각자 계급이 존재 --> 자기 계급 줄에만 줄서기
            - 어느 프로세스에 속할 것인가
        - multilevel feedback queue
            - 계급 변동 有 ---> 줄 변동 有
            - 위의 queue가 비어야 다음 queue 실행
            - 고려사항(issue)
                - queue 몇개 ? 
                - queue의 scheduling 알고리즘?
                - queue 어디로 들어갈지?
                - 승격, 하강 기준?
            - 우선순위 ∝ (1/time quantum(q))
2. CPU 여러개
    - multiple processor scheduling
        - 한줄 서기 or 여러줄 --> CPU마다 다름
        - load sharing
            - 일부 CPU가 놀지 않도록 하기
        - symmetric multiprocessing(SMP)
            - 각 프로세서가 각자 알아서 scheduling
        - asymmetric multiprocessing
            - 하나의 프로세서가 모든걸 책임, 담당
3. real time scheduling
    - hard
        > deadline 반드시 지킴
    - soft
        > 다른 프로세스에 비해 높은 우선순위얻음
4. thread scheduling
    - local
        - user level thread(OS가 thread 존재 모름)
        - OS는 어떤 프로세스에게 CPU 줄지 결정
        - 프로세스 결정되면 어떤 thread에게 CPU를 줄지 내부에서 결정
        - OS 관여X
    - global
        - kernel level thread(OS가 thread 존재 앎)
        - 프로세스 scheduling처럼 결정
        - OS 관여
---
### 알고리즘 평가방법
- queueing models
    - 이론적인 모델
        - 도착률, 처리율이 확률분포로 주어지면 계산 후 성능척도 결과 나옴
- implementation & measurement
    - 실제 시스템 구현 후 돌려보면서 성능 측정
- simulation
    - 모의 프로그램 작성 후 trace(input data)를 입력하여 결과 비교
    - 여러개 시도