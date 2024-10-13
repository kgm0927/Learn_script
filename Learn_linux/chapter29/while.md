bash는 앞의 방식과 유사하게 표현할 수 있다. 1부터 5까지의 연속된 5개의 숫자를 표시하길 원한다면 다음과 같이 bash 스크립트를 만들 수 있을 것이다.

```shell
#!/bin/bash

# while-count: display a series of numbers

count=1

while [ $count -le 5 ]; do
        echo $count
        count=$((count + 1))
done
echo "Finished."

```

스크립트가 실행되면 다음과 같은 결과를 볼 수 있다.

```shell
┌──(kali㉿kali)-[~]
└─$ ./while-count
1
2
3
4
5
Finished.
          
```

while 명령어의 문법은 다음과 같다.

`while commands; do commands; done`

[[chapter27 흐름 제어 if 분기#if의 사용|if]]와 유사하게, while은 명령어 목록의 종료 상태를 확인한다. 종료 상태가 0인 동안에는 루프 내에서 명령어를 실행한다. 이 스크립트에서는 count라는 변수가 생성되고 이 변수에는 1이라는 초기 값이 설정되었다. while 명령어로 [[test]] 명령어의 종료 상태를 확인한다. test 명령이 종료 상태 값 0을 반환하면 루프 내에 있는 모든 명령어들이 실행된다. 각 루프가 여섯 번 반복된 후에 count 값은 6으로 증가되고 test 명령은 더 이상 0인 종료 상태를 반환하지 않고 루프를 종료한다. 프로그램은 계속해서 루프 다음 구문을 이어 진행된다.

**while 루프**를 활용하여 28장의 **read-menu** 프로그램을 개선시켜보자.

```shell
#!/bin/bash

# while-menu: a menu driven system information program

DELAY=3 # Number of seconds to display results

while [[ $REPLY != 0 ]]; do
        clear
        cat <<- _EOF_
                Please Select:

                1. Display System Information
                2. Display Disk Space
                3. Display Home Space Utilization
                0. Quit
        _EOF_
        read -p "Enter selection [0-3] > "
        if [[ $REPLY =~ ^[0-3]$ ]]; then
                if [[ $REPLY == 1 ]]; then
                        echo "Hostname: $HOSTNAME"
                        uptime
                        sleep $DELAY
                fi
                if [[ $REPLY == 2 ]]; then
                        df -h
                        sleep $DELAY
                fi
                if [[ $REPLY == 3 ]]; then
                        if [[ $(id -u) -eq 0 ]]; then
                                echo "Home Space Utilization (All Users)"
                                du -sh /home/*
                        else
                                echo "Home Space Utilization ($USER)"
                                du -sh $HOME
                        fi
                        sleep $DELAY
                fi
        else
                echo "Invalid entry"
                sleep $DELAY
        fi
done
echo "Program terminated."

```

메뉴를 while 루프로 감싸서, 메뉴 선택 후에 프로그램이 메뉴를 다시 표시할 수 있도록 한다. 루프는 REPLY의 값이 0이 아니면 계속 반복되어, 사용자가 다른 선택을 할 수 있도록 기회를 주기 위해 메뉴가 다시 표시된다. 각 액션 끝에는 sleeep 명령어를 실행하여 프로그램이 몇 초간 멈추게 되는데, 이는 화면 내용이 지워지고 다시 메뉴가 뜨기 전에 선택 결과를 확인할 수 있도록 한다. REPLY의 값이 0이면 "종료"를 가리키게 하고 루프는 종료되어 done 이후의 스크립트를 계속해서 실행한다.

---
# 루프 탈출


bash는 두 개의 내장 명령어를 제공하는데, 루프 내에서 프로그램의 흐름을 제어하기 위함이다. break 명령어는 즉각적으로 루프를 중단하고 프로그램이 루프 다음에 나오는 구문들을 실행하도록 한다. continue 명령어는 루프가 진행되는 중간에 뒤에 남은 내용을 건너뛰고 다음 루프의 처음부터 실행하도록 한다. 이 두 명령어를 활용한 `white-menu` 프로그램을 예제로 살펴보도록 하자.

```shell
#!/bin/bash

# while-menu2: a menu driven system information program

DELAY=3 # Number of seconds to display results


while true; do
        clear
        cat <<- _EOF_
                Please Select:

                1. Display System Information
                2. Display Disk Space
                3. Display Home Space Utilization
                0. Quit

        _EOF_
		read -p "Enter selection [0-3] > "

        if [[ $REPLY =~ ^[0-3]$ ]]; then
                if [[ $REPLY == 1 ]]; then
                        echo "Hostname: $HOSTNAME"
                        uptime
                        sleep $DELAY
                        continue
                fi
                if [[ $REPLY == 2 ]]; then
                        df -h
                        sleep $DELAY
                        continue
                fi
                if [[ $REPLY == 3 ]]; then
                        if [[ $(id -u) -eq 0 ]]; then
                                echo "Home Space Utilization (All Users)"
                                du -sh /home/*
                        else
                                echo "Home Space Utilization ($USER)"
                                du -sh $HOME
                        fi
                        sleep $DELAY
                        continue
                fi
                if [[ $REPLY == 0 ]]; then
                        break
                fi
        else
                echo "Invalid entry."
                sleep $DELAY
        fi
done
echo "Program terminated."
                           
```

이 스크립트에서는 true 명령어를 사용하여 while 명령에 종료 상태를 제공하는 것으로 **무한 루프**(그 자체로는 영원히 끝나지 않는 루프)를 설정하였다. true는 종료 상태 0을 반환하기 때문에 루프는 영원히 종료될 수 없다. 놀랍게도 이것은 흔한 스크립트 작성 기법이다. 루프가 무한 반복되는 경우 적시에 루프에서 벗어나기 위한 방법을 제공하는 것은 프로그래머의 몫이다. 이 스크립트에서는 메뉴 0이 선택되면 break 명령어로 루프를 종료한다. continue 명령어는 또 다른 스크립트 선택지의 끝 부분마다 사용하여 더 효율적인 실행이 이루어지게끔 했다. continue 명령어를 사용함으로써 선택 메뉴가 확인된 경우 불필요해진 코드는 건너뛰도록 한다. 예를 들면, 1을 선택하고 이것이 확인됐다면 다른 메뉴들에 대해서는 선택 여부를 굳이 검사할 필요가 전혀 없는 것이다.

