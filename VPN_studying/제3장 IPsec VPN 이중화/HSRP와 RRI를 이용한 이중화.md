

이번 장에서 장애에 대비하여 순수 IPsec VPN을 이중화하는 방법에 대하여 살펴본다. 순수 IPsec VPN을 이중화할 수 있는 방법은 다음과 같다.

- HSRP와 RRI(reverse route injection)를 이용한 IPsec VPN 이중화
- 다수개의 크립토 피어를 이용한 이중화
- SSO나 SSP를 이용한 이중화
- 먼저, HSRP와 RRI를 이용하여 IPsec VPN 이중화해 보자.



### 테스트 네트워크 구축

IPsec VPN 이중화 구성을 위하여 다음과 같은 테스트 네트워크를 구축한다. 그럼에서 R1, R2, R3, R4는 본사 라우터라고 가정하고, R6, R7은 지사 라우터라고 가정한다. 또, R4, R5, R6, R7을 연결하는 구간은 인터넷 또는 외부망이라고 가정한다.


[그림 3-1] 테스트 네트워크





테스트 네트워크 구축을 위하여 다음과 같이 각 라우터의 인터페이스에 IP 주소를 부여한다.


[그림 3-2] 인터페이스별 IP 주소



각 라우터에서 F0/0 인터페이스를 서브 인터페이스로 동작시킨다. 서브 인터페이스 번호, 서브넷 번호 및 VLAN 번호를 동일하게 사용한다. 이를 위하여 다음과 같이 SW1에서 VLAN을 만들고, 각 라우터와 연결되는 포트를 트렁크로 동작시킨다.

[예제 3-1] SW1 설정

```bash
SW1(config)# vlan 10
SW1(config-vlan)# vlan 12
SW1(config-vlan)# vlan 13
SW1(config-vlan)# vlan 100
SW1(config-vlan)# vlan 45
SW1(config-vlan)# vlan 56
SW1(config-vlan)# vlan 57
SW1(config-vlan)# vlan 60
SW1(config-vlan)# vlan 70
SW1(config-vlan)# exit

SW1(config)# interface range f1/1 - 7
SW1(config-if-range)# switchport trunk encapsulation dot1 q
SW1(config-if-range)# switchport mode trunk




```

이번에는 각 라우터의 인터페이스를 활성화시키고, 서브 인터페이스를 만든 다음 IP 주소를 부여한다. R1의 설정은 다음과 같다.


[예제 3-2] R1의 설정

``` bash
R1(config)# interface f0/0
R1(config-if)# no shut
R1(config-if)# exit

R1(config)# interface f0/0.10
R1(config-subif)# encapsulation dot1q 10
R1(config-subif)# ip address 10.1.10.1 255.255.255.0
R1(config-subif)# exit

R1(config)# interface f0/0.12
R1(config-subif)# encapsulation dot1q 12
R1(config-subif)# ip address 10.1.12.1 255.255.255.0
R1(config-subif)# exit

R1(config)# interface f0/0.13
R1(config-subif)# encapsulation dot1q 13
R1(config-subif)# ip address 10.1.13.1 255.255.255.0
R1(config-subif)# exit
```

R2의 설정은 다음과 같다.


[예제 3-3] R2의 설정

``` bash
R2(config)# interface f0/0
R2(config-if)# no shut
R2(config-if)# exit

R2(config)# interface f0/0.12
R2(config-subif)# encapsulation dot1Q 12
R2(config-subif)# ip address 10.1.12.2 255.255.255.0
R2(config-subif)# exit

R2(config)# interface f0/0.100
R2(config-subif)# encapsulation dot1Q 100
R2(config-subif)# ip address 10.1.100.2 255.255.255.0
R2(config-subif)# exit
```

R3의 설정은 다음과 같다.

[예제 3-4] R3의 설정
```bash
R3(config)# interface f0/0
R3(config-if)# no shut
R3(config-if)# exit

R3(config)# interface f0/0.13
R3(config-subif)# encapsulation dot1Q 13
R3(config-subif)# ip address 10.1.13.3 255.255.255.0
R3(config-subif)# exit

R3(config)# interface f0/0.100
R3(config-subif)# encapsulation dot1Q 100
R3(config-subif)# ip address 10.1.100.3 255.255.255.0
R3(config-subif)# exit

```


