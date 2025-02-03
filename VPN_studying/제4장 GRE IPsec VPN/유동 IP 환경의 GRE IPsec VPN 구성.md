

이번 절에서는 지사의 라우터와 외부 인터페이스를 연결할 때 xDSL이나 케이블 네트워크와 같이 유동 IP를 사용하는 환경에서, 동적 크립토 맵을 사용하여 GRE IPsec VPN을 구성하는 방법에 대하여 살펴보자.


---
### 테스트 네트워크 구성

동적 크립토 맵을 사용한 GRE IPsec VPN을 설정하기 위하여 다음과 같은 테스트 네트워크를 구성한다. 앞 절의 네트워크 구성과 다른 점은 지사 라우터인 R4, R5의 외부 인터페이스 IP 주소가 DHCP 서버인 R3에서 받아오는 유동 IP라는 것이다.


[그림 4-8] 테스트 네트워크







이를 위하여 다음과 같이 각 라우터에 IP 주소를 부여한다.


[그림 4-9] IP 주소






각 라우터에서 F0/0 인터페이스를 서브 인터페이스로 동작시킨다. 서브 인터페이스 번호, 서브넷 번호 및 VLAN 번호를 동일하게 사용한다. 이를 위하여 다음과 같이 SW1에서 VLAN을 만들고, 각 라우터와 연결되는 포트를 트렁크로 동작시킨다.


[예제 4-29] SW1 설정
```cisco
SW1(config)# vlan 10
SW1(config-vlan)# vlan 12
SW1(config-vlan)# vlan 23
SW1(config-vlan)# vlan 34
SW1(config-vlan)# vlan 35
SW1(config-vlan)# vlan 40
SW1(config-vlan)# vlan 50
SW1(config-vlan)# exit

SW1(config)# interface range f1/1 - 5
SW1(config-if-range)# switchport trunk encapsulation dot1q
SW1(config-if-range)# switchport mode trunk


```

이번에는 각 라우터의 인터페이스를 활성화시키고, 서브 인터페이스를 만든 다음 IP 주소를 부여한다. R3과 인접 라우터 사이는 인터넷이라고 가정하고, IP 주소 1.0.0.0/8을 서브넷팅하여 사용한다. 나머지 부분은 내부망이라고 가정하고 IP 주소 10.0.0.0/8을 서브넷팅하여 사용한다. R1의 설정은 다음과 같다.


[예제 4-30] R1 설정

``` cisco
R1(config)# interface f0/0
R1(config-if)# no shut
R1(config-if)# exit

R1(config)# interface f0/0.10
R1(config-subif)# encapsulation dot1Q 10
R1(config-subif)# ip address 10.1.10.1 255.255.255.0
R1(config-subif)# exit

R1(config)# interface f0/0.12
R1(config-subif)# encapsulation dot1Q 12
R1(config-subif)# ip address 10.1.12.1 255.255.255.0


```

R2의 설정은 다음과 같다.


[예제 4-31] R2 설정
``` cisco
R2(config)# interface f0/0
R2(config-if)# no shut
R2(config-if)# exit

R2(config)# interface f0/0.12
R2(config-subif)# encapsulation dot1Q 12
R2(config-subif)# ip address 10.1.12.2 255.255.255.0
R2(config-subif)# exit


R2(config)# interface f0/0.23
R2(config-subif)# encapsulation dot1Q 23
R2(config-subif)# ip address 10.1.23.2 255.255.255.0
```


R3의 설정은 다음과 같다.


[예제 4-32] R3 설정
```cisco
R3(config)# interface f0/0
R3(config-if)# no shut
R3(config-if)# exit

R3(config)# interface f0/0.23
R3(config-subif)# encapsulation dot1Q 23
R3(config-subif)# ip address 1.1.23.3 255.255.255.0
R3(config-subif)# exit

R3(config)# interface f0/0.34
R3(config-subif)# encapsulation dot1Q 34
R3(config-subif)# ip address 1.1.34.3 255.255.255.0
R3(config-subif)# exit

R3(config)# interface f0/0.35
R3(config-subif)# encapsulation dot1Q 35
R3(config-subif)# ip address 1.1.35.3 255.255.255.0
```

R4의 설정은 다음과 같다. 인터넷과 연결되는 `F0/0.34` 인터페이스에는 DHCP를 통하여 IP 주소를 할당받도록 설정한다.



