
### 이전 장에서 우리는 어떤 문제와 맞닥뜨렸다. '우리가 작성 중인 보고서 생성 스크립트가 실행될 때 사용자 권한에 따라 결과를 어떻게 조정할 수 있을까?' 이 문제에 대한 해결책은 스크립트 내에서 테스트 결과에 따라 "방향을 바꾸는" 방법을 찾는데 있을 것이다. 프로그래밍식으로 말하면, 프로그램을 ==분기==할 필요가 있다.


**의사코드**로 작성된 예제 로직을 살펴보자. 의사코드란, 사람이 알아볼 수 있도록 컴퓨터 언어를 가상으로 작성한 코드를 의미한다.

``` shell
X=5
If X = 5, then:
	say "X equals 5."

Otherwise:
	Say "X is not equal to 5."
```

이것이 분기문의 예다. "X=5"라는 조건을 만족하면 "X는 5다"라고,그렇지 않으면 "X는 5가 아니다"라고 할 수 있다.

---
# if의 사용

쉘을 사용하여 다음과 같이 앞의 로직을 코딩할 수 있다.

```shell
x=5

if[$x=5]; then
	echo "x equals 5."
else
	echo "x does not equal 5."
fi
```

또는 커맨드라인에 직접 입력할 수도 있다(조금 짧아졌다).
```shell
┌──(kali㉿kali)-[~]
└─$ x=5
┌──(kali㉿kali)-[~]
└─$ if [ $x = 5 ]; then echo "equals 5"; else echo "does not equals 5"; fi
equals 5    

┌──(kali㉿kali)-[~]
└─$ x=0
                                 
┌──(kali㉿kali)-[~]
└─$ if [ $x = 5 ]; then echo "equals 5"; else echo "does not equals 5"; fi
does not equals 5

```

이 예제에서는 같은 명령을 두 번 실행했다. 먼저 x를 5로 설정한 후 실행하면 equals 5라는 문자열일 출려되고, 두 번째는 0으로 설정한 후에는 `does not equal 5`라는 문자열이 출력된다.

if의 사용법은 다음과 같은 구문을 따른다.

```shell
if commands; then
	commands
[elif commands; then
commands ...]
[else
      commands]
fi
```

***commands***에는 명령어 목록이 위치한다. 이 구문은 언뜻 보기엔 다소 혼란스러울 수 있다. 하지만 일단 완벽히 이해하기 전에, 어떻게 쉘이 명령어 실행의 성공여부를 결정짓는지에 대해서 먼저 살펴보도록 하자.

---
# 종료 상태

명령어들(우리가 작성하고 있는 스크립트와 쉘 함수를 모두 포함한 의미)은 종료될 때 **종료 상태**(exit status)라는 값을 생성한다. ==이 값은 0부터 255까지 사이의 정수로 명령어 실행의 성공 여부에 대한 정보를 나타낸다.== 일반적으로, 0은 성공을 나타내고 그 외의 다른 숫자는 시실패를 가리킨다. 쉘은 종료 상태를 확인할 수 있는 매개변수를 제공한다. 어떻게 동작하는지 살펴보자.

```shell
┌──(kali㉿kali)-[~]
└─$ ls -d /usr/bin      
/usr/bin
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo $?
0
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ ls -d /bin/usr
ls: cannot access '/bin/usr': No such file or directory
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo $?
2

```

이 예제에서 ls 명령을 두 번 실행했다. 첫 번째 실행은 성공적이다. `$?` 매개변수의 값이 0임을 알 수 있다. 두 번째로 `ls` 명령은 오류가 나고, 다시 `$?` 매개변수를 확인한다. ==그 값은 2 실행시 오류가 난다는 것을 의미한다.== 일부 명령들은 오류를 진단하기 위해 각기 다른 종료 상태 값을 지닌다. ==일반적으로 1이 실패했다는 것을 의미한다.== [[man]] 페이지들에는 종종 "Exit Status"라는 제목의 섹션이 있는데 여기에는 명령어마다 사용하는 종료 상태 값을 확인할 수 있다. 단, 0은 항상 성공을 의미한다.

