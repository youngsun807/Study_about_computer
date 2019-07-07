# ----메모리 관리(물리적)----
> OS 개입X ---> 모두 H/W 사용
### 메모리
    > 주소를 통해 접근하는 매체
    - 논리적 주소(가상주소)
        - **CPU가 보는 주소**
        - 각 프로세스마다 0번지부터 시작
    - 물리적 주소
        - 메모리에 실제 올라가는 위치
---
### 주소 바인딩(주소변환)
> 주소 결정
- 논리주소 --> 물리주소로 바뀔 때 주소변환
- 변환 시점에 따라
    - compile time binding
        > 컴파일 시에 일어남
        - 논리주소 그대로 물리주소로 변환(fix)
        - compile time binding에 의한 코드 = 절대코드(absolute code) ---> 컴파일러가 생성함
        - 메모리주소(물리주소)를 바꾸고 싶으면 컴파일을 다시해야함
    - load time binding
        > 실행 시작될 때 일어남
        - 물리적 메모리에 비어있는 곳을 찾아서 변환
        - 컴파일 코드 = 재배치가능코드(relocatable code) ---> 컴파일러가 생성함
    - execution time binding(run time binding)
        > 프로그램이 시작된 이후 실행하다가 중간에 물리적 메모리 주소가 바뀜
        - 현재 사용하고 있는 방식
        - CPU가 주소 참조할 때마다 binding 점검
        - 하드웨어적 지원 필요(MMU)
            - memory management unit(MMU)
                - 주소변환용 H/W
                - 프로그램이 통째로 올라갈때만 사용 가능
                - 레지스터 2개 이용(dynamic relocation)
                    - relocation(base)
                        > 시작위치
                    - limit
                        > 프로그램 크기(논리적 주소 범위)
                        > 다른 프로그램 메모리 주소 접근시 확인 및 제한을 줌
---
### dynamic loading(!= paging 기법)
- 해당 루틴이 불려질 때마다 메모리에 올림
- 가끔씩 사용되는 코드 양이 많을 경우 유용(오류처리 루틴...)
- 프로그래머가 코딩해서 사용
- OS 지원가능(라이브러리 이용)
### overlays
- 메모리에 실제 필요한 정보만 올림
- 수작업으로 프로그래머가 구현
- OS 지원X
### swapping
- 프로세스를 메모리에서 backing store로 쫓아냄
- backing store(=swap area)
    > 쫓겨나는 공간
    - 쫓아내면 swap out
    - 메모리로 올라오면 swap in
- 중기 스케줄러(swapper)에 의해 swap out 시킬 프로세스 선정
    - CPU 우선순위에 따라 swap out 
- 메모리로 돌아올 때 run time binding일 때 효율적
- swap time(transfer time, 데이터 전송시간) ∝ swap data 양
### dynamic linking
- linking
    > 컴파일에 필요한 것들을 모아서 하나의 실행파일로 만드는 것
> linking을 실행시간까지 미루는 기법
- staic linking
    - 라이브러리가 실행파일 코드에 포함
- dynamic linking(DLL)
    - 라이브러리 포함X ---> 포인터로 표시 
    - 프로그램 실행시 라이브러리 호출 부분에서 라이브러리 위치를 찾음 ---> 필요하면 메모리 올림, 실행
    - dynamic linking 해주는 library = shared library ---> DLL(windows)
---
### 물리적 메모리 관리
- 메모리 구성
    - 낮은 주소부분 = OS 커널 존재
    - 높은 주소부분 = 사용자 프로그램(프로세스) 존재
        - 상위부분 = 코드 데이터 구성
        - 하위부분 = 스택
        - 중간부분 = 사용되지 않는 주소영역
- 낭비되는 조각
        - 외부조각
            - 올릴려는 프로그램 > 메모리 영역일 경우
            - 사용될 수 있는 공간임에도 크기가 작아 사용안됨
        - 내부조각
            - 올릴려는 프로그램 < 메모리 영역일 경우
            - 프로그램이 할당됬지만 남는 메모리 영역
### 사용자 프로세스 영역 할당방법
1. 연속(contiguous)  
    > 연속적인 공간에 적재
    1. 고정 분할
        > 프로그램이 들어갈 사용자 프로세스 메모리 영역을 미리 나눔
        - 내부조각, 외부조각 생성됨
    2. 가변 분할
        > 미리 나누지X
        - 프로그램이 실행될 때마다 순서대로 메모리에 올림
        - 외부조각 생성됨
        - hole
            - 가용 메모리공간(비어있는 공간)
            - 다양한 크기의 hole이 여러곳에 존재
            - 프로그램 종료되면 할당공간에 추가
            - 프로그램 도착하면 수용가능한 hole에 할당
                - dynamic storage-allocation problem
                    > 어느 hole에 프로그램 넣을지 
                    - first fit
                        > 프로그램 크기 < hole ---> 처음 만난 hole에 할당
                    - best fit
                        > 프로그램 크기보다 큰 hole 중 가장 프로그램 크기와 비슷한 hole에 할당
                    - worst fit
                        > 모든 hole 중 가장 큰 hole에 할당
            - compaction
                - 여러 hole을 한 곳으로 몰아서 큰 hole 생성
                - 너무 작아서 사용못한 hole 문제 해결
                - 실행중인 메모리 영역에서 합치는 것 ---> 고비용, 복잡
