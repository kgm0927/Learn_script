

이전 챕터: [[chapter03 시스템 살펴보기]]

- [[cp]]- 파일 및 디렉토리 복사하기

- [[mv]]- 파일 및 디렉토리 이동 그리고 이름 바꾸기

- [[mkdir]]- 디렉토리 새로 만들기

- [[rm]]- 파일 및 디렉토리 삭제하기

- [[ln]]- 하드 링크 또는 심볼릭 링크 만들기


가장 리눅스에서 빈번하게 이용된다. 또한 파일이나 디렉토리를 다룰 때 사용되는 명령어들이기도 하다.

이러한 명령어들은 GUI 환경의 파일 관리자를 통하여 쉽게 할 수 있다. 하지만 커맨드라인의 강력함과 유연성 때문에 아주 복잡한 작업은 커맨드라인 프로그램을 쓰는 것이 더욱 쉽다. 예를 들어 어떤 디렉토리에 저장된 모든 HTML 파일을 다른 디렉토리로 복사하려면 어떻게 하는가? 파일을 디렉토리로 복사할 때 새 파일이나 최신 버전의 파일만 복사하려고 하면 어떻게 하는가? 이는 파일 관리자로는 번거롭지만, 커맨드라인으로는 매우 쉽다.


`cp –u *.html destination`


---

# 와일드카드

쉘은 굉장히 많은 파일명을 사용하기에 간단하게 파일명의 그룹을 지정할 수 있도록 특수한 문자들을 지원하다. 이러한 특수 문자들을 ‘**와일드카드**(**글로빙**)’라고 한다. 이는 문자 패턴에 따라 파일명을 선택할 수 있다.


[표 4-1] 와일드카드


| 와일드카드          | 매칭 문자                      |
| -------------- | -------------------------- |
| *              | 모든 문자                      |
| ?              | 모든 하나의 문자                  |
| `[character]`  | characters 문자셋에 포함된 문자.    |
| `[!character]` | characters 문자셋에 포함되지 않은 문자 |
| `[[:class:]]`  | 지정된 문자 클래스에 포함된 문자         |

표 4-2는 가장 흔히 사용되는 문자 클래스들을 보여주고 있다.


[표 4-2] 가장 많이 사용되는 문자 클래스



| 문자 클래스    | 매칭 문자         |
| --------- | ------------- |
| [:alnum:] | 모든 알파벳과 숫자 문자 |
| [:alpha:] | 모든 알파벳 문자     |
| [:digit:] | 모든 숫자         |
| [:lower:] | 모든 소문자        |
| [:upper:] | 모든 대문자        |


와일드카드를 이용하면 아주 복잡한 선택 조건을 파일명에 사용된다 해도 가능해진다.


- [표 4-3] 와일드카드 사용예시


| 패턴                       | 매칭 문자                               |
| ------------------------ | ----------------------------------- |
| `*`                      | 모든 파일                               |
| `g*`                     | g로 시작하는 모든 파일                       |
| `b*.txt`                 | b로 시작하되 .txt 형식 파일                  |
| `Data???`                | Data로 시작하면서 뒤에 정확히 세 개의 문자만 있는 파일   |
| `[abc]*`                 | a,b,c로 시작하는 모든 파일                   |
| `BACKUP.[0-9][0-9][0-9]` | BACKUP으로 시작하면서 뒤에 정확히 세 개의 숫자로 된 파일 |
| `[[:upper:]]*`           | 대문자로 시작하는 모든 파일                     |
| `[![:digit:]]*`          | 숫자로 시작하는 모든 파일                      |
| `*[[:lower:]123]`        | 파일명이 소문자로 끝나거나 1,2,3으로 끝나는 파일       |


- ***부연설명***

Gui 환경에서도 와일드카드를 사용할 수 있다.

와일드카드가 그토록 가치가 있는 이유는 커맨드라인에서 자주 애용될 뿐만 아니라 일부 GUI 환경 파일 관리자를 통해서도 사용할 수 있기 때문이다.

