리눅스에서 HSRP와 유사한 기능을 제공하려면 **VRRP (Virtual Router Redundancy Protocol)**를 사용할 수 있습니다. VRRP는 HSRP와 기능적으로 유사하며, 네트워크 인터페이스 고가용성을 제공합니다. 리눅스에서는 VRRP를 구현하기 위해 ==Keepalived==**와 같은 소프트웨어를 사용할 수 있습니다.

다음은 HSRP 구성을 Keepalived로 재현한 예제와 함께 대조 분석입니다.


---
### Keepalived 설정 예제

**Keepalived 설정 파일** (`/etc/keepalived/keepalived.conf`):

```
vrrp_instance GROUP1 { 

state MASTER 
interface eth0.100 
virtual_router_id 1 
priority 105 
advert_int 1 

virtual_ipaddress { 1.1.100.10 } 

track_interface { eth0.12 } 

preempt_delay 0 
}
```


---
### HSRP와 Keepalived 설정 대조 분석

| HSRP Cisco 명령             | Keepalived 설정 필드                   | 설명                                                                |
| ------------------------- | ---------------------------------- | ----------------------------------------------------------------- |
| `interface f0/0.100`      | `interface eth0.100`               | HSRP 서브인터페이스와 VRRP에서 사용하는 VLAN 인터페이스. Keepalived에서는 실제 인터페이스를 지정. |
| `standby 1 ip 1.1.100.10` | `virtual_ipaddress { 1.1.100.10 }` | HSRP 가상 IP와 VRRP 가상 IP를 설정. 동일한 목적을 가짐.                           |
| `standby 1 priority 105`  | `priority 105`                     | HSRP와 VRRP 모두 라우터/서버의 우선순위를 설정. 높은 값일수록 우선순위가 높음.                 |
| `standby 1 preempt`       | 기본 활성화 (`state MASTER`)            | VRRP에서는 기본적으로 preempt가 활성화됨. 추가적으로 `preempt_delay`를 설정할 수 있음.     |
| `standby 1 track f0/0.12` | `track_interface { eth0.12 }`      | 특정 인터페이스의 상태를 추적하여 다운 시 우선순위를 낮춤. HSRP와 VRRP 모두 고가용성을 위해 중요한 요소.  |
| `standby 1 name GROUP1`   | `vrrp_instance GROUP1`             | HSRP와 VRRP에서 그룹 이름을 정의하여 설정을 구분.                                  |


---

### 설정 파일의 주요 필드 설명


- **`state`**
    
    - `MASTER`: 기본 활성 노드로 설정합니다.
    - 다른 노드는 `BACKUP` 상태로 설정됩니다.
- **`interface`**
    
    - 가상 IP를 제공할 인터페이스를 지정합니다. HSRP의 `interface`와 동일합니다.
- **`virtual_router_id`**
    
    - VRRP 그룹의 고유 ID를 설정합니다. HSRP의 그룹 번호(`standby 1`)에 해당합니다.
- **`priority`**
    
    - HSRP와 동일하게 VRRP에서 우선순위를 설정합니다.
- **`track_interface`**
    
    - HSRP의 `track`과 동일한 기능으로, 지정한 인터페이스의 상태를 감시합니다.
- **`preempt_delay`**
    
    - HSRP의 `preempt`와 비슷한 기능으로, 더 높은 우선순위를 가진 노드가 활성 상태로 전환될 때의 지연 시간을 설정합니다.


---


### 확인 및 서비스 시작

1. **Keepalived 설치**


```bash 
sudo apt install keepalived # Ubuntu/Debian 
sudo yum install keepalived # RHEL/CentOS
```


2. **서비스 시작**
``` bash
sudo systemctl start keepalived
sudo systemctl enable keepalived
```

3. **상태 확인**

``` bash

sudo systemctl status keepalived
```

4. **로그 확인**

``` bash
tail -f /var/log/syslog # Ubuntu/Debian 
tail -f /var/log/messages # RHEL/CentOS
```