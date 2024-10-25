프로그래머가 공통적으로 자주 범하는 실수는 계산 결과를 먼저 서식으로 지정하지 않고 사용자에게 제시하는 것이다. 사용자가 43245435를 오른쪽에서 왼쪽으로 세어 계산하거나 세 자리마다 쉼표를 삽입하지 않고 수백만 일 것이라고 예측하기는 어렵다. [리스트 1-7]의 스크립트는 숫자를 멋진 형태로 만든다.


### 코드

 - [리스트 1-7]: 큰 숫자를 좀 더 읽기 쉽게 만들어주기 위한 nicenumber 스크립트
 
```shell
#!/bin/bash

#nicenumber--주어진 숫자는 쉼표로 구분된 형식으로 표시된다.

# (decimal point delimiter - 소수점) 및 TD(thousands delimiter - 천단위 구분

# 기호)가 인스턴스화 될 것으로 예상된다.

# nicenum을 인스턴스화하거나 두 번째 arg가 지정되면 출력이 stdout으로 보내진다.

  

nicenumber(){

# 스크립트에서 '.'는 INPUT 값의 소수점 구분 기호다.

# 사용자가 -d 플래그로 지정하지 않는 한 출력값 소수점 구분 기호는 '.'이다.

  

integer=$(echo $1 | cut -d. -f1) # 소수점의 왼쪽 # 1

decimal=$(echo $1 | cut -d. -f2) # 소수점의 오른쪽 # 2

  

# 숫자에 정수 부분 이상이 있는지 확인한다.

if [ "$decimal" != "$1" ]; then

# 분수 부분이 있으므로 포함시킨다.

result="${DD:='.'}$decimal"

fi

  

thousands=$integer

  

while [ $thousands -gt 999 ]; do # 3

remainder=$(($thousands % 1000)) # 4 # 세 자리 최하위 숫자.

  

# 세 자리 숫자를 위해 'remainder'가 필요. 0을 추가하길 원하는가?

while [ ${#remainder} -lt 3 ]; do # 앞에 0을 붙인다.

remainder="0$remainder"

done

  

result="${TD:=","}${remainder}${result}" # 5

thousands=$(($thousands/1000)) # 6 # reminder의 왼쪽으로

done

  

nicenum="${thousands}${result}"

if [ ! -z $2 ]; then

echo $nicenum

fi

}

  

DD="." # 전체 및 소수값 구분하는 소수점 구분 기호

TD="," # 매 세 자리를 구분하는 천 단위 구분 기호

  

# 메인 스크립트 시작

# =====================

  

while getopt "d:t:" opt; do # 7

case $opt in

d ) DD="$OPTARG" ;;

t ) TD="$OPTARG" ;;

esac

done

  

shift $(($OPTIND - 1))

  

# 입력 유효성 검사

  

if [ $# -eq 0 ]; then

echo "Usage: $(basename $0) [-d c] [-t c] number"

echo " -d specifies the decimal point delimiter"

echo " -t specifies the thousands delimiter"

exit 0

fi

  

nicenumber $1 # 8 # 두 번째 인자는 nicenumber를 'echo'로 출력한다.

  

exit 0
```


### 동작 방식

이 스크립트의 핵심은 nicenumber() 함수 안의 while 루프다 `(#3)`. 이는 변수 thousand`(#4)`에 저장된 숫자값에서 3개의 최하위 숫자를 반복적으로 제거하고, 자릿수를 붙여 숫자들이 누적되면서 예쁜 모양이 된다 `(#5)`. 그런 다음, 루프는 thousands`(#6)`에 저장된 수를 줄이고, 필요한 경우 인자로 넘겨 다시 시작한다. 일단 nicenumber() 함수가 완료되면, 메인 스크립트 로직이 시작된다. 먼저 getopt`(#7)`로 스크립트에 전달된 모든 옵션을 구문 분석한 후, 마지막으로 사용자가 지정한 인자로 `nicenumber()` 함수`(#8)`를 호출한다.

---
### 스크립트 실행하기

간단하게 이 스크립트를 실행하려면 매우 큰 숫자값을 지정하면 된다. 스크립트는 기본값을 사용하거나 플래그를 통해 지정된 문자를 사용해 필요에 따라 소수점과 구분 기호를 추가한다. 결과는 다음에서 보여주는 것처럼 출력 메시지 내에 통합될 수 있다.

```shell
echo "Do you really want to pay \$$(nicenumber $price)"
```


---

### 결과

nicenumber 셸 스크립트는 사용하기 쉽지만,몇 가지 고급 옵션을 사용할 수도 있다. [리스트 1-8]은 스크립트를 사용해 몇 개의 숫자를 세팅하는 보여준다. 

```shell

```