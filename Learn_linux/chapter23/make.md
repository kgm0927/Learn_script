
# C 프로그램 컴파일하기

이제 컴파일을 해 보자. 하지만 그 전에 컴파일러, 링커, 그리고 make와 같은 툴들이 필요할 것이다. 리눅스 환경에서 C 컴파일러는 리차드 스톨만에 의해 개발된 gcc(GNU C Complier)가 거의 보편적으로 사용된다. 대부분의 배포판은 gcc를 기본적으로 설치하지 않는 다음 예제처럼 컴파일러가 존재하는지 확인할 수 있다.

``` shell
┌──(kali㉿kali)-[~]
└─$ which gcc                          
/usr/bin/gcc

```

이 예제 결과에서는 컴파일러가 설치되었음을 알 수 있다.

>[!tip] 저자주
>사용자의 배포판에는 소프트웨어 개발용 메타 패키지(패키지들의 모음)가 포함되어 있을지도 모른다. 만약 그렇다면, 그 시스템에서 프로그램을 컴파일하려면 그것을 설치하는 것도 고려해봐라. 반대로 메타패키지가 포함되지 않았다면, gcc와 make 패키지도 설치해라. 많은 배포판들이 다음의 연습문젤르 실행하기에는 충분할 것이다.


### 소스 코드 구하기

우리는 컴파일 예제에서 diction이라는 GNU 프로젝트로부터 프로그램을 컴파일할 것이다. 글쓰기의 질과 스타일을 위해 텍스트 파일을 확인하는 데 사용하는 간편한 프로그램이다. 일반적인 프로그램처럼 아주 작고 빌드하기 쉽다.

다음의 방식에 따라 소스 코드용 디렉토리 src를 생성하고 [[ftp]]를 사용하여 소스 코드를 이 디렉토리에 다운로드 할 것이다.

```shell

┌──(kali㉿kali)-[~]
└─$ which gcc                          
/usr/bin/gcc
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ mkdir src    
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ cd src                        
                                                                                                                   
┌──(kali㉿kali)-[~/src]
└─$ ftp ftp.gnu.org               
Trying 209.51.188.20:21 ...
Connected to ftp.gnu.org.
220 GNU FTP server ready.
Name (ftp.gnu.org:kali): anonymous
230-NOTICE (Updated October 15 2021):
230-
230-If you maintain scripts used to access ftp.gnu.org over FTP,
230-we strongly encourage you to change them to use HTTPS instead.
230-
230-Eventually we hope to shut down FTP protocol access, but plan
230-to give notice here and other places for several months ahead
230-of time.
230-
230----
230-
230-Due to U.S. Export Regulations, all cryptographic software on this
230-site is subject to the following legal notice:
230-
230-    This site includes publicly available encryption source code
230-    which, together with object code resulting from the compiling of
230-    publicly available source code, may be exported from the United
230-    States under License Exception "TSU" pursuant to 15 C.F.R. Section
230-    740.13(e).
230-
230-This legal notice applies to cryptographic software only. Please see
230-the Bureau of Industry and Security (www.bxa.doc.gov) for more
230-information about current U.S. regulations.
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> cd gnu/diction
250 Directory successfully changed.
ftp> ls
229 Entering Extended Passive Mode (|||29434|)
150 Here comes the directory listing.
-rw-r--r--    1 3003     65534       68940 Aug 28  1998 diction-0.7.tar.gz
-rw-r--r--    1 3003     65534       90957 Mar 04  2002 diction-1.02.tar.gz
-rw-r--r--    1 3003     65534      141062 Sep 17  2007 diction-1.11.tar.gz
-rw-r--r--    1 3003     65534         189 Sep 17  2007 diction-1.11.tar.gz.sig
226 Directory send OK.
ftp> get diction-1.11.tar.gz
local: diction-1.11.tar.gz remote: diction-1.11.tar.gz
229 Entering Extended Passive Mode (|||22347|)
150 Opening BINARY mode data connection for diction-1.11.tar.gz (141062 bytes).
100% |**********************************************************************|   137 KiB  217.09 KiB/s    00:00 ETA
226 Transfer complete.
141062 bytes received in 00:00 (164.88 KiB/s)
ftp> bye
221 Goodbye.
                                                                                                                   
┌──(kali㉿kali)-[~/src]
└─$ ls                                   
diction-1.11.tar.gz

```

