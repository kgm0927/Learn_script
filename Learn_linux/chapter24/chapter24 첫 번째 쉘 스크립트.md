
### 지금까지 커맨드라인 도구들을 모아서 하나의 강력한 무기고를 만들었다. 이러한 도구들로 수많은 시스템 문제들을 하나로 해결할 수 있지만 매번 일일이 커맨드라인에서 사용하는데 한계가 있다. 만약 각각의 도구 대신 쉘 그 자체를 활용할 수 있다면 더 좋지 않을까? 물론, 가능한 일이다. 우리가 직접 설계한 프로그램에 커맨드라인 툴들을 모아놓으면 쉘이 스스로 복잡한 작업들을 순차적으로 수행할 수 있다. 이를 위해 우리는 ==쉘 스크립트==를 작성한다.

---
# 쉘 스크립트란

간단히 말해서, 쉘 스크립트는 명령어들이 나열되어 있는 파일들이다. 쉘은 이 파일을 읽어서 마치 커맨드라인에 직접 명령어를 입력하여 실행하는 것처럼 수행한다.

쉘은 시스템의 강력한 커맨드라인 인터페이스라는 점과 스크립트 언어 인터프리터라는 점에서 조금 독특하다. 앞에서 살펴보게 되겠지만 커맨드라인에서 할 수 있는 작업 대부분이 스크립트를 통해서도 가능하면 스크립트에서 할 수 있는 작업 또한 커맨드라인에서 가능하다.

우리는 다양한 쉘 기능을 다룰 예정이지만 그 중에서도 주로 커맨드라인에서 자주 사용되는 기능에 집중할 것이다. 또한 쉘은 프로그램을 작성할 때에도 일반적으로 사용되는(항상 그런 것은 아니다) 기능들을 제공한다.

---
# 쉘 스크립트 작성 방법

쉘 스크립트를 만들고 성공적으로 실행하려면 세 가지 작업이 필요하다.

1. **스크립트 작성하기.** 쉘 스크립트는 일반적인 텍스트 파일이다. 따라서 텍스트 편집기가 필요하다. 좋은 텍스트 편집기는 구문 강조 기능이 있어서 스크립트 요소들을 색상별로 표시해준다. 구문 강조 기능은 흔히 발생하는 오류들을 눈에 띄게 해준다. 스크립트를 작성할 때 [[chapter12 VI 맛보기|vim]], gedit, kate 등 다양한 편집기들을 사용할 수 있다.
2. **스크립트를 실행파일로 설정하기.** 시스템은 여러 이유들로 예전 텍스트 파일들을 프로그램으로 처리하지 않는다. 따라서 스크립트 파일에 실행 권한을 주어야 한다.
3. **쉘이 접근할 수 있는 장소에 저장하기.** 쉘은 경로명이 명시되지 않아도 실행 가능한 파일들이 존재하는 특정 디렉토리를 자동으로 검색한다. 우리는 최대한의 편의를 위해 이 디렉토리에 작업한 스크립트를 저장할 것이다.


### 스크립트 파일 포맷

프로그래밍의 전통을 지키는 의미에서 "hello world" 프로그램을 작성하여 아주 단순한 스크립트를 실행해볼 것이다. 이제 편집기를 띄워서 다음의 스크립트를 입력해보자.


```shell
┌──(kali㉿kali)-[~]
└─$ cat Hello world   
# !/bin/bash

# This is our first script

echo 'Hello World!' # This is comment too
cat: world: No such file or directory
                                          
```

스크립트의 마지막 줄은 꽤 익숙하다. echo 명령어에 문자열 인자로 이루어진 커맨드라인이다. 두 번째 줄도 역시 친숙하다. 우리가 실습하면서 편집하기도 했던 여러 환경설정 파일에서 보던 주석처럼 보인다. 쉘 스크립트에서는 주석이 다음과 같이 명령어 끝에 뒤따라올 수 있다.

```shell
echo 'Hello World!' # This is comment too
```

`#` 기호 다음에 나오는 모든 내용은 무시된다.  이 명령은 커맨드라인에서도 동일하게 적용된다.
```shell
┌──(kali㉿kali)-[~]
└─$ echo 'Hello World!' # This is a comment too
Hello World!
              
```

