
### 테이프 아카이빙 유틸리티

# 파일 보관하기(아카이빙)

압축 작업과 함께 주로 사용되는 파일 관리 작업은 **파일 보관**(archiving) 작업이다. 아카이빙이란 많은 파일들을 모아서 하나의 큰 파일로 묶는 과정을 말한다. 아카이빙 시스템 백업의 일환으로 종종 수행되는 작업이다. 또한 일종의 장기 보관용 저장 장치에 오래된 데이터를 옮길 때 필요한 작업이다.


유닉스 세상에서 tar 프로그램은 파일 보관을 위한 전통적인 툴이다. 그 이름은 tape archive의 준말로, 백업 테이프를 만들기 위한 도구에서 유래되었음을 알 수 있다. 전통적인 파일 보관 작업 외에도 다른 저장 장치에서도 잘 사용된다. .tar이나 .tgz를 확장자로 가진 파일을 종종 보게 되는데 이것은 각각 "일반적인" tar 아카이브와 gzip으로 압축된 아카이브라는 것을 뜻한다. tar 아카이브는 여러 파일로 구성된 그룹이거나, 하나 이상의 디렉토리 또는 이 두 가지가 혼합된 형태일 수 있다.


`tar mode[options] pathname ...`\

mode에는 표[18-2]에 나와 있는 명령 모드 중 하나를 사용한다(표에는 일부만 설명되어 있다. 전체의 내용을 볼려면 tar의 man 페이지를 참고하라).


- [표 18-2] tar 모드


| 모드  | 설명                           |
| --- | ---------------------------- |
| c   | 파일과(또는) 디렉토리의 목록에서 아카이브 생성하기 |
| x   | 아카이브 해제하기                    |
| r   | 아카이브 끝에 지정된 경로명을 덧붙이기        |
| t   | 아카이브 내용 보기                   |

tar는 옵션을 사용하는 방식이 조금 특이하므로 어떻게 사용하는지 몇 가지 예를 통해 알아보도록 하자. 우선, 이전 장에서 만들었던 놀이터를 다시 구성해보자.

```

┌──(kali㉿kali)-[~/playground/dir-001]
└─$ touch playground/dir-{00{1..9},0{10..99},100}/file-{A..Z}  
┌──(kali㉿kali)-[~]
└─$ touch playground/dir-{00{1..9},0{10..99},100}/file-{A..Z} 

```

그런 다음, 놀이터 전체를 tar 아카이브로 만들어보자.

``` shell
┌──(kali㉿kali)-[~]
└─$ tar cf playground.tar playground
                                                   
```

이 명령으로 playground.tar라는 이름의 tar 파일을 생성한다. 이 파일에는 놀이터 디렉토리에 있는 모든 디렉토리 트리가 포함된다. 여기서 우리는 모드와 f 옵션을 볼 수 있다. f 옵션은 tar 아카이브의 이름을 지정하고, 이와 같이 붙여서 사용하고 대시 기호가 필요하지 않는다. 하지만 항상 모드는 반드시 다른 옵션 앞에 명시되어야 한다.

아카이브 내용을 보기 위해서 다음과 같이 할 수 있다.

``` shell
┌──(kali㉿kali)-[~]
└─$ tar cf playground.tar playground
                                        
```

더 자세하게 내용을 표시하고 있다 v(verbose) 옵션이 필요하다.