[예제 4-33] R4 설정
``` cisco
R4(config)# interface f0/0
R4(config-if)# no shut
R4(config-if)# exit

R4(config)# interface f0/0.34
R3(config-subif)# encapsulation dot1Q 34
R3(config-subif)# ip address dhcp
R3(config-subif)# exit


R4(config)# interface f0/0.40
R3(config-subif)# encapsulation dot1Q 40
R3(config-subif)# ip address 10.1.40.4 255.255.255.0
```

R5의 설정은 다음과 같다. R4와 마찬가지로 인터넷과 연결되는 F0/0.35 인터페이스에는 DHCP를 통하여 IP주소를 할당받도록 설정한다.


[예제 4-34] R5 설정
``` cisco
R5(config)# interface f0/0
R5(config-if)# no shut
R5(config-if)# exit

R4(config)# interface f0/0.35
R3(config-subif)# encapsulation dot1Q 35
R3(config-subif)# ip address dhcp
R3(config-subif)# exit

R4(config)# interface f0/0.50
R3(config-subif)# encapsulation dot1Q 50
R3(config-subif)# ip address 10.1.50.5 255.255.255.0

```

인터넷 라우터인 R3에서 R4, R5에게 IP 주소를 부여하기 위하여 다음과 같이 DHCP 서버를 설정한다.


[예제 4-35] DHCP 서버 설정
``` cisco
R3(config)# ip dhcp pool POOL-34
R3(dhcp-config)# default-router 1.1.34.3
R3(dhcp-config)# network 1.1.34.0 255.255.255.0
R3(dhcp-config)# exit

R3(config)# ip dhcp pool POOL-35
R3(dhcp-config)# default-router 1.1.35.3
R3(dhcp-config)# network 1.1.35.0 255.255.255.0
R3(dhcp-config)# exit

```

잠시 후 R4, R5에서 다음과 같이 IP 주소를 할당받는지 확인한다.

[예제 4-36] IP 주소 할당 확인


```cisco
R4# show ip interface brief
Interface          IP-Address        OK?      Method       Status    Protocol
FastEthernet0/0    unassigned        YES      unset        up        up
FastEthernet0/0.34 1.1.34.1          YES      DHCP         up        up
FastEthernet0/0.40 10.1.40.4         YES      manual       up        up
```

또, DHCP를 통하여 디폴트 루트도 받아왔는지 확인한다.


[예제 4-37] 디폴트 루트 할당 확인

``` cisco
R5# show ip route
(생략)

Gateway of last resort is 1.1.35.3 to network 0.0.0.0

1.0.0.0/24 is subnetted, 1 subnets
C    1.1.35.0 is directly connected, FastEthernet0/0.35
10.0.0.0/24 is subnetted, 1 subnets
C    10.1.50.0 is directly connected, FastEthernet0/0.50
S*  0.0.0.0/0 [254/0] via 1.1.35.3

```

이번에는 다음 그림과 같이 인터넷 또는 외부망으로 사용할 R2, R3, R4, R5간의 라우팅을 설정한다. R4와 R5에서는 이미 DHCP를 통하여 R3 방향으로 디폴트 루트가 설정되어 있다.


[그림 4-10] 외부망 라우팅





따라서, 다음과 같이 R2에서만 R3 방향으로 디폴트 루트를 설정하면 된다.


[예제 4-38] 디폴트 루트 설정

``` cisco
R2(config)# ip route 0.0.0.0 0.0.0.0 1.1.23.3
```

설정이 끝나면 다음과 같이 R4, R5에서 R2의 외부 인터페이스와 핑이 되는지 확인한다.


[예제 4-39] 핑 테스트

``` cisco
R4# ping 1.1.23.2
R5# ping 1.1.23.2
```

이제, 유동 IP 주소를 환경에서 동적 크립토 맵(dynamic crypto map)을 사용하여 GRE IPsec VPN을 설정하고 동작을 확인할 준비가 완료되었다.


---
### GRE 터널 구성

다음 그림과 같이 본사 라우터인 R2와 지사 라우터인 R4, R5간에 포인트 투 포인트 GRE 터널을 구성한다. 터널의 출발지와 목적지로 사용할 IP 주소를 위하여 별도의 루프백 인터페이스를 만들고 IP 주소를 부여한다.

만약, R2, R4, R5 등의 내부망의 인터페이스에 부여된 IP 주소를 터널의 출발지/목적지 주소로 사용하는 경우를 가정해 보자. 이 경우, 해당 네트워크를 내부망의 라우팅에 포함시켜야 해당 인터페이스에 연결된 사이에 통신이 된다.


