
### 지금까지 우리가 작성한 스크립트들은 대다수 컴퓨터 프로그램에서 사용하는 일반적인 기능이 빠져 있다. 바로 ==대화식== 모드다. 이것은 사용자와 프로그램 사이에 상호작용하는 것을 말한다. 이것은 사용자와 프로그램 사이에 상호 작용하는 것을 말한다. 모든 프로그램들이 대화식으로 작동할 필요는 없지만 일부 프로그램에서는 사용자로부터 직접적인 입력을 허용하는 것이 효과적일 수도 있다. 예를 들어 예전에 작성한 스크립트들을 다시 살펴보자.

```shell
#!/bin/bash

# test-integer2: evaluate the value of an integer.

INT=-5

if [[ "$INT" =~  ^-?[0-9]+$ ]]; then
        if (( $INT == 0 )); then
                echo "INT is zero."
        else
                if (( $INT < 0 )); then
                        echo "INT is negative."
        else
                        echo "INT is positive."
        fi
        if (( $((INT % 2)) == 0 )); then
                        echo "INT is even."
        else
                        echo "INT is odd."
        fi
fi
else
        echo "INT is not a integer." >&2
        exit1
fi


```

`INT` 값을 변경하려고 할 때마다 스크립트를 수정해야만 한다. 만약 값을 변경할 때, 사용자에게 변경할 값에 대해 묻게 한다면 이 스크립트는 훨씬 더 효율적이지 않을까. 이 장에서는 우리 프로그램에 대화식 모드를 어떻게 추가할 수 있는지 다뤄보기로 한다.

---
# read - 표준 입력에서 값 읽어오기

- [[read]]


---
# 입력 값 인증

키보드 입력을 읽을 수 있는 새로운 능력을 갖춤으로써 추가적인 프로그래밍 문제를 얻게 되었다. 바로 입력 내용을 검증하는 것이다. 잘 작성된 프로그램과 그렇지 않은 프로그램 사시에 흔히 볼 수 있는 차이점은 바로 예상치 못한 상황에서 대처하는 능력이다. 주로, 예상치 못한 상황은 잘못된 입력에서 비롯된다. 이전 장에서 만들었던 계산 프로그램으로 이러한 상황을 구성해보려 한다. 우리는 이 프로그램에서 정수 값을 검사하고 빈 값이나 숫자가 아닌 문자인 경우를 걸러냈다. 유효하지 않은 데이터 입력을 방지하는 차원에서 프로그램이 입력 값을 받을 대마다 항상 검사하도록 만드는 것은 매우 중요하다. 특히 여러 사용자에게 공유되는 프로그램인 경우 더욱 그러하다. 단 한번만 사용되는 프로그램이나 작성자에 의해 특별한 작업을 수행하는 경우에는 경제성을 위해 이러한 안전망을 생략하는 것은 예외가 될 수 있겠다. 그렇다 하더라도, 그 프로그램이 파일을 삭제하는 것과 같은 위험한 작업을 수행하는 경우에는 만일을 대비하여 입력 내용을 검증하는 작업을 수행하는 것이 현명한 처사일 것이다.

다음은 여러 종류의 데이터 입력을 검증하는 예제 프로그램이다.

```shell
#!/bin/bash

# read-validate: validate input

invalid_input(){

        echo "Invalid input '$REPLY'" >&2
        exit 1
}

read -p "Enter a single item > "

# input is empty (invalid)
[[ -z $REPLY ]] && invalid_input

# input is multiple items (invalid)
(( $(echo $REPLY | wc -w) > 1 )) && invalid_input


# is input a valid filename;
if [[ $REPLY =~ ^[-[:alnum:]\._]+$ ]]; then
        echo "'$REPLY' is a valid filename."
        if [[ -e $REPLY ]]; then
                echo "And file '$REPLY' exists."
        else
                echo "However, file '$REPLY' does not exists."
        fi

        # is input a floating point number?
        if [[ $REPLY =~ ^-?[[:digit:]]*\.[[:digit:]]+$ ]]; then
                echo "'$REPLY' is a floating point number."
        else
                echo "'$REPLY' is not a floating point number."
        fi

        # is input an integer?
        if [[ $REPLY =~ ^-?[[:digit:]]+$ ]]; then
                echo "'$REPLY' is an integer."
        else
                echo "'$REPLY' is not an integer."
        fi
else
        echo "The string '$REPLY' is not a valid filename."
fi

```

앞의 스크립트는 사용자에게 항목을 입력하라는 프롬프트 메시지를 띄운다. 이 항목은 이어서 그 내용을 검증하기 위해서 분석된다. 앞에서 볼 수 있듯이, 이 스크립트는 지금까지 우리가 다룬 다양한 개념들을 활용하고 있다. 쉘 함수, `[[]]`, `(())`, 제어 연산자인 `&&` 연산자와 `if` 그리고 적당량의 정규 표현식이 사용되었다.

---
# 메뉴

대화 모드의 일반적인 형식은 **메뉴 방식**이다. 이 메뉴 방식의 프로그램에서는 사용자에게 선택지를 주어 선택하도록 한다. 예를 들어서 생각해보자.


```shell
Please Select:

1. Display System Information
2. Display Disk Space
3. Display Home Space Utilization
0. Quit

Enter selection [0-3] > 
```


`sys_info_page` 프로그램에서 배운 것을 활용하여, 우리는 메뉴 방식이 프로그램을 구성해 앞의 메뉴에서 작업을 수행할 수 있다.

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

이 스크립트는 논리적으로 두 부분으로 나뉘다. 첫 번째 부분은, 메뉴를 표시하여 사용자의 응답을 입력하는 것이다. 두 번째 부분은, 응답을 확인하고 선택된 행동을 수행한다. 이 스크립트의 exit 명령에 주목하자. 이는 특정 동작이 수행된 후 스크립트가 불필요한 코드를 실행하는 것을 방지하기 위한 것이다. 일반적으로 프로그램에서 exit 지점이 여러 군데서 보인다면 그다지 좋은 방식이 아니다(프로그램 로직을 이해하기 더 힘들게 한다). 하지만 이 스크립트에서는 그냥 사용하기로 한다.

---
# 마무리 노트

이번 장에서는 대화식 모드에 대한 첫 단계를 진행했다. 사용자에게 프로그램상에서 키보드를 이용하여 입력을 허용한 것이다. 지금까지 배운 기능들을 활용하여 많은 유용한 프로그램들을 만들 수 있다. 특수한 계산 프로그램이나 불가사의해 보이는 커맨드라인 툴을 위한 편리한 프론트엔드와 같은 것을 말이다. 다음 장에서는 메뉴 방식의 프로그램 개념을 점목하여 훨씬 더 멋있게 만들어볼 것이다.

---
# 추가 학습

이 장의 프로그램들을 주의 깊게 학습하고 논리적으로 구성된 방식을 완벽히 이해하는 것이 중요하다. 연습 문제로 `[[]]` 합성 명령어로 된 프로그램들을 test 명령어로 재작성해봐라. 힌트를 주자면, grep 명령어 정규 표현식을 사용하고 그 종료 상태를 확인해라. 이주 좋은 학습이 될 것이다.