커맨드라인에서는 주석이 많이 사용되진 않지만 스크립트에서는 동일하게 인식된다.

하지만 이 스크립트의 첫 번째 줄은 살짝 의심스러운 부분이 있다. `#` 기호로 시작하기 때문에 당연히 주석인 것처럼 보인다. 그러나 단순히 주석이라고 하기엔 또 다른 목적이 있어보인다. `#!` 이 두개의 문자열은 사실 **shebang**이라고 하는 특별한 조합이다. ==shebang은 뒤따라오는 스크립트를 실행하기 위한 인터프리터의 이름을 시스템에 알려준다.== ==모든 쉘 스크립트의 첫 줄에는 shebang이 반드시 포함되어야 한다.==

자, 이 스크립트를 hello_world라는 파일로 저장해보자.


### 실행 퍼미션

다음으로 해야 할 일은 스크립트에 실행 권한을 설정하는 것이다. 이 작업은 [[chmod]] 명령어로 손쉽게 할 수 있다.

```shell
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ ls -l hello_world     
-rw-rw-r-- 1 kali kali 84 Oct  5 21:42 hello_world
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ chmod 755 hello_world
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ ls -l hello_world
-rwxr-xr-x 1 kali kali 84 Oct  5 21:42 hello_world

```

스크립트에 실행 퍼미션을 설정하는 일반적인 방법은 두 가지가 있다. 퍼미션을 755로 설정하면 모든 사용자에게 실행 권한이 주어지고, 700은 파일 소유자만 실행 가능하다. 여기서 주의할 점은 실행을 위해 항상 읽기 권한이 설정되어야 한다는 것이다.


### 스크립트 파일 저장 위치

퍼미션을 설정하고 나면 스크립트를 실행할 수 있다.

```shell
┌──(kali㉿kali)-[~]
└─$ ./hello_world
Hello World!

```

스크립트를 실행하기 위해 스크립트명 앞에 정확한 경로명을 입력해줘야 한다. 그렇지 않으면 다음과 같은 메시지를 보게 된다.

```shell
┌──(kali㉿kali)-[~]
└─$ hello_world
hello_world: command not found
                                 
```

왜 이럴까? 스크립트에 문제가 있는걸까? 따지고 보면 스크립트는 아무 잘못이 없다. 그 위치에 문제가 있는 것이다. [[chapter11 환경|11장]] 내용을 다시 떠올려보면, 우리는 PATH 환경 변수와 시스템이 실행 프로그램을 검색하는 데 있어 어떤 역할을 하는지 알아보았다. ==다시 요약하자면 경로명이 지정되어 있지 않다면, 시스템은 실행 프로그램을 찾을 때마다 디렉토리 목록을 검색한다.== 이것은 우리가 [[ls]] 명령어를 입력할 대, 시스템이 어떻게 `/bin/ls` 디렉토리를 실행하는지를 말해준다. `/bin` 디렉토리는 시스템이 자동 검색하는 디렉토리 중 하나다. 디렉토리 목록은 PATH라고 하는 환경 변수에 규정되어 있다. PATH 환경 변수에는 검색될 디렉토리들이 콜론 기호로 되어 있다. PATH 내용을 확인해보도록 하자.

```shell
┌──(kali㉿kali)-[~]
└─$ echo $PATH                                    
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/games:/usr/games:/home/kali/go/bin
    
```

자, 디렉토리 목록이 보인다. 우리가 작성한 스크립트가 나열된 디렉토리들 가운데 하나에라도 저장되어 있다면(스크립트 실행 시 경로명을 지정해야하는) 문제는 해결될 것이다. 주의할 점은 디렉토리 목록의 첫 번째 디렉토리 `/home/me/bin`이다. ==대부분의 리눅스 배포판은 bin 디렉토리를 사용자의 홈 디렉토리에 포함하도록 PATH 변수를 설정해두는데 그것은 사용자의 프로그램을 실행할 수 있도록 하기 위해서다.== 따라서 우리는 bin 디렉토리를 생성하고, 그 디렉토리에 스크립트 파일을 저장하고 다른 프로그램들처럼 실행하면 된다.