[그림 4-11] GRE 터널






그러나, 터널 목적지 주소를 터널을 통하여 광고받게 되는 상황이 발생하여, 이 경우 터미널이 다운된다. 따라서, 라우팅이 불필요한 별도의 루프백 인터페이스에 할당된 IP 주소를 터널의 출발지/목적지 주소로 사용해야 한다.

일반적인 경우와 같이 공인 IP 주소를 터널의 출발지/목적지 주소로 사용하면 이와 같은 일이 발생하지 않는다. 그러나, 터널을 구성해야 하는 한 쪽이 유동 IP ㅈ주소를 사용하므로 공인 IP 주소를 이용해서는 터널을 구성할 수 있다.

실제, GRE 터널을 이용한 라우팅 정보의 교환은 IPsec 터널 구성이 완료된 다음에 일어나므로 GRE 터널의 출발지/목적지 주소를 사설 IP로 설정해도 문제 없다. 즉, GRE 터널의 주소는 IPsec을 이하여 새로이 추가되는 헤더 다음에 오기 때문에 도중의 라우터들은 이를 참조할 수도 없고, 참조하지도 않는다.


R2에서 터널을 구성하는 방법은 다음과 같다.


[예제 4-40] R2의 GRE 터널 구성

```cisco

R2(config)# interface loopback 0
R2(config-if)# ip address 10.1.2.2 255.255.255.255
R2(config-if)# exit

R2(config)# interface tunnel 24
R2(config-if)# ip address 10.1.24.2 255.255.255.0
R2(config-if)# tunnel source loopback 0
R2(config-if)# tunnel destination 10.1.4.4
R2(config-if)# keepalive 10 3

R2(config)# interface tunnel 25
R2(config-if)# ip address 10.1.25.2 255.255.255.0
R2(config-if)# tunnel source loopback 0
R2(config-if)# tunnel destination 10.1.5.5
R2(config-if)# keepalive 10 3
```

지사 라우터인 R4에서도 다음과 같이 GRE 터널을 구성한다.


[예제 4-41] R4의 GRE 터널 구성

```cisco

R4(config)# interface loopback 0
R4(config-if)# ip address 10.1.4.4 255.255.255.255
R4(config-if)# exit

R4(config)# interface tunnel 24
R4(config-if)# ip address 10.1.24.4 255.255.255.0
R4(config-if)# tunnel source loopback lo0
R4(config-if)# tunnel destination 10.1.2.2
R4(config-if)# keepalive 10 3

```

지사 라우터인 R5에서도 다음과 같이 GRE 터널을 구성한다.


[예제 4-42] R5의 GRE 터널 구성
``` cisco

R5(config)# interface loopback 0
R5(config-if)# ip address 10.1.5.5 255.255.255.255
R5(config-if)# exit

R5(config)# interface tunnel 25
R5(config-if)# ip address 10.1.25.5 255.255.255.0
R5(config-if)# tunnel source lo0
R5(config-if)# tunnel destination 10.1.2.2
R5(config-if)# keepalive 10 3

```

이번에는 GRE를 통한 동적인 라우팅을 설정한다. 라우팅 프로토콜은 EIGRP을 사용하기로 한다. 이를 위한 각 라우터의 설정은 다음과 같다.


[예제 4-43] 라우팅 프로토콜 설정


```cisco
R1(config)# router eigrp 1
R1(config-router)# network 10.1.10.1 0.0.0.0
R1(config-router)# network 10.1.12.1 0.0.0.0


R1(config)# router eigrp 1
R1(config-router)# network 10.1.10.1 0.0.0.0
R1(config-router)# network 10.1.24.2 0.0.0.0
R1(config-router)# network 10.1.25.2 0.0.0.0

R1(config)# router eigrp 1
R1(config-router)# network 10.1.40.4 0.0.0.0
R1(config-router)# network 10.1.24.4 0.0.0.0


R1(config)# router eigrp 1
R1(config-router)# network 10.1.50.5 0.0.0.0
R1(config-router)# network 10.1.25.5 0.0.0.0
```

라우팅 설정시 터널의 출발지 IP 주소는 반드시 제외시켜야 한다. 예를 들어, R2에서 `10.1.2.2` 네트워크를 EIGRP에 포함시키면 R4, R5에서 터널의 목적지 IP 주소인 `10.1.2.2`에 대한 라우팅 광고를 터널을 통하여 수신한다. 즉, 터널의 목적지로 가는 경로가 터널을 통하는 경우를 '재귀적 라우팅(recursive routing)' 이라고 하며, 이 경우 구성된 터널이 다운된다. 이상으로 본사와 지사 사이에 GRE 터널을 구성하고, 라우팅 프로토콜을 설정하였다.


