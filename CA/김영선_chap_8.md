## **8.1 병렬처리의 개념 및 필요성**
### **< parallel processing >**
> 다수의 프로세서들이 여러개 프로그램, 분할된 부분들을 분담하여 동시에 처리
- 선결 조건
    - 많은 수의 프로세서들로 하나의 시스템 구성 --> 저비용, 고속 프로세스 사용이 가능해야함
    - 한 프로그램을 여러개 부분들로 분할 가능
    - 분할된 부분들을 병렬 처리한 결과 = 순차적으로 처리한 경우 결과
- 문제점
    - 문제분할
    - 프로세서간 통신
- 관련 기술
    - 병렬 프로그램 언어, 컴파일러 개발
    - 상호 배타 매커니즘 지원
    - 공유 자원들의 경합 축소, 이용률 극대화 --> OS 개발
---
## **8.2 병렬처리의 단위**
- 작업 단위 병렬성
    - 독립적인 작업 프로그램 단위
- 테스크 단위 병렬성
    - 기능에 따라 분할한 작은 프로그램 단위
    - 프로세서들간의 정보 교환 필요
- thread 단위 병렬성
    - 동시에 처리될 수 있는 가장 작은 크기의 독립적인 단위 프로그램(thread) 단위
- 명령어 단위 병렬성
    - 데이터 의존성이 존재하지 않는 여러개의 명령어들 동시에 수행
---
## **8.3 병렬컴퓨터의 분류**
### **<Flynn의 분류>**
- 구조적 특징에 따라 분류
    - 명령어 스트림
        - 프로그램 실행시 순서대로 나열된 명령어 코드 집합
    - 데이터 스트림
        - 명령어 실행 시 필요한 순서대로 나열된 데이터 집합
1. SISD
    > 단일 명령어 스트림 + 단일 데이터 스트림
    - 한개 명령어, 데이터 순서대로 처리 ---> 단일프로세서 시스템
    - 파이프라이닝, 슈퍼스칼라 구조
2. SIMD
    > 단일 명령어 스트림 + 복수 데이터 스트림
    - 배열 프로세서
    - 여러개 프로세싱 UNIT ---> 동작들은 하나의 제어 UNIT에 의해 통제
    - 데이터 스트림 --> 여러개 동시 처리
    - GPU
3. MISD
    > 복수 명령어 스트림 + 단일 데이터 스트림
    - 비현실적
    - 데이터 스트림 ---> 1개 처리
    - 정확한 결과값이 필요할 때 사용하는 것 같음(우주정거장, 비행기)
4. MIMD
    > 복수 명령어 스트림 + 복수 데이터 스트림
    - 밀결합 시스템
        - 공유 기억장치 구조
        - 다중프로세서 시스템
    - 소결합 시스템
        - 지역 기억장치를 가징 독립적인 컴퓨터 모듈로 구성
        - 프로세서간 통신 --> 메세지 전송
        - 다중컴퓨터 시스템
```
- RISC(reduced instruction set computer)
    - 내부에 명령어 단순화 ---> 최대한 일을 적게 하도록  ---> 실행속도 빠름, 성능 향상
- CISC(complex instruction set computer)
    - 명령어 복잡 ---> 여러 단계로 나누어 처리 ---> 하나 명령어 실행이 오래걸림
```
### **<기억장치 엑세스 모델에 따른 분류>**
1. 균일 기억장치(UMA)
    - 모든 프로세스들이 상호연결망에 의해 주기억장치 공유
    - 주기억장치 모든 영역 엑세스 가능, 소요시간 동일
    - 장점
        - H/W 간단
        - 프로그래밍 용이
    - 단점
        - 공유 자원 경합 높아짐 --> 시스템 크기 한계
2. 불균일 기억장치(NUMA)
    - 다수 UMA 모델 상호연결망에 의해 접속 --- UMA 한계 극복
    - 분산공유 기억장치 구조
    - 기억장치 엑세스 시간은 기억장치 위치에 따라 다름
        - 지역
        - 전역
        - 원격
3. 무 원격 기억장치(NORMA)
    - 프로세스가 원격 기억장치에 직접 엑세스X
    - 프로세서와 기억장치들은 메세지 전송으로 접속
    - 분산 기억장치 시스템
    - 다른쪽을 원하면 요청을 보냄

### **<시스템 구성방법에 따른 분류>**
1. 대칭적 다중프로세서(SMP)
    - 64개 이하의 프로세서 소유
    - 완전 공유 구조
    - 시스템 내에 하나의 OS만 존재
    - 대칭적
        - 모든 프로세서들이 필요시 직접 OS 코드 수행
        - 모든 프로세서들이 자원들을 동등한 권한으로 사용
    - 특징
        - 능력이 비슷한 프로세서 구성
        - 기억장치, I/O장치 공유, 상호연결망(네트워크)에 의해 접속
        - 모든 프로세서들은 동등한 권한 소유, 같은 수준의 기능 수행
        - 공유 기억장치를 이용하여 통신
        - 하나의 OS에 의해 통합적으로 JOB scheduling
        - 공유 자원 경합 발생 ---> 시스템 크기 한계
