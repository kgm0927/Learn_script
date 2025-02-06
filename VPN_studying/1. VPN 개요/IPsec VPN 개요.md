
이번 절에서는 IPsec VPN 개요에 대해서 살펴보자. IPsec VPN은 본지사 사이의 보안통신에 가장 많이 사용되는 것으로 주요한 구성요소는 다음과 같다.


- IKE와 ISAKMP
**IKE**(internet key exchange)와 **ISAKMP**(internet security association and key management protocol)는 <mark style="background: #ADCCFFA6;">처음 통신 당사자(피어 peer)를 인증</mark>하고, <mark style="background: #ADCCFFA6;">보안통신에 필요한 보안정책 집합인 SA을 결정</mark>하며,<mark style="background: #ADCCFFA6;"> 보안통신에 여러 가지 키(암호)를 결정하기 위한 프로토콜이다</mark>.


- SA
SA(security association)란 보안통신을 위해 필요한 <mark style="background: #ADCCFFA6;">여러 가지 알고리즘 및 정책의 집합</mark>을 말한다. 예를 들어, ISAKMP 프로토콜을 암호화하기 위하여 AES라는 암호화 방식을 사용하여, 무결성 확인을 위하여 MD5라는 알고리즘을 사용한다면 이와 같은 여러 가지 ==보안정책의 집합을 **ISAKMP SA**==라고 한다.

또, 실제 데이터를 IPsec으로 송수신하기 위하여 3DES, SHA-1 등과 같은 보안정책을 사용한다면 이 보안정책의 집합을 IPsec SA라고 한다. SA를 하나의 코드로 표시한 것을 **SPI**(security parameter index)라고 한다.


- ESP 또는 AH
**ESP**(encalpsulating security payload)와 **AH**(authentication header)는 <mark style="background: #ADCCFFA6;">실제 데이터를 인캡슐레이션하고 보호하기 위한 프로토콜이다.</mark> <mark style="background: #FF5582A6;">ESP는 데이터를 암호화시키고, 무결성 확인 기능이 있는 반면, AH는 암호화 기능이 없어 많이 사용하지 않는다.</mark> 특히 시스코의 장비중에서 방화벽 및 VPN 게이트웨어 역할을 할 수 있는 ASA나 PIX에서는 AH 자체를 지원하지 않는다. 이제, 앞서 설명한 여러가지 프로토콜의 구체적인 내용을 살펴보자.

---

# IKE와 ISAKMP

<mark style="background: #ADCCFFA6;">두 개의 장비가 보안통신을 하기 위해서는 데이터를 암호화하고 무결성을 확인하기 위한 보안 프토토콜이 정해져야 한다.</mark> <mark style="background: #ADCCFFA6;">또, 통신 상대방이 공격자가 이닌지를 확인하는 인증과정도 거쳐야 한다.</mark> 이와 같은 일을 하는 것이 IKE와 ISAKMP이다.


ISAKMP는 이와 같은 일을 하는 절차를 명시한 프로토콜이고, IKE는 ISAKMP가 명시한 각 절차에서 필요한 구체적인 프로토콜의 종류와 사용방법을 정의한다.

그러나, 두 개의 프로토콜이 규정하는 내용도 동일한 것이 많고 관계도 좀 애매하여 IKE와 ISAKMP를 혼용하여 표현하는 경우가 허다하다. 

이와같은 문제들 대문에 현재 주로 사용되고 있는 IKEv1 대신 2005년 12월에 발표되고 2010년 9월에 개정된 **IKEv2** (RFC5996)에서는 IKE와 ISAKMP를 통합하였고, ISAKMP이라는 용어도 사용하지 않는다. 기본적으로 IKEv1을 사용하고 있는 시스코의 IOS에서는 관련 명령어들이 모두 **crypto isakmp**로 시작한다.

본서에서는 IKEv1을 중심으로 설명하기로 한다. IKE는 두 가지 단계로 동작한다. <mark style="background: #BBFABBA6;">IKE 제1단계에서는 현재의 단계에서 사용할 보안정책인 ISAKMP SA를 결정</mark>하고, <mark style="background: #BBFABBA6;">각 보안 알고리즘에서 사용할 임시 키를 생성하며, 상대 장비를 인증한다.</mark>


