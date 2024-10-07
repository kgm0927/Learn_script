


---
# 02-1 깃 저장소 만들기



이제 본격적으로 깃을 사용해보겠다. 깃의 기본 개념을 익히기 위해 먼저 사용자 컴퓨터에 저장소를 만들겠다. 저장소를 만들고 싶은 디렉터리로 이동해서 깃을 초기화하면 그때부터 해당 디렉터리에 있는 파일들을 버전 관리할 수 있다.


### 깃 초기화하기 -git init


1. 깃 저장소에서 만들 디렉터리를 하나 새로 만들겠다. 터미널 창을 열고 다음과 같이 입력해서 홈 디렉터리에 hello-git이라는 디렉터리를 만든다. 그러기 [[cd]] 명령을 사용해 hello-git 디렉터리로 이동해라.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents
$ ls
 GitHub/                desktop.ini    '사용자 지정 Office 서식 파일'/
'My Music'@             download.pdf   '윈도우즈 프로그래밍_보고서.hwpx'
'My Pictures'@          emdfhrrma.pdf   제목.hwpx
'My Videos'@            test/          '카카오톡 받은 파일'/
'Visual Studio 2022'/   test.txt
'Welcome to Hwp.hwp'    그래픽/

kgm09@kim_gyeong_min MINGW64 ~/Documents
$ mkdir hello-git

kgm09@kim_gyeong_min MINGW64 ~/Documents
$ cd hello-git

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git
$

```

2. hello-git 디렉터리 안의 내용을 살펴보기 위해 'ls -al' 명령을 입력하겠다. 화면에 두 줄짜리 결과가 나타날 것이다. 마침표가 하나(.)인 현재 디렉터리를 나타내고, 마침표가 두 개(..)인 항목은 상위 디렉터리를 나타낸다. 아직 아무것도 만들지 않았기 때문에 파일을 하나도 없다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git
$ ls -al
total 8
drwxr-xr-x 1 kgm09 197609 0 Mar  8 09:50 ./
drwxr-xr-x 1 kgm09 197609 0 Mar  8 09:50 ../

```



3. 이 디렉터리에 저장소를 만들기 위해 깃에 다음과 같이 git init 명령을 입력한다. 깃을 사용할 수 있도록 디렉터리를 초기화하는 것이다. 'Initialized empty Git repository ...'라는 메시지가 나타난다면 이제부터 해당 디렉터리에서 깃을 사용할 수 있다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents
$ mkdir hello-git

kgm09@kim_gyeong_min MINGW64 ~/Documents
$ cd hello-git

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git
$ ls -al
total 8
drwxr-xr-x 1 kgm09 197609 0 Mar  8 09:50 ./
drwxr-xr-x 1 kgm09 197609 0 Mar  8 09:50 ../

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git
$ git init
Initialized empty Git repository in C:/Users/kgm09/Documents/hello-git/.git/

```


4. ls 명령을 사용해서 다시 한번 디렉터리 안의 내용을 확인해 보겠다. 다음과 같이 입력하라. 처음에 살펴봤을 때와는 다를게 '.git'이라는 디렉터리가 생겨 있다. 이 디렉터리가 깃을 사용하면서 버전이 저장될 '저장소(repository)'이다. 저장소에 대해서는 앞으로 자세히 설명할 것이다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ ls -al
total 12
drwxr-xr-x 1 kgm09 197609 0 Mar  8 09:53 ./
drwxr-xr-x 1 kgm09 197609 0 Mar  8 09:50 ../
drwxr-xr-x 1 kgm09 197609 0 Mar  8 09:53 .git/

```


---
# 02-2 버전 만들기



프로그램 개발에는 수정 내용이 쌓이면 새로 번호를 붙여서 이전 상태와 구별한다. 이렇게 번호 등을 구별된 것을 버전이라고 부른다. 여기에서는 깃에서 만드는 방법을 알아보겠다.


### 깃에서 버전이란


깃에서 버전이란 문서를 수정하고 저장할 때마다 생기는 것이라고 생각하면 쉽다. 


이렇게 파일을 다른 이름으로 저장해 버전을 만드는 방법보다 훨씬 쉽게 버전을 만들고 만든 시간과 수정 내용까지 기록할 수 있는 것이 깃과 같은 버전 관리 시스템이다. 깃에서 버전을 관리하면 원래 파일 이름은 그대로 유지하면서 파일에서 무엇을 변경했는지를 변경 시점마다 확인할 수 있다. 또 각 버전만다 작업했던 내용을 확인할 수 있고, 그 버전으로 되돌아갈 수 있다.



### 스테이지와 커밋 이해하기


깃은 어떻게 파일 이름은 그대로 유지하면서 수정 내역을 기록하는 걸까? 깃을 처음 공부할 때 한번에 이해하기 힘든 이유는 이 과정이 생소할 뿐만 아니라 눈에 보이지 않은 가상의 개념들이 등장하기 때문이다. 먼저 아래 그림을 보면서 깃에서 버전을 만드는 단계를 살펴보겠다.


``` mermaid

flowchart LR

tree[작업 트리]
stage[스테이지]---.git디렉터리---repository[저장소]
```




- 작업 트리

그림에서 가장 왼쪽에 있는 작업 트리(working tree)는 파일 수정, 저장 등의 작업을 하는 디렉터리로, '작업 디렉터리(working directory)'라고도 한다. 앞에서 만들었던 hello-git 디렉터리가 작업 트리가 된다. 즉 우리 눈에 보이는 디렉터리가 바로 작업 트리이다.


- 스테이지

스테이지(stage)는 버전으로 만들 파일이 대기하는 곳이다. 스테이징 영역(staging area)이라고 부르기도 한다. 예를 들어 작업 트리에서 10개의 파일을 수정했는데 4개의 파일만 버전으로 만들려면 4개의 파일만 스테이지로 넘겨주면 된다.


- 저장소 

저장소(repository, 리포지토리)는 스테이지에서 대기하고 있던 파일들을 버전으로 만들어 저장하는 곳이다. '리포지토리'와 '저장소'라는 용어가 모두 사용되지만 여기에서는 '저장소'라고 부른다.


스테이지와 저장소는 눈에 보이지 않는다. 깃을 초기화했을 때 만들어지는 .git 디렉터리 안에 숨은 파일 형태로 존재하는 영역이다. .git 안에 숨어 있는 스테이지와 저장소 영역을 상상하며 깃이 버전을 만드는 과정을 살펴보겠다.

hello.txt 파일 문서를 수정하고 저장하면 그 파일은 작업 트리에 있게 된다. 그리고 수정한 hello.txt 파일을 버전으로 만들고 싶을 때 스테이지에 넣는다. 다른 파일도 수정한 뒤 버전으로 만들겠다면 스테이지에 넣어둔다.