==**노틸러스**(Nautilus, GNOME 파일 관리자)에서 편집-> 패턴 선택하기 메뉴로 파일을 선택하고 와일드카드 패턴을 입력하면 현재 나타난 디렉토리에서 해당 파일들이 되어 있는 것을 볼 수 있을 것이다.==

==**돌핀**(Dolphin)이나 **컨쿼러**(Konqueror, KDE 파일 관리자)의 일부 버전에서는 주소 바(location bar)에 직접 와일드카드를 입력할 수 있다. 예를 들어, /usr/bin 디렉토리에 있는 파일 중 소문자 u로 모든 파일을 보고 싶다면, 주소 바에 `/usr/bin/u*`를 입력하면 된다.==


와일드 카드는 리눅스 데스크톱 환경을 강력하게 해준 수많은 특성 중 하나이다.


---

# 놀이터를 만들어보자


먼저 한번 작업을 할 수 있는 디렉토리가 필요하다. 홈 디렉토리에 공간을 만들고 이를 playground 라고 한다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ mkdir playground

kgm0917@DESKTOP-DT2VQLB:~$ ls

playground
```


여기서 하위 디렉토리를 만들자. dir1과 dir2라는 디렉토리를 만들기 위해 작업 디렉토리를 playground로 변경한 뒤 mkdir 명령어를 만든다.


```shell
kgm0917@DESKTOP-DT2VQLB:~/playground$ mkdir dir1 dir2
```


이는 한번에 2개의 디렉토리를 생성했음을 알 수 있다.





### 파일 복사


그 다음올 파일을 복사하여 playground에 있는 일부 데이터를 가져와보자. cp명령어를 사용해서 /etc 디렉토리에 있는 passwd 파일을 현재 작업 디렉토리로 복사한다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~/playground$ cp /etc/passwd .
```


여기서 현재 작업 디렉토리를 입력할 때 단순히 끝에 마침표를 하나 넣음으로써 손쉽게 처리할 수 있음을 알 수 있다. ls 명령어를 실행하면 지금까지 작업한 파일을 볼 수 있다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~/playground$ ls -l

total 4

drwxr-xr-x 1 kgm0917 kgm0917 4096 Jan 23 11:03 dir1

drwxr-xr-x 1 kgm0917 kgm0917 4096 Jan 23 11:03 dir2

-rw-r--r-- 1 kgm0917 kgm0917 1422 Jan 23 11:03 passwd
```


이제 여기서 –v 옵션을 사용하여 복사해본다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~/playground$ cp -v /etc/passwd .

'/etc/passwd' -> './passwd’
```


[[cp]] 명령어로 다시 복사가 이루어졌지만 이번에는 어떠한 작업 없이 실행되었는지 알려주는 짧은 메시지를 덧붙여준다. 여기서 파일이 복사될 때 아무런 경고 없이 그대로 덮어쓰는 것을 알 수 있다. 이와 같은 cp 명령어를 사용한 경우에도, 리눅스는 여러분이 지금 어떠한 작업을 하고 있는지에 인지하고 있음을 가정한다.


만일 경고 메시지를 보길 원한다면 –i(interactive)옵션을 사용하라.


``` shell
kgm0917@DESKTOP-DT2VQLB:~/playground$ cp -i /etc/passwd .

cp: overwrite './passwd'?
```


이 질문에 y를 입력하면 복사가 진행되고 y가 아닌 다른 문자를 입력하면 복사가 취소된다.





### 파일 이동 및 이름 변경


passwd 를 조금 다른 이름으로 만들어 보자.


``` shell
kgm0917@DESKTOP-DT2VQLB:~/playground$ mv passwd fun
```


이번에는 이름을 바꾼 fun 파일을 돌려가면 디렉토리마다 이동시켜보자.


``` shell
kgm0917@DESKTOP-DT2VQLB:~/playground$ mv fun dir1
```

dir1 디렉토리로 이동한다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~/playground$ mv dir1/fun dir2
```

dir1에 있는 파일을 dir2으로 이동한다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~/playground$ mv dir2/fun .
```


마침내 원래 위치로 돌아왔다.(위의 형식은 상위 디렉토리의 위치로 dir2 안에 있는 fun 파일을 다시 갖다 놓는다.)

