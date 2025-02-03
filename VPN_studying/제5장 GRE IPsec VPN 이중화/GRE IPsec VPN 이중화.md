

이번 장에서는 GRE IPsec VPN을 이중화하는 방법에 대하여 살펴본다. GRE IPsec VPN은 동적인 라우팅 프로토콜을 사용하기 때문에 라우팅 자체의 이중화 기능을 이용하여 VPN의 이중화를 구현한다. 즉, 평소에는 주 라인을 사용하다가 주 라인에 장애가 발생하면 라우팅 프로토콜이 백업 회선을 이용하며, VPN 터널도 따라서 변경된다.

---
### GRE IPsec VPN 이중화 테스트 네트워크 구축

GRE IPsec VPN 이중화 설정 및 동작 확인을 위하여 다음과 같은 테스트 네트워크를 구축한다. 그림에서 R1, R2, R3은 본사 라우터라고 가정하고, R5, R6은 지사 라우터라고 가정한다. 또, R2, R3, R4, R5, R6을 연결하는 구간은 인터넷 또는 IPsec으로 보호하지 않을 외부망이라고 가정한다.

[그림 5-1] 테스트 네트워크




각 라우터에서 `F0/0` 인터페이스를 서브 인터페이스로 동작시킨다. 서브 인터페이스 번호, 서브넷 번호 및 VLAN 번호를 동일하게 사용한다. 이를 위하여 다음과 같이 SW1에서 VLAN을 만들고, 각 라우터와 언결되는 포트를 트렁크로 동작시킨다.

[예제 5-1] SW1 설정
``` cisco
SW1(config)# vlan 10
SW1(config-vlan)# vlan 12
SW1(config-vlan)# vlan 13
SW1(config-vlan)# vlan 24
SW1(config-vlan)# vlan 34
SW1(config-vlan)# vlan 45
SW1(config-vlan)# vlan 46
SW1(config-vlan)# vlan 50
SW1(config-vlan)# vlan 60
SW1(config-vlan)# exit

SW1(config)# interface range f1/1 -6
SW1(config-if-range)# switchport trunk encapsulation dot1q
SW1(config-if-range)# switchport mode trunk



```

이번에는 각 라우터의 인터페이스를 활성화시키고, 서브 인터페이스를 만든 다음 IP 주소를 부여한다. R4와 인접 라우터 사이는 인터넷이라고 가정하고, IP 주소 1.0.0.0/8을 서브넷팅하여 사용한다. 나머지 부분은 내부망이라고 가정하고 IP 주소 `10.0.0.0/8`을 서브넷팅하여 사용한다. R1의 설정은 다음과 같다.


[예제 5-2] R1 설정
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
R1(config-subif)# exit

R1(config)# interface f0/0.13
R1(config-subif)# encapsulation dot1Q 13
R1(config-subif)# ip address 10.1.13.1 255.255.255.0

```

R2의 설정은 다음과 같다.


[예제 5-3] R3 설정
``` cisco
R2(config)# interface f0/0
R2(config-if)# no shut
R2(config-if)# exit

R2(config)# interface f0/0.12
R2(config-subif)# encapsulation dot1Q 12
R2(config-subif)# ip address 10.1.12.2 255.255.255.0
R2(config-subif)# exit


R2(config)# interface f0/0.24
R2(config-subif)# encapsulation dot1Q 24
R2(config-subif)# ip address 1.1.24.2 255.255.255.0



```

R3의 설정은 다음과 같다.

[예제 5-4] R3 설정
```cisco
R3(config)# interface f0/0
R3(config-if)# no shut
R3(config-if)# exit

R3(config)# interface f0/0.13
R3(config-subif)# encapsulation dot1Q 13
R3(config-subif)# ip address 10.1.13.3 255.255.255.0
R3(config-subif)# exit

R3(config)# interface f0/0.34
R3(config-subif)# encapsulation dot1Q 34
R3(config-subif)# ip address 10.1.34.3 255.255.255.0

```

R4의 설정은 다음과 같다.


[예제 5-5] R4 설정
``` cisco
R4(config)# interface f0/0
R4(config-if)# no shut
R4(config-if)# exit