2. 대규모 병렬프로세서(MPP)
    - 무공유 구조
    - 수백, 수천개의 프로세싱 노드로 구성
    - 간단한 구조의 노드 프로세서 --> 단순 계산
    - 메세지전송으로 통신
    - 복잡도 높은 상호연결망 사용
3. 캐시 일관성 NUMA 시스템(CC-NUMA)
    - 독립적인 노드들이 상호연결망에 의해 접속
    - 분산 공유 기억장치시스템 ---> 모든 기억장치들이 전역 주소공간 소유
    - 장점
        - S/W 변경없이 SMP보다 큰 시스템 구축 가능
4. 분산시스템
    - 독립적인 노드들이 전통적인 네트워크(우리가 알고있는)에 의해 접속
    - 시스템이 각각 자기 자신의 고유한 LOCAL 메모리 소유
    - 노드들간의 정보교환, 병렬처리 수행 때만 네트워크를 이용하여 통신
    - 노드
        - PC
        - WORKSTATION
        - SMP
        - MPP
5. 클러스터 컴퓨터
    - 동일계열의 컴퓨터를 모아서 사용
    - 네트워크 ---> 고속 LAN, 네트워크 스위치
    - 모든 시스템 자원들을 단일시스템 이미지로 통합 <-- 중앙에서 똑같은 하나의 일을 처리해야 해서 
    - 장점
        - 저비용으로 구축가능
        - 결함 대체 용이 ---> 가용성 향상
---
## **8.4 다중프로세서시스템 구조**
> MIMD 조직
>> 여러개의 프로세서들이 비동기적으로 프로그램 실행
1. 공유 기억장치 구조
    > 밀결합 구조
    >> 모든 프로세서들이 주기억장치 공유
    - 장점
        - 모든 사용 데이터가 하나 장소에 저장 --> 데이터 교환 필요X
        - 처리할 작업들을 동적으로 균등하게 할당 ---> 프로세서 이용률 극대화 --> 효율 상승
    - 단점
        - 통신량 증가 ---> 경합 발생 --> 지연시간 길어짐
        - 경합 발생시 1개 이외 프로세서들이 대기해야함 --> 고속 연결망, 캐시 메모리로 보완
    - 상호연결 구조(NETWORK)
        1. 버스
            - 공유버스
                - H/W 간단
                - 문제점
                    - 버스 경합 ---> 지연시간 증가 ---> 성능 저하
                - 해결책
                    - 버스 전송속도 향상, 캐시 사용
            - 다중버스
                - 버스 수 증가
                - 계층버스 구조
                - 로컬, 공유 메모리 보유
        2. 크로스바 스위치
            - 방향성을 결정해줌
            - 자원 충돌 완화
            - 프로세서들과 기억장치들 사이의 완전 연결성
            - 단점
                - 고비용
                - H/W 복잡
        3. 다단계 상호연결망(MIN)
            - 크로스바 스위치 사용 + H/W 복잡성 감소
            - EX) 오메가 네트워크
                - 필요한 단계 = STAGE
                - 스위칭 소자 접속방식
                    - 직진
                    - 교차
                    - 하위 방송(lower broadcasting)
                    - 상위 방송(upper broadcasting)
2. 분산 기억장치
    - 소결합 구조
    - 각 프로세서가 자신의 지역 기억장치 소유
    - 메시지 전송으로 통신
    - 네트워크 지름(링크 수)
        - 네트워크 내에서 가장 멀리 떨어져 있는 노드들간 거리
    - 장점
        - 공유자원 경합 감소
    - 단점
        - 통신 프로토콜에 의한 지연시간 증가
    - 종류
        1. 선형 배열 구조
            - 네트워크 지름 = N-1
            - 버스 구조보다 동시성이 높음
            - 노드수와 통신시간 비례
        2. 링 구조
            - 네트워크 지름
                - 양방향성 = N/2
                - 단반향성 = N-1
            - 코달원형
                - 변형구조
                - 링크수, 네트워크 지름 반비례
        3. 트리구조
            - n(층) = K
            - n(노드) = N = -1+2<sup>k<sup>
            - 네트워크 지름 = 2(K-1)
            - 시스템 요소 수 증가 --> 성능이 선형적으로 향상
            - 팻트리 구조
                - 상위층으로 올라갈수록 통신 채널 수 증가 ---> 병목현상 해결
        4. 매시 구조
            - 망구조
            - 데이터를 어디서든 전달 가능
            - 단점 보완
                - 토러스 네트워크
                    - 원형구조 + 매시 구조 
                    - 확장 용이
                    - 네트워크 지름 = max(n)
        5. 하이퍼큐브 네트워크
            - 큐브형태
            - 경로설정 방법
                - E큐브 라우팅
                    - 두 노드 번호를 나타내는 2진수비트들 간의 XOR 연산 수행
                    - C0 : X축/ C1 : 축 / C2 : Z축

