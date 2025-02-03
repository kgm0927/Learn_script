

GRE(generic routing encapsulation)는 IP, IPv6, IPX, IPsec 등과 같은 여러 가지 패킷을 IP 패킷에 실어 전송할 때 사용하는 터널링 프로토콜이다. GRE 터널은 포인트 투 포인트와 멀티 포인트 터널이 있다. 이번 장에서는 포인트 GRE 터널을 사용한 IPsec VPN에 대하여 살펴본다.

---
### GRE 동작 방식

먼저, GRE의 동작 방식에 대하여 살펴보자. GRE는 원래의 패킷에 GRE를 위한 헤더를 추가한다. 예를 들어, 다음 그림에서 사설 IP 주소 `10.1.45.5`를 사용하는 R5가 원격지의 R1에게 패킷을 전송하는 경우를 생각해보자.

[그림 4-1]


R5는 출발지 IP 주소가 `10.1.45.5`이고 목적지 IP 주소가 `10.1.12.1`인 패킷을 R4에게 전송한다. GRE 터널 출발지인 R2는 원래의 패킷에 GRE를 위한 20 바이트 길이의 IP 헤더와 GRE 관련 정보를 표시하는 4바이트 길이의 GRE 헤더를 추가한다.

추가된 IP 헤더에서 사용하는 출발지 IP 주소는 터널의 출발지 주소인 `1.1.34.4`를 사용하고, 목적지 IP 주소는 터널의 목적지 IP 주소인 `1.1.32.2`로 설정된다. GRE 패킷을 전송하는 도중의 라우터들은 터널의 목적지 IP 주소인 `1.1.23.2`를 참조하여 해당 패킷을 라우팅시킨다.

따라서, GRE 헤더 내부의 패킷의 예와 같이 인터넷에서 라우팅시킬 수 없는 사설 IP 주소를 사용하든, IPv6 또는 IPX 주소를 사용해도 GRE를 위하여 추가된 라우팅 가능한 터널 목적지 주소만을 참조하므로 인터넷을 통하여 전송이 가능하다.

4바이트 길이의 GRE 헤더에는 GRE 내부의 패킷 정보가 표시된다. 즉, 16비트 길이의 프로토콜 타입 필드에 GRE가 실어나르는 패킷의 종류가 표시된다. 프로토콜 타입 필드에서 사용하는 프로토콜의 종류는 이더넷에서 사용하는 것과 동일하다.



---

### GRE IPsec 동작 방식


GRE IPsec은 다음 그림과 같이 GRE 패킷을 다시 IPsec으로 보호한다.

[그림 4-2] GRE IPsec




일반적으로 지사에서는 GRE 터널과 IPsec 터널이 시작되는 라우터가 동일하지만, 본사에서는 두 라우터를 다르게 운용할 수 있다. 앞장에서 살펴본 순수 IPsec만 사용한 IPsec VPN에 비해서 GRE 터널을 이용하는 GRE IPsec VPN은 다음과 같은 장점이 있다.


- 동적인 라우팅 프로토콜을 사용할 수 있다.
- 멀티캐스트 패킷을 전송할 수 있다.
- 동적인 라우팅 프로토콜을 이용하여 이중화 구성이 용이하다.


---

# GRE IPsec VPN 테스트 네트워크 구축

GRE IPsec VPN의 설정 및 동작 확인을 위하여 다음과 같은 테스트 네트워크를 구축한다. 그림에서 R1, R2는 본사 라우터, R4, R5는 지사 라우터라고 가정한다. 또 R2, R3, R4, R5를 연결하는 구간은 인터넷 또는 IPsec으로 보호하지 않을 외부망이라고 가정한다.


[그림 4-3] 테스트 네트워크




이를 위하여 다음과 같이 각 라우터에 IP 주소를 부여한다.


[그림 4-4] IP 주소


각 라우터에서 F0/0 인터페이스를 서브 인터페이스로 동작시킨다. 서브 인터페이스 번호, 서브넷 번호 및 VLAN 번호를 동일하게 사용한다. 이를 위하여 다음과 같이 SW1에서 VLAN을 만들고, 각 라우터와 연결되는 포트를 트렁크로 동작시킨다.