``` mermaid

flowchart LR

tree(작업 트리:hello.txt)-->stage(스테이지:hello.txt)
repo(저장소)

```



파일 수정을 끝내고 스테이지에 다 넣었다면 버전을 만들기 위해 깃에게 '커밋(commit)' 명령을 내린다. 커밋 명령을 내리면 새로운 버전이 형성되면서 스테이지에 대기하던 파일이 모두 저장소에 저장된다. 커밋 명령은 뒤에서 자세히 알아본다.


``` mermaid

flowchart LR

tree(작업 트리:hello.txt)
stage(스테이지:없음)
stage-->repo(저장소:hello.txt)

```


정리해 보자면 먼저 작업 트리에서 문서를 수정한다. 수정한 파일 중 버전으로 만들고 싶은 것을 스테이징 영역, 즉 스테이지에 저장한다. 그리고 스테이지에 있던 파일을 저장소로 커밋하는 것이 깃이 버전을 만드는 순서이다.




### 작업 트리에서 빔으로 문서 수정하기

이제부터 작업 트리에서 문서를 직접 수정해 보겠다. 여기에서는 앞에서 만들었던 hello-git 디렉터리에 새로운 파일을 만든다.


1. 터미널 창을 열어 hello-git 디렉터리까지 이동한다. 앞에서 hello-git 디렉터리에서 깃을 초기화했기 때문에 버전 관리를 할 수 있다. 먼저 깃 상태를 확인하기 위해 다음과 같이 입력해 본다.

```
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git status
On branch master

No commits yet

nothing to commit (create/copy files and use "git add" to track)
```


2. 깃의 상태를 나타내는 메시지가 나타나는데 그 의미를 간단히 살펴보겠다.


``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git status
On branch master

No commits yet

nothing to commit (create/copy files and use "git add" to track)

```

*On branch master*: 현재 master 브랜치에 있다. master 브랜치는 다음 장에서 자세히 설명하겠지만 여기에서는 저장소에 들어 있는 디렉터리와 비슷한 것이라고 생각해 둬라.

*No commits yet*: 아직 커밋한 파일이 없다.

*nothing to commit*: 현재 커밋할 파일이 없다.


3. hello-git 디렉터리에 새로운 파일을 만들어 보겠다. 터미널 창에 다음과 같이 입력해서 hello.txt 파일을 만든다.


4. 빔 화면이 나타나면 I 또는 A를 눌러 입력 모드를 바꾼다. 간단하게 '1'을 입력해 본다. 그리고 Esc를 눌러 ex 모드로 돌아간 다음 ':wq'를 입력하고 Enter 를 누르면 수정한 문서를 저장하고 편집기를 종료한다.


``` shell

1
~
~
~
~
~
~
~
~
~
~
hello.txt[+] [unix] (08:59 01/01/1970)                                   1,1 All
:wq

```

5. 터미널 창으로 돌아와서 ls -al 명령을 입력해 보아라. 이번에는 조금 다른 메시지가 나타난다. branch master에 hello.txt 라는 **untracked file**가 있다고 한다. 깃에서는 아직 한번도 버전 관리하지 않은 파일을 **untracked files**라고 한다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        hello.txt

nothing added to commit but untracked files present (use "git add" to track)

```


그림으로 나타내면 다음과 같다.

``` mermaid

flowchart TB

tree(작업 트리:hello.txt)
stage(스테이지: 없음)
repo(저장소: 없음)
```






### 수정한 파일을 스테이징하기 -git add

작업 트리에서 파일을 만들거나 수정했다면 스테이지에서 수정한 파일을 추가한다. 이렇게 깃에게 버전 만들 준비를 하라고 알려주는 것을 '스테이징(staging)'또는 '스테이지에 올린다'라고 표현한다.


1. 깃에서 스테이징할 때 사용하는 명령은 git add이다. 터미널에 다음과 같이 입력해도 아무 내용도 나타나지 않을 것이다. 그렇다고 아무 일도 안 한 것은 아니다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git add hello.txt
warning: in the working copy of 'hello.txt', LF will be replaced by CRLF the next time Git touches it

```


2. 다음과 같이 입력해 깃 상태롤 확인해 보라.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   hello.txt



```


3. untracked files: 이라는 문구가 changes to be committed:로 바뀌었는데, 그리고 hello.txt 파일 앞에 'new file:'이라는 수식어가 추가로 나타난다. '새 파일 hello.txt를 (앞으로)커밋할 것이다'라는 뜻이다.


수정한 파일 hello.txt가 스테이지에 추가되었다. 그림으로 나타내면 다음과 같다.

``` mermaid

flowchart LR

tree(작업 트리: hello.txt)---add-->stage(스테이지:hello.txt)
repo(저장소 :없음)

```



### 스테이지에 올라온 파일 커밋하기- git commit


파일이 스테이지에 있다면 이제 버전을 만들 수 있다. 깃에서는 버전을 만드는 것을 간단히 '커밋(commit)한다'고도 말한다. 커밋할 때는 그 버전에 어떤 변경 사항이 있는지 확인하기 위해 메시지를 함께 기록해 두어야 한다.


1. 깃에서 파일을 커밋하는 명령은 git commit이다. 한 칸 띄운 후에 -m 옵션을 붙이면 커밋과 함께 저장할 메시지를 적을 수 있다. 이 메시지를 커밋 메시지라고 한다. 터미널 창에 다음과 같이 입력해 보아라.
``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git commit -m "message1"

```

2. 커밋 후에 결과 메시지를 보면 파일 1개가 변경되었고, 파일에 1개의 내용이 추가되었다고 나타난다. 스테이지에 있던 hello.txt 파일이 저장소에 추가된 것이다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git commit -m "message1"
[master (root-commit) c37afa3] message1
 1 file changed, 1 insertion(+)
 create mode 100644 hello.txt

```


3. 그러면 현재의 깃 상태는 어떨까? 다음과 같이 입력해라.
``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git status
On branch master
nothing to commit, working tree clean

```


4. 결과 메시지를 보면 버전으로 만들 파일이 없고(nothing to commit) 작업 트리도 수정사항 없이 깨끗하다(working tree clean)고 나타난다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git status
On branch master
nothing to commit, working tree clean

```

5. 버전이 제대로 만들어졌는지 어떻게 확인할까요? 저장소에 저장된 버전을 확인할 때는 git log 명령을 사용한다. 터미널 창에 다음과 같이 입력해라.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git log