R4의 설정은 다음과 같다.

[예제 3-5] R4의 설정
```bash
R3(config)# interface f0/0
R3(config-if)# no shut
R3(config-if)# exit

R3(config)# interface f0/0.100
R3(config-subif)# encapsulation dot1Q 100
R3(config-subif)# ip address 10.1.100.4 255.255.255.0
R3(config-subif)# exit

R3(config)# interface f0/0.45
R3(config-subif)# encapsulation dot1Q 45
R3(config-subif)# ip address 10.1.45.4 255.255.255.0
R3(config-subif)# exit

```


R5의 설정은 다음과 같다.

[예제 3-6] R5의 설정
```bash
R3(config)# interface f0/0
R3(config-if)# no shut
R3(config-if)# exit

R3(config)# interface f0/0.45
R3(config-subif)# encapsulation dot1Q 45
R3(config-subif)# ip address 10.1.45.5 255.255.255.0
R3(config-subif)# exit

R3(config)# interface f0/0.56
R3(config-subif)# encapsulation dot1Q 56
R3(config-subif)# ip address 10.1.56.5 255.255.255.0
R3(config-subif)# exit

R3(config)# interface f0/0.57
R3(config-subif)# encapsulation dot1Q 57
R3(config-subif)# ip address 10.1.57.5 255.255.255.0
R3(config-subif)# exit
```

[예제 3-7] R6의 설정
```bash
R3(config)# interface f0/0
R3(config-if)# no shut
R3(config-if)# exit

R3(config)# interface f0/0.56
R3(config-subif)# encapsulation dot1Q 56
R3(config-subif)# ip address 10.1.56.5 255.255.255.0
R3(config-subif)# exit

R3(config)# interface f0/0.60
R3(config-subif)# encapsulation dot1Q 60
R3(config-subif)# ip address 10.1.60.6 255.255.255.0
R3(config-subif)# exit
```

R7의 설정은 다음과 같다.


[예제 3-8] R7의 설정

```bash
R3(config)# interface f0/0
R3(config-if)# no shut
R3(config-if)# exit

R3(config)# interface f0/0.57
R3(config-subif)# encapsulation dot1Q 57
R3(config-subif)# ip address 10.1.56.5 255.255.255.0
R3(config-subif)# exit

R3(config)# interface f0/0.70
R3(config-subif)# encapsulation dot1Q 70
R3(config-subif)# ip address 10.1.60.6 255.255.255.0
R3(config-subif)# exit
```

라우터의 IP 주소 설정이 끝나면 모든 라우터에서 넥스트 홉 IP 주소까지의 통신 핑으로 확인하다. 넥스트 홉까지의 통신 확인이 끝나면 다음 그림의 진한 부분에 대해 정적 경로를 설정한다.

[그림 3-3] 외부망 라우팅





R2, R3에서 R4 방향으로 정적인 디폴트 루트를 설정하고, R4에서 R5 방향으로, R6,R7에서 R5 방향으로도 정적인 디폴트 루트를 설정한다.  R5에서는 R4 내부에서 공인 IP 주소로 사용할 1.1.100.0/24 네트워크에 대해서 정적 경로를 설정한다 .각 라우터의 설정은 다음과 같다.


[예제 3-9] 정적 경로 설정
```bash
R2(config)# ip route 0.0.0.0 0.0.0.0 1.1.100.4
R3(config)# ip route 0.0.0.0 0.0.0.0 1.1.100.4
R4(config)# ip route 0.0.0.0 0.0.0.0 1.1.45.5
R5(config)# ip route 1.1.100.0 255.255.255.0 1.1.45.4
R6(config)# ip route 0.0.0.0 0.0.0.0 1.1.56.5
R7(config)# ip route 0.0.0.0 0.0.0.0 1.1.57.5

```

정적 경로 설정이 끝나면 각 라우터의 라우팅 테이블을 확인하고, 원격지까지의 통신을 핑으로 확인한다. 예를 들어, R2의 라우팅 테이블은 다음과 같다.



[예제 3-10] R2의 라우팅 테이블