[예제 4-1] SW1 설정
```cisco
SW1(config)# vlan 10
SW1(config-vlan)# vlan 12
SW1(config-vlan)# vlan 23
SW1(config-vlan)# vlan 35
SW1(config-vlan)# vlan 40
SW1(config-vlan)# vlan 50
SW1(config-vlan)# exit

SW1(config)# interface range f1/1 - 5
SW1(config-if-range)# switchport trunk encapsulation dot1q
SW1(config-if-range)# switchport mode trunk


```

이번에는 각 라우터의 인터페이스를 활성화시키고, 서브 인터페이스를 만든 다음 IP 주소를 부여한다. R3과 인접 라우터 사이는 인터넷이라고 가정하고, IP 주소 `1.0.0.0/8`을 서브넷팅하여 사용한다. 나머지 부분은 내부망이라고 가정하고 IP 주소 10.0.0.0/8을 서브넷팅하여 사용한다. R1의 설정은 다음과 같다.


[예제 4-2] R1의 설정

```cisco
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


[예제 4-3] R2의 설정
``` cisco

R2(config)# interface f0/0
R2(config-if)# no shut
R2(config-if)# exit


R2(config)# interface f0/0.12
R2(config-subif)# encapsulation dot1Q 12
R2(config-subif)# ip address 10.1.12.2 255.255.255.0
R1(config-subif)# exit

R2(config)# interface f0/0.23
R2(config-subif)# encapsulation dot1Q 23
R2(config-subif)# ip address 1.1.23.2 255.255.255.0
```


R3의 설정은 다음과 같다.


[예제 4-4] R3의 설정

``` cisco

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

R4의 설정은 다음과 같다.


[예제 4-5] R4의 설정

```cisco

R4(config)# interface f0/0
R4(config-if)# no shut
R4(config-if)# exit

R4(config)# interface f0/0.34
R4(config-subif)# encapsulation dot1Q 34
R4(config-subif)# ip address 1.1.34.4 255.255.255.0
R4(config-subif)# exit

R4(config)# interface f0/0.40
R4(config-subif)# encapsulation dot1Q 40
R4(config-subif)# ip address 1.1.40.4 255.255.255.0
```


R5의 설정은 다음과 같다.

[예제 4-6] R5의 설정

```cisco

R5(config)# interface f0/0
R5(config-if)# no shut
R5(config-if)# exit

R5(config)# interface f0/0.35
R5(config-subif)# encapsulation dot1Q 35
R5(config-subif)# ip address 1.1.35.5 255.255.255.0
R5(config-subif)# exit

R5(config)# interface f0/0.50
R5(config-subif)# encapsulation dot1Q 50
R5(config-subif)# ip address 1.1.50.5 255.255.255.0
```

라우터의 IP 주소 설정이 끝나면 넥스트 홉 IP 주소까지의 통신을 핑으로 확인한다. 다음에는 외부망 또는 인터넷으로 사용할 R3과 인접 라우터간에 라우팅을 설정한다.이를 위하여 R2, R4, R5에서 R3 방향으로 정적인 디폴트 루트를 설정한다.



[그림 4-5] 외부망 라우팅






[예제 4-7] 디폴트 루트 설정
``` cisco
R2(config)# ip route 0.0.0.0 0.0.0.0 1.1.23.3
R2(config)# ip route 0.0.0.0 0.0.0.0 1.1.34.3
R2(config)# ip route 0.0.0.0 0.0.0.0 1.1.35.5

```

설정이 끝나면 R2의 라우팅 테이블을 확인하여 디폴트 루트가 제대로 인스톨되어 있는지 다음과 같이 확인한다.


[예제 4-8] R2의 라우팅 테이블
```bash
R2# show ip route

Gateway of last resort is 1.1.23.3 to network 0.0.0.0

    1.0.0.0/24 is subnetted, 1 subnets
C      1.1.23.0 is directly connected, FastEthernet0/0.23
    10.0.0.0/24 is subnetted, 1 subnets
C      10.1.12.0 is directly connected, FastEthernet0/0.12
S*  0.0.0.0/0 [1/0] via 1.1.23.3
```