>[!tip] 저자주
>우리는 이 소스 코드의 메인테이너이기 때문에 컴파일하는 동안 ~/src에 그 소스 코드를 유지할 것이다. 배포판에 의해 설치된 소스 코드는 /usr/src에 위치하고, 복수의 사용자를 위해 사용될 코드는 항상 /usr/local/src에 위치한다.

이처럼 소스 코드는 항상 압축된 tar 파일 형태로 제공된다. 때때로 **타르볼**(tarball)이라 부르면, **소스 트리** 혹은 소스 코드로 구성된 파일과 디렉토리 계층을 포함하고 있다. FTP 사이트에 연결 후, 가용한 tar 파일들의 목록을 확인하고 최신 버전의 파일을 선택하여 다운로드한다. ftp에서 get 명령어를 사용하여, FTP 서버에서 사용자 컴퓨터로 파일을 복사한다.

tar 파일이 다운로드되면 압축 해제해야 한다. 다음과 같이 tar 프로그램을 사용하면 된다.

``` shell
                                                                                                                   
┌──(kali㉿kali)-[~/src]
└─$ tar xzf diction-1.11.tar.gz                                              
                                                                                                                   
┌──(kali㉿kali)-[~/src]
└─$ ls
diction-1.11  diction-1.11.tar.gz
                                      
```

>[!tip] 저자주
> diction 프로그램은 다른 GNU 프로젝트 소프트웨어처럼 소스 코드 패키징의 표준을 다룬다. 리눅스 환경에서 가용한 대부분의 소스 코드 또한 이 표준을 따른다. 이 표준의 한 요건으로, 소스 코드 tar 파일이 압축 해제될 때 소스 트리를 포함한 디렉토리가 생성된다. 이 디렉토리는 project-x.xx와 같이 프로젝트의 이름과 버전 번호를 명기한 형태로 이름 지어진다. 이러한 체계는 동일한 프로그램의 다양한 버전을 설치하기 쉽게 한다. 하지만 소스 코드를 압축 해제하기 전에 트리의 구조를 자주 확인하는 것도 좋은 생각이다. 일부 프로젝트들은 디렉토리를 생성하지 않고 대신에 직접 현재 디렉토리에 파일을 전달하는 경우도 있다. 이는 잘 조직된 src 디렉토리를 어질러 놓을 것이다. 이를 피하기 위해서는 다음과 같이 명령어를 사용하여 tar 파일의 내용을 확인해야 한다.

`tar tzvf tarfile | head`



### 소스 트리 확인하기

tar 파일을 푼 결과로 diction-1.11이라는 새 디렉토리가 생성된다. 이 디렉토리는 소스 트리를 포함하고 있다. 한번 살펴보자.

```shell
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ cd src         
                                                                                                                   
┌──(kali㉿kali)-[~/src]
└─$ cd diction-1.11
                                                                                                                   
┌──(kali㉿kali)-[~/src/diction-1.11]
└─$ ls
config.guess  configure.in  diction.1.in  diction.spec.in  en_GB.po   getopt_int.h  misc.c  nl.po       style.1.in
config.h.in   COPYING       diction.c     diction.texi.in  getopt1.c  INSTALL       misc.h  README      style.c
config.sub    de            diction.pot   en               getopt.c   install-sh    NEWS    sentence.c  test
configure     de.po         diction.spec  en_GB            getopt.h   Makefile.in   nl      sentence.h

```

