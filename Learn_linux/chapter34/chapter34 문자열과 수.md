

### 컴퓨터 프로그램은 데이터를 가지고 작업하는 것이 전부라고 해도 과언이 아니다. 이전에는 파일 수준의 데이터 처리에 초점을 맞췄다. 하지만 다수의 프로그래밍 문제들이 문자열과 숫자처럼 더 작은 데이터 단위를 사용하여 해결되는 경우가 많이 있다.

이 장에서는 문자열과 수를 조작하는 쉘의 여러 기능들 살펴볼 것이다. 쉘은 문자열 연산을 수행하는 다양한 파라미터 확장을 제공한다. 추가적으로 산술 확장(7장에서 다룬)에 대해서도 살펴볼 것이다. 여기서는 고수준의 계산을 bc라는 커맨드라인 프로그램을 소개할 것이다.


---
# 매개변수 확장

이미 7장에서 매개변수 확장을 언급했지만 자세하게 다루지는 않았다. 대부분의 매개변수 확장이 커맨드라인보다 스크립트에서 사용되기 때문이다. 우리는 이미 쉘 변수처럼 매개변수 확장의 일부 형식을 사용해왔다. 쉘은 더 많은 것들을 제공한다.


### 기본 매개변수

 가장 단순한 형태의 매개변수 확장은 일반적인 변수의 사용이다. 예를 들면 , `$a`는 그 변수가 가진 값으로 확장된다. 단순 매개변수는 `${a}`와 같이 중괄호로 감쌀 수도 있다. 이는 확장에는 아무런 영향을 주지 않지만 쉘이 혼동할 수 있다면 필요하다. 이 예제에서는 변수 a의 내용에 *_file* 문자열을 추가하여 파일명을 생성하려 한다.


```shell
┌──(kali㉿kali)-[~]
└─$ a="foo"         
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo "$_file"


```

이대로 실행하면, 아무런 결과도 없을 것이다. 쉘 a가 아닌 a_file을 변수명으로 확장했기 때문이다. 이 문제는 다음과 같이 중괄호로 해결할 수 있다.

```shell
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo "${a}_file"
foo_file
```

또한 우리는 이미 중괄호에서 숫자를 둘러싸서 9보다 큰 위치 매개변수에 접근할 수 있는 것을 보았다. 예를 들면, 11번재 위치의 매개변수에 접근하기 위해서는 `${11}`를 사용하면 된다.


### 빈 변수를 관리하기 위한 확장

여러 매개변수 확장들이 존재하지 않거나 빈 변수를 처리할 수 있다. 이러한 확장들은 위치 매개변수의 부재를 제어하고 매개변수에 기본값을 할당하기 쉽게 한다. 그러한 확장이 여기 있다.

`${parameter: -word}`

*parameter*가 설정되지 않거나(즉, 존재하지 않으면) 비어있다면, 이 확장 결과는 *word*의 값이 된다. 만약 *parameter*가 비어있지 않다면, 확장 결과는 *parameter*의 값이 된다.

```shell                                    
┌──(kali㉿kali)-[~]
└─$ foo=                                   
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo:-"substitute value if unset"}
substitute value if unset
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo $foo                               

                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ foo=bar
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo:-"substitute value if unset"}
bar
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo $foo                               
bar

```

여기 또 다른 확장이 있다. 이 확장은 대시 기호 대신에 등호를 사용한다.

`${parameter:=word}`


*parameter*가 설정되지 않거나 비어있다면, 이 확장 결과는 *word*의 값이 된다. 게다가 *word*의 값은 *parameter*에 할당된다. 만약 *parameter*가 비어있다면, 확장 결과는 *parameter*의 값이 된다.
```shell
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ foo=
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo:="default value if unset"}
default value if unset
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo $foo                            
default value if unset
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ foo=bar
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo:="default value if unset"}
bar
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo $foo                            
bar

```

>[!tip] 저자주
>위치 매개변수나 다른 특수한 매개변수들은 이러한 방식으로 할당될 수 없다.


이번에는 물음표 기호를 사용한다.

`${parameter:?word}`

*parameter*가 설정되지 않거나 비어있다면, 이 확장으로 오류가 발생하여 스크립트는 종료될 것이다. 그리고 *word*의 값은 표준 출력으로 보내진다. 만약 *parameter*가 비어있지 않다면, 확장 결과는 *parameter*의 값이 된다.

