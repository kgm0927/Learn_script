
로그 파일과 같은 공유 파일을 읽거나 추가하는 스크립트 파일을 잠글 수 잇는 믿을 만한 방법이 필요하다. 이는 스크립트의 다른 인스턴스가 실수로 현재 사용 중인 데이터를 덮어 씌우지 않도록 하기 위해서다. 이를 수행하는 일반적인 방법은 사용되는 각 파일에 대해 별도의 *잠금 파일*(lock file)을 만드는 것이다. 잠금 파일은 '파일이 다른 스크립트에서 사용 중이며 사용할 수 없다'라는 표시자인 *세파포어*(semaphore) 역할을 한다. 그런 다음, 요청 스크립트는 파일을 자유롭게 편집할 수 있음을 알려주는 세마포어 잠금 파일이 제거될 때까지 반복적으로 대기하고 다시 시도한다.

겉보기에 절대 안전한 많은 솔루션이 실제로 동작하지 않기 때문에 잠금 파일을 까다롭다. 예를 들어, 다음 코드는 이 문제를 해결하는 일반적인 방법이다.

```shell
while [ -f $lockfile ] ; do
	sleep 1
done
touch $lockfile
```

동작하는 것처럼 보이는데, 코드는 잠긴 파일이 존재하지 않을 때까지 반복한 후, 잠긴 파일을 생성해 소유하고 있으므로 베이스 파일을 안전하게 수정할 수 있을 것이다. 동일한 루프를 가진 다른 스크립트가 잠긴 것을 보게 되면 잠금 파일이 사라질 때까지 반복한다. 그러나 실제로는 작동하지 않는다. [[while]] 루프가 종료된 직후 [[touch]]가 실행되기 전에 이 스크립트를 스왑하고 프로세서 큐에 다시 넣으면 다른 스크립트에 실행할 기회가 주어지는 경우를 생각해보자.

이 경우, 무엇을 참조하는지 확신할 수 없다. 컴퓨터가 한 번에 한 가지 일을 하는 것처럼 보이더라도 실제로는 한 번에 다른 프로그램을 전환하고 작은 비트(tiny bit)를 실행하고 다시 전환하는 것으로 동시에 여러 프로그램을 실행하고 있음을 기억해야 한다. 여기서 문제는 스크립트가 잠금 파일을 확인하고 그 스크립트를 생성하는 사이에 시스템이 다른 스크립트로 스왑할 수 있다는 것이다. 이 스크립트는 충실히 잠금 파일을 테스트하고 부재(absent)를 확인해 자체 스크립트를 만들 수 있다. 그런 다음, 스크립트가 스왑될 수 있고, 스크립트가 다시 스왑 돼 [[touch]] 명령 실행을 재개할 수 있다. 그 결과, 두 스크립트 모두 잠금 파일에 대한 독점적인 액세스를 갖고 있다고 생각할 것이다. 이것이 우리가 피하려고 했던 것이다.

다행스럽게도 procmail 전자 메일 필터링 프로그램의 저자인 스테판 벤 덴 베르그(Stephen van den Berg)와 필립 겐더(Philip Guenther)는 커맨드라인 유틸리티인 lockfile을 만들어 셸 스크립트의 잠금 파일을 사용해 안전하고 안정적으로 작업할 수 있게 했다.

GNU/리눅스 및 맥 OS를 포함한 많은 Unix 배포판에는 lockfile이 이미 설치돼 있다. `man 1 lockfile`을 입력하면 시스템에 lockfile이 있는지 여부를 확인할 수 있다. 매뉴얼 페이지를 얻으면 운이 좋은 것이다. 리스트 1-22의 스크립트는 lockfile 명령을 갖고 있다고 가정하고, 후속 스크립트는 스크립트 `(#10)`의 안정적인 잠금 메커니즘을 필요로 함으로 먼저 시스템에 lockfile 명령어가 설치돼 있는지 확인해보자.

---
### 코드


- [리스트 1-22]: filelock 스크립트
``` shell
#!/bin/bash
# filelock--A flexible file locking mechanism

retries="10"            # Default number of retries
action="lock"           # Default action
nullcmd="`which true`"     # Null command for lockfile

while getopts "lur:" opt; do
  case $opt in
    l ) action="lock"      ;;
    u ) action="unlock"    ;;
    r ) retries="$OPTARG"  ;;
  esac
done
shift $(($OPTIND - 1))

if [ $# -eq 0 ] ; then # Output a multi-line error message to stdout.
  cat << EOF >&2
Usage: $0 [-l|-u] [-r retries] LOCKFILE
Where -l requests a lock (the default), -u requests an unlock, -r X 
specifies a max number of retries before it fails (default = $retries).
EOF
  exit 1
fi

# Ascertain if we have the lockfile command.

if [ -z "$(which lockfile | grep -v '^no ')" ] ; then
  echo "$0 failed: 'lockfile' utility not found in PATH." >&2
  exit 1
fi
if [ "$action" = "lock" ] ; then
  if ! lockfile -1 -r $retries "$1" 2> /dev/null; then
    echo "$0: Failed: Couldn't create lockfile in time" >&2
    exit 1
  fi
else    # Action = unlock.
  if [ ! -f "$1" ] ; then
    echo "$0: Warning: lockfile $1 doesn't exist to unlock" >&2
    exit 1
  fi
  rm -f "$1"
fi

exit 0

```