``` shell
┌──(kali㉿kali)-[~]
└─$ tar tvf playground.tar playground
drwx------ kali/kali         0 2024-09-23 05:36 playground/
drwx------ kali/kali         0 2024-09-24 06:43 playground/dir-089/
-rw------- kali/kali         0 2024-09-24 06:43 playground/dir-089/file-G
-rw------- kali/kali         0 2024-09-24 06:43 playground/dir-089/file-U
-rw------- kali/kali         0 2024-09-24 06:43 playground/dir-089/file-T
-rw------- kali/kali         0 2024-09-24 06:43 playground/dir-089/file-D
-rw------- kali/kali         0 2024-09-24 06:43 playground/dir-089/file-M
-rw------- kali/kali         0 2024-09-24 06:43 playground/dir-089/file-H
-rw------- kali/kali         0 2024-09-24 06:43 playground/dir-089/file-X
-rw------- kali/kali         0 2024-09-24 06:43 playground/dir-089/file-E
-rw------- kali/kali         0 2024-09-24 06:43 playground/dir-089/file-K
-rw------- kali/kali         0 2024-09-24 06:43 playground/dir-089/file-Z
-rw------- kali/kali         0 2024-09-24 06:43 playground/dir-089/file-R
-rw------- kali/kali         0 2024-09-24 06:43 playground/dir-089/file-J
-rw------- kali/kali         0 2024-09-24 06:43 playground/dir-089/file-A
-rw------- kali/kali         0 2024-09-24 06:43 playground/dir-089/file-B
-rw------- kali/kali         0 2024-09-24 06:43 playground/dir-089/file-F
-rw------- kali/kali         0 2024-09-24 06:43 playground/dir-089/file-Q
-rw------- kali/kali         0 2024-09-24 06:43 playground/dir-089/file-S
-rw------- kali/kali         0 2024-09-24 06:43 playground/dir-089/file-V

```

이제 놀이터 아카이브 파일을 새로운 위치에서 풀어보도록 하자. foo라는 이름의 새로운 디렉토리르 만들고 현재 작업 디렉토리를 새로 생성한 foo 디렉토리로 변경한 뒤 놀이터 아카이브 파일을 풀어보자.

``` shell
┌──(kali㉿kali)-[~]
└─$ mkdir foo                                       
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ cd foo   
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~/foo]
└─$ tar xf ../playground.tar         
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~/foo]
└─$ ls           
playground

```

`~/foo/playground` 내용을 확인하려면, 아카이브가 성공적으로 설치되고 원본 파일들이 정확하게 생성되는지를 보면 된다. 하지만 한 가지 주의할 점은 슈퍼유저가 아닌, 아카이브에서 생성된 파일과 디렉토리들에 대해서는 원 소유자가 아닌 아카이브를 해제한 사용자의 소유가 된다.

tar의 또 다른 흥미로운 부분은 아카이브의 경로명을 다루는 방법이다. 기본적인 경로명은 절대 경로명이 아닌 상대 경로명이다. ==tar는 아카이브가 생성될 때 앞에 오는 모든 슬래시 기호를 삭제함으로써 상대 경로명을 사용할 수 있다.== 이를 확인하기 위해서 아카이브 파일을 다시 만든 후, 이번에는 절대 경로명을 지정해보자.

``` shell
┌──(kali㉿kali)-[~]
└─$ tar cf playground2.tar ~/playground
tar: 구성 요소 이름 앞에 붙은 `/' 제거 중
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ dir
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos  foo  foo.txt  playground  playground.tar  playground2.tar

```

여기서 엔터를 입력하는 경우 ~/playground 경로명은 /home/me/playground로 확장될 것이다. ==따라서 우리는 시현용 절대 경로명을 얻을 것이다.== 그런 다음, 앞에서와 같이 아카이브를 해제하고 어떠한 일이 벌어지는지 확인해보자.

``` shell
                                                                                                                                                             
┌──(kali㉿kali)-[~]
└─$ cd foo
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~/foo]
└─$ tar xf ../playground2.tar          
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~/foo]
└─$ ls          
home
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~/foo]
└─$ ls home     
kali
                                                                                                                                                                                                                                                                                                                                               
┌──(kali㉿kali)-[~/foo]
└─$ ls home/kali
playground


