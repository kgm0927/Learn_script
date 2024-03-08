

터미널 창이 나타내면 'git'을 입력해 보라. 깃이 제대로 설치가 됐다면 git 명령 다음에 쓸 수 있는 여러 옵션들이 나타나게 될 것이다.


```
kgm09@kim_gyeong_min MINGW64 ~
$ git
usage: git [-v | --version] [-h | --help] [-C <path>] [-c <name>=<value>]
           [--exec-path[=<path>]] [--html-path] [--man-path] [--info-path]
           [-p | --paginate | -P | --no-pager] [--no-replace-objects] [--bare]
           [--git-dir=<path>] [--work-tree=<path>] [--namespace=<name>]
           [--config-env=<name>=<envvar>] <command> [<args>]

These are common Git commands used in various situations:

start a working area (see also: git help tutorial)
   clone     Clone a repository into a new directory
   init      Create an empty Git repository or reinitialize an existing one

work on the current change (see also: git help everyday)
   add       Add file contents to the index
   mv        Move or rename a file, a directory, or a symlink
   restore   Restore working tree files
   rm        Remove files from the working tree and from the index

examine the history and state (see also: git help revisions)
   bisect    Use binary search to find the commit that introduced a bug
   diff      Show changes between commits, commit and working tree, etc
   grep      Print lines matching a pattern
   log       Show commit logs
   show      Show various types of objects
   status    Show the working tree status

grow, mark and tweak your common history
   branch    List, create, or delete branches
   commit    Record changes to the repository
   merge     Join two or more development histories together
   rebase    Reapply commits on top of another base tip
   reset     Reset current HEAD to the specified state
   switch    Switch branches
   tag       Create, list, delete or verify a tag object signed with GPG

collaborate (see also: git help workflows)
   fetch     Download objects and refs from another repository
   pull      Fetch from and integrate with another repository or a local branch
   push      Update remote refs along with associated objects

'git help -a' and 'git help -g' list available subcommands and some
concept guides. See 'git help <command>' or 'git help <concept>'
to read about a specific subcommand or concept.
See 'git help git' for an overview of the system.
```

### 깃 환경 설정하기

깃을 사용하기 전에 먼저 사용자 정보를 입력해야 한다. 깃은 버전을 저장할 때마다 그 버전을 만든 사용자 정보도 함께 저장하기 때문이다. 이를 통해 어떤 버전을 누가 언제 만들었는지 쉽게 파악할 수 있다. 이제 깃에 사용자 정보를 입력해 본다. 사용하는 운영체제가 윈도우라면 깃 바시, 맥이라면 터미널 창이다. 

깃에서 사용자 정보를 설정하려면 git config 명령을 사용한다. 여기에 --global 옵션을 추가하면 현재 컴퓨터에 있는 모든 저장소에서 같은 사용자 정보를 사용하도록 설정한다. 터미널 창에 다음과 같이 입력해서 이름과 메일 주소를 설정한다.


``` shell
kgm09@kim_gyeong_min MINGW64 ~
$ git config --global user.name
kgm0927

kgm09@kim_gyeong_min MINGW64 ~
$ git config --global user.email
initial929@gmail.com
(이미 설되어 있으므로 넘어간다.)
```


---
# 01-3 리눅스 명령 연습하기

터미널 창에서 깃을 사용하기 위해 쓰는 명령은 리눅스 명령과 같다. 깃을 사용하기 전에 미리 알아두어야할 리눅스 명령을 먼저 보겠다.


### 현재 디렉터리 살펴보기

1. 깃 배시를 실행한 후 커서 윗줄을 보면 맨 끝에 물결 표시(~)가 있다. 현재 홈 디렉터리(home dircetory)에 있다는 의미이다.
``` shell
kgm09@kim_gyeong_min MINGW64 ~
$

```

2. 'pwd'명령을 입력하고 'Enter'를 눌러라. 현재 위치의 경로가 나온다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~
$ pwd
/c/Users/kgm09

```

3. 현재 디렉터리에 어떤 파일이나 디렉터리가 있는지 확인할 때는 'ls' 명령을 사용한다. ls 명령만 입력하고 Enter를 누르면 디렉터리와 파일 이름이 나온다. 이름 뒤에 슬래시(/)가 붙어 있는 거싱 디렉터리이다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~
$ ls
 AppData/
'Application Data'@
 Contacts/
 Cookies@
 Desktop/
 Documents/
 Downloads/
 Favorites/
 Go/
 Links/
'Local Settings'@
 Music/
'My Documents'@
 NTUSER.DAT
 NTUSER.DAT{6435ef44-a162-11ee-bf28-c722df82fe6f}.TM.blf
 NTUSER.DAT{6435ef44-a162-11ee-bf28-c722df82fe6f}.TMContainer00000000000000000001.regtrans-ms
 NTUSER.DAT{6435ef44-a162-11ee-bf28-c722df82fe6f}.TMContainer00000000000000000002.regtrans-ms
 NetHood@
 OneDrive/
 Pictures/
 PrintHood@
 Recent@
'Saved Games'/
 Searches/
 SendTo@
 Templates@
 Videos/
 loc-git/
 manuals/
 ntuser.dat.LOG1
 ntuser.dat.LOG2
 ntuser.ini
 source/
 work.txt
'시작 메뉴'@
```


