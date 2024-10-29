
가장 어려운 검증 작업 중 하나고, 날짜로 작업하는 셸 스크립트에서 중요한 것은 달력에서 특정 날짜가 실제로 존재하는지 확인하는 것이다. 윤년을 무시한다면, 달력은 매년 일관성이 있기 때문에 위 검증 작업은 그리 힘들지 않다. 이 경우 우리가 필요한 것은 단지 지정된 날짜를 비교하기 위한 최대 월일의 개수를 가진 테이블이다. 윤년을 고려하기 위해 스크립트에 몇 가지 로직을 추가해야 하는데, 이것이 약간 복잡하다.

주어진 해가 윤년인지 여부를 테스트하기 위한 규칙은 다음과 같다.

- 4로 나눌 수 없는 연도는 윤년이 아니다.
- 4로 나눌 수 있는 연도와 400으로 나눌 수 있는 연도는 윤년이다.
- 4로 나눌 수 있는 연도, 400으로 나눌 수는 없지만 100으로 나눌 수 있는 연도는 윤년이 아니다.
- 4로 나눌 수 있는 다른 모든 연도는 윤년이다.

[리스트 1-14]의 소스 코드를 검토하는 동안, 진행하기 전에 일관성 있는 날짜 형식을 보정하기 위해 이 스크립트에서 normdate를 활용하는 방법을 확인해보자.


---

### 코드


- [리스트 1-14]: valid-date 스크립트
``` shell
#!/bin/bash

# valid-date--윤년 규칙을 고려해, 날짜의 유효성을 검사한다.

  

# chapter1- 7

  

normdate="/home/kali/Documents/GitHub/learning_shell_script/bin/normdate.sh"

  

exceedDayInMonth(){

# 해당 달의 월 이름과 잘짜가 주어지면,

# 이 함수는 지정된 요일 값이 해당 월의 최대 요일보다 작거나 같으면 0,

# 그렇지 않으면 1을 리턴한다.

  

case $(echo $1|tr '[:upper:]' '[:lower:]') in # (#1)

jan* ) days=31 ;; feb* ) days=28 ;;

mar* ) days=31 ;; apr* ) days=30 ;;

may* ) days=31 ;; jun* ) days=30 ;;

jul* ) days=31 ;; aug* ) days=31 ;;

sep* ) days=30 ;; oct* ) days=31 ;;

nov* ) days=30 ;; dec* ) days=31 ;;

* ) echo "$0: Unknown month name $1" >&2

exit 1

esac

  
  

if [ $2 -lt 1 -o $2 -gt $days ]; then

return 1

else

return 0 # 날짜는 유효하다.

fi

}

  

isLeapYear(){

# 이 함수는 주어진 년도가 윤년이면 0, 그렇지 않으면 1을 리턴한다.

# 윤년인지 체크하는 공식은 다음과 같다.

# 1. 4로 나눌 수 없으면 윤년이 아니다.

# 2. 4와 400으로 나눌 수 있으면 윤년이다.

# 3. 4로 나눌 수 있다면, 400으로 나눌 수는 없지만

# 100으로 나눌 수 있는 연도는 윤년이 아니다.

# 4. 4로 나눌 수 있는 다른 모든 연도는 윤년이다.

  

year=$1

if [ "$((year % 4))" -ne 0 ]; then # (#2)

return 1 #Nope, not a leap year.

elif [ "$((year % 400))" -eq 0 ]; then

return 0 # Yes, it's a leap year.

elif [ "$((year % 100))" -eq 0 ]; then

return 1

else

return 0

fi

}

  

# 메인 스크립트 시작

# ================

  

if [ $# -ne 3 ]; then

echo "Usage: $0 month day year" >&2

echo "Typical input formats are August 3 1962 and 8 3 1962" >&2

exit 1

fi

  

# 날짜를 표준화하고 리턴값을 저장해 오류를 확인하자.

  

newdate="$($normdate "$@")" # (#3)

  

if [ $? -eq 1 ]; then

exit 1 # normdate에서 이미 보고한 오류 조건

fi

  

# 첫 번째 단어 = 월, 두 번째 단어 = 일, 세 번째 단어 =연도로

# 정규화된 날짜 형식을 분할한다.

  
  

month="$(echo $newdate | cut -d' ' -f1)"

day="$(echo $newdate | cut -d' ' -f2)"

year="$(echo $newdate | cut -d' ' -f3)"

  

# 이제 날짜가 정규화됐으므로,

# 날짜값이 적합하고 유효한지 확인해보자(예를 들면, 1월 36일은 유효하지 않다).

  
  

if ! exceedDayInMonth $month "$2"; then

if [ "$month" = "Feb" -a "$2" -eq "29" ] ; then

if ! isLeapYear $3 ; then

echo "$0: $3 is not a leap year, so Feb doesn't have 29 days." >&2 # (#4)

exit 1

fi

else

echo "$0: bad day value: $month doesn't have $2 days." >&2

exit 1

fi

fi

  

echo "Valid date: $newdate"

  

exit 0
```