그 안에는 많은 파일들이 보인다. 다른 것들뿐만 아니라 GNU 프로젝트에 속한 프로그램들은 README, INSTALL, NEWS, COPYINGS과 같은 문서 파일들을 제공할 것이다. ==이 파일들은 프로그램을 설명하고, 빌드 및 설치 방법과 해당 라이선스 조항에 대한 정보를 포함하고 있다.== 프로그램을 빌드하기 전에 항상  README와 INSTALL 파일을 주의 깊게 읽어보는 것도 좋은 생각이다.

이 디렉토리에서 살펴봐야 할 다른 파일들은 .c와 .h로 끝나는 것들이다.

``` shell
┌──(kali㉿kali)-[~/src/diction-1.11]
└─$ ls *.c                               
diction.c  getopt1.c  getopt.c  misc.c  sentence.c  style.c
                                                                                                                   
┌──(kali㉿kali)-[~/src/diction-1.11]
└─$ ls  *.h
getopt.h  getopt_int.h  misc.h  sentence.h

```

.c 파일들에는 패키지에 의해 제공된 모듈로 나뉜 두 개(style과 diction)의 C 프로그램이 포함되어 있다. 일반적으로 큰 프로그램들은 관리하기 더 쉬운 작은 조각으로 나뉜다. 소스 코드 파일은 일반 텍스트이고, [[less]] 명령으로 확인할 수 있다.


``` shell
┌──(kali㉿kali)-[~]
└─$ less diction.c


```

**헤더 파일**로 알려진 .h 파일들 또한 평범한 텍스트 파일이다. 헤더 파일은 소스 코드나 라이브러리에 포함된 루틴에 대한 설명을 가지고 있다. 컴파일러는 프로그램을 완성하기 위해 필요한 모든 모듈의 정보를 받아야 하고 이로 모듈을 연결한다. diction.c 파일 시작 부근에서 다음과 같은 라인이 있다.

``` c
#include "getopt.h"
```

이것은 getopt.c에 정의된 무언가를 "알기 위해" 컴파일러에 diction.c의 소스 코드를 처리하는 동안 getopt.h 파일을 읽으라고 지시한다. getopt.c 파일은 style과 diction 프로그램이 모두 공유하는 루틴을 제공한다.

getopt.h `include` 문 위에서 또 다른 `include`문을 다음처럼 보게 된다.

``` shell
#include <regex.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

```

이 또한 헤더 파일을 참조하는 것이지만, 앞에서와 달리 현재 소스 트리 바깥쪽에 위치한 헤더 파일을 참조한다. 이 헤더들은 모든 프로그램의 컴파일을 위해 시스템에서 제공하는 것이다. `/usr/include`를 살펴보면 볼 수 있다.


``` shell
┌──(kali㉿kali)-[~/src/diction-1.11]
└─$ ls /usr/include   
```

컴파일러를 설치할 때 이 디렉토리의 헤더 파일들이 설치되었다.



### 프로그램 빌드하기

대부분의 프로그램은 다음 두 명령어로 간단히 빌드된다.


`./configure`
`make`


configure 프로그램은 소스 트리와 함께 제공된 쉘 스크립트이며 빌드 환경을 분석하는 역할을 한다. 대부분의 소스 코드는 이식 가능하게 설계된다. 즉 유닉스형 시스템 중 하나 이상의 시스템에서 빌드할 수 있도록 설계된다는 것이다. 하지만 그렇게 하기 위해서 빌드하는 동안 소스 코드에 약간의 수정이 필요할지도 모른다. 각 시스템들 간의 차이점을 수용하기 위해서 말이다. ==또한 **configure**는 필수적인 외부 툴과 컴포넌트가 설치되어 있는지를 검사한다.==

configure를 실행해보자. configure는 쉘이 일반적으로 예상하는 프로그램들이 위치에 있지 않기 때문에, 반드시 `./`와 함께 명령어를 사용해야 한다. 이것은 해당 프로그램이 현재 작업 디렉토리에 있다는 것을 나타낸다.

``` shell
┌──(kali㉿kali)-[~/src/diction-1.11]
└─$ ./configure

```

configure는 빌드에 관한 설정과 테스트로 많은 메시지를 출력한다. 작업이 완료되면 다음과 같은 결과를 나타낼 것이다.

