# ----virtual memory----
> **OS 개입**
### demand paging
> 요청이 있을 때 page를 메모리에 올림
- 장점
    - I/O 양의 감소
    - memory 사용량 감소
    - 빠른 응답시간
    - 더 많은 사용자(프로그램) 수용
---
### page fault
> 요청한 page가 메모리에 없을 경우
- overhead 큼
- 처리순서
    1. invalid page 접근 시 주소변환을 해주는 H/W(MMU)가 trap 발생 
    2. OS에게 CPU 넘어감(S/W 인터럽트)
    3. OS내 page fault 처리코드(page fault handler)가 실행됨
    4. 잘못된 요청이 아닌지?
        - 주소가 잘못되거나
        - 이 프로세스가 사용안하는 주소?
        - 접근 권한에 대한 주소?
    5. 빈 page frame 획득
        - if 메모리 full상태 ---> page 쫓아냄 ---> 빈 page 획득
    6. page를 disk--->memory에 올림
        - disk I/O가 끝날 때까지 CPU가 필요한 프로세스한테 뺏김(preempt) ---> disk block상태
        - disk read 끝나면 page table entry 기록, valid상태로 바꿈
    7. page fault난 부분이 CPU를 다시 잡게 되면 주소변환(MMU)
    8. 다음 명령 재개
---
### page replacement
> 어떤 page frame을 쫓아낼지 결정
- replacement algorithm
    > min(page fault rate)하도록
    - reference string
        > 시간 순서대로 참조된 page 번호 나열
    - 순서
        1. page 쫓아냄
        2. 그자리에 새로운 page 올림
        3. 쫓아낸 page entry를 invalid로 바꿈
        3. 새로운 page frame 번호를 entry에 적고 valid
    - 종류
        - reference string 안다고 가정
            - offline optimal algorithm
                > 미래에 참조될 page(reference string)를 다 안다고 가정
                - 가장 먼 미래에 참조되는 page를 쫓아냄
                - 최대 page fault 수를 도출 ---> 다른 알고리즘의 성능에 대한 upper bound 제공
        - 미래를 모름
            - FIFO
                > 먼저 들어온 page 먼저 out
                - FIFO anomaly
                    > 프레임크기 ∝ n(page fault)
            - least recently used(LRU)
                > 가장 오래전 참조된 page 쫓아냄
                - 구현
                    > 참조순서대로 linked list 생성
                    - 다시 참조/새로 참조 ---> 맨 아래로 내려감
                    - 쫓아낼 때 가장 위에 것 쫓아냄
                    - 복잡도 = O(1) ---> 쫓아내기 위해 비교 필요X
            - least frequently used(LFU)
                > 가장 적은 참조횟수 page 쫓아냄
                - 구현  
                    > MIN heap(자료구조) 생성
                    > 이진트리(자식 2개씩 있음)
                    - 맨 위에 min(참조횟수) page
                    - 밑으로 갈수록 참조횟수 많은 page
                    - 비교대상 = 밑에 있는 2개만
                    - 쫓아낼 때는 root에 있는 것 쫓아내고 heap 재구성
                    - 모두 비교
                    - 복잡도 = O(log n)
                    - 한줄세우기 X 
                        - 참조횟수 증가 ≠ max(빈도수)
---                
### 다양한 캐슁 환경
- 캐슁기법
    - 한정된 빠른공간(=캐쉬)에 요청된 데이터를 저장해두었다가 후속 요청시 캐쉬로부터 직접 서비스하는 방식
    - paging system, cache memory, buffer cashing, web cashing...
    - 시간제약으로 replace
        - buffer, web
            - 복잡도 = O(n), O(log n)
- paging에서 LFU, LRU 사용 가능? X
    - OS가 참조상황을 알 수 없음, 과거모름
    - clock(second chance, NUR(not used recently), NRU) algorithm
        - modified bit
            > 메모리 올라온 후 수정여부(write)
            > I/O 동반하는 page
            > 0인걸 우선해서 쫓아냄
        - reference bit
            > 참조여부
        - 구현순서
            1. 참조되면 해당 page reference bit=1로 하고 CPU로 읽음(H/W 업무)
            2. (reference bit=1)==(최근에 참조) ---> reference bit=0으로 바꿈
            3. reference bit=0, modified bit=0을 만나면 그 page를 쫓아냄(메모리 올라온 후 한번도 수정X)
            4. reference bit=0, modified bit=1을 만나면 backing store에 수정내용 반영후 쫓아냄(메모리 올라온 후 수정 발생)
---
### page frame의 allocation
- 프로그램에게 얼마만큼의 frame을 할당할 것인가?
- 너무 적게 할당 ---> page fault 증가
- scheme
    - equal
        > 균등하게 할당
    - proportional
        > 프로그램 비례하게 할당
    - priority
        > CPU 우선순위별로 할당
- global replacement
    - 때에 따라 프로세스 별로 메모리 할당량이 조절되기 때문에 미리 할당X
- local replacement
    - 프로그램 할당을 하고 새로운 page가 필요하면 자신에게 할당된 frame 내에서 쫓아냄
- thrasing
    > page fault가 자주 일어나는 상황
    - 문제상황
        1. page fault rate 증가
        2. CPU utilization 감소
        3. CPU 놀고있다 판단 
        4. OS는 프로그램을 메모리에 더 올려야 한다고 생각
        5. MPD(multiprogramming degree) 높여야 한다고 판단 
        6. 또다른 프로그램 올라감(MPD 증가)
        7. 프로세스당 할당된 frame수 더 감소 
        8. page fault 더 발생
        9. swap in/out 빈번
        10. CPU 놀고 있음 ---> 비효율적
    - 해결방안
        - MPD 조절해야함
        - 프로그램이 어느정도 메모리 확보할 수 있도록 해야함(working set algorithm, PFF)
        - working set algorithm
            - working set model
                > 어느정도의 메모리를 가지고 있어야 함
                ```
                - locality of reference
                    > 프로세스는 특정시간동안 일정 장소만을 집중적으로 참조
                - locality set(= working set)
                    > 집중적으로 참조되는 page 집합
                ```
                - woking set 부분은 한꺼번에 메모리에 올라와있는 기법 필요 ---> page fault 최소화
                - 전체가 올라오지 못하면 모든 frame 반납후 swap out(suspend) ---> thrashing 방지, MPD 조절
            - algorithm
                - 과거를 통해 working set을 추정
                - working set = 특정시간(window, Δ)동안 참조된 page, 중복없이 page 갯수
                - working set에 포함되어 있으면 쫓아내지 않음
                - working set 크기만큼 메모리에 올릴 수 있으면 올리고 안되면 suspend상태
                - 일정시간(window)동안 유지 후 버림
        - page fault frequency(PFF) scheme
            - MPD 조절, thrashing 방지
            - 직접 page fault, 특정 프로그램의 page fault rate 관찰
            - page fault rate 높은 프로그램에게 page frame 더 줌
            - 더 줄 메모리가 없으면 그 프로그램 통째로 swap out ---> 남아있는 프로그램의 page fault 감소시킴
```
**page size 커지고 있음**

- page size 감소 시
    <장점>
    - page 수 증가
    - page table 크기 증가
    - 내부 조각 감소
    - 필요한 정보만 메모리에 올라와 메모리 이용이 효율적
        - 일부분만 필요한데 크기가 크면 비효율적
    <단점>
    - locality 활용 측면에서는 좋지 않음
    - disk transfer 효율성 감소
        - 필요할 때마다 seek해야 함
```