R4(config)# interface f0/0.24
R4(config-subif)# encapsulation dot1Q 24
R4(config-subif)# ip address 1.1.24.4 255.255.255.0
R4(config-subif)# exit

R4(config)# interface f0/0.34
R4(config-subif)# encapsulation dot1Q 34
R4(config-subif)# ip address 1.1.34.4 255.255.255.0
R4(config-subif)# exit

R4(config)# interface f0/0.45
R4(config-subif)# encapsulation dot1Q 45
R4(config-subif)# ip address 1.1.45.4 255.255.255.0
R4(config-subif)# exit

R4(config)# interface f0/0.46
R4(config-subif)# encapsulation dot1Q 46
R4(config-subif)# ip address 1.1.46.4 255.255.255.0
```

R5의 설정은 다음과 같다.


[예제 5-6] R5 설정
``` cisco
R5(config)# interface f0/0
R5(config-if)# no shut
R5(config-if)# exit

R5(config)# interface f0/0.45
R5(config-subif)# encapsulation dot1Q 45
R5(config-subif)# ip address 1.1.45.5 255.255.255.0
R5(config-subif)# exit

R5(config)# interface f0/0.50
R5(config-subif)# encapsulation dot1Q 50
R5(config-subif)# ip address 10.1.50.5 255.255.255.0
```


R6의 설정은 다음과 같다.


[예제 5-7] R6 설정
``` cisco
R6(config)# interface f0/0
R6(config-if)# no shut
R6(config-if)# exit

R6(config)# interface f0/0.46
R6(config-subif)# encapsulation dot1Q 46
R6(config-subif)# ip address 1.1.46.6 255.255.255.0
R6(config-subif)# exit

R6(config)# interface f0/0.60
R6(config-subif)# encapsulation dot1Q 60
R6(config-subif)# ip address 1.1.60.6 255.255.255.0
```

라우터의 IP 주소 설정이 끝나면 넥스트 홉 IP 주소까지의 통신을 핑으로 확인한다. 다음에는 외부망 또는 인터넷으로 사용할 R4와 인접 라우터간에 라우팅을 설정한다. 이를 위하여 R2, R3, R5, R6에서 R4 방향으로 정적인 디폴트 루트를 설정한다.


[그림 5-2] 외부망 라우팅









각 라우터에서 다음과 같이 디폴트 루트를 설정한다.


[예제 5-8] 디폴트 루트 설정

``` cisco
R2(config)# ip route 0.0.0.0 0.0.0.0 1.1.24.4
R3(config)# ip route 0.0.0.0 0.0.0.0 1.1.34.4
R5(config)# ip route 0.0.0.0 0.0.0.0 1.1.45.4
R6(config)# ip route 0.0.0.0 0.0.0.0 1.1.46.4

```

설정이 끝나면 R2의 라우팅 테이블에 디폴트 루트가 제대로 인스톨되어 있는지 다음과 같이 확인한다.

[예제 5-9] R2의 라우팅 테이블
```
R2# show ip route
(생략)

Gateway of last resort is 1.1.24.4 to network 0.0.0.0

10.0.0.0/24 is subnetted, 1 subnets
C    1.1.24.0 is directly connected, FastEthernet0/0.24
10.0.0.0/24 is subnetted, 1 subnets
C    10.1.12.0 is directly connected, FastEthernet0/0.12
S*   0.0.0.0/0 [1/0] via 1.1.24.4


```

또, 원격지까지의 통신을 핑으로 확인한다.


[예제 5-10] 핑 테스트
``` cisco
R2# ping 1.1.34.3
R2# ping 1.1.45.5
R2# ping 1.1.46.6
```

이제, GRE IPsec VPN을 설정할 네트워크가 구성되었다.


---
### GRE 터널 구성

다음 그림과 같이 본사 라우터인 R2, R3과 지사 라우터인 R5, R6 사이에 포인트 투 포인트 GRE 터널을 구성한다.


[그림 5-3] GRE 터널 구성



R2에서 터널을 구성하는 방법은 다음과 같다.


[예제 5-11] R2의 GRE 터널 구성

```cisco