또, 원격지까지의 통신을 핑으로 확인한다.


[예제 4-9] 핑 테스트
```cisco
R2# ping 1.1.34.4
R2# ping 1.1.35.5
```

경계 라우터인 R2에 다음과 같이 ACL을 설정해 보자.



[예제 4-10] ACL 설정

```cisco
R2(config)#
R2(config-ext-nacl)# permit udp host 1.1.34.4 host 1.1.32.2 eq isakmp
R2(config-ext-nacl)# permit udp host 1.1.35.5 host 1.1.23.2 eq isakmp
R2(config-ext-nacl)# permit udp host 1.1.34.4 host 1.1.23.2
R2(config-ext-nacl)# permit esp host 1.1.35.5 host 1.1.23.2
R2(config-ext-nacl)# permit gre host 1.1.34.4 host 1.1.23.2
R2(config-ext-nacl)# permit gre host 1.1.35.5 host 1.1.23.2
R2(config-ext-nacl)# exit

R2(config)# interface f0/0.23
R2(config-subif)# ip access-group ACL-INBOUND in


```

ACL의 내용은 R4, R5가 전송하는 GRE, ISAKMP 패킷 및 ESP 패킷을 허용한다. 이제, GRE IPsec VPN을 설정할 네트워크가 구성되었다.


---
### GRE 터널 구성

다음 그림과 같이 본사 라우터인 R2와 지사 라우터인 R4, R5간에 포인트 투 포인트 GRE 터널을 구성한다.


[그림 4-6] GRE 터널





R2에서 터널을 구성하는 방법은 다음과 같다.


[예제 4-11] R2의 GRE 터널 설정

``` cisco
R2(config)# interface tunnel 24                          (#1)
R2(config-if)# tunnel source 1.1.23.2                    (#2)
R2(config-if)# tunnel destination 1.1.34.4               (#3)
R2(config-if)# ip address 10.1.24.2 255.255.255.0
R2(config-if)# keepalive 10 3                            (#4)
R2(config-if)# exit

R2(config)# interface tunnel 25
R2(config-if)# tunnel source 1.1.23.2
R2(config-if)# tunnel destination 1.1.35.5
R2(config-if)# ip address 10.1.25.2 255.255.255.0
R2(config-if)# keepalive 10 3

```

1. 적당한 터널 번호를 사용하여 R4와의 GRE 터널 설정모드로 들어간다.
2. 인터넷 또는 외부망에서 라우팅 가능한 IP 주소를 사용하여 터널의 출발지 IP 주소를 지정한다.
3. 인터넷 또는 외부망에서 라우팅 가능한 IP 주소를 사용하여 터널의 목적지 IP 주소를 지정한다. `interface tunnel 24` 명령어를 사용하여 터널을 만드는 순간 터널은 UP/DOWN 상태가 된다. `tunnel destination 1.1.34.4` 명령어를 사용하여 지정하는 터널의 목적지 IP 주소가 라우팅 가능하면 이 순간 터널이 UP/UP 상태로 변경된다. 본사에서 지정한 터널의 출발지/목적지 IP 주소를 사용하여 지사에서는 반대로 출발지/목적지 IP 주소로 설정해야 한다.
4. GRE 킵얼라이브(keepalive) 기능을 사용하도록 지정한다. GRE 킵얼라이브 기능을 사용하면 주기적으로 GRE 킵얼라이브 메시지를 전송한다. 설정된 횟수동안 응답하지 않으면 터널이 다운된 것으로 간주한다. GRE 킵얼라이브로 주기를 10초, 재전송 횟수를 3회로 지정하였다. 동적인 라우팅 프로토콜도 유사한 기능을 제공하지만 GRE 킵얼라이브도 동시에 사용하면 SNMP 트랩을 발생시키는 등 관리에 유용하다. 또, 말단 라우터에서 동적인 라우팅 프로토콜을 사용하지 않는 경우에도 터널의 동작여부를 신속히 파악할 수 있어 편리하다.

