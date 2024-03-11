

지금까지 우리는 자신의 컴퓨터에서 작업한 뒤 그 컴퓨터 안에 커밋을 저장했다. 이 저장소를 지역 저장소(local repository)라고 부른다. 만약 실수로 지역 저장소를 삭제한다면 그때까지 작업했던 내용이 다 사라져버릴 것이다. 


### 원격 저장소란

깃에서는 지역 저장소와 원격 저장소(remote repository)를 연결해서 버전 관리하는 파일들을 쉽게 백업시킬 수 있다. 원격 저장소는 지역 저장소가 아닌 컴퓨터나 서버에 만든 장소를 말한다.


원격 저장소를 직업 구축할 수도 있지만 유지하는 것이 쉽지 않다. 그래서 인터넷에서 원격 저장소를 제공하는 서비스를 주소 사용한다. 그 중에서 깃과 관련해 가장 많이 사용하는 서비스가 바로 깃허브이다.


### 깃허브로 할 수 있는것

- 원격 저장소에서 깃을 사용할 수 있다.
- 지역 저장소를 백업할 수 있다.
- 협업 프로젝트에 사용할 수 있다.
- 자신의 개발 이력을 남길 수 있다.
- 다른 사람의 소스를 살펴볼 수 있고, 오픈 소스에 참여할 수 있다.


(나머지에 있는 것은 생략하고 04-3로 넘어간다.)


---
# 04-3 지역 저장소를 원격 저장소에 연결하기


원격 저장소를 만들었으니 이제 지역 저장소에서 한 작업을 원격 저장소로 올리거나 원격 저장소에 있는 파일을 지역 저장소로 내려받아 작업해 보겠다. 이를 위해서는 먼저 지역 저장소와 원격 저장소를 연결해야할 것이다.


1. local-git을 줄인 loc-git이라는 이름으로 새 디렉터리를 만드록 지역 저장소로 초기화하겠다. 


``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents
$ git init loc-git
Initialized empty Git repository in C:/Users/kgm09/Documents/loc-git/.git/

kgm09@kim_gyeong_min MINGW64 ~/Documents
$ cd loc-git

kgm09@kim_gyeong_min MINGW64 ~/Documents/loc-git (master)
$ vim f1.txt



```


2. f1.txt에는 간단하게 영문자 'a'만 입력하고 파일을 저장한 후 편집기를 종료한다.

``` shell
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
~
f1.txt[+] [unix] (08:59 01/01/1970)                                      1,1 All
:wq

```


3. f1.txt를 스테이지에 올린 후 커밋한다. 커밋 메시지는 'add a'라고 하겠다. git log 명령으로 커밋이 잘 되어 있는지 확인해라. 아직 터미널 창은 닫지 말고.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/loc-git (master)
$ vim f1.txt

kgm09@kim_gyeong_min MINGW64 ~/Documents/loc-git (master)
$ git add f1.txt
warning: in the working copy of 'f1.txt', LF will be replaced by CRLF the next time Git touches it

kgm09@kim_gyeong_min MINGW64 ~/Documents/loc-git (master)
$ git commit -m "add a"
[master (root-commit) 9ca327e] add a
 1 file changed, 1 insertion(+)
 create mode 100644 f1.txt

kgm09@kim_gyeong_min MINGW64 ~/Documents/loc-git (master)
$ git log
commit 9ca327e0bdbfe1caca5f32abbe04b303f862e157 (HEAD -> master)
Author: kgm0927 <initial929@gmail.com>
Date:   Mon Mar 11 12:24:27 2024 +0900

    add a

```


### 원격 저장소에 연결하기


사용자 컴퓨터에 있는 지역 저장소를 깃허브에 있는 원격 저장소와 연결해 보겠다. 깃허브 저장소 화면에서 알려준 여러 가지 접속 방법 중 '커맨드 라인에서 기존 저장소를 푸시하기(... or push an existing repository from the command line)'방법을 사용해서 지역 저장소에 있는 파일을 원격 저장소에 올려 보겠다.


1. 지역 저장소와 원격 저장소를 연결하려면 깃허브의 저장소 주소를 전부 알고 있어야 한다. 웹 브라우저에서 아까 새로 만든 깃허브 저장소에 접속하라. 화면 위쪽의 깃허브 주소 오른쪽에 있는 것을 누르면 주소가 복사된다.

2. 저장소 주소를 복사했다면 터미널 창에 다음과 같이 입력한다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/loc-git (master)
$ git remote add origin https://github.com/kgm0927/test-1.git