<mark style="background: #BBFABBA6;">IKE 제2단계에서는 실제 데이터 송수신을 위해서 필요한 보안적인 **IPsec SA**를 결정한다.</mark> 먼저, IKE 제 1단계가 동작하는 절차는 다음과 같다.

1. 통신을 시작하는 <mark style="background: #BBFABBA6;">송신측에서 ISAKMP SA를 제안</mark>한다. ISAKMP SA는 ISAKMP 단계에서 필요한 인증방식, 암호화 알고리즘, 해싱 방식, 디피-헬먼 알고리즘용 키 길이, ISAKMP SA 유효기간등을 의미한다.
2. 이를 수신한 <mark style="background: #BBFABBA6;">수신측 장비는 자신에게 설정된 ISAKMP SA 세트들과 비교하여 일치하는 SA 세트가 있으면 상대가 제안한 것과 동일한 SA를 전송하여 상대의 제안을 선택했다고 알려준다.</mark> 이렇게 두 개의 패킷을 주고 받아 ISAKMP SA가 결정된다.
3. 다음에는 **디피-헬먼 알고리즘**을 <mark style="background: #BBFABBA6;">사용하여 양측에서 이후 과정부터 암호화 및 해싱에 사용할 공통된 키(암호)를 생성한다.</mark> 이를 위하여 R1이 자신의 공개키와 R1이 임시로 만든 수(이를 논스 **nonce**라고 한다)를 R2에게 전송한다. 이때, R1는 자신의 공개키와 개인키를 사용하여 논스를 만든다.
4. R2도 자신의 공개키와 R2의 논스를 R1에게 전송한다. <mark style="background: #BBFABBA6;">이후 R1, R2는 자신의 개인키, 상대에게서 수신한 공개키 및 논스를 이용하여 양측에서 동일한 대칭키를 계산해내고 이것을 패킷의 암호화 및 해시코드 계산용 키로 사용한다.</mark>

결과적으로<mark style="background: #ADCCFFA6;"> 디피-헬먼 알고리즘을 이용하여 보안성이 없는 네트워크로 연결된 R1, R2가 자신들끼리만 아는 대칭키를 만든다.</mark> 이처럼 디피-헬먼 알고리즘 자체는 공개키/개인키라는 비대칭 키를 사용하지만 이를 이용하여 생성한 키는 양측에서 동일한 대칭키이다. <mark style="background: #ADCCFFA6;">이 대칭키는 ISAKMP 세션과 IPsec이 종료될 때까지 사용된다.</mark> 그러나, ==**PFS**(perfect forward secracy) 기능을 사용하면 현재 생성된 키는 ISAKMP 단계에서만 사용하고, IPsec 단계에서 사용할 대칭키를 다시 사용한다.==


[그림 1-1] *IKE 제1단계 절차 (메인 모드)*
![[기본 폴더 - 1.jpg]]