```shell
┌──(kali㉿kali)-[~]
└─$ foo=   
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo:?"parameter is empty"}    
zsh: foo: "parameter is empty"
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo $?                          
1
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ foo=bar
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo:?"parameter is empty"}
bar
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo $?                          
0
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ 


```



이번에는 더하기 부호다.


`${parameter:+word}`


*parameter*가 설정되지 않거나 비어있다면, 이 확장은 아무런 결과를 표시하지 않는다. 만약 *paramter*가 비어있지 않다면, *parameter*는 word의 값으로 대체된다. 하지만 *parameter*의 값은 변하지 않는다.

```shell
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ foo=   
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo:+"substitute value if set"}

                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ foo=bar
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo:+"substitute value if set"}
substitute value if set
                      
```


### 변수명을 반환하는 확장

쉘은 변수명을 반환하는 기능이 있다. 이 기능은 이례적인 상황에서 사용된다.

`${!prefix*} ${!prefix@}`

이 확장은 *prefix*로 시작되는 이미 존재하는 변수의 이름을 반환한다. bash 문서에 따르면, 이 두 확장 형식은 동일하게 동작한다. 즉 환경 값에 저장된 BASH로 시작하는 이름을 가진 모든 변수를 나열한다.

```shell
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${!BASH*}  
```

### 문자열 연산

확장들의 집합은 문자열을 조작하기 위해 사용될 수 있다. 이러한 확장들은 특히 경로명을 조작하기에 적당하다.

`${#parameter}`

이 확장은 *parameter* 가 포함된 문자열의 길이로 확장된다. 일반적으로 *parameter*는 문자열이지만 만약 @이거나, `*`이면 그 확장 결과는 위치 매개변수의 개수를 나타낸다.

```shell
┌──(kali㉿kali)-[~]
└─$ foo="This string is long."
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo "'$foo' is ${#foo} characters long."
'This string is long.' is 20 characters long.
                                                  
```

`${parameter:offset}`
`${parameter:offset:length}`

이 확장은 `parameter`에 포함된 문자열의 일부를 추출하기 위해 사용된다. `offset`에 위치한 문자부터 시작해서 `length`를 명시하지 않으면 문자열 끝까지 추출한다.

```shell
┌──(kali㉿kali)-[~]
└─$ foo="This string is long."               
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo:5}                            
string is long.
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo:5}
string is long.
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo:5:6}
string
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ 

```

만약 `offset` 값이 음수이면, 문자열의 앞부분이 아닌 끝부분부터 시작하라는 것을 의미한다. `${parameter:-word}` 확장과 혼동을 막기 위해 음수 값은 반드시 앞에 스페이스를 두어야 한다.
`length`값은 0보다 작아서는 안된다.

만약 *parameter*가 `@`이면, 확장의 결과는 `offset`에서 시작하는 `length`의 위치 매개변수이다.

```shell
┌──(kali㉿kali)-[~]
└─$ foo="This string is long."
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo:-5}  # 만약 :와 - 사이 띄어쓰기를 하지 않으면 전체가 다 나온다.
This string is long.
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo: -5}
long.
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo: -5:2}
lo

```

`${parameter#pattern}`
`${parameter##pattern}`


이 확장들은 매개변수가 가진 문자열에서 *pattern*에 정의된 내용으로 시작되는 부분을 제거한다. *pattern*은 경로명 확장에 사용되는 것처럼 와일드카드 패턴이다. 두 형식의 차이점은 `#` 형식은 최단 길이로 일치하는 것을 제거하고, 반면 `##` 형식은 최장 길이로 일치하는 것을 제거한다.

```shell
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ foo=file.txt.zip          
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo#*.}   
txt.zip
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo##*.}
zip
```

`${parameter%pattern}`
`${parameter%%pattern}`

이 확장들은 앞의 `#`와 `##` 확장과 동일하다. 다만 문자열의 시작이 아닌 끝에서부터 제거한다는 것이 다르다.

```shell
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ foo=file.txt.zip
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo%.*} 
file.txt
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo%%.*}
file
      
```

`${parameter/pattern/string}`
`${parameter/pattern/string}`
`${parameter/#pattern/string}`
`${parameter/%pattern/string}`


