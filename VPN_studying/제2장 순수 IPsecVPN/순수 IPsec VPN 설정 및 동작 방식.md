

순수 IPsec VPN 즉, **IPsec 다이렉트 인캡슐레이션**(direct encapsulation) VPN이란 원래의 패킷에 IPsec 관련 헤더 외에 다른 헤더는 사용하지 않는 방식을 말한다. 이번 장에서는 순수 IPsec VPN의 설정 및 동작 방식에 대해서 살펴보자.


---
# 순수 IPsec VPN의 특성


다음 그림은 순수 IPsec VPN 중에서 ESP를 사용하는 트랜스포트 모드와 터널 모드의 패킷 구성을 나타낸다. <mark style="background: #FF5582A6;">첫 번째 그림은 트랜스 포트 모드로 IPsec 관련 헤더 외에는 다른 헤더를 사용하지 않는다.</mark> <mark style="background: #FF5582A6;">두 번째 그림은 터널 모드로 역시 터널을 위한 새로운 IP 헤더와 ESP 헤더 외에는 다른 헤더를 추가하지 않았다.</mark>

[그림 2-1] 순수 IPsec VPN 패킷 구성

![[2장 - 1.jpg]]

그러나, 예를 들어, 나중에 살펴볼 GRE IPsec VPN은 다음 그림과 같이 IPsec 헤더외에 GRE 헤더가 추가된다. 첫 번째 그림인 트랜스포트 모드나 두 번째 그림인 터널 모드 모드에서 GRE 헤더가 추가된다.


[그림 2-2] GRE IPsec 패킷 구성

![[2장 - 2.jpg]]

이처럼 IPsec 헤더외에 GRE 헤더를 추가하는 이유는<mark style="background: #ADCCFFA6;"> 동적인 라우팅을 지원</mark>하거나, <mark style="background: #ADCCFFA6;">대규모 IPsec VPN 네트워크 구축 및 관리의 편이성</mark>을 위해서이다.

순수 IPsec VPN은 가장 기본적인 형태의 IPsec VPN으로서 동적인 라우팅이나 멀티캐스트를 지원하지 않으며, 소규모의 IPsec VPN을 구축하는 곳에 많이 사용되다.



---
# 테스트 네트워크 구축

<mark style="background: #ADCCFFA6;">순수 IPsec VPN</mark>의 설정 및 동작 확인을 위하여 다음과 같은 테스트 네트워크를 구축한다. 그림에서 R1, R2는 본사 라우터라고 가정하고, R4, R5는 지사 라우터라고 가정한다. 또, R2, R3, R4, R5를 연결하는 구간은 인터넷 또는 IPsec으로 보호하지 않을 외부망이라고 가정한다.

[그림 2-3] *테스트 네트워크*

![[2장 - 3.jpg]]

이를 위하여 다음과 같이 각 라우터의 인터페이스에 IP 주소를 부여한다.


[그림 2-4] *IP 주소*

![[2장 - 4.jpg]]

각 라우터에서 F0/0 인터페이스를 서브 인터페이스로 동작시킨다. 서브 인터페이스 번호, 서브넷 번호  및 VLAN 번호를 동일하게 사용한다. 이를 위하여 다음과 같이 SW1에서 VLAN을 만들고, 각 라우터와 연결되는 포트를 트렁크로 동작시킨다.


[예제 2-1] *SW1 설정*

``` cisco
SW1(config)# vlan 10
SW1(config-vlan)# vlan 12
SW1(config-vlan)# vlan 23
SW1(config-vlan)# vlan 34
SW1(config-vlan)# vlan 35
SW1(config-vlan)# vlan 40
SW1(config-vlan)# vlan 50
SW1(config-vlan)# exit

SW1(config)# interface f1/1 - 5
SW1(config-if-range)# switchport trunk encapsulation dot1q
SW1(config-if-range)# switchport mode trunk 

```
[[예제 2-1 리눅스 환경]]


이번에는 각 라우터의 인터페이스를 활성화시키고, 서브 인터페이스를 만든 다음 IP 주소를 부여한다. R3과 인접 라우터 사이는 인터넷이라고 가정하고, IP 주소 1.0.0.0/8을 서브넷팅하여 사용한다. 나머지 부분은 내부망이라고 가정하고 IP 주소 10.0.0.0/8을 서브네팅하여 사용한다. R1의 설정은 다음과 같다.


[예제 2-2] *R1 설정*
``` linux
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

[[예제 2-2 리눅스 환경]]



R2의 설정은 다음과 같다.

[예제 2-3] *R2 설정*

```bash
R2(config)# interface f0/0
R2(config-if)# no shut
R2(config-if)# exit

