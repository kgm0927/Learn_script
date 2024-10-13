
### 우리가 마든 프로그램에서 한 가지 놓치고 있는 기능은 커맨드라인 옵션과 인자를 허용하고 처리하는 능력이다. 이 장에서는 커맨드라인의 내용에 접근하는 쉘 기능을 알아볼 것이다.

---
# 커맨드라인 항목 접근


쉘 **위치 매개변수**라는 변수의 집합을 제거한다. 그것은 커맨드라인 명령의 개별 요소들을 가지고 있으며 변수들은 0부터 9까지 이름 붙인다. 다음과 같은 방식으로 나타낼 수 있다.
```shell
#!/bin/bash

# posit-param: script to view command line parameter

echo "
\$0 = $0
\$1 = $1
\$2 = $2
\$3 = $3
\$4 = $4
\$5 = $5
\$6 = $6
\$7 = $7
\$8 = $8
\$9 = $9
"

```

이는 변수 `$0`부터 `$9`까지 값을 표시하는 매우 간단한 스크립트다. 커맨드라인 인자 없이 실행하면 결과는 다음과 같다.

```shell
┌──(kali㉿kali)-[~]
└─$ ./posit-param    

$0 = ./posit-param
$1 = 
$2 = 
$3 = 
$4 = 
$5 = 
$6 = 
$7 = 
$8 = 
$9 = 

```

인자가 없는 경우조차도 `$0`은 항상 커맨드라인의 첫 번째 항목을 가지고 있다. 그것은 바로 실행되고 있는 프로그램의 경로명이다. 인자를 입력하여 실행하면 다음 결과를 볼 수 있다.

```shell
┌──(kali㉿kali)-[~]
└─$ ./posit-param a b c d

$0 = ./posit-param
$1 = a
$2 = b
$3 = c
$4 = d
$5 = 
$6 = 
$7 = 
$8 = 
$9 = 


```

>[!tip] 저자주
>사실 매개변수 확장을 사용하면 9개 이상의 매개변수에 접근할 수 있다. 9보다 큰 수를 지정하기 위해서는 중괄호를 사용하면 된다. 예를 들어 ${10}, ${55}, ${211} 등과 같이 말이다.
>


### 인자 수 확인

또한 쉘은 커맨드라인의 인자 수를 넘겨주는 `$#`을 제공한다.

```shell
#!/bin/bash

# posit-param: script to view command line parameter

echo "
Number of arguments: $#
\$0 = $0
\$1 = $1
\$2 = $2
\$3 = $3
\$4 = $4
\$5 = $5
\$6 = $6
\$7 = $7
\$8 = $8
\$9 = $9
"
           
```

결과는 이와 같다.

```shell
Number of arguments: 4
$0 = ./posit-param
$1 = a
$2 = b
$3 = c
$4 = d
$5 = 
$6 = 
$7 = 
$8 = 
$9 = 


```

### shift- 다수의 인자에 접근

- [[shift]]


---
# 위치 매개변수 전체 제어

때때로 위치 매개변수 전부를 그룹으로 관리하면 도움이 된다. 예를 들어, 우리가 다른 프로그램을 감싸는 **래퍼**(wrapper)를 작성하기를 원한다면, 이는 그 프로그램의 실행을 간소화하는 스크립트가 쉘 함수를 만든다는 것을 의미한다. 래퍼는 커맨드라인 옵션 목록을 공급하여 나서 인자 목록을 하위 레벨 프로그램에 전달한다.

쉘은 이러한 목적으로 두 가지 특수한 매개변수를 제공한다. 이들은 둘 다 위치 매개변수의 전체 목록으로 확장되지만 미묘한 방식 차이가 있다. [표 32-1]은 이들 매개변수를 설명한다.


- [표 32-1] 특수 매개변수 `*`와 `@`


| 매개변수 | 설명                                                                                                                       |
| ---- | ------------------------------------------------------------------------------------------------------------------------ |
| `$*` | 항목 1부터 시작하여 위치 매개변수 목록으로 확장된다. 이것을 쌍 따옴표로 둘러싸면, 쌍 따옴표 내의 문자열 모두가 위치 매개변수로 확장되고 각각 IFS 쉘 변수의 첫 번째 문자(기본값을 스페이스)에 의해 구분된다. |
| `$@` | 항목 1부터 시작하여 위치 매개변수 목록으로 확장된다. 이것을 쌍 따옴표로 둘러싸면, 각 위치 매개변수는 쌍 따옴표로 구분된 단어로 확장된다.                                          |

다음은 이 특수 매개변수들이 동작하는 모습을 보여주는 스크립트다.

```shell
#!/bin/bash

# posit-param3: script to demonstrate $* and $@

print_params(){

        echo "\$1 = $1"
        echo "\$2 = $2"
        echo "\$3 = $3"
        echo "\$4 = $4"


}

pass_params(){

        echo -e "\n" '$* :';   print_params $*
        echo -e "\n" '"$*" :'; print_params "$*"
        echo -e "\n" '$@ :';   print_params $@
        echo -e "\n" '"$@" :';  print_params "$@"
}

pass_params "word" "words with spaces"
                                            

```