R2(config)# interface tunnel 25
R2(config-if)# ip address 10.1.25.2 255.255.255.0
R2(config-if)# tunnel source 1.1.24.2
R2(config-if)# tunnel destination 1.1.45.5
R2(config-if)# keepalive 10 3
R2(config-if)# exit
R2(config)# interface tunnel 26
R2(config-if)# ip address 10.1.26.2 255.255.255.0
R2(config-if)# tunnel source 1.1.24.2
R2(config-if)# tunnel destination 1.1.46.6
R2(config-if)# keepalive 10 3
```


R3에서는 다음과 같이 GRE 터널을 구성한다.


[예제 5-12] R3의 GRE 터널 구성
``` cisco
R2(config)# interface tunnel 35
R2(config-if)# ip address 10.1.35.3 255.255.255.0
R2(config-if)# tunnel source 1.1.34.3
R2(config-if)# tunnel destination 1.1.45.5
R2(config-if)# keepalive 10 3
R2(config-if)# exit

R2(config)# interface tunnel 36
R2(config-if)# ip address 10.1.36.3 255.255.255.0
R2(config-if)# tunnel source 1.1.34.3
R2(config-if)# tunnel destination 1.1.46.6
R2(config-if)# keepalive 10 3

```

R5에서는 다음과 같이 GRE 터널을 구성한다.


[예제 5-13] R5의 GRE 터널 구성

``` cisco
R2(config)# interface tunnel 25
R2(config-if)# ip address 10.1.25.5 255.255.255.0
R2(config-if)# tunnel source 1.1.45.5
R2(config-if)# tunnel destination 1.1.24.2
R2(config-if)# keepalive 10 3
R2(config-if)# exit

R2(config)# interface tunnel 35
R2(config-if)# ip address 10.1.35.5 255.255.255.0
R2(config-if)# tunnel source 1.1.45.5
R2(config-if)# tunnel destination 1.1.34.6
R2(config-if)# keepalive 10 3
```

R6에서는 다음과 같이 GRE 터널을 구성한다.



[예제 5-14] R6의 GRE 터널 구성
``` cisco
R6(config)# interface tunnel 26
R6(config-if)# ip address 10.1.26.6 255.255.255.0
R6(config-if)# tunnel source 1.1.46.6
R6(config-if)# tunnel destination 1.1.24.2
R6(config-if)# keepalive 10 3
R6(config-if)# exit

R6(config)# interface tunnel 36
R6(config-if)# ip address 10.1.36.6 255.255.255.0
R6(config-if)# tunnel source 1.1.46.6
R6(config-if)# tunnel destination 1.1.34.3
R6(config-if)# keepalive 10 3

```

GRE 터널 설정이 끝나면 각 라우터의 라우팅 테이블에 터널 인터페이스에 설정된 네트워크가 인스톨되는지 확인한다. 예를 들어, R2의 라우팅 테이블은 다음과 같다.


[예제 5-15] R2의 라우팅 테이블
``` cisco
R2# show ip route
    (생략)

Gateway of last resort is 1.1.24.4 to network 0.0.0.0


1.0.0.0/24 is subnetted, 1 subnets
C    1.1.24.0 is directly connected, FastEthernet0/0.24
10.0.0.0/24 is subnetted, 3 subnets
C    10.1.12.0 is directly connected, FastEthernet0/0.24
C    10.1.26.0 is directly connected, Tunnel26
C    10.1.25.0 is directly connected, Tunnel25
S*  0.0.0.0/0 [1/0] via 1.1.24.4

```

터널을 통하여 원격지와 통신이 되는지 다음과 같이 핑으로 확인한다.

[예제 5-16] 핑 테스트
``` cisco
R2# ping 10.1.25.5
R2# ping 10.1.26.6
```

이상으로 본사와 지사 사이에 포인트 투 포이트 GRE 터널이 구성되었다.


---
### GRE를 통한 라우팅 설정

이번에는 GRE를 통한 동적인 라우팅을 설정한다. 라우팅 프로토콜은 OSPF를 사용하기로 한다. 이를 위한 각 라우터의 설정은 다음과 같다.


[예제 5-17] GRE를 통한 동적인 라우팅 설정
``` cisco
R1(config)# router ospf 1
R1(config)# network 10.1.10.1 0.0.0.0 area 0
R1(config)# network 10.1.12.1 0.0.0.0 area 0
R1(config)# network 10.1.13.1 0.0.0.0 area 0