쉘은 0 또는 1의 종료 상태 값을 가지고 아무런 일 없이 종료되는 아주 간단한 내장 명령어 두 가지를 제공하고 있다. true 명령어는 항상 성공적으로 실행되고, false 명령어는 항상 실패하게 된다.

```shell

┌──(kali㉿kali)-[~]
└─$ true           
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo $?
0
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ false  
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo $?
1

```

이 명령어들을 활용하여 if문이 어떻게 작동하는지 살펴보도록 하자. if문이 정말로 명령의 성공과 실패를 평가하는 것이 무엇인지 알아보자.

```shell
┌──(kali㉿kali)-[~]
└─$ if true; then echo "It's true."; fi                                   
It's true.
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ if false; then echo "It's true."; fi
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ 



```

`echo "It's true."` 명령은 if문이 성공적으로 수행됐을 때 실행되고, if문이 성공하지 않으면 이 명령 또한 실행되지 않는다. 만약 명령어 목록이 if에 따라오면, 그 중 마지막 명령이 평가된다.

```shell
┌──(kali㉿kali)-[~]
└─$ if false; then echo "It's true."; fi
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ if false; true; then echo "It's true."; fi
It's true.
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ if true; false; then echo "It's true."; fi

```

---
# test의 사용

- [[test]]



### 문자열 표현식

[표 27-2]에서 문자열을 검사하는 표현식을 알아보자.


- [표 27-2] 문자열 표현식


| 표현식                                           | 표현식 참인 경우...                                                        |
| --------------------------------------------- | ------------------------------------------------------------------- |
| *string*                                      | *string*은 null이 아니다.                                                |
| -n *string*                                   | *string*의 길이는 0보다 크다.                                               |
| -z *string*                                   | *string*의 길이는 0이다.                                                  |
| *string1* = *string2*, *string1* == *string2* | *string1*과 *string2*는 값다. 등호나 이중 등호가 사용될 수 있는데 보통은 이중 등호가 더 많이 쓰인다. |
| *string1*!=*string2*                          | *string1*과 *string2*는 같지 않다.                                        |
| *string1* > *string2*                         | *string1*은 *string2*보다 뒤에 정렬된다.                                     |
| *string1* < *string2*                         | *string1*은 *string2*보다 앞에 정렬된다.                                     |


>[!tip] 저자주
> `>,<` 연산자가 test와 함께 사용될 대는 반드시 따옴표 안에서 사용되어야 한다(백슬래시를 사용할 수도 있다). 그렇지 않으면 쉘이 이 연산자를 리다이렉션 연산자로 인식하여 원하는 결과를 얻지 못할 수 있다. 또한 bash 문서는 현재 로케일의 정렬 순서에 따라 순서를 서술하지만, 사실 그렇지만은 않다. ASCII(POSIX) 정렬 순서가 bash 4.0부터 최신 버전까지 사용되고 있다.

문자열 표현식을 사용한 예제를 살펴보자.

```shell
#!/bin/bash

# test-string: evaluate the value of a string

ANSWER=maybe


if [ -z "$ANSWER" ]; then
        echo  "There is no answer." >&2
        exit 1
fi

if [ "$ANSWER" = "yes" ]; then
        echo "The answer is YES."
elif [ "$ANSWER" = "no" ]; then
        echo "The answer is NO."
elif [ "$ANSWER" = "maybe" ]; then
        echo "The answer is MAYBE."
else
        echo "The answer is UNKNOWN."
fi

```

이 스크립트에서는 상수 ANSWER를 검증하였다. 우선 문자열이 비었는지 확인하고 비어있다면 스크립트를 종료하고 종료 상태를 1로 설정한다. [[echo]] 명령의 리다이렉션을 살펴보자. 이것은"There is no answer"라는 오류 메시지를 표준 오류로 전달한다. ==표준 오류는 오류 메시지를 표시하는 데 가장 적합하다.== 문자열이 비어있지 않은 경우, 문자열의 값을 검사하여 "yes", "no", 또는 "maybe"중 하나와 동일한지 검사한다. 이는 elif 명령을 사용하여 완료할 수 있는데, ==`else if`의 준말이다. `elif`로 조금 더 복잡한 논리 테스트를 구성할 수 있다.==


