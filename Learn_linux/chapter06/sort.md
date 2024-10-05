
---

# 필터

파이프라인은 데이터의 복잡한 연산을 수행할 때 종종 사용된다. 하나의 파이프라인에 여러 명령어를 입력하는 것이 가능하다. 이러한 방식의 명령어들을 ‘필터’라고 한다. 필터는 받은 내용을 어떻게든 바꾸어 출력하게 한다.

첫 번째로 시도해볼 것은 **sort** 명령어다. 한 가지 상황을 상상해보자. 우리는 /bin 디렉토리와 /usr/bin 디렉토리에 있는 실행 프로그램을 하나의 목록으로 정렬한 뒤 그 목록을 보길 원한다.

``` shell
kgm0917@DESKTOP-DT2VQLB:~$ ls /bin /usr/bin | sort |less
```

두 디렉토리를 지정했기 때문에 ls 결과로 정렬된 두 목록을 가져올 것이다. 파이프라인에 sort를 포함함으로써 하나로 정렬된 데이터 목록으로 바꿀 수 있다.


---
### sort- 텍스트 파일의 행을 정렬

sort 프로그램은 표준 입력이나 커맨드라인에 명시한 하나 이상의 파일의 내용물들을 정렬하고 표준 출력으로 결과를 전달한다. cat과 동일한 방식으로 직접 키보드로부터 표준 입력을 처리할 수 있다.

``` shell
┌──(kali㉿kali)-[~]
└─$ sort > foo.txt
c
b
a                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ cat foo.txt   
a
b
c

```

명령어 입력 후, 문자 c, b, a를 타이핑하고 다시 한번 ctrl-D로 파일의 끝을 알린다. 그러고 나서 결과 파일을 보면 이제 그 줄들이 정렬된 것을 보게 된다.


`sort`는 커맨드라인 인자로 복수의 파일을 허용하기 때문에, ==파일들을 전부 합쳐 하나의 정렬된 형태로 만들 수 있다.== 예를 들어, 세 개의 텍스트 파일을 가지고 있고 하나의 정렬된 파일로 합치길 원한다면 다음과 같이 수행할 수 있다.


`sort file1.txt file2.txt file3.txt > final_sorted_list.txt`


sort는 여러 가지 재미있는 옵션을 가지고 있다. [표 20-1]는 일부 목록을 보여준다.





- [표 20-1] sort 주요 옵션

| 옵션  | Long 옵션                 | 설명                                                                                          |
| --- | ----------------------- | ------------------------------------------------------------------------------------------- |
| -b  | --ignore-leading-blanks | 기본적으로 정렬은 각 줄의 첫 문자를 대상으로 전체에 실행한다. 이 옵션은 각 줄의 첫 공백들은 무시하고 공백이 아닌 첫 문자를 기준으로 계산하여 정렬하도록 한다. |
| -f  | --ignore-case           | 정렬 시에 대소문자를 구분하지 않도록 한다.                                                                    |
| -n  | --numeric-sort          | 문자열의 숫자 값을 평가하여 정렬을 실행한다. 이 옵션을 사용하면 알파벳 대신 수치를 기준으로 정렬하도록 한다.                              |
| -r  | --reverse               | 역순으로 정렬한다. 결과는 오름차순 대신 내림차순으로 정렬된다.                                                         |
| -k  | --key= `field[,field2]` | 필드 전체가 아닌 파일명을 각각 인자로 처리한다. 복수의 파일을 추가적인 정렬 없이 하나의 정렬된 결과로 합한다.                             |
| -o  | `--output=file`         | 정렬된 결과를 표준 출력이 아닌 *file*로 보낸다.                                                              |
| -m  | --merge                 | 이미 정렬된 파일명을 각각 인자로 처리한다. 복수의 파일을 추가적인 정렬 없이 하나의 정렬된 결과로 합친다.                                |
| -t  | --field-seprator= char  | 필드 구문문자를 정의한다. 기본적으로 스페이스나 탭으로 필드를 구분한다.                                                    |


이 옵션 대부분이 부가 설명 없이도 잘 이해가 가지만 일부는 그렇지 않다. 먼저 숫자 값으로 정렬하는 -n 옵션을 살펴보자. 이 옵션은 숫자 값을 기반으로 값을 정렬한다. 디스크 사용량이 가장 많은 것들을 확인하기 위해 du 명령어의 결과를 기반으로 보여줄 것이다. 일반적으로 du 명령은 경로명순으로 요양된 결과를 나열한다.