R2(config)# router ospf 1
R2(config)# network 10.1.12.1 0.0.0.0 area 0
R2(config)# network 10.1.25.1 0.0.0.0 area 0
R2(config)# network 10.1.26.1 0.0.0.0 area 0

R3(config)# router ospf 1
R3(config)# network 10.1.13.3 0.0.0.0 area 0
R3(config)# network 10.1.35.3 0.0.0.0 area 0
R3(config)# network 10.1.36.3 0.0.0.0 area 0

R5(config)# router ospf 1
R5(config)# network 10.1.25.5 0.0.0.0 area 0
R5(config)# network 10.1.35.5 0.0.0.0 area 0
R5(config)# network 10.1.50.5 0.0.0.0 area 0

R6(config)# router ospf 1
R6(config)# network 10.1.26.6 0.0.0.0 area 0
R6(config)# network 10.1.36.6 0.0.0.0 area 0
R6(config)# network 10.1.60.6 0.0.0.0 area 0

```

OSPF 설정이 끝나면 본사의 내부 라우터인 R1에 다음과 같이 지사의 네트워크가 인스톨되는지 확인한다.

[예제 5-18] R1의 라우팅 테이블
```cisco
R1# show ip route
    (생략)
10.0.0.0/24 is subnetted, 9 subnets
C    10.1.10.0 is directly connected, FastEthernet0/0.10
C    10.1.13.0 is directly connected, FastEthernet0/0.13
C    10.1.12.0 is directly connected, FastEthernet0/0.12
O    10.1.26.0 [110/11112] via 10.1.12.2, 00:07:11, FastEthernet0/0.12
O    10.1.25.0 [110/11112] via 10.1.12.2, 00:07:11, FastEthernet0/0.12
O    10.1.35.0 [110/11112] via 10.1.13.3, 00:06:13, FastEthernet0/0.13
O    10.1.26.0 [110/11112] via 10.1.13.3, 00:06:03, FastEthernet0/0.13
O    10.1.60.0 [110/11113] via 10.1.13.3, 00:05:43, FastEthernet0/0.13
               [110/11113] via 10.1.12.2, 00:05:43, FastEthernet0/0.12
O    10.1.50.0 [110/11113] via 10.1.13.3, 00:00:18, FastEthernet0/0.13
               [110/11113] via 10.1.12.2, 00:06:51, FastEthernet0/0.12

```

또, 다음과 같이 지사까지의 통신을 핑으로 확인한다.



[예제 5-19] 핑 테스트
``` cisco
R1# ping 10.1.50.5
R1# ping 10.1.60.6
```

지사 라우터인 R5에 다음과 같이 본사 및 원격지 지사의 네트워크가 인스톨되는지 확인한다.


[예제 5-20] R5의 라우팅 테이블

```cisco
R5# show ip route
    (생략)

Gateway of last resort is 1.1.45.4 to network 0.0.0.0

    1.0.0.0/24 is subnetted, 1 subnets
C        1.1.45.0 is directly connected, FastEthernet0/0.45
    10.0.0.0/24 is subnetted, 9 subnets
O        10.1.10.0 [110/11113] via 10.1.35.3, 00:02:12, Tunnel35
                    [110/11113] via 10.1.25.2, 00:08:34, Tunnel25
O        10.1.13.0 [110/11112] via 10.1.35.3, 00:02:12, Tunnel35
O        10.1.12.0 [110/11112] via 10.1.25.2, 00:08:34, Tunnel25
O        10.1.26.0 [110/22222] via 10.1.25.2, 00:08:34, Tunnel25
C        10.1.25.0 is directly connected, Tunnel25
C        10.1.35.0 is directly connected, Tunnel25
O        10.1.36.0 [110/22222] via 10.1.35.3, 00:02:12, Tunnel35
O        10.1.60.0 [110/22223] via 10.1.35.3, 00:02:12, Tunnel35
                   [110/22223] via 10.1.25.2, 00:07:26, Tunnel25