```

여기서 우리가 두 번째 아카이브를 해제할 때, 루트 디렉토리에 상대적 경로가 아닌 현재 작업 디렉토리 `~/foo`에 상대적 방식으로 마치 절대 경로명을 가진 경우처럼 `/home/kali/playround` 디렉토리가 다시 생성된다. 다소 이상한 방식이긴 하지만 사실 더 유용한 방식이다. 왜냐하면 기존 위치가 아니더라도 다른 위치 어디에서든 아카이브를 해제할 수 있기 때문이다. v 옵션을 사용해서 다시 이 작업을 해 보면 보다 구체적으로 그 내용을 이해할 수 있을 것이다.

여기서 tar 예제를 통해 한번 가설을 세워보자. 우리는 지금 홈 디렉토리와 그 안의 모든 자료를 다른 시스템으로 복사하고 싶다. 그리고 대용량의 USB 하드 드라이브 자료를 옮기는 데 사용할 것이다. 최신 리눅스 시스템에서는 드라이브 `/media` 디렉토리로 자동적으로 마운트된다. 또한 이 디스크의 볼륨명은 BigDisk라고 하자. 이제 tar 아카이브를 만들기 위해 다음과 같이 할 수 있다.

``` shell
┌──(kali㉿kali)-[~/foo]
└─$ sudo tar cf /media/BigDisk/home.tar /home
```


tar 파일을 만들고 나 후에 드라이브를 마운트 해제하고 다른 시스템에 드라이브를 연결한다. 다시 /media/BigDisk 디렉토리에 마운트될 것이다. 이 아카이브 파일을 해제하기 위해서 다음과 같이 해보자.

```shell

┌──(kali㉿kali)-[~/foo]
└─$ cd /

┌──(kali㉿kali)-[~/]
└─$ sudo tar xf /media/BigDisk/home.tar
```

여기서 우리가 중요하게 봐야 할 것은, 맨 처음 디렉토리를 /로 변경해야 한다는 것이다. 그래야만 아카이브 해제가 root 디렉토리에 상대적으로 이루어진다. 아카이브 내의 모든 경로명이 상태 경로명이기 때문이다.

아카이브를 해제할 때, 해제할 대상을 제한하는 것도 가능하다. 예를 들면, 아카이브에서 하나의 파일만 풀고 싶다면 다음과 같이 하면 된다.

``` shell
tar xf archive.tar pathname
```

*pathname*을 명령어로 끝에 덧붙임으로써 tar 프로그램이 지정된 파일을 복구하게끔 할 수 있다. 또한 여러 경로명을 지정할 수 있다. 하지만 지정할 경로명은 반드시 아카이브에 저장된 대로, 정확히 일치하는 상대 경로명으로 표현되어야 한다. 경로명을 지정할 때, 와일드카드를 사용할 수 없다.==하지만 tar의 GNU 버전(리눅스 배포판에서 가장 많이 사용되는 버전)은 --wildcards 옵션을 지원하기 때문에 다음에서 이 옵션을 사용한 예제를 확인해보자.==


``` shell
┌──(kali㉿kali)-[~]
└─$ cd foo
                                                                                                                   
┌──(kali㉿kali)-[~/foo]
└─$ dir                                          
home
                                                                                                                   
┌──(kali㉿kali)-[~/foo]
└─$ tar xf ../playground2.tar --wildcards 'home/kali/playground/dir-*/file-A'

```


이 명령은 `dir-*` 와일드카드를 포함하고 있는 앞의 경로명과 일치하는 파일만을 해제한다.

tar는 종종 아카이브를 생성하기 위해 find 명령어와 함께 사용된다. 다음의 예제에서 find 명령어를 사용하여 아카이브에 포함시킬 파일들을 생성해 보도록 하자.

``` shell
┌──(kali㉿kali)-[~]
└─$ find playground -name 'file-A' -exec tar rf playground.tar '{}' '+'

```

여기서 playground에 있는 file-A라는 이름과 일치하는 모든 파일을 찾기 위해 find 명령어를 사용한다. 그런 다음 -exec 액션을 통해 일치하는 파일들을 playground.tar 아카이브에 추가하기 위해 tar를 append 모드(r)로 실행하였다.

tar를 find와 함께 사용하는 것은 디렉토리 트리나 시스템 전체에 대한 **증분 백업**을 만드는 데 있어 좋은 방법이다. 특정 시간 이후에 변경된 파일을 find로 찾아서 그 새로운 파일들만 최근 아카이브 뒤에 붙이기만 하면 되기 때문이다. 여기서 특정 시간 즉 타임스탬프 파일은 아카이브가 생성된 시점으로 항상 갱신된다고 가정을 한다.

``` shell
┌──(kali㉿kali)-[~]
└─$ find playground -name 'file-A' | tar cf - --files-from=- | gzip > playground.tgz

