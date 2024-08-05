

bash에는 각 쉘 빌트인마다 내장된 도움말 기능이 있다. 이 도움말을 확인하려면 다음과 같이 help를 쉘 내장 명령어 이름 앞에 입력하면 된다.

``` shell
kgm0917@DESKTOP-DT2VQLB:~$ help cd

cd: cd [-L|[-P [-e]] [-@]] [dir]

Change the shell working directory.

Change the current directory to DIR. The default DIR is the value of the

HOME shell variable.

The variable CDPATH defines the search path for the directory containing

DIR. Alternative directory names in CDPATH are separated by a colon (:).

A null directory name is the same as the current directory. If DIR begins

with a slash (/), then CDPATH is not used.

If the directory is not found, and the shell option `cdable_vars' is set,

the word is assumed to be a variable name. If that variable has a value,

its value is used for DIR.
```


저자주 : [ ] (대괄호) 기호가 명령어 문장에서 나타날 때, 이 괄호 안의 내용은 옵션이라는 것을 뜻하고 | (수직 선) 기호는 둘 중에 하나의 항목만을 사용한다는 것이다. cd 명령어 사용 예를 앞에서 확인해보자.

`cd [-L|-P] [dir].`


이 표기가 의미하는 것은 cd 명령어를 다음에 부가적으로 –L 또는 –P가 올 수 있고 또한 dir 인자가 옵션으로 따라 올 수 있다는 것이다.

cd 명령어의 도움말은 간결하고 정확하나 사용 지침서 수준은 아니다. 또한 보이는 것은 아직 우리가 다르지 않은 것도 있다.



- --help -사용법 표시

많은 실행 프로그램이 명령어 문법과 옵션에 대한 설명을 보여주는 —help 옵션을 지원한다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ mkdir --help

Usage: mkdir [OPTION]... DIRECTORY...

Create the DIRECTORY(ies), if they do not already exist.

Mandatory arguments to long options are mandatory for short options too.

-m, --mode=MODE set file mode (as in chmod), not a=rwx - umask

-p, --parents no error if existing, make parent directories as needed

-v, --verbose print a message for each created directory

-Z set SELinux security context of each created directory

to the default type

--context[=CTX] like -Z, or if CTX is specified then set the SELinux

or SMACK security context to CTX

--help display this help and exit

--version output version information and exit
```


--help 옵션을 지원하지 않는 것도 있지만 시도해 보는 것이 좋다. 같은 명령어 사용법을 알려주는 오류 메시지를 표시하기도 한다.