``` shell
┌──(kali㉿kali)-[~/src/diction-1.11]
└─$ ./configure
checking build system type... x86_64-unknown-linux-gnu
checking host system type... x86_64-unknown-linux-gnu
checking for gcc... gcc
checking for C compiler default output file name... a.out
checking whether the C compiler works... yes
checking whether we are cross compiling... no
checking for suffix of executables... 
checking for suffix of object files... o
checking whether we are using the GNU C compiler... yes
checking whether gcc accepts -g... yes
checking for gcc option to accept ISO C89... none needed
checking for a BSD-compatible install... /usr/bin/install -c
checking for strerror... yes
checking for library containing regcomp... none required
checking for broken realloc... no
checking for msgfmt... no
configure: creating ./config.status
config.status: creating Makefile
config.status: creating diction.1
config.status: creating diction.texi
config.status: creating diction.spec
config.status: creating style.1
config.status: creating test/rundiction
config.status: creating config.h

```

여기서 중요한 것은 아무런 에러 메시지가 없어야 한다는 것이다. 만약 에러 메시지가 있다면 설정 과정이 실패한 것이다. 그리고 프로그램은 에러가 수정될 때까지 빌드할 수 없다.

configure는 소스 디렉토리에 몇 가지 새 파일들을 생성했다. 이 중 중요한 것은 Makefile이다. Makefile은 make 프로그램이 정확히 어떻게 프로그램을 빌드하는지 알려주는 설정 파일이다. 그 파일이 없다면 make는 실행되지 않을 것이다. Makefile은 일반 텍스트 파일이기 때문에 다음과 같이 확인할 수 있다.

``` shell
┌──(kali㉿kali)-[~/src/diction-1.11]
└─$ less Makefile  
```


make 프로그램은 최종 프로그램으로 구성되는 요소들 간의 관계와 의존성을 기술한 makefile(일반적으로 Makefile이란 이름으로)을 입력받는다.

makefile의 첫 부분은 makefile의 뒷부분의 섹션에서 치환될 변수를 정의한다. 예를 들면 다음과 같다.
``` shell
CC=             gcc

```

C컴파일러가 gcc가 정의되었다. makefile 뒷부분에서 이 변수가 사용되는 부분을 보게 된다.

``` shell
diction:        diction.o sentence.o misc.o getopt.o getopt1.o
                $(CC) -o $@ $(LDFLAGS) diction.o sentence.o misc.o \
                getopt.o getopt1.o $(LIBS)

```
여기서 치환이 일어나고, 실행 시에 `$(CC)`의 값은 gcc로 대체된다.

makefile의 대부분은 **타겟**(target)과 타겟 생성에 의존적인 파일들의 정의로 라인을 생성한다. 이 예제에서는 diction 실행파일이 타겟이 된다. 나머지 라인은 그 요소들로부터 타겟을 생성하는 데 필요한 명령들을 기술한다. 이 예제에서 실행파일 diction(최종 제품 중 하나)은 diction.o, sentence.o, misc.o, getopt.o, getoptl.o의 존재 여부에 의존적이다. makefile의 뒷부분에서 이들 또한 각각 타겟으로 정의된 것을 볼 수 있다.

```shell
diction.o:      diction.c config.h getopt.h misc.h sentence.h
getopt.o:       getopt.c getopt.h getopt_int.h
getopt1.o:      getopt1.c getopt.h getopt_int.h
misc.o: misc.c config.h misc.h
sentence.o:     sentence.c config.h misc.h sentence.h
style.o:        style.c config.h getopt.h misc.h sentence.h

```

하지만 그것들을 지정하는 어떤 명령어도 보이지 않는다. 그것은 일반적인 타겟으로 취급되어, 이 파일 초기에 모든 .c 파일을 .o 파일로 컴파일하는데 사용하는 명령어가 기술되어 있기 때문이다.

```shell
.c.o:
                $(CC) -c $(CPPFLAGS) $(CFLAGS) $<

```