```

find 프로그램을 이용하여 일치하는 파일 목록을 만들고 그것을 tar로 연결하였다. 필요에 따라 만약 파일명이 -로 지정되면, 그것은 표준 입력 혹은 표준 출력을 의미한다(- 기호를 표준 입출력을 나타내는 기호로 사용하는 관습은 다른 다수의 프로그램에서도 동일하다). `--files-from` 옵션(`-T`로도 쓰임)은 tar 프로그램이 커맨드라인이 아닌 파일에서 직접 경로명을 읽어오기도 한다. 그 다음, tar로 생성된 아카이브 gzip에 연결되어 `playground.tgz`라는 압축 아카이브 파일로 생성한다. .tgz 확장자는 gzip으로 압축된 tar 파일이라는 것이다. 또한 .tar.gz 확장자로도 쓰인다.

압축 아카이브를 생성하기 위해 gzip 프로그램을 외부적으로 사용했었지만, GNU tar의 최신 버전은 각각 z와 j 옵션을 사용하여 gzip과 bzip2 압축 프로그램 둘 다 지원한다. 이 예제를 기초로 다음과 같이 명령을 단순화 시켜보자.

``` shell

┌──(kali㉿kali)-[~]
└─$ find playground -name 'file-A' | tar czf playground.tgz -T -   
```

gzip 대신에 bzip2 압축 아카이브를 만드는 것은 다음과 같이 할 수 있다.

``` shell

┌──(kali㉿kali)-[~]
└─$ find playground -name 'file-A' | tar cjf playground.tbz -T -
                                                                        
```

옵션을 z에서 j로(bzip로 압축된 파일임을 나타내기 위해 출력 파일의 확장자는 .tbz로 변경한다) 바꾸면 bzip2 압축이 실행된다.

tar 프로그램으로 입출력을 활용한 또 다른 예로는, 네트워크를 통해 시스템 간에 파일 전송을 하는 것이다. tar와 ssh 프로그램이 설치된 동작 중인 유닉스형 시스템이 두 대 있다고 가정해보자. 또한, 원격 시스템(이 예제에서는 remote-sys라는 이름의)으로부터 디렉토리 하나를 로컬 시스템으로 전송하려 한다고 가정하자.

(그러한 원격 시스템은 지금은 없지만 있다고 가정하겠다.)
``` shell

┌──(kali㉿kali)-[~]
└─$ mkdir remote-stuff       
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ cd remote-stuff
                                                                                                                   
┌──(kali㉿kali)-[~/remote-stuff]
└─$ ssh remote-sys 'tar cf - Documents' | tar xf -
remote-sys's password:

┌──(kali㉿kali)-[~/remote-stuff]
└─$ ls
Documents


```

원격 시스템 remote-sys에서 Documents 디렉토리를 로컬 시스템의 remote-stuff 디렉토리 안으로 복사하였다. 어떻게 된 것일까? ssh를 이용하여 원격 시스템에서 tar 프로그램을 실행하였다. ssh 프로그램은 네트워크로 연결된 컴퓨터에서 원격으로 프로그램을 실행할 수 있다는 것과 그 결과를 로컬 시스템상에서 확인할 수 있다는 것을 상기하게 될 것이다. ==원격 시스템에서 발생한 표준 출력이 로컬 시스템으로 전송되기 때문에 볼 수 있는 것이다.== ==tar로 하여금 아카이브 파일(c 모드)을 생성하고 그것을 파일(f 옵션과 대시 기호)로 보내는 대신 표준 출력으로 보내게 한다.== 그리고 ssh가 제공하는 암호화 터널을 통해 아카이브 파일이 로컬 시스템으로 전송한다. 로컬 시스템에서는 tar 프로그램을 실행하고 표준 입력(다시 f옵션과 대시 기호)에서 온 아카이브(x 모드)를 해제시킨다.