GRE 킵얼라이브나 동적인 라우팅 프로토콜이 사용되는 경우라도 DPD를 사용하는 것이 좋다. 이 경우, 한 쪽 피어의 SA를 수동으로 제거하거나 재부팅이 일어나는 경우 신속하게 감지할 수 있다.

5. 적당한 터널 번호를 사용하여 R5와의 GRE 터널 설정모드로 들어가서, 필요한 설정을 한다.


지사 라우터인 R4에서도 다음과 같이 GRE 터널을 구성한다.


[예제 4-12] R4의 GRE 터널 설정
``` cisco
R4(config)# interface tunnel 24
R4(config-if)# tunnel source f0/0.34
R4(config-if)# tunnel destination 1.1.23.2
R4(config-if)# ip address 10.1.24.4 255.255.255.0
R4(config-if)# keepalive 10 3

```

지사 라우터인 R5에서도 다음과 같이 GRE 터널을 구성한다.


[예제 4-13] R5의 GRE 터널 설정
``` cisco
R5(config)# interface tunnel 25
R5(config-if)# tunnel source 1.1.35.5
R5(config-if)# tunnel destination 1.1.23.2
R5(config-if)# ip address 10.1.25.5 255.255.255.0
R5(config-if)# keepalive 10 3

```

GRE 터널 설정이 끝나면 다음과 같이 본사 라우터인 R2의 라우팅 테이블에 터널 인터페이스에 설정된 네트워크가 인스톨되는지 확인한다.


[예제 4-14] R2의 라우팅 테이블
``` cisco
R2# show ip route

Gateway of last resort is 1.1.23.3 to network 0.0.0.0

10.0.0.0/24 is subnetted, 1 subnets
C    1.1.23.0 is directly connected, FastEthernet0/0.23
10.0.0.0/24 is subnetted, 3 subnets
C    10.1.12.0 is directly connected, FastEthernet0/0.12
C    10.1.25.0 is directly connected, Tunnel25
C    10.1.24.0 is directly connected, Tunnel24
S*  0.0.0.0 [1/0] via 1.1.23.3
```

터널을 통하여 원격지와 통신이 되는지 다음과 같이 핑으로 확인한다.


[예제 4-15] 핑 테스트
``` cisco
R2# ping 10.1.24.4
R2# ping 10.1.25.5
```

이상으로 본사와 지사 사이에 GRE 터널이 구성되어 있다.

---
### GRE를 통한 라우팅 설정

이번에는 GRE를 통한 동적인 라우팅을 설정한다. 라우팅 프로토콜은 EIGRP를 사용하기로 한다. 이를 위하나 각 라우터의 설정은 다음과 같다.


[예제 4-16] 라우팅 설정

``` cisco
R1(config)# router eigrp 1
R1(config-router)# network 10.1.10.1 0.0.0.0
R1(config-router)# network 10.1.12.1 0.0.0.0

R2(config)# router eigrp 1
R2(config-router)# network 10.1.12.2 0.0.0.0
R2(config-router)# network 10.1.24.2 0.0.0.0

R2(config-router)# network 10.1.25.2 0.0.0.0
R2(config-router)# redistribute static

R2(config)# router eigrp 1
R2(config-router)# network 10.1.24.4 0.0.0.0
R2(config-router)# network 10.1.40.4 0.0.0.0

R2(config)# router eigrp 1
R2(config-router)# network 10.1.25.5 0.0.0.0
R2(config-router)# network 10.1.50.5 0.0.0.0
```

R2에서 설정한 정적인 디폴트 루트를 내부 라우터인 R1로 재분배시켰다. VPN 동작을 위해서는 이 설정이 불필요하지만 R1에서 인터넷을 사용할 수 있게 하기 위해 이와 같이 재분해시켰다. EIGRP 설정이 끝나면 본사의 내부 라우터인 R1에 다음과 같이 지사의 네트워크가 인스톨되는지 확인한다.