이 확장은 `parameter`의 내용은 치환한다. 만약 와일드카드 `pattern`과 일치하는 텍스트를 발견하면 `string`의 내용으로 대체된다. 일반적인 형식은 단지 `pattern`과 일치하는 첫 부분만 대체한다. `//` 형식은 일치하는 모든 부분을 대체한다. `/#` 형식은 문자열의 시작 부분에서 일치하는 경우에, `/%` 형식은 문자열 끝 부분에서 일치하는 경우에 사용된다. `/string`을 생략하면 `pattern`과 일치하는 텍스트는 삭제된다.


```shell
┌──(kali㉿kali)-[~]
└─$ foo=JPG.JPG     
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo/JPG/jpg}
jpg.JPG
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo//JPG/jpg}
jpg.jpg
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo/#JPG/jpg}
jpg.JPG
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo ${foo/%JPG/jpg}
JPG.jpg
         
```

매개변수 확장은 알아두기에 유용한 것이다. 문자열 조작 확장은 [[sed]]와 [[cut]]과 같은 일반 명령어의 대체재로 사용된다. 확장은 외부 프로그램 사용을 없애 스크립트 효율을 증진시킨다. 예제처럼, 우리는 이전 장에 논의했던 최장 길이 단어 프로그램을 수정할 것이다.`$(echo $j | wc -c)` 명령을 `${#j}` 매개변수 확장으로 바꾸어 사용한다.


```shell
#!/bin/bash

# longest-word3: find longest string in a file

for i; do
        if [[ -f $i ]]; then
                max_word=
                max_len=
                for j in $(strings $i); do
                        len=${#1}
                        if (( len > max_len )); then
                                max_len=$len
                                max_word=$j
                        fi
                done
                echo "$i: '$max_word' ($max_len characters)"
        fi
        shift
done
```

다음은 time 명령어를 사용하여 두 버전의 효율성을 비교할 것이다.

```shell
┌──(kali㉿kali)-[~]
└─$ time longest-word2 dirlist-usr-bin.txt
longest-word2: command not found

real    0.26s
user    0.14s
sys     0.06s
cpu     75%
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ time longest-word3 dirlist-usr-bin.txt
longest-word3: command not found

real    0.15s
user    0.11s
sys     0.04s
cpu     98%

```

원본 스크립트 텍스트 파일을 검사하는 것보다, 매개변수 확장을 통한 새 버전은 이보다 훨씬 줄어들었다. 꽤나 큰 발전이다.

---

# 산술 연산과 확장

우리는 [[chapter07 확장과 인용|7장]]에서 산술 확장에 대해 알아보았다. 즉 정수에 대하여 다양한 산술 연산을 수행할 수 있다는 것인데, 기본적인 형식은 다음과 같다.

`$((expression))`

*expression*는 유효한 산술식이다. 이는 27장에서 본 산술 평가(참 여부 테스트)용으로 사용하는 복합 명령어 `(()) `와 관련이 있다. 지금까지 우리는 표현식과 연산자의 일반적인 형태를 보았다. 여기서는 좀 더 완전한 목록을 살펴볼 것이다.


### 기수

우리는 [[chapter09 퍼미션|9장]]에서 8진수와 16진수를 살펴보았다. 쉘은 산술식에서 모든 기수의 정수 상수를 제공한다. [표 34-1]은 기수를 명시하는데 사용하는 표기법을 보여준다.

- [표 34-1] 기수 지정

| 표기            | 설명                              |
| ------------- | ------------------------------- |
| *Number*      | 기본값, 아무런 기호 없는 수는 10진 정수로 처리한다. |
| *0Number*     | 산술식에서 0으로 시작하는 수는 8진법을 나타낸다.    |
| *0xnumber*    | 16진법                            |
| *base#number* | *base*를 기수로 하는 수                |


예제:

```shell
┌──(kali㉿kali)-[~]
└─$ echo $((0xff))
255
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo $((2#11111111))
255
```

이 예제에서는 16진수 ff(두 자릿수 중 가장 큰)의 값과 8자리의 2진수 중 가장 큰 값을 출력한다.


### 단항 연산자


양수인지 음수인지를 나타내는 `+`와 `-` 두 단항 연산자가 있다.