R2(config)# interface f0/0.12
R2(config-subif)# encapsulation dot1Q 10
R2(config-subif)# ip address 10.1.12.2 255.255.255.0
R2(config-subif)# exit

R2(config)# interface f0/0.23
R2(config-subif)# encapsulation dot1Q 23
R2(config-subif)# ip address 10.1.23.2 255.255.255.0
```



R3의 설정은 다음과 같다.


[예제 2-4] *R3 설정*

``` bash
R3(config)# interface f0/0
R3(config-if)# no shut
R3(config-if)# exit

R3(config)# interface f0/0.23
R3(config-subif)# encapsulation dot1Q 23
R3(config-subif)# ip address 10.1.23.3 255.255.255.0
R3(config-subif)# exit

R3(config)# interface f0/0.34
R3(config-subif)# encapsulation dot1Q 34
R3(config-subif)# ip address 10.1.34.3 255.255.255.0

R3(config)# interface f0/0.35
R3(config-subif)# encapsulation dot1Q 35
R3(config-subif)# ip address 10.1.35.3 255.255.255.0
```

R4의 설정은 다음과 같다.
[예제 2-5] R4 설정
``` bash
R4(config)# interface f0/0
R4(config-if)# no shut
R4(config-if)# exit

R4(config)# interface f0/0.34
R4(config-subif)# encapsulation dot1Q 34
R4(config-subif)# ip address 10.1.34.3 255.255.255.0
R4(config-subif)# exit

R4(config)# interface f0/0.40
R4(config-subif)# encapsulation dot1Q 40
R4(config-subif)# ip address 10.1.40.3 255.255.255.0

```

R5의 설정은 다음과 같다.

[예제 2-6] *R5 설정*

``` bash
R5(config)# interface f0/0
R5(config-if)# no shut
R5(config-if)# exit

R5(config)# interface f0/0.35
R5(config-subif)# encapsulation dot1Q 35
R5(config-subif)# ip address 10.1.35.5 255.255.255.0
R5(config-subif)# exit

R5(config)# interface f0/0.50
R5(config-subif)# encapsulation dot1Q 50
R5(config-subif)# ip address 10.1.50.5 255.255.255.0
```

라우터의 IP 주소 설정이 끝나면 넥스트 홉 IP 주소까지의 통신을 핑으로 확인한다. 다음에는 외부망 또는 인터넷으로 사용할 부분의 라우팅을 설정한다.


[그림 2-5] *외부망 라우팅*

![[2장 - 5.jpg]]

이를 위하여 외부망에 인접한 각 라우터에서 다음과 같이 R3으로 정적인 디폴트 루트를 설정한다.

[예제 2-7] *디폴트 루트 설정*

``` bash
R3(config)# ip route 0.0.0.0 0.0.0.0 1.1.23.3
R4(config)# ip route 0.0.0.0 0.0.0.0 1.1.34.3
R5(config)# ip route 0.0.0.0 0.0.0.0 1.1.35.3


```

[[예제 2-7 리눅스 환경]]





설정이 끝나면 R2에서 원격지까지의 통신을 핑으로 확인한다.


[예제 2-8] *원격지까지의 통신 확인*

```bash

R2# ping 1.1.34.4
R2# ping 1.1.35.5

```

다음 그림과 같이 IP 주소 10.0.0.0/8이 설정된 부분을 내부 네트워크로 사용한다. 본사 네트워크로 사용할 R1, R2간에 EIGRP 1을 설정한다. 또, 앞서 R2에서 설정한 디폴트 루트를 EIGRP에 재분배시켜 R1에게도 광고한다.




[그림 2-6] *내부망 라우팅*

![[2장 - 6.jpg]]



R1, R2에서의 라우팅 설정은 다음과 같다.


[예제 2-9] *R2, R1에서의 라우팅 설정*

``` bash
R1(config)# router eigrp 1
R1(config-router)# network 10.1.10.1 0.0.0.0
R1(config-router)# entwork 10.1.12.1 0.0.0.0

R1(config)# router eigrp 1
R1(config-router)# entwork 10.1.12.2 0.0.0.0
R1(config-router)# redistribute static

```

[[예제 2-9 리눅스 환경]]

EIGRP 설정 후 R2의 라우팅 테이블에서 다음과 같이 R1의 내부망인 `10.1.10.0/24` 네트워크가 인스톨되는지 확인한다.


[예제 2-10] *R2의 라우팅 테이블*

``` bash