```
mv fun dir1
```


다음은 디렉터리들을 이동해 본다. 우선 데이터 파일은 dir1 디렉토리로 다시 옮긴다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~/playground$ mv dir1 dir2

kgm0917@DESKTOP-DT2VQLB:~/playground$ ls -l dir2

total 0

drwxr-xr-x 1 kgm0917 kgm0917 4096 Jan 23 12:56 dir1

kgm0917@DESKTOP-DT2VQLB:~/playground$ ls -l dir2/dir1

total 4

-rw-r--r-- 1 kgm0917 kgm0917 1422 Jan 23 11:12 fun
```


dir2 디렉토리가 이미 존재하였기에, [[mv]] 명령어로 dir1을 dir2로 이동시킬 수 있었을 것이다. 만약 dir2가 없는 디렉토리였다면, 여기서 mv명령어는 dir1의 이름을 dir2로 바꿨을 것이다. 이제 원래 상태로 돌아간다.

```
kgm0917@DESKTOP-DT2VQLB:~/playground$ mv dir2/dir1 .

kgm0917@DESKTOP-DT2VQLB:~/playground$ mv dir1/fun .

kgm0917@DESKTOP-DT2VQLB:~/playground$ ls

dir1 dir2 fun
```


### 하드 링크 생성

이제 링크에 대해서 연습해보자. 먼저 하드 링크를 만드는 데 다음과 같이 우리가 갖고 있는 데이터 파일에 링크를 적용해보자.


``` shell
kgm0917@DESKTOP-DT2VQLB:~/playground$ ln fun fun-hard

kgm0917@DESKTOP-DT2VQLB:~/playground$ ln fun dir1/fun-hard

kgm0917@DESKTOP-DT2VQLB:~/playground$ ln fun dir2/fun-hard
```

이제 우리는 네 가지의 fun 파일을 가지게 된다. 다시 playground 디렉토리를 확인해보자.


``` shell
kgm0917@DESKTOP-DT2VQLB:~/playground$ ls -l

total 8

drwxr-xr-x 1 kgm0917 kgm0917 4096 Jan 23 13:02 dir1

drwxr-xr-x 1 kgm0917 kgm0917 4096 Jan 23 13:02 dir2

-rw-r--r-- 4 kgm0917 kgm0917 1422 Jan 23 11:12 fun

-rw-r--r-- 4 kgm0917 kgm0917 1422 Jan 23 11:12 fun-hard
```


==여기서 알 수 있는 것은 fun 파일과 fun-hard 파일 정보의 두 번째 필드 내용이 4이고 이것은 해당 파일의 하드 링크 개수라는 것이다.== 하나의 파일에 적어도 하나의 링크가 연결된다는 것을 기억할 것이다.

그렇다면 fun 파일과 fun-hard 파일이 같은 것임을 어떻게 알 수 있을까? 이 경우에는 ls 명령어가 도움이 되지 않는다. 다섯 번째 필드에 나와 있는 것처럼 <mark style="background: #FF5582A6;">이 두 파일의 크기는 동일함을 알 수 있지만 파일 자체가 동일하다는 사실을 알려주지 못하고 있다.</mark> 이러한 문제를 해결하기 위해 좀더 깊이 있게 알아보려고 한다.

<mark style="background: #ADCCFFA6;">하드 링크에 대해 고민할 때, 파일이 두분으로 나뉘어질 때 파일 내용을 담고 있는</mark> ‘**데이터 영역**’, <mark style="background: #ADCCFFA6;">파일 이름을 갖고 있는</mark> ‘**이름 영역**’<mark style="background: #ADCCFFA6;">으로 나뉘어진다는 것을 생각하면 도움이 된다.</mark> 실제로 하드 링크를 생성할 때, 동일한 데이터 영역을 참조하도록 부가적인 이름 영역들을 생성한다.

==시스템은 **아이노드**(inode)라고 불리우는 디스크 블록체인 하나 할당하고 이것은 이름영역과 연결된다.== 결국 각 하드 링크가 파일 내용을 담고 있는 각각의 아이노드를 참조하게 되는 것이다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~/playground$ ls -li

total 8

21955048183434109 drwxr-xr-x 1 kgm0917 kgm0917 4096 Jan 23 13:02 dir1

20829148276597361 drwxr-xr-x 1 kgm0917 kgm0917 4096 Jan 23 13:02 dir2

14355223812253776 -rw-r--r-- 4 kgm0917 kgm0917 1422 Jan 23 11:12 fun

14355223812253776 -rw-r--r-- 4 kgm0917 kgm0917 1422 Jan 23 11:12 fun-hard
```