### 기본 연산

[표 34-2]에 일반 산술 연산자들이 나열되어 있다.

- [표 34-2] 기본 연산자

| 연산자  | 설명        |
| ---- | --------- |
| `+`  | 덧셈        |
| `-`  | 뺄셈        |
| `*`  | 곱셈        |
| `/`  | 정수 나눗셈    |
| `**` | 거듭제곱      |
| `%`  | 모듈로 (나머지) |

대부분은 명확하지만, 정수 나눗셈과 모듈로는 좀 더 논의가 필요하다.

쉘의 산술 연산은 오직 정수에서 이루어지기 때문에 나눗셈의 결과는 항상 자연수다.

```shell
┌──(kali㉿kali)-[~]
└─$ echo $(( 5/2 ))     
2
     
```

사실은 나눗셈 연산의 나머지를 결정하는 데 더 중요하다.

```shell
┌──(kali㉿kali)-[~]
└─$ echo $(( 5%2 ))
1

```

나눗셈과 모듈로 연산자를 사용하여 5 나누기 2의 결과 2와 나머지 1을 확인할 수 있다.

반복문에서 나머지 계산은 유용하다. 특정한 주기마다 실행하는 연산을 허용하기 때문이다. 다음 예제에서는 5의 배수마다  하이라이트를 표시한다.

```shell
#!/bin/bash

# modulo: demonstrate the modulo operator

for (( i = 0; i <= 20; i = i + 1)); do
        remainder=$((i % 5))
        if (( remainder == 0 )); then
                printf "<%d> " $i
        else
                printf "%d " $i
        fi
done
printf "\n"
                   
```

실행 결과는 다음과 같다.

```shell
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ ./modulo  
<0> 1 2 3 4 <5> 6 7 8 9 <10> 11 12 13 14 <15> 16 17 18 19 <20> 

```

### 대입

비록 산술식에서 대입의 쓰임이 직접적으로 보이지 않더라도 그것은 수행되고 있을지도 모른다. 이미 여러 변의 대입이 이뤄지고 있었다. 변수에 값을 줄 때마다 대입이 이루어졌고 산술식 내에서도 사용될 수 있다.

```shell
┌──(kali㉿kali)-[~]
└─$ echo $foo      

                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ if (( foo = 5 ));then echo "It is true."; fi
It is true.
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo $foo
5

```

이 예제에서는 먼저 빈 값을 변수 foo에 대입하고 그 값이 제대로 들어갔는지 확인한다. 그 다음 if와 함께 합성 명령 `(( foo = 5 )) `을 수행한다. 이 과정은 두 가지 흥미로운 점이 있다. foo 변수에 5 값을 대입하는 것과 그 할당이 성공했기 때문에  값이 참으로 평가된다는 점이다.


>[!tip] 저자주
> 이 식의 `=` 연산자의 정확한 의미를 기억해야 한다. `=` 연산자는 foo에 5를 할당하라는 의미다. 반면 `==` 연산자는 foo가 5와 같은지 비교하라는 명령이다. 이는 test 명령어가 단일 `=` 기호를 문자열 비교에 사용하기 때문에 매우 혼동할 수 있다. 이것이 테스트문에서 더 최근 방식인 `[[]] `와 `(()) `합성 명령어를 사용하는 또 다른 이유다.

추가적으로 쉘은 `=`에 다양한 대입 연산을 수행하는 표기법을 제공한다. 이는 [표 34-3]dmfh 확인할 수 있다.

- [표 34-3] 대입 연산자

| 표기                     | 설명                                                        |
| ---------------------- | --------------------------------------------------------- |
| *`parameter = value`*  | 단순 대입. parameter에 value를 대입한다.                            |
| *`parameter += value`* | 덧셈. *`parameter = parameter + value`* 와 동일하다.             |
| *`parameter -= value`* | 뺄셈. *`parameter = parameter - value`* 와 동일하다.             |
| `parameter *= value`   | 곱셈 `parameter = parameter * value`와 동일하다.                 |
| `parameter /= value`   | 정수 나눗셈 `parameter = parameter * value`와 동일하다.             |
| `parameter %= value`   | 모듈로 `parameter = parameter % value`와 동일하다.                |
| *`parameter++`*        | 후치 증가 변수. `parameter = parameter + 1`와 동일하다(다음 논의를 참고하라). |
| *`parameter--`*        | 후치 감소 변수. `parameter = parameter - 1`와 동일하다.              |
| *`++parameter`*        | 전치 증가 변수. `parameter = parameter + 1`와 동일하다.              |
| *`--parameter`*        | 전치 감소 변수. `parameter = parameter - 1`와 동일하다.              |