```


6. 방금 커밋한 버전에 대한 설명이 나타난다. 커밋을 만든 사람, 만든 사람과 커밋 메시지가 함께 나타난다. 수정한 파일을 커밋하면 수정과 관련된 여러 정보를 저장할 수 있고 필요할 때 확인할 수 있다.
``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git log
commit c37afa3a761d7808b7c9f7a3706037d5f801440c (HEAD -> master)
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:13:15 2024 +0900

    message1

```


이렇게 스테이지에 있던 hello.txt 파일의 버전이 저장소에 만들어졌다. 그림으로 나타내면 다음과 같다.

``` mermaid

flowchart LR

tree(작업트리: hello.txt)
stage(스테이지: 없음)
repo(저장소: hello.txt)

stage---commit-->repo


```



### 스테이징과 커밋 한꺼번에 처리하기 -git commit -am


commit 명령에 -am 옵션을 사용하면 스테이지에 올리고 커밋하는 과정을 한꺼번에 처리할 수 있다. 단, 이 방법은 한 번이라도 커밋한 적인 있는 파일을 다시 커밋할 때만 사용할 수 있다.


1. 빔에서 hello.txt 파일을 열어 다음과 같이 입력한다.

``` shell

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ vim hello.txt

```

2. 빔이 열리면 'I'를 눌러 입력 모드로 바꾼다. 그리고 숫자 '2'를 추가한 후 Esc를 누르고 :wq를 입력해 문서를 저장하면서 편집기를 종료한다.
``` shell
1
2
~
~

~
~
~
~
~
~
hello.txt[+] [unix] (10:44 08/03/2024)                                   2,1 All
:wq

```


3.  앞에서는 수정한 파일을 스테이지에 올리고 커밋하는 것을 git add 명령과 git commit 명령을 사용해서 처리했다. 한 번 커밋한 파일이라면 git commit 명령에 -am 옵션을 붙여서 스테이징과 커밋을 한꺼번에 처리할 수 있다. 다음과 같이 입력한다.

``` shell

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git commit -am "message2"
warning: in the working copy of 'hello.txt', LF will be replaced by CRLF the next time Git touches it
[master 0a0046a] message2
 1 file changed, 1 insertion(+)


```

4. 방금 커밋한 버전에 어떤 정보가 들어있는지 확인해 보라.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git log
commit 0a0046a897e573da6fb40f27427d3c925ea7167d (HEAD -> master)
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:29:59 2024 +0900

    message2

commit c37afa3a761d7808b7c9f7a3706037d5f801440c
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:13:15 2024 +0900

    message1


```


---
# 02-3 커밋 내용 확인하기


버전을 관리하기 위해서는 지금까지 어떤 버전을 만들었는지 알 수 있어야 한다. 또 각 버전마다 어떤 차이가 있는지도 파악할 수 있어야 할 것이다. 여기에서는 이 방법을 알아보겠다.


### 커밋 기록 자세히 살펴보기 - git log

깃에서 자주 사용하는 명령 중 하나가 지금까지 커밋했던 기록을 살펴보기 위한 명령인 git log이다. git log 명령을 입력하면 지금까지 만든 버전이 화면에 나타나고, 각 버전마다 설명도 함께 나타난다. 위에서 git log 명령을 입력했을 때 나타난 화면을 더 자세히 살펴보겠다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git log
commit 0a0046a897e573da6fb40f27427d3c925ea7167d (HEAD -> master)
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:29:59 2024 +0900

    message2

commit c37afa3a761d7808b7c9f7a3706037d5f801440c
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:13:15 2024 +0900

    message1
```

**커밋 해시(commit hash)**, 또는 **깃 해시(git hash)**: commit이라는 항목 옆에 영문과 숫자로 된 긴 문자열이 나타나는 이것. 일종의 커밋을 구분하는 아이디다.

**최신 버전**: 커밋 해시 몇에 있는 (HEAD->master)로 이 버전이 가장 최신이라는 표시이다.

**Author**: 버전을 누가 만들었는지 알 수 있음.

**Date**: 버전이 언제 만들어졌는지를 알 수 있음.

이렇게 git log 명령을 입력했을 때 나오는 정보를 묶어 간단히 '커밋 로그'라고 부른다.



### 변경사항 확인하기- git diff


큰 규모의 프로그램을 짠다고 생각해 본다. 수만 줄 짜라 소스 코드를 수정한 다음 저장소에 있는 최근 버전과 비교해서 어떤 부분이 다른지 찾아야 한다면 어떻게 할까? 커밋 메시지를 참고해도 구체적으로 어디가 어떻게 수정되었는지 파악하기가 쉽지 않을 것이다.

이럴 때 git diff 명령을 사용하면 작업 트리에 있는 파일과 스테이지에 있는 파일을 비교하거나, 스테이지에 있는 파일과 저장소에 있는 최신 커밋을 비교해서 수정한 파일을 커밋하기 전에 최종적으로 검토할 수 있다.


1. 앞의 내용을 계속 따라왔다면 hello.txt 파일에는 숫자 1부터 2까지 입력되어 있고, 저장소에는 2개의 버전이 저장되어 있다.


``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git log
commit 0a0046a897e573da6fb40f27427d3c925ea7167d (HEAD -> master)
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:29:59 2024 +0900

    message2

commit c37afa3a761d7808b7c9f7a3706037d5f801440c
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:13:15 2024 +0900

    message1

```

2. 빔에서 hello.txt 파일을 열고 기존의 내용 중에서 '2'를 지우고 'two'를 추가한 후 저장해라.


``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ vim hello.txt

```


```
1
two
~
~
~
~
~
~
~
~
hello.txt[+] [unix] (14:27 08/03/2024)                                   2,3 All
:wq


```

3. git status 명령을 사용해 깃의 상태를 확인해 보면 hello.txt 파일이 수정되었고, 아직 스테이징 상태가 아니라고 나온다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ vim hello.txt

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   hello.txt

no changes added to commit (use "git add" and/or "git commit -a")

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$

```


4. 방금 수정한 hello.txt 파일이 저장소에 있는 최신 버전의 helllo.txt와 어떻게 다른지 확인해 보겠다. 이 경우 git diff 명령을 사용한다.
``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git diff

```


5. 여러 내용이 나타나지만 여기에서는 필요한 부분만 본다. '-2' 최신 버전과 비교할 때 hello.txt 파일에서 '2'가 삭제된다는 뜻이다. '+two'라는 것은 hello.txt 파일에 'two'라는 내용이 추가되었다는 뜻이다.
``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git diff
warning: in the working copy of 'hello.txt', LF will be replaced by CRLF the next time Git touches it
diff --git a/hello.txt b/hello.txt
index 1191247..0b66db0 100644
--- a/hello.txt
+++ b/hello.txt
@@ -1,2 +1,2 @@
 1