이 난해한 프로그램은 word와 words with spaces라는 두 인자를 만들고 pass_params 함수에 전달한다. 결국 그 함수는 특수 매개변수 `$*`와 `$@`로 사용 가능한 4가지 방식으로 `print_params` 함수에 인자를 전달한다. 이 스크립트의 실행 결과는 방식에 따라 차이점을 보여준다.

```shell
┌──(kali㉿kali)-[~]
└─$ ./posit-param3  

 $* :
$1 = word
$2 = words
$3 = with
$4 = spaces

 "$*" :
$1 = word words with spaces
$2 = 
$3 = 
$4 = 

 $@ :
$1 = word
$2 = words
$3 = with
$4 = spaces

 "$@" :
$1 = word
$2 = words with spaces
$3 = 
$4 = 

```

`$*`와 `$@`는 word, words, with, spaces라는 모두 네 단어를 생성한다. `"$*"`는 word words with spaces라는 한 단어를 생성한다. `"$@"`는 word와 words with spaces 두 단어를 생성한다.

이는 우리가 의도한 것과 일치한다. 이것으로 얻을 수 있는 교훈은, 쉘이 위치 매개변수 목록을 얻을 수 잇는 4가지 방식을 제공함에도 불구하고 "$@"는 각각의 위치 매개변수 그대로를 유지하기 때문에 가장 많이 사용되고 유용하다.


---
# 완전한 응용 프로그램

우리는 오랜만에 sys_info_page 프로그램으로 다시 작업할 예정이다. 다음과 같이 프로그램에 여러 커맨드라인 옵션을 추가하려고 한다.

- **출력 파일.** 프로그램 출력을 저장할 파일명을 지정하기 위한 옵션을 추가할 것이다. 이 옵션은 *-f file* 나 *--file file*로 지정한다.
- **대화식 모드.** 옵션은 출력 파일명을 사용자에게 표시하고 그 파일의 존재 여부를 확인한다. 만약 존재한다면 사용자에게 해당 파일을 덮어쓰기 전에 물어본다. 이 옵션은 `-i` 또는 `--interactive`로 지정한다.
- **도움말.** `-h`나 `--help`를 사용하면 사용법이 표시된다.

다음은 커맨드라인 처리를 구현하기 위해 필요한 코드다.

```shell
#!/bin/bash

# Program to output a system information page

usage(){
        echo "$PROGNAME: usage: $PROGNAME [ -f file | -i]"
        return
}

#process command line options

interactive=
filename=

while [[ -n $1 ]]; do
                case $1 in
                        -f | --file)            shift
                                                filename=$1
                                                ;;
                        -i | --interactive)     interactive=1
                                                ;;
                        -h | --help)            usage
                                                exit
                                                ;;
                        *)                      usage >&2
                                                exit 1
                                                ;;
                esac
                shift
done
      

```

먼저, 우리는 help 옵션이 노출되거나 알 수 없는 옵션인 경우에 메시지를 표시하는 `usage`라는 쉘 함수를 추가한다.

그 다음, 처리 루프를 실행한다. 이 루프는 위치 매개변수 `$1`의 값이 빌 때까지 계속된다. 루프가 종료되기 위해서는 루프의 끝에서 [[shift]] 명령어로 위치 매개변수를 전진시킨다.

루프 내에서 [[case]] 문은 현재 위치 매개변수가 이 프로그램이 지원하는 옵션과 일치하는지 확인하기 위해 사용된다. 만약 지원하는 매개변수이면 그에 따라 동작한다. 지원하지 않는다면, 사용법을 표시하고 스크립트는 오류와 함께 종료된다.


**-f** 매개변수는 재미있는 방식으로 전개된다. 이 옵션이 입력되면 추가적으로 [[shift]]가 수행되어 위치 매개변수 `$1`에는 `-f` 옵션에 제공된 파일명이 전달된다.

다음은 대화식 모드를 구현하는 코드다.

```shell
#interactive mode


if [[ -n $interactive ]]; then
        while true; do
                read -p "Enter name of output file: " filename
                if [[ -e $filename ]]; then
                        read -p "'$filename' exists. Overwrite? [y/n/q] > "
                        case $REPLY in
                                Y|y)    break
                                        ;;
                                Q|q)    echo "Program terminated"
                                        exit
                                        ;;
                                *)      continue
                                        ;;
                        esac
                elif [[ -z $filename ]]; then
                        continue
                else
                        break
                fi
        done
fi

```

만약 interactive 변수가 비어있다면, 파일명 프롬프트와 파일 조작 코드가 존재하는 무한 루프가 시작된다. 원하는 출력 파일이 이미 존재한다면, 덮어쓰기를 위한 프롬프트를 표시하고 사용자가 다른 파일명을 선택하거나 프로그램을 종료하도록 한다. 사용자가 기존 파일을 덮어쓰기로 결정한다면 `break` 문이 실행되어 루프는 종료된다. 여기서 [[case]] 문은 사용자가 덮어쓰기나 종료를 선택하는 경우만 감지한다는 것을 명심해라. 다른 것을 선택하면 루프는 계속되고 사용자에게 다시 프롬프트를 표시한다.