이 대입 연산자는 기본 산술 작업을 간소화하여 편리함을 제공한다. 특히 흥미로운 것은 매개변수의 값을 각각 1씩 증감하는 증가(++), 감소(--) 연산자다. 이런 표기 방식은 C 프로그래밍 언어에서 가져온 것으로 bash를 포함한 여러 다른 언어들에 포함된 것이다.

이 연산자들은 매개변수 앞 또는 뒤에 놓일 수 있다. 이들은 둘 다 매개변수를 1 증가하거나 감소키시지만, 그 위치에 따라 미묘한 차이가 있다. 만약 매개변수 앞에 놓여 있다면, 매개변수는 매개변수가 반환되기 **전**에 증가한다(또는 감소한다). 만약 뒤에 위치한다면, 매개변수가 반환되 **후**에 연산은 수행된다. 이는 약간 이상해 보이지만, 의도된 동작이다. 여기에 그 예제가 있다.

```shell
┌──(kali㉿kali)-[~]
└─$ foo=1  
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo $((foo++))
1
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo $foo      
2
               치
```

변수 foo에 1을 대입하고 나서 매개변수명 뒤의 `++` 연산자로 증가시키면, foo 값은 1을 반환한다. 하지만 다시 한번 변수 값을 확인하면, 그 값이 증가된 것을 보게 된다. 만약 매개변수 앞에 `++` 연산자가 오면, 원래 예상하던 결과가 나올 것이다.
```shell
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ foo=1
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo $((++foo)) 
2
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo $foo      
2

```

대부분의 쉘 응용에서 전치 연산자는 가장 쓸모가 있다.

`++`과 `--` 연산자는 종종 반복문과 결합하여 사용된다. 우리는 모듈로 스크립트를 좀 더 줄이는 방향으로 수정할 것이다.

```shell
#!/bash/bash

# modulo2: demonstrate the modulo operator

for ((i = 0; i <= 20; ++i )); do
        if(((i % 5) == 0 )); then
                printf "<%d> " $i
        else
                printf "%d " $i
        fi
done
printf "\n"
                  
```


### 비트 연산

연산자 분류 중 비트 연산자는 특이한 방식으로 수를 조작하고 비트 단위로 작업한다. 또한 종종 설정이나 읽기 비트 플래그 포함하여 저수준 작업류에서 사용된다. [표 34-4]는 비트 연산자 목록이다.

- [표 34-4] 비트 연산자


| 연산자  | 설명                                      |
| ---- | --------------------------------------- |
| `~`  | 비트 부정. 수의 모든 비트를 부정 연산한다.               |
| `<<` | 왼쪽 비트 시프트. 수의 모든 비트를 왼쪽으로 이동한다.         |
| `>>` | 오른쪽 비트 시프트. 수의 모든 비트를 오른쪽으로 이동한다.       |
| `&`  | 비트 논리곱. 두 수의 모든 비트에 AND 연산을 수행한다.       |
| \|   | 비트 논리합. 두 수의 모든 비트에 OR 연산을 수행한다.        |
| `^`  | 비트 배타 논리합. 두 수의 모든 비트에 배타적 OR 연산을 수행한다. |


명심해야 할 점은 비트 부정을 제외하고 모든 비트 연산은 이에 상응하는 대입 연산자(예, `<<=`)도 있다는 것이다.

왼쪽 비트 시프트 연산자를 사용하여 2의 배수를 생성하는 예제를 살펴보자.

```shell
┌──(kali㉿kali)-[~]
└─$ for ((i=0;i<8;++i)) do echo $((1<<i)); done
1
2
4
8
16
32
64
128
```


### 논리 연산

27장에서 살펴본 것처럼, `(()) ` 합성 명령어는 다양한 비교 연산자를 제공한다. 여기에는 논리를 평가하기 위해 사용되는 것들이 좀 더 있다. [표 34-5]는 비교 연산자 목록을 보여준다.