```bash
R2# show ip route

Gateway of last resort is 1.1.100.4 to network 0.0.0.0

1.0.0.0/24 is subnetted, 1 subnets 
 C  1.1.100.0 is directly connected, FastEthernet0/0.100

10.0.0.0/24 is subnetted, 1 subnets
C   10.1.12.0 is directly connected, FastEthernet0/0.12
S* 0.0.0.0/0 [1/0] via 1.1.100.4
```


R2에서 R6, R7의 외부 네트워크까지 핑이 되는지를 다음과 같이 확인한다.


[예제 3-11] 핑 테스트

``` bash
R2# ping 1.1.56.7
R2# ping 1.1.57.7
```

<mark style="background: #ADCCFFA6;">외부망 정적 경로 설정의 끝나면 다음 그림과 같이 R2, R3에서 HSRP를 설정한다.</mark> HSRP는 나중에 본사 IPsec VPN 게이트웨이로 사용할 R2, R3의 장애에 대비한 IPsec VPN 이중화를 위하여 사용한다.


[그림 3-4] HSRP 설정



HSRP 그룹 1을 설정하고 이름을 GROUP1로 지정한다. 가상 IP 주소 (VIP, virtual IP)는 1.1.100.10으로 설정하고, R2를 액티브 라우터로 동작시킨다. R2의 각 인터페이스에 장애가 발생하면 R3이 액티브 라우터가 되도록 한다. 이를 위한 R2의 설정은 다음과 같다.


[예제 3-12] HSRP 설정

```bash
R2(config)# interface f0/0.100
R2(config-subif)# standby 1 ip 1.1.100.10
R2(config-subif)# standby 1 priority 105
R2(config-subif)# standby 1 preempt
R2(config-subif)# standby 1 track f0/0.12
R2(config-subif)# standby 1 name GROUP1
R2(config-subif)# exit

```

[[리눅스 예제 3-12_ VRRP]]

R3의 HSRP 설정은 다음과 같다.


[예제 3-13] R3의 HSRP 설정


```bash
R3(config)# interface f0/0.100
R3(config-subif)# standby 1 ip 1.1.100.10
R3(config-subif)# standby 1 preempt
R3(config-subif)# standby 1 name GROUP1
R3(config-subif)# exit
```


설정 후 다음과 같이 `show standby brief` 명령어를 사용하여 HSRP가 제대로 동작하는지 확인한다.

[예제 3-14] HSRP 동작 확인

``` bash
R3# show standby brief

```


[그림 3-5] 내부망 라우팅






본사의 내부 라우터인 R1, R2, R3에서는 OSPF 에어리어 0을 설정한다. 앞서 R2와 R3에서 R4 방향으로 설정한 디폴트 루트를 OSPF를 통하여 R1에게 광고한다. R1의 라우팅 설정은 다음과 같다.


[예제 3-15] R1의 OSPF 설정

``` bash

R2(config)# router ospf 1
R2(config-router)# network 10.1.10.1 0.0.0.0 area 0
R2(config-router)# network 10.1.12.1 0.0.0.0 area 0
R2(config-router)# network 10.1.13.1 0.0.0.0 area 0

```

[[리눅스 예제 3-15]]



R2의 라우팅 설정은 다음과 같다. 내부 라우터인 R1으로 디폴트 루트를 광고할 때 `metric-type 1` 옵션을 사용하여 평소에는 R2를 선호하도록 한다.


[예제 3-16] R2의 OSPF 설정

``` bash
R2(config)# router ospf 1
R2(config-router)# network 10.1.12.2 0.0.0.0 area 0
R2(config-router)# default-information originate metric-type 1

```

[[리눅스 예제 3-16]]

R3의 라우팅 설정은 다음과 같다.



[예제 3-17] R3의 OSPF 설정

``` bash
R3(config)# router ospf 1
R3(config-router)# network 10.1.13.3 0.0.0.0 area 0
R3(config-router)# default-information originate
R3(config-router)# exit
```

설정 후 각 라우터에서 라우팅 테이블을 확인한다. 예를 들어, R1의 라우팅 테이블은 다음과 같다.


[예제 3-18] R1의 라우팅 테이블

