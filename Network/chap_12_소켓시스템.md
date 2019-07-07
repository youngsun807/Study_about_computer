# ---- 주소표현 ----
### 소켓주소
- 프로토콜 종류에 따라 사용하는 주소체계가 다름
    - AF_UNIX
        - 유닉스 주소체계
        - 한 호스트에 존재하는 프로세스 사이 통신 지원
        - 주소체계는 파일 시스템 경로명을 기반으로 함
    - AF_INET
        - 인터넷 주소체계
        - 다른 호스트에 존재하는 프로세스 사이 통신 지원
        - 주소체계 (32비트 IP주소 + 16비트 포트번호) 기반
    - 통합주소쳬게
        - 프로토콜마다 주소체계를 지원하는 문법 구조가 다름
        - 문법 구조상 하나의 함수에서 다양한 주소체게를 지원하는데 어려움 有
        - 모든 주소체계를 수용할 수 있는 공통주소 체계 필요
---
### 소켓서비스
- 소켓 유형
    - SOCK_STREAM
        - 연결형 서비스 지원
        - AF_INET에서 TCP사용
    - SOCK_DGRM
        - 비연결형 서비스 지원
        - AF_INET에서 UDP 사용
    - SOCK_RAW
        - IP프로토콜 직접 사용
- 소켓함수
    - s = socket(int domain, int type, int protocol)
        - 매개변수로 지정된 유형을 지원하는 소켓 생성
        - 생성된 소켓을 가리키는 파일 descriptor 리턴
    - bind(int s, struct sockaddr *name, socklen_t *namelen)
        - s가 가리키는 소켓에 소켓주소 부여
        - name = 소켓주소
        - 해당된 포트번호로 온 것은 내것이니까 나한테 넘겨달라고 등록하는 행위
    - listen(int s, int backlog)
        - 소켓 활성화
    - accept(int s, struct sockaddr *addr, socklen_t *addrlen)
        - 클라이언트/서버 환경에서 서버 대기하는 역할
        - 클라이언트 connect()함수와 만나면 소켓 연결 설정
    - connect(int s, struct sockaddr *name, socklen_t namelen)
        - 클라이언트/서버 환경에서 클라이언트 연결설정 요청 수행
        - 서버 accpet() 함수와 만나면 소켓 연결 설정
    - send(int s, void *msg, size_t len, int flags)
        - 연결이 설정된 소켓에 데이터송신
        - 전송 데이터는 msg가 가리킴
    - recv(int s, void *buf, size_t len, int flags)
        - 연결이 설정된 소켓에서 데이터수신
        - 수신 데이터는 buf가 가리키는 공간에 저장
---
---
# ---- 시스템콜 ----
### socket() 함수
- 소켓 생성, 생성된 소켓의 descriptor 반환
- 문법
```C
    # include <sys/types.h>
    # include <sys/socket.h>
    int socket(int domain, int type, int protocol);
    //domain = 사용할 도메인 지정
    //type = 서비스 유형지정
    //protocol = 보통 0으로 지정 
```
---
### bind() 함수
- 문법
```C
    # include <sys/types.h>
    # include <sys/socket.h>
    int bind(int s, struct sockaddr *name, socklen_t *namelen);
    //s = socket() 함수가 리턴한 descriptor
    //name = 바인드할 소켓주소 표기
    //namelen = name에 보관도니 주소공간 크기
```
---
### 주소(정수)변환
- 컴퓨터마다 정수형 데이터 처리방법이 다를 수 있음
- 개별 호스트 --> 네트워크 변환
    - htonl()
    - htons()
- 네트워크 ---> 개별 호스트 변환
    - ntohl()
    - ntohs()