### 정수 표현식

[표 27-3]에 정수를 이용한 표현식을 소개한다.

- [표 27-3] 정수 표현식

| 표현식                       | 표현식이 참인 경우...                    |
| ------------------------- | -------------------------------- |
| *integer1* -eq *integer2* | *integer1*은 *integer2*와 같다.      |
| *integer1* -en *integer2* | *integer1*은 *integer2*와 같지 않다.   |
| *integer1* -le *integer2* | *integer1*은 *integer2*보다 작거나 같다. |
| *integer1* -lt *integer2* | *integer1*은 *integer2*보다 작다.     |
| *integer1* -ge *integer2* | *integer1*은 *integer2*보다 크거나 같다. |
| *integer1* -gt *integer2* | *integer1*은 *integer2*보다 크다.     |

이를 스크립트로 확인해보자.

```shell
#!/bin/bash

# test-string: evaluate the value of a string

ANSWER=maybe


if [ -z "$ANSWER" ]; then
        echo  "There is no answer." >&2
        exit 1
fi

if [ "$ANSWER" = "yes" ]; then
        echo "The answer is YES."
elif [ "$ANSWER" = "no" ]; then
        echo "The answer is NO."
elif [ "$ANSWER" = "maybe" ]; then
        echo "The answer is MAYBE."
else
        echo "The answer is UNKNOWN."
fi
   
```

이 스크립트의 흥미로운 점은 어떻게 정수 값이 짝수인지 홀수인지를 분별해내는가이다. ==그 방법은 바로 2로 나눈 나머지 값을 계산하는 모듈로 연산을 사용==하여, 그 결과로 수가 짝수인지 홀수인지 판단한다.

---
# 현대식 테스트

bash의 최신 버전에는 test의 역할을 대신하는 합성 명령어를 지원한다. 다음과 같은 구문을 따른다.

`[[ expression ]]`

*expression*에는 참/거짓을 판단하는 표현식을 입력한다. `[[]]` 명령식은 test와 매우 흡사하나(test의 모든 표현식을 지원한다). 중요한 새 문자열 표현식이 추가된다.

`string1 =~ regex`


만일 *string1*이 확장 정규 표현식인 *regex*에 부합하면 참을 반환하는 표현식이다. ==이것은 데이터 유효성 작업을 수행하는 등 다양한 가능성을 제공한다.== 정수 표현식의 첫 예제를 다시 보면 상수 `INT`값이 정수가 아닌 다른 것을 포함하고 있다면 스크립트 실행이 실패하게 된다. 그 스크립트는 상수가 정수임을 증명할 방법이 필요했다. `[[]]` 명령식과 문자열 표현식 연산자인 `=~` 기호를 이용하여 다음과 같이 해당 스크립트의 오류를 개선할 수 있다.

```shell
#!/bin/bash

# test-integer2: evaluate the value of an integer.

INT=-5

if [[ "$INT" =~  ^-?[0-9]+$ ]]; then
        if [ $INT -eq 0 ]; then
                echo "INT is zero."
        else
                if [ $INT -lt 0 ]; then
                        echo "INT is negative."
        else
                        echo "INT is positive."
        fi
        if [ $((INT % 2)) -eq 0 ]; then
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

정규 표현식을 적용함으로써 상수 `INT`값을 하나 이상의 숫자 값을 가진 경우로 제한할 수 있다. 마이너스 기호는 있을 수도 없을 수도 있다. 또한 이 표현식은 빈 값의 존재 가능성도 제거해주었다.

`[[]]` 명령식의 또 다른 기능은 바로 `==` 연산자가 경로명 확장과 똑같은 방식의 패턴 찾기를 지원한다는 점이다. 예를 들어보자.

```shell
┌──(kali㉿kali)-[~]
└─$ FILE=foo.bar              
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ if [[ $FILE == foo.* ]]; then                                         
then> echo "$FILE matches pattern 'foo.*'"
then> fi               
foo.bar matches pattern 'foo.*'
                                
