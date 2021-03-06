# ---- 계층구조 ----
- 모듈화
    - 모듈
        - 독립적으로 동작하면서 상호유기적으로 통합 가능해야함
        - 적절한 인터페이스 필요
    - 자동차, H/W, 객체지향개념...
- 계층화된 모듈구조
    - 상위계층이 하위계층에서 제공하는 서비스를 요구하는 방식
    - 장점
        - 크고 복잡한 시스템 ---> 간단한 시스템들로 재구성 ---> 이해하기 쉽고, 설계/구현 용이
        - 모듈이 연동할 수 있는 인터페이스 제공
        - 전송 매체 양단의 호스트가 수행하는 프로토콜은 상호 대칭관계
        - 각 계층 오류 수정, 개선 시 시스템 교체없이 해당 모듈만 교체가능
    - 단점
        - 데이터가 하위계층으로 계속 복사되어야 함
---
### 프로토콜 설계 시 주요 고려사항
1. 주소 표현
    > 상대방 구분 방법 필요 ---> 주소체계
    - 호스트주소
    - 그룹주소
        - 브로드캐스트  
            - 모두가 다 보는 것
        - 멀티캐스트
            - 특정 그룹이 보는 것
2. 오류제어
    > 다양한 오류를 극복해야함
    - 도착하지 않았다는 것을 누가 어떻게 알 수 있는가? ---> 송신 호스트 ---> 받은 쪽에서 잘 받았다는 신호 보냄
    - 변형 발생은 어떻게 알 수 있는가? ---> 원래데이터와 추가확인 가능한 데이터 같이 전송
        - 타임아웃
    - 동일 데이터가 도착했다는 것을 어떻게 알 수 있는가? ---> 데이터 보낼 때마다 순서 필요
    - 필요요소
        - 응답
        - 타임아웃
        - 재전송
        - 순서번호
3. 흐름제어
    > 송신 호스트 전송 속도 조절
    - 송수신 호스트의 데이터 처리 속도 차이에서 데이터 분실 발생가능
        - 수신단 부하 > 송신단 부하
            > 원할 때 송신하지만 언제 올지 몰라서 수신단은 대기
    - 송신 속도 > 수신속도 ---> 버퍼 이용
        - 버퍼 제한 ---> full ---> 이후 데이터 버림 ---> 데이터 손실(분실)
    - ready/not-ready
4. 데이터 전달방식
    - 단방향
        - 한쪽 방향으로만 송신
    - 전이중
        - 양쪽 동시 송신
    - 반이중
        - 양방향 전송 가능
        - 동시 송신X
        - 데이터 전송 시점 관리 필요
5. 데이터 전송 우선순위
6. 긴급데이터 처리
---
### 서비스 종류
- 연결형
    - 전달경로를 설정하는 연결 설정 필요
    - 데이터 전송단계
    - 연결 해제 과정 필요    
- 비연결형
     - 연결 설정, 해제없이 데이터 전송
```
< 연결형 >
- 앞으로 전송될 데이터가 지나가야 되는 각각의 노드들이 준비되어있음 ---> 동일한 길을 따라서 여러 데이터 전송가능
- 많은 데이터, 안정적 전송

< 비연결형 >
- 매번 데이터를 어디로 보낼지 판단해서 송신 ---> 다른 경로를 통해 전송 가능
- 적은 데이터, 한번에
```
---
### 서비스 프리미티브
> 표현 = 종류.기능
- 종류
    - 연결형
        - connect
        - data
        - disconnect
    - 비연결형
        - data
- 기능
    - request
        > 클라이어트가 서버에 서비스 요구
        - 클라이언트에서 발생
        - 하위계층에 요구
    - indication
        > 서버에 서비스 요구가 도착했음을 통지
        - 요청이 하위계층을 통해서 상대방 계층에 도착
        - 서버에서 발생
        - 클라이언트 요청이 있었음을 상위계층에 알림
    - response
        > 서버가 클라이언트에 서비스 응답
        - 서버 to 클라이언트
    - confirm
        > 클라이언트에 응답이 도착했음을 통지
---
---
# ---- OSI 참조모델 ----
- 송신 호스트 응용계층에서 물리계층 도달 ---> 수신 호스트 물리계층에서 응용계층 도달
- 송신
    - 하위계층으로 이동시 각 계층 프로토콜에서 정의한 헤더 추가
- 수신
    - 상위 계층으로 이동시 헤더 정보 처리하고 제거
---
### 용어정의
- 계층n 프로토콜
    - 계층 n 모듈끼리 사용하는 통신 규칙
    - 역할
        - 정의된 기능 수행 + 필요한 정보 교환
        - 주소 표현, 오류제어, 흐름 제어...