```


이 명령은 원격 저장소(remote)에 origin을 추가(add)하겠다고 깃에게 알려주는 것이다. 여기에서 origin은 깃허브 저장소 주소( https://github.com/kgm0927/test-1.git)를 가리킨다. 깃허브 저장소 주소를 그대로 쓰면 너무 길기 때문에 origin이라는 단어로 줄여서 remote에 추가하는 것이다. 이렇게 지역 저장소를 특정 원격 저장소에 연결하는 것은 한 번만 하면 된다.


> [! 중요한 점] 깃에서 기본 브랜치를 master라고 하는 것처럼 기본 원격 저장소에는 origin이라는 이름을 사용한다.


3. 오류 메시지 없이 프롬프트($)가 나타나면 제대로 실행한 것이다.



4. 원격 저장소(remote)에 제대로 연결됐는지 확인해 본다. 다음과 같이 git remote 명령에 -v 옵션을 붙여서 입력해 보라.

```
$ git remote -v
```


5. 다음과 같이 remote에 origin이 연결되어 있고 origin이 가리키는 주소가 바로 옆에 표시될 것이다. 주소 끝에 있는 fetch와 push에 대해서는 아픙로 배울 것임므로 여기에서는 지역 저장소가 원격 저장소에 잘 연결 되어있는지 만 확인해라. 



---

# 04-4 원격 저장소에 올리기 및 내려받기


지역 저장소와 원격 저장소를 연결했으니 ==이제부터 지역 저장소의 소스를 원격 저장소에 올릴 수도 있고, 원격 저장소에 있는 소스를 지역 저장소로 내려받을 수 있다.== 이때 지역 저장소의 소스를 원격 저장소로 올리는 것은 **'푸시(push)'** 라고 하고, 원격 저장소에서 지역 저장소로 내려받는 것은 **'풀(pull)'** 이라고 한다.



###  원격 저장소에 파일 올리기 - git push


먼저 지역 저장소의 커밋을 원격 저장소로 보내는 푸시를 알아보겠다. 원격 저장소에 처음 접속할 대 나타나는 내용 중 두 번째 항목이 바로 푸시하라는 명령이다. 직접 사용해 보겠다.


1. 터미널 창에서 다음과 같이 입력하라. ==지역 저장소의 브랜치를 origin, 즉 원격 저장소의 master 브랜치로 푸시하라는 명령이다.== 여기에서 '-u' 옵션은 지역 저장소의 브랜치를 원격 저장소의 master 브랜치에 연결하기 위한 것으로 처음에 한 번만 사용하면 된다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/loc-git (master)
$ git push -u origin master



```

2. 터미널 창에서 푸시가 진행되는 것을 볼 수 있다. 푸시가 끝나면 프롬프트($)가 나타난다.
``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/loc-git (master)
$ git push -u origin master
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Writing objects: 100% (3/3), 203 bytes | 203.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/kgm0927/test-1.git
 * [new branch]      master -> master
branch 'master' set up to track 'origin/master'.

kgm09@kim_gyeong_min MINGW64 ~/Documents/loc-git (master)
$

```


3. 푸시가 끝났다는 것은 지역 저장소의 커밋이 원격 저장소로 올라갔다는 의미이다. 푸시가 끝났으면 깃허브 저장소가 열려있는 웹 브라우저 창으로 돌아와 **'F5'** 를 눌러 새로고침해 보라. 지역 저장소에 있던 f1.txt 파일이 원격 저장소로 올라와 았다.



4. 파일 목록 위의 '1 commit'을 눌러 보라. 지역 저장소에서 커밋했던 내용이 똑같이 올라와 있을 것이다.


5. 한 번이라도 지역 저장소와 원격 저장소를 연결해서 푸시했다면 그다음부터는 더 간단하게 푸시할 수 있다. 지역 저장소에서 또 다른 커밋을 만들고 다시 푸시해 보겠다. 빔으로 f1.txt를 다시 열어라.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/loc-git (master)
$ vim f1.txt


```

6. 원래 내용 다음에 영문자 'b'를 추가하고 저장 및 종료한다.

``` shell

a
b
~
~
~

```

7. 다음 명령을 사용해 스테이징과 커밋을 한꺼번에 실행한다. .git commit 명령에서 -am 은 스테이징 옵션(-a)과 메시지 옵션(-m)을 함께 쓴 것으로 최소한 한 번이라도 커밋한 파일(tracked)이어야 사용할 수 있다. 커밋 메시지는 'add b' 라고 하겠다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/loc-git (master)
$ git commit -am "add b"
warning: in the working copy of 'f1.txt', LF will be replaced by CRLF the next time Git touches it
[master a51d5a4] add b
 1 file changed, 1 insertion(+)