[예제 4-17] R1의 라우팅 테이블
``` cisco

R1# show ip route

Gateway of last resort is 10.1.12.2 to network 0.0.0.0

10.0.0.0/24 is subnetted, 6 subnets
C    10.1.10.0 is directly connected, FastEthernet0/0.10
C    10.1.12.0 is directly connected, FastEthernet0/0.12
D    10.1.25.0 [90/297246976] via 10.1.12.2, 00:03:43, FastEthernet0/0.12
D    10.1.25.0 [90/297246976] via 10.1.12.2, 00:03:43, FastEthernet0/0.12
D    10.1.25.0 [90/297246976] via 10.1.12.2, 00:03:25, FastEthernet0/0.12
D    10.1.25.0 [90/297246976] via 10.1.12.2, 00:03:11, FastEthernet0/0.12
D*EX 0.0.0.0/0 [170/30720] via 10.1.12.2, 00:00:10, FastEthernet0/0.12
```

또, 다음과 같이 지사까지의 통신을 핑으로 확인한다.



[예제 4-18] 핑 테스트

```cisco
R1# ping 10.1.40.4
R1# ping 10.1.50.5
```

지사 라우터인 R4에 다음과 같이 본사 및 원격지 지사의 네트워크가 인스톨되는지 확인한다.



---
### GRE IPsec VPN 구성

이제, 다음 그림과 같이 본사와 지사간에 GRE IPsec VPN을 구성해 보자.


[그림 4-7] GRE IPsec VPN




본사 라우터인 R2에서 GRE IPsec VPN을 구성하는 방법은 다음과 같다.

[예제 4-21] R2의 GRE IPsec VPN 구성

``` cisco
R2(config)# crypto isakmp policy 10                       (#1)
R2(config-isakmp)# encryption aes
R2(config-isakmp)# authentication pre-share
R2(config-isakmp)# exit


R2(config)# crypto isakmp key cisco address 1.1.34.4      (#2)
R2(config)# crypto isakmp key cisco address 1.1.35.5

R2(config)# crypto isakmp keepalive 10                    (#3)

R2(config)# ip access-list extended R4-VPN-TRAFFIC        (#4)
R2(config-ext-nacl)# permit gre host 1.1.23.2 host 1.1.34.4
R2(config-ext-nacl)# exit
R2(config)# ip access-list extended R5-VPN-TRAFFIC
R2(config-ext-nacl)# permit gre host 1.1.23.2 host 1.1.35.5
R2(config-ext-nacl)# exit

R2(config)# crypto ipsec transform-set PHASE2 esp-aes esp-md5-hmac (#5)
R2(cfg-crypto-trans)# exit


R2(config)# crypto map VPN 10 ipsec-isakmp (#6)
R2(config-crypto-map)# match address R4-VPN-TRAFFIC
R2(config-crypto-map)# set peer 1.1.34.4
R2(config-crypto-map)# set tranform-set PHASE2
R2(config-crypto-map)# exit
R2(config)# crypto map VPN 20 ipsec-isakmp
R2(config-crypto-map)# set peer 1.1.35.5
R2(config-crypto-map)# set transform-set PHASE2
R2(config-crypto-map)# exit

R2(config)# interface f0/0.23                           (#7)
R2(config-subif)# crypto map VPN

```

1. ISAKMP 정책을 설정한다.
2. 각 피어별 암호를 지정한다.
3. DPD(dead pear detection)을 설정한다.
4. IPsec으로 보호할 트래픽을 지정한다. 본지사 사이의 트래픽을 모두 GRE 터널을 통과하므로 GRE 패킷들을 모두 보호하면 된다. 이처럼, GRE IPsec VPN에서 보호 대사 트래픽을 지정하는 방법은 간단하다.
5. IPsec SA를 지정한다.
6. 정적인 크립토 맵을 만든다.
7. 정적인 크립토 맵을 인터페이스에 적용한다. `12.2(13)T` 이전까지는 크립토 맵을 물리적인 인터페이스와 GRE 터널 인터페이스에 동시에 적용해야 한다. 그러나, `12.2(13)T`부터는 앞의 예와 같이 물리적인 인터페이스만 적용해야 한다.