``` bash

R1# show ip route
(생략)

Gateway of last resort is 10.1.12.2 to network 0.0.0.0

	10.0.0.0/24 is subnetted, 3 subnets
C     10.1.10.0 is directly connected, FastEthernet0/0.10
C     10.1.10.0 is directly connected, FastEthernet0/0.13
C     10.1.10.0 is directly connected, FastEthernet0/0.12

O*E1 0.0.0.0/0 [110/2] via 10.1.12.2, 00:01:39 FastEthernet0/0.12
```

이상으로 IPsec VPN을 설정할 네트워크가 완성되었다.

---
### HSRP, RRI 및 DPD를 이용한 VPN 대중화

다음 그림과 같이 IPsec L2L VPN을 구성해보자.


[그림 3-6]IPsec L2L VPN






본지사 사이의 VPN이 평소에는 본사의 R2를 통하여 구성된다. 그러다가, R2에 장애가 발생하면 R3를 통하여 VPN이 구성하도록 해보자. R2의 설정은 다음과 같다.


[예제 3-19] R2의 설정

``` bash
R2(config)# crypto isakmp policy 10 (#1)
R2(config-isakmp)# encryption aes
R2(config-isakmp)# authenication pre-share
R2(config-isakmp)# group 2
R2(config-isakmp)# exit

R2(config)# crypto isakmp key cisco address 1.1.56.6 (#2)
R2(config)# crypto isakmp key cisco address 1.1.56.6

R2(config)# crypto isakmp keepalive 10 (#3)


R2(config)# ip access-list extended R6 (#4)
R2(config-ext-nacl)# permit ip 10.0.0.0 0.255.255.255 10.1.60.0 0.0.0.255
R2(config)# exit
R2(config-ext-nacl)# permit ip 10.0.0.0 0.255.255.255 10.1.70.0 0.0.0.255
R2(config-ext-nacl)# exit

R2(config)# crypto ipsec transform-set PHASE2 esp-aes esp-sha-hmac (#5)
R2(cfg-crypto-trans)# exit

R2(config)# crypto map VPN 10 ipsec-isakmp
R2(config-crypto-map)# match address R6
R2(config-crypto-map)# set peer 1.1.56.6
R2(config-crypto-map)# set transform-set PHASE2
R2(config-crypto-map)# reverse-route                               (#7)
R2(config-crypto-map)# exit
R2(config)# crypto map VPN 20 ipsec-isakmp
R2(config-crypto-map)# match address R7
R2(config-crypto-map)# set peer 1.1.57.7
R2(config-crypto-map)# set transform-set PHASE2
R2(config-crypto-map)# reverse-route                              
R2(config-crypto-map)# exit

R2(config)# interface f0/0.100
R2(config-subif)# crypto map VPN redundancy  GROUP 1
R2(config-subif)# exit


```

1. IKE 제1단계 정책을 설정한다.
2. IKE 제 1단계 정책 설정시 피어 인증방식을 PSK(pre-shared key)로 지정했기 때문에 각 피어 별로 PSK를 설정한다.
3. DPD(dead peer detection) 기능을 활성화시킨다.

DPD는 ISAKMP 킵얼라이브(keepalive)를 개선하여 만든 기능으로 피어의 정삭적인 동작 여부를 감시한다.

피어로부터 정상적인 IPsec 트래픽을 수신하면 해당 피어가 정상적으로 동작한다고 가정하고 헬로 메시지를 전송하지 않는다. 특정 기간 동안 피어로부터 트래픽을 수신하지 못하면 DPD가 `ISAKMP R_U_THERE` 헬로 메시지를 보낸다. 만약, 특정 횟수 동안 응답을 하지 않으면 피어가 죽은 것으로 간주하고 IPse 터널을 끊는다. 즉, 상대 피어와 ISAKMP SA 및 IPsec SA를 제거한다.


이와 같이 DPD를 이용하면 상대 피어의 장애를 감지하지 못하고 패킷을 전송하여 블랙홀이 발생하는 현상을 방지할 수 있으며, ISAKMP 킵얼라이브를 사용하는 것에 비해 CPU의 부하를 줄일 수 있다. **DPD는 양측 피어에 모두 설정되어야 한다.**


4. IPsec으로 보호할 트래픽을 지정한다.
5. 실제 데이터를 보호하기 위하여 사용할 암호화 알고리듬과 무결성 확인 방식을 지정한다.
6. 보호 대상 트래픽, 피어, 보호용 알고리듬 등을 묶어주는 정적인 크립토 맵을 만든다.
7. 크립토 맵 내에서 `reverse-route` 명령어를 사용하면 RRI가 동작한다.