```

8. 지역 저장소에 새로운 커밋이 만들어졌으니 원격 저장소로 푸시해 볼까? 이미 앞에서 지역 저장소의 브랜치와 origin의 master 브랜치를 연결했기 때문에 다시 파일을 푸시할 때는 git push라고만 입력하면 된다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/loc-git (master)
$ git push

```

9. 방금 만든 커밋이 원격 저장소로 푸시된다.

``` shell

kgm09@kim_gyeong_min MINGW64 ~/Documents/loc-git (master)
$ git push
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Writing objects: 100% (3/3), 235 bytes | 235.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/kgm0927/test-1.git
   fbef7f8..a51d5a4  master -> master


```

10. 웹 브라우저에서 킷허브 저장소 화면을 새로고침해 보아라. 파일 목록에서 파일 이름 오른쪽에 최신 커밋 메시지가 나타나는데, 방금 커밋한 'add b'라는 커밋 메시지가 보인다. f1.txt 파일을 눌러 보라.
11. 가장 최근에 수정한 파일 내용인 a와 b가 들어 있다.


### 깃허브 사이트에서 직접 커밋하기


보통 지역 저장소와 원격 저장소를 연결한 후 지역 저장소의 커밋을 원격 저장소에 푸시하는 방법은 많이 사용하지만, ==깃허브 사이트에서 직업 커밋할 수도 있다.== 지역 저장소가 있는 컴퓨터를 사용할 수 없을 대 편리할 것이다.


1. 깃허브의 저장소로 접속한다. 앞에서 푸시한 f1.txt파일이 있다. 여기에 새로운 파일을 추가해 보겠다. [create new file]을 눌러라.
2. 맨 위에 파일 이름을 입력한 후 내용을 작성한다. 여기에서는 파일 이름으로 f2.txt 내용으로 숫자 '1,2,3'을 입력했다.

3. 파일 내용을 입력했다면 화면 아래로 이동해 보라. 기본적인 커밋 메시지('Create f2.txt')가 입력되어 있다. 이 메시지를 그냥 사용하거나 원하는 내용으로 수정한 후 [Commit new file]을 누른다.
4. 원격 저장소에 새로운 커밋이 추가되었다.



### 원격 저장소에서 파일 내려받기 -git pull


원격 저장소에 있는 소스파일을 다른 사용자가 수정했거나 깃허브 사이트에서 직접 커밋하면 지역 저장소와 차이가 생간다. 이럴 때는 원격 저장소와 지역 저장소의 상태를 같게 만들기 위해 원격 저장소의 소스를 지역 저장소로 가져온다. 이것을 '풀(pull)한다'고 한다. 
풀 하는 방법을 알아보도록 하겠다.

1. 앞에서 loc-git 지역 저장소를 원격 저장소에 연결한 후 푸시했다. 그리고 깃허브 사이트에서 바로 f2.txt라는 파일을 새로 만들었다. ==그리므로 loc-git 지역 저장소에는 아직 f2.txt 파일이 없다.== 터미널 창에서 loc-git 디렉터리로 이동한 후 ls 명령을 통해 디렉터리 안의 내용을 확인해 보라. 지역 저장소에서 만든 f1.txt 파일만 있을 것이다.


``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/loc-git (master)
$ ls
f1.txt
```
2. 다음 명령은 origin(원격 저장소)의 내용을 master 브랜치로 가져온다는 뜻이다.
```
kgm09@kim_gyeong_min MINGW64 ~/Documents/loc-git (master)
$ git pull origin master


kgm09@kim_gyeong_min MINGW64 ~/Documents/loc-git (master)
$ git pull origin master
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 918 bytes | 131.00 KiB/s, done.
From https://github.com/kgm0927/test-1
 * branch            master     -> FETCH_HEAD
   a51d5a4..a173515  master     -> origin/master
Updating a51d5a4..a173515
Fast-forward
 f2.txt | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 f2.txt

