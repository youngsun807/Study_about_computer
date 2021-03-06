< Q&A - MAC에 관한 내용 >
> MAC주소는 게임할 때 불법프로그램 잡을 때 IP는 변경이 가능하므로 (컴퓨터 1대 : MAC주소 = 1 : 1) ---> 맥주소 변경 불가로 MAC주소로 차단, 메인보드의 주소
# ---- IEEE 802 시리즈 ----
### LLC, MAC 계층
- LAN 환경에서 데이터 계층 = LLC + MAC
    - LLC(logical link control)
        - 데이터계층의 상위
        - 데이터계층의 기본기능
        - WAN의 데이터계층과 거의 유사
        - 부분적인 차이 존재
    - MAC(medium access control)
        - 데이터계층의 하위
        - 물리적 선로 특징 + 매체간의 연결방식에 따른 제어
        - 다중 접근 채널방식 이용 ---> 공유매체에 프레임 전송방식에서 충돌 발생 가능
            - 해결방안
                - 충돌 허용 후 조치
                    - 이더넷
                        - CSMA/CD
                        - 충돌 감지 후 재전송
                        - 매체 길이 ∝ 프레임전송 지연, 충돌 발생 가능성
                        - 프레임
                            - MAC 프레임 = MAC 헤더 + LLC프레임(LLC계층이 MAC계층에게 전송하도록 요청한 데이터) + MAC 트레일러
                                - MAC 헤더
                                    - preamble
                                        - 수신호스트가 송신호스트의 클록 동기를 맞추는 용도
                                    - start delimiter
                                        - 프레임의 시작 위치 구분
                                    - destination address
                                        - 수신호스트의 MAC 주소
                                    - Source Address
                                        - 송신호스트의 MAC주소
                                    - length
                                        - 데이터필드에 포함된 가변길이 전송데이터 크기
                                - LLC 프레임
                                    - data
                                - MAC 트레일러
                                    - pad
                                    - checksum
                                        - 데이터 변형 오류 감지
                        - 연결 방식
                            - 고전방식
                                - 트랜시버
                                - 리피터
                                - 관리 문제 발생
                                    - 중간에 케이블 문제 발생 시 망 전체 단절
                                    - 케이블 물리적 이상 ---> 이상한 동작들
                                - 개선방향
                                    - 허브
                                        - 중심에 연결되어 있는 모든 호스트에 전송
                                        - 각 호스트는 외형상 스타형 구조로 허브에 연결
                                        - 내부 동작 원리 = 공유버스방식
                                    - 스위칭 허브
                                        - 주소를 보고 해당되는 포트에만 데이터 전송
                                        - 각자 개별적 통신 가능
                                        - 스위치 기능
                                            - 모든 호스트에게 프레임 전송X
                                            - 목적지로 지정도니 호스트에게만 전송
                                            - 동시에 여러 호스트가 데이터 전송 가능
                                        - 장점
                                            - 스위치 허브 용량 허용 시 각 호스트는 할당된 LAN 용량 모두 사용
                                            - 일반 허브에서 스위치허브로 교체하기 쉬움
                - 원천적으로 충돌 차단
                    - 타임 슬롯 배정(시간 정함)
                    - 토큰
        - 토큰 링
            - 순환 구조 전송매체
            - 대기모드, 전송모드로 동작
            - 프레임 구조
                - 토큰 프레임 + 데이터 프레임
                    - 토큰 프레임
                        - SD
                        - AC
                            - P*3 + T + M + R*3 = 8비트
                                - P 
                                    > 우선순위
                                - T
                                    > 토큰
                                    - 토큰 프레임=0
                                    - 일반 프레임=1
                                - M
                                    > 모니터
                                    - 모니터 호스트 지날때=1
                                    - 1인 상태로 모니터 호스트 지나가면 송신호스트의 회수 기능 오류로 간주
                                    - 모니터 호스트
                                        - 토큰 생성 기능
                                            - 토큰보유한 호스트 고장 등의 원인에 의한 토큰 분실
                                            - 분실  감시 시 토큰 재생성
                                        - 데이터 무한순환 방지
                                            - 송신호스트가 전송데이터 회수하지 않는 오류
                                            - 전송데이터 회수 후 토큰 생성
                                - R
                                    > 예약
                        - ED
                    -  데이터 프레임
                        - SD
                            - 시작 구분자
                        - AC
                            - 접근 제어
                        - FC
                            - 프레임 제어
                        - destination address
                        - source address
                        - data
                        - checksum
                        - ED
                            - 끝 구분자
                        - FS 
                            - 프레임 상태
        - 토큰 버스
            - 물리적 = 버스구조
            - 논리적 = 링구조
            - 토큰이라는 제어 프레임 사용
            - 프레임구조
                - MAC 헤더
                    - preamble
                        - 수신호스트가 송신호스트의 클록 동기를 맞추는 용도
                    - start delimiter
                        - 프레임의 시작 위치 구분
                    - frame control
                        - 데이터프레임과 제어프레임 구분
                        - T*2 + C*6 = 8비트
                    - destination address
                        - 수신호스트의 MAC 주소
                    - Source Address
                        - 송신호스트의 MAC주소
                - LLC 프레임
                    - data
                - LLC 트레일러
                    - checksum
                        - 데이터 변형 오류 감지
                    - end delimiter
                        - 프레임 끝 위치 구분
- IEEE 802시리즈
    - LAN 표준안 연구결과 == 802 시리즈
    - 802.1
        - 관련 표준안 전체소개
        - 인터페이스 프리미티브 정의
    - 802.2
        - LLC 프로토콜 정의
    - 802.3~
        - 물리, MAC 계층
        - 이더넷
            - 1-persistent CSMA/CD 방식의 LAN 환경 규정
                - 1-persistent CSMA
                    - 프레임 전송 전 채널(공유버스) 사용여부 확인(carrier sense)
                    - 채널 사용 중 ---> 유휴상태까지 대기
                    - 유휴상태 ---> 확률 1의 조건으로 프레임 전송
                    - 2 이상의 호스트에서 동시에 유휴상태로 판단 ---> 충돌 발생
                    - 충돌 발생 ---> 임의의 시간동안 대기 후 처음부터 다시 시작
                - CD
                    - 2 이상의 호스트 채널이 유휴상태라고 판단 ---> 충돌 발생
                    - 충돌 감지기능 필요
                    - 충돌 감지 ---> 진행중인 프레임 전송중지
            - Non-persistent CSMA
                - 프레임 전송 전 채널 사용 여부 확인
                - 채널 사용중 ---> 유휴상태 확인X
                - 임의시간동안 대기 후 다시 채널 감지 시작
                - 1-persistent보다 충돌 확률 낮음
            - P-persistent CSMA
                - 슬롯 채널방식에서 주로 사용
                - 채널 사용 중 ---> 다음 슬롯까지 대기 후 다시 채널 감지 시작
                - 채널 유휴상태 ---> p의 확률로 프레임 전송
    - 802.5 = 토큰링
    - 802.11 = 와이파이
    - 802.15 = 블루투스 (wireless LAN)