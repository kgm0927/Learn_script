
이전 챕터: [[chapter10 프로세스]]


앞에서 살펴본 바와 같이 쉘은 쉘 세션이 진행되는 동안 모든 정보를 관리하는 환경을 유지한다. 쉘 환경에 저장된 데이터는 설정 프로그램에 의해 사용된다. 대부분의 프로그램은 **환경설정 파일**을 사용하지만, 일부 프로그램은 동작하기 위해 환경에 설정된 값을 찾아보기도 한다.

이 장에서는 다음 명령어들을 사용하여 작업게 될 것이다.

- [[printenv]]- 환경 일부 또는 전체 출력하기

- [[set]]- 쉘 옵션 설정하기

- [[export]] – 다음 실행 프로그램에 환경 적용하기

- alias – 명령어 별칭 생성하기


---
# 환경 편집

이제 시작 파일이 어디에 있는지 또 어떤 내용이 들어있는지 알기 때문에, 우리만의 환경을 설정할 수 있다.

- 어떤 파일을 수정해야 할까?

일반적인 규칙에 따라, 사용자의 PATH에 디렉토리를 추가하거나 부가적인 환경 변수를 정의하기 위해서는 .bash_profile 파일(또는 파일 배포판에 따라 그에 준하는 파일, 우분투에서는 .profile 파일) 내용을 수정해야 한다. 그 밖의 다른 것들은 .bashrc 파일에서 변경한다.

자신이 시스템 관리자가 아니고 시스템에 있는 모든 사용자의 기본값을 수정할 필요가 없다면 홈 디렉토리에 있는 파일만을 편집하도록 한다.


- 텍스트 편집기

시작 파일뿐만 아니라 시스템에 있는 다른 환경설정 파일을 편집하기 위해서는 텍스트 편집기라고 하는 프로그램을 사용한다. 텍스트 편집기는 커서 이동으로 화면상의 단어를 편집할 수 있다는 점에서는 워드 프로세서와 비슷하다. 다른 점은 텍스트만을 지원하다는 것과 편집 프로그램을 위한 기능들이 포함되어 있다는 것이다.

리눅스에는 수많은 텍스트 편집기들이 있다. 왜 그렇게 많을까? (필자 생각은) 프로그래머들은 광범위하게 편집기를 사용하기 때문에 그들이 생각했을 때 편집기가 갖추어야 하는 기능을 편집기에 표현해두려고 한다.

텍스트 편집기에는 두 가지 카테고리가 있는데 하나는 그래픽 환경, 다른 하나는 텍스트 기반 환경이다. GNOME와 KDE 모두 인기 있는 그래픽 환경의 편집기를 가지고 있다. GNOME 기반에는 gedit라고 하는 편집기가 있는데 GNOME 메뉴에서는 보통 Text Editor로 불린다. KDE에는 세 가지 편집기가 있는데 (복잡성이 작은 순서대로) kedit, kwrite, kate 이다.


또한 텍스트 기반의 편집기도 많이 있으면 이들 중 가장 유명한 것은 nano, vi, emacs이다. nano 편집기는 간단하면서 사용하기 쉬운 편집기로 PINE 이메일 프로그램에서 제공하는 pico 편집기의 확장판으로 설계되었다. vi 편집기(대부분의 리눅스 시스템에서 Vi IMproved의 준말인 vim 이라는 프로그램 이름으로 나타냄)는 유닉스형 시스템을 위한 전통적인 편집기다. 이것은 12장의 주제이기도 하다. 


- 텍스트 편집기 사용하기

모든 텍스트 편집기는 커맨드라인에서 편집하려는 파일명 앞에 편집기 이름을 입력하면 해당 파일을 불러올 수 있다. 만약 파일이 존재햐지 않으면 편집기는 사용자가 새 파일을 만든다고 생각할 것이다.

```
┌──(kali㉿kali)-[~]
└─$ gedit some_file
Command 'gedit' not found, but can be installed with:
sudo apt install gedit
```