표시된 내용을 보면 첫 번째 필드에 아이노드 번호가 있고, fun과 fun-hard 파일이 같은 아이노드 번호를 공유하고 있음을 볼 수 있다. 동일한 파일임을 알 수 있다.


### 심볼릭 링크 생성

심볼릭 링크는 앞서 말했듯이 하드 링크의 두 가지 단점을 보완하기 위해 탄생된 것이다. 즉 <mark style="background: #ADCCFFA6;">하드 링크는 물리적인 장치들을 포괄하지 못하고 파일만 참조할 수 있지</mark> ==디렉토리를 참조할 수 없다는 점==이다. **심볼릭 링크는 파일이나 디렉토리를 가리킬 텍스트 포인터를 가지고 있는 특수한 형태의 파일이다.**

만드는 방법

``` shell
kgm0917@DESKTOP-DT2VQLB:~/playground$ ln -s fun fun-sym

kgm0917@DESKTOP-DT2VQLB:~/playground$ ln -s ../fun dir1/fun-sym

kgm0917@DESKTOP-DT2VQLB:~/playground$ ln -s ../fun dir2/fun-sym
```

첫 번째 예제를 보면 상당히 직관적이다. **-s** 옵션을 입력해서 하드 링크 대신 심볼릭 링크를 만들었다. ==다음의 가장 중요한 2개의 예제는, 심볼릭 인크가 참조하는 원본파일의 위치 정보를 생성하는 것이다.== ls 명령 결과를 보면 쉽게 알 수 있다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~/playground$ ls -l dir1

total 4

-rw-r--r-- 4 kgm0917 kgm0917 1422 Jan 23 11:12 fun-hard

lrwxrwxrwx 1 kgm0917 kgm0917 6 Jan 23 13:26 fun-sym -> ../fun
```


결과를 보면, dir1 디렉토리에 위치한 fun-sym 파일이 보이는데, 그 줄의 맨 처음에 표시된 1에 의해 이것이 심볼릭 링크라는 것과 이 심볼릭 링크가 ../fun을 가리키고 있다는 사실을 정확하게 보여주고 있다.

==fun-sym의 위치와는 상대적으로 fun 파일은 현재 위치의 상위 디렉토리에 위치해 있음을 알 수 있다.== 또한, 심볼릭 링크 파일의 길이는 6이라는 것인데 이는 심볼릭 링크가 가리키고 있는 파일의 길이가 아니라 ../fun 문자열 개수를 나타내고 있다.

심볼릭 링크를 생성할 시 **절대경로**를 사용할 수 도 있다.

또한 이전에 이미 했던 것처럼 상대 경로도 쓸 수 있는데, 이 방법이 더 바람직하다. 심볼릭 링크를 포함하고 있는 디렉토리가 링크를 유지하면서 파일명을 변경하거나 파일을 이동할 수 있도록 허용하기 때문이다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~/playground$ ls -l

total 8

drwxr-xr-x 1 kgm0917 kgm0917 4096 Jan 23 13:26 dir1

lrwxrwxrwx 1 kgm0917 kgm0917 4 Jan 23 13:41 dir1-sym -> dir1

drwxr-xr-x 1 kgm0917 kgm0917 4096 Jan 23 13:27 dir2

-rw-r--r-- 4 kgm0917 kgm0917 1422 Jan 23 11:12 fun

-rw-r--r-- 4 kgm0917 kgm0917 1422 Jan 23 11:12 fun-hard

lrwxrwxrwx 1 kgm0917 kgm0917 3 Jan 23 13:26 fun-sym -> fun
```


### 파일 및 디렉토리 삭제