4. 리눅스 명령에 옵션(Option)을 추가하려면 붙임표(-)와 원하는 옵션을 나타내는 글자를 함께 입력한다. 예를 들어 파일과 디렉터리의 상세 정보까지 표시하는 옵션을 추가하려면 ls 명령 뒤에 '-l'을 추가로 입력한다. 숨긴 파일과 디렉터리를 표시하려면 '-a'를 추가 입력한다. 두 옵션을 함께 사용하려면 '-la' 또는 '-al'처럼 입력하면 된다.



- ls 명령 옵션 모음

ls 명령을 사용할 때 옵셥을 추가하면 다양한 형식으로 파일과 디렉터리를 표시할 수 있다.


| 옵션  | 설명                         |
| --- | -------------------------- |
| -a  | 숨김 파일과 디렉터리도 함께 표시한다.      |
| -l  | 파일이나 디렉터리의 상세 정보도 함께 표시한다. |
| -r  | 파일의 정렬 순서를 거꾸로 표시한다.       |
| -t  | 파일 작성 시간 순으로 (내림차순) 표시한다.  |



### 터미널 창에서 디렉터리 이동하기


터미널 창에서 디렉터리 사이를 이동할 때는 'cd'라는 명령어를 사용한다. cd 명령어를 사용해 보겠다.

1. 먼저 현재 위치에서 상위 디렉터리로 이동해 보겠다. 다음과 같이 cd 명령 다음에 한 칸 띄고 마침표 2개를 입력하라.
``` shell

kgm09@kim_gyeong_min MINGW64 ~
$ cd ..

kgm09@kim_gyeong_min MINGW64 /c/Users
$
```

2. cd 명령을 실행한 후 $ 기호 위에 표시된 경로를 확인해 보라. 끝부분에 /c/User라고 나타난다. 

3. 이번에는 한 단계 위인 c 드라이브 루트(root)폴더, 즉 /c 까지 이동할 것이다. 이동한 다음 ls 명령을 사용하면 /c 안의 파일와 디렉터리를 확인할 수 있다.

``` shell


kgm09@kim_gyeong_min MINGW64 /c/Users
$ cd ..

kgm09@kim_gyeong_min MINGW64 /c
$ ls
'$Recycle.Bin'/             Python/
'$SysReset'/                Recovery/
'$WinREAgent'/             'System Volume Information'/
 4DThumb/                   Temp/
 4DefaultTempSaveScan/      Users/
 Apps/                      WMV/
 Dell/                      Windows/
'Documents and Settings'@   appverifUI.dll*
 Drivers/                   backup/
 DumpStack.log.tmp          hiberfil.sys
 OneDriveTemp/              kali-linux-2023.4-virtualbox-amd64/
 PerfLogs/                  langpacks/
'Program Files'/            pagefile.sys
'Program Files (x86)'/      swapfile.sys
 ProgramData/               vfcompat.dll*

```

4. 하위 디렉터리로 이동할 때는 cd 명령 다음에 이동할 하위 디렉터리 이름을 입력한다. 예를 들어 현재 /c 디렉터리에 있는데 /c/User 디렉터리로 가려면 다음과 같이 명령을 입력한다. 그리고 $ 위에 표시된 현재 경로를 보면 /c/User라고 나타날 것이다.

``` shell
kgm09@kim_gyeong_min MINGW64 /c
$ cd User
bash: cd: User: No such file or directory

```

5. 처음에 출발했던 디렉터리, 즉 홈 디렉터리로 돌아가려면 다음과 같이 입력한다. '~'는 홈 디렉터리를 나타낸다.

``` shell
kgm09@kim_gyeong_min MINGW64 /c
$ cd ~

kgm09@kim_gyeong_min MINGW64 ~
$

```


- 리눅스에서 디렉터리를 나타내는 기호

리눅스에서는 현재 위치나 파일 경로를 나타낼 때 몇 가지 약속된 기호를 사용하고 있다. 이것은 꼭 기억해 두어야 한다.

