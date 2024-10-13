
---
# 명령어 타입 표시

명령어의 네 가지 정의 중 어떠한 종류의 명령어인지 알아두는 것은 종종 유용하다. 리눅스에서는 이를 확인하기 위해 여러 방법을 지원하고 있다.


- type –명령어 표시

type 명령어는 쉘에 내장된 형식으로 명령어 이름을 입력하면 쉘이 실행하게 될 명령어가 어떤 타입인지 보여준다. 다음과 같이 입력하면 된다.


type *command*


command 인자는 확인하고픈 명령어의 이름을 입력하는 부분이다. 몇 가지 예제를 보자.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ type type

type is a shell builtin

kgm0917@DESKTOP-DT2VQLB:~$ type ls

ls is aliased to `ls --color=auto'

kgm0917@DESKTOP-DT2VQLB:~$ type cp

cp is /usr/bin/cp
```


우리는 앞의 결과에서 세 가지 다른 명령어들을 볼 수 있다. ls 명령어(위의 결과는 데미안에서 가지고 온 것이다. 페도라에서는 `—color=tty` 라는 결과가 나온다.)실제 명령어에 `—color=auto`라는 옵션이 붙어있는 별칭이라는 것을 알 수 있다. 이제 어떻게 ls 명령어의 출력결과에 색상이 표시되는지 알게 됬다.