C        10.1.50.0 is directly connected, FastEthernet0/0.50
S*    0.0.0.0/0 [1/0] via 1.1.45.4
```

이상으로 본사와 지사 사이에 GRE 터널을 구성하고, 라우팅 프로토콜을 설정하였다.


---
### GRE IPsec VPN 구성


이제, 다음 그림과 같이 본사와 지사간에 GRE IPsec VPN을 구성해 보자.


[그림 5-4] GRE IPsec VPN


본사 라우터인 R2에서 GRE IPsec VPN을 구성하는 방법은 다음과 같다. 모든 라우터에서 기본적인 IPsec VPN만 설정하면 된다.


[예제 5-21] R2의 GRE IPsec VPN 구성
```cisco
R2(config)# crypto isakmp policy 10
R2(config-isakmp)# encryption 3des
R2(config-isakmp)# authentication pre-share
R2(config-isakmp)# exit

R2(config)# crypto isakmp key cisco address 1.1.45.5
R2(config)# crypto isakmp key cisco address 1.1.46.5

R2(config)# crypto isakmp keepalive 10

R2(config)# ip access-list extended R5
R2(config-ext-nacl)# permit gre host 1.1.24.2 host 1.1.45.5
R2(config-ext-nacl)# exit
R2(config)# ip access-list extended R6
R2(config-ext-nacl)# permit gre host 1.1.24.2 host 1.1.46.6
R2(config-isakmp)# exit

R2(config)# crypto ipsec transform-set PHASE2 esp-aes esp-sha-hmac
R2(cfg-crypto-trans)# exit

R2(config)# crypto map VPN 10 ipsec-isakmp
R2(config-crypto-map)# match address R5
R2(config-crypto-map)# set peer 1.1.45.5
R2(config-crypto-map)# set transform-set PHASE2
R2(config-crypto-map)# exit
R2(config)# crypto map VPN 20 ipsec-isakmp
R2(config-crypto-map)# match address R6
R2(config-crypto-map)# set peer 1.1.46.6
R2(config-crypto-map)# set transform-set PHASE2
R2(config-crypto-map)# exit

R2(config)# interface f0/0.24
R2(config-subif)# crypto map VPN


```


본사 라우터인 R3에서 GRE IPsec VPN을 구성하는 방법은 다음과 같다.


[예제 5-22] R3의 GRE IPsec VPN 구성
``` cisco
R3(config)# crypto isakmp policy 10
R3(config-isakmp)# encryption 3des
R3(config-isakmp)# authentication pre-share
R3(config-isakmp)# exit

R3(config)# crypto isakmp key cisco address 1.1.45.5
R3(config)# crypto isakmp key cisco address 1.1.46.6

R3(config)# crypto isakmp keepalive 10

R3(config)# ip access-list extended R5
R3(config-ext-nacl)# permit gre host 1.1.34.3 host 1.1.45.5
R3(config-ext-nacl)# exit
R3(config)# ip access-list extended R6
R3(config-ext-nacl)# permit gre host 1.1.34.3 host 1.1.46.6
R3(config-ext-nacl)# exit


R3(config)# crypto ipsec transform-set PHASE2 esp-aes esp-sha-hmac
R3(cfg-crypto-trans)# exit

R3(config)# crypto map VPN 10 ipsec-isakmp
R3(config-crypto-map)# match address R5
R3(config-crypto-map)# set peer 1.1.45.5
R3(config-crypto-map)# set transform-set PHASE2
R3(config-crypto-map)# exit
R3(config)# crypto map VPN 20 ipsec-isakmp
R3(config-crypto-map)# match address R6
R3(config-crypto-map)# set peer 1.1.46.6
R3(config-crypto-map)# set transform-set PHASE2
R3(config-crypto-map)# exit

R3(config)# interface f0/0.34
R3(config-subif)# crypto map VPN


```

지사 라우터인 R5에서 GRE IPsec VPN을 구성하는 방법은 다음과 같다.


[예제 5-23] R5의 GRE IPsec VPN 구성
``` cisco
R5(config)# crypto isakmp policy 10
R5(config-isakmp)# encryption 3des
R5(config-isakmp)# authentication pre-share
R5(config-isakmp)# exit

R5(config)# crypto isakmp key cisco address 1.1.24.2
R5(config)# crypto isakmp key cisco address 1.1.34.3

