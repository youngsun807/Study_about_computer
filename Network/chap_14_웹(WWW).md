# ---- 웹서비스구조 ----
### 개요
- 전세계적으로 TCP, UDP, SCTP포트 80으로 지정
    - 보안을 위해 8000, 8080 이용
    - 보안이 강화된 HTTPS(HTTP over TLS/SSL) ---> TCP, UDP, SCTP포트 443 이용
- 웹브라우저는 http, https서비스에 지정된 포트를 통해 서버와 연결시도
- 웹 서버 연결 ---> 클라이언트 정보 요구에 대해 서버가 웹 문서 회신
- 사용자 요구마다 연결 설정과 해제 반복
---
### 클라이언트-서버모델
- URL
    - uniform resource locator
    - 서버 자원 명칭
    - 사용하는 프로토콜 + 서버 호스트 이름 + 서버 내부 파일 경로명 
    - UNIX/LINUX
        - 로그인 이름 hong
        - 개인 홈 디렉터리 = /public_html/index.html
        - http://www.korea.co.kr/~hong
- 연결설정, 해제
    1. 사용자가 웹브라우저에게 웹서버 URL주소 입력
    2. 웹브라우저가 DNS서버에게 웹서버 호스트 이름을 IP주소로 변경요청
    3. 웹브라우저가 <IP주소+포트80번>의 웹서버와 TCP접속 시도
    4. 웹브라우저가 웹서버에게 최초 화면을 위한 GET 명령 전송
    5. 웹서버가 웹브라우저에게 요청한 웹문서 회신
    6. 웹브라우저와 웹서버 사이의 연결해제
    7. 웹브라우저가 사용자 화면에 웹문서 출력
---
### HTTP(hypertext transfer protocol)
- 웹브라우저는 URL을 이용하여 원하는 자원 표현
- 요청(GET)
    - HTTP 클라이언트가 서버에 요청 전송
    - 요청 메소드 , URL, HTTP 버전과 기타 부가정보 포함
- 응답(POST)
    - HTTp 서버가 요청결과인 응답코드가 포함된 정보 회신
- 비상태 연결(stateless)
    - 요청 응답 이후 연결 끊김 ---> 비상태 프로토콜
- MIME(multipurpose internet message extensions)
    - 기존 ASCII 문자로 구성된 텍스트만이 전송 가능했던 전자우편을 멀티미디어 데이터 전송도 가능하도록 확장
    - header
        - MIME-version
        - content-type
        - content-transfer-encoding
            - 메시지를 0,1로 인코딩하는 방법 정의
            - 다양한 type제공
                - Base64
                    - 최상위비트가 0일 필요가 없을 때 byte data전송
                    - 연속된 비트데이터를 24비트 블록으로 분할
                    - 각 블록은 4개의 단락(6비트)으로 분리
                    - 각 단락은 하나의 문자로 해석 ---> ASCII 변환
                    - 24비트가 4개 문자 ---> 32비트가 전송 25%의 오버헤드
        - content-id
        - content-description
    - MIME와 MIME 유사메시지(HTTP에서 사용) 차이점
        - MIME 유사메시지는 content-length필드 有
        - MIME 유사메시지는 content-transfer-encoding 대신 content-encoding, transfer-encoding필드 사용
- 요청메시지
    - 요청메시지 구성
        - 요청문
        - 헤더
        - 공백
        - 바디
    - 요청문 구성
        - 요청메소드
            - GET = URL이 가리키는 웹문서 전송 요청
            - POST = 클라이언트가 서버에 정보(사용자입력) 전송
            - PUT = 클라이언트가 서버에 문서(몸체에 포함)전송
        - URL
        - HTTP 버전
- 응답메시지
    - 응답메시지 구성
        - 요청메시지와 유사
        - 요청문 대신 상태문(status line) 사용
    - 상태문 구성
        - HTTP버전
        - 상태코드
        - 상태이름
    - 주요상태코드, 이름
        - 200 OK = 요청 성공적으로 수행
        - 202 ACCEPTED = 요청 수신, 즉각 실행되지 않고 있음
        - 400 BAD REQUEST = 요청 메시지 문법 오류
        - 401 UNAUTHORIZED = 요청 실행에 필요한 권한x
        - 403 FORBIDDEN = 요청거부
        - 404 NOT FOUND = 원하는 문서 찾을 수 없음
        - 500 INTERNAL SERVER ERROR = 서버 오류
        - 501 NOT IMPLEMENTED = 요청 수행할 수 없음