이 명령어는 gedit 텍스트 편집기를 실행하고 some_file 이라는 파일이 있다면 그 파일을 불러올 것이다.


그래픽 환경의 텍스트 편집기들은 사용이 직관적이기 때문에 여기서는 다루지 않을 것이다. 대신 첫 번째 텍스트 편집기인 nano에 집중할 것이다. nano 편집기를 실행하고 .bashrc파일을 편집해보겠다.

일단 작업에 앞서, 안전한 사용을 위해 선행 작업을 미리 해 두도록 하겟다. 중요한 설정 파일을 편집할 때마다 항상 백업 파일을 만들어 두는 것이 좋다. 잘못된 편집으로 파일이 엉망이 될 경우를 대비해두는 것이다. .bashrc 파일을 백업해 둘 려면 다음과 같이 입력한다.


```shell
┌──(kali㉿kali)-[~]
└─$ cp .bashrc .bashrc.bak
                              
```


이 파일을 굳이 백업 파일이라고 하지 않아도 상관없다. 그저 알아볼 수 있는 이름을 사용하면 된다. **.bak, .sav, .old, .orig**와 같은 확장자는 모두 백업 파일을 표시하는 인기 있는 방법이다. 한 가지더, **cp 명령어는 기존 파일을 아무 경고 없이 덮어쓴다는 사실이다.**


편집기를 시작한다.

nano 편집기를 실행하면, 다음과 같은 화면을 볼 수 있다.

``` bash
 ~/.bashrc: executed by bash(1) for non-login shells.
# see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
# for examples

# If not running interactively, don't do anything
case $- in
    *i*) ;;
      *) return;;
esac

# don't put duplicate lines or lines starting with space in the history.
# See bash(1) for more options
HISTCONTROL=ignoreboth

# append to the history file, don't overwrite it
shopt -s histappend

# for setting history length see HISTSIZE and HISTFILESIZE in bash(1)
HISTSIZE=1000
HISTFILESIZE=2000

# check the window size after each command and, if necessary,
# update the values of LINES and COLUMNS.
shopt -s checkwinsize

# If set, the pattern "**" used in a pathname expansion context will
# match all files and zero or more directories and subdirectories.
#shopt -s globstar

# make less more friendly for non-text input files, see lesspipe(1)
#[ -x /usr/bin/lesspipe ] && eval "$(SHELL=/bin/sh lesspipe)"

# set variable identifying the chroot you work in (used in the prompt below)
if [ -z "${debian_chroot:-}" ] && [ -r /etc/debian_chroot ]; then
    debian_chroot=$(cat /etc/debian_chroot)
fi

# set a fancy prompt (non-color, unless we know we "want" color)
case "$TERM" in
    xterm-color|*-256color) color_prompt=yes;;
esac

# uncomment for a colored prompt, if the terminal has the capability; turned
# off by default to not distract the user: the focus in a terminal window
# should be on the output of commands, not on the prompt
force_color_prompt=yes

if [ -n "$force_color_prompt" ]; then

```


화면의 상단에는 헤더, 중앙에는 편집할 텍스트, 아래에는 명령어 메뉴가 있다. nano 편집기는 이메일 클라이언트와 함께 제공된 텍스트 편집기의 확장판으로 설계된 것이어서 편집 기능은 다소 부족한 편이다.

모든 편집기에서 배워야 할 가장 첫 번째 명령은 바로 그 프로그램을 종료하는 방법이다. nano의 경우 ctrl-X키를 입력하면 된다. 화면 하단에 있는 메뉴에도 종료가 있다. ^X는 ctrl-X를 의미하는데 많은 프로그램에서 사용되는 제어 문자 표기법이다.

우리가 알아야 하는 두 번째 명령은 저장하는 방법이다. nano에서는 ctrl-O키를 사용하면 된다. 이 두 가지를 잘 이해했다면 편집할 준비가 완료된 것이다. 아래쪽 화살표의 키와 page-down키를 사용해서 커서를 파일 끝으로 이동하자. 그런 다음 .bashrc 파일에 다음 내용을 추가해보자.