-2
+two
```


이렇게 작업 트리에서 수정한 파일과 최신 버전을 비교한 다음, 수정한 내용으로 다시 버전을 만들려면 스테이지에 올린 후 커밋하고 수정한 내용을 버리려면 git checkout 명령을 사용해 수정 내용을 취소한다. git checkout 사용법은 조금 뒤에서 알아본다.



6. 마지막으로 이어지는 실습을 위해 hello.txt를 원래대로 되돌려 놓는다. 빔에서 hello.txt 파일을 열고 'two' 부분은 '2'로 수정한 후 저장한다.

``` shell
1
2
~
~
~
~
~
~
~
~
hello.txt[+] [unix] (14:43 08/03/2024)                                                          2,1 All
:wq

```


---
# 02-4 버전 만드는 단계마다 파일 상태 알아보기


깃에서는 버전을 만드는 각 단계마다 파일 상태를 다르게 표시한다. 그래서 파일의 상태를 이해하면 이 파일이 버전 관리의 여러 단계 중 어디에 있는지, 그 상태에서 어떤 일을 할 수 있는지 알 수 있다.



### tracked 파일과 untracked 파일

git status 명령을 사용하면 화면에 파일 상태와 관련된 여러 메시지가 나타난다. 작업 트리에 있는 파일은 크게 tracked와 untracked 상태로 나뉜다. 두 개의 상태가 무엇을 의미하는지 알아보겠다.



1. 빔에서 hello.txt 파일을 열고 숫자 '3'을 추가한 후 저장한다.

```
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ vim hello.txt




1
2
3
~
~
~
~
~
hello.txt[+] [unix] (11:34 09/03/2024)                                                          3,1 All
:wq
```


2. 다음과 같이 입력해서 hello2.txt라는 새로운 파일을 만들어 보겠다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ vim hello2.txt

```


3. 새로 만든 hello2.txt에 알파벳 a와 b,c,d를 한 줄에 한 글자씩 입력하고 파일을 입력하고 편집기를 종료한다.

```
a
b
c
d
~
~
~
~
~
~
hello2.txt[+] [unix] (08:59 01/01/1970)                                                         4,1 All
:wq

```


4. hello.txt 파일과 hello2.txt 파일 모두 작업 트리에 있다. git status 명령을 사용해 어떤 상태인지 확인해 보라.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git status



```

5. 앞에서 커밋했던 hello.txt 파일은 =='Changes not staged for commit:'이라고 되어 있다. 변경된 파일이 아직 스테이지에 올라가지 않았다는 뜻이다.== 그리고 파일 이름 앞에 modified: 라고 되어 있어 hello.txt가 수정되어 있음을 알 수 있다. 이렇게 깃은 한 번이라도 커밋을 한 파일의 수정 여부를 계속 추적한다. 깃이 추적하고 있다는 뜻에서 tracked 파일이라고 부른다.

반면에 hello2.txt 파일 앞에는 아무 것도 없고 바로 위에는 'untracked files:'이라고 되어 있다. hello2.txt 파일은 한 번도 깃에서 버전 관리를 하지 않았기 때문에 수정 내역을 추적하지 않다. 그래서 untracked 파일이라고 표시한다.


``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   hello.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        hello2.txt

no changes added to commit (use "git add" and/or "git commit -a")

```


6. 수정했던 hello.txt 파일과 hello2.txt 파일은 모두 git add 명령을 사용해서 스테이지에 올릴 수 있다.
``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git add .
warning: in the working copy of 'hello.txt', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'hello2.txt', LF will be replaced by CRLF the next time Git touches it
(만약 git add에 .를 붙이면 수정한 파일 전부 올릴 수 있다.)
```


7. git status를 사용해 상태를 확인해 보겠다. 마지막 버전 이후에 수정된 hello.txt는 modified:로 표시되고, 한 번도 관리하지 않았던 hello2.txt는 new file:로 표시된다. 또한 tracked 파일이나 untracked 파일 모두 스테이지에 올라온 것을 확인할 수 있다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   hello.txt
        new file:   hello2.txt

```


8. 이제 커밋해 보면, 이 커밋에서는 hello.txt를 수정한 내용과 새로 만든 hello2.txt 내용이 다 포함된다. 커밋 메시지는 다음과 같이 작성한다. 성공적으로 커밋이 되었다면 로그를 확인해 본다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git commit -m "message3"
[master 3a99c02] message3
 2 files changed, 5 insertions(+)
 create mode 100644 hello2.txt

```

```
git log
```


9. 'message3'라는 메시지를 붙인 커밋이 보인다. 그런데 각 커밋에 어떤 파일들이 관련된 것인지 알 수 없다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git log
commit 3a99c0259848ae3981c92e5420a786a42b162b86 (HEAD -> master)
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 12:27:08 2024 +0900

    message3

commit 0a0046a897e573da6fb40f27427d3c925ea7167d
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:29:59 2024 +0900

    message2

commit c37afa3a761d7808b7c9f7a3706037d5f801440c
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:13:15 2024 +0900

    message1

```

10. 커밋에 관련된 파일까지 함께 살펴보려면 git log 명령 (붙임표가 2개 붙인 ) --stat 옵션을 사용한다.
``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git log --stat

```


11. 가장 최근의 커밋부터 순서대로 커밋 메시지와 관련 파일이 나열된다. 'Message3' 커밋은 hello.txt, hello2.txt 파일과 관련되어 있고, 'message2'는 hello.txt 파일과 관련되었다는 것을 알 수 있다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git log --stat
commit 3a99c0259848ae3981c92e5420a786a42b162b86 (HEAD -> master)
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 12:27:08 2024 +0900

    message3

 hello.txt  | 1 +
 hello2.txt | 4 ++++
 2 files changed, 5 insertions(+)

commit 0a0046a897e573da6fb40f27427d3c925ea7167d
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:29:59 2024 +0900

    message2

 hello.txt | 1 +
 1 file changed, 1 insertion(+)

commit c37afa3a761d7808b7c9f7a3706037d5f801440c
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:13:15 2024 +0900

    message1

 hello.txt | 1 +
 1 file changed, 1 insertion(+)


```


로그 메시지가 너무 많을 경우 한 화면씩 나누어 보여준다. '**Enter**' 를 누르면 다음 로그 화면을 볼 수 있고, **'Q'** 를 누르면 로그 화면을 빠져 나와 다시 깃 명령을 입력할 수 있다.



### unmodified , modified, staged 상태


한 번이라도 버전을 만들었던 파일은 tracked 상태가 된다고 했다. tracked 상태인 파일은 깃 명령으로 파일 상태를 확인하면 현재 작업 트리에 있는지, 스테이지에 있는지 등 더 구체적인 상태를 알려준다. 깃의 커밋 과정 중에서 tracked의 파일의 상태가 어떻게 바뀌는지 확인해 보겠다.