- 동작과정
    - 요청메시지
        - 개요
            - HTTP 서버 = uu.ac.kr
            - 요청메소드 = GET
            - 요청문서 URL = index.php
            - HTTP버전 = 1.1
        - 메시지 내용
            ```
            GET /index.php HTTP/1.1 ---> 요청문
            Host: uu.ac.kr ---> 헤더
            <공백 한줄>
            ```
    - 응답메시지
        - 개요
            - 상태문 = HTTP/1.1 200 ok
            - 헤더정보
            - 바디정보 = <HTML>로 시작하는 웹문서 내용
    - 시뮬레이션
        - HTTP 서버 = uu.ac.kr
        - HTTP 클라이언트 = kihyun.uu.ac.kr ---> telnet프로그램이 대행
---
### APM
- apache
    - 웹서버 프로그램
    - 대응되는 프로그램 = microsoft IIS...
- php
    - 유닉스, 리눅스 환경에서 지원
    - HTML언어 기능 보강
    - 대응되는 언어 
        - microsoft ASP(active server page)
        - java 기반 JSP(java server pages)...
- MYSQL
    - 데이터베이스 기능지원
    - 대응되는 DB
        - 오라클
        - 마리아
- 동작원리
    1. 웹브라우저가 apache에 웹문서 요청
    2. php 코드 처리 필요 시 php에 요청
    3. 데이터베이스 처리 필요 시 MYSQL에 요청
    4. 데이터베이스 결과 회신
    5. php가 실행결과인 HTML코드 회신
    6. 웹문서를 웹브라우저에 회신
---
---
# ---- HTML(hypertext markup language) ----
- 웹브라우저에서 데이터를 어떻게 표시하는지 나타냄
- HTML로 작성한 문서는 서버에 보관
- 클라이언트는 그 문서를 받아 화면에 표시
- 웹표준
    - XHTML(eXtensible HTML) = 정보 구조 표현
    - CSS 
    - cross browsing = 다양한 환경에서 브라우징 되도록 함
    - ``HTML5 = HTML + CSS + javascript API``
- 시맨틱 마크업
    - 시맨틱 검색
        - 검색로봇이 검색어 의미를 스스로 분석, 추리 ---> 사람이 원하는 정보를 정교하게 찾아주는 검색방법
        - 검색 로봇이 정보를 잘 추출할 수 있도록 구조적인 홈페이지 작성 중요
        - 일반검색방식
            - 사용자가 입력한 단어 위주로 검색
            - 가장 검색빈도수가 높은 순으로 제시
            - 사용자의 검색어 입력 후 다시 입력하는 검색어를 기록 ---> 연관 검색 생성
    - 시맨틱 마크업과 디자인 분리
        - 내용의 의미표현에 집중
        - HTML 대신 CSS 사용 ---> 구조, 표현 구분
- HTML5 마크업 사용
    - 앨리먼트
        - 블록레벨 ⊃ 인라인 레벨
    - 마크
    - 시맨틱 마크업 방법
        - 앨리먼트가 가지고 있는 본래 목적에 맞게 사용
    - 주요 태그 사용제안
        - 블록 레벨 = div
        - 인라인 레벨 = span
        - 그룹핑할 때 
            - header = 문서 머리말
            - nav = 메뉴 영역
            - article = 콘텐츠 영역
            - section = 문서 내용
            - footer = 꼬리말 영역
---
---
# ---- CGI(common gateway interface) ----
- 필요성
    - html로만 웹문서를 작성하는 경우 서버정보만을 일방적으로 받아들이는 단방향 정보 흐름
    - 사용자가 입력하는 정보에 따른 처리기능 필요
- 고급언어로도 작성가능 ---> 독립된 개발, 컴파일, 개별 프로세스로 처리하는 부담 때문에 스크립트 언어 선호
- java를 이용한 웹 응용 개발도 하나의 흐름
    - MVC패턴
    - 스프링