```

3. 원격 저장소에서 소스 파일을 가져오는 과정이 화면에 나타난다. $가 화면에 표시되면 가져오기가 끝난 것이다.


4. git log 명령으로 커밋 로그를 확인해 보겠다. 깃허브 사이트에서 만들었던 'Create f2.txt'라는 커밋이 지역 저장소 커밋 로그에도 나타나는 것을 확인할 수 있다.
``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/loc-git (master)
$ git log
commit a173515967e036ee45ea209c822fe42c06ef2119 (HEAD -> master, origin/master)
Author: kgm0927 <87554722+kgm0927@users.noreply.github.com>
Date:   Mon Mar 11 13:24:10 2024 +0900

    Create f2.txt

commit a51d5a4f3b8a140751bb9e70bdac3a7651e74507
Author: kgm0927 <initial929@gmail.com>
Date:   Mon Mar 11 13:05:26 2024 +0900

    add b

commit fbef7f82c0d787e15a3b5329f24400ff5d291333
Author: kgm0927 <initial929@gmail.com>
Date:   Mon Mar 11 12:33:06 2024 +0900

    add a

```



---
# 04-5 깃허브에서 SSH 원격 접속하기

여기에서는 Secure Shell, 줄여서 SSH라는 방법을 통해서 깃허브에 접속하는 법을 알아보도록 하겠다.

### SSH 원격 접속이란

SSH는 Secure Shell의 줄임말로 보안이 강화된 안전한 방법으로 정보를 교환하는 방식이다. SSH에서는 기본적으로 프라이빗 키(Private key)와 퍼블릭 키(Public Key)를 한 쌍으로 묶어서 컴퓨터를 인증한다. **private은 '사적의, 비밀의, 비공개의'** 라는 뜻을, **public '공적인, 공개의'라는 뜻** 을 가지고 있다. 퍼블릭 키는 말 그대로 외부로 공개되는 키이고, 프라이빗 키는 아무도 알 수 없게 사용자 컴퓨터에 저장되는 키이다. 사용자 컴퓨터에서 SSH 키 생성기를 실행하면 **프라이빗 키**와 **퍼블릭 키**가 만들어진다.


일반적으로 깃허브의 원격 저장소에 파일을 올리는 등의 작업을 하기 위해서는 아이디와 비밀번호를 입력해서 깃허브에게 내가 해당 저장소를 만든 주인임을 인증해야 한다. 웹 브라우저에 깃허브 저장소에 접속할 때나 SourceTree 같은 프로그램을 사용해 깃허브 저장소에 접속할 때 이런 방법을 사용한다.

이에 비해 SSH 원격 접속은 **프라이빗 키**와 **퍼블릭 키**를 사용해 현재 사용하고 있는 기기를 깃허브에 인증하는 방식이다. 예를 들어서 서버 환경에서 깃허브 저장소에 접속해야 한다면 서버 자체를 깃허브에 등록하고, 개인 노트북으로 접속한다면 노트북을 깃허브에 등록해 둔다. 이렇게 하면 터미널 창을 이용할 수 있는 상태라면 언제 어디서든 깃허브에 접속할 수 있다. 또 터미널 창에서 깃허브를 쓰다 보면 아이디와 비밀번호를 요구하는 경우가 많은데, SSH 접속 방법을 사용하면 자동 로그인 기능을 통해 이러한 번거로움을 줄일 수 있다.


### SSH 키 생성하기

사용자 컴퓨터에서 SSH 키 생성기를 사용하면 프라이빗 키와 퍼블릭 키가 만들어 진다고 했다. 이 키들이 어디에 생성하고 어떤 용도로 사용되는지 알아보겠다.


1. 터미널 창에서 홈 디렉터리로 이동한다. 그리고 ssh-keygen 이라고 입력한다. 화면에 SSH 키가 저장되는 디렉터리 경로가 표시되면서 파일 이름을 입력하라고 한다. SSH키가 저장되는 디렉터리는 홈 디렉터리 안에 있는 .ssh 디렉터리임을 확인할 수 있다. 파일 이름은 입력하지 말고 'Enter'를 누른다.
``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/loc-git (master)
$ ssh-keygen
Generating public/private ed25519 key pair.
Enter file in which to save the key (/c/Users/kgm09/.ssh/id_ed25519):