```

이는 `[[]]` 명령이 파일과 경로명을 평가할 때 유용하게 해 준다.


---
# (()) - 정수 테스트

bash는 `[[]]` 합성 명령어 뿐만 아니라 `(())` 명령식 또한 지원한다. 이것은 정수에 대한 연산을 수행할 때 유용하다. 이 식으로 모든 산술 연산이 가능하고 이 부분은 34장에서 자세하게 다룰 것이다.

`(())` 명령식은 산술식의 참 여부를 검사한다. 이 명령은 산술식의 결과가 0이 아니면 참 값을 반환한다.

```shell
┌──(kali㉿kali)-[~]
└─$ if ((1)); then echo "It is true."; fi
It is true.
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ if ((0)); then echo "It is true."; fi
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ 

```

`(()) ` 명령식으로 test-integer2 스크립트를 좀 더 단순하게 변형할 수 있다.

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

우리는 여기서 부등호 연산과 등호 연산을 사용하였다. 이렇게 함으로써 정수 표현식이 한결 자연스러워 보인다. 또한 `(())` 합성 명령이 일반적인 명령이 아닌 쉘 구문이기 때문에 그리고 정수만 취급하기 때문에, 이름으로 변수를 구분할 수 있고 더 이상 확장이 필요하지 않다.

---
# 표현식 조합

더 복잡한 검사를 진행하기 위해 표현식을 조합하는 것도 가능하다. 표현식은 논리 연산자를 이용함으로써 조합이 가능하다. 우리는 이미 17장에서 [[find]] 명령에 대해 학습할 때 이를 보았다. [[test]] 명령어와 `[[]]`합성 명령이 지원하는 논리 연산자는 세 가지다. AND, OR, NOT이다. test 명령과 `[[]]` 합성 명령은 이 연산을 수행하기 위해서 각기 다른 연산자를 사용하는데, 다음 [표 27-4]를 확인해보도록 하자.

- [표 27-4] 논리 연산자


| 논리 연산 | test | `[[]]`,`(())` |
| ----- | ---- | ------------- |
| AND   | -a   | &&            |
| OR    | -o   | \|\|          |
| NOT   | !    | !             |


AND 연산의 예제를 보자. 다음 스크립트에서는 값이 정수 범위 내에 있는지 확인한다.

```shell
┌──(kali㉿kali)-[~]
└─$ cat test-integer3
#!/bin/bash

# test-integer3: determine if an integer is within a
# specified range of values.

MIN_VAL=1
MAX_VAL=100

INT=50

if [[ "$INT" =~ ^-?[0-9]+$ ]]; then
        if [[ INT -ge MIN_VAL && INT -le MAX_VAL ]]; then
                echo "$INT is within $MIN_VAL to $MAX_VAL."
        echo
                echo "$INT is out of range."
        fi
else
        echo "INT is not an integer." >&2
        exit1
fi

```



스크립트를 보면 `INT`라는 정수 값이 `MIN_VAL`와 `MAX_VAL` 사이에 있는지를 확인한다. 이는 && 연산자로 구분된 두 표현식을 포함한 `[[]]` 명령어를 사용했다. 이는 또한 test 명령어로 할 수 있다.

```shell
if [[ "$INT" =~ ^-?[0-9]+$ ]]; then
        if [ $INT -ge $MIN_VAL -a $INT -le $MAX_VAL ]; then
                echo "$INT is within $MIN_VAL to $MAX_VAL."
        echo
                echo "$INT is out of range."
        fi

```

! 부정 연산자는 표현식 결과의 값을 반환한다. 표현식이 거짓이면 참 값을 반환하고, 표현식이 참이면 거짓을 반환한다. 다음 스크립트에서 특정 범위 밖에 있는 INT 값을 찾기 위해 계산식을 바꿔보도록 하자.

```shell
#!/bin/bash

# test-integer4: determine if an integer is within a
# specified range of values.