R2# show ip route eigrp
```

경계 라우터인 R2에 다음과 같이 ACL을 설정해 보자. ACL 설정이 꼭 필요한 것은 아니지만 IPsec VPN의 동작을 확인하는데 도움이 된다.

[예제 2-11] *R2의 ACL*

``` bash
R2(config)# ip access-list extended ACL_INBOUND
R2(config-ext-nacl)# permit udp host 1.1.34.4 eq 500 host 1.1.23.2 eq 500
R2(config-ext-nacl)# permit udp host 1.1.35.5 eq 500 host 1.1.23.2 eq 500
R2(config-ext-nacl)# permit esp host 1.1.34.4 host 1.1.23.2
R2(config-ext-nacl)# permit esp host 1.1.35.5 host 1.1.23.2
R2(config-ext-nacl)# exit

R2(config)# interface f0/0.23
R2(config-subif)# ip access-group ACL-INBOUND in

```

[[예제 2-11 리눅스 환경]]

ACL은 R4, R5가 전송하는 ISAKMP 패킷 및 ESP 패킷을 허용한다. 이제, 순수 IPsec VPN을 설정할 네트워크가 구성되었다.


---
# 순수 IPsec VPN 설정


다음 그림과 같이 인터넷과 연결되는 경계 라우터인 R2, R4, R5에서 순수 IPsec L2L VPN을 설정하여 내부망간의 트래픽을 보호해 보자. 그림과 같이 본지사 내부망 사이의 통신을 위한 VPN을 LAN-to-LAN 또는 L2L VPN이라고 한다.


[그림 2-7] IPsec VPN 설정

![[2장 - 7.jpg]]


지사 네트워크인 R4의 `10.1.40.0/24`와 R5의 `10.1.50.0/24` 네트워크 사이의 통신도 본사 라우터인 R2를 통하도록 설정한다.

기본적인 순수 IPsec L2L VPN을 설정하는 순서는 다음과 같다.


1. IKE 제1단계 정책을 설정한다.
2. IKE 제2단계 정책을 설정한다.
3. 크립토 맵을 만들고 인터페이스에 적용한다.

---
# IKE 제1단계 정책 설정


먼저, `crypto isakmp policy` 명령어를 사용하여 IKE(internet key exchange) 제1단계 정책을 설정한다. I**KE 제1단계 정책을 ISAKMP 정책**이라고도 한다.<mark style="background: #ADCCFFA6;"> IKE 제1단계는 상대에 대한 인증 작업을 하고, 보안성이 없는 네트워크를 통하여 송수신되는 패킷에 대해 암호화, 무결성 확 등을 통하여 보안성을 가진 네트워크로 변경하는 단계</mark>이다. 이렇게 제1단계 거쳐 보안성을 가진 네트워크로 변경되면 이를 통하여 IKE 제2단계 동작한다.

IKE 제1단계의 보안정책 협상 내용을 보호하기 위하여 패킷의 암호화 및 무결성 확인 방식을 지정한다. 마찬가지로 나중에 IKE 제2단계에서도 실제 데이터를 보호하기 위한 패킷의 암호화 및 무결성 확인 방식을 지정하는데, 이 두 가지는 별개이다. 즉, <mark style="background: #BBFABBA6;">IKE 제1단계에서는 암호화 알고리즘으로 3DES</mark>를 사용하고, <mark style="background: #BBFABBA6;">IKE 제2단계에서는 AES를 사용할 수 있다</mark>.

IKE 정책을 설정할 때 사용하는 명령어는 대부분 `crypto isakmp`로 시작한다. 또, IKE 정책의 동작을 확인할 때 사용하는 명령어도 대부분 `show crypto isakmp`로 시작한다. 별도로 설정하지 않았을 때 기본적인 IKE 제1단계 정책은 다음과 같다.

[예제 2-12] *기본적인 IKE 제1단계 정책*

``` bash
R4# show crypto isakmp policy

Global IKE policy
Default protection suite
   encryption algorithm:   DES-Data Encryption Standard (56 bits keys).
   hash algorithm:         Secure Hash Standard
   authentication method:  Rivest-Shamir-Adleman Signature
   Diffie-Hellman group:   #1 (768 bit)
   lifetime:               86400 seconds, no volume limit