``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ ls -al
total 18
drwxr-xr-x 1 kgm09 197609 0 Mar  9 11:39 ./
drwxr-xr-x 1 kgm09 197609 0 Mar  8 11:00 ../
drwxr-xr-x 1 kgm09 197609 0 Mar  9 12:27 .git/
-rw-r--r-- 1 kgm09 197609 6 Mar  9 11:38 hello.txt
-rw-r--r-- 1 kgm09 197609 8 Mar  9 11:39 hello2.txt


```


2. git status 명령을 사용해 깃의 상태와 파일의 상태를 살펴본다.
``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git status

```

3. 작업 트리에 아무 변경 사항도 없다. 'working tree clean'이라고 나타나면 현재 작업 트리에 있는 모든 파일의 상태는 unmodified, 즉 수정되지 않은 상태이다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git status
On branch master
nothing to commit, working tree clean

```

4. hello2.txt 파일을 수정해 보겠다. hello2.txt 에서 'a'만 남기고 나머지 내용을 삭제한 후 파일을 저장하고 편집기를 종료한다.

```

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ vim hello2.txt




a
~
~
~
~
~
~
~

~
~
hello2.txt[+] [unix] (11:39 09/03/2024)                                                            1,1 All
:wq

```


5. 다시 git status 명령을 실행한다. hello2.txt 파일이 수정되었고 아직 스테이지에 올라가지 않았다고 나타난다. 'Change not stage for commit:'이라는 메시지가 나타나면 파일이 수정만 된 modified 상태이다.


``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   hello2.txt

no changes added to commit (use "git add" and/or "git commit -a")


```


6. git add 명령을 사용해 스테이지에 올리고 git status 명령을 실행해 보아라. 커밋할 변경 사항이 있다고 한다. 이렇게 'Change to be commited:' 라는 메시지가 나타나면 커밋 직전 단계, 즉 **staged** 상태이다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git add hello2.txt
warning: in the working copy of 'hello2.txt', LF will be replaced by CRLF the next time Git touches it

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   hello2.txt


kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$

```

7. 스테이지에 있는 hello2.txt 파일을 커밋한다. 그리고 git status 명령을 실행한다. 커밋을 끝내고 난 후 hello2.txt 파일의 상태는 수정이 없던 **unmodified**로 돌아간 것을 볼 수 있다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git commit -m "delete b,c,d"
[master cad02bf] delete b,c,d
 1 file changed, 3 deletions(-)

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git status
On branch master
nothing to commit, working tree clean


```


지금까지 깃에서 버전을 만드는 과정에서 어느 단계에 있는지에 따라 달라지는 파일 상태를 살펴봉았다. 파일의 상태 변화는 다음과 같이 간단한 그림으로 정리할 수 있다. 이제 git status 명령으로 파일 상태를 보면 이 파일이 어느 단계에 있는지 알 수 있다.

``` mermaid

sequenceDiagram


participant untracked
participant unmodified
participant modified
participant staged


untracked->>staged: 스테이징
unmodified->>modified: 파일 수정
modified->>staged:스테이징
staged->>unmodified:커밋

```



---



> [!note] 한 걸음 더: 방금 커밋한 메시지 수정하기
> 문서의 수정 내용을 기록해둔 커밋 메시지를 잘못 입력했다간 커밋을 만든 즉시 커밋 메시지를 수정할 수도 있다. 가장 최근의 커밋 메시지를 수정하려면 git commit 명령어 --amend(붙임표 2개)를 붙인다.


``` shell

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git commit --amend



delete b,c,d

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# Date:      Sat Mar 9 16:49:42 2024 +0900
#
# On branch master
# Changes to be committed:
#       modified:   hello2.txt
#
~
~
~
~
~
.git/COMMIT_EDITMSG [unix] (17:00 09/03/2024)                                                      1,1 All
"~/Documents/hello-git/.git/COMMIT_EDITMSG" [unix] 11L, 269B

```

'**I**'를 눌러 입력 모드로 바꾸면 메시지를 수정할 수도 있다.

``` shell
message3

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# Date:      Sat Mar 9 16:49:42 2024 +0900
#
# On branch master
# Changes to be committed:
#       modified:   hello2.txt
#
~
~
~
~
~
~
~
~
~
~
~
.git/COMMIT_EDITMSG[+] [unix] (17:02 09/03/2024)                                                   1,8 All
:wq

```

수정 

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git commit --amend
[master 7c425c2] message3
 Date: Sat Mar 9 16:49:42 2024 +0900
 1 file changed, 3 deletions(-)

```


---
# 02-5 작업 되돌리기


앞에서 수정한 파일을 스테이지에 올리고 커밋하는 방법까지 살펴보았다. 파일을 수정하고 버전을 만들 때 여러 단계를 거쳤다. 이제부터는 스테이지에 올렸던 파일을 내리거나 커밋을 취소하는 등 각 단계로 돌아가는 방법에 대해 알아보겠다. 여기 나오는 방법까지 익히고 나면 훨씬 능숙하게 버전 관리를 할 수 있을 것이다.


### 작업 트리에서 수정한 파일을 되돌리기 - git checkout


파일을 수정한 뒤 소스가 정상적으로 동작하지 않는 등의 이유로 수정한 내용을 취소하고 가장 최신버전 상태로 되돌려야 할 때가 있다. 이럴 때 일일이 수정한 소스를 찾아서 직접  되돌려야 한다면 아주 번거로울 것이다. 이럴 때 checkout 명령을 사용하면 작업 트리에서 수정한 내용을 쉽게 취소할 수 있다.

hello.txt 파일을 수정하면서 checkout 명령의 사용법을 알아보겠다.



1. 먼저 빔 열고 숫자 '3'을 영문 'three'로 수정한 후 저장한다.
``` shell

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ vim hello.txt


1
2
three
~
~
~
~
~
~
~
~
hello.txt [unix] (17:15 09/03/2024)                                                                3,5 All
"hello.txt" [unix] 3L, 10B

```


2. 그렇다면 현재 파일 상태를 어떨까? git status 명령을 사용해 보아라. hello.txt가 수정되어 있지만 아직 스테이지에 올라가 있지 않는다. 그리고 두 번째 괄호 메시지를 보면 작업트리(디렉터리)의 변경 사항을 취소하려면 checkout을 사용하라고 되어 있다. 
``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   hello.txt

no changes added to commit (use "git add" and/or "git commit -a")

```

3. git checkout 명령 다음에 붙임표 2개(--)를 붙이고 한 칸 띈 다음 파일 이름을 쓰면 된다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git checkout -- hello.txt