이것은 매우 복잡한 것처럼 보인다. 왜 각 부분을 컴파일하고 완료하기 위해 모든 단계를 간단하게 나열하지 않는가? 그 답은 곧 명확해진다. 그럼 프로그램 make로 빌드해보자.

```shell

┌──(kali㉿kali)-[~/src/diction-1.11]
└─$ make     
gcc -c -I. -DSHAREDIR=\"/usr/local/share\" -DLOCALEDIR=\"/usr/local/share/locale\" -g -O2 -pipe -Wno-unused -Wshadow -Wbad-function-cast -Wmissing-prototypes -Wstrict-prototypes -Wcast-align -Wcast-qual -Wpointer-arith -Wcast-align -Wwrite-strings -Wmissing-declarations -Wnested-externs -Wundef -pedantic -fno-common diction.c
gcc -c -I. -DSHAREDIR=\"/usr/local/share\" -DLOCALEDIR=\"/usr/local/share/locale\" -g -O2 -pipe -Wno-unused -Wshadow -Wbad-function-cast -Wmissing-prototypes -Wstrict-prototypes -Wcast-align -Wcast-qual -Wpointer-arith -Wcast-align -Wwrite-strings -Wmissing-declarations -Wnested-externs -Wundef -pedantic -fno-common sentence.c
gcc -c -I. -DSHAREDIR=\"/usr/local/share\" -DLOCALEDIR=\"/usr/local/share/locale\" -g -O2 -pipe -Wno-unused -Wshadow -Wbad-function-cast -Wmissing-prototypes -Wstrict-prototypes -Wcast-align -Wcast-qual -Wpointer-arith -Wcast-align -Wwrite-strings -Wmissing-declarations -Wnested-externs -Wundef -pedantic -fno-common misc.c
gcc -c -I. -DSHAREDIR=\"/usr/local/share\" -DLOCALEDIR=\"/usr/local/share/locale\" -g -O2 -pipe -Wno-unused -Wshadow -Wbad-function-cast -Wmissing-prototypes -Wstrict-prototypes -Wcast-align -Wcast-qual -Wpointer-arith -Wcast-align -Wwrite-strings -Wmissing-declarations -Wnested-externs -Wundef -pedantic -fno-common getopt.c
gcc -c -I. -DSHAREDIR=\"/usr/local/share\" -DLOCALEDIR=\"/usr/local/share/locale\" -g -O2 -pipe -Wno-unused -Wshadow -Wbad-function-cast -Wmissing-prototypes -Wstrict-prototypes -Wcast-align -Wcast-qual -Wpointer-arith -Wcast-align -Wwrite-strings -Wmissing-declarations -Wnested-externs -Wundef -pedantic -fno-common getopt1.c
gcc -o diction -g diction.o sentence.o misc.o \
        getopt.o getopt1.o 
gcc -c -I. -DSHAREDIR=\"/usr/local/share\" -DLOCALEDIR=\"/usr/local/share/locale\" -g -O2 -pipe -Wno-unused -Wshadow -Wbad-function-cast -Wmissing-prototypes -Wstrict-prototypes -Wcast-align -Wcast-qual -Wpointer-arith -Wcast-align -Wwrite-strings -Wmissing-declarations -Wnested-externs -Wundef -pedantic -fno-common style.c
gcc -o style -g style.o sentence.o misc.o \
        getopt.o getopt1.o -lm 
                                      
```

make의 동작을 설명하기 위해 Makefile의 내용을 사용하여 프로그램이 실행될 것이다. 많은 메시지를 출력할 것이다.

작업이 완료되었을 때, 이제 모든 대상이 디렉토리에 나타나게 될 것이다.