```

[[`crypto isakmp`]]


<mark style="background: #ADCCFFA6;">출력 결과를 보면 기본적인 암호화 알고리즘은 DES를 사용한다.</mark> 또, 변조 방지를 위한 **무결성 확인**을 위해서는 **SHA**(Secure Hash Standard)를 사용하고, 상대방 인증을 위해서는 RSA 시그니처(Rivest-Shamir-Adleman Signature)를 사용한다.

패킷 암호화 및 무결성 확인시 비밀키를 생성하기 위하여 키 길이 768 비트의 디피-헬먼 그룹 1 알고리즘을 사용하며, 이와 같은 ISAKMP SA(security association)의 수명은 86,400초 즉, 1일로 지정된다.

[표 2-1]


| 항목          | 기본값                    | 사용 가능 값                      |
| ----------- | ---------------------- | ---------------------------- |
| 암호화 알고리즘    | DES                    | DES, 3DES, AES               |
| 무결성 확인 알고리즘 | SHA                    | MD5, SHA                     |
| 인증 방식       | RSA-SIG                | Pre-Share, RSA-SIG, RSA-ENCR |
| 대칭키 생성 알고리즘 | Diffie-Hellman group 1 | Diffie-Hellman group 1, 2, 5 |
| 보안정책 수명     | 86,400 초               | 60- 86,400초                  |

예를 들어, 본사 라우터인 R2에서 암호화 알고리즘은 AES, 인증 방식은 각 VPN 라우터에서 미리 암호를 지정하는 PSK(pre-shared key) 방식, 대칭 비밀키 생성은 디피-헬먼 그룹 2를 사용하도록 설명하는 방법을 다음과 같다.


[예제 2-13] *IKE 제1단계 정책 설정*

``` bash
R2(config)# crypto isakmp policy 10
R2(config-isakmp)# encryption aes
R2(config-isakmp)# authentication pre-share
R2(config-isakmp)# group 2
R2(config-isakmp)# exit


```

[[예제 2-13 리눅스 환경]]



IKE 제1단계 정책을 설정할 때 crypto isakmp policy 명령어 다음에 1에서 10000까지의 번호를 지정한다.이 번호가 낮을수록 우선순위가 높다. 즉, <mark style="background: #ADCCFFA6;">피어 라우터와 정책을 비교할 때 이 값이 낮은 것부터 먼저 비교하고 양측에 일치하는 정책이 있으면 그것을 이용한다.</mark> 피어 라우터 간에 보안정책 유효기간 서로 다를 때는 값이 적은 것을 사용한다. <mark style="background: #ADCCFFA6;">나머지 보안 알고리즘은 반드시 양측이 일치해야 하며, 일치하지 않으면 피어가 맺어지지 않는다</mark>.

설정 후 다음과 같이 `show crypto isakmp policy` 명령어를 사용하여 확인해보면 별도로 설정하지 않은 항목들은 기본값을 사용한다.

[예제 2-14] *제1단계 정책 보기*

``` bash
R2# show crypto isakmp policy

Global IKE policy
Protection suite of priority 10
	encryption alogrithm: AES- Advanced Encryption Standard (128 bits keys)
	hash algorithm: Secure Hash Standard
	authentication method: Pre-Shared KEy
	Diffie-Hellman group: #2 (1024 bit)
	lifetime: 86400 seconds, no volume limit

Default protection suite:
	encryption algorithm: DES - Data Encryption Standard (56 bit keys).
	hash algorithm:       Secure Hash Standard
	authentication method : Rivest-Shamir-Adleman Signature
	Diffie-Hellman group: #1 (768 bit)
	lifetime:             86400 seconds, no volume limit
```


다음과 같이 상대 VPN 라우터, 피어(peer)를 인증하기 위한 암호를 지정한다.


[예제 2-15] *피어 인증을 위한 암호*

``` bash
R2(config)# crypto isakmp key cisco address 1.1.34.4
R2(config)# crypto isakmp key cisco address 1.1.34.5
```

만약 피어의 IPv4 또는 IPv6 주소 대신 호스트 이름을 사용하려면 다음과 같이 hostname의 키워드를 사용한 다음 상대의 호스트 이름을 지정하면 된다.


[예제 2-16] *호스트 이름 사용시의 암호 지정*

```bash
R2 (config)# crypto isakmp key cisco hostname r4.cisco.com
```

만약 ,암호 자체를 AES로 암호화시켜 저장시키려면 다음과 같이 `key config-key password-encrypt` 명령어를 사용한다. 이후 입력하는 암호는 길이가 8자 이상이어야 하며, 나중에 암호를 암호화하기 위한 설정을 제거할 때 필요한다. password encryption aes 명령어를 사용하여 암호화하는 방식을 AES로 지정한다.

[예제 2-17] *암호를 AES를 암호화하기*

```bash
R2(config)# key config-key password-encypt
New key:
Confirm key:
R2(config)# password encryption aes
```



설정 후 확인해 보면 다음과 같이 암호 자체가 암호화되어 있다.

[예제 2-18]

```
R2# show running-config | include isakmp key