```

4. 정상적으로 처리되면 화면에 아무것도 나타나지 않는다. hello.txt 파일의 수정 내용이 정말 사라졌는가? cat 명령어를 통하여 파일 내요을 확인해 보면 앞에서 '3'을 지우고 'three'를 추가했던 수정 내용이 사라지고 '3'이 그대로 남아 았는 것을 확인할 수 있다.
``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git checkout -- hello.txt

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ cat hello.txt
1
2
3

```



### 스테이징 되돌리기- git reset HEAD 파일 이름


앞에서는 파일의 이름을 취소하고 원래대로 되돌렸다. 이번에는 수정된 파일을 스테이징했을 때, 스테이징했을 때, 스테이징을 취소하는 방법을 알아보겠다. 여기에서는 앞에서 만든 hello2.txt 파일을 사용하겠다.


1. 빔을 사용해서 hello2.txt를 수정해 보겠다. 기존 내용을 삭제하고 대문자 'A,B,C,D'를 입력한 후 저장하고 빔을 종료한다.

``` shell

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ vim hello2.txt


a
b
c
d

~
~
hello2.txt[+] [unix] (16:45 09/03/2024)                                  4,1 All
:wq

```

2. git add 명령으로 hello2.txt 파일을 스테이지에 올린 후 git status 명령으로 파일의 상태를 살펴보아라.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git add hello2.txt
warning: in the working copy of 'hello2.txt', LF will be replaced by CRLF the next time Git touches it

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git status

```


3. 상태 메시지 중 괄호 안의 내용을 읽어 보아라. 스테이지에서 내리려면(to unstage)git reset HEAD 명령을 사용하라고 되어 있다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git reset HEAD hello2.txt

```


4. git reset 명령을 사용해 스테이지에서 hello2.txt를 내려 보겠다.

```shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git reset HEAD hello2.txt


```


> [!중요한 점] : HEAD 다음에 파일 이름을 인정하지 않으면 스테이지에 있는 모든 파일을 되돌린다.








5. 수정된 hello2.txt가 스테이지에서 내려졌다는(unstaged) 메시지가 나타난다.

```shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git reset HEAD hello2.txt
Unstaged changes after reset:
M       hello2.txt


```


6. 다시 git status를 사용해 파일의 상태를 확인할 수 있다. 파일이 아직 스테이지에 올라가기전(not staged)으로 돌아온 것을 알 수 있다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   hello2.txt

no changes added to commit (use "git add" and/or "git commit -a")

```



### 최신 커밋 되돌리기- git reset HEAD^


이번에는 수정된 파일을 스테이징하고 커밋까지 했을 때, 가장 마지막에 한 커밋을 취소하는 방법을 알아본다.


1. 다시 한 번 hello2.txt 문서를 수정해 보겠다. 빔을 열고 대문자 'E'를 끝에 추가한 후 저장한다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ vim hello2.txt


a
b
c
d
e
~
~
~
~
hello2.txt [unix] (19:28 09/03/2024)                                     5,1 All
"hello2.txt" [unix] 5L, 10B


```


2. git commit 명령을 사용해 스테이징과 커밋을 함께 실행한다. 커밋 메시지는 'message4'로 하겠다.
``` shell

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git commit -am "message4"
warning: in the working copy of 'hello2.txt', LF will be replaced by CRLF the next time Git touches it
[master c3af1b1] message4
 1 file changed, 4 insertions(+)


```


3. git log 명령을 사용하면 커밋 메시지가 'message4'인 커밋을 확인할 수 있다.

``` shell

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git log
commit c3af1b18e15df26a1655f9651fe6f2b9e88235d1 (HEAD -> master)
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 19:31:13 2024 +0900

    message4

commit 7c425c2335e9e2cb17a8c9b1812c93b181ac665c
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 16:49:42 2024 +0900

    message3

commit 3a99c0259848ae3981c92e5420a786a42b162b86
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 12:27:08 2024 +0900

    message3

commit 0a0046a897e573da6fb40f27427d3c925ea7167d
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:29:59 2024 +0900

    message2

commit c37afa3a761d7808b7c9f7a3706037d5f801440c
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:13:15 2024 +0900

    message1


```


4. 최신 커밋을 되돌리려면 git reset 명령 다음에 HEAD^를 붙인다. HEAD^는 현재 HEAD가 가리키는 브랜치의 최신 커밋을 가리킨다. git log 명령을 실행했을 때 가장 최신 커밋에 (HEAD->master)표시가 있던 것을 기억할 것이다. ==이렇게 되돌리면 커밋도 취소되고 스테이지에서도 내려진다. 취소한 파일이 작업 트리에만 남는 것이다.==

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git reset HEAD^

```


5. 커밋이 취소되고 스테이지에서도 내려졌다는 메시지가 나타난다.
``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git reset HEAD^
Unstaged changes after reset:
M       hello2.txt

```


6. 정말 커밋이 취소됐을까? git log 명령으로 알 수 있다. 메시지가 'message4'인 커밋이 사라진 것을 알 수 잇다. 이 방법으로 취소하면 이 커밋 전에 했던 스테이징도 함께 취소가 된다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git log
commit 3a99c0259848ae3981c92e5420a786a42b162b86 (HEAD -> master)
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 12:27:08 2024 +0900

    message3

commit 0a0046a897e573da6fb40f27427d3c925ea7167d
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:29:59 2024 +0900

    message2

commit c37afa3a761d7808b7c9f7a3706037d5f801440c
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:13:15 2024 +0900

    message1

```



##### 한 걸음 더
- git reset 명령의 옵션 살펴보기

reset 명령은 사용하는 옵션에 따라 되돌릴 수 있는 단계가 다르다.

| 명령            | 설명                                                                             |
| ------------- | ------------------------------------------------------------------------------ |
| --soft HEAD^  | 최근 커밋을 하기 전 상태로 작업 트리를 되돌린다.                                                   |
| --mixed HEAD^ | 최근 커밋과 스테이징을 하기 전 상태로 작업 트리를 되돌린다. 옵션 없이 git reset 명령을 사용할 경우 이 옵션을 기본으로 작동한다. |
| --hard HEAD^  | 최근 커밋과 스테이징, 파일 수정을 하기 전 상태로 작업 트리를 되돌린다. 이 옵션으로 되돌린 내용은 복구할 수 없다.             |
| --hard HEAD^  | 최근 커밋과 스테이징, 파일 수정을 하기 전 상태로 작업 트리를 되돌린다. 이 옵션으로 되돌린 내용은 복구할 수 없다.             |



### 특정 커밋으로 되돌리기 - git reset 커밋 해시

깃에서 파일을 수정하고 커밋할 때마다 저장된 버전들이 쌓여 있다. 앞에서 살펴본 git reset HEAD^ 명령으로 최신 커밋을 되돌릴 수도 있지만 특정 버전으로 되돌린 다음 그 이후 버전을 삭제할 수도 있다. 특정 커밋으로 되돌릴 때는 git reset 명령 다음에 커밋 해시를 사용한다.


1. git reset 명령을 연습해 보기 위해 몇 개의 커밋을 만들어 보겠다. 밤을 사용해 hello-git 디렉터리에 rev.txt를 만든다. 간단하게 영문자 'a'를 입력한 후 저장한다.

```shell
kgm09@kim_gyeong_min MINGW64 ~/Documents
$ vim rev.txt