![깃허브 버전 이미지](https://github.com/kgm0927/Learn_script/issues/1#issue-2834757074)

5. 지금부터는 1.과 2.단계에서 결정된 암호화/해싱 알고리즘과 3.과 4. 단계에서 계산한 키를 사용하여 패킷을 암호화시키고, 무결성 확인용 해시코드를 만들고 첨부한다. 이후부터의 패킷은 제3자가 해독하거나 변조할 수 없다. <mark style="background: #ADCCFFA6;">이번 단계에서는 두 장비가 서로 인증하는 절차를 진행한다.</mark> R1자신의 ID와 인증정보를 R2에게 암호화하여 전송한다. 사전에 양측에 설정된 암호를 사용하는 <mark style="background: #ADCCFFA6;">PSK(pre-shared key) 방식으로 인증하는 경우에는 자신의 ID, 암호를 이용하여 해시코드를 만들고 이를 전송한다.</mark> R2는 R1과 동일하게 사전에 설정된 암호, R1의 ID, 동일한 해싱 알고리즘을 적용하여 해시코드를 계산한다. 이를 R1에게서 수신한 해시코드를 비교하고, 동일한 값이면 인증에 성공한다. PSK가 아닌 디지털 인증서를 사용하도록 설정되어 있다면 인증 서버를 통한 인증 과정을 거친다.
6. <mark style="background: #BBFABBA6;">R2도 자신의 ID와 인증정보를 R1에게 암호화하여 전송한다. R1은 R2와 동일한 해싱 알고리즘을 적용항 해시코드를 계산한다.</mark> 이를 <mark style="background: #BBFABBA6;">R2에게서 수신한 해시코드를 비교하고, 동일한 값이면 인증에 성공한다.</mark> 이렇게 <mark style="background: #ADCCFFA6;">6단계를 거쳐 실제 데이터 보호를 위한 IKE 제2단계 보안정책을 협상한 준비가 완료된다. 이처러머 6단계를 거쳐 IKE 제1단계를 보안정책으로 협상하는 방법을 메인모드(main mode)라고 한다.</mark> 다음과 같이 3개의 패킷 교환으로 IKE 1단계 협상을 끝내는 것을 어그레시브(aggressive)모드라고 한다.

[그림 1-2] IKE 제1단계 절차(어그레시브 모드)

![[기본 폴더 - 2.jpg]]


<mark style="background: #BBFABBA6;">어그레시브 모드에서는 첫 번째 패킷에 ISAKMP SA 제안, 디피-헬먼 키 계산에 필요한 정보 및 ID까지 담아 보낸다.</mark> <mark style="background: #BBFABBA6;">두 번째 패킷에는 응답 장비인 R2에서 선택된 ISAKMP SA, 디피-헬먼 키 계산에 필요한 정보와 함께 R1을 인증하는 메시지가 포함된다.</mark> 마지막으로 R1이 인증 메시지를 전송하여 IKE 1단계 협상이 종료된다.

이처럼 <mark style="background: #ADCCFFA6;">어그레시브 모드를 사용하면 적은 수의 패킷 교환으로 협상이 빠른 시간내에 종료되지만 ID가 공격자에게 노출된다.</mark> 따라서, 대부분의 경우 IKE 제1단계 협상과정에서 어그레시브 모드 대신 메인 모드를 사용한다.

IKE 1단계 협상이 종료되면 <mark style="background: #ADCCFFA6;">실제 데이터를 보호하기 위해 보안 정책인 IPsec SA를 협상하는 IKE 제2단계 협상이 시작된다.</mark> IKE 제2단계 보안정책 협상은 다음과 같은 과정으로 이루어진다.


[그림 1-3] *IKE 제2단계 보안정책 협상*

![[기본 폴더 - 3.jpg]]

1. R1이 실제 데이터를 보호하기 위한 보안정책 즉, <mark style="background: #BBFABBA6;">IPsec SA를 제안</mark>한다. 여기에는 암호화/해싱 알고리즘, 보호대상 네트워크 정보, VPN 동작 모드(터널 모드 또는 트랜스 포트 모드), 인캡슐레이션 방식(ESP또는 AH) 등이 포함된다. 앞서의 단계에서 살펴본 ISAKMP 패킷을 보호하기 위한 보안정책과 실제 데이터를 보호하기 위한 IPsec 보안정책은 별개이다. 또, VPN 동작 모드와 인캡슐레이션 방시에 대해서는 나중에 자세히 설명한다.
2. <mark style="background: #BBFABBA6;">이를 수신한 R2는 자신이 지원가능한 IPsec SA 세트를 선택하여 R1에게 알려준다.</mark>
3. R1은 R2가 동의한 IPsec SA에 대해서 수신확인 메시지를 보낸다.

이처럼 3단계를 거쳐 IPsec 보안정책을 협상하는 것을 퀵 모드(quick mode)라고 한다. 이렇게 총 9단계를 거쳐 ISAKMP세션 즉, IKE 세션이 완료된다.

ISAKMP 세션에서 송수신되는 패킷들을 모두 UDP 포트번호 500을 사용한다. 따라서, IPsec VPN 장비 사이에 방화벽을 사용하고 있다면 UDP 포트 500을 허용하도록 설정해야 한다.


---
# 보안정책을 적용한 실제 데이터 송수신

이제부터는 앞서 IKE 제2단계에서 협상된 보안정책을 적용한 실제 데이터가 송수신된다. <mark style="background: #ADCCFFA6;">이 단계에서 송수신되는 패킷을 인캡슐레이션하는 방식으로 ESP와 AH가 있다.</mark>

**ESP**를 사용하면 전송되는 모든 패킷이 암호화되고(**encrypt**), 변조방지 및 인증을 위하여 해시코드가 첨부된다(**digest**). 이 패킷을 수신하는 장비는 거꾸로 모든 패킷의 해시를 검사하고(**verify**), 패킷을 복호화한다(**decrypt**). <mark style="background: #ADCCFFA6;">AH는 암호화 기능은 없으며 변조방지 및 인증을 위한 해시코드만 첨부된다.</mark>

==**ESP**는 IP 헤더의 프로토콜 번호 50==을 사용하며, ==**AH**는 51==을 사용한다. 따라서, IPsec VPN 장비 사이에 방화벽을 사용하고 있다면 인캡슐레이션 방식에 따라 IP 프로토콜 **50이나 51을 허용**해야 한다.

다음 그림은 ESP 인캡슐레이션을 사용하는 IPsec VPN이 설정된 두 장비 사이의 초기 트래픽을 패킷 분석 프로그램인 와이어샤크(Wireshark)로 캡쳐한 것이다.


 [그림 1-4] ISAKMP와 IPsec 패킷

![[기본 폴더 - 4.jpg]]

<mark style="background: #BBFABBA6;">처음 IKE 제1단계에서 6개의 ISAKMP 패킷이 메인보드로 교환되고,</mark> <mark style="background: #BBFABBA6;">다시 IKE 제2 단계에서는 3개의 패킷이 퀵모드로 송수신된다.</mark> 이후, 실제 데이터들은 ESP로 인캡슐레이션되어 전송되는 것을 확인할 수 있다.


---
# ESP와 AH

암호화 및 무결성 확인을 위한 보안처리 과정을 거친후 데이터를 포장하여 전송하는데 이때 포장방식 즉, <mark style="background: #ADCCFFA6;">인캡슐레이션 방식은 크기 ESP(encapsulating security payload)와 AH(authentication header)라는 두 가지가 있다.</mark> IPsec VPN에서 주로 사용하는 ESP의 포맷은 다음과 같다.


- **SPI**(security parameter index): <mark style="background: #ADCCFFA6;">목적지 IP 주소와 조합하여 현재 패킷의 SA 값을 표시하며 32비트다.</mark> **SA**(security association)에는 <mark style="background: #ADCCFFA6;">암호화/무결성 확인 알고리즘 종류, 보호대상 네트워크 등의 정보가 포함</mark>되어 있다.
- **순서번호**(sequence number): 각 패킷마다 1씩 증가하는 순서번호이고, 패킷의 재생방지를 위하여 사용되며 32비트이다. RFC4303에서는 64비트의 순서번호도 사용할 수 있도록 규정하고 있다. **SPI와 순서번호는 암호화시키지 않는다.**
- 데이터: 보호 대상이 되는 원래의 IP 패킷이 위치한다.

[그림 1-5] *ESP 패킷 포맷*


![[기본 폴더 - 5 1.jpg]]

- **패딩**(padding): 사용된 암호화 알고리즘에 따라 필요한 크기를 맞추기 위하여 추가하는 <mark style="background: #FF5582A6;">의미없는 데이터이다.</mark>
- **패딩 길이**(pad length): 패딩의 길이를 바이트 단위로 표시하며 **8비트**이다.
- **넥스트 헤더**(next header): ESP 헤더 바로 다음의 프로토콜 종류를 표시한다. IP 헤더에서 사용하는 프로토콜 필드의 값을 빌려 표시하며 8비트이다. <mark style="background: #ADCCFFA6;">ESP의 필드 중에서 패딩, 패딩 길이 및 넥스트 헤더 필드를 합쳐 ESP  트레일러(trailer)라고 한다.</mark>
- **ICV**(integrity check value): <mark style="background: #ADCCFFA6;">데이터의 변조 확인을 위하여 추가하는 해시(hash) 코드 값</mark>이며, 기본적으로는 **가변적인 길이를 가진다.** 패킷 인증 및 무결성 확인을 위하여 사용하는 알고리즘에 따라 길이가 달라진다.
**HMAC MD5-96** 알고리즘을 사용하면 128비트의 해시코드가 생성된다. 그러나, <mark style="background: #ADCCFFA6;">ESP나 AH에서는 이중에서 96비트만 잘라서 사용한다.</mark> 수신측에서는 동일한 해시코드를 계산한 다음 앞부분 96비트만 비교한다. 또, **HMAC-SHA-1-96**은 160 비트의 해시코드를 만들지만,<mark style="background: #ADCCFFA6;"> ESP나 AH에서는 앞부분의 96비트만 잘라서 사용한다.</mark>


AH의 패킷 포맷은 다음과 같다.

- **넥스트 헤더**(nextheader): **AH 헤더 바로 다음의 프로토콜 종류를 표시**한다. IP 헤더에서 사용하는 프로토콜 필드의 값을 빌려 표시하며 8비트이다.
- **길이**: AH의 헤더 길이를 워드 단위로 표시하며 8비트이다.
- **SPI**(security parameter index): 목적지 IP 주소와 조합하여 현재 패킷의 SA 값을 표시하며 32비트이다.
- **순서번호**(sequence number): 처음 1부터 시작하여 각 패킷마다 1씩 증가하는 순서 번호이고, 패킷의 재생방지를 위하여 사용되며 32비트이다. 송신측에서 반드시 사용해야 하며, 수신측에서는 재생방지 기능이 동작할 때 이 필드를 사용한다.

[그림 1-6] *AH 패킷 포맷*

![[기본 폴더 - 6.jpg]]


$2^{32}$ 개의 패킷을 전송한 다음에는 새로운 SA를 협상해야 한다. 또, RFC4302에서는 64비트의 순서번호도 사용할 수 있도록 규정하고 있다.

- **ICV**(integrity check value): <mark style="background: #ADCCFFA6;">데이터의 변조 확인을 위하여 추가하는 해시(hash)코드 값이다.</mark> AH 헤더 앞에 위치하는 IP 헤더 중에서 변동되는 값이 TTL, 첵섬, DSCP, ECN 필드 등을 제외한 부분과, AH 헤더 뒤에 위치하는 모든 필드에 대한 해시코드 값을 계산한다.
- **데이터**: 보호 대상이 되는 원래의 IP 패킷 중에서 레이서4 이상의 데이터가 위치한다.



---
# 트랜스포트 모드

IPsec VPN은 원래의 데이터 패킷에 VPN 장비의 세로운 IP 헤더를 추가하는 지의 여부에 따라 **트랜스포트**(transport) 모드와 **터널**(tunnel) 모드로 구분할 수 있다. 일반적으로 다음 그림과 같이 <mark style="background: #ADCCFFA6;">종단 장비 사이에 직접 IPsec VPN을 이용한 통신이 이루어지는 경우에는 트랜스포트 모드를 사용한다.</mark> 그러나 <mark style="background: #FF5582A6;">이 경우에도 터널 모드를 사용할 수도 있다.</mark>

[그림 1-7] *IPsec VPN 트랜스포트 모드 동작 범위*

![[기본 폴더 - 7.jpg]]


인캡슐레이션 방식을 ESP로 사용하는 IPsec VPN인 경우, 다음 그림과 같이 IP 헤더 다음에 8바이트의 ESP 헤더가 온다. ESP 헤더 다음에는 원래의 데이터가 위치하고, 그 다음에는 **ESP 트레일러**와 패킷 무결성 확인을 위한 해시코드인 **ICV**가 첨부된다.


[그림 1-8] *IPsec VPN 트랜스포트 모드(ESP) 패킷*

![[기본 폴더 - 8.jpg]]




트랜스포트 모드로 동작하는 ESP 패킷은 앞의 그림과 같이 데이터와 ESP 트레일러 까지를 암호화하여 보호하고, ESP 헤더부터 ESP 트레일러까지의 정보를 이용하여 무결성 확인용 해시코드를 만들고 패킷 뒤에 첨부한다. <mark style="background: #FF5582A6;">결과적으로 트랜스포트 모드를 사용하면 레이어 4 정보부터 ESP로 보호한다.</mark>

인캡슐레이션 방식을 AH로 사용하는 IPsec VPN인 경우, 다음 그림과 같이 IP 헤더 다음에 AH 헤더가 온다. <mark style="background: #ADCCFFA6;">트랜스포트 모드에서 AH는 IP 헤더를 포함한 전체 패킷에 대해서 인증 및 무결성 확인 기능을 적용한다.</mark>

[그림 1-9] *IPsec VPN 트랜포트 모드(AH) 패킷*

![[기본 폴더 - 9.jpg]]


<mark style="background: #ADCCFFA6;">이때 IP 헤더중에서 값이 변동되는 필드인 TTL, DSCP, ECN, 첵섬 등은 해시코드 계산에서 제외한다. </mark>그 이유는 AH 패킷이 전송 도중에 이와같은 필드 값들이 변동될 수 있고, 결과적으로 해시코드 값이 달라지기 때문이다. 결과적으로 AH 트랜스포트 모드를 사용하면 레이어 3 정보부터 인증된다.

트랜스포트 모드를 사용하면 20바이트 길이의 추가적인 IP 헤더를 사용하지 않는 반면, 보안 통신을 하는 두 장비의 IP 주소가 외부에 노출된다.


---
# 터널 모드

다음 그림과 같이 <mark style="background: #ADCCFFA6;">종단 장비들이 전송경로 사이에 있는 IPsec VPN 게이트웨이를 이용하여 통신을 하는 경우에는 터널 모드를 사용하는 것이 일반적이다.</mark> 이때, IPsecVPN 관련 처리는 VPN 게이트웨이에서 이루어지고, <mark style="background: #FF5582A6;">종단 장비들은 VPN에 존재를 인식하지 못한다.</mark>

<mark style="background: #FFF3A3A6;">순수 IPsec VPN이 아닌 GRE IPsec이나 DMVPN의 경우에는 어차피 원래의 패킷 앞에 GRE 헤더가 첨부되고, 이것이 암호화되므로 종단 장비의 L3 헤더가 노출되지 않는다.</mark> 따라서, 이 경우에 <mark style="background: #ADCCFFA6;">터널 모드 대신 트랜스포트 모드를 사용하면 20바이트 오버헤더를 줄일 수 있다.</mark>


[그림 1-10] *IPsec VPN 터널 모드 동작 범위*



![[기본 폴더 - 10.jpg]]




앞 그림과 같이 서버나 PC와 같은 종단 장비들이 인접한 VPN 게이트웨이 사이에는 평문으로 이루어지며, <mark style="background: #ADCCFFA6;">VPN 게이트웨이 사이에는 IPsec를 시용하여 송수신 데이터를 보호한다.</mark>

<mark style="background: #BBFABBA6;">터널 모드로 동작하는 ESP 패킷은 다음 그림과 원래의 패킷 앞에 VPN 게이트웨이의 주소를 사용한 IP 헤더를 새로 추가한다.</mark> 그리고, <mark style="background: #BBFABBA6;">원래 패킷 전체와 ESP 트레일러까지를 암호화하여 보호하고, ESP 헤더부터 ESP 트레일러까지의 정보를 이용하여 무결성 확인용 해시코드를 만들어 패킷 뒤에 첨부한다.</mark>

순수 IPsec VPN에서 터널 모드를 사용하면 <mark style="background: #ADCCFFA6;">20바이트 길이의 새로운 IP 헤더가 추가</mark>되고 원래의 패킷은 노출되지 않는다. 결과적으로 터널 모드는 **레이어 3 정보부터 보호**된다.


[그림 1-11] *IPsec VPN 터널 모드(ESP) 패킷*

![[기본 폴더 - 11.jpg]]

터널 모드로 동작하는 <mark style="background: #BBFABBA6;">AH 패킷은 다음 그림과 같이 원래의 패킷 앞에 VPN 게이트웨이의 주소를 사용한 IP 헤더를 새로 추가한다.</mark> 터널 모드에서 AH는 새로운 IP 헤더를 포함한 전체 패킷에 대해서 인증 및 무결성 확인 기능을 적용한다.



[그림 1-12] *IPsec VPN 터널 모드(AH) 패킷*

![[기본 폴더 - 12.jpg]]

이때 새로운 IP 헤더 중에서 값이 변동되는 필드인 TTL, DSCP, ECN, 첵섬 등은 해시코드 계산에서 제외한다. 그러나, <mark style="background: #BBFABBA6;">원래의 IP 헤더는 전송도중 TTL 등이 변경되지 않으므로 해시코드 계산시 모두 포함된다.
</mark>

---
# VPN과 패킷 분할

<mark style="background: #BBFABBA6;">IPsec 패킷들은 사용하는 기술에 따라 추가적으로 헤더의 길이가 늘어난다.</mark> 예를 들어, DMVPN을 사용하는 경우 80 바이트 이상의 오버헤더가 추가된다. 따라서, FTP 등과 같이 이더넷의 MTU인 1500 바이트 크기의 패킷 전송이 많은 경우, 패킷 분할이 필요하다.

IOS 버전의 12.1(11)E, 12.2(13)T 이후인 경우에 **LAF**(look ahead fragementation)라는 기능이 자동으로 모든 물리적인 인터페이스에 활성화되어 있다. <mark style="background: #BBFABBA6;">IPsec이 동작하는 라우터가 출력 인터페이스의 MTU와 IPsec 패킷에 추가될 헤더를 계산하여 암호화 작업 전에 미리 분할을 한다.</mark> <mark style="background: #BBFABBA6;">수신측 IPsec 장비는 각 분할된 패킷을 복호화시키고 종단 장비로 전송한다. 종단 장비가 최종적으로 분할된 패킷을 조립한다.</mark>

그러나, 이와 같은 기능이 지원되지 않은 환경에서는 가능하다면 <mark style="background: #ADCCFFA6;">종단장비인 서버와 PC의 MTU를 조정한다.</mark> 시스코에서 권고하는 최적 MTU 값은 1300 바이트이다. 그러나, PC가 많다면 일일이 조정하기가 힘들다. 차선책으로 서버의 MTU만 조정하는 것도 도움이 된다.

종단 장비간에 최소 MTU를 찾아내는 **PMTUD**(path MTU discovery)<mark style="background: #ADCCFFA6;">기능을 사용하면 종단 장비가ICMP 패킷을 이용하여 가장 작은 MTU 값을 찾아내어 자신의 MTU 값을 여기에 맞춘다.</mark> 그러나, **중간에 방화벽이 있는 경우** <mark style="background: #FF5582A6;">해당 패킷을 차단하면 PMTUD가 제대로 동작하지 않는다.
</mark>

이 경우에 VPN 인터페이스의 MTU 값을 1300 바이트로 조정하면 된다. GRE 터널을 사용하는 경우,터널 인터페이스의 MTU 값을 조정해야 한다. 그러나, 터널 인터페이스의 기본 MTU 값이 1514이고, 이 값은 조정되지 않는다. 따라서 **ip mtu 1300** 명령어를 사용하여 조정해야 한다.


---
# ESP 패킷 처리순서

<mark style="background: #ADCCFFA6;">라우터가 패킷을 VPN 터널로 전송하려면 해당 패킷의 목적지가 라우팅 테이블에 존재해야 한다.</mark> 이후, ESP가 상대에게 패킷을 전송하기 전에 다음과 같은 과정을 거친다.

1. SA 확인

패킷에 적용할 암호화 알고리즘 등을 결정하기 위하여 해당 **SA를 확인**한다.

2. 패킷 암호화

앞서 확인한 **SA에 따라 패킷을 암호화한다.**

3. 순서 번호 생성

순서 번호를 1부터 시작하여 1씩 증가시켜 나간다. <mark style="background: #ADCCFFA6;">전송패킷 수가 계속 증가하여 최대치에 도달시 재생방지(anti-replay) 기능을 사용하면 다시 SA를 만들고</mark>, <mark style="background: #FF5582A6;">재생방지 기능을 사용하지 않으면 순서번호를 다시 0부터 시작한다.
</mark>
4. ICV 계산

무결성 확인 기능을 사용하는 경우, 이를 위한 **ICV**(integrity check value, 해시코드)를 계산한다.

5. 분할

패킷에 ESP 프로세싱후 분할한다.

라우터가 ESP 패킷을 수신하면 먼저,<mark style="background: #BBFABBA6;"> 인터페이스의 ACL를 참조한다.</mark> 이후, <mark style="background: #BBFABBA6;">상대에게서 수신한 ESP 패킷에 대하여 다음과 같은 과정을 거친 후, IPsec ACL이 설정되어 있다면 이를 적용</mark>한다. 그러나, <mark style="background: #FF5582A6;">오래된 IOS를 사용하는 경우, 인터페이스 ACL 적용->복호화->인터페이스 ACL 적용이라는 과정을 거친다.</mark>


1. 분할 패킷 조립

분할된 패킷을 수신한 경우 조립한다.

2. SA 확인

ESP 헤더의 SPI 값을 참조하여 SA를 찾는다. SA에서 암호화 알고리즘, 재생방지 기능 여부, 무결성 확인 여부 등을 확인한다.

3. 순서번호 확인

재생방지 기능 사용시 중복된 순서번호를 가진 패킷이나 특정 시간대 범위안에 들지 않은 들이 않은 패킷을 폐기한다.

4. 무결성 확인

무결성 확인 기능 사용시 ICV 값을 확인하여 잘못된 값을 가진 패킷은 폐기한다.

5. 패킷 복호화

암호화된 패킷을 복호화한다.


---
# NAT-T

NAT-T(NAT traversal)란 <mark style="background: #ADCCFFA6;">IPsec 장비들 사이에 NAT 장비가 있을 때 자동으로 퀵 모드의 ISAKMP 패킷과 ESP 패킷을 UDP 4500번으로 인캡슐레이션하여 전송하는 것을 말한다.</mark> 이처럼 NAT-T를 사용하는 이유는 <mark style="background: #FF5582A6;">출발지/목적지 포트번호를 500으로 사용하는 ISAKMP가 변환된 번호로 인하여 제대로 동작을 하지 못하거나, NAT/PAT 장비중에서 TCP와 UDP가 아닌 패킷들은 처리하지 못하는 것들이 있어 ESP나 AH 패킷들은 폐기될 수 있기 때문이다.
</mark>

또, <mark style="background: #ADCCFFA6;">IPsec 트랜스포트 모드의 패킷이 NAT 장비를 통과하면 IP 주소가 변환되고, IP 주소가 원래 해시 코드를 계산했을 때와 달라진다.
</mark>

[그림 1-13] *IP 주소 변경 시 해시 코드 값이 달라진다*

![[기본 폴더 - 13.jpg]]

결과적으로 수신장비에 계산된 해시코드 값이 달라져서 IPsec이 동작하지 않는다. 이와 같은 문제들을 해결하기 위하여 NAT-T를 사용한다. <mark style="background: #ADCCFFA6;">만약 IPsec 장비 사이에 NAT 장비가 존재하면 IPSec 장비들은 트래픽을 전송할 때 다음 그림과 같이 IPsec 패킷을 UDP 세그먼트 내부에 인캡슐레이션시켜서 전송한다.</mark> 이 때 UDP 포트번호 4500을 사용한다.

[그림 1-14] *UDP 포트번호 4500을 사용한 인캡슐레이션*

![[기본 폴더 - 14.jpg]]

IPSec 장비 사이에 NAT 장비가 존재하는 것을 확인하기 위하여 IKE 제1단계에서 서로에게 해싱된 데이터를 전송한다. 해싱 정보가 달라지면 NAT 장비가 존재함을 의미한다. 그러면 자동으로 IKE 제2단계에서 데이터 전송시 NAT-T 기능을 사용한다.

이상으로 VPN의 개요에 대하여 살펴보았다. 이제, 다음 장에서 다양한 종류의 VPN을 설정해보고 동작하는 것을 확인해보도록 한다.

