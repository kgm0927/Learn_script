
### 파일 및 디렉토리 동기화

유닉스 세계에서는 동기화 작업에 선호하는 툴로 rsync라는 것이 있다. 이 프로그램은 rsync remote-update protocol을 이용하여 로컬과 원격 디렉토리 모두 동기화한다. ==이 프로토콜은 rsync 프로그램이 빠르게 두 디렉토리 간의 차이를 감지하여 동기화할 내용을 최소한으로 유지할 수 있도록 한다.== 이는 rsync를 다른 복사 프로그램들에 비해 더 빠르고 경제적으로 사용할 수 있도록 한다.

rsync는 다음과 같이 실행한다.

rsync *options source destination*

*source*와 *destination*에는 다음 중 하나가 올 수 있다.

- 로컬과 파일이나 디렉토리
- 원격 파일이나 디렉토리, `[user@]host:path` 형식
- `rsync://[user@]host[:port]/path` 형식의 URI로 지정된 원격 rsync 서버


source나 destination 둘 중 하나는 반드시 로컬 파일이어야 한다. 원격지 간의 복사는 지원되지 않는다.

몇몇 로컬 파일에 rsync를 실행해보자. 우선 foo 디렉토리를 비우도록 하자.

```shell
┌──(kali㉿kali)-[~]
└─$ rm -rf foo/*    
zsh: sure you want to delete all 3 files in /home/kali/foo [yn]? y

```

그 다음, playground 디렉토리를 foo 디렉토리에 동기화해보자.

```shell
┌──(kali㉿kali)-[~]
└─$ rsync -av playground foo
sending incremental file list
playground/
playground/timestamp
playground/dir-001/
playground/dir-001/file-A
playground/dir-001/file-B
playground/dir-001/file-C
playground/dir-001/file-D
playground/dir-001/file-E

```

-a 옵션(아카이브 생성, 실행 반복 및 파일 속성 유지시켜줌)과 -v 옵션(자세한 출력 방식)을 사용하여 playground 디렉토리의 내용을 foo에 **미러링**할 수 있다. 명령이 실행되는 동안 복사 중인 파일과 디렉터리 목록을 볼 수 있다. 결국, 이와 같은 복사량을 나타내는 요약 메시지를 보게 된다.

``` shell
sent 36,458 bytes  received 133 bytes  73,182.00 bytes/sec
total size is 0  speedup is 0.00

```

명령을 다시 하면 다른 결과를 볼 수 있다.


``` shell             
┌──(kali㉿kali)-[~]
└─$ rsync -av playground foo
sending incremental file list

sent 36,458 bytes  received 133 bytes  73,182.00 bytes/sec
total size is 0  speedup is 0.00

```

이번에는 파일 목록이 표시되지 않았다. 그 이유는 rsync 프로그램이 ~/playground 디렉토리와 ~foo/playground 디렉토리 사이에 다른 점이 없다고 감지했기 때문이다. 따라서 어떠한 복사도 이뤄지지 않았다. ==만약 playground의 파일을 수정하고 rsync 프로그램을 다시 실행하게 되면, rsync는 변경을 감지하고 업데이트된 파일에 대해서만 복사를 진행한다.==


``` shell
┌──(kali㉿kali)-[~]
└─$ touch playground/dir-099/file-Z                                          
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ rsync -av playground foo
sending incremental file list
playground/dir-099/file-Z

sent 36,511 bytes  received 158 bytes  73,338.00 bytes/sec
total size is 0  speedup is 0.00

```

실제 상황을 예로 들어 보자. 이전에 tar 프로그램과 함께 사용했던 외장 하드 드라이브를 다시 떠올려보자. 우리 시스템에 이 드라이브를 연결하게 되면 그때처럼 /media/BigDisk 디렉토리에 자동 마운트 될 것이다. 그럼 우리는 외장 하드 드라이브에 맨 먼저 /backup 디렉토리를 만들어서 시스템 백업 작업을 수행할 수 있다. 그런 다음 rsync를 이용하여 제일 중요한 것들을 시스템에서 외장 하드 드라이브로 복사한다.

``` shell
┌──(kali㉿kali)-[~]
└─$ mkdir /media/BigDisk/backup
┌──(kali㉿kali)-[~]
└─$ sudo rsync -av --delete /etc /home /usr /local /media /BigDisk /backup

```