이전에 살펴본 대로 [[rm]] 명령어는 파일이나 디렉토리를 삭제할 때 사용된다. 이제 이것으로 정리를 해 보고자 한다.

링크 하나를 삭제해 보자.


``` shell
kgm0917@DESKTOP-DT2VQLB:~/playground$ ls -l

total 4

drwxr-xr-x 1 kgm0917 kgm0917 4096 Jan 23 13:26 dir1

lrwxrwxrwx 1 kgm0917 kgm0917 4 Jan 23 13:41 dir1-sym -> dir1

drwxr-xr-x 1 kgm0917 kgm0917 4096 Jan 23 13:27 dir2

-rw-r--r-- 3 kgm0917 kgm0917 1422 Jan 23 11:12 fun

lrwxrwxrwx 1 kgm0917 kgm0917 3 Jan 23 13:26 fun-sym -> fun
```


fun-hard 파일이 삭제되고, fun 파일 정보의 두 번째 필드는 링크 개수가 4개에서 3개로 줄어들었음을 보여주고 있다. 다음으로 fun 파일을 삭제해 보자. -i 옵션을 통하여 어떤 결과가 나올지 확인해보자.


``` shell
kgm0917@DESKTOP-DT2VQLB:~/playground$ rm -i fun

rm: remove regular file 'fun'? y
```

프롬프트에 y를 입력하면 fun 파일이 삭제가 된다. ls 명령어로 현재까지 작업한 결과를 확인해보자. fun-sym 파일에 어떤 변화가 생겼는가? 바로 심볼릭 링크 파일이 현재는 삭제된 파일을 가리키고 있기에 링크가 깨져버렸다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~/playground$ ls -l

total 0

drwxr-xr-x 1 kgm0917 kgm0917 4096 Jan 23 13:26 dir1

lrwxrwxrwx 1 kgm0917 kgm0917 4 Jan 23 13:41 dir1-sym -> dir1

drwxr-xr-x 1 kgm0917 kgm0917 4096 Jan 23 13:27 dir2

lrwxrwxrwx 1 kgm0917 kgm0917 3 Jan 23 13:26 fun-sym -> fun
```

대부분의 리눅스 배포판에는 이렇게 깨져버린 링크까지도 ls 명령어로 확인할 수 있도록 설정한다. 깨진 링크가 보이는 것은 더 이상 무의미하기에 위험하진 않지만, 다소 지저분하다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~/playground$ less fun-sym

fun-sym: No such file or directory
```

심볼릭 링크를 삭제해서 놀이터를 깨끗이 정리해보자.

```

kgm0917@DESKTOP-DT2VQLB:~/playground$ rm fun-sym dir1-sym

kgm0917@DESKTOP-DT2VQLB:~/playground$ ls -l

total 16

drwxr-xr-x 1 kgm0917 kgm0917 4096 Jan 23 13:26 dir1

drwxr-xr-x 1 kgm0917 kgm0917 4096 Jan 23 13:27 dir2

-rw-r--r-- 1 kgm0917 kgm0917 13067 Jan 23 13:49 's'$'\033\033''qqq’(이게 뭔지는 잘 모르겠음.)
```
<mark style="background: #FF5582A6;">심볼릭 링크에 대해 한가지 기억해야 할 것은 대부분의 파일 작업이 링크 그 자체에서 실행되어야 하는 것이 아니라</mark> <mark style="background: #ADCCFFA6;">링크가 가리키고 있는 원본 파일에서 이루어진다는 것이다.</mark>

이제 우리 놀이터 playground 를 삭제하도록 한다. 우선 홈 디렉토리로 이동한 뒤 [[rm]] 명령어와 –r 옵션을 사용하여 playgournd의 하위 디렉토리까지 모두 삭제한다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ rm -r playground

kgm0917@DESKTOP-DT2VQLB:~$ ls

kgm0917@DESKTOP-DT2VQLB:~$ ls -a

. .. .bash_history .bash_logout .bashrc .lesshst .motd_shown .profile .sudo_as_admin_successful
```

---
다음 챕터: [[chapter05 명령어와 친해지기]]