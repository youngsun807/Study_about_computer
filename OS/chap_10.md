# ----file systems----
> 하드디스크에 저장하는 단위
- 파일 metadata 
    - 파일이 저장되어있는 디렉토리에 저장
- 연산
    - 생성
    - 읽기
        - 시스템콜
    - 쓰기
    - 삭제
    - 열기 
        > 파일의 속성이 메모리에 올라옴
    - 닫기
    - reposition(lseek)
- 파일 접근시
    - open -> read, write 시스템콜
- buffer cashing
    - OS가 자신의 공간 일부분에 내용을 copy해서 저장해놓음
    - LRU, LFU 사용가능
- protection
    - 접근 연산, 접근 권한 여부
    - control 방법
        - access control matrix
            - access control list
                > 파일별로 누구에게 접근권한이 있는지
            - capability
                > 사용자별로 접근권한을 가진 파일 및 권한표시
        - grouping
            > 세그룹의 접근권한을 9비트로 표시(rwx)
        - password
- mounting 연산
    - partitioning
        > 논리적인 디스크로 나눌 수 있음
        - 논리적인 디스크에는 file system을 설치해서 사용가능
    - mounting 된 root 디렉토리에 접근하면 다른 partition의 root 디렉토리 접근
- 파일 접근 방법
    - 순차
        - 카세트테이프
    - 직접(임의)
        - 원하는 위치로 바로 접근 가능