이 예제에서, /etc, /home, /usr/local 디렉토리를 상상 속의 외장 저장 장치로 복사하였다.` --delete`옵션을 포함시켜 기존 시스템에서는 더 이상 존재하지는 않지만 백업 장치에는 남아있을지도 모르는 파일에 대해 삭제하도록 한다(처음 백업을 수행할 때는 이런 상황이 발생하지 않을 테지만 이후부터는 유용한 옵션이다). 외장 하드 드라이브를 연결하고 rsync 명령을 실행하는 반복적인 절차에는 시스템 백업량을 최소한으로 유지할 수 있는 좋은 방법이다. 또한, 여기서 별칭(alias)을 활용하는 것도 유용하다. 이 작업에 별칭을 붙여 .bashrc 파일에 추가시켜 보자.

``` shell
--alias backup='sudo rsync -av --delete /etc /kali /usr/local /media/BigDisk/backup'
```

이제 남는 일은 외장 드라이브를 연결하여 새롭게 만든 backup 명령을 실행하기만 하면 된다.


---
# 네트워크 상에서 rsync 실행하기

rsync의 진면모 중 하나는 네트워크상에서 파일을 복구할 수 있다는 점이다. 어쨌든 rsync의 r이 의미하는 바가 remote이다. 원격 복사는 두 가지 중 하나를 통해 이뤄진다.

첫 번째 방법은 rsync 프로그램이 설치되어 있고 ssh와 같은 원격 쉘 프로그램이 동작하고 있는 다른 시스템으로 복사하는 것이다. 로컬 네트워크상에 연결된 한 시스템이 있고 이 시스템에는 공간이 아주 많은 하드 드라이브가 있다고 해보자. 우리는 외장 하드 대신 원격 시스템을 이용해서 백업 작업을 수행하길 원한다. 파일을 복사해둘 /backup이란 디렉토리가 이미 있다고 가정하고 다음과 같이 해보도록 하자.

``` shell
┌──(kali㉿kali)-[~]
└─$ sudo rsync -av --delete --rsh=ssh /etc /home /usr /local remote-sys:/backup

```

우리는 네트워크상에서 복사 작업을 하기 위해서 이 명령의 두 부분을 수정했다. 먼저 `--rsh=ssh` 옵션을 추가했다. 이 옵션은 rsync로 하여금 원격 쉘로서 ssh를 사용하라는 뜻이다. 이로써 SSH로 암호화된 터널을 사용하여 로컬 시스템에서 원격 호스트까지 안전하게 데이터를 전송할 수 있다. 두 번째는 원격 호스트 지정이다. 그 이름을 목적 경로명 앞에 덧붙였다(이 경우 원격 호스트명은 remote-sys).

원격 복사의 두 번째 방법은 rsync server를 이용하여 네트워크상에서 동기화하는 것이다. rsync 프로그램은 데몬으로 실행해서 동기화 요청에 응답할 수 있도록 설정할 수 있다. 이 방법은 원격 시스템을 미러링할 대 많이 사용된다. 예를 들어, 레드햇 소프트웨어사는 페도라 배포판 개발을 위해서 큰 규모의 소프트웨어 패키지 저장소를 운영한다. 배포판 테스트 기간 동안 해당 배포판에 대해서 미러링하는 것은 소프트웨어 테스터들에게는 매우 유용하다. 저장소에 있는 파일들은 자주 바뀌기 때문에(하루에 한 번 이상), 대량 복사를 통해 백업하는 것보다는 주기적인 동기화로 로컬 백업본을 유지하는 것이 바람직하다. 조지아 텍(Georgia Tech)의 서버를 이용하여 이 저장소를 미러링할 수 있다.

``` shell

┌──(kali㉿kali)-[~]
└─$ rsync -av --delete rsync://rsync.gtlib.gatech.edu/fedoralinux-core/development/i386/os fedora-devel
---------------------------------------------------------------------------

GEORGIA TECH SOFTWARE LIBRARY

Unauthorized use is prohibited.  Your access is being logged.

GTlib is hosted by the Partnership for an Advanced Computing Environment
(PACE) at the Georgia Institute of Technology in Atlanta, Georgia, USA.

     http://www.pace.gatech.edu

GTLib is operated as a "best effort" service.  We _strongly_ recommend
against depending on the availability of this service in a critical context.

If you run a publicly accessible mirror, and are interested in
mirroring from us, please contact gtlib@gtlib.gatech.edu.

If you run a mirror that is not accessible to the public, please mirror
from the modules listed at rsync://rsync.gtlib.gatech.edu.

----------------------------------------------------------------------------

@ERROR: Unknown module 'fedoralinux-core'
rsync error: error starting client-server protocol (code 5) at main.c(1850) [Receiver=3.3.0]
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ 

```

이 예제에서, 우리는 `rsync://`라는 프로토콜과 원격 호스트명(rsync.gtlib.gatech.edu), 저장소 경로명으로 구성된 원격 rsync 서버의 URL를 사용하였다.