a
~
~
~
~
~
~
~
~
~
rev.txt [unix] (20:52 09/03/2024)                                        1,1 All
"rev.txt" [unix] 1L, 2B


```

2. rev.txt를 스테이지에 올린 후 'R1' 메시지와 함께 커밋한다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git add rev.txt
warning: in the working copy of 'rev.txt', LF will be replaced by CRLF the next time Git touches it

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git commit -m "R1"
[master aa65456] R1
 1 file changed, 1 insertion(+)
 create mode 100644 rev.txt

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$

```


3. rev.txt 한 번 더 수정해서 영문자 'b'를 추가하고, 'R2' 메시지와 함께 커밋한다.

``` shell
a
b
~
~
~
~
~
~
~
~
~
~
rev.txt[+] [unix] (20:54 09/03/2024)                                                            2,1 All
:wq


kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ vim rev.txt

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git commit -am "R2"
warning: in the working copy of 'hello2.txt', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'rev.txt', LF will be replaced by CRLF the next time Git touches it
[master 3e39492] R2
 2 files changed, 2 insertions(+)





```



4. 같은 방법으로 rev.txt 영문자 'c'를 추가한 후 'R3' 메시지와 함께 커밋하고, rev.txt 영문자 'd'를 추가한 후 'R4' 메시지와 함께 커밋한다. 지금까지 모두 4번의 커밋을 했다.
```shell
a
b
c

~
~
~
~
rev.txt[+] [unix] (20:58 09/03/2024)                                                            3,1 All
:wq

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git add rev.txt
warning: in the working copy of 'rev.txt', LF will be replaced by CRLF the next time Git touches it

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git commit -m "R3"
[master 20a7af4] R3
 1 file changed, 1 insertion(+)


a
b
c
d
~
~
~
~
~
~
~
rev.txt[+] [unix] (21:04 09/03/2024)                                                            4,1 All
:wq



kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git commit -am "R4"
warning: in the working copy of 'rev.txt', LF will be replaced by CRLF the next time Git touches it
[master 8c205d4] R4
 1 file changed, 1 insertion(+)


```


5. git log 명령을 사용해 지금까지 만든 커밋을 확인해보라. 4개의 커밋이 있고 각 커밋마다 커밋 해시가 함께 나타나 있다. 여기에서는 R4 메시지가 있는 커밋을 R4 커밋으로, R3 메시지가 있는 커밋을 R3 커밋으로 부르겠다. 4개의 커밋 중 R2라는 메시지가 붙은 커밋으로 되돌려 보겠다. 즉 R2 메시지가 있는 커밋을 최신 커밋으로 만들 것이다.


``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git log
commit 8c205d40dd102df0a4ba286dda6f2f420f90567c (HEAD -> master)
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 21:06:19 2024 +0900

    R4

commit 20a7af485384e24e33a26b3349fce0470ccf5fa1
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 21:04:47 2024 +0900

    R3

commit 3e394926070dd747c116ce70fb20b0cf14208345
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 20:58:57 2024 +0900

    R2

commit aa654569f59870d175c83023639e02c630753e4a
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 20:57:15 2024 +0900

    R1

commit 3a99c0259848ae3981c92e5420a786a42b162b86
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 12:27:08 2024 +0900

    message3

commit 0a0046a897e573da6fb40f27427d3c925ea7167d
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:29:59 2024 +0900

    message2

commit c37afa3a761d7808b7c9f7a3706037d5f801440c
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:13:15 2024 +0900

    message1

```

> [!중요한 점] 커밋 해시는 커밋ID 라고도 한다.

> [!중요한 점] 커밋 목록이 너무 길어서 화면에 볼 수 없다면 'Enter'를 눌러 다음 화면을 볼 수 있다.




6. reset에서 커밋 해시를 사용해 되돌릴 때 주의할 점이 있다. 예를 들어 reset A를 입력한다면 이 명령은 A 커밋을 리셋하는 것이 아니라 최근 커밋을 A로 리셋한다. 즉 A 커밋을 삭제하는 것이 아니라 A 커밋 이후에 만들었던 커밋을 삭제하고, A 커밋으로 이동하겠다는 의미이다. R2 커밋으로 이동하기 위해 git log 명령의 결과 화면에서 R2 커밋의 커밋 해시를 선택한다. 그리고 마우스 오른쪽 버튼을 눌러 copy를 선택한다.


``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git log
commit 8c205d40dd102df0a4ba286dda6f2f420f90567c (HEAD -> master)
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 21:06:19 2024 +0900

    R4

commit 20a7af485384e24e33a26b3349fce0470ccf5fa1
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 21:04:47 2024 +0900

    R3

commit 3e394926070dd747c116ce70fb20b0cf14208345
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 20:58:57 2024 +0900

    R2

commit aa654569f59870d175c83023639e02c630753e4a
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 20:57:15 2024 +0900

    R1

commit 3a99c0259848ae3981c92e5420a786a42b162b86
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 12:27:08 2024 +0900

    message3

commit 0a0046a897e573da6fb40f27427d3c925ea7167d
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:29:59 2024 +0900

    message2

commit c37afa3a761d7808b7c9f7a3706037d5f801440c
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:13:15 2024 +0900

    message1

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git reset --hard 3e394926070dd747c116ce70fb20b0cf14208345
HEAD is now at 3e39492 R2

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$

```

7. git reset 명령 다음에 --hard 옵션까지 입력한 후 복사한 커밋 해시를 붙여넣는다. 그리고 Enter를 넣는다.

```
$ git reset --hard 복사한 커밋 해시
```


8. HEAD 방금 복사해서 붙인 커밋 해시 위치로 옮겨졌다고 나타난다. 즉, 방금 복사해서 붙인 커밋이 가장 최근 커밋이 된 것이다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git reset --hard 3e394926070dd747c116ce70fb20b0cf14208345
HEAD is now at 3e39492 R2

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$