---

### 동작 방식

잘 만들어진 셸 스크립트에서 흔히 볼 수 있듯이, [리스트 1-22]의 절반은 입력 변수를 파싱하고 오류 조건을 검사한다. 마지막으로 [[if]]문으로 이동한 후, 실제로 시스템 lockfile 명령어를 사용하려고 시도한다. lockfile이 존재하는 경우, 재시도 횟수를 지정해 호출하고, 최종적으로 성공하지 못하면 자체 오류 메시지를 생성한다. 만약 잠금 해제를 요청한 경우(예를 들어, 기존 잠금 제거) 아무것도 없으면 다른 오류가 발생한다. 그렇지 않으면 lockfile은 제거되고 작업이 완료된다.

보다 구체적으로, 첫 번째 `(#1)`은 강력한 getopts 함수를 사용해 가능한 모든 사용자 입력 플래그 ( -l, -u, -r)를 [[while]]루프로 구문 분석한다. 이것은 getopts를 사용하는 일반적인 방법으로, 이 책에서 반복적으로 사용할 것이다. `(#2)`에서 [[shift]] `$(($OPTIND - 1)) `문에 주목하자. OPTIND는 getopts에 의해 설정돼 스크립트가 대시로 시작하는 값을 처리할 대까지 스크립트가 값을 하나씩 낮춘다(예를 들어, $2가 $1이 된다).

이 스크립트는 시스템 lockfile 유틸리티를 사용하기 때문에 lockfile 호출`(#3)`하기 전에 유틸리티가 사용자 경로에 있는지 확인하는 것이 좋다. 그렇지 않은 경우, 오류 메시지와 함께 동작하지 않는다. 그렇다면 각각의 경우에 lockfile 유틸리티에 대한 적절한 호출과 잠금 또는 잠금 해제 여부를 확인하기 위한 간단한 조건 `(#4)`이 있다.

---

### 결과

먼저 [리스트 1-23] 처럼 잠긴 파일을 생성한다.


- [리스트 1-23]: filelock 명령어로 잠긴 파일 생성
``` shell
 kali@kali  ~/Documents/GitHub/learning_shell_script   main  filelock /tmp/exclusive.lck
 kali@kali  ~/Documents/GitHub/learning_shell_script   main  ls -l /tmp/exclusive.lck
-r--r--r-- 1 kali kali 1 Oct 29 04:31 /tmp/exclusive.lck

```

두 번째로 파일 잠금을 시도하면, filelock은 기본 횟수(10회)만큼 시도한 후, 실패한다([리스트 1-24]).

- [리스트 1-24]: filelock 명령어로 잠긴 파일을 생성하지 못하고 실패
```shell
 kali@kali  ~  filelock /tmp/exclusive.lck

/home/kali/Documents/GitHub/learning_shell_script/bin/filelock: Failed: Couldn't create lockfile in time

```

첫 번째 프로세스가 파일로 처리되면 [리스트 1-25]와 같이 잠금을 해제할 수 있다.

- [리스트 1-25]: filelock 스크립트로 잠긴 파일을 해제

```shell
 ✘ kali@kali  ~  filelock -u /tmp/exclusive.lck

```

filelock 스크립트가 2개의 터미널에서 동작하는 방식을 보려면, 다른 한쪽이 회전하는 동안, 한쪽 창에서 unlock 명령을 실행하고 단독 잠금(exclusive lock)을 설정하려고 시도하면 된다.

---

### 스크립트 해킹하기

이 스크립트는 잠금 파일이 여전히 적용된다는 증거로, 잠금 파일의 존재로 판단하기 때문에 잠금이 유효해야 할 가장 긴 시간과 같은 추가 매개 변수를 갖는 것이 유용하다. lockfile 루틴이 시간 종료되면, 잠긴 파일의 마지막 액세스 시간을 확인할 수 있으며, 잠긴 파일이 매개변수의 값보다 오래된 경우, 경고 메시지와 함께 누락된 파일로 안전하게 삭제할 수 있다.

사용자에게 영향을 미치지 않지만, NFS(Network File System) 마운트 네트워크 드라이브에서는 lockfile이 동작하지 않는다. 사실, NFS 마운트 디스크의 안정적인 파일 잠금 메커니즘은 상당히 복잡하다. 문제를 완전히 해결하는데 더 나은 전략은 로컬 디스키에서만 잠금 파일을 만들거나 여러 시스템에서 잠금을 관리할 수 있는 네트워크 인식 스크립트를 사용하는 것이다.