RRI(reverse route injection)는 IPsec SA에서 보호 대상 네트워크를 추출하여 해당 네트워크로 가는 정적 경로를 자동으로 만들어 라우팅 테이블에 인스톨시키는 기능이다. 결과적으로 IPsec VPN이 설정된 각 라우터들은 해당 보호 대상 패킷의 목적지 네트워크로 가능 경로를 알게 된다. 즉, (#6)번 크립토 맴의 정보에서 보호 대상 트래픽(목적지 네트워크, 10.1.60.0/24)과 피어 (목적지 네트워크로 가는 게이트웨이 주소, 1.1.56.6) 정보를 이용하여 10.1.60.0/24로 가는 게이트웨이는 1.1.56.6이라는 정적 경로를 자동으로 생성한다.

필요시 RRI가 인스톨시킨 정적 경로를 동적 라우팅 프로토콜에 재분배하면 IPsec VPN이 설정된 라우터 후방에 있는 내부 라우터들도 목적지 네트워크를 알게 된다. RRI는 피어의 동작여부와 무관하게 무조건 라우팅 테이블에 정적 경로를 인스톨시키거나, DPD 기능이 해당 피어의 장애를 감지했을 때 RRI 라우팅 테이블에 인스톨시킨 정적 경로를 제거하게 할 수도 있다.


reverse-route 명령어만 사용하면 피어와 연결된 경우에만 정적 경로가 인스톨되고, `reverse-route static` 명령어를 사용하면 피어의 동작 여부와 상관없이 무조건 정적 경로를 인스톨한다. 현재 토폴로지와 같이 HSRP 액티브 라우터가 RRI를 이용한 정적 경로를 인스톨해야 하는 경우에는 static 옵션을 사용하지 않는다.

RRI에 의해서 자동으로 만들어지 정적 경로를 내부 라우터들에게 알려주기 위하여 다음과 같이 OSPF에 정적 경로를 재분배한다. 


[예제 3-20] OSPF에 정적 경로 재분배하기

``` bash
R2(config)# router ospf 1
R2(config-router)# redistribute static subnets
R2(config-router)# exit

```

8. 인터페이스 설정모드에서 `crypto map VPN redundancy GROUP1` 명령어를 사용하여 HSRP와 IPsec VPN이 함께 동작하도록 설정한다.

R30의 설정은 다음과 같이 R2의 설정과 완전히 동일하다.


[예제 3-21] R3의 설정

``` bash
R3(config)# crypto isakmp policy 10
R3(config-isakmp)# encryption aes
R3(config-isakmp)# authentication pre-share
R3(config-isakmp)# group 2
R3(config-isakmp)# exit

R3(config)# crypto isakmp key cisco address 1.1.56.6
R3(config)# crypto isakmp key cisco address 1.1.57.7

R3(config)# crypto isakmp keepalive 10

R3(config)# ip access-list extended R6
R3(config-ext-nacl)# permit ip 10.0.0.0 0.255.255.255 10.1.60.0 0.0.0.255
R3(config-ext-nacl)# exit
R3(config)# ip access-list extended R7
R3(config-ext-nacl)# permit ip 10.0.0.0 0.255.255.255 10.1.60.0 0.0.0.255
R3(config-ext-nacl)# exit

R3(config)# crypto ipsec transform-set PHASE2 esp-aes esp-sha-hmac
R3(cfg-crypto-trans)# exit

R3(config)# crypto map VPN 10 ipsec-isakmp
R3(config-crypto-map)# match address R6
R3(config-crypto-map)# set peer 1.1.56.6
R3(config-crypto-map)# set tranform-set PHASE2
R3(config-crypto-map)# reverse-route
R3(config-crypto-map)# exit
R3(config)# crypto map VPN 20 ipsec-isakmp
R3(config-crypto-map)# match address R7
R3(config-crypto-map)# set peer 1.1.57.7
R3(config-crypto-map)# set tranform-set PHASE2
R3(config-crypto-map)# reverse-route
R3(config-crypto-map)# exit

R3(config)# interface f0/0.100
R3(config-subif)# crypto map VPN redundancy GROUP1
R3(config-subif)# exit

R3(config)# router ospf 1
R3(config-subif)# redistribute static subnets
R3(config-subif)# exit


```

R6의 설정은 다음과 같다. R6에서도, R2, R3과 마찬가지로 `crypto isakmp keepalive` 명령어를 사용하여 DPD를 동작시켰다. 원격 지사인 R6에서 본사의 VPN 피어 주소를 설정할 때 실제 주소가 아닌 HSRP의 가상 주소 (VIP)인 1.1.100.10을 지정하였다.



[예제 3-22] R6의 설정

```bash
R6(config)# crypto isakmp policy 10
R6(config-isakmp)# encryption aes
R6(config-isakmp)# authentication pre-share
R6(config-isakmp)# group 2
R6(config-isakmp)# exit

R6(config)# crypto isakmp key 6 cisco address 1.1.100.10

R6(config)# crypto isakmp keepalive 10

R6(config)# ip access-list extended ACL-VPN-TRAFFIC
R6(config-ext-nacl)# permit ip 10.1.60.0 0.0.0.255 10.0.0.0 0.255.255.255
R6(config-ext-nacl)# exit

R6(config)# crypto ipsec transform-set PHASE2 esp-aes esp-sha-hmac
R6(config-crypto-trans)# exit

R6(config)# crypto map VPN 10 ipsec-isakmp
R6(config-crypto-map)# match address ACL-VPN_TRAFFIC
R6(config-crypto-map)# set peer 1.1.100.10
R6(config-crypto-map)# set transform-set PHASE2
R6(config-crypto-map)# exit

R6(config)# interface f0/0.56
R6(config-subif)# crypto map VPN
R6(config-subif)# exit


```

R7의 설정은 다음과 같다. R7에서도 `crypto isakmp keepalive` 명령어를 사용하여 DPD를 동작시켰다. R7에서도 본사의 HSRP 가상 IP 주소를 사용하여 피어를 설정한다.


[예제 3-23] R7의 설정
``` bash
R7(config)# crypto isakmp policy 10
R7(config-isakmp)# encryption aes
R7(config-isakmp)# authentication pre-share
R7(config-isakmp)# group 2
R7(config-isakmp)# exit

R7(config)# crypto isakmp key 6 cisco address 1.1.100.10

R7(config)# crypto isakmp keepalive 10

R7(config)# ip access-list extended ACL-VPN-TRAFFIC
R7(config-ext-nacl)# permit ip 10.1.70.0 0.0.0.255 10.0.0.0 0.255.255.255
R7(config-ext-nacl)# exit

R7(config)# crypto ipsec transform-set PHASE2 esp-aes esp-sha-hmac
R7(config-crypto-trans)# exit

R7(config)# crypto map VPN 10 ipsec-isakmp
R7(config-crypto-map)# match address ACL-VPN_TRAFFIC
R7(config-crypto-map)# set peer 1.1.100.10
R7(config-crypto-map)# set transform-set PHASE2
R7(config-crypto-map)# exit

R7(config)# interface f0/0.56
R7(config-subif)# crypto map VPN
R7(config-subif)# exit
```

이상과 같이 HSRP, RRI 및 DPD를 이용한 VPN 이중화 구성이 완료되었다. 다음과 같이 지사 라우터인 R6에서 본사의 내부망으로 핑을 해보면 성공한다.


[예제 3-24] 핑 테스트

``` bash

R6# ping 10.1.10.1 source 10.1.60.6

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.10.1, timeout is 2 seconds:
Packet sent with a source address of 10.1.60.6
..!!!
Success rate is 60 percent (3/5), round-trip min/avg/max=124/300/536 ms
```

반대로, 본사의 내부망에서 지사 라우터인 R7로 핑을 해도 된다.


[예제 3-25] 핑 테스트

``` bash

R6# ping 10.1.70.7 

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.70.7, timeout is 2 seconds:
..!!!
Success rate is 60 percent (3/5), round-trip min/avg/max=124/300/536 ms
```

본사의 주 VPN 게이트웨이 역할을 하는 R2에서 show crypto isakmp sa 명령어를 사용하여 확인해보면 다음과 같이 R6 (1.1.56.6), R7(1.1.57.7)과 ISAKMP SA가 만들어져 있다.


[예제 3-26] ISAKMP SA 확인

```bash
R2# show crypto isakmp sa
```

본사의 주 VPN 게이트웨이 역할을 하는 R2에서 `show crypto ipsec sa` 명령어를 사용하여 확인해보면 다음과 같이 R6, R7의 내부망과 IPsec으로 보호되어 송수신 패킷의 수를 확인할 수 있다.


[예제 3-27] IPsec SA 확인

``` bash
R2# show crypto ipsec sa
```

그러나, 백업 역할을 하는 R3은 `show crypto isakmp sa` 명령어를 사용하여 확인해보면 다음과 같이 생성된 ISAKMP SA가 없다. 즉, 현재 R3은 IPsec 통신에 참여하고 있지 않다는 것을 알 수 있다.


[예제 3-28] ISAKMP SA 확인


``` bash

R3# show crypto isakmp sa
IPv4 Crypto ISAKMP SA
dst           src         state       conn-id    slot       status

IPv6 Crypto ISAKMP SA

R3#


```

---

### RRI 동작 확인


R2의 라우팅 테이블을 확인해보면 다음과 같이 자동으로 RRI를 이용하여 R6의 내부망인 `10.1.60.0/24`와 R7의 내부망인 `10.1.70.0/24`로 가능 정적 경로가 인스톨되어 있다.


[예제 3-29] R2의 라우팅 테이블

``` bash
R2# show ip route


Gateway of last resort is 1.1.100.4 to network 0.0.0.0

1.0.0.0/24 is subnetted, 1 subnets
C     1.1.100.0 is directly connected, FastEthernet0/0.100 10.0.0.0/24 is subnetted, 5 subnets

O 10.1.10.0 [110/2] via 10.1.12.1, 00:36:34, FastEthernet0/0.12
O 10.1.13.0 [110/2] via 10.1.12.1, 00:36:34, FastEthernet0/0.12
C 10.1.12.0 is directly connected, FastEthernet0/0.12
S 10.1.60.0 [1/0] via 1.1.56.6
S 10.1.70.0 [1/0] via 1.1.57.7
S* 0.0.0.0/0 [1/0] via 1.1.100.4
```

또, 이것이 OSPF로 재분배되어 내부망인 R1의 라우팅 테이블에도 다음과 같이 인스톨된다.



[예제 3-30] R1의 라우팅 테이블

``` bash
R1# show ip route

Gateway of last resort is 1.1.12.2 to network 0.0.0.0

1.0.0.0/24 is subnetted, 5 subnets
C     10.1.10.0 is directly connected, FastEthernet0/0.10 
C     10.1.13.0 is directly connected, FastEthernet0/0.13 
C 10.1.12.0 is directly connected, FastEthernet0/0.12
O E2 10.1.60.0 [110/20] via 10.1.12.2, 00:09:40, FastEthernet0/0.12
O E2 10.1.70.0 [110/20] via 10.1.12.2, 00:08:46, FastEthernet0/0.12
O*E1 0.0.0.0 [110/20] via 10.1.12.2, 00:33:23, FastEthernet0/0.12
```

결과적으로 내부망인 R1에서 목적지가 `10.1.60.0/24`이거나 `10.1.70.0/24`인 패킷들은 라우팅은 테이블을 참조하여 모두 R2로 전송한다. 이를 수신한 R2는 목적지가 `10.1.60.0/24`이거나 `10.1.70.0/24`인 패킷들을 RRI가 만든 라우팅 테이블을 참조하여 해당 게이트웨이인 R6 또는 R7로 전송한다.


[그림 3-7] 정상시의 통신 경로




그러다가, R2에 장애가 발생하면 다음 그림과 같이 R3과 지사 라우터들간에 IPsec 세션이 만들어지고 이번에는 R3이 RRI를 이용하여 정적 경로를 생성한 후 OSPF를 통하여 R1에게 광고한다.


[그림 3-8] 장애 발생시의 통신 경로



이제 R1에서 목적지가 10.1.60.0/24이거나 10.1.70.0/24인 패킷들은 라우팅 테이블을 참조하여 모두 R3으로 전송된다. 이를 수신한 R3은 목적지가 10.1.60.0/24이거나 10.1.70.0/24인 패킷들을 RRI가 만든 라우팅 테이블을 참조하여 해당 게이트인 R6 또는 R7로 전송한다.




### VPN 이중화 동작 확인

실제 HSRP, DPD 및 RRI가 결합되어 IPsec 다이렉트 인캡슐레이션 VPN의 이중화가 동작하는 것을 확인해보자. 다음과 같이 본사의 VPN 게이트웨이에서 ISAKMP와 IPsec을 디버깅한다.


[예제 3-31] IPsec 디버깅

```bash
R2# debug crypto isakmp
R2# debug crypto ipsec

R3# debug crypto isakmp
R3# debug crypto ipsec

```

지사 라우터에서 본사 내부의 네트워크로 핑을 한다.

[[리눅스 예제 3-31]]


[예제 3-32] 핑 테스트

``` bash
R6# ping 10.1.10.1 source 10.1.60.6 repeat 100000
```

[[리눅스 예제 3-32]]

다음과 같이 주 VPN 게이트웨이인 R2의 인터페이스를 셧다운시켜 장애를 발생시킨다.

[예제 3-33] R2의 인터페이스 셧다운시키기

```bash
R2(config)# interface f0/0.12
R2(config-subif)# shut
```

그러면, 다음과 같이 잠시동안 핑이 빠진다. 즉, IPsec VPN이 끊어졌다가 다시 연결된다.


[예제 3-34] IPsec VPN의 재동작

```bash
R6#  ping 10.1.10.1 source 10.1.60.6 repeat 10000009
```



결과적으로 다음 그림과 같이 장애가 발생한 R2를 대신하여 R3이 IPsec VPN 게이트웨이 역할을 이어 받는다.

[그림 3-9] 장애 발생시의 IPsec VPN 게이트웨이




다음 디버깅 결과를 참조하여 IPsec VPN 이중화가 동작하는 것을 살펴보자.




[예제 3-35] 디버깅 결과

```bash
R3# 
%HSRP-5-STATECHANGE: FastEthernet0/0.100 Grp 1 state Standby -> Active (#1)

%CRYPTO-4-RECVD_PKT_INV_SPI: decaps: rec`d IPSEC packet has invalid spi for destaddr=1.1.100.10, prot=50, spi=0x33C701EA(868680170), srcaddr=1.1.56.6 (#2) 

ISAKMP: ignoring request to send delete notify (no ISAKMP sa) src 1.1.100.10 dst 1.1.56.6 for SPI 0x33C701EA (#3)

ISAKMP: (0:0): received packet from 1.1.56.6 dport 500 sport 500 Global (N) NEW SA (#4)

ISAKMP:(0):found peer pre-shared key matching 1.1.56.6 (#5)
ISAKMP:(0): local preshared key found
ISAKMP : Scanning profiles for xauth ...
ISAKMP:(0): Checking ISAKMP transform 1 against priority 10 policy
ISAKMP:            encryption AES-CBC
ISAKMP:            keylength of 128
ISAKMP:            hash SHA
ISAKMP:            default group 2
ISAKMP:            auth pre-share
ISAKMP:(0):atts are acceptable. Next payload is 0.

IPSEC(update_current_outbound_sa): updated peer 1.1.56.6 current outbound sa to SPI DD8C4BB4

```

1. R2에 장애가 발생하여 R3이 HSRP 액티브 라우터가 된다.
2. 목적지가 HSRP 가상 IP 주소인 1.1.100.10으로 가는 패킷이 모두 R3으로 전송된다. 기존에 R6에서 R2로 전송되던 IPsec 패킷을 R3이 수신한다. R3에는 해당 IPsec SA가 없으므로 SPI(security parameter index)가 잘못된 패킷이라는 로그 메시지를 생성한다.
3. 해당 패킷을 폐기한다.
4. DPD에 의해서 R2-R6간의 IPSec 세션에 장애가 생긴 것을 감지한 R6이 새로운 ISAKMP SA 협상을 시도한다.
5. 새로운 ISAKMP SA가 만들어진다.
6. 새로운 IPsec SA가 만들어진다.

이후 R3의 라우팅 테이블을 확인해보면 다음과 같이 RRI에 의한 정적 경로가 인스톨된다.