| 기호  | 설명                                                                                                 |
| --- | -------------------------------------------------------------------------------------------------- |
| ~   | 현재 접속 중인 사용자의 홈 디렉터리를 가리킨다. 홈 디렉터리의 경로는 'c/User/사용자 아이디'이다. 사용자 디렉터리라고도 부른다. 사용자 아이디는 5글자까지만 나타난다. |
| ./  | 현재 사용자가 작업 중인 디렉터리이다.                                                                              |
| ../ | 현재 디렉터리의 상위 디렉터리이다.                                                                                |



### 터미널 창에서 디렉터리 만들기 및 삭제하기


터미널 창에서 바로 디렉터리를 만들고 삭제도 할 수 있다면 훨씬 편리할 것이다.


1. 터미널 창에서 현재 디렉터리 안에 하위 디렉터리를 만들 때는 'mkdir'명령을 사용한다. 예를 들어 홈 디렉터리 안에 있는 Documents 디렉터리에 'test'라는 하위 디렉터리를 만든다면 다음과 같이 작성한다.
``` shell

kgm09@kim_gyeong_min MINGW64 ~
$ cd Documents

kgm09@kim_gyeong_min MINGW64 ~/Documents
$ mkdir test

```


2. mkdir 명령을 실행하고 하위 디렉터리가 만들어져도 화면에는 아무것도 나타나지 않는다. test 디렉터리가 제대로 만들어졌는지 확인하기 위해 ls 명령을 입력한다. 다음과 같이 목록에 'test/'가 있다면 test 디렉터리를 잘 만든 것이다.


``` shell

kgm09@kim_gyeong_min MINGW64 ~
$ cd Documents

kgm09@kim_gyeong_min MINGW64 ~/Documents
$ ls
 GitHub/                desktop.ini    '사용자 지정 Office 서식 파일'/
'My Music'@             download.pdf   '윈도우즈 프로그래밍_보고서.hwpx'
'My Pictures'@          emdfhrrma.pdf   제목.hwpx
'My Videos'@            test/          '카카오톡 받은 파일'/
'Visual Studio 2022'/   test.txt
'Welcome to Hwp.hwp'    그래픽/

```

3. 디렉터리를 만들었다면 삭제도 할 수 있을 것이다. 디렉터리를 삭제할 때는 'rm' 명령을 사용한다. 이때 -r 옵션을 붙이면 디렉터리 안에 있는 하위 디렉터리와 파일까지 함께 삭제된다. 다음 명령을 입력한 다음 ls 명령을 실행해 보면 test 디렉터리가 삭제되어 있을 것이다.
``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents
$ rm -r test

kgm09@kim_gyeong_min MINGW64 ~/Documents
$ ls
 GitHub/               'Welcome to Hwp.hwp'   그래픽/
'My Music'@             desktop.ini          '사용자 지정 Office 서식 파일'/
'My Pictures'@          download.pdf         '윈도우즈 프로그래밍_보고서.hwpx'
'My Videos'@            emdfhrrma.pdf         제목.hwpx
'Visual Studio 2022'/   test.txt             '카카오톡 받은 파일'/

```

여기서 기억해야 할 것은 삭제할 디렉터리의 상위 디렉터리에서 rm 명령을 입력해야 한다는 것이다. 예를 들어 Documents 디렉터리 안에 있는 test 디렉터리를 삭제하려면 먼저 Documents 디렉터리로 이동한 다음 rm 명령을 입력해야 한다.


### 빔에서 텍스트 문서 만들기

리눅스의 기본 편집기인 빔(vim)은 터미널에서 사용할 수 있는 대표적인 편집기이다. 앞에서 깃을 설치할 때 기본 편집기를 빔으로 설정한 이유도 이런 편리함 때문이다.




1. 깃 배시 프로그램을 실행해서 터미널 창을 연다. 터미널 창을 열면 기본적으로 홈 디렉터리부터 시작한다. Documents 디렉터리로 이동해서 test 디렉터리를 만들고 test 디렉터리로 이동한다.
``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents
$ ls
 GitHub/               'Welcome to Hwp.hwp'   그래픽/
'My Music'@             desktop.ini          '사용자 지정 Office 서식 파일'/
'My Pictures'@          download.pdf         '윈도우즈 프로그래밍_보고서.hwpx'
'My Videos'@            emdfhrrma.pdf         제목.hwpx
'Visual Studio 2022'/   test.txt             '카카오톡 받은 파일'/

kgm09@kim_gyeong_min MINGW64 ~/Documents
$ mkdir test

kgm09@kim_gyeong_min MINGW64 ~/Documents
$ cd test

kgm09@kim_gyeong_min MINGW64 ~/Documents/test
$

```