```

2.  두 번 더 'Enter'를 누르면 화면에 SSH를 통해서 다른 컴퓨터에 접속할 수 있는 비밀번호가 생성된다. 실제로 내부를 들여다보면 굉장히 복잡한 비밀번호이기 때문에 외부에서 쉽게 공격할 수 없다. 화면에서는 몇 가지 파일 경로가 표시되는데 그중에id_ed 파일이 프라이빗 키이고, id_ed.pub 파일이 퍼블릭 키이다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/loc-git (master)
$ ssh-keygen
Generating public/private ed25519 key pair.
Enter file in which to save the key (/c/Users/kgm09/.ssh/id_ed25519):
/c/Users/kgm09/.ssh/id_ed25519 already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /c/Users/kgm09/.ssh/id_ed25519
Your public key has been saved in /c/Users/kgm09/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:tpzpy6I2DFzchIypo942jDTnYm7nt7DkRgWqMiCZIgE kgm09@kim_gyeong_min
The key's randomart image is:
+--[ED25519 256]--+
|E  + .           |
|. o.o .          |
| =...o           |
|X.  o..          |
|Bo ..   S        |
|=ooo   o +       |
|+.O=    =        |
| *oO*..o         |
|+.*=++..+.       |
+----[SHA256]-----+

```

3. 정말 이 키들이 .ssh 디렉터리에 저장되어 있는지 확인해 보겠ㄷ. .ssh 디렉터리는 홈 디렉터리 하위에 만들어지므로 터미널 창에 다음과 같이 입력해서 .ssh 디렉터리로 한 번에 이동한 후 그 안의 내용을 살펴보겠다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~
$ ls .ssh
id_ed25519  id_ed25519.pub

kgm09@kim_gyeong_min MINGW64 ~
$ cd .ssh

kgm09@kim_gyeong_min MINGW64 ~/.ssh
$ ls -al
total 14
drwxr-xr-x 1 kgm09 197609   0 Feb  3 15:45 ./
drwxr-xr-x 1 kgm09 197609   0 Mar 11 13:39 ../
-rw-r--r-- 1 kgm09 197609 411 Mar 11 14:25 id_ed25519
-rw-r--r-- 1 kgm09 197609 102 Mar 11 14:25 id_ed25519.pub

kgm09@kim_gyeong_min MINGW64 ~/.ssh
$

```


### 깃허브에 퍼블릭 키 전송하기

앞에서 만든 키를 사용해 보기 전에 SSH 방식으로 깃허브 저장소에 접속하는 과정을 간단히 살펴보겠다. SSH 방식으로 접근하려면 먼저 사용자 컴퓨터에 만들어져 있는 퍼블릭 키를 깃허브 서버로 전송한 다음 저장한다.


``` mermaid

flowchart LR

private(프라이빗 키: 사용자 컴퓨터) --> public(깃허브 서버: 퍼블릭 키)

```


사용자 컴퓨터에서 깃허브 저장소에 접속하려면 사용자 컴퓨터에 있는 프라이빗 키와 깃허브 서버에 있는 퍼블릭 키를 비교한다. 퍼블릭 키와 프라이빗 키는 한 쌍이므로 두 개의 키가 서로 맞으면 사용자 컴퓨터와 깃허브 저장소가 연결된다.



``` mermaid

flowchart BT
pr[프라이빗 키]---pu[퍼블릭 키]--> public(깃허브 서버: 퍼블릭 키)

pr-->private(프라이빗 키: 사용자 컴퓨터)
```


접속 과정을 간단히 알아봤으니 이제부터 직접 SSH 방식을 사용해서 깃허브에 접속해 보겠다.


1. SSH키를 만들면 먼저 퍼블릭 키를 깃허브에 올려야 한다. 퍼블릭 키가 담겨 있는 id_ed25519.pub 파일의 내용을 확인해 보겠다. 아직 .ssh 디렉터리에 있지 않다면 .ssh 디렉터리로 이동한 다음 cat 명령을 사용해서 파일을 열어 보라.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/.ssh
$ cat id_ed25519
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW
QyNTUxOQAAACAPELvFMZxYYeUt34521sTYjs4hI5wmacWoxhrrBXdh7QAAAJjf64jk3+uI
5AAAAAtzc2gtZWQyNTUxOQAAACAPELvFMZxYYeUt34521sTYjs4hI5wmacWoxhrrBXdh7Q
AAAEAaVmoy7ukHE8T8VI2QaaXt21maIvBN6xLzpLbgo4/NFw8Qu8UxnFhh5S3fjnbWxNiO
ziEjnCZpxajGGusFd2HtAAAAFGtnbTA5QGtpbV9neWVvbmdfbWluAQ==
-----END OPENSSH PRIVATE KEY-----


```

