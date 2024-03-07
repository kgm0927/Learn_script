
# (정확히는 head/tail)- 파일의 처음/끝 부분 출력


단 몇 줄만 확인하기 위해서 **head** 명령어로 파일의 첫 10줄만 출력할 수 있고, tail 명령어로 마지막 10줄을 표시할 수 있다. 기본적으로 10줄만을 출력하지만 –n 옵션을 사용하여 길이를 조절할 수 있다.

``` shell
kgm0917@DESKTOP-DT2VQLB:~$ head -n 5 ls-output.txt

total 96744

lrwxrwxrwx 1 root root 4 Feb 17 2020 NF -> col1

-rwxr-xr-x 1 root root 51632 Feb 8 2022 [

-rwxr-xr-x 1 root root 35344 Jun 21 2022 aa-enabled

-rwxr-xr-x 1 root root 35344 Jun 21 2022 aa-exec

kgm0917@DESKTOP-DT2VQLB:~$ tail -n 5 ls-output.txt

-rwxr-xr-x 1 root root 8103 Sep 5 22:33 zgrep

-rwxr-xr-x 1 root root 60065 Oct 5 03:16 zipdetails

-rwxr-xr-x 1 root root 2206 Sep 5 22:33 zless

-rwxr-xr-x 1 root root 1842 Sep 5 22:33 zmore

-rwxr-xr-x 1 root root 4577 Sep 5 22:33 znew
```

이 두 명령어 모두 다음과 같은 파이프라인에서 사용할 수 있다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ ls /usr/bin | tail -n 5

zgrep

zipdetails

zless

zmore

znew
```

tail 명령어는 실시간으로 파일을 확인할 수 있는 옵션을 지원한다. 로그 파일이 기록되는 동안 최근 내용을 확인할 때 매우 편리하다.

다음 예제에서 /var/log 디렉토리에 있는 메시지 파일을 열어볼 것이다. 일부 리눅스 배포판에서는 이러한 작업을 위해서 슈퍼유저 권한이 필요하다. 이는 /var/log/messages 파일에는 보안이 필요한 정보가 포함되어 있을지 모르기 때문이다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ tail –f /var/log/messages

(여기서는 /var/log/messages 가 없다. 그러므로 그냥 예시를 넘기겠다.)
```


-f 옵션을 이용하면 tail은 지속적으로 로그 파일을 감시하고 새 내용이 추가될 때 곧바로 그 내용을 표시한다. **ctrl-C** 키를 입력할 때까지 이 작업은 계속 수행된다.