``` shell
┌──(kali㉿kali)-[~]
└─$ du -s /usr/share/* | head
56      /usr/share/GConf
8988    /usr/share/GeoIP
116     /usr/share/ImageMagick-6
36      /usr/share/ModemManager
24      /usr/share/Thunar
6084    /usr/share/X11
24      /usr/share/accountsservice
40      /usr/share/aclocal
3396    /usr/share/alsa
372     /usr/share/alsa-card-profile

```

이 예제에서는 결과를 head로 연결하여 처음 10줄만을 표시할 수 있도록 한다. 사용량이 가장 큰 10가지를 보기 위해서 다음과 같은 방식으로 숫자로 정렬된 목록을 만들 수 있다.

``` shell
└─$ du -s /usr/share/* | sort -nr | head
536820  /usr/share/metasploit-framework
502408  /usr/share/dotnet
399424  /usr/share/code
374148  /usr/share/locale
369112  /usr/share/doc
334516  /usr/share/texlive
291576  /usr/share/exploitdb
268624  /usr/share/fonts
264524  /usr/share/burpsuite
182040  /usr/share/icons

```

==-nr 옵션을 사용하여, 결과에 가장 큰 값이 처음 나올 수 있도록 역순으로 숫자 값 정렬을 한다.== 이 정렬은 각 행이 숫자 값으로 시작하기 때문에 동작한다. 하지만 만약 그 행에 있는 어떤 값을 기준으로 정렬하기를 원한다면 어떻게 될까? 예를 들면 ls -l의 결과 값은 이와 같다.

``` shell
┌──(kali㉿kali)-[~]
└─$ ls -l /usr/bin | head         
합계 844632
lrwxrwxrwx 1 root root            31 2023년 12월  7일 1password2john -> ../share/john/1password2john.py
-rwxr-xr-x 1 root root            96 2022년  8월  1일 2to3-2.7
-rwxr-xr-x 1 root root         14568  5월  5일  11:58 411toppm
-rwxr-xr-x 1 root root            38  4월 11일  06:53 7z
lrwxrwxrwx 1 root root            24 2023년 12월  7일 7z2john -> ../share/john/7z2john.pl
-rwxr-xr-x 1 root root            39  4월 11일  06:53 7za
-rwxr-xr-x 1 root root            39  4월 11일  06:53 7zr
lrwxrwxrwx 1 root root            29 2023년 12월  7일 DPAPImk2john -> ../share/john/DPAPImk2john.py
lrwxrwxrwx 1 root root            28  5월  4일  01:27 FileCheck-16 -> ../lib/llvm-16/bin/FileCheck

```

ls의 크기를 기준으로 정렬할 수 있다는 사실은 잠시만 무시하고, 이 목록을 파일의 크기로 정렬하기 위해 sort도 사용할 수 있다.


``` shell
┌──(kali㉿kali)-[~]
└─$ ls -l /usr/bin | sort -nr -k 5 | head
-rwxr-xr-x 1 root root      44571056 2024년  3월  9일 mutool
-rwxr-xr-x 1 root root      43546304 2024년  3월  9일 muraster
-rwxr-xr-x 1 root root      36237176 2023년  9월 13일 amass
-rwxr-xr-x 1 root root      33140936 2024년  3월 18일 x86_64-w64-mingw32-lto-dump-win32
-rwxr-xr-x 1 root root      32992864  8월 18일  13:10 x86_64-linux-gnu-lto-dump-14
-rwxr-xr-x 1 root root      32547064  4월 29일  05:49 x86_64-linux-gnu-lto-dump-13
-rwxr-xr-x 1 root root      32461000 2024년  3월 18일 i686-w64-mingw32-lto-dump-win32
-rwxr-xr-x 1 root root      12484000  5월  7일  03:45 kismet
-rwxr-xr-x 1 root root      10613432  5월 16일  15:07 wireshark
-rwxr-xr-x 1 root root       8184512 2023년 11월 14일 ffuf
                                                             
```

sort의 사용 대부분은 이 ls 명령의 결과처럼 표로 구성된 자료의 처리와 관련이 있다. 만약 위 표를 데이터베이스 용어로 적용해보면, 각 행은 **레코드**라 부르고 각 레코드는 파일 속성, 링크 수, 파일명, 파일 크기 등의 복수 **필드**로 구성된다. sort는 각각의 필드들을 처리할 수 있다. 데이터베이스 용어인, **정렬키**로 사용하기 위해 **키 필드**를 하나 이상 지정 가능하다. 이 예제에서 명시한 n과 r 옵션은 역순 숫자 값 정렬을 실행하고 `-k -5`옵션은 다섯 번째 필드를 정렬 키로 사용하게끔 한다.

