

프로세스를 보는 가장 일반적인 명령어는 ps이다. ps 프로그램은 많은 옵션을 가지고 있지만 다음과 같이 아주 간단한 형태로 사용할 수 있다.


``` shell
┌──(kali㉿kali)-[~]

└─$ ps

PID TTY TIME CMD

1323 pts/0 00:00:00 zsh

1687 pts/0 00:00:00 ps
```


여기서 프로세스 1687과 프로세스 1323은 각각 zsh와 ps를 나타낸다. 앞서 볼 수 있듯이 기본적으로 ps는 많은 정보를 보여주지 않는다. 자세하게 볼려면 약간의 옵션을 추가해야 한다.

TTY는 teletype의 약자로 프로세스용 제어 터미널을 나타낸다. TIME 필드는 프로세스의 CPU 사용 시간을 나타낸다. 앞의 결과에서 알 수 있듯이 어느 프로세스도 컴퓨터는 열심히 일하게 만들지는 못했다.


``` shell
┌──(kali㉿kali)-[~]

└─$ ps x

PID TTY STAT TIME COMMAND

790 ? Ss 0:00 /lib/systemd/systemd --user

791 ? S 0:00 (sd-pam)

806 ? S<sl 0:00 /usr/bin/pipewire

807 ? Ssl 0:00 /usr/bin/pipewire -c filter-chain.conf

809 ? S<sl 0:00 /usr/bin/wireplumber

810 ? S<sl 0:00 /usr/bin/pipewire-pulse

811 ? SLsl 0:00 /usr/bin/gnome-keyring-daemon --foreground --com
```


x 옵션을 추가하면(대시 기호는 필요없다.) ps는 그서들이 제어되는 터미널에 상관없이 모든 프로세스를 보여준다. TTY 항목의 ? 표시는 아무런 제어 터미널이 없다는 의미이다. 이 옵션을 사용하면 소유한 모든 프로세스를 보게 된다.

시스템은 많은 프로세스를 실행하기 때문에 ps는 긴 목록을 생성한다. 좀 더 편하게 보기 위해 ps의 결과를 파이프를 통해 **less**에 전달하는 것이 도움이 될 것이다. 터미널 에뮬레이터 창을 최대화 하는 것도 좋은 방법이다.

위의 결과를 보면 출력결과에 **STAT**라는 새 항목이 추가되었다. STAT는 **state**(상태)의 약자로프로세스의 현재 상태를 나타낸다.




[표 10-1]프로세스 상태

| 상태 값 | 의미                                                                                                                                                        |
| ---- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| R    | 실행 상태, 프로세스는 실행 중이거나 실행 대기 중이다.                                                                                                                           |
| S    | 수면 상태. 프로세스는 실행 중이 아니고 키 입력이나 네트워크 패킷과 같은 이벤트는 기다리는 상태                                                                                                    |
| D    | 인터럽트 불가능한 수면 상태. 프로세스는 I/O(입출력)을 기다리는 중이다.(디스크 입출력 등)                                                                                                     |
| T    | 종료 상태. 프로세스는 종료 요청을 받았거나 종료된 상태이다.                                                                                                                        |
| Z    | 현존하지 않거나 “좀비” 프로세스. 이것은 부모 프로세스에 의해 정리되지 않는 종료된 자식 프로세스다.                                                                                                 |
| <    | 높은 우선순위 프로세스. 특정 프로세스에 더 중요성을 부여하는 것이 가능하다. 즉 CPU 시간을 더 줄 수 있다. 프로세스의 이러한 속성을 nicencess라고 한다. 높은 우선순위의 프로세스는 다른 프로세스보다 더 많은 CPU 시간을 갖기 때문에 nice하지 않다고 한다. |
| N    | 낮은 우선순위 프로세스. 낮은 우선순위 프로세스(nice 프로세스)는 더 높은 우선순위 프로세스가 사용한 뒤 시간을 얻을 수 있다.                                                                                 |


이 문자들은 다양한 프로세스의 특징을 나타낸다. 좀 더 자세한 정보는 ps 명령어의 man 페이지를 참조하기 바란다.


또 다른 인기 있는 옵션 조합은 **aux**(대시 기호 없이)다. 이것은 더 많은 정보를 준다.


``` shell
┌──(kali㉿kali)-[~]

└─$ ps aux

USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND

root 1 0.0 0.6 167672 12284 ? Ss 19:54 0:01 /sbin/init

root 2 0.0 0.0 0 0 ? S 19:54 0:00 [kthreadd]

root 3 0.0 0.0 0 0 ? I< 19:54 0:00 [rcu_gp]

root 4 0.0 0.0 0 0 ? I< 19:54 0:00 [rcu_par_g

root 5 0.0 0.0 0 0 ? I< 19:54 0:00 [slub_flus

root 6 0.0 0.0 0 0 ? I< 19:54 0:00 [netns]

root 8 0.0 0.0 0 0 ? I< 19:54 0:00 [kworker/0

root 10 0.0 0.0 0 0 ? I< 19:54 0:00 [mm_percpu

root 11 0.0 0.0 0 0 ? I 19:54 0:00 [rcu_tasks

root 12 0.0 0.0 0 0 ? I 19:54 0:00 [rcu_tasks

root 13 0.0 0.0 0 0 ? I 19:54 0:00 [rcu_tasks

root 14 0.0 0.0 0 0 ? S 19:54 0:00 [ksoftirqd

root 15 0.0 0.0 0 0 ? I 19:54 0:01 [rcu_preem

root 16 0.0 0.0 0 0 ? S 19:54 0:00 [migration

... 그 외 ...
```