``` shell
# Change umask to make directory sharing easier
umask 0002

# Ignore duplicates in command history and increase
# history size to 1000 lines
export HISTCONTROL=ignoredups
export HISTSIZE=1000

# Add some helpful aliases
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'


```


[표 11-4] .bashrc에 추가한 내용


| 내용                               | 의미                                                  |
| -------------------------------- | --------------------------------------------------- |
| umask 0002                       | 9장에서 설명한 공유 디렉토리에서 발생하는 문제를 해결하기 위해 umask 설정        |
| export HISTCONTROL=ignoredups    | 쉘 히스토리에 똑같은 명령어가 기록되어 있다면 그 명령어의 기록은 무시하도록 한다.      |
| export HISTSIZE=1000             | 명령어 히스토리 크기를 최대 500개에서 1000개로 수정함.                  |
| alias l.='ls -d .* --color=auto' | l. 이라는 명령어를 새로 만들어서 마침표로 시작하는 모든 디렉토리 항목을 표시하도록 한다. |
| alias ll='ls -l --color=auto'    | ll이라는 명령어를 만들어서 디렉토리 목록을 자세히 보기 형식(long 포맷)으로 표시한다. |


위의 예시에는 주석을 달아놓았다.


여기까지 했으면 ctrl-O키를 입력해서 수정된 .bashrc 파일을 저장하고 ctrl-X로 nano를 종료한다.


- 변경사항 적용하기

.bashrc 파일에 편집한 내용들은 터미널 세션을 종료하고 다시 새로 실행할 때까지 적용되지 않는다. 왜냐하면 .bashrc 파일은 최초 세션이 시작될 때 참조되는 파일이기 때문이다. 하지만 bash에 강제로 이 파일을 잠조하로록 명령할 수 있다.

```
┌──(kali㉿kali)-[~]
└─$ source .bashrc        
Command 'shopt' not found, did you mean:
  command 'shout' from deb libshout-tools
Try: sudo apt install <deb name>
Command 'shopt' not found, did you mean:
  command 'shout' from deb libshout-tools
Try: sudo apt install <deb name>
Command 'shopt' not found, did you mean:
  command 'shout' from deb libshout-tools
Try: sudo apt install <deb name>
Command 'shopt' not found, did you mean:
  command 'shout' from deb libshout-tools
Try: sudo apt install <deb name>
complete: command not found
complete: command not found
complete: command not found
complete: command not found
complete: command not found
complete: command not found
complete: command not found
complete: command not found
complete: command not found
complete: command not found
/usr/share/bash-completion/bash_completion:1590: parse error near `|'
                                                                         
```

이 명령을 한 후에, 변경 사항이 적용됬는지 반드시 확인해보아야 한다. 새로 추가한 명령어 별칭 가운데 하나를 실행해보자.


``` shall
\[\e]0;\u@\h: \w\a\]\[\033[;32m\]┌──(\[\033[1;34m\]\u㉿\h\[\033[;32m\])-[\[\033[0;1m\]\w\[\033[;32m\]]\n\[\033[;32m\]└─\[\033[1;34m\]$\[\033[0m\] ll
total 36
drwxr-xr-x 3 kali kali 4096 Dec 26 21:07 Desktop
drwxr-xr-x 2 kali kali 4096 Dec 26 20:14 Documents
drwxr-xr-x 2 kali kali 4096 Dec 26 21:02 Downloads
-rw-rw-rw- 1 kali kali    0 Mar  6 23:00 foo.txt
drwxr-xr-x 2 kali kali 4096 Dec 26 20:14 Music
-rw------- 1 kali kali    1 Mar  7 01:14 nano.60969.save
drwxr-xr-x 2 kali kali 4096 Dec 26 20:14 Pictures
drwxr-xr-x 2 kali kali 4096 Dec 26 20:14 Public
drwxr-xr-x 2 kali kali 4096 Dec 26 20:14 Templates
drwxr-xr-x 2 kali kali 4096 Dec 26 20:14 Videos
                                                              
```


---
다음 챕터: [[chapter12]]