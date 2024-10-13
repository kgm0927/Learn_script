

# 원격 호스트와 안전하게 통신하기




수년간, 유닉스형 운영체제는 네트워크를 통해 원격으로 시스템을 관리할 수 있는 기능이 있었다. 인터넷이 사용되기 이전에는 원격 호스트에 접속하기 위해 사용되던 몇 개의 유명한 프로그램이 있었다. ==이러한 프로그램들은 공통적으로 치명적인 문제를 가지고 있는데 그것은 ftp 프로그램처럼 평문으로 통신 정보(사용자 계정을 포함하여)를 송수신한다는 점이다. 이것은 인터넷 시대에 사용하기란 아주 부적절할 수 밖에 없다.==


---
# ssh - 원격 컴퓨터에 안전하게 로그인하기


이러한 문제를 해결하기 위해 SSH(안전한 쉘)라고 하는 새로운 프로토콜이 개발되었다. SSH로 원격 호스트와 통신에 존재하는 보안 문제 중 두 가지 기본적인 문제를 해결할 수 있다. ==**첫째**로는 원격 호스트에 대한 진위성(중간자 공격을 방지)을 입증해준다.== ==**둘째**로는 로컬과 원격 호스트 간의 모든 통신을 암호화한다.==

SSH는 두 부분으로 구성된다. 원격 호스트에서 구동 중인 22번 포트로 연결을 기다리는 SSH 서버와 원격 서버와 통신하기 위해 로컬 시스템에서 사용되는 SSH 클라이언트다.

대부분의 리눅스 배포판에서는 BSD 프로젝트의 OpenSSH가 포함되어 있다. 일부 배포판에서는 기본적으로 클라이언트와 서버 모두 지원(래드햇)되지만 클라이언트만 지원되는 경우(우분투)도 있다. 원격 접속을 받기 위해서는, OpenSSH-server 패키지가 시스템에 설치되어 환경설정 후 실행 중이어야 한다. 그리고 TCP 22번 포트로 들어오는 네트워크 연결을 허용하도록 설정해야 한다.


>[!tip]  저자주
> 지금까지의 작업을 테스트할 원격 시스템이 없지만, 예제를 실행해보고 싶다면 사용자의 시스템에 OpenSSH-server 패키지를 설치하고 원격 호스트로 localhost를 사용하면 된다. 이런 식으로 사용자의 컴퓨터에서 네트워크 접속 상태를 만들 수 있다.


SSH 클라이언트 프로그램은 원격 SSH 서버에 연결할 때 사용되는데 ssh라고 지칭해도 무방하다. localhost를 통해 원격 호스트에 연결하기 위해 다음과 같이 ssh 클라이언트 프로그램을 사용해 보겠다.



우선 ssh의 서비스를 실행하기 위해 sudo 차원에서 service ssh를 먼저 실행할 것이다.

``` shell
┌──(kali㉿kali)-[~]
└─$ sudo service ssh start

```


```  shell
┌──(kali㉿kali)-[~]
└─$ ssh localhost         
kali@localhost's password: 
Linux kali 6.5.0-kali3-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.5.6-1kali1 (2023-10-09) x86_64

The programs included with the Kali GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Kali GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Thu Mar 14 21:33:12 2024 from ::1
┌──(kali㉿kali)-[~]
└─$ 

```

이미 로컬 호스트 연결을 했기 때문에 나오지는 않지만, 사실 첫 번재 연결 시도 후에 원격 호스트를 인증할 수 없다는 메시지가 표시된다. 지금 사용 중인 클라이언트 프로그램이 해당 원격 호스트를 한 번도 연결한 적이 없는 상황에서만 나오기 때문이다. 

이렇게 해서 사용자는 비밀번호를 입력 후 원격 시스템의 쉘 프롬프트를 볼 수 있게 된다.



사용자가 exit 명령어를 원격 쉘 프롬프트에 입력하기 전까지는 원격 쉘 세션은 지속되고, exit 명령어를 입력하게 되면 연결은 종료된다. 그럼 다시 로컬 쉘 세션이 시작되고 쉘 프롬프트가 보이게 된다.




다른 사용자명으로 원격 시스템에 연결하는 것도 가능하다. 예를 들면, me라는 사용자가 bob이라는 이름의 계정을 원격 시스템에 가지고 있다면, 사용자 me은 bob 계정으로 원격 시스템에 로그인할 수 있을 것이다.
``` shell
┌──(kali㉿kali)-[~]
└─$ ssh kali@localhost                                                          
kali@localhost's password: 
Linux kali 6.5.0-kali3-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.5.6-1kali1 (2023-10-09) x86_64

The programs included with the Kali GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Kali GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Thu Mar 14 22:07:49 2024 from ::1

```
(다만 이 방법은 지금 상황에서 유료한지는 모르겠다. kali@localhost라는 계정을 따로 만든 적이 없기에 일다는 실행이 되는 되로 적어 놓도록 하겠다.)




앞서 말한 바와 같이 ssh는 원격 호스트의 진위성을 검증한다. 원격 호스트가 인증되지 않으면 다음과 같은 메시지가 표시될 것이다.

```
@ WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!
```