k 옵션은 매우 흥미롭고 많은 기능이 있다. 하지만 먼저 어떻게 sort가 필드를 정의하는지 대한 논의가 필요하다. 매우 단순하게 필자의 이름이 표시된 줄 하나로 구성된 텍스트 파일을 살펴보자.

`William    Shotts`

기본적으로 sort는 이 줄이 두 필드를 가진 것으로 본다. 첫 번째 필드는 William이라는 단어를 가진 것이고, 두 번째 필드는 Shotts이다. 이는 공백 문자들(스페이스와 탭)이 필드 사이의 구분자로 사용되고 그 구분자는 정렬할 때 그 필드에 포함된다는 뜻이다.

ls 출력 결과에서 다시 한번 한 줄을 살펴보면 그 줄이 8개의 필드를 가지고 있고 다섯 번째 필드는 파일 크기임을 볼 수 있다.
``` shell
-rwxr-xr-x 1 root root      44571056 2024년  3월  9일 mutool

```

다음 실습은 2006년부터 2008년까지 출시된 리눅스 배포판 기록을 담은 다음 파일을 살펴보는 것이다. 파일의 각 행은 세 필드로 나뉘어 있다. 배포판 이름, 버전 번호, MM/DD/YYYY 형식의 출시 날짜를 포함한다.

``` shell
SUSE    10.2    12/07/2006
Fedora  10      11/25/2008
SUSE    11.0    06/19/2008
Ubuntu  8.04    04/24/2008
Fedora  8       11/08/2007
SUSE    10.3    10/04/2007
Ubuntu  6.10    10/26/2006
Fedora  7       05/31/2007
Ubuntu  7.10    10/18/2007
Ubuntu  7.04    04/19/2007
SUSE    10.1    05/11/2007
Fedora  6       10/24/2006
Fedora  9       05/13/2008
Ubuntu  6.06    06/01/2006
Ubuntu  8.10    10/30/2008
Fedora  5       03/20/2006

```

텍스트 편집기(아마도 vim)를 사용하여 이 자료를 입력하고 distros.txt 라는 파일명으로 저장할 것이다.

그 다음에 파일을 정렬하고 결과를 살펴볼 것이다.

```shell
┌──(kali㉿kali)-[~]
└─$ sort distros.txt
Fedora  10      11/25/2008
Fedora  5       03/20/2006
Fedora  6       10/24/2006
Fedora  7       05/31/2007
Fedora  8       11/08/2007
Fedora  9       05/13/2008
SUSE    10.1    05/11/2007
SUSE    10.2    12/07/2006
SUSE    10.3    10/04/2007
SUSE    11.0    06/19/2008
Ubuntu  6.06    06/01/2006
Ubuntu  6.10    10/26/2006
Ubuntu  7.04    04/19/2007
Ubuntu  7.10    10/18/2007
Ubuntu  8.04    04/24/2008
Ubuntu  8.10    10/30/2008

```

거의 제대로 동작한다. 페도라 버전 번호의 정렬에 한 가지 문제가 발생했다. 1이 5보다 앞선 문자이기 때문에 결국 버전 10은 맨 위로 올라가는 반면 버전 9는 맨 끝으로 내려간다.


이 문제를 고치기 위해서는 복수 키로 정렬해야 한다. 첫 번째 필드는 알파벳 정렬을 세 번째 필드는 수 정렬을 한다. -k 옵션은 복수로 사용 가능하기에 여러 정렬키를 명시할 수 있다. 사실 키는 필드의 범위를 가질 수도 있다. 만약 범위를 지정하지 않으면, sort는 명시된 필드부터 행의 끝까지 확장된 키를 사용한다.

복수키 정렬을 위한 문법이 여기 있다.

``` shell
┌──(kali㉿kali)-[~]
└─$ sort --key=1,1 --key=2n distros.txt 
Fedora  5       03/20/2006
Fedora  6       10/24/2006
Fedora  7       05/31/2007
Fedora  8       11/08/2007
Fedora  9       05/13/2008
Fedora  10      11/25/2008
SUSE    10.1    05/11/2007
SUSE    10.2    12/07/2006
SUSE    10.3    10/04/2007
SUSE    11.0    06/19/2008
Ubuntu  6.06    06/01/2006
Ubuntu  6.10    10/26/2006
Ubuntu  7.04    04/19/2007
Ubuntu  7.10    10/18/2007
Ubuntu  8.04    04/24/2008
Ubuntu  8.10    10/30/2008

```