이 옵션 조합은 모든 사용자에 속한 프로세스들을 보여준다. 대시 기호 없이 옵션을 사용하는 것은 “BSD 스타일”로 명령을 실행하는 것이다. ps의 리눅스 버전은 여러 유닉스 호환 시스템에 있는 ps 프로그램을 흉내 낼 수 있다. 이 옵션으로 표 10-2와 같이 추가적인 항목을 볼 수 있다.


[표 10-2] BSD 스타일의 ps 헤더들

| 헤더    | 의미                                         |
| ----- | ------------------------------------------ |
| USER  | 사용자 DI, 프로세스 소유자                           |
| %CPU  | CPU 사용량(%)                                 |
| %MEM  | 메모리 사용량(%)                                 |
| VSZ   | 가상 메모리 크기                                  |
| RSS   | 사용 메모리 크기. 프로세스가 사용중인 물리적 메모리(RAM)량을 나타낸다. |
| START | 프로세스가 시작된 시각. 24시간을 넘긴 값은 날짜를 사용한다.        |


- top 명령어로 프로세스 변화 보기

ps 명령어가 시스템 동작에 관한 많은 정보를 주긴 하지만 오직 ps 명령어가 실행된 순간의 상태에 대해서만 제공한다. 시스템의 활동을 좀 더 동적으로 보기 위해서는 top 명령어를 사용하면 된다.

top 프로그램은 프로세스 활동순으로 나열된 시스템 프로세스들을 지속적으로 갱신하여 보여준다(기본적으로 3초마다). top은 두 부분으로 나뉘어 표시한다. 최상위에 시스템 요약과 그 아래에 CPU 활동순으로 정렬된 프로세스 테이블이 표시된다.


``` shell
op

top - 21:21:07 up 1:27, 1 user, load average: 0.29, 0.22, 0.18

Tasks: 152 total, 2 running, 150 sleeping, 0 stopped, 0 zombie

%Cpu(s): 2.4 us, 3.8 sy, 0.0 ni, 93.6 id, 0.2 wa, 0.0 hi, 0.0 si, 0.0 st

MiB Mem : 1967.4 total, 823.8 free, 810.2 used, 493.8 buff/cache

MiB Swap: 1024.0 total, 1024.0 free, 0.0 used. 1157.1 avail Mem

PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND

520 root 20 0 467820 151052 67556 R 6.3 7.5 0:35.18 Xorg

1188 kali 20 0 454484 114132 91968 S 4.0 5.7 0:05.15 qterminal

1009 kali 20 0 1
```


시스템 요약 정보에는 유용한 것들이 많이 포함되어 있다.



[표 10-3] top 정보 필드


| 행   | 항목           | 의미                                                                                                                                |
| --- | ------------ | --------------------------------------------------------------------------------------------------------------------------------- |
| 1   | top          | dl 프로그램의 이름                                                                                                                       |
|     | 21:21:07     | 현재 시각                                                                                                                             |
|     | up 1:27,     | 시스템이 마지막 부팅된 시점부터 지금까지의 시간을 나타낸다. 이를 업타임(uptime)이라 한다.<br><br>이 예제에서 시스템은 부팅한 지 1시간 27분이 지났다.                                     |
|     | 1 user       | 1명의 사용자가 로그인을 했다는 의미이다.                                                                                                           |
|     | load average | ‘평균 부하’는 실행 대기중인 프로세스의 수를 의미한다. 모두 세 가지 값을 보여주는데 각각 시간 주기를 나타낸다. 첫 번째는 최근 60초 동안의 평균값이고, 그 다음은 지난 5분, 그리고 마지막은 지난 15분간의 평균을 나타낸다. |
| 2   | Tasks:       | 프로세스 수와 프로세스 상태별 수를 보여준다.                                                                                                         |
|     | 0.8 us       | CPU의 0.8%를 ‘사용자 프로세스’들이 사용 중이다. 이는 커널 바깥의 프로세스를 의미한다.                                                                             |
|     | 1.7 sy       | CPU의 1.7%를 ‘시스템(커널)’ 프로세스에서 사용 중이다.                                                                                               |
|     | 0.0 ni       | CPU의 0.0%를 nice(우선순위가 낮은) 프로세스가 사용 중이다.                                                                                           |
|     | 97.2 id,     | CPU의 98.3%가 유휴 상태이다.                                                                                                              |
|     | 0.0 wa       | CPU의 0.0%가 I/O를 대기 중이다.                                                                                                           |
| 4   | Mem          | 물리 메모리 사용현황을 보여준다.                                                                                                                |
| 5   | Swap:        | 스왑 영역(가상 메모리) 사용현황을 보여준다.                                                                                                         |


top 프로그램은 수 많은 키보드 명령어를 허용한다. 가장 많이 사용하는 두 가지는 h와 q다. h는 프로그램 도움말 화면을 보여주는 명령어고, q는 top 프로그램을 종료한다.

주요 데스크톱 환경 모두 top 프로그램과 유사한 그래픽 애플리케이션을 제공한다(윈도우즈에서는 작업관리자가 대체로 비슷하다). 하지만 둘 중에 비교를 해 보았을 때 top이 그래픽 버전 프로그램보다 더 낮다. top은 더 빠르고 더 적은 시스템 리소스를 사용한다. (필자의 생각)