---
### GRE IPsec VPN 구성

이제, 다음 그림과 같이 본사와 지사간에 GRE IPsec VPN을 구성해 보자.

[그림 4-12] GRE IPsec VPN







본사 라우터인 R2에서 GRE IPsec VPN을 구성하는 방법은 다음과 같다.


[예제 4-44] R2의 GRE IPsec VPN 구성
``` cisco

R2(config)# crypto isakmp policy 10                          (#1)
R2(config-isakmp)# encryption aes
R2(config-isakmp)# authentication pre-share
R2(config-isakmp)# exit

R2(config)# crypto isakmp key cisco address 0.0.0.0 0.0.0.0  (#2)

R2(config)# crypto isakmp keepalive 10                       (#3)

R2(config)# ip access-list extended VPN-TRAFFIC              (#4)
R2(config-ext-nacl)# permit gre host 10.1.2.2 host 10.1.4.4
R2(config-ext-nacl)# permit gre host 10.1.2.2 host 10.1.5.5
R2(config-ext-nacl)# exit

R2(config)# crypto ipsec transform-set PHASE2 esp-aes esp-md5-hmac (#5)
R2(cfg-crypto-trans)# exit

R2(config)# crypto dynamic-map DMAP 10                             (#6)
R2(config-crypto-map)# match address VPN-TRAFFIC
R2(config-crypto-map)# set transform-set PHASE2
R2(config-crypto-map)# exit

R2(config)# crypto map VPN 10 ipsec-isakmp dynamic DMAP            (#7)

R2(config)# interface f0/0.23                                      (#8)
R2(config-subif)# crypto map VPN


```

1. ISAKMP SA를 설정한다.
2. PSK 설정시 ISAKMP 피어가 유동 IP 주소를 사용하므로 직접 피어의 주소를 지정할 수 없다. 따라서, 불특정 IP 주소를 의미하는 0.0.0.0 0.0.0.0을 사용한다.
3. DPD를 설정한다.
4. 보호대상 네트워크를 지정한다.
5. IPsec SA를 지정한다.
6. IPsec 피어가 유동 IP 주소를 사용하므로 정적인 크립토 맵에서 피어 주소를 지정할 수 없다. 따라서, 동적 크립토 맵을 사용하여 현재 시점에서 결정된 IPsec SA만 지정하였다.
7. 크립토 맵에서 앞서 설정한 동적 크립토 맵을 참조한다.
8. 인터페이스에 크립토 맵을 활성화시킨다.

유동 IP를 사용하는 지사 라우터인 R4의 설정은 다음과 같다. 지사에서는 정적 크립토 맵을 사용하며, 앞 절에서 사용한 정적 크립토 맵들과 내용이 동일한다.


[예제 4-45] R4의 IPsec VPN 구성
``` cisco
R4(config)# crypto isakmp policy 10
R4(config-isakmp)# encryption aes
R4(config-isakmp)# authentication pre-share
R4(config-isakmp)# exit

R4(config)# crypto isakmp key 6 cisco address 1.1.23.2

R4(config)# crypto isakmp keepalive 10

R4(config)# ip access-list extended VPN-TRAFFIC
R4(config-ext-nacl)# permit gre host 10.1.4.4 host 10.1.2.2
R4(config-ext-nacl)# exit

R4(config)# crypto ipsec transform-set PHASE2 esp-aes esp-md5-hmac

R4(config)# crypto map VPN 10 ipsec-isakmp
R4(config-crypto-map)# match address VPN-TRAFFIC
R4(config-crypto-map)# set peer 1.1.23.2
R4(config-crypto-map)# set transform-set PHASE2
R4(config-crypto-map)# exit

R4(config)# interface f0/0.34
R4(config-subif)# crypto map VPN



```

지사 라우터인 R5에서 GRE IPsec VPN을 구성하는 방법은 다음과 같다.



