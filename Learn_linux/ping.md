


---
# 네트워크 점검 및 모니터링


### ping 네트워크 호스트로 고유 패킷 전송하기

네트워크와 관련된 명령어 중 가장 기본적인 것이 바로 ping 명령어다. ping 명령어는 IMCP ECHO_REQUEST라고 하는 고유의 네트워크 패킷을 지정된 호스트로 전송한다. 이러한 패킷을 수신하는 대부분의 네트워크 장비들을 이에 응답하여 네트워크 연결을 확인시켜 준다.


**저자주**: 대부분의 네트워크 장비(리눅스 호스트를 포함하여)는 이 패킷들을 무시하도록 설정할 수 있다. 주로 보안적인 이유 때문인데, 잠재적인 공격자에게 호스트를 부분적으로 알아보지 못하게 하기 위해서이다. 또한 IMCP 트래픽을 차단하기 위해 방화벽을 설정하기도 한다.


예를 들어 http://www.linuxcommand.org/에 접속하려고 하면 다음과 같이 ping 명령을 사용할 수 있다.

``` shell
┌──(kali㉿kali)-[~]
└─$ ping linuxcommand.org
PING linuxcommand.org (216.105.38.11) 56(84) bytes of data.
64 bytes from secureprojects.sourceforge.net (216.105.38.11): icmp_seq=1 ttl=46 time=141 ms
64 bytes from secureprojects.sourceforge.net (216.105.38.11): icmp_seq=2 ttl=46 time=139 ms
64 bytes from secureprojects.sourceforge.net (216.105.38.11): icmp_seq=3 ttl=46 time=141 ms
64 bytes from secureprojects.sourceforge.net (216.105.38.11): icmp_seq=4 ttl=46 time=144 ms
64 bytes from secureprojects.sourceforge.net (216.105.38.11): icmp_seq=5 ttl=46 time=138 ms
64 bytes from secureprojects.sourceforge.net (216.105.38.11): icmp_seq=6 ttl=46 time=139 ms
64 bytes from secureprojects.sourceforge.net (216.105.38.11): icmp_seq=7 ttl=46 time=139 ms
64 bytes from secureprojects.sourceforge.net (216.105.38.11): icmp_seq=8 ttl=46 time=143 ms
64 bytes from secureprojects.sourceforge.net (216.105.38.11): icmp_seq=9 ttl=46 time=143 ms
64 bytes from secureprojects.sourceforge.net (216.105.38.11): icmp_seq=10 ttl=46 time=142 ms
64 bytes from secureprojects.sourceforge.net (216.105.38.11): icmp_seq=11 ttl=46 time=140 ms
64 bytes from secureprojects.sourceforge.net (216.105.38.11): icmp_seq=12 ttl=46 time=140 ms
64 bytes from secureprojects.sourceforge.net (216.105.38.11): icmp_seq=13 ttl=46 time=139 ms
64 bytes from secureprojects.sourceforge.net (216.105.38.11): icmp_seq=14 ttl=46 time=139 ms
64 bytes from secureprojects.sourceforge.net (216.105.38.11): icmp_seq=15 ttl=46 time=144 ms
64 bytes from secureprojects.sourceforge.net (216.105.38.11): icmp_seq=16 ttl=46 time=138 ms
^C
--- linuxcommand.org ping statistics ---
16 packets transmitted, 16 received, 0% packet loss, time 15038ms
rtt min/avg/max/mdev = 138.168/140.688/144.352/2.072 ms

```

명령이 실행되면, ping은 패킷을 중단이 없는 한 지정된 간격(기본적으로 1초)에 따라 계속 전송한다.



ctrl-C 키를 눌러서 연결을 중단하면, ping 명령어로 네트워크 성능 현황을 알 수 있다. 적당히 수행된 네트워크는 0%의 패킷 손실을 보인다. 성공적인 ping이란 네트워크 요소들(인터페이스 카드, 케이블, 라우팅 및 게이트웨이 등)이 정상적으로 잘 작동되고 있음을 의미한다.


---
