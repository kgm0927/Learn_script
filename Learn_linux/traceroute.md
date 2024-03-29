

# -네트워크 패킷 경로 추적하기

---
traceroute 프로그램(일부 시스템에서는 이와 비슷한 tracepath라는 프로그램을 대신 사용하기도 한다)은 로컬 시스템으로부터 지정된 호스트까지의 모든 네트워크 이동 "구간(hop)들"을 보여준다. 예를 들면, http://www.slashdot.org/ 사이트까지 도달 경로를 볼려면 다음과 같이 할 수 있다.

``` shell
┌──(kali㉿kali)-[~]
└─$ traceroute naver.com
traceroute to naver.com (223.130.192.248), 30 hops max, 60 byte packets
 1  10.0.2.2 (10.0.2.2)  0.931 ms  2.355 ms  2.276 ms
 2  * * *
 3  * * *
 4  * * *
 5  * * *
 6  * * *
 7  * * *
 8  * * *
 9  * * *
10  * * *
11  * * *
12  * * *
13  * * *
14  * * *
15  * * *
16  * * *
17  * * *
18  * * *
19  * * *
20  * * *
21  * * *
22  * * *
23  * * *
24  * * *
25  * * *
26  * * *
27  * * *
28  * * *
29  * * *
30  * * *
                  
```

출력된 경로를 보면, 현재 테스트 중인 시스템에서 http://www.slashdot.org/ 사이트까지 30개의 경로를 거쳤으며, 1번 라우터를 보면, 호스트명, IP 주소, 그리고 로컬 시스템에서 라우터까지의 왕복 시간에 대한 3번의 테스트 결과 데이터를 볼 수 있다.
하지만 라우터를 식별할 수 없을 시(이는 라우터 환경설정, 네트워크 정체 상황, 방화벽 등등의 이유가 있다) 두번째 구간에서부터와 같이 별표로 표시된다.