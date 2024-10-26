
63쪽의 스크립트 `(#2)`에서 본 것처럼 정수 입력의 유효성 검사는 손쉬운 것으로 보이고, 음수 값 또는 허용되기를 원한다. 문제는 각 숫자값이 음수 기호 하나만 가질 수 있다는 것이며, 이 음수 기호는 값의 맨 처음에 와야 한다는 것이다. [리스트 1-9]의 유효성 검사 루틴은 음수가 올바른 형식인지 확인하고, 보다 일반적으로 사용할 수 있게 해당 값이 사용자가 지정한 범위 내에 있는지 여부도 확인할 수 있다.

- [리스트 1-9]: validint 스크립트

```shell
#!/bin/bash

# validint--음수인 정수를 허용하면서 정수 입력을 검증

  
  

validint(){

  

# 첫 번째 필드의 유효성을 검사하고 최솟값 $ 2

# 그리고 / 또는 최댓값 $3이 제공되면 해당 값을 테스트한다.

# 값이 범위 내가 아니거나 숫자만으로 구성되지 않은 경우에는 실패한다.

  

number="$1"; min="$2"; max="$3"

  

if [ -z $number ] ; then # 1

echo "You didn't enter anything. Please enter a number" >&2

return 1

fi

  
  

if [ "${number%${number#?}}" = "-" ]; then # 2

testvalue="${number#?}" # Grab all but the first character to test.

else

testvalue="$number"

fi

  

# 테스트용으로 숫자가 없는 버전을 만든다.

nodigits="$(echo $testvalue| sed 's/[[:digit:]]//g')" # 3

  
  

# nondigit 문자를 체크한다.

if [ ! -z $nodigits ] ; then

echo "Invalid number format! Only digits, no commas, spaces etc." >&2

return 1

fi

  

if [ ! -z $min ]; then

# 입력이 최솟값보다 작은가?

if [ "$number" -lt "$min" ]; then

echo "Your value is too small: smallest acceptable value is $min." >&2

return 1

fi

fi

  

if [ ! -z $max ]; then

# 입력이 최댓값보다 더 큰가?

if [ "$number" -gt "$max" ]; then

echo "Your value is too big: largest acceptable value is $max." >&2

return 1

fi

fi

  

return 0

}
```


---
### 동작 방식

정수의 유효성 검사는 값의 유효 범위가 일련의 숫자(0~9)거나 맨 앞 하나만 있는 음수 기호기 때문에 매우 간단하다. `validint()` 함수가 최솟값이만 최댓값으로 호출되거나 둘 다로 호출되면, 입력값이 범위 내에 있는지 확인하기 위한 유효성 검사도 수행한다.

이 함수는 `(#1)`에서 사용자가 입력했음을 보장한다. 여기에서 오류 메시지가 생성되지 않도록 따옴표를 사용해 빈 문자열의 가능성이 있음을 예측하는 것이 중요하다. 그런 다음, `(#2)`에서 음수 기호를 찾고, `(#3)`에서 모든 자릿수가 제거된 입력된 값의 버전값을 만든다. 이 값의 길이가 0이 아니면 문제가 발생한 것이고, 테스트가 실패한다.

값이 유효한 경우, 사용자가 입력한 숫자와 최솟값 몇 최댓값`(#4)`이 비교된다. 마지막으로 함수는 오류가 발생하면 1, 성공하면 0을 리턴한다.


---
### 스크립트 실행하기

이 전체 스크립트는 다른 셸 스크립트로 복사하거나 라이브러리 파일에 포함할 수 있는 함수다. 이를 명령어로 바꾸려면, [리스트 1-10]의 코드를 스크립트 맨 아래에 추가하면 된다.

```shell
# 입력 유효성 검사

if validint "$1" "$2" "$3" ; then

echo "Input is a valid integer within your constraints."

fi
```

---
### 결과


스크립트에 [리스트 1-10]을 배치한 후, 코드 1-11과 같이 사용할 수 있어야 한다.

- [리스트 1-11]: validint 스크립트 테스트

```shell
 kali@kali  ~/Documents/GitHub/learning_shell_script/bin   validint 1234.3
Invalid number format! Only digits, no commas, spaces etc.

 kali@kali  ~/Documents/GitHub/learning_shell_script/bin  validint 103 1 100
Your value is too big: largest acceptable value is 100.

 kali@kali  ~/Documents/GitHub/learning_shell_script/bin  validint -17 0 25 
Your value is too small: smallest acceptable value is 0.

 kali@kali  ~/Documents/GitHub/learning_shell_script/bin  validint -17 -20 25
Input is a valid integer within your constraints.
```


---

### 스크립트 해킹하기 

`(#2) `의 테스트가 첫 번째 문자가 음수인지, 아닌지를 확인한다.

```shell
 if [ "${number%${number#?}}" = "-"] ; then
```

첫 번째 문자가 음수이면 testvalue에 정수값의 문자 부분이 할당된다. 그런 다음, 이 음수가 아닌 값은 숫자를 제거하고 더 테스트한다.

AND(`-a`)를 사용해 표현식을 연결하고 중첩된 [[chapter27 흐름 제어 if 분기|if]]문을 축소할 수 있다. 예를 들어, 다음 코드는 동작하는 것 처럼 보인다.

```shell
if [ ! -z $min -a "$number" -lt "$min" ]; then
	echo "Your value is too small: smallest acceptable value is $min." >&2
	exit 1
fi
```

그러나 그렇지 않다. 왜냐하면 AND의 첫 번째 조건이 거짓이라고 해서 두 번째 조건 또한 테스트되지 않을 것이라는 보장은 할 수 없다(대부분의 다른 프로그래밍 언어와는 다르다). 즉 이 방법을 사용하면 유효하지 않은, 예상치 못한 비교값으로 인해 모든 종류의 버그를 경험하게 된다. 그렇게 해서는 안되지만, 이것이 쉘 스크립트다.