지사 라우터인 R4에서 GRE IPsec VPN을 구성하는 방법은 다음과 같다. 지사 라우터에서의 설정도 본사와 유사하다. 다만, 피어가 하나뿐인 것만 다르다.



[예제 4-22] R4의 GRE IPsec VPN 구성

```cisco


R4(config)# crypto isakmp policy 10
R4(config-isakmp)# encryption aes
R4(config-isakmp)# authentication pre-share
R4(config-isakmp)# exit

R4(config)# crypto isakmp key cisco address 1.1.23.2

R4(config)# crypto isakmp keepalive 10

R4(config)# ip access-list extended VPN-TRAFFIC
R4(config-ext-nacl)# permit gre host 1.1.34.4 host 1.1.23.2
R4(config-ext-nacl)# exit

R4(config)# crypto ipsec tranform-set PHASE2 esp-aes esp-md5-hmac
R4(config-crypto-trans)# exit

R4(config)# crypto map VPN 10 ipsec-isakmp
R4(config-crypto-map)# match address VPN-TRAFFIC
R4(config-crypto-map)# set peer 1.1.23.2
R4(config-crypto-map)# set transform-set PHASE2
R4(config-crypto-map)# exit


R4(config)# interface f0/0.34
R4(config-subif)# crypto map VPN




```

지사 라우터인 R5에서 GRE IPsec VPN을 구성하는 방법은 다음과 같다.

[예제 4-23] R5의 GRE IPsec VPN 구성

```cisco

R5(config)# crypto isakmp policy 10
R4(config-isakmp)# encryption aes
R4(config-isakmp)# authentication pre-share
R4(config-isakmp)# exit

R5(config)# crypto isakmp key cisco address 1.1.23.2

R5(config)# crypto isakmp keepalive 10

R5(config)# ip access-list extended VPN-TRAFFIC
R5(config-ext-nacl)# permit gre host 1.1.35.5 host 1.1.23.2
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

설정 후 R5의 라우팅 테이블을 확인해 보면 다음과 같이 모든 내부망 네트워크가 EIGRP를 이용하여 인스톨되어 있는 것을 알 수 있다.


[예제 4-24] R5의 라우팅 테이블

```cisco
R5# show ip route eigrp

D    10.1.10.0 [90/297249536] via 10.1.25.2, 00:11:37, Tunnel25
D    10.1.10.0 [90/297249536] via 10.1.25.2, 00:11:37, Tunnel25
D    10.1.10.0 [90/310044416] via 10.1.25.2, 00:11:37, Tunnel25
D    10.1.10.0 [90/310044416] via 10.1.25.2, 00:11:37, Tunnel25

```

원격지의 지사인 R4로 핑을 해 보면 성공한다. 현재 보사와 지사 사이에 포인트 투 포인트 GRE 터널을 사용하고 있으므로 지사 사이의 통신의 모두 본사를 통하여 이루어진다.



[예제 4-25] 핑 테스트
``` cisco
R5# ping 10.1.40.4
```

다음과 같이 ACL 카운트를 초기화시키고 IPsec 세션을 다시 맺도록 해보자.



[예제 4-26] ACL 카운트 및 IPsec 세션 초기화

```cisco
R2# clear ip access-list counters
R2# clear crypto session
```

GRE 터널간에 EIGRP 헬로 패킷이 전송되므로 자동으로 IPsec 터널이 구성된다.



[예제 4-27] IPsec 터널의 자동 구성

``` cisco
R2# show crypto isakmp sa
IPv4 Crypto ISAKMP SA
dst              src              state          conn-id     slot      status
1.1.35.5         1.1.23.2         QM_IDLE           1006        0      ACTIVE
1.1.35.5         1.1.23.2         QM_IDLE           1006        0      ACTIVE

```

R2에서 ACL 통계를 확인해 보면 GRE에 매칭되는 패킷은 보이지 않는다. 즉, GRE 패킷이 ESP에 인캡슐레이션되어서 전송되기 때문이다.


[예제 4-28] ACL 통계 확인
```cisco
R2# show ip access-lists
```

이상으로 포인트 투 포인트 GRE 터널을 통한 IPsec VPN을 구성하고, 동작을 확인해 보았다.