R5(config)# crypto isakmp keepalive 10

R5(config)# ip access-list extended HQ-R2
R5(config-ext-nacl)# permit gre host 1.1.45.5 host 1.1.24.2
R5(config-ext-nacl)# exit
R5(config)# ip access-list extended HQ-R3
R5(config-ext-nacl)# permit gre host 1.1.45.5 host 1.1.34.3
R5(config-ext-nacl)# exit

R5(config)# crypto ipsec transform-set PHASE2 esp-aes esp-sha-hmac
R5(config-crypto-trans)# exit

R5(config)# crypto map VPN 10 ipsec-isakmp
R5(config-crypto-map)# match address HQ-R2
R5(config-crypto-map)# set peer 1.1.24.2
R5(config-crypto-map)# set transform-set PHASE2
R5(config-crypto-map)# exit
R5(config)# crypto map VPN 20 ipsec-isakmp
R5(config-crypto-map)# match address HQ-R3
R5(config-crypto-map)# set peer 1.1.34.3
R5(config-crypto-map)# set transform-set PHASE2
R5(config-crypto-map)# exit


R5(config)# interface f0/0.45
R5(config-subif)# crypto map VPN 


```

지사 라우터인 R6에서 GRE IPsec VPN을 구성하는 방법은 다음과 같다.


[예제 5-24] GRE IPsec VPN 구성
```cisco
R6(config)# crypto isakmp policy 10
R6(config-isakmp)# encryption 3des
R6(config-isakmp)# authentication pre-share
R6(config-isakmp)# exit

R6(config)# crypto isakmp key cisco address 1.1.24.2
R6(config)# crypto isakmp key cisco address 1.1.34.3

R6(config)# crypto isakmp keepalive 10

R6(config)# ip access-list extended HQ-R2
R6(config-ext-nacl)# permit gre host 1.1.46.6 host 1.1.24.2
R6(config-ext-nacl)# exit

R6(config)# ip access-list extended HQ-R3
R6(config-ext-nacl)# permit gre host 1.1.46.6 host 1.1.24.3
R6(config-ext-nacl)# exit

R6(config)# crypto ipsec transform-set PHASE2 esp-aes esp-sha-hmac
R6(cfg-crypto-trans)# exit

R6(config)# crypto map VPN 10 ipsec-isakmp
R6(config-crypto-map)# match address HQ-R2
R6(config-crypto-map)# set peer 1.1.24.2
R6(config-crypto-map)# set transform-set PHASE2
R6(config-crypto-map)# exit
R6(config)# crypto map VPN 20 ipsec-isakmp
R6(config-crypto-map)# match address HQ-R3
R6(config-crypto-map)# set peer 1.1.34.3
R6(config-crypto-map)# set transform-set PHASE2
R6(config-crypto-map)# exit

R6(config)# interface f0/0.46
R6(config-subif)# crypto map VPN

```

이상으로 IPsec VPN 설정이 끝이 났다.


---
### GRE IPsec VPN 이중화 및 부하분산

GRE IPsec VPN은 동적인 라우팅 프로토콜을 사용한다. 따라서, <mark style="background: #ADCCFFA6;">라우팅 프로토콜을 사용하여 VPN을 이중화시킬 수 있다.</mark> 다음 그림에서 R5와 본사간의 통신시 본사 라우터 R2를 사용하여 VPN이 구성되게 하려면 라우팅 프로토콜이 R2-R5 사이의 터널을 우선하도록 하면 된다.


[그림 5-5] GRE IPsec VPN 이중화






이를 위하여 tunnel 25 인터페이스의 대역폭을 R3-R5 사이를 연결하는 tunnel 35보다 좀 더 크게 설정해주면 된다. 기본적으로 터널 인터페이스의 대역폭은 9kbps이다.



[예제 5-25] 터널 인터페이스 정보 확인하기
```cisco
R2# show interface tunnel 25

Tunnel25 is up, line protocol is up
	Hardware is Tunnel
	Internet address is 10.1.25.2/24
	MTU 1514 bytes, BW 9 Kbit/sec, DLY 500000 usec,
		reliability 255/255, txload 1/255, rxload 1/255
	Encapsulation TUNNEL, loopback not set
	Keepalive set (10 sec), retries 3
	Tunnel source 1.1.24.2, destination 1.1.45.5
	Tunnel protocol/transport GRE/IP
	(생략)