```

[[예제 2-18 리눅스 환경]]


이상으로 기본적은 IKE 제 1단계 설정이 끝이 났다.

---
# IKE 제2단계 정책 설정

IKE 제2단계 정책을 IPsec SA라고도 한다. IKE 제2단계 정책은 IPsec 패킷의 캡슐레이션 방식, 데이터 암호화 알고리듬, 무결성 확인 알고리듬 종류 등과 보호대상 트래픽을 지정하는 것들이 포함된다.

IKE 제2단계 정책을 설정을 위해서 먼저 IPsec으로 보호해야 하는 트래픽을 ACL을 사용하여 지정한다. 이를 위하여 다음과 같이 피어 별로 별개의 ACL을 사용하여 보호대상 트래픽을 지정한다.

[예제 2-19] *보호대상 트래픽 지정*

``` bash
R2(config)# ip access-list extended R4
R2(config-ext-nacl)# permit ip 10.1.10.0 0.0.0.25 10.1.40.0 0.0.0.25
R2(config-ext-nacl)# permit ip 10.1.12.0 0.0.0.25 10.1.40.0 0.0.0.25
R2(config-ext-nacl)# permit ip 10.1.50.0 0.0.0.25 10.1.40.0 0.0.0.25
R2(config-ext-nacl)# exit

R2(config)# ip access-list extended R5
R2(config-ext-nacl)# permit ip 10.1.10.0 0.0.0.255 10.1.50.0 0.0.0.255
R2(config-ext-nacl)# permit ip 10.1.12.0 0.0.0.255 10.1.50.0 0.0.0.255
R2(config-ext-nacl)# permit ip 10.1.40.0 0.0.0.255 10.1.50.0 0.0.0.255
R2(config-ext-nacl)# exit

```

[[예제 2-19 리눅스 환경]]


이번에는 다음과 같이 IKE 제2단계 SA 보안 알고리즘을 지정한다.

[예제 2-20] *IKE 제2단계 SA 보안 알고리즘*

``` bash
R2(config)# crypto ipsec transform-set PHASE-POLICY esp-aes esp-md5-hmac
```

[[예제 2-20 리눅스 환경]]


이처럼 IKE 제2단계 정책을 설정하는 명령어는 crypto ipsec으로 시작하며, IKE 제 2단계 정책의 동작을 확인하는 명령어는 show crypto ipsec으로 시작한다. 제2단계에서 패킷의 인캡슐레이션은 ESP(encapsulation security payload)를 사용하고, 암호화 알고리즘은 AES, 무결성 알고리즘은 MD5를 사용하도록 설정했다. 다음과 같이 `show crypto ipsec transform-set` 명령어를 사용하면 현재 설정된 IKE 제2단계 정책, IPsec SA를 확인할 수 있다.



[예제 2-21] *IPsec SA 확인하기*

``` bash
R2(config)# crypto ipsec transform-set
Transform set PHASE2-POLICY: {esp-aes esp-md5-hmac}
	will negotiate = { Tunnel, },
```

또, 확인 결과에서 기본적인 IPsec 모드가 터널 모드인 것도 알 수 있다.


---
# 크립토 맵 만들기

앞서 지정한 보호대상 네트워크, 보안 알고리즘 종류 등을 하나로 묶기 위하여 다음과 같이 크립토 맵(crypto map)을 만든다.

[예제 2-22] 크립토 맵 만들기

``` bash
$ R2(config)# crypto map VPN 10 ipsec-isakmp
$ R2(config-crypto-map)# match address R4
$ R2(ocnfig-crypto-map)# set peer 1.1.34.4
$ R2(config-crypto-map)# set transform-set PHASE2-POLICY
$ R2(config-crypto-map)# exit

$ R2(config)# crypto map VPN 20 ipsec-isakmp
$ R2(config-crypto-map)# match address R4
$ R2(ocnfig-crypto-map)# set peer 1.1.34.4
$ R2(config-crypto-map)# set transform-set PHASE2-POLICY
$ R2(config-crypto-map)# exit
```


마지막으로 앞서 만든 크립토 맵을 다음과 같이 인터페이스에 적용한다.

[[예제 2-23 리눅스 예제]]


[예제 2-23] 크립토 맵을 인터페이스에 적용하기
``` bash
R2(config)# interface f0/0.23
R2(config-subif)# crypto map VPN
```

이상으로 본사 VPN 게이트웨이인 R2에서 기본적인 IPsec L2L VPN 설정이 완료되었다.


---
# 지사의 IPsec L2L VPN 설정

이번에는 지사 라우터에서 IPsec L2L VPN을 설정해 보자. R4의 IKE 제1단계 정책 설정은 다음과 같다.

[예제 2-24] IKE 제1단계 정책 설정

``` bash
R4(config)# crypto isakmp policy 10
R4(config-isakmp)# encryption aes
R4(config-isakmp)# authentication pre-share
R4(config-isakmp)# group 2
R4(conifg-isakmp)# exit