```shell
┌──(kali㉿kali)-[~/src/diction-1.11]
└─$ ls             
config.guess   configure.in  diction.c        en         getopt_int.h  misc.h      sentence.h  test
config.h       COPYING       diction.o        en_GB      getopt.o      misc.o      sentence.o
config.h.in    de            diction.pot      en_GB.po   INSTALL       NEWS        style
config.log     de.po         diction.spec     getopt1.c  install-sh    nl          style.1
config.status  diction       diction.spec.in  getopt1.o  Makefile      nl.po       style.1.in
config.sub     diction.1     diction.texi     getopt.c   Makefile.in   README      style.c
configure      diction.1.in  diction.texi.in  getopt.h   misc.c        sentence.c  style.o

```

생성된 파일들 중에 diction과 style도 보인다. 그 프로그램들을 우리가 빌드를 위해 설정한 것이다. 축하한다! 우리는 막 소스 코드로부터 첫 프로그램을 컴파일했다.

그런데 호기심이 생긴다. 다시 한번 make를 실행해보자.

``` shell
┌──(kali㉿kali)-[~/src/diction-1.11]
└─$ make
make: Nothing to be done for 'all'.

```

이런 이상한 메시지가 출력된다. 무슨 일이지? 왜 다시 프로그램을 빌드하지 않는 걸까? 아, 이것은 바로 make의 마법이다. make는 모든 것을 간단히 다시 빌드하기보다 필요한 것만 빌드한다. 모든 타겟이 존재하기에 make는 아무것도 할 것이 없다고 결정했다. 타겟 중 하나를 삭제하고 다시 make를 실행하여 어떻게 동작하는지 살펴볼 것이다.

``` shell
┌──(kali㉿kali)-[~/src/diction-1.11]
└─$ rm getopt.o        
                                                                                                                   
┌──(kali㉿kali)-[~/src/diction-1.11]
└─$ make
gcc -c -I. -DSHAREDIR=\"/usr/local/share\" -DLOCALEDIR=\"/usr/local/share/locale\" -g -O2 -pipe -Wno-unused -Wshadow -Wbad-function-cast -Wmissing-prototypes -Wstrict-prototypes -Wcast-align -Wcast-qual -Wpointer-arith -Wcast-align -Wwrite-strings -Wmissing-declarations -Wnested-externs -Wundef -pedantic -fno-common getopt.c
gcc -o diction -g diction.o sentence.o misc.o \
        getopt.o getopt1.o 
gcc -o style -g style.o sentence.o misc.o \
        getopt.o getopt1.o -lm 

```

==make는 삭제된 모듈에 의존적이기 때문에 getopt.o를 다시 빌드하고 diction과 style을 재링크시킨다.== ==이는 make의 또 다른 중요 기능으로 타겟을 항상 최신으로 유지시키는 행동이다.== make는 타겟이 의존 파일보다 최신이 되도록 한다. 프로그래머는 종종 소스 코드의 일부를 갱신하여 새 버전의 제품을 빌드하기 위해 make를 사용하기 때문에 이는 전적으로 맞는 말이다. make는 갱신된 코드의 빌드 여부를 가지고 빌드가 필요한 모든 것을 확인한다. 만약 [[touch]] 프로그램으로 소스 파일 중 하나를 "갱신"하면 다음과 같은 결과를 볼 수 있다.

```shell
┌──(kali㉿kali)-[~/src/diction-1.11]
└─$ ls -l diction getopt.c               
-rwxrwxr-x 1 kali kali 52280 Oct  5 07:47 diction
-rw-r--r-- 1 kali kali 33125 Mar 30  2007 getopt.c
                                                                                                                   
┌──(kali㉿kali)-[~/src/diction-1.11]
└─$ touch getopt.c                                           
                                                                                                                   
┌──(kali㉿kali)-[~/src/diction-1.11]
└─$ ls -l diction getopt.c
-rwxrwxr-x 1 kali kali 52280 Oct  5 07:47 diction
-rw-r--r-- 1 kali kali 33125 Oct  5 07:51 getopt.c
                                                                                                                   
┌──(kali㉿kali)-[~/src/diction-1.11]
└─$ make
gcc -c -I. -DSHAREDIR=\"/usr/local/share\" -DLOCALEDIR=\"/usr/local/share/locale\" -g -O2 -pipe -Wno-unused -Wshadow -Wbad-function-cast -Wmissing-prototypes -Wstrict-prototypes -Wcast-align -Wcast-qual -Wpointer-arith -Wcast-align -Wwrite-strings -Wmissing-declarations -Wnested-externs -Wundef -pedantic -fno-common getopt.c
gcc -o diction -g diction.o sentence.o misc.o \
        getopt.o getopt1.o 
gcc -o style -g style.o sentence.o misc.o \
        getopt.o getopt1.o -lm 
                                                                                                                   
┌──(kali㉿kali)-[~/src/diction-1.11]
└─$ ls -l diction getopt.c
-rwxrwxr-x 1 kali kali 52280 Oct  5 07:51 diction
-rw-r--r-- 1 kali kali 33125 Oct  5 07:51 getopt.c

```