MIN_VAL=1
MAX_VAL=100

INT=50

if [[ "$INT" =~ ^-?[0-9]+$ ]]; then
        if [[ ! (INT -ge MIN_VAL && INT -le MAX_VAL) ]]; then
                echo "$INT is within $MIN_VAL to $MAX_VAL."
        echo
                echo "$INT is out of range."
        fi
else
        echo "INT is not an integer." >&2
        exit1
fi



```

표현식을 그룹화하기 위해 괄호를 사용하였다. 괄호를 사용하지 않았다면 부정 연산자는 첫 번째 식에만 적용이 되었을 뿐 두 개로 혼합된 표현식 전체에 적용되지 않았을 것이다. 다시 한번 test 명령어로도 스크립트를 작성해보자.

```shell
#!/bin/bash

# test-integer4: determine if an integer is within a
# specified range of values.

MIN_VAL=1
MAX_VAL=100

INT=50

if [[ "$INT" =~ ^-?[0-9]+$ ]]; then
        if [ ! \( $INT -ge $MIN_VAL -a $INT -le $MAX_VAL \) ]; then
                echo "$INT is within $MIN_VAL to $MAX_VAL."
        else
                echo "$INT is out of range."
        fi
else
        echo "INT is not an integer." >&2
        exit 1
fi


```

test에서 사용되는 모든 표현식과 연산자는 쉘의 입장에서는 test의 명령 인자로 인식되기 때문에 (`[[]]`, `(())` 명령식과는 달리) bash에서 특별한 의미를 하진 `<,>,(,)` 기호와 같은 문자들은 반드시 따옴표나 이스케이프 기호와 함께 사용해야 한다.

test 명령어나 `[[]] ` 명령식은 전반적으로 같은 일을 수행하는 데, 어느 것이 더 사용하기 좋을까? test는 전통적으로 쓰이는 명령어(그리고 POSIX의 일부분)인  반면, `[[]] ` 명령식은 bash에 특화되어 있다. test 명령어가 광범위하게 사용되기 때문에 그 사용법을 알아두는 것은 매우 중요하다. 하지만 `[[]]` 명령식은 test 명령어 사용법보다 훨씬 유용하고 코딩하기에 쉽다는 것은 분명한 사실이다.

>[!tip] 이식성은 일방적인 방식일 뿐
> "진짜" 유닉스 사용자들과 대화를 하다 보면, 대다수가 리눅스를 그다지 달답게 여기지 않음을 금방 알아차리게 된다. 그들은 리눅스가 마치 순수하지 않고 깨끗하지 못한 것이라 생각한다. 유닉스 추종자들이 따르는 교리 중 하나는 유닉스의 모든 것은 **이식성**이 있어야 한다는 것이다. 이 말은 여러분이 작성하는 어떤 스크립트라도 유닉스형 시스템에서는 별도의 추가 작업 없이 실행되어야 한다는 것이다.
> 
> 유닉스 사용자들이 이러한 교리를 따르는 데는 충분한 이유가 있다. POSIX 이전의 유닉스 세계에서 명령어와 쉘들이 상업적으로 확장된 광경을 목격했기 때문에, 그들이 사랑하는 운영체제에 리눅스가 끼칠 영향력에 대해 자연스럽게 경계하게 되었다.
> 
> 하지만 이식성이라는 것은 아주 심각한 단면이 존재한다. ==바로 발전을 막는다.== 그 이유는 "최소한의 공통 분모"만을 가지려 하기 때문이다. 쉘 프로그래밍의 경우에는, 모든 것은 원조 Bourne Shell인 sh와 호환되도록 만들어야 함을 의미한다.
> 이러한 단면은 "혁명"이라는 미명하에 상업적 확장을 정당화하기 위해 상업용 벤더들이 이용하는 구실일 뿐이다. 하지만 이러한 상업적인 확장은 고객에게 아주 불리한 장치임이 틀림없다.
> bash와 같은 GNU 툴들은 아무런 제약이 없다. 오히려 표준에 따라 전반적으로 사용이 가능하도록 이식성을 장려한다. 거의 대부분의 시스템에 bash와 또 다른 GNU 툴들을 설치할 수 있다. 심지어 윈도우즈 시스템에 아무런 비용 없이 설치가 가능하다. 따라서 bash의 모든 기능을 사용해보는 것을 두려워하지 말길 바란다. 이야말로 진정한 이식성 아닌가?


---
# 제어 연산자: 분기의 또 다른 방법


bash는 분기를 수행할 수 있는 두 개의 제어 연산자를 제공한다. &&(AND)와 ||(OR) 연산자는 `[[]]` 합성 명령어에서 사용되는 논리 연산자와 수행하는 방식이 같다. 구문은 다음과 같다.

`command1 && command2`

그리고

`command1 || command2`


이들이 수행되는 행동 방식을 이해해야 한다. `&&`연산자를 사용하는 경우, `command1`과 `command2` 모두 실행되면 `command1`이 성공했다는 것을 의미한다. `||`연산자를 사용하는 경우, `command1`과 `command2` 모두 실행되면 `command1`은 실패했다는 것을 의미한다.


다시 말하자면 다음과 같이 사용할 수 있다는 것이다.

```shell