R4(config)# crypto isakmp key cisco address 1.1.32.2
```

피어인 본사와 동일한 IKE 제1단계 보안정책을 사용해야 한다. 즉, AES 알고리즘을 이용하여 패킷을 암호화하고, 인증 방식으로 미리 설정한 암호(PSK)를 사용하며, 암호화 및 무결성 확인을 위한 대칭키를 디피-헬먼 그룹 2 알고리즘을 사용하여 생성한다. 또, 본사에서 설정한 것과 동일한 암호를 지정하였다.

R4의 IKE 제2단계 정책 설정은 다음과 같다.


[예제 2-25] R4의 IKE 제2단계 정책 설정

``` bash
R4(config)# ip access-list extended R2
R4(config-ext-nacl)# permit ip 10.1.40.0 0.0.0.25 10.1.10.0 0.0.0255
R4(config-ext-nacl)# permit ip 10.1.40.0 0.0.0.25 10.1.12.0 0.0.0255
R4(config-ext-nacl)# permit ip 10.1.40.0 0.0.0.25 10.1.50.0 0.0.0255
R4(config-ext-nacl)# exit

R4(config)# crypto ipsec transform-set PHASE2-POLICY esp-aes esp-md5-hmac
R4(cfg-crypto-trans)# exit

```
 IPsec으로 보호할 트래픽을 ACL을 이용하여 지정하였다. 본사에서 지정한 내용과 반대로 설정하면 된다. 예를 들어, <mark style="background: #ADCCFFA6;">본사의 설정에서는 10.1.10.0/24에서 10.1.40.0/24로 가는 패킷을 보호하고</mark>, <mark style="background: #ADCCFFA6;">지사에서는 반대로 10.1.40.0/24에서 10.1.10.0/24로 가는 패킷을 보호하도록 설정한다.
</mark>

마지막으로 다음과 같이 크립토 맵을 만들고 인터페이스에 적용한다.

[예제 2-26] 크립토 맵을 만들고 인터페이스에 적용하기
``` bash
R4(config)# crypto map VPN 10 ipsec-isakmp
R4(config-crypto-map)# match address R2
R4(config-crypto-map)# set peer 1.1.23.2
R4(config-crypto-map)# set transform-set PHASE2-POLICY
R4(config-crypto-map)# exit

R4(config)# interface f0/0.34
R4(config0subif)# crypto map VPN
R4(config0subif)# exit

```

이상과 같이 지사인 R4에서 IPsec L2L VPN을 설정하였다. 다음은 R5에서 IPsec VPN을 설정한다.

[예제 2-27] R5의 IPsec VPN 설정

``` bash
R5(config)# crypto isakmp policy 10
R5(config-isakmp)# encryption aes
R5(config-isakmp)# authentication pre-share
R5(config-isakmp)# group 2
R5(config-isakmp)# exit

R5(config)# crypto isakmp key 6 cisco address 1.1.23.2

R5(config)# ip access-list extended R2
R5(config-ext-nacl)# permit ip 10.1.50.0 0.0.0.255 10.1.10.0 0.0.0.255
R5(config-ext-nacl)# permit ip 10.1.50.0 0.0.0.255 10.1.12.0 0.0.0.255
R5(config-ext-nacl)# permit ip 10.1.50.0 0.0.0.255 10.1.40.0 0.0.0.255
R5(config)# exit

R5(config)# crypto ipsec transform-set PHASE2-POLICY esp-aes esp-md5-hmac
R5(cfg-crypto-trans)# exit

R5(config)# crypto map VPN 10 ipsec-isakmp
R5(config-crypto-map)# match address R2
R5(config-crypto-map)# set peer 1.1.23.2
R5(config-crypto-map)# set transform-set PHASE2-POLICY
R5(config-crypto-map)# exit

R5(config)# interface f0/0.35
R5(config-subif)# crypto map VPN
R5(config-subif)# exit

```

R5의 설정도 R2, R4와 유사하므로 설명은 생략한다. 이상과 같이 IPsec L2L VPN 게이트웨이인 R2, R4, R5의 설정이 완료되었다.

---
# 루프백 IP 주소를 이용한 피어 설정

루프백 IP 주소를 이용하여 피어를 설정하면, 피어와 연결되는 다수개의 경로가 있을 때 하나의 경로가 다운되어도 계속적인 VPN 동작이 가능하다. 이를 위하여 다음과 같이 외부망에서 라우팅 가능한 IP 주소를 루프백 인터페이스에 부여한다.

[예제 2-28]

``` bash
R2(config)# interface lo0
R2(config-if)# ip address 1.1.2.2. 255.255.255.0

