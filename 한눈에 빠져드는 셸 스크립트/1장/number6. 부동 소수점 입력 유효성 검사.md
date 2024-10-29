
언뜻 보기에 셸 스크립트의 제약과 기능 내에서 부동 소수점(또는 "실제")값의 유효성을 검사하는 과정은 힘든 것으로 보일 수 있지만, 부동 소수점은 숫자는 소수점으로 구분된 2개의 정수로 간주된다. 다른 스크립트 인라인(validint)을 참조할 수 있는 능력은 무엇이고, 부동 소수점 유효성 검증이 놀라울 정도로 짧을 수 있음을 알 수 있다. [리스트 1-12]의 스크립트 `validint` 스크립트와 동일한 디렉터리에서 실행되고 있다고 가정한다.



---
### 코드

- [리스트 1-12]
``` shell
#!/bin/bash


# chapter1-5

  

# validfloat--숫자가 유효한 부동 소수점 값인지 테스트한다.
# 이 스크립트는 지수 표기법(1.304e5)을 허용하지 않는다.


# 입력된 값이 부동 소수점 숫자인지 테스트하려면,
# 정수 부분과 분수 부분의 두 부분으로 값을 분할해야 한다.
# 첫 번째 부분을 테스트해 유효한 정수인지 확인한 후
# 두 번째 유효한 >= 0 정수인지의 여부를 테스트한다.
# 그래서 -30.5는 유효한 것으로 확인되지만, -30.8은 그렇지 않다.

  

# "." 소스 명령어를 사용해 다른 셸 스크립트를 포함시킨다.

# 아주 쉽다.

  

. validint

validfloat()
{

fvalue="$1"
 

# 입력된 숫자에 소수점이 있는지 확인한다.
if [ ! -z $(echo $fvalue | sed 's/[^.]//g' ) ] ; then # 1

# 소수점 앞부분을 추출.
decimalPart="$(echo $fvalue | cat -d. -f1)" # 2

# 소수점 뒤의 숫자들을 추출.
fractionalPart="${fvalue#*\.}" # 3

  

# 소수점 왼쪽에 있는 모든 부분인 소수 부분을 테스트해 시작한다.
if [ ! -z $decimalPart ] ; then # 4

# "!"은 테스트 로직을 반대로 한다.
# 그래서 다음은 "유효한 정수가 아니라면"이다.

if ! validint "$decimalPart" "" "" ; then
return 1
fi
fi

  

# 이제 분수 값을 테스트해보자.

  

# 시작하려면 33.-11과 같이 소수점 뒤에 음수 부호를 사용할 수 없으므로

# 소수점의 '-' 기호를 테스트해보자.

  

if [ "${fractionalPart%${fractionalPart#?}}" = "-" ]; then # 5
echo "Invalid floating-point number: '-' not allowed \
after decimal point." >&2
return 1
fi
if [ "$fractionalPart" != "" ] ; then
# 만약 분수 부분이 유효한 정수가 아니라면...
if ! validint "$fractionalPart" "0" "" ; then
return 1
fi
fi

  

else
# 전체 값이 "-"인 경우에도 유효한 값이 아니다.
if [ "$fvalue" = "-" ] ; then # 6
echo "Invalid floating-point format." >&2
return 1
fi
# 마지막으로, 나머지 자릿수가 실제로 정수로 유효한지 확인하자.

if ! validint "$fvalue" "" ""; then
return 1
fi
fi

  

return 0

  

}
```


---

### 동작 방식

스크립트는 먼저 입력값에 소수점`(#1)`이 있는지 확인한다. 그렇지 않으면 부동 소수점 숫자가 아니다. 다음으로, 값의 정수`(#2)` 및 소수 `(#3) `부분을 분석하기 위해 잘라낸다. 그런 다음, `(#4)`에서 스크립트는 정수 부분(소수점의 왼쪽에 있는 숫자)이 유효한 정수인지 확인한다. 다음 시퀀스는 좀 더 복잡한데, 왜냐하면 `(#5) `에러 다른 음수 부호(예를 들어, 17 -30과 같은 기이한 숫자를 피하기 위해)를 확인해야 하기 때문이다. 그리고 다시 소수 부분(소수점의 오른쪽에 있는 숫자)이 유효한 정수인지 확인한다.
마지막 체크`(#6)`는 사용자가 단지 음수 기호와 소수점을 지정했는지 여부를 체크한다(매우 특이한 경우지만, 이런 경우도 있을 수 있다).

문제가 없으면 스크립트는 0을 반환해 사용자가 유요한 float를 입력했음을 알려준다.


---
### 스크립트 실행하기

함수가 호출될 대 오류가 나지 않는다면 리턴 코드는 0, 지정된 숫자는 유효한 부동 소수점 값이다. 코드 끝에 다음 몇 줄을 추가해 이 스크립트를 테스트할 수 있다.