2. 몇 줄에 걸친 문자열이 나타난다. 이것이 퍼블릭 키에 담긴 내용이다. 이 내용을 깃허브 서버에 올려야 한다. 앞부분에 있는 글자부터 문자열 끝까지 선택한 후 마우스 오른쪽 버튼을 누르고 [Copy]를 선택한다.
3. 웹 브라우저에서 깃허브에 접속한 후 로그인한다. 그리고 화면 오른쪽 위에 있는 사용자 아이콘을 누른 후[Settings]를 선택한다.
4. 여러 설정 메뉴 중[SSH and GPG keys]를 누른 후 퍼블릭 키를 추가하기 위해 화면 오른쪽에 나타난 [New SSH key]를 누른다.
5.  SSH 중 퍼블릭 키는 여러 개를 등록할 수 있기 때문에 Title 항목에 현재 등록하는 SSH 퍼블릭 키를 쉽게 알아볼 수 있도록 제목을 붙인다. 그리고 Key 항목에 앞에서 복사한 퍼블릭 키 값을 붙여 넣는다.
6. Key 항목에 ssh-rsa로 시작하는 키 값을 입력했다면 [Add SSH key] 버튼을 눌러서 SSH 키를 추가한다.
7. 퍼블릭 키를 추가할 때 비밀번호를 한번 확인한다.
8. 만들었던 SSH 키 중에서 퍼블릭 키를 깃허브 서버에 올렸다. 이제 SSH 키를 만들었던 컴퓨터는 깃허브 저장소의 SSH 주소만 알고 있으면 로그인 정보를 입력하지 않고도 그 저장소에 접속할 수 있다.


### SSH 주소로 원격 저장소 연결하기

SSH 원격 접속 준비가 끝났다. 이제 **==SSH 주소를 사용해 지역 저장소와 원격 저장소를 연결==**해 보겠다.


1. 깃허브 사이트에서 화면 오른쪽에 있는 [+]를 누른 후 [New repository]를 선택한다. 저장소 이름을 입력한 후 [Create repository]를 눌러서 저장소를 만든다.
2. 저장소가 만들어지면 HTTPS 주소가 나타난다. 우리는 SSH 방식으로 접근할 것이므로 [SSH]를 눌러서 SSH 주소를 표시한다. 그리고 SSH 주소를 복사한다.
3. 이제 SSH 방식으로 접속해 보겠다. 홈 디렉터리에 connect-ssh 저장소를 만든 후 해당 디렉터리로 이동한다.
``` shell
kgm09@kim_gyeong_min MINGW64 ~/.ssh
$ cd ~/Documents

kgm09@kim_gyeong_min MINGW64 ~/Documents
$ git init connect-ssh
Initialized empty Git repository in C:/Users/kgm09/Documents/connect-ssh/.git/

kgm09@kim_gyeong_min MINGW64 ~/Documents
$ cd connect-ssh

kgm09@kim_gyeong_min MINGW64 ~/Documents/connect-ssh (master)
$

```

4. SSH 주소를 사용해 원격 저장소에 연결하는 방법은 HTTPS 주소를 사용할 때와 같다. git remote add origin 명령 뒤에 복사한 주소를 붙여 넣는다.

git remote add origin *복사한 주소 붙여넣기*

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents/connect-ssh (master)
$ git remote add origin git@github.com:kgm0927/connect-ssh.git

kgm09@kim_gyeong_min MINGW64 ~/Documents/connect-ssh (master)
$ git remote -v
origin  git@github.com:kgm0927/connect-ssh.git (fetch)
origin  git@github.com:kgm0927/connect-ssh.git (push)

kgm09@kim_gyeong_min MINGW64 ~/Documents/connect-ssh (master)
$

```

5. 오류 메시지 없이 프롬프트($)가 표시되면 정상적으로 연결된 것이다. git remote 명령 뒤에 -v 옵션을 붙여서 어떤 저장소가 연결되었는지 확인할 수 있다. 이제부터는 원격 저장소를 사용하는 동안 로그인 정보를 요구하지 않기 때문에 좀 더 편하게 푸시나 풀을 할 수도 있다.

``` shell
kgm09@kim_gyeong_min MINGW64 ~/Documents
$ cd connect-ssh

kgm09@kim_gyeong_min MINGW64 ~/Documents/connect-ssh (master)
$ git remote add origin git@github.com:kgm0927/connect-ssh.git

kgm09@kim_gyeong_min MINGW64 ~/Documents/connect-ssh (master)
$ git remote -v
origin  git@github.com:kgm0927/connect-ssh.git (fetch)
origin  git@github.com:kgm0927/connect-ssh.git (push)

kgm09@kim_gyeong_min MINGW64 ~/Documents/connect-ssh (master)
$

```