---

### 동작 방식

이 코드는 월당 일수, 윤년 등에 대한 등에 대한 꽤 많은 테스트를 수행해야 하기 때문에 작성하기가 재미있는 스크립트다. 로직은 월=1-12, 일=1-31 등으로 지정하지 않는다. 구성(organization)을 위해 특정 함수를 사용해 작성하면 이해하기가 더 쉬워진다.

시작하려면 exceedsDaysInMonth()가 사용자가 입력한 달을 구분하는데, 분석이 매우 느슨하다(월 이름을 JANUAR라고 입력해도 괜찮다). 이것은 `(#1)`에서 인자를 소문자로 변환한 후, 값을 비교해 해당 달의 날짜를 확인하는 case문과 함께 수행된다. 이 방법은 동작하긴 하지만, 2월에는 항상 28일이라는 것을 가정한다.

윤년을 처리하기 위해 두 번째 함수 isLeapYear()는 몇 가지 기본 수학 테스트를 사용해 지정된 연도에 2월 29일 `(#2)`이 있었는지 여부를 확인한다.

메인 스크립트에서, 입력은 이전에 제시된 스크립트 normdate로 전달돼 입력 형식을 정규화`(#3)`한 후, `$field`, `$day`, `$year` 세 필드로 나눈다. 그런 다음, 함수 exceedDaysInMonth가 호출돼 특정 월(예를 들어, 9월 31일)에 유효하지 않은지 여부를 확인하고, 만약 사용자가 월을 2, 일을 29로 지정하면 특별한 조건이 트리거된다. 해당 연도에 대해 isLeapYear로 테스트 `(#4)`되고, 적절한 오류가 발생한다. 사용자가 입력한 날짜가 이 테스트를 모두 통과하면 유효하다.


---

### 스크립트 실행하기

스크립트를 실행하려면 ([리스트 1-15] 참조), 명령 행에 월-일-연도 형식으로 날짜를 입력한다. 월은 3자로 된 약어, 전체 단어 또는 숫자값이 될 수 있다. 연도는 네 자리여야 한다.


### 결과

```shell
 kali@kali  ~/Documents/GitHub/learning_shell_script/bin   main ±  valid-date august 3 1960
Valid date: Aug 3 1960

 kali@kali  ~/Documents/GitHub/learning_shell_script/bin   main ±  valid-date 9 31 2001    
/home/kali/Documents/GitHub/learning_shell_script/bin/valid-date: bad day value: Sep doesn't have 31 days.

 ✘ kali@kali  ~/Documents/GitHub/learning_shell_script/bin   main ±  valid-date feb 29 2004
Valid date: Feb 29 2004

 kali@kali  ~/Documents/GitHub/learning_shell_script/bin   main ±  valid-date feb 29 2014
/home/kali/Documents/GitHub/learning_shell_script/bin/valid-date: 2014 is not a leap year, so Feb doesn't have 29 days.

 ✘ kali@kali  ~/Documents/GitHub/learning_shell_script/bin   main ±  

```


---

### 스크립트 해킹하기

이 스크립트에 대한 비슷한 접근 방식으로 24시간제 또는 AM/PM(ante meridiem/ post meridiem) 접미사를 사용해 시간 스펙을 검증할 수 있다. 콜론으로 값을 분할하고 분과 초(지정된 경우)가 0과 60 사이에 있는지 확인한 후, AM/PM을 허용하는 경우 첫 번째 값이 0과 12 사이인지 확인한다. 만약, 24시간제를 선호하는 경우에는 0에서 24 사이의 값인지 확인한다. 다행스럽게도 달력을 균형 있게 유지할 수 있게 도와주는 윤년 및 기타 작은 변형이 있지만, 일상적으로 이를 무시할 수 있으므로 이러한 귀찮은 시간계산을 구현하는데 초조해할 필요는 없다. 유닉스나 또는 GNU/리눅스 구현에서 GNU date에 접근할 수 있다면, 윤년을 테스트하는 완전히 다른 방법이 있다. 다음 명령어를 입력하고 결과를 확인해 테스트해본다.

```shell
$ date -d 12/31/1996 +%j
```

새롭고 더 나은 버전의 date 368쪽에서 볼 수 있다. 이전 버전에서 입력 형식에 대해 불만이 있었다. 이제 새로운 명령어의 결과값을 확인해보고, 주어진 연도가 윤년인지 여부를 테스트하는 두 줄 함수를 찾을 수 있는지 확인해보자.

마지막으로, 이 스크립트는 월 이름에 대해 상당히 관대하다. febmama는 [[case]] 문이 특정 단어의 세 글자만 검사하기 때문에 아무런 문제가 없다. 완전히 철자가 지정된 월 이름(february), 일반적인 맞춤법 오류(febuary)와 함께 일반 약어(예: feb)를 테스트하려는 경우, 이 방법을 정리하고 향상시킬 수 있다. 하려고 하는 의지만 있다면, 모든 것이 쉽게 끝날 수 있을 것이다.