[예제 4-46] R5의 GRE IPsec VPN 구성
``` cisco
R5(config)# crypto isakmp policy 10
R5(config-isakmp)# encryption aes
R5(config-isakmp)# authentication pre-share
R5(config-isakmp)# exit

R5(config)# crypto isakmp key 6 cisco address 1.1.23.2

R5(config)# crypto isakmp keepalive 10

R5(config)# ip access-list extended VPN-TRAFFIC
R5(config-ext-nacl)# permit gre host 10.1.5.5 host 10.1.2.2
R5(config-ext-nacl)# exit

R5(config)# crypto ipsec transform-set PHASE2 esp-aes esp-md5-hmac
R5(cfg-crypto-trans)# exit

R5(config)# crypto map VPN 10 ipsec-isakmp
R5(config-crypto-map)# match address VPN-TRAFFIC
R5(config-crypto-map)# set peer 1.1.23.2
R5(config-crypto-map)# set transform-set PHASE2
R5(config-crypto-map)# exit

R5(config)# interface f0/0.35
R5(config-subif)# crypto map VPN
```

IPsec VPN을 설정하면 EIGRP가 헬로를 보내고 자동으로 지사와 본사간의 IPsec 터널 및 GRE 터널이 구성된다. 잠시 후 R5의 라우팅 테이블을 확인해 보면 다음과 같이 모든 내부망 네트워크가 EIGRP를 이용하여 인스톨되어 있다는 것을 알 수 있다.



[예제 4-47] R5의 라우팅 테이블

```cisco

R5# show ip route

Gateway of last resort is 1.1.35.3 to network 0.0.0.0

1.0.0.0/24 is subnetted, 1 subnets
C    1.1.35.0 is directly connected, FastEthernet0/0.35
10.0.0.0/8 is variably subnetted, 7 subnets, 2 masks
D    10.1.10.0/24 [90/297249536] via 10.1.25.2, 00:03:50, Tunnel25
D    10.1.12.0/24 [90/297246976] via 10.1.25.2, 00:03:50, Tunnel25
C    10.1.5.5/32 is directly connected, Loopback0
C    10.1.25.0/24 is directly connected, Tunnel25
D    10.1.24.0/24 [90/310044416] via 10.1.25.2, 00:03:50, Tunnel25
D    10.1.40.0/24 [90/310046976] via 10.1.25.2, 00:03:50, Tunnel25
C    10.1.50.0/24 is directly connected, FastEthernet0/0.50
S* 0.0.0.0/0 [254/0] via 1.1.35.3

```

원격지의 지사인 R4로 핑을 해보면 다음과 같이 성공한다. 현재 본사와 지사 사이에 포인트 투 포인트 GRE 터널을 사용하고 있으므로 지사 사이의 통신은 모두 본사를 통하여 이루어진다.


[예제 4-48] 핑 테스트

```cisco
R5# ping 10.1.40.4

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.40.4, timeout is 2 seconds:
!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max= 280/448/560 ms
```

R2dptj show crypto map 명령어를 사용하여 확인해보면 다음과 같이 현재의 피어 주소 등 필요한 내용이 추가된 임시 크립토 맵이 만들어져 있는 것을 알 수 있다.


[예제 4-49] 임시 크립토 맵
```cisco
R2# show crypto map
Crypto Map "VPN" 10 ipsec-isakmp
    Dynamic map template tag: DMAP

Crypto Map "VPN" 65536 ipsec-isakmp
    Peer=1.1.34.1
    Extended IP access list
        acces-list permit gre host 10.1.2.2 host 10.1.4.4
        dynamic (created from dynamic map DMAP/10)
    Current peer: 1.1.34.1
    Security association lifetime: 4608000 kilobytes/3600 seconds
    PFS (Y/N): N
    Transform sets={
	    PHASE2,
    }

Crypto Map "VPN" 65537 ipsec-isakmp
	Peer= 1.1.35.1
	Extended IP access list
		access-list permit gre host 10.1.2.2 host 10.1.5.5
		dynamic (created from dynamic map DMAP/10)
	Current peer: 1.1.35.1
	Security association lifetime: 4608000 kilobytes/3600 seconds
	PFS (Y/N): N
	Transform sets={
	PHASE2,
	}
	Interfaces using crypto map VPN:
		FastEthernet0/0.23
```


이처럼 동적인 크립토 맵을 사용하면 지사에서 유동 IP를 사용하는 환경을 지원한다. 또, 지사가 추가되면 본사에서는 포인트 투 포인트 GRE 터널을 구성하고, 추가적인 ACL 정도만 조정해주면 되므로 편리하다. 즉, 추가되는 지사에 대한 별도의 크립토 맵을 설정할 일은 없다.

이상으로 GRE IPsec VPN을 설정하고 동작을 확인해 보았다.