```shell
┌──(kali㉿kali)-[~]
└─$ mkdir bin           
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ mv hello_world bin  

┌──(kali㉿kali)-[~/bin]
└─$ ./hello_world
Hello World!
                  
```

만약 PATH 변수에 bin 디렉토리가 포함되어 있지 않다면 .bashrc 파일에 다음의 내용을 입력하여 bin 디렉토리를 추가할 수 있다.
```shell
export PATH=~/bin:"$PATH"

```

이렇게 설정하고 나면 새로운 터미널 세션이 시작될 때마다 이 설정이 적용될 것이다. 현재 세션에 이 내용을 바로 적용하고 싶다면 .bashrc 파일을 쉘이 다시 읽어 들여야 한다. 다음과 같이 소스를 읽어 들이자("sourcing").

>[!error] 주의할 점
> .bashrs를 사용하기 전에 수시로 시스템을 업그레이드 하는 것이 좋다.
> ``` shell
> sudo apt update && sudo apt upgrade
> ```
> 그리고 bash 자체의 업데이트 bash-completion도 업데이트 하는 것이 좋다.
> ``` shell
> sudo apt install bash-completion`
>```
> 

>[!error] 주의할 점
>나는 zsh를 사용하고 있었기 때문에 bashrc 업데이트가 제대로 되지 않는다. 그래서 이를 어떻게 해결하는지 적어 놓겠다.
>1. 먼저 bash를 시작한다.
>``` shell
>┌──(kali㉿kali)-[~]
└─$ exec bash
>```
>2. 그 다음 bashrc 내용을 다시 초기화한다.
>``` shell
>┌──(kali㉿kali)-[~]
└─$ source ~/.bashrc
>```
>
>
>3.그 후에 zsh를 시작한다.
>``` shell
>┌──(kali㉿kali)-[~]
└─$ exec zsh
>```
>



```shell
┌──(kali㉿kali)-[~]
└─$ . ~/.bashrc

```

마침표 (`,`) 명령어는 쉘에 내장된 source 명령어와 동일하다. 이는 쉘 명령들이 정의된 특정 파일을 읽어서 마치 키보드에 입력된 명령어처럼 인식하도록 한다.

>[!tip] 저자주
>우분투 시스템은 사용자의 .bashrc 파일이 실행되면 ~/bin 디렉토리를 (디렉토리가 생성되어 있을 경우) PATH 변수에 자동으로 추가한다. 따라서 우분투에서는 ~/bin 디렉토리를 생성하고 로그아웃하고 다시 로그인하기만 하면 된다.



### 스크립트를 저장하기 좋은 장소

`~/bin` 디렉토리는 개인적인 용도로 사용하려는 스크립트를 저장하기에 적합한 장소다. 시스템상의 모든 사용자가 접근 가능한 스크립트를 작성하는 경우에는 의례 저장하는 위치는 `/usr/local/bin` 디렉토리이다. 시스템 관리자용 스크립트는 `/usr/bin/sbin` 디렉토리에 저장한다. 대부분의 경우, 스크립트든 컴파일된 프로그램이든 시스템에서 사용되는 소프트웨어는 /bin 이나 /usr/bin 디렉토리가 아닌 /usr/local 디렉토리에 반드시 저장되어야 한다. 이러한 디렉토리들은 리눅스 파일시스템 계층 표준에 의해 지정되는 것으로 리눅스 배포자가 제공하고 관리하는 파일만 저장하도록 되어 있다.

---
# 기타 포맷 방법

중요한 스크립트를 작성할 때 주요 목표 중 하나가 바로 **유지보수**의 용이성이다. 즉 스크립트 원작자나 다른 사람들이 스크립트를 수정할 때 그 수정사항이 쉽게 반영되도록 하는 것이다. 쉽고 이해하기 쉬운 스크립트를 작성하는 것이야말로 스크립트 관리를 보다 편하게 해 주는 방법이다.



### 확장 옵션명(long 옵션명)

지금까지 공부한 명령어들은 대부분 **축약형**과 **확장 옵션명**이 있다. [[ls]] 명령어로 예를 들어 확인해보도록 하자.

```shell
┌──(kali㉿kali)-[~]
└─$ ls -ad           
.