```


9. git log 명령을 사용해서 로그 목록을 살펴보아라. 우리가 의도했던 대로 R4와 R3 메시지가 있던 커밋은 삭제되고 커밋 해시를 복사했던 커밋이 최신 커밋이 됐다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git log
commit 3e394926070dd747c116ce70fb20b0cf14208345 (HEAD -> master)
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 20:58:57 2024 +0900

    R2

commit aa654569f59870d175c83023639e02c630753e4a
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 20:57:15 2024 +0900

    R1

commit 3a99c0259848ae3981c92e5420a786a42b162b86
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 12:27:08 2024 +0900

    message3

commit 0a0046a897e573da6fb40f27427d3c925ea7167d
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:29:59 2024 +0900

    message2

commit c37afa3a761d7808b7c9f7a3706037d5f801440c
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:13:15 2024 +0900

    message1

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$

```


10. 하나 더 확인해 볼 것이 있다. rev.txt에 'b'를 포함한 것이 R2, 'c'를 추가한 것이 R3 커밋이었다. R2 커밋으로 되돌렸으니 rev.txt 어떻게 됐을까? cat 명령을 사용해서 rev.txt 파일을 확인해 보면 내용에 'b'까지만 있다. 이렇게 R4 커밋과 R3 커밋이 사라지고, R2 메시지가 있는 두 번째 커밋이 최신 커밋이 되었다.

``` shell

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ cat rev.txt
a
b


```


### 커밋 삭제하지 않고 되돌리기 -git revert

커밋으로 되돌릴 때 수정했던 것을 삭제해도 된다면 git reset 명령을 사용하면 되지만, 나중에 사용할 것을 대비해서 커밋을 되돌리더라도 취소한 커밋을 남겨두어야 할 때가 많다. 이때는 git reset이 아닌 git revert라는 명령을 사용한다.


``` shell

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ vim rev.txt


b
e
~
~
~
~
~
rev.txt[+] [dos] (21:11 09/03/2024)                                                             3,1 All
:wq
```


2. 수정한 rev.txt를 'R5'라는 메시지와 함께 커밋한다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git commit -am "R5"
[master fa69df2] R5
 1 file changed, 1 insertion(+)

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$



```


3. git log를 입력해 버전을 확인해 보라. rev.txt 파일에 대해 R1과 R2, R5 3개의 버전이 만들어졌다.



``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git log
commit fa69df2a1d42cd052fe9cba26a75f76cd4a9d683 (HEAD -> master)
Author: kgm0927 <initial929@gmail.com>
Date:   Sun Mar 10 10:20:17 2024 +0900

    R5

commit 3e394926070dd747c116ce70fb20b0cf14208345
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 20:58:57 2024 +0900

    R2

commit aa654569f59870d175c83023639e02c630753e4a
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 20:57:15 2024 +0900

    R1

commit 3a99c0259848ae3981c92e5420a786a42b162b86
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 12:27:08 2024 +0900

    message3

commit 0a0046a897e573da6fb40f27427d3c925ea7167d
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:29:59 2024 +0900

    message2

commit c37afa3a761d7808b7c9f7a3706037d5f801440c
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:13:15 2024 +0900

    message1

```

4. 가장 최근에 커밋한 R5 버전을 취소하고, R5 직전 커밋 R2로 되돌아가려고 한다. 앞에서 공부했던 reset의 경우에는 R2로 가기 위해서 reset 명령 뒤에 R2의 커밋 해시를 지정했지만, revert 명령의 경우에는 revert 명령 뒤에 취소하려고 하는 버전, 즉 R5의 커밋 해시를 지정한다. 먼저 revert할 R5 커밋 해시를 복사한다.


5. 로그 화면이 너무 길다면 '*Q*'를 눌러서 프롬프트($)를 표시한다. 그리고 아래와 같이 입력하라.
```
git revert 복사한 R5 커밋 해시
```


6. revert 명령을 실행할 때는 깃을 설치할 때 지정했던 기본 편집기가 자동으로 나타나면서 커밋 메시지를 입력할 수 있다. 커밋 메시지 맨 위에는 어떤 버전을 revert 했는지 나타나 있다. 문서 맨 위에 revert하면서 추가로 남겨둘 내용이 있다면 입력하고 저장한다.

``` shell
Revert "R5"

일시적으로 커밋 보류함.

This reverts commit fa69df2a1d42cd052fe9cba26a75f76cd4a9d683.

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# On branch master
# Changes to be committed:
#       modified:   rev.txt
#
~
~
~
~
~
~
~
~
~
.git/COMMIT_EDITMSG[+] [unix] (10:25 10/03/2024)                                            3,33-23 All
:wq

```


7. 'R5' 버전이 revert 되었다는 간단한 메시지가 나타나는데, 실제로 버전이 어떻게 바뀌었는지 확인해 보자. git log를 입력해 보라.

```
$ git log
```


8. 로그에 R5를 revert한 새로운 커밋이 생겼다. 그리고 기존의 R5 역시 사라지지 않았다. R5 버전을 지우는 대신 R5에서 변경했던 이력을 취소한 새 커밋을 만든 것이다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ git log
commit 82c6671deb790f5c795fc269cdd64991ebb6a36d (HEAD -> master)
Author: kgm0927 <initial929@gmail.com>
Date:   Sun Mar 10 10:25:39 2024 +0900

    Revert "R5"

    일시적으로 커밋 보류함.

    This reverts commit fa69df2a1d42cd052fe9cba26a75f76cd4a9d683.

commit fa69df2a1d42cd052fe9cba26a75f76cd4a9d683
Author: kgm0927 <initial929@gmail.com>
Date:   Sun Mar 10 10:20:17 2024 +0900

    R5

commit 3e394926070dd747c116ce70fb20b0cf14208345
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 20:58:57 2024 +0900

    R2

commit aa654569f59870d175c83023639e02c630753e4a
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 20:57:15 2024 +0900

    R1

commit 3a99c0259848ae3981c92e5420a786a42b162b86
Author: kgm0927 <initial929@gmail.com>
Date:   Sat Mar 9 12:27:08 2024 +0900

    message3

commit 0a0046a897e573da6fb40f27427d3c925ea7167d
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:29:59 2024 +0900

    message2

commit c37afa3a761d7808b7c9f7a3706037d5f801440c
Author: kgm0927 <initial929@gmail.com>
Date:   Fri Mar 8 14:13:15 2024 +0900

    message1


```


9. 방금 취소한 R5 커밋은 rev.txt 문서에 영문자 'e'를 추가한 것이었다. R5 커밋을 취소한 것이 문서에도 반영되었는지 확인해 보겠다. 다음과 같이 입력하라.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ cat rev.txt
a
b

```


10. 앞에서 추가했던 'e'가 없어진 것을 볼 수 잇다. 이렇게 revert 명령을 사용하면서 버전에 있던 이력을 취소할 수 있다.

``` shell

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$ cat rev.txt
a
b

kgm09@kim_gyeong_min MINGW64 ~/Documents/hello-git (master)
$

```