```shell
#!/bin/bash

  
  

# chapter1-5

  

# validfloat--숫자가 유효한 부동 소수점 값인지 테스트한다.

# 이 스크립트는 지수 표기법(1.304e5)을 허용하지 않는다.

  

# 입력된 값이 부동 소수점 숫자인지 테스트하려면,

# 정수 부분과 분수 부분의 두 부분으로 값을 분할해야 한다.

# 첫 번째 부분을 테스트해 유효한 정수인지 확인한 후

# 두 번째 유효한 >= 0 정수인지의 여부를 테스트한다.

# 그래서 -30.5는 유효한 것으로 확인되지만, -30.8은 그렇지 않다.

  

# "." 소스 명령어를 사용해 다른 셸 스크립트를 포함시킨다.

# 아주 쉽다.

  

. validint

  

validfloat()

{

fvalue="$1"

  

# 입력된 숫자에 소수점이 있는지 확인한다.

if [ ! -z $(echo $fvalue | sed 's/[^.]//g' ) ] ; then # 1

  

# 소수점 앞부분을 추출.

decimalPart="$(echo $fvalue | cat -d. -f1)" # 2

  

# 소수점 뒤의 숫자들을 추출.

fractionalPart="${fvalue#*\.}" # 3

  

# 소수점 왼쪽에 있는 모든 부분인 소수 부분을 테스트해 시작한다.

  

if [ ! -z $decimalPart ] ; then # 4

# "!"은 테스트 로직을 반대로 한다.

# 그래서 다음은 "유효한 정수가 아니라면"이다.

if ! validint "$decimalPart" "" "" ; then

return 1

fi

fi

  

# 이제 분수 값을 테스트해보자.

  

# 시작하려면 33.-11과 같이 소수점 뒤에 음수 부호를 사용할 수 없으므로

# 소수점의 '-' 기호를 테스트해보자.

  

if [ "${fractionalPart%${fractionalPart#?}}" = "-" ]; then # 5

echo "Invalid floating-point number: '-' not allowed \

after decimal point." >&2

return 1

fi

if [ "$fractionalPart" != "" ] ; then

# 만약 분수 부분이 유효한 정수가 아니라면...

if ! validint "$fractionalPart" "0" "" ; then

return 1

fi

fi

  

else

# 전체 값이 "-"인 경우에도 유효한 값이 아니다.

if [ "$fvalue" = "-" ] ; then # 6

echo "Invalid floating-point format." >&2

return 1

fi

  

# 마지막으로, 나머지 자릿수가 실제로 정수로 유효한지 확인하자.

if ! validint "$fvalue" "" ""; then

return 1

fi

fi

  

return 0

  

}

  

if validint $1 ; then

echo "$1 is a valid floating-point value."

fi

exit 0
```


---

### 동작 방식

스크립트는 먼저 입력값에 소수점 `(#1)`이 있는지 확인한다. 그렇지 않으면 부동 소수점 숫자가 아니다. 다음으로, 값의 정수 `(#2)` 및 소수 `(#3)` 부분을 분석하기 위해 잘라낸다. 그런 다음, `(#4)`에서 스트립트는 정수 부분(소수점의 왼쪽에 있는 숫자)이 유효한 정수인지 확인한다. 다음 시퀀스는 매우 복잡한데, 왜냐하면 `(#5)`에서 다음 음수 부호(예를 들어, 17. -31과 같은 기이한 숫자를 피하기 위해)를 확인해야 하기 때문이다. 그리고 다시 소수 부분(소수점의 오른쪽에 있는 숫자)이 유효한 정수인지 확인한다.

마지막 체크 `(#6)`는 사용자가 단지 음수 기호와 소수점을 지정했는지 여부를 체크한다(매우 특이한 경우지만, 이런 경우도 있을 수 있다).

문제가 없으면 스크립트는 0을 반환해 사용자가 유효한 float를 입력했음을 알려준다.


---

### 스크립트 실행하기

함수가 호출될 때 오류가 나지 않는다면 리턴 코드는 0, 지정된 숫자는 유효한 부동 소수점 값이다. 코드 끝에 다음 몇 줄을 추가해 이 스크립트를 테스트할 수 있다.

``` shell

if validfloat $1 ; then
	echo "$1 is a valid floating-point value."
fi

exit 0
```

---

### 결과

validfloat 셸 스크립트는 유효성을 검사하기 위해 인자가 필요하다. [리스트 1-13]은 validfloat 스크립트를 사용해 몇 개의 입력을 검증한다.

- [리스트 1-13]: validfloat 스크립트 테스트
```shell

 kali@kali  ~/Documents/GitHub/learning_shell_script/bin   validfloat 1234.56
Invalid number format! Only digits, no commas, spaces etc.
 kali@kali  ~/Documents/GitHub/learning_shell_script/bin  validfloat -1234.56
Invalid number format! Only digits, no commas, spaces etc.

 kali@kali  ~/Documents/GitHub/learning_shell_script/bin   validfloat -.75    
Invalid number format! Only digits, no commas, spaces etc.
 kali@kali  ~/Documents/GitHub/learning_shell_script/bin   validfloat -11.-12 
Invalid number format! Only digits, no commas, spaces etc.
 kali@kali  ~/Documents/GitHub/learning_shell_script/bin   validfloat 1.0344e22
Invalid number format! Only digits, no commas, spaces etc.

```

여기서 추가로 다른 출력이 보이면, 이전에 validint를 테스트하기 위해 몇 줄을 추가했으나 이 스크립트에서 추가된 것을 제거하지 않았기 때문일 수 있다. 간단히 75쪽의 스크립트 `#5`로 돌아가 기능을 자립 실행형으로 실행할 수 있도록 해주는 마지막 몇 줄을 주석 처리하거나 삭제했는지 확인한다.


---

### 스크립트 해킹하기

추가로 보완할 점은 마지막 예제에서 설명한 것처럼 이 기능을 확장해 지수 표기법을 허용하는 것이다. 너무 어렵지는 않을 것이다. 'e' 또는 'E'의 존재 여부를 테스트한 후, 그 결과를 소수 부분(항상 한 자릿수), 분수 부분 및 10의 거듭제곱의 세 부분으로 나눈다. 각각 유효한지를 확인하면 된다.

소수점 앞에 선행하는 0을 필요로 하지 않으면, [리스트 1-12] `(#6)`에서 조건 테스트를 수정할 수 있다. 그러나 이상한 형식에는 주의해야 한다.