2. 불연속(noncontigous)
    > 하나의 프로세스 메모리가 여러영역에 분산
    1. **paging**
        - 프로그램 주소공간(virtual memory)을 일정한 크기로 분할 = page
        - 메모리 주소공간(physical memory)을 일정한 크기로 분할 = (page)frame
        - 일부 backing storage, 일부 물리적 메모리에 저장
        - OS는 비어있는 page frame이 어딘지 관리
        - page 자투리 ---> 내부조각 발생
        - shared page(re-entrant code, pure code)
            - 여러 프로세스의 공유할 수 있는 코드는 하나의 frame에 mapping
            - 필요조건
                - read-only
                - 동일한 논리주소에 위치
        - private code, data
            - 각각 다른 frame에 mapping
        - **page table**을 이용하여 주소변환
            - 프로세스마다 존재
            - index, 배열처럼 주소search
            - entry
                > page table의 레코드, 데이터
                - 프로세스 page 갯수만큼 존재
            - 접근횟수 = 단계(주소변환) + 1(실제데이터 접근)
            - 문제점 ---> inverted page table
                - page table이 너무 큼
                - 공간 overhead
            - protection + valid + invalid bit 포함
                - protection
                    - 연산에 대한 접근 권한
                    (read/write/read)
                - valid
                    - page table 내의 메모리 주소로 가면 frame에 존재
                - invalid
                    - 프로세스가 해당 주소내용을 사용X
                    - page가 물리적 메모리에 있지 않고 backing store에 있는 경우
            1. 1단계 page table
                - 프로그램마다 필요, 큰 크기 ---> 메인메모리에 상주
                - 레지스터 2개 사용
                    - page table base(PTBR)
                        > page table
                    - page table length(PTLR)
                        > 테이블 크기, limit
                - 모든 메모리 접근 연산 = 2번 메모리 access ---> 비효율적 ---> 속도향상 : TLB
                    - page table을 통한 주소변환을 위한 접근
                    - 실제 데이터를 접근
                - TLB에 내용이 없으면 page table로 넘어감
                - 전체 search X ---> p만큼 떨어진 주소로 찾음
                - 사용되지 않는 page에 대한 entry도 무조건 table에 존재해야함
                - 논리 page 갯수 = page table 크기
            - TLB
                - 일종의 캐시
                - 메인메모리와 CPU 사이 존재
                - 주소변환하는 개체
                - 프로세스마다 필요 ---> context switch 때 flush(전체 TLB 비움)
                - 논리주소 page + 물리주소 page
                - 전체 search ---> 시간 오래걸림 ---> 병렬탐색(parallel search)
            2. 2단계 page table
                > page table을 위한 공간 축소위해 사용
                - 바깥page + 안쪽 page + 물리적 주소 page
                - page 1개 크기 = 4KByte = 2^12 byte ---> 물리적 주소 page 12bit(page offset)
                - 안쪽 page table 크기 = page frame 크기 ---> 1K = 2^10 ---> 10bit
                - 전체 32bit - (10+12) = 10bit --> 바깥page 크기
                - page 대부분은 사용이 안됨 ---> 사용되지 않는 page의 바깥 page table은 null pointer, 안쪽 table은 없음 ---> 공간축소, 효율적
            3. 다단계 page table
            4. inverted page table
                - 시스템마다 page table 존재
                - page frame : page table entry = 1:1
                - entry = n(물리적 메모리의 frame)
                - process id + process
                - 단점
                    - 전체 테이블 검색
                - 해결점
                    - associative 레지스터에 넣어 사용
    2. **segmentation**
        - 의미있는 크기로 분할 != 크기 균일
        - segment number, offset
            - segment number
            - offset
                > segment 내에서 얼마나 떨어져 있는지
        - segment table
            - segment 갯수 적음
            - base 레지스터(STBR)
                > segment table의 물리적 메모리에서 시작위치
            - length 레지스터(STLR)
                > segment table 길이
                > n(프로그램이 사용하는 segment)
                > n(segment) 
                > n(entry)
        - protection, shared 측면에서 paging보다 유리
            - 공유
                > 같은 segment number여야 함
        - allocation 측면에서 가변분할 방식과 같은 문제 발생
            - dynamic storage-allocation problem 발생
            - 외부조각 발생
        ```
        paging vs segmentation
        - paging : 프로그램 table 크기가 큼 ---> 메모리 낭비 
        - segmentation : segment = 크기
        ```
    3. paged segmentation
        - segment : page = 1:N
        - 메모리에 올라갈 때는 page 단위 ---> allocation 문제X
        - segment : page table = 1:1
        - protection, shared ---> segment table에서 진행 
        - 주소변환 2번
            - segment 주소변환 ---> page table 시작위치
            - page table 주소변환 ---> page 내에 데이터 위치