```

다음과 같이 tunnel 25의 대역폭을 다른 터널보다 약간 빠른 10kbps 정도로 설정한다.



[예제 5-26] 터널 인터페이스 대역폭 조정
``` cisco
R2(config)# inteface tunnel 25
R2(config-if)# bandwidth 10

R5(config)# inteface tunnel 25
R5(config-if)# bandwidth 10
```

반대로 R6과 본사간의 통신시 다음 그림과 같이 본사 라우터 R3을 이용하여 VPN이 구성하게 하려면 라우팅 프로토콜이 R3-R6 사이의 터널을 우선하도록 설정하면 된다.


[그림 5-6] GRE IPsec VPN 이중화





이를 위하여 다음과 같이 tunnel 36의 대역폭을 다른 터널보다 약간 빠른 10kbps 정도로 설정한다.

[예제 5-27] 터널 인터페이스 대역폭 조정
```cisco
R3(config)# inteface tunnel 36
R3(config-if)# bandwidth 10

R6(config)# inteface tunnel 36
R6(config-if)# bandwidth 10

```

설정 후 R5의 라우팅 테이블을 확인해 보면 다음과 같이 본사를 통하는 모든 경로가 tunnel 25와 연결되는 R2를 통한다.


[예제 5-28] R5의 라우팅 테이블
```cisco
R5# show ip route
    (생략)

Gateway of last resort is 1.1.45.4 to network 0.0.0.0

1.0.0.0/24 is subnetted, 1 subnets
C    1.1.45.0 is directly connected, FastEthernet0/0.45
10.0.0.0/24 is subnetted, 9 subnets
O    10.1.10.0 [110/10002] via 10.1.25.2, 00:03:16, Tunnel25
O    10.1.13.0 [110/10002] via 10.1.25.2, 00:03:16, Tunnel25
O    10.1.12.0 [110/10001] via 10.1.25.2, 00:03:16, Tunnel25
O    10.1.26.0 [110/21111] via 10.1.25.2, 00:03:16, Tunnel25
C    10.1.25.0 is directly connected, Tunnel25
C    10.1.35.0 is directly connected, Tunnel35
O    10.1.36.0 [110/21113] via 10.1.25.2, 00:03:16, Tunnel25
O    10.1.60.0 [110/21112] via 10.1.25.2, 00:03:16, Tunnel25
C    10.1.50.0 is directly connected, FastEthernet0/0.50
S*   0.0.0.0/0 [1/0] via 1.1.45.4
```


R6의 라우팅 테이블을 확인해 보면 다음과 같이 본사를 통하는 모든 경로가 tunnel 36과 연결되는 R3을 통한다.


[예제 5-29] R6의 라우팅 테이블

``` cisco
R6# show ip route
(생략)

Gateway of last resort is 1.1.46.4 to network 0.0.0.0

1.0.0.0/24 is subnetted, 1 subnets
C    1.1.46.0 is directly connected, FasthEthernet0/0.46
10.0.0.0/24 is subnetted, 9 subnets
O    10.1.10.0 [110/10002] via 10.1.36.3, 00:00:16, Tunnel36
O    10.1.13.0 [110/10001] via 10.1.36.3, 00:00:16, Tunnel36
O    10.1.12.0 [110/10002] via 10.1.36.3, 00:00:16, Tunnel36
C    10.1.26.0 is directly connected, Tunnel26
O    10.1.25.0 [110/20002] via 10.1.36.3, 00:00:16, Tunnel36
O    10.1.35.0 [110/21111] via 10.1.36.3, 00:00:16, Tunnel36
C    10.1.36.0 is directly connected, Tunnel36
C    10.1.36.0 is directly connected, FastEthernet0/0.60
O    10.1.50.0 [110/20003] via 10.1.36.3, 00:00:16, Tunnel36
S*   0.0.0.0/0 [1/0] via 1.1.46.4