여기서는 명료함을 위해 long 옵션을 사용했지만, `-k 1,1 -k 2n`도 정확히 동일한 기능을 한다. 첫 번째 key 옵션은 첫 번째 키에 포함할 필드 범위를 명시한다. 첫 번째 필드로 정렬을 제한하기 위해 1,1로 지정했다. 이는 "1번 필드로 시작해서 1번 필드로 끝남"을 의미한다. 두 번째 key 옵션은 2n로 지정하였고 이는 2번 필드가 정렬시키고 숫자 값 정렬을 의미한다. 실행할 정렬 방식을 나타내는 문자 옵션은 키 지정자 끝에 포함될 수 있다. 이들 옵션 문자는 sort 프로그램의 전역 옵션과 동일하다.  즉 *b*(시작 공백 무시), *n*(숫자 값 정렬), *r*(역순 정렬) 등을 사용할 수 있다.

목록의 세 번째 필드는 정렬을 위한 날짜를 포함하고 있다. 이는 조금 불편한 방식으로 지정되어 있다. 컴퓨터상에서 날짜는 시간순으로 정렬되기 쉽게 항상 `YYYY-MM-DD`형식으로 지정된다. 그러나 이것은 미국식 포맷인 `MM/DD/YYYY`을 사용하고 있다. 우리는 어떻게 이 목록을 시간순으로 정렬할 수 있을까?

다행히도, sort는 이 방식을 지원한다. key 옵션은 필드 내의 특정 영역을 지정 가능하고 따라서 이로 필드 내의 키를 정의할 수 있다.

``` shell
┌──(kali㉿kali)-[~]
└─$ sort -k 3.7nbr -k 3.1nbr -k 3.4nbr distros.txt
Fedora  10      11/25/2008
Ubuntu  8.10    10/30/2008
SUSE    11.0    06/19/2008
Fedora  9       05/13/2008
Ubuntu  8.04    04/24/2008
Fedora  8       11/08/2007
Ubuntu  7.10    10/18/2007
SUSE    10.3    10/04/2007
Fedora  7       05/31/2007
SUSE    10.1    05/11/2007
Ubuntu  7.04    04/19/2007
SUSE    10.2    12/07/2006
Ubuntu  6.10    10/26/2006
Fedora  6       10/24/2006
Ubuntu  6.06    06/01/2006
Fedora  5       03/20/2006

```

`-k 3.7`을 명시하면서 sort에 3번째 필드의 7번째 문자에서 시작하는 정렬키를 사용하도로고 지시한다. 이는 연도의 시작에 해당된다. 마찬가지로 `-k 3.1`과 `k 3.4`는 해당 날짜의 월과 일을 분리하는 데 사용한다. 또한 역순 숫자 값 정렬을 위해 n과 r 옵션을 추가한다. b 옵션은 날짜 필드의 시작이 공백인 경우를 제거한다(행마다 공백은 다르고, 정렬의 결과에 영향을 미친다).

어떤 파일은 탭과 스페이스를 필드 구분자로 사용하지 않는다. /etc/passwd 파일이 그 예다.


```shell
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ head /etc/passwd
root:x:0:0:root:/root:/usr/bin/zsh
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
                                                       
```

이 파일의 필드들은 콜론(:)으로 구분된다. 그러면 어떻게 키 필드를 사용하여 이 파일을 정렬할 수 있을까? sort는 필드 구분자를 지정하는 -t 옵션을 제공한다. passwd 파일의 7번째 필드(사용자 기본 쉘)를 기준으로 정렬하기 위해 다음과 같이 할 수 있다.

``` shell
┌──(kali㉿kali)-[~]
└─$ sort -t ':' -k 7 /etc/passwd | head           
postgres:x:129:132:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash
Debian-snmp:x:119:123::/var/lib/snmp:/bin/false
lightdm:x:109:112:Light Display Manager:/var/lib/lightdm:/bin/false
mysql:x:116:120:MariaDB Server,,,:/nonexistent:/bin/false
speech-dispatcher:x:107:29:Speech Dispatcher,,,:/run/speech-dispatcher:/bin/false
tss:x:101:104:TPM software stack,,,:/var/lib/tpm:/bin/false
sync:x:4:65534:sync:/bin:/bin/sync
kali:x:1000:1000:,,,:/home/kali:/usr/bin/zsh
root:x:0:0:root:/root:/usr/bin/zsh
_apt:x:42:65534::/nonexistent:/usr/sbin/nologin
                                                    
```

필드 구분자를 콜론 문자로 지정하여 7번째 필드를 정렬할 수 있다.