- 동료(peer) 프로세스
    - 동일 계층에 위치한 통신 양단 프로세스
- 인터페이스
    - 상하위 계층 사이의 접속 방법
- 서비스
    - 상위 계층이 하위계층을 사용하는 방법
---
### 중계기능
- 중계 노드(라우터)
    - 물리, 데이터, 네트워크계층까지만 수행
    - 라우팅
        - 경로 배정
    - 헤더 정보 해석하여 적절한 경로로 전달
    - 다음 라우터로 보내기 전 헤더정보를 적절히 수정하여 전송
---
### 계층별 기능
1. 물리
    - 전송 매체의 물리적 인터페이스에 관한 사항 기술
    - H/W로 구현
    - 데이터 전송속도, 클럭 동기화, 물리적 연결형태(전선두께)...
2. 데이터 링크
    - 물리계층을 통해 전송 데이터의 물리적 전송 오류 해결, 흐름제어 
    - 2개의 노드가 1:1로 직접 연결된 환경에서 데이터 전송 지원
    - 프레임
        > 데이터 링크 계층을 통해 전송되는 데이터
        - 헤더에 포함되는 정보
            - MAC주소
            - 오류 제어 관련 추가정보
                - 오류 검출 정보
                - 순서번호
            - 흐름 제어 관련 추가정보
                - 버퍼크기
                - ready 상태
        - 효율적인 처리 위해 트레일러 이용
3. 네트워크
    > 경로설정
    - 네트워크 주소 기반으로 호스트 간 데이터의 전송 경로를 결정하는 라우팅 문제 처리
    - 전달경로
        - 정적
            - 사전에 미리 전해진 경로
        - 동적
            - 네트워크 상황등을 고려하여 판단
    - 패킷
        - 전송데이터 명칭
    - 혼잡제어
        - 전송 데이터가 과도하게 많은 경우 전송속도 조절
        - 네트워크 계층에서 수행하지 않을 경우 전송 계층에서 수행
            - **``Q&A 수행 계층에 따른 차이점?``**
                - 네트워크 : 네트워크 사이에서의 라우터의 속도 제어
                - 전송 : 송신 측 자체에서 전송속도 제어
4. 전송
    > 실제 통신하고자 하는 상대와 연결
    - 송신 프로세스와 수신 프로세스 연결 ---> end-to-end 통신기능
        - 프로세스 구별을 위해 주소 개념 필요(포트번호)
    - 논리적으로 구축되는 통신 담당자 사이 문제처리
        - 전송속도, 전송오류, 흐름제어...
5. 세션
    - 세션 제공
        > 상위적(논리적) 연결 개념
    - 대화 제어
    - 토큰 제어
        - 발언권을 넘기는 것
    - 동기화
        - 끊긴 지점부터 다시 시작
6. 표현
    - 데이터 의미, 표현방법 처리
    - 통신 양단에서 서로 이해할 수 있는 표준 방식으로 코딩
    - 암호화/압축 기능 처리
7. 응용
    - 사용자에게 유용한 다양한 서비스
    - http, ftp, telnet, email...
---
---
# ----TCP/IP----
- 구현환경
    - 시스템 공간
        - TCP/IP & 하위계층 ---> OS 커널 내부에 구현
        - 사용자가 TCP, UDP, IP기능을 이용할 수 있도록 소켓 인터페이스 제공
        - 하위계층은 LAN카드, 드라이버루틴으로 동작
    - 사용자 공간
        - 세션~응용계층 기능은 사용자프로그램으로 구현
- ARP(address resolution protocol)
    > IP를 알려줘서 해당 데이터 계층 주소 요청
    - 데이터 계층 프로토콜을 이용하여 데이터 전송시 네트워크 주소를 데이터 계층 주소로 변환해야 함
    - 방송기반
    - 경우
        - 동일망
            - 해당 IP 주소를 가지고 있는 호스트에서 자신의 데이터 계층 주소 알려줌
        - 다른망
            - 라우터가 대신 응답
- RARP(reverse ARP)
    > 호스트가 자신의 H/W를 알려주면서 해당 IP 주소 요청
    - 호스트는 자신의 IP주소를 디스크에 저장
    - 부팅시 LAN카드에서 MAC주소를 알아낸 다음 RARP로 자신의 IP주소 문의 ---> 반환
- ICMP(Internet control message protocol)
    - IP 프로토콜에 캡슐화
        - IP 위에 데이터 실어서 보내는 형태
        - 하는 방식은 전송계층이지만 네트워크제어 내용을 담고 있어 네트워크계층으로 간주
    - 주요 control message
        > 망에서 일어나는 상황을 호스트에게 알려주기 위한 목적
        - echo request/reply
        - destination unreachable...
    - 헤더
        - type + code + checksum + 나머지