┌──(kali㉿kali)-[~]
└─$ mkdir temp && cd temp     
```

이것은 temp라는 디렉토리를 생성하고 성공하면 현재 작업 디렉토리를 temp로 바꾸라는 의미다. 두 번째 명령은 mkdir 명령이 성공적으로 수행되어야만 실행된다. 이와 같이 다음 명령을 이용하여 temp라는 디렉토리의 존재 유무를 검사해보자.

```shell
┌──(kali㉿kali)-[~/temp]
└─$ [ -d temp ] || mkdir temp
           
```

테스트가 실패해야만 디렉토리가 생성될 것이다. 이런 식의 구성은 스크립트 오류를 관리할 대 매우 유용하다. 추후에 이 부분에 대해서 더 자세하게 공부하게 될 것이다. 예를 들어 다음과 같이 스크립트를 꾸며볼 수 있다.

```shell
[ -d temp ] || exit 1
```

스크립트는 temp라는 디렉토리를 필요로 하고 만약 존재하지 않으면 스크립트는 종료 상태 1을 반환하고 종료될 것이다.


---
# 마무리 노트

이번 장을 시작할 때 우리는 질문 하나를 던졌다. 어떻게 하면 sys_info_page 스크립트 내에서 그 사용자가 모든 홈 디렉토리에 대한 읽기 권한이 있는지 감지할 수 있는지에 대한 것이다. if문을 활용하여 report_home_space 함수에 다음과 같은 코드를 삽입하면 이 문제를 해결할 수 있다.

```shell

report_home_space () {
        if [[ $(id -u) -eq 0]]; then
                cat <<- _EOF_
                        <H2>Home Space Utilization (All Users)</H2>
                        <PRE>$(du -sh /home/*)</PRE>
                        _EOF_
        else
                cat <<- _EOF_
                        <H2>Home Space Utilization ($USER)</H2>
                        <PRE>$(du -sh $HOME)</PRE>
                        _EOF_
        fi
        return
}

```

우리는 id 명령어의 결과를 확인하였다. id 명령의 -u 옵션은 유효 사용자에 대하여 숫자로 된 사용 ID를 출력할 것이다. 슈퍼유저는 항상 0이며 나머지 모든 사용자는 0보다 큰 수를 가진다. ==이러한 사실을 바탕으로 우리는 두 개의 서로 다른 here 문서를 구성할 수 있는데, 하나는 슈퍼유저의 권한이라는 장점을 활용한 것이고 또 다른 하나는 해당 사용자의 홈 디렉토리로 제한을 두는 것이다.==

이쯤에서 잠시 sys_info_page 프로그램을 접어둘 예정이지만 너무 걱정하지 마라. 곧 다시 들춰보게 될 것이다. 그 동안 우리는 이 작업을 다시 시작하기 위해 필요한 몇 가지 주제에 대해 살펴보게 될 것이다.