빌드가 필요한 것만 자동적으로 빌드하는 make의 능력은 프로그래머에게는 큰 혜택이다. 이처럼 소형 프로젝트에서는 시간 절약의 효과가 나타나지 않을지도 모르지만, 대형 프로젝트에서는 의미가 있다. 리눅스 커널(지속적으로 수정과 개선 중인 프로그램이다)은 **수백만** 줄의 코드를 가지고 있다는 것을 상기해봐라.

### 프로그램 설치하기

잘 패키징된 소스 코드는 종종 [[install]]이라 부르는 특별한 make 타겟을 가지고 있다. 이 타겟은 시스템 디렉토리에 최종 사용 제품을 설치할 것이다. 주로 이 디렉토리는 /usr/local/bin이고 전통적으로 빌드된 소프트웨어가 위치하는 곳이다. 하지만 이 디렉토리는 일반적으로 사용자에게 쓰기 권한이 없다. 그래서 반드시 슈퍼유저로 설치를 진행해야 한다.

``` shell
                                                                                                                  
┌──(kali㉿kali)-[~/src/diction-1.11]
└─$ sudo make install
[ -d /usr/local/bin ] || /usr/bin/install -c -m 755 -d /usr/local/bin
/usr/bin/install -c diction /usr/local/bin/diction
/usr/bin/install -c style /usr/local/bin/style
/usr/bin/install -c -m 755 -d /usr/local/share/diction
/usr/bin/install -c -m 644 ./de /usr/local/share/diction/de
/usr/bin/install -c -m 644 ./en /usr/local/share/diction/en
(cd /usr/local/share/diction; rm -f C; ln en C)
/usr/bin/install -c -m 644 ./en_GB /usr/local/share/diction/en_GB
/usr/bin/install -c -m 644 ./nl /usr/local/share/diction/nl
[ -d /usr/local/share/man/man1 ] || /usr/bin/install -c -m 755 -d /usr/local/share/man/man1
/usr/bin/install -c -m 644 diction.1 /usr/local/share/man/man1/diction.1
/usr/bin/install -c -m 644 style.1 /usr/local/share/man/man1/style.1
make install-po-no
make[1]: Entering directory '/home/kali/src/diction-1.11'
make[1]: Nothing to be done for 'install-po-no'.
make[1]: Leaving directory '/home/kali/src/diction-1.11'

```
설치 후에 실행 준비된 프로그램을 확인할 수 있다.

```																												   
┌──(kali㉿kali)-[~/src/diction-1.11]
└─$ which diction
/usr/local/bin/diction
                                                                                                                   
┌──(kali㉿kali)-[~/src/diction-1.11]
└─$ man diction

```

자, 이제 우리는 새 프로그램을 갖게 되었다.

---
# 마무리 노트

이 장에서는 소스 코드 패키지를 빌드하기 위해 세 가지 간단한 명령어(./configure, make, make install)가 어떻게 이용되는지를 살펴보았다. 또한 make가 프로그램들을 유지하는 중요한 규칙을 보았다. Make 프로그램은 꼭 소스 코드 컴파일뿐만 아니라 타겟/의존 관계를 유지하기 위한 어떠한 작업에도 사용될 수 있다.