```

그리고,

```shell
┌──(kali㉿kali)-[~]
└─$ ls --all --directory
.
```

이 두 명령어는 동일하다. 타이핑을 적게 하기 위해서는 축약형 옵션을 쓰면 되지만 스크립트 작성시에는 되도록 확장형을 사용하는 것이 스크립트의 가독성을 높인다.


### 들여쓰기 및 문장 연결


긴 명령어들을 사용하다 보면 여러 줄에 걸쳐 구분하여 입력하게 되고 이는 가독성을 확실히 높이게 된다. 우리는 17장에서 [[stat|find]] 명령어를 사용한 상당히 긴 예제를 보았다.

```shell
\( -type f -not -perm 0600 -exec chmod 0600 '{}' ';' \) -or \( -type d -not -perm 0700 -exec chmod 0700 '{}' ';' \)

```

이 예제는 한 번에 이해하기 다소 어려운 부분이 있다. 스크립트에서 다음과 같이 작성하면 더 이해하기 쉬울 것이다.

```shell
find playground \
	\(\
			-type -f \
			-not -perm 0600 \
			-exec chmod 0600 '{}' ';' \
	\) \
	-or \
	\( \
			-type d \
			-not -perm 0700 \
			
			-exec chmod 0700 '{}' ';'\
	\)
			
```

문장 연결(백슬래시-라인피드 문자열)과 들여쓰기를 통해 복잡한 명령어 구조가 더 분명하게 전달될 수 있다. 이러한 기법은 입력과 편집이 굉장히 불편해서 잘 사용되진 않지만 커맨드라인에서도 통하는 방법이긴 하다. 스크립트와 커맨드라인의 차이점 중 하나는 스크립트에서드 탭 기호가 들여쓰기에 사용되는 반면, 커맨드라인에서는 명령어 자동완성 시에 사용된다는 점이다.


>[!info] 스크립트 작성을 위한 VIM 설정
>[[chapter12 VI 맛보기|vim]] 텍스트 편집기는 굉장히 다양한 설정이 가능하다. 그 중에서도 스크립트 작성을 용이하게 해주는 몇 가지 옵션을 살펴보도록 하자.
>**:syntax on** 옵션은 구문 강조 기능이다. 이 옵션을 선택하면 스크립트의 쉘 구문마다 다른 색상으로 표시해주기 때문에 프로그래밍 오류를 쉽게 찾을 수 있다. 더불어 보기에도 좋다. 이 기능을 사용하기 위해서는 vim 편집기의 풀 버전이 설치되어 있어야 한다. 또한, 작성중인 파일이 쉘 스크립트 파일이라는 것을 알리기 위해 shebang이 반드시 존재해야 한다. 만약 `:syntax on` 옵션 사용에 문제가 있으면 `:set syntax=sh` 옵션을 사용해보길 바란다.
>
> **:set hlsearch** 옵션은 검색 결과를 강조하여 표시해준다. [[echo]]라는 단어를 검색할 때 사용하면 검색된 단어가 모두 하이라이트 된다.
> 
> **set tabstop=4** 옵션은 탭 간격을 지정한다. 기본값은 8로 되어 있고 이 값을 4(주로 4를 사용)로 지정하면 긴 내용이더라도 화면에 더 적합하게 표시된다.
> 
> **:set autoindent** 자동 들여쓰기 기능으로 vim 편집기에서 직전에 입력한 줄과 같은 간격으로 새 줄 들여쓰기가 된다. 이 기능으로 많은 프로그래밍 작업 시에 입력 속도를 높일 수 있다. 이 기능을 사용하지 않으면 `ctrl-D`키를 입력하면 된다.
> 
> 이 옵션들을 영구적으로 적용하려면 ~/.vimrc 파일에 이 옵션을 추가하면 된다( 콜론 기호는 빼고 입력).


---
# 마무리 노트

우리는 스크립트에 관한 첫 번째 장에서 어떻게 스크립트가 작성되고 시스템에서 쉽게 실해되도록 설정하는 방법에 대해서 살펴보았다. 더불어 스크립트의 가독성을 높이기 위한(결국 유지보수가 용이하도록 하는) 방법에 대해서도 알아보았다. 이후에는 훌륭한 스크립트를 작성하기 위한 핵심 내용으로 유지보수의 용이성을 높이는 방법들이 계속해서 소개될 것이다.





