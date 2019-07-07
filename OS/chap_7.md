# ---deadlock(교착상태)---
> 일련의 프로세스들이 서로가 가진 자원을 기다리며 block 상태
- 자원
    - S/w + H/W
    - 사용절차
        1. request
        - allocate
        - use
        - release
- deadlock 발생조건(모두 만족해야 발생)
    - mutual exclusion(상호배제)
        > 매순간 하나의 프로세스가 자원 독점
    - no preemption(비선점)
        > 프로세스는 강제로 뺏기지 않음
    - hold and wait(보유대기)
        - 이미 보유한 자원은 가지면서 추가적인 자원 기다림
    - circular wait(순환대기)
        - 필요로 하는 자원들 간에 사이클이 형성됨
- 자원할당그래프
    > deadlock 발생 했는지 알아봄
    - available한 상황이 있기만 하면 deadlock 아니라고 봄
    - 원
        > 프로세스
    - 사각형
        > 자원
    - 화살표
        - 자원->프로세스
            - 이 자원이 프로세스에 속해있음
        - 프로세스->자원
            - 이 프로세스가 자원을 요청
            - 아직 자원 획득은 못함
    - 사각형 내부 점 
        - n(자원 안 인스턴스)
    - 그래프 내에 사이클 없으면 deadlock X
    - cycle 有
        - 자원 인스턴스 1개
            - deadlock
        - 자원 인스턴스 여러개
            - deadlock일수도 아닐수도
---
## deadlock 처리방법
1. 미연에 방지
    > 비효율적
    1. deadlock prevention
        - 이용률, 사용률 저하 발생
            > deadlock이 생길지 안생길지 모르는데 미리 예방을 해놓은거라 비효율적
        - 4가지 조건 중 하나를 차단해서 방지
            - hold and wait 없애려면
                1. 프로세스 시작될 때 평생에 필요한 자원 모두 할당 ---> 끝나면 자원반납 --> 프로세스 종료때까지 기다리면 자원 비효율성
                2. 필요할 때 자원 할당 ---> hold하고 있는데 다른자원 기다려야 함 ---> 가지고 있는 것 모두 반납
            - no preemption 없애려면
                - 자원 preemption 허용
                - 주로 save, restore이 가능한 자원들에서 deadlock을 막을 수 있음
                    - CPU
                    - memory
            - circular wait 없애려면
                - 획득 순서를 정함
    2. deadlock avoidance
        - 최악의 상황을 가정하는 것
        - 프로세스가 시작될 때 필요한 max(자원)을 미리 알고있다고 가정하고 deadlock 피해감
        - 어떤 프로세스가 자원 요청했을 때 deadlock 발생가능성이 있으면 자원 안 줌
        - 자원 인스턴스에 따라
            - 1개
                - 점선
                    - 프로세스->자원
                        > 평생에 적어도 한번은 자원을 요청할 수 있음
                        - 요청하면 점선으로 바뀜
                - deadlock 발생할 것 같으면 자원을 안 줌
            - 여러개
                - banker's 알고리즘
                    - allocation-max-available-need(max-allo.)
                    - 사용가능한 자원 내에서 필요한 양이 충족되는 순서대로 자원주고 사용이 끝난 프로세스는 자원반납
                    - 잠재적으로 요청하고자 하는 자원 최대량이 사용할 수 있는 자원에 완전히 충족되어야 함
                    - 자원반납되면 해당 프로세스의 allocation을 avaailable에 추가해서 충족여부 확인
        - safe state 유지
            > safe sequence 존재하는 상태
        - safe sequence
            - Pi의 자원요청 < (가용자원 + 모든 Pj(j < i)의 보유자원)
2. deadlock 발생하도록 놔둠
    1. deadlock detection and recovery
        - 자원요청하는대로 주고 상황이 이상할 때 deadlock detection, 발견되면 recovery
        - 자원 인스턴스에 따라
            - 1개
                - 자원할당그래프
                - wait-for 그래프(자원 제외 프로세스간의 연결상태)
            - 여러개
                - 테이블
                    - allocation-request-available
                    - request가 없으면 반환될 자원이라고 가정하고 가용자원에 다 합쳐 계산
        - recovery
            - 사살
                1. deadlock에 연관된 프로세스 한번에 kill
                2. deadlock 연루된 프로세스 deadlock 없어질때까지 하나씩 kill
            - 자원 강탈
                > deadlock에 연루된 프로세스의 자원 강탈
                - 누구한테 뺏을 지 정하고 뺏어서 deadlock 없앰 ---> safe state로 rollback ---> process restart
                - 뺏는 패턴을 조금씩 바꿈
                    > starvation 없애기 위해
    2. deadlock ignorance
        - deadlock 드물게 발생 ---> deadlock 조치가 더 큰 overhead일 수 있음
        - OS 아무일도 안함
        - deadlock 발생시 사용자가 대처

    