R2(config)# crypto isakmp key 6 cisco address 1.1.4.4

R2(config)# crypto map VPN local-address loopback 0
R2(config)# crypto map VPN 10 ipsec-isakmp
R2(config-crypto-map)# set peer 1.1.4.4
R2(config-crypto-map)# exit

R2(config)# interface f0/0.23
R2(config-subif)# crypto map VPN
```

그런 다음, 크립토 맵을 만들 때 `crypto map VPN local-address loopback 0` 명령을 사용하여 VPN 세션의 출발지 주소가 루프백에 설정된 것임을 알려주면 된다.



### IPsec L2L VPN 동작 확인

이제, IPsec L2L VPN의 동작을 확인해보자. 먼저, 다음과 같이 R2에게 `clear access-list counters` 명령어를 사용하여 ACL의 통계를 초기화시킨다.

[예제 2-29] ACL 통계 초기화

``` bash
R2# clear access-list counters
```

R2에서 다음과 같이 `show crypto isakmp peers` 명령어를 사용하여 확인해 보면 피어가 없다.



[예제 2-30] 피어 확인

``` bash
R2# show crypto isakmp peers

R2#
```

본사 내부 라우터인 R1에서 지사의 내부망인 10.1.40.4로 핑을 해보면 성공한다.


[예제 2-31] 핑 테스트

``` bash
R1# ping 10.1.40.4

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.40.4, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5). round-trip min/avg/max= 72/133/232 ms
```

즉, R2가 10.1.12.1에서 10.1.40.4로 전송되는 패킷을 IPsec 터널로 전송하고, R4도 IPsec 터널로 응답했다. R2에서 다시 `show crypto isakmp peers` 명령어를 사용하여 확인해 보면 다음과 같이 R4의 주소인 1.1.34.4가 피어로 등록된다.

[예제 2-32] 1.1.34.4가 피어로 등록

``` bash
R2# show crypto isakmp peers
Peer: 1.1.34.4 Port: 500 Local: 1.1.32.2
Phase1 id: 1.1.34.4
```

다음과 같이 `show crypto isakmp sa` 명령어를 사용하여 확인해 보면 상태가 `QM_IDLE`로 표시되면 ISAKMP가 정상적으로 동작하여 피어가 맺어졌음을 의미한다. 피어 중에서 세션을 먼저 시작하는 측이 출발지(src) 주소로 표시되고 상대가 목적지(dst) 주소로 표시된다.

[예제 2-33] ISKAMP SA 확인

``` bash
R2# show crypto isakmp sa
IPv4 Crypto ISAKMP SA
dst          src            state        conn-id     slot          status
1.1.34.4     1.1.23.2       QM_IDLE         1005        0          ACTIVE
```

다음과 같이 `show crypto ipsec sa` 명령어를 사용하여 확인해 보면 보호대상 트래픽 별로 ESP 포맷으로 인캡슐레이션되고, 암호화되며, 무결성 확인을 위해 해시 코드를 추가하여 전송한 패킷의 수가 표시된다. 반대로 상대측에서 수신하여 디캡슐레이션, 복호화 및 무결성 확인과정을 거친 패킷 수도 표시된다.


[예제 2-34] IPsec SA 확인

```bash
R2# show crypto ipsec sa


```

다음과 같이 show crypto session detail 명령어를 사용하여 확인해보면 각 SA 별 암호화 및 복호화된 패킷 수를 알 수 있다.
``` bash
R2# show crypto session detail
```

다음과 같이 show crypto engine connection active 명령어를 사용하여 확인하면 앞서 show crypto ipsec sa 명령어로 확인할 수 있는 각 접속 ID별 암호화 및 복호화된 패킷 수를 간단히 알 수 있다.


[예제 2-36] 크립토 접속 확인

``` bash
R2# show crypto engine connections active
```

다음과 같이 show ip access-lists 명령어를 사용하여 ACL의 적용 통계를 보면 R4가 R2로 전송한 ISAKMP 패킷 (UDP 포트 500)의 수와 ESP 패킷의 수를 확인할 수 있다.


[예제 2-37] ACL 통계 확인

``` bash
R2# show ip access-lists
```

다음과 같이 R5에서 R2로 핑을 하여 IPsec이 동작하도록 한다.


[예제 2-38] 핑 테스트

```bash
R5# ping 10.1.10.1 source 10.1.50.5
```

다시, 다음과 같이 `show ip access-lists` 명령어를 사용하여 ACL의 적용 통계를 보면 R5가 R2로 전송한 ISAKMP 패킷 (UDP 포트 500)의 수와 ESP 패킷의 수를 확인할 수 있다.


[예제 2-39] ACL 확인

```bash
R2# show ip access-lists
```

다음과 같이 R2에서 다른 라우터의 내부망이 아닌 인터넷으로 핑을 해보면 실패한다.


[예제 2-40] 핑 테스트

```bash
P2# ping 1.1.23.3
```

그 이유는 1.1.23.2에서 1.1.23.3으로 전송되는 패킷은 IPsec으로 보호해야 하는 패킷이 아니므로 그냥 전송된다. 이 패킷이 다시 내부로 돌아올 때도 ESP로 인캡슐레이션된 것이 아니므로 ACL-INBOUND에 의해서 차단된다. 다음과 같이 R2의 ACL에서 목적지 IP 주소가 1.1.23.2인 ICMP 패킷을 허용해 보자.

[예제 2-41] ICMP 패킷 허용

```bash
R2(config)# ip access-list extended ACL-INBOUND
R2(config-ext-nacl)# permit icmp any host 1.1.23.2
R2(config-ext-nacl)# exit

