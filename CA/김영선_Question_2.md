1. 세트연관사상에서 연관도가 낮으면 적중률이 낮은 이유는?
    연관도 = 1SET 내에 몇개의 LINE?
    연관도가 가장 낮은 것 ---> 1SET당 1LINE ---> 직접 사상
    1개 캐시라인, 1개의 태그밖에 못찾아서 히트률 낮다.

2. 부동 소수점 표현에서 범위가 왜 저렇게 되는지 모르겠음...
    ```
    N < (-2^127) : 음수 오버플로우 --> ERR : INF
    N > 2^127 : 양수 오버플로우 ---> ERR : INF
    N > (-2^-128) : 음수 언더플로우 --> -0
    N < (2^-128) : 양수 언더플로우 ---> +0
    ```