```

본사 내부 라우터인 R1의 라우팅 테이블을 확인해 보면 다음과 같이 R5의 내부 네트워크인 `10.1.50.0/24`는 R2로 라우팅되고, R6의 내부 네트워크인 `10.1.60.0/24`는 R3으로 라우팅된다.



[예제 5-30] R1의 라우팅 테이블

``` cisco
R1# show ip route
(생략)

Gateway of last resort is 10.1.13.3 network 0.0.0.0

10.0.0.0/24 is subnetted, 9 subnets
C    10.1.10.0 is directly connected, FastEthernet0/0.10
C    10.1.13.0 is directly connected, FastEthernet0/0.13
C    10.1.12.0 is directly connected, FastEthernet0/0.12
O    10.1.26.0 [110/11112] via 10.1.12.2, 01:08:42, FastEthernet0/0.12
O    10.1.25.0 [110/10001] via 10.1.12.2, 00:07:52, FastEthernet0/0.12
O    10.1.35.0 [110/11112] via 10.1.13.3, 01:16:49, FastEthernet0/0.13
O    10.1.36.0 [110/10001] via 10.1.13.3, 01:01:13, FastEthernet0/0.13
O    10.1.35.0 [110/10002] via 10.1.13.3, 01:01:13, FastEthernet0/0.13
O    10.1.35.0 [110/10002] via 10.1.12.2, 01:07:52, FastEthernet0/0.12


```

결과적으로 다음 그림과 같이 본지사 사이의 GRE IPsec VPN 경로가 R2, R3 두 개의 라우터간에 로드 밸런싱되고 있다.


[그림 5-7] GRE IPsec VPN 로드 밸런싱





다음과 같이 R2에 장애를 발생시켜보자.


[예제 5-31] 장애 발생시키기

``` cisco

R2(config)# interface f0/0.24
R2(config-subif)# shut

```

이제, 다음 그림과 같이 본사와 R5간의 트래픽이 R3-R5간에 구성된 tunnel 35를 통하여 전송된다.



[그림 5-8] 장애 발생시의 동작



다음과 같이 R5의 라우팅 테이블을 보면 이를 확인할 수 있다.



[예제 5-32]
``` cisco
R2# show ip route

10.0.0.0/24 is subnetted, 9 subnets
O    10.1.10.0 [110/11113] via 10.1.35.3 00:00:08, Tunnel35
O    10.1.13.0 [110/11112] via 10.1.35.3 00:00:08, Tunnel35
O    10.1.12.0 [110/11113] via 10.1.35.3 00:00:08, Tunnel35
O    10.1.26.0 [110/22224] via 10.1.35.3 00:00:08, Tunnel35
O    10.1.25.0 [110/21113] via 10.1.35.3 00:00:08, Tunnel35
O    10.1.36.0 [110/21111] via 10.1.35.3 00:00:08, Tunnel35
O    10.1.60.0 [110/21112] via 10.1.35.3 00:00:08, Tunnel35

```


본사 내부의 라우터인 R1의 라우팅 테이블에도 다음과 같이 R5의 내부망인 `10.1.50.0/24` 네트워크로 가는 경로가 R3의 주소인 10.1.13.3으로 변경된다.



[예제 5-33] R1의 라우팅

``` cisco
R1# show ip route
    (생략)

Gateway of last resort is 10.1.13.3 to network 0.0.0.0


10.0.0.0/24 is subnetted, 7 subnets
C    10.1.10.0 is directly connected, FastEthernet0/0.10
C    10.1.13.0 is directly connected, FastEthernet0/0.13
C    10.1.12.0 is directly connected, FastEthernet0/0.12
O    10.1.35.0 [110/11112] via 10.1.13.3, 01:13:35, FastEthernet0/0.13
O    10.1.36.0 [110/10001] via 10.1.13.3, 00:16:00, FastEthernet0/0.13
O    10.1.60.0 [110/10002] via 10.1.13.3, 00:16:00, FastEthernet0/0.13
O    10.1.50.0 [110/11113] via 10.1.13.3, 00:01:12, FastEthernet0/0.13
O*E2 0.0.0.0/0 [110/1] via 10.1.13.3, 02:04:04 FastEthernet0/0.13


```

이상으로 GRE IPsec VPN의 이중화 및 부하분산을 설정하고 동작 방식을 확인해 보았다.