이러한 메시지는 보통 두 가지 상황 중 하나다. 첫 번째 상황은 중간자 공격이 시도되고 있을지도 모르는 경우이다. 이는 ssh가 사용장게 미리 경고해주기 때문에 흔한 일은 아니다. 보다 정확한 원인은 원격 시스템이 어떠한 이유에서든 변경되어서일 수도 있다. 예를 들어, 운영체제나 SSH 서버가 재설치된 경우다. 보안과 안전을 우선시한다면 첫 번째 상황의 가능성을 지나치면 안 된다.





이 메시지가 별다른 이유가 없음이 확인되면 클라이언트 쪽의 문제를 수정하는 것이 안전하다. 이를 위해 텍스트 편집기([[chapter12 VI 맛보기|vim]] 편집기)로 ~/.ssh/known_hosts 파일에서 필요없는 키를 삭제하도록 한다. 앞의 예제에서 다음과 같은 메세지가 나옴을 알 수 있다.


```
Offending key in /home/me/.ssh/known_hosts:1
```


이 메시지가 뜻하는 것은 known_hosts 파일의 첫 번째 줄에 문제가 될만한 키가 있다는 것이다. 그 파일에서 이 줄을 삭제하면 ssh 프로그램은 원격 시스템의 새로운 인증 자격을 허용하게 된다.


원격 시스템의 쉘 세션이 열리면, 명령어를 실행할 수 있다. 예를 들어, free 명령어를 원격 호스트 localhost에 실행해보면 다음과 같은 로컬 시스템에 표시된다.

``` shell
┌──(kali㉿kali)-[~]
└─$ ssh localhost free
kali@localhost's password: 
               total        used        free      shared  buff/cache   available
Mem:         2012064      816944      884408       13164      471996     1195120
Swap:        1048572           0     1048572
                                                                                                                    
┌──(kali㉿kali)-[~]
└─$ 

```

조금 더 재미난 명령어를 입력해보자면 원격 시스템에서 ls 명령을 실행하고 그 결과를 로컬 시스템에 있는 파일로 보내보도록 한다.

``` shell
┌──(kali㉿kali)-[~]
└─$ ssh localhost 'ls *'>dirlist.txt
kali@localhost's password: 
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ 

dirlist.txt 의 내용

┌──(kali㉿kali)-[~]
└─$ cat dirlist.txt       
dirlist.txt
dirlist.xt
foo.txt
index.html
ls-output.txt
nano.60969.save

Desktop:
google-chrome.desktop
Python

Documents:

Downloads:
code_1.85.1-1702462158_amd64.deb
google-chrome-stable_current_amd64.deb

Music:

Pictures:

Public:

Templates:

Videos:



```

여기서 따옴표 사용에 주의해야 하는데, 로컬 머신이 아닌 원격 시스템의 경로명 확장을 실행하기 위해 따옴표를 사용했다. 마찬가지로 원격 시스템의 출력 결과를 보내고 싶다면 따옴표 아에 리다이렉션 연산자와 파일명을 모두 입력하면 된다.


``` shell
┌──(kali㉿kali)-[~]
└─$ ssh localhost 'ls * > dirlist.txt '
kali@localhost's password: 
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ 

```


---

- ***==부연 설명==***

SSH로 원격 호스트와 연결할 경우 이뤄지는 작업 중 하나는 로컬 시스템과 원격 시스템 간의 **암호화된 터널**이 생성되는 것이다. 보통 이 터널은 로컬 시스템에 입력된 명령어가 안전하게 원격 시스템으로 전송되게 하며 또 그 결과를 안전하게 가져올 수 있도록 한다. SSH 프로토콜은 이러한 기본적 기능 외에도 로컬과 원격지 사이에 일종의 VPN(가상사설망)을 생성하여 네트워크 트래픽의 대다수 형식이 암호화된 터널을 통해 전송되는 것을 지원한다.

아마도 이러한 기능을 사용한 가장 흔한 경우는 X 윈도우 트래픽을 전송할 때다. X서버가 실행 중인 시스템(GUI 환경)에서 X 클라이언트 프로그램(그래픽 환경의 애플리케이션)을 원격 시스템에 실행하고 화면을 로컬 시스템이 표시할 수 있다. 굉장히 쉬운 작업이다. 

linuxbox라는 리눅스 시스템에 X 서버가 실행 중이다. 그리고 우리는 원격 시스템 localhost에 xload 프로그램을 실행하여 해당 프로그램의 그래픽 출력 결과를 로컬 시스템에 표시하려고 한다.

``` shell
┌──(kali㉿kali)-[~]
└─$ ssh -X localhost
kali@localhost's password: 
Linux kali 6.5.0-kali3-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.5.6-1kali1 (2023-10-09) x86_64

The programs included with the Kali GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Kali GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Thu Mar 14 21:41:06 2024 from ::1
┌──(kali㉿kali)-[~]
└─$ xload
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ ssh -X remotelost 
ssh: Could not resolve hostname remotelost: Name or service not known
                                                                             
```

원격 시스템에서 xload 명령어가 실행되면 그 윈도우 화면이 로컬 시스템에 나타난다. 일부 시스템에서는 -x 옵션 대신 -y 옵션이 필요할 수도 있다.