- [표 34-5] 비교 연산자


| 연산자                  | 설명                                                               |
| -------------------- | ---------------------------------------------------------------- |
| `<=`                 | 보다 작거나 같은                                                        |
| `>=`                 | 보다 크거나 같은                                                        |
| `<`                  | 보다 작은                                                            |
| `>`                  | 보다 큰                                                             |
| `==`                 | 같은                                                               |
| `!=`                 | 같지 않은                                                            |
| `&&`                 | 논리 AND                                                           |
| \|\|                 | 논리 OR                                                            |
| `expr1? expr2:expr3` | 비교 (삼항)연산자. 만약 expr1 수식이 참(0이 아닌)이라면 expr2가, 아니면 expr3 수식이 실행된다. |

논리 연산자를 사용할 때, 수식은 다음과 같은 산술 논리 규칙을 따라야 한다. 수식어로 0으로 평가되면 거짓이고, 0이 아니면 참으로 간주된다. `(()) ` 합성 명령어는 그 결과를 쉘의 일반적인 종료 코드에 매핑된다.

```shell
┌──(kali㉿kali)-[~]
└─$ if ((1)); then echo "true"; else echo "false"; fi
true
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ if ((0)); then echo "true"; else echo "false"; fi
false
       
```

논리 연산자에서 가장 생소한 것은 **삼항 연산자**라는 것이다. 이 연산자(나중에 C 프로그래밍 언어의 그것에 모델이 된)는 단독으로 논리 테스트를 수행한다. `if/then/else`문의 한 부류로 사용될 수 있고, 세 산술식(문자열에는 동작하지 않는다)에 영향을 준다. 만약 첫 번째 식이 참(0이 아닌)이면, 두 번째 식은 수행되거나 세 번째 식이 수행된다. 커맨드라인에서 이를 수행할 수 있다.

```shell
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ a=0
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ ((a<1?++a:--a))
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo $a  
1
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ ((a<1?++a:--a))
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo $a
0

```

이 예제는 토글을 구현한 것으로, 실제 삼항 연산자를 볼 수 있다. 연산자가 수행될 때마다 변수 a 의 값은 0에서 1로 또는 그 반대로 바뀐다.

수식 내에서 값을 할당하는 것은 간단하지 않다. 이를 시도하면 bash는 오류를 표시할 것이다.

```shell
┌──(kali㉿kali)-[~]
└─$ a=0
┌──(kali㉿kali)-[~]
└─$ ((a<1?a+=1:a=-1))  
zsh: bad math expression: ':' expected
                       
```

이 문제는 대입식을 괄호로 감싸는 것으로 없앨 수 있다.

```shell

                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ ((a<1?(a+=1):(a=-1)))
```

다음은 간단한 숫자 표를 생성하는 스크립트에서 산술 연산자를 사용하는 좀 더 종합적인 예제다.

```shell
#!/bin/bash

# arith-loop: script to demonstrate arithmetic operators

finished=0
a=0
printf "a\ta**2\ta**3\n"

printf "=\t====\t====\n"


until ((finished)); do
        b=$((a**2))
        c=$((a**3))
        printf "%d\t%d\t%d\n" $a $b $c
        ((a<10?++a;(finished=1)))
done

```

 이 스크립트에서 finished 변수의 값을 기본으로 [[until]] 루프를 구현한다. 초기에 변수는 0(산술 거짓)으로 설정되고, 반복은 0이 아닌 값까지 계속된다. 루프 내에서 카운터 변수 a의 정사각형과 정육면체 넓이를 계산한다. ==루프의 끝부분에서 카운터 변수의 값은 평가된다.== 만약 10(최대 반복 수)보다 작으면 1이 증가하고, 아니면 변수 finished가 1로 변한다. finished는 참이 되고 루프는 종료된다. 스크립트를 실행하면 결과는 다음과 같다.

```shell
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ ./arith-loop  
a       a**2    a**3
=       ====    ====
0       0       0
1       1       1
2       4       8
3       9       27
4       16      64
5       25      125
6       36      216
7       49      343
8       64      512
9       81      729
10      100     1000
                        ```


---
# bc - 정밀 계산기 언어

- [[bc]]
