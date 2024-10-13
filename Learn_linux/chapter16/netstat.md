

# -네트워크 설정 및 통계 정보 확인하기

netstat 프로그램은 다양한 네트워크 설정 사항이나 통계 정보를 확인하는데 사용된다. 많은 옵션들을 사용하여 네트워크 설정 환경에서 다양한 기능을 살펴볼 수 있다.


``` shell
┌──(kali㉿kali)-[~]
└─$  netstat -ie
Kernel Interface table
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.2.15  netmask 255.255.255.0  broadcast 10.0.2.255
        inet6 fe80::221f:bb93:b02:648  prefixlen 64  scopeid 0x20<link>
        ether 08:00:27:21:b1:d0  txqueuelen 1000  (Ethernet)
        RX packets 47149  bytes 71216132 (67.9 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 5077  bytes 317526 (310.0 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 4  bytes 240 (240.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 4  bytes 240 (240.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0


```

이 예제에서는 테스트 시스템이 두 네트워크 인터페이스가 있음을 알 수 있다. 첫 번째 인터페이스는 **==eth0==** 라는 것인데, 이는 ==**이더넷 인터페이스**==를 말하고, 두 번째 **==lo==** 는 ==**루프백 인터페이스**==로 시스템 **"자체"** 에서 네트워크 상태를 테스트할 때 사용하는 가상 인터페이스이다.

일반적인 네트워크 진단 작업을 수행할 때, 중요한 것은 각 인터페이스 정보 중 4번째 줄의 시작인 **UP** 이라는 단어의 존재여부다. ==**이것은 네트워크 인터페이스가 현재 활성화된 상태를 나타낸다.**== 또 한 두 번째 줄의 inet addr 필드에 유효한 IP 주소가 있는지도 확인한다. 동적 호스트 설정 프로토콜(DHCP)을 사용하는 시스템에서 유효한 IP 주소를 사용하여 DHCP가 잘 작동하는지 확인할 수 있다.


**-r** 옵션은 커널의 네트워크 라우팅 테이블 정보를 보여준다. 이 정보를 통해 네트워크 간에 패킷을 송신하기 위해 네트워크가 어떤 식으로 설정되었는지 알 수 있다.


``` shell
┌──(kali㉿kali)-[~]
└─$ netstat -r 
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
default         10.0.2.2        0.0.0.0         UG        0 0          0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U         0 0          0 eth0

```

이 간단한 예제는 방화벽과 라우터가 있는 근거리 통신망(LAN)에 연결되어 있는 클라이언트 장치의 전형적인 라우팅 테이블에 관한 정보를 보여준다. 

첫 번째 줄에 있는 정보는 목적지 IP 주소가 default 이다. ==**만약 주소에서 0으로 끝나면 이는 개별 호스트 주소가 아닌 네트워크 주소를 의미하는 것이다.**==

Gateway 필드는 현재 호스트에서 목적지 네트워크까지 가는 데 사용되는 게이트웨이(라우터)의 IP 주소나 이름을 담고 있다. Gateway 필드에 별표가 있는 경우는 여기서 게이트웨이가 필요하지 않다는 것을 의미한다.

마지막 줄은 기본 목적지를 포함하고 있다. 이는 라우팅 테이블에 목록화되지 않은 네트워크를 위해 정해진 경로를 의미한다. 여기 실험에서는 0.0.0.0 주소의 라우터로 정의되어 있고 그 라우터는 목적지 경로에 관한 것을 알고 있을 것이다.

더 많은 옵션 정보는 netstat 명령어의 [[man]] 페이지를 참고해야 한다.

