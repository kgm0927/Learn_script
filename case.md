
bash의 다중 선택 합성 명령어는 case라고 하며, 다음 문법을 따른다.

```shell
case word in
	[ pattern[|pattern]...) commands;;]...
esac
```


[[chapter28 키보드 입력 읽기|28장]]의 read-menu 프로그램을 살펴보면 사용자 선택에 따라 동작하는 로직을 볼 수 있다.

```shell
#!/bin/bash

# read-menu: a menu driven system information program

clear
echo "
Please Select:

1. Display System Information
2. Display Disk Space
3. Display Home Space Utilization
0. Quit
"

read -p "Enter selection [0-3] > "

if [[ $REPLY =~ ^[0-3]$ ]]; then
        if [[ $REPLY == 0 ]]; then
                echo "Program terminated"
                exit
        fi
        if [[ $REPLY == 1 ]]; then
                echo "Hostname: $HOSTNAME"
                uptime
                exit
        fi
        if [[ $REPLY == 2 ]]; then
                df -h
                exit
        fi
        if [[ $REPLY == 3 ]]; then
                if [[ $(id -u) -eq 0 ]]; then
                        echo "Home Space Utilization (All Users)"
                        du -sh /home/*
                else
                        echo "Home Space Utilization ($USER)"
                        du -sh $HOME
                fi
                exit
        fi
else
        echo "Invalid entry." >&2
        exit 1
fi
#!/bin/bash

# read-menu: a menu driven system information program

clear
echo "
Please Select:

1. Display System Information
2. Display Disk Space
3. Display Home Space Utilization
0. Quit
"

read -p "Enter selection [0-3] > "

if [[ $REPLY =~ ^[0-3]$ ]]; then
        if [[ $REPLY == 0 ]]; then
                echo "Program terminated"
                exit
        fi
        if [[ $REPLY == 1 ]]; then
                echo "Hostname: $HOSTNAME"
                uptime
                exit
        fi
        if [[ $REPLY == 2 ]]; then
                df -h
                exit
        fi
        if [[ $REPLY == 3 ]]; then
                if [[ $(id -u) -eq 0 ]]; then
                        echo "Home Space Utilization (All Users)"
                        du -sh /home/*
                else
                        echo "Home Space Utilization ($USER)"
                        du -sh $HOME
                fi
                exit
        fi
else
        echo "Invalid entry." >&2
        exit 1
fi

```
case를 사용하여 이 로직을 좀 더 간단하게 구성할 수 있다.

```shell
#!/bin/bash

# read-menu: a menu driven system information program

clear
echo "
Please Select:

1. Display System Information
2. Display Disk Space
3. Display Home Space Utilization
0. Quit
"

read -p "Enter selection [0-3] > "

if [[ $REPLY =~ ^[0-3]$ ]]; then
        if [[ $REPLY == 0 ]]; then
                echo "Program terminated"
                exit
        fi
        if [[ $REPLY == 1 ]]; then
                echo "Hostname: $HOSTNAME"
                uptime
                exit
        fi
        if [[ $REPLY == 2 ]]; then
                df -h
                exit
        fi
        if [[ $REPLY == 3 ]]; then
                if [[ $(id -u) -eq 0 ]]; then
                        echo "Home Space Utilization (All Users)"
                        du -sh /home/*
                else
                        echo "Home Space Utilization ($USER)"
                        du -sh $HOME
                fi
                exit
        fi
else
        echo "Invalid entry." >&2
        exit 1
fi

```

case 명령어는 **단어**의 값(이 예제에서는 REPLY 변수의 값)을 확인하고 그 값과 일치하는 **패턴**을 찾는다. 일치하는 패턴이 있으면 해당 패턴의 **명령들**을 실행한다. 일치하는 패턴을 찾은 후에는 더 이상 패턴을 찾지 않는다.


---
### 패턴

case에서 사용하는 패턴은 경로명 확장에서 사용되는 패턴과 동일하다. 이 패턴들은 `)` 문자로 끝난다. [표 31-1]은 유효한 패턴들을 보여준다.

- [표 31-1] case 패턴 예제


| 패턴               | 설명                                                                                              |
| ---------------- | ----------------------------------------------------------------------------------------------- |
| **a)**           | a와 일치하는 단어                                                                                      |
| **[[:alpha:]])** | 하나의 알파벳 문자와 일치하는 단어                                                                             |
| `???)`           | 정확히 세 글자로 이루어진 단어                                                                               |
| `*.txt)`         | `.txt` 문자열로 끝나는 단어                                                                              |
| `*)`             | 모든 단어. 이는 case 명령어의 마지막 패턴으로 사용된다. 앞선 패턴에서 일치하는 게 없는 단어를 처리하기 위해 사용된다. 즉 유효하지 않는 값을 처리하기 위해서이다. |

다음은 패턴 예제다.

```shell
#!/bin/bash

read -p "enter word > "

case $REPLY in
	[[:alpha:]]) echo "is a single alphabetic character.";;
	[ABC][0-9]) echo "is A, B, or C followed by a digit." ;;
	???)        echo "is three character long." ;;
	*.txt)      echo "is a word ending in '.txt'" ;;
	*)          echo "is something else."         ;;
```


### 패턴 결합

수직바를 구분자로 여러 패턴들을 결합하여 사용하는 것도 편리하다. 이것은 "OR" 조건 패턴을 생성한다.

```shell
#!/bin/bash

# read-menu: a menu driven system information program

clear
echo "
Please Select:

1. Display System Information
2. Display Disk Space
3. Display Home Space Utilization
0. Quit
"

read -p "Enter selection [0-3] > "

if [[ $REPLY =~ ^[0-3]$ ]]; then
        if [[ $REPLY == 0 ]]; then
                echo "Program terminated"
                exit
        fi
        if [[ $REPLY == 1 ]]; then
                echo "Hostname: $HOSTNAME"
                uptime
                exit
        fi
        if [[ $REPLY == 2 ]]; then
                df -h
                exit
        fi
        if [[ $REPLY == 3 ]]; then
                if [[ $(id -u) -eq 0 ]]; then
                        echo "Home Space Utilization (All Users)"
                        du -sh /home/*
                else
                        echo "Home Space Utilization ($USER)"
                        du -sh $HOME
                fi
                exit
        fi
else
        echo "Invalid entry." >&2
        exit 1
fi
           
```

우리는 메뉴 선택을 위한 숫자 대신 글자를 사용하기 위해 case-menu 프로그램을 수정한다. 주의할 점은, 새 패턴들은 대문자와 소문자 모두 허용한다는 것이다.