2. 현재 디렉터리(test 디렉터리)에 test.txt 파일을 만들기 위해 다음과 같이 'vim' 명령을 입력하겠다. vim 명령은 뒤에 입력한 파일 이름과 같은 파일이 없다면 그 이름으로 새로운 텍스트 문서를 만들고, 파일이 있다면 그 파일을 연다. 여기에서는 test.txt 파일 새로 만들어 진다.
```
kgm09@kim_gyeong_min MINGW64 ~/Documents
$ mkdir test

kgm09@kim_gyeong_min MINGW64 ~/Documents
$ cd test

kgm09@kim_gyeong_min MINGW64 ~/Documents/test
$ vim test.txt



~
~
~
~
~

```


3. 명령을 입력했을 때 다음과 같이 화면이 바뀌면 빔이 잘 실행된 것이다. 화면 왼쪽 위에는 커서가 깜박이고 왼쪽 아래는 현재 빔으로 연 파일 이름이 표시된다.


4. 새로 만든 파일에 아무 내용이나 입력해 보라. 아마 제대로 입력이 되지 않을 것이다. 빔에는 문서를 작성하는 '입력 모드'와 문서를 저장하는 'ex 모드'가 있다. 빔에는 처음에 'ex 모드'로 열리기 때문에 키를 눌러도 반응이 없다.

``` mermaid

flowchart LR

node(ex 모드: 저장, 종료)--I 누름-->node1(입력 모드: 텍스트 입력, 수정)
node1--ESC 누름-->node
```



5. 빔에서 텍스트를 입력하려면 ex 모드에서 **I**또는 **A**를 눌러 입력 모드로 바꿔야 한다. 입력 모드가 되면 화면 맨 아래 '끼워넣기'라는 단어가 뜨는데, 이때부터 텍스트를 입력할 수 있다. 모드를 바꾼 다음 간단하게 텍스트를 입력해 보겠다.

``` shell
이것은 연습문서입니다.
vim 편집기를 사용하고 있습니다.
~

```


6. 텍스트 입력이 끝난 후 파일을 저장할 때는 다시 ex 모드로 돌아가야 한다. Esc를 누르면 ex 모드로 돌아간다. 그리고 콜론(:)를 입력하면 원래 '끼워넣기'가 있던 자리에 텍스트를 입력할 수 있다. ':wq' 명령을 입력하고 Enter를 눌러라. 'W'는 저장, 'q'는 종료를 실행하는 명령이다.

``` shell
이것은 연습문서입니다.
vim 편집기를 사용하고 있습니다.
~
:wq
```


7. 이제 Enter를 누르면 작성 중이던 파일을 저장하면서 편집기가 종료되고 빔을 실행했던 터미널 창으로 돌아간다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents
$ mkdir test

kgm09@kim_gyeong_min MINGW64 ~/Documents
$ cd test

kgm09@kim_gyeong_min MINGW64 ~/Documents/test
$ vim test.txt

kgm09@kim_gyeong_min MINGW64 ~/Documents/test
$ vim test.txt

kgm09@kim_gyeong_min MINGW64 ~/Documents/test
$
```

- 빔 ex 모드 명령 모음

ex 모드에서 사용하는 명령은 콜론(:)으로 시작하고, 자주 사용하는 명칭은 다음과 같다.

| 명령           | 설명                                                |
| ------------ | ------------------------------------------------- |
| :w 또는 :write | 편집 중이던 문서를 저장한다.                                  |
| :q 또는 :quit  | 편집기를 종료한다.                                        |
| :wq(*파일*)    | 편집 중이던 문서를 저장하고 종료한다. 파일 이름을 함께 입력하면 그 이름으로 저장한다. |
| :q!          | 문서를 저장하지 않고 편집기를 종료한다. 확장자가 .swp인 임시 파일이 생긴다.     |


### 텍스트 문서 확인하기

터미널 창에서 간단히 텍스트 문서의 내용을 확인할 때는 리눅스의 cat 명령을 사용한다. cat 명령과 텍스트 파일 이름을 입력해 보라. 앞에서 작성했던 test.txt 파일의 내용이 터미널 창에 나타난다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/test
$ cat test.txt
이것은 연습문서입니다.
vim 편집기를 사용하고 있습니다.

```


이외에 cat 명령으로 실행할 수 있는 다양한 기능들이 있다.


| 명령                               | 설명                           |
| -------------------------------- | ---------------------------- |
| $ cat 파일                         | 파일의 내용을 화면에 표시한다.            |
| $ cat *파일1, 파일2, ... , 파일n> 새파일* | 파일 n개를 차례로 연결해서 새로운 파일을 만든다. |
| $ cat *파일1>> 파일2*                | 파일1의 내용을 파일2 끝에 연결한다.        |
 