- 문법
```C
    # include <sys/types.h>
    # include <netinet/in.h>
    # include <inttypes.h>
    uint32_t htonl(uint32_t hostlong);
    uint16_t htons(uint16_t hostshort);
    uint32_t ntohl(uint32_t netlong);
    uint16_t htons(uint16_t netshort);
```
---
### listen() 함수
- 소켓에서 대기할 수 있는 n(연결요청) 지정
- 문법
```C
    # include <sys/types.h>
    # include <sys/socket.h>
    int listen(int s, int backlog);
    //s = socket() 함수가 생성한 연결형 서비스용 소켓
    //backlog = 일반적인 환경에서 5로 지정, 버퍼크기
```
---
### accept() 함수
- 서버 프로그램에서 클라이언트 연결 요청 대기
- 문법
```C
    # include <sys/types.h>
    # include <sys/socket.h>
    int accept(int s, struct sockaddr *addr, socklen_t *addrlen); // 새로운 소켓이 생성
    //s = socket() 함수가 생성한 연결형 서비스용 소켓
    //addr = 연결 요청한 클라이언트 소켓 주소 반환
```
---
### connect() 함수
- 클라이언트 프로그램에서 서버에게 연결 요청 수행
- 문법
```C
    # include <sys/types.h>
    # include <sys/socket.h>
    int connect(int s, struct sockaddr *name, socklen_t namelen); 
    //s = socket() 함수가 생성한 연결형 서비스용 소켓
    //name = 연결하고자 하는 서버 소켓주소
```
- 주소변환
    - IP주소 표기방식
        - 10진수 표기방식
            - 사람들 편의를 위해
        - 2진수 표기방식
            - IP프로토콜에서 사용
```C
    # include <sys/types.h>
    # include <sys/socket.h>
    # include <netinet/in.h>
    # include <arpa/inet.h>
    //inet_addr() = 10진수 ---> 2진수 변환
    //inet_ntoa() = 2진수 ---> 10진수 변환
    unsigned long inet_addr(const char *cp);
    char *inet_ntoa(const struct in_addr in);
```
---
### send()함수
- 연결형 서비스에서 데이터 송신
- 문법
```C
    # include <sys/types.h>
    # include <sys/socket.h>
    ssize_t send(int s, const void *msg, size_t len, int flags);
    ssize_t sendto(int s, const void *msg, size_t len, int flags, const struct sockaddr *to, socklen_t tolen);
    //s = socket()함수가 생성한 소켓
    //msg = 송신할 데이터
    //to = 비연결형 서비스에서 수신자 주소
    //sendto() = 비연결형 서비스에서 데이터송신
```
---
### recv() 함수
- 연결형 서비스에서 데이터 수신
- 문법
```C
    # include <sys/types.h>
    # include <sys/socket.h>
    # include <sys/uio.h>
    ssize_t recv(int s, void *buf, size_t len, int flags);
    ssize_t recvfrom(int s, void *buf, size_t len, int flags, struct sockaddr *from, socklen_t *fromlen);
    //s = socket()함수가 생성한 소켓
    //buf = 수신할 데이터 저장할 공간
    //from = 비연결형 서비스에서 송신자 주소
    //recvfrom() = 비연결형 서비스에서 데이터 수신
```
---
---
# ---- 네트워크 프로그래밍 ----
### 연결형 서비스
- 소켓
    - 네트워크 통신을 위한 S/W 교신점
- 2개 독립 프로세스가 네트워크를 통해 통신하려면 논리적인 연관관계를 맺어주는 소켓 필요
- 서버 동작
    - well-known포트에서 대기
    - 동작과정
        1. 서비스 교신점 공개(호스트 IP주소, 포트번호)
        2. 클라이언트로부터 발생하는 서비스 요구 대기
        3. 클라이언트에 서비스 제공
        4. 해당 클라이언트에 서비스 제공완료
        5. 단계 2로 이동
- 클라이언트 동작
    - 서버 well-known포트로 접속 시도
    - 동작 과정
        1. 원하는 서비스 제공하는 서버 확인
        2. 해당 서버와 연결시도
        3. 서버에 서비스 요청
        4. 서버에 서비스 요구완료
- 소켓함수 컴파일
    - 소켓관련 라이브러리 추가(solaris)
        - % cc - o time_client time_client.c -lsocket -lnsl
        - % cc - o time_server timer_server.c -lsocket -lnsl
    - 서버 먼저 실행 ---> 클라이언트 실행