출력 파일명 옵션을 구현하기 위해 먼저 기존 페이징 작성 코드를 쉘 함수로 변환해야 한다. 그 이유는 순식간에 업어져 버릴 수 있기 때문이다.

```shell
write_html_page (){
        cat <<- _EOF_
        <HTML>
                <HEAD>
                        <TITLE>$TITLE</TITLE>
                </HEAD> 
                <BODY>
                        <H1>$TITLE</H1>
                        <P>$TIME_STAMP</P>
                        $(report_uptime)
                        $(report_disk_space)
                        $(report_home_space)
                </BODY>
        </HTML>
        _EOF_
        return
}

# output html page
        
if [[ -n $filename ]]; then
        if touch $filename && [[ -f $filename ]]; then
                write_html_page > $filename
        else
                echo "$PROGNAME: Cannot write file '$filename'">&2
                exit 1            
        fi
else
        write_html_page
fi  

```

이 코드는 `-f` 옵션의 흐름을 제어한다. 파일의 존재를 확인하고 만약 있다면 그 파일이 정말로 쓰기 가능한지 확인하기 위해 테스트한다. 이를 위해 [[touch]] 명령을 실행하고 이어서 해당 파일이 일반 파일인지 확인한다. 이 두 테스트는 유효하지 않는 경로명이 입력되는 상황(touch는 실패한다)을 처리하고, 만약 파일이 존재한다면 일반 파일로 인지한다.

위에서 볼 수 있는 것처럼, write_html_page 함수는 페이지를 실제로 생성하기 위해 호출된다. 출력 결과는 파일명 변수 값이 없다면 표준 출력으로 직접 보내지거나 지정된 파일로 보내진다.


---
# 마무리 노트

위치 매개변수의 추가로 이제는 꽤 기능적인 스크립트를 작성할 수 있다. 예를 들어 반복 작업에서 위치 매개변수는 사용자의 `.bashrc` 파일에 놓을 수 있는 아주 유용한 쉘 함수를 작성 가능하게 한다.

우리 sys_info_page 프로그램은 점점 복잡하고 정교해져 간다. 그 다음은 그 완전한 목록이다. 최근 수정된 사항은 하이라이트로 처리하였다.


``` bash

#!/bin/bash

# sys_info_page: program to output a system information page


PROGNAME=$(basename $0)
TITLE="System Information Report For $HOSTNAME"
CURRENT_TIME=$(date + "%x %r %Z")
TIME_STAMP="Generated $CURRENT_TIME, by $USER"

report_uptime(){
        cat <<- _EOF_
                <H2>System Uptime</H2>
                <PRE>$(uptime)</PRE>
                _EOF_
        return

}

report_disk_space(){
        cat <<- _EOF_
                <H2>Disk Space Utilization</H2>
                <PRE>$(df -h)</PRE>
                _EOF_
        return
}

report_home_space(){
        if [[ $(id -u) -eq 0 ]]; then
                cat <<- _EOF_
                        <H2>Home Space Utilization (All Users)</H2>
                        <PRE>$(do -sh /home/*)</PRE>
                        _EOF_
        else
                cat <<- _EOF_
                        <H2>Home Space Uitlization ($USER)</H2>
                        <PRE>$(du -sh $HOME)</PRE>
                        _EOF_
        fi
        return
}

usage(){
        echo "$PROGNAME: usage: $PROGNAME [ -f file | -i]"
        return
}

write_html_page(){

        cat <<- _EOF_
        <HMTL>
                <EHAD>
                        <TITLE>$TITLE</TITLE>
                </HEAD>
                <BODY>
                        <H1>$TITLE</H1>
                        <P>$TIME_STAMP</P>
                        $(report_uptime)
                        $(report_disk_space)
                        $(report_home_space)
                </BODY>
        </HTML>
        _EOF_
        return 
}

# process command line options


interactive=
filename=

while [[ -n $1 ]]; do
        case $1 in
                -f | --file)            shift
                                        filename=$1
                                        ;;
                -i | --interactive)     interactive=1
                                        ;;
                -h | --help)            usage
                                        exit
                                        ;;
                *)                      usage >&2
                                        exit 1
                                        ;;
                esac
                shift
done


#interactive mode

if [[ -n $interactive ]]; then
        while true; do
                read -p "Enter name of output file: " filename
                if [[ -e $filename ]]; then
                        read -p "'$filename' exists. Overwrite? [y/n/q] > "
                        case $REPLY in
                                Y/y) break
                                        ;;
                                Q|q) echo "Program terminated."
                                        exit
                                        ;;
                                *)      continue
                                        ;;
                        esac
                fi
        done
fi

# output html page



if [[ -n $filename ]]; then
        if touch $filename && [[ -f $filename ]]; then
                write_html_page > $filename
        else
                echo "$PROGNAME: Cannot write file '$filename'" >&2
                exit 1
        fi
else
        write_html_page
fi
       
```

이제 스크립트는 꽤 쓸 만해졌다. 하지만 아직 끝난 건 아니다. 다음 장에서 마지막으로 개선사항을 추가할 것이다.