```

이제, R2에서 1.1.23.3으로 핑이 된다.



[예제 2-42] 핑 테스트

``` bash
R2# ping 1.1.23.3
```

ISAKMP SA와 IPsec SA를 모두 지우려면 다음과 같이 `clear crypto session remote` 명령어를 사용한다. 그려면, IPsec VPN 접속이 끊긴다.

[예제 2-43] IPsec 세션 끊기

``` bash
R2# clear crypto session remote 1.1.34.4
```

잠시후 `show crypto isakmp sa` 명령어를 사용하여 확인해 보면 다음과 같이 피어 주소 1.1.34.4에 대한 ISAKMP SA가 존재하지 않는다.


[예제 2-44] 1.1.34.4에 대한 ISAKMP SA가 없다.

```bash
$R2# show crypto isakmp sa
```

ISAKMP가 동작하는 것을 실시간으로 확인하려면 다음과 같이 debug crypto isakmp 명령어를 사용한다.


[예제 2-45] ISAKMP 디버깅

``` bash
R2# debug crypto isakmp
```

다음과 같이 R1에서 R4의 내부망으로 핑을 때려 IPsec으로 보호해야 하는 트래픽을 발생시킨다.


[예제 2-46] IPsec 트래픽 발생

```bash
R1# ping 10.1.40.4
```

R2에서의 ISAKMP 동작 디버깅 결과를 다음과 같이 일부만 표시하였다.


[예제 2-47] ISAKMP 동작 디버깅

``` bash
R2#
ISAKMP:(0):found peer pre-shared key matching 1.1.34.4 (1)

ISAKMP:(0): beginning Main Mode exchange (2)

ISAKMP:(1008):beginning Quick Mode exchange, M-ID of -1458007633 (3)


OMPLETE
```

1. 미리 설정된 인증용 암호 (PSK)로 피어를 인증한다.
2. IKE 제1단계에서 사용할 보안정책을 협상하여 결정한다.
3. IKE 제2단계에서 사용할 보안정책을 협상하여 결정한다.

IPsec 동작과정을 확인하려면 다음과 같이 `debug crypto ipsec` 명령어를 사용한다.


[예제 2-48] IPsec 디버깅

```bash
R2# clear crypto session remote 1.1.34.4
R2# un all
R2# debug crypto ipsec
```

R1에서 R4의 내부망인 10.1.40.4로 핑을 때려 IPsec으로 보호해야 하는 트래픽을 발생시킨다. 이후, R2에서의 IPsec 동작 디버깅 결과를 다음과 같이 일부만 표시하면 다음과 같다. IPsec SA는 입력 및 루력 방향에 따라 별개로 생성된다.

[예제 2-49] IPsec 동작 디버깅 결과

```bash

R2#
IPSEC(sa_request): ,
	(key eng. msg.) OUTBOUND local=1.1.23.2, remote=1.1.34.4,
	local_proxy=10.1.12.0/255.255.255.0/0/0 (type=4)
	remote_proxy=10.1.40.0/255.255.255.0/0/0 (type=4)
	protocol=ESP, transform= esp-aes esp-md5-hmac (Tunnel),
	lifedur=3600s and 4608000kb,
	spi=0x0(0), conn_id=0, keysize=128,flags=0x0

IPSEC(create_sa):sa created,
(sa) sa_dest=1.1.23.2 sa_proto=50,
sa_spi=0x62223D9(102900697),
sa_trans=esp-aes esp-md5-hmac, sa_conn_id=39
```

이상으로 기본적인 IPsec L2L VPN을 설정하고, 동작하는 방식으로 살펴보았다.