

---
# 환경에는 어떤 것들이 저장이 될까?

bash에서는 구분하기 힘들지만 쉘은 환경에 두 가지 기본적인 형식을 저장한다. 하나는 **환경변수**고, 다른 하나는 **쉘 변수**다. 쉘 변수는 bash에 의해 저장된 작은 데이터이고, 환경 변수는 기본적으로 그 밖의 모든 것이다. 쉘은 변수 뿐만 아니라, **별칭** 그리고 **쉘 함수**와 같은 프로그램 데이터도 저장한다.


- 환경 검증하기

환경에 저장된 것이 무엇인지 보려면 bash에 저장된 set 명령어나 printenv 프로그램을 사용하면 된다. set 명령어는 쉘 변수와 환경 변수 모두 보여주ㅗ며, printenv 명령어는 오직 환경 변수 만을 출력하다. 환경 변수의 내용이 상당히 길기 때문에 파이프라인을 활용해서 less 명령어를 사용하는 것이 좋다.

```
$ printenv | less

CLUTTER_IM_MODULE=fcitx

COLORFGBG=15;0

COLORTERM=truecolor

COMMAND_NOT_FOUND_INSTALL_PROMPT=1

DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus

DESKTOP_SESSION=lightdm-xsession

DISPLAY=:0

DOTNET_CLI_TELEMETRY_OPTOUT=1

GDMSESSION=lightdm-xsession

GTK_IM_MODULE=fcitx

GTK_MODULES=gail:atk-bridge

HOME=/home/kali

LANG=en_US.UTF-8

LANGUAGE=

LOGNAME=kali

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/games:/usr/games

POWERSHELL_TELEMETRY_OPTOUT=1

POWERSHELL_UPDATECHECK=Off

PWD=/home/kali

QT_ACCESSIBILITY=1

QT_AUTO_SCREEN_SCALE_FACTOR=0

QT_IM_MODULE=fcitx

QT_QPA_PLATFORMTHEME=qt5ct

SDL_IM_MODULE=fcitx

SESSION_MANAGER=local/kali:@/tmp/.ICE-unix/814,unix/kali:/tmp/.ICE-unix/814

SHELL=/usr/bin/zsh

SSH_AGENT_PID=934

SSH_AUTH_SOCK=/tmp/ssh-Wce4Gw7D9NS7/agent.814

TERM=xterm-256color

USER=kali

WINDOWID=0

XAUTHORITY=/home/kali/.Xauthority

XDG_CONFIG_DIRS=/etc/xdg

XDG_CURRENT_DESKTOP=XFCE

XDG_DATA_DIRS=/usr/share/xfce4:/usr/local/share/:/usr/share/:/usr/share

XDG_GREETER_DATA_DIR=/var/lib/light

... 그 외 ...
```

지금 보고 있는 것이 환경 변수의 목록과 그 값들이다. 예를 들어 **USER**라는 변수를 보면 me라는 값을 가지고 있다. **printenv** 명령어로 이러한 특수한 변수의 값을 나열할 수 있다.


``` shell
┌──(kali㉿kali)-[~]

└─$ printenv USER

kali
```


옵션이나 명령 인자 없이 set 명령어를 사용하면 쉘과 환경 변수, 둘 다 볼 수 있다. 그 밖에도 정의된 쉘 함수 인자가 있다면 그것 또한 표시된다.


``` shell
┌──(kali㉿kali)-[~]

└─$ set | less
```


printenv와는 달리 set 명령어는 친절하게도 결과를 알파벳순으로 정렬해준다. 다음과 같이 [[echo]] 명령어로 변수 내용을 보는 것도 가능하다.

```shell

┌──(kali㉿kali)-[~]

└─$ echo $HOME
```



set이나 printenv 명령어가 표시하지 않는 하나의 환경 요소는 바로 별칭이다. 이를 보려면 alias 명령어를 인자 없이 입력하면 된다.


[[alias]]