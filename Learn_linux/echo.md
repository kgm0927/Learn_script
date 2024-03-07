

---

# 확장


명령어를 입력하고 엔터키를 누르면 hash는 그 명령어를 수행하기 전에 텍스트에 몇 가지 프로세스를 진행한다. 예를 들면 * 기호처럼 쉘에 여러 의미를 주는 경우, 단순히 연속된 문자열로 처리되는 것과 같은 몇 가지 경우를 살펴보았다. 이러한 프로세스를 **확장**이라고 하는데, 이 기능으로 인해 무엇이든 입력하면 쉘이 그것을 처리하기 전에 다른 무언가로 확장된다. 이것이 정확하게 의미하는 바를 알아보려면 echo 명령어를 살펴보아야 한다.

``` shell
kgm0917@DESKTOP-DT2VQLB:~$ echo this is a test

this is a test
```

꽤 직관적이다. 어떠한 명령 인자라도 echo 명령어에 의해 표시된다.


```shell
kgm0917@DESKTOP-DT2VQLB:~$ echo *

lazy_dog.txt ls-error.txt ls-output.txt ls.txt movie.mpeg
```

와일드카드를 떠올러보면,`*` 기호는 “파일명에 있는 어떤 글자도 해당된다”라는 것이다. 그러나 본문에서 우리가 확인하지 않았던 것은 어떻게 쉘이 그것을 이해하고 수행하는가이다. 간단한 해답은 바로 쉘이 echo 명령어가 실행되기 전에 `*` 기호를 다른 무언가로 확장시킨다는 것이다.

엔터키를 눌렀을 때, 쉘은 자동적으로 명령어가 실행되기 직전에 모든 한정 문자들을 확장시킨다. 따라서 echo 명령어는 `*` 기호 자체를 출력하지 않고 그 확장된 결과만 보여준다.


- 경로명 확장


경로명 확장: 와일드카드로 동작하는 방식을 의미

(원래 밑의 예제는 홈 디렉토리에서 진행한다. 하지만 내 우분투에서는 없기에 그냥 root 디렉토리에서 진행해서 다른 결과를 만들 것이다.)

```
kgm0917@DESKTOP-DT2VQLB:/$ ls

bin dev home lib lib64 media opt root sbin srv tmp var

boot etc init lib32 libx32 mnt proc run snap sys usr
```
같은 루트 디렉토리가 있다.

다음과 같이 확장이 가능하다.


```shell
kgm0917@DESKTOP-DT2VQLB:/$ echo b*

bin boot
```

그리고

```
kgm0917@DESKTOP-DT2VQLB:/$ echo s*

sbin snap srv sys
```

또는 

``` shell
kgm0917@DESKTOP-DT2VQLB:/$ echo [[:upper:]]*

[[:upper:]]*
```

(여기서는 해당이 되지 않기 때문에 그냥 자기 자신을 출력한다.)

홈 디렉토리를 벗어난 다른 디렉토리도 살펴볼 수 있다.

``` shell
kgm0917@DESKTOP-DT2VQLB:~$ echo /usr/*/share

/usr/local/share
```


- ***==부연 설명==***

숨김 파일의 경로명 확장

마침표로 시작하는 파일명이 있다면 그것은 숨겨진 파일이다. 경로명 확장 또한 이러한 숨겨진 파일의 규칙을 따른다. 다음과 같은 확장 명령으로는 숨김 파일을 가져올 수 없다.

`echo *`

언뜻 보기에는 다음과 같이 마침표를 앞에 두고 확장을 하면 파일도 볼 수 있을 것 같을 것이다.

`echo *
`
거의 비슷하다. 하지만 자세히 보면 파일명에 .과 ..이 포함된 파일까지도 결과로 출력될 것이다. 이 이름들이 의미하는 것의 현재 작업 디렉토리와 그 상위 디렉토리이기에 이러한 패턴을 이용하는 것은 정확한 결과를 가져오지 못한다.

`ls –d .* |less
`
이러한 경우 올바른 경로명 확장을 수행하기 위해서 보다 특별한 패턴을 적용해야 한다. 다음 명령어를 하면 올바로 수행된다.

`ls –d .[!.]?*`

이 패턴은 마침표로 시작하는 모든 파일명으로 확장시키면, 두 번째 마침표는 포함시키지 않으면서 추가로 하나 이상의 문자가 있어야 하면 그 뒤로 어떠한 문자도 올 수 있다는 것을 나타낸다.


- 틸드 (~) 확장

cd 명령어를 다시 보자면, ~(물결표) 기호 문자는 특별한 의미를 가지고 있다. 이 기호가 맨 앞에 있다면, 지정된 사용자의 홈 드렉토리명을 나타내고,


``` shell
kgm0917@DESKTOP-DT2VQLB:/home$ ~ echo

-bash: /home/kgm0917: Is a directory
```

이름을 지정하지 않으면 현재 사용자의 홈 디렉토리명을 나타낸다.

``` shell
kgm0917@DESKTOP-DT2VQLB:/home$ echo ~

/home/kgm0917
```

- 산술 확장


쉘에서는 산술식 확장이 가능하다. 쉘 프롬프트를 계산기처럼 쓸 수 있다.

``` shell
kgm0917@DESKTOP-DT2VQLB:/home$ echo $((2+2))

4
```

다음과 같이 나타낸다.

*$((expression))*


**산술식**에는 산술 연산자가 포함될 수 있다.


산술 확장에서는 정수만 사용할 수 있지만 꽤 다양하게 연산을 할 수 있다.


[[표 7-1]] 산술 연산자

| 연산자 | 설명          |
| --- | ----------- |
| +   | 더하기         |
| -   | 빼기          |
| *   | 곱하기         |
| /   | 나누기(정수만 가능) |
| %   | 모듈로, 나머지 반환 |
| **  | 거듭제곱        |

산술식에서 공백은 중요하지 않으며, 중첩될 수 있다.

``` shell
kgm0917@DESKTOP-DT2VQLB:/home$ echo $(($((5**2))*3))

75
```

나누기와 나머지 연산자를 활용한 예제가 있다. 정수의 나눗셈 결과를 확인해보자.


``` shell
kgm0917@DESKTOP-DT2VQLB:/home$ echo Five divided by two equals $((5/2))

Five divided by two equals 2

kgm0917@DESKTOP-DT2VQLB:/home$ echo with $((5%2)) left over.

with 1 left over.
```





- 중괄호 확장

가장 어려운 개념일 것이다. 중괄호 안에 표현된 패턴과 일치하는 다양한 텍스트 문자열을 만들 수 있다.


```
kgm0917@DESKTOP-DT2VQLB:/home$ echo Front -{A,B,C}-Back

Front -A-Back –B-Back -C-Back
```

중괄호에 의해 확장된 패턴은 **프리앰블**(preamble)이라고 부르는 앞부분과 **포스트스크립트**(postscript)라는 끝부분을 가진다. 중괄호 표현식은 그 자체가 쉼표로 구분된 문자열을 표현하거나 정수나 문자 범위를 표현할 수 있다. 그리고 이런 패턴에는 빈칸이 허용되지 않는다.

``` shell
kgm0917@DESKTOP-DT2VQLB:/home$ echo Number_{1..5}

Number_1 Number_2 Number_3 Number_4 Number_5
```


역순으로 된 알파벳 결과도 보여주고 있다.


``` shell
kgm0917@DESKTOP-DT2VQLB:/home$ echo {Z..A}

Z Y X W V U T S R Q P O N M L K J I H G F E D C B A
```

괄호 확장식도 중첩될 수 있다.

``` shell
kgm0917@DESKTOP-DT2VQLB:/home$ echo a{A{1,2},B{3,4}}b

aA1b aA2b aB3b aB4b
```


대부분 일반적인 어플리케이션은 파일 및 디렉토리 목록을 생성한다. 예를 들면, 사진을 연도별 또는 월별로 정리하려는 방대한 양의 사진이 있을 시 첫 번째로 연도-월 형식의 디렉토리를 만든느 것이다. 이런 식으로 디렉토리명들을 시간 순서대로 정리하는 것이다.

이런 방법도 가능하다.

```
kgm0917@DESKTOP-DT2VQLB:~$ mkdir Pics

kgm0917@DESKTOP-DT2VQLB:~$ cd Pics

kgm0917@DESKTOP-DT2VQLB:~/Pics$ mkdir {2009..2011}-0{1..9} {2009..2011}-{10..12}

kgm0917@DESKTOP-DT2VQLB:~/Pics$ ls

2009-01 2009-06 2009-11 2010-04 2010-09 2011-02 2011-07 2011-12

2009-02 2009-07 2009-12 2010-05 2010-10 2011-03 2011-08

2009-03 2009-08 2010-01 2010-06 2010-11 2011-04 2011-09

2009-04 2009-09 2010-02 2010-07 2010-12 2011-05 2011-10


2009-05 2009-10 2010-03 2010-08 2011-01 2011-06 2011-11

```


- 매개변수 확장

사실 커맨드라인보다 쉘 스크립트에서 더 유용한 기능이 매개변수 확장이다. 이 기능은 작은 데이터 덩어리를 저장하고 각 덩어리마다 이름을 붙이는 시스템 기능과 함께 사용할 게 더 많은 능력을 발휘할 수 있다. 이러한 덩어리를 더 정확하게 표현하면 **변수**다. 각 변수들은 다음 예제처럼 확인할 수 있다.

예를 들어 USER 라는 변수는 사용자명을 가지고 있는데, 매개변수 확장으로 USER 내용을 볼 수 있다.

``` shell
kgm0917@DESKTOP-DT2VQLB:~/Pics$ echo $USER

kgm0917
```

사용 가능한 변수 목록을 보기 위해 다음과 같이 입력할 수 있다.

``` shell
kgm0917@DESKTOP-DT2VQLB:~/Pics$ printenv | less
```


여기서 다른 형식의 확장과 함께 사용했을 경우, 패턴을 잘못 입력하면 그 확장은 작동하지 않고 echo 명령어는 단순히 잘못된 패턴을 출력한다. 매개변수 확장으로는 변수명을 잘못 입력하면 확장은 되지만, 빈 문자열을 반환한다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ echo $USER

kgm0917@DESKTOP-DT2VQLB:~$
```


- 명령어 치환

명령어 치환으로 명령어의 출력 결과를 확장으로 사용할 수 있다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ echo $(ls)

Pics lazy_dog.txt ls-error.txt ls-output.txt ls.txt movie.mpeg
```

이것은 약간 다른 방법이다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ ls -l $(which cp)

-rwxr-xr-x 1 root root 141824 Feb 8 2022 /usr/bin/cp
```


ls 명령어 인자로 **which cp** 결과를 사용했음을 알 수 있다. 이렇게 하면 경로명 전체를 알지 못해도 cp 프로그램의 내용은 볼 수 있다.

이는 단순히 명령어에만 국한되지 않고 파이프라인 전체에서 사용될 수 있다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ file $(ls /usr/bin/* | grep zip)

/usr/bin/gpg-zip: POSIX shell script, ASCII text executable

/usr/bin/gunzip: POSIX shell script, ASCII text executable

/usr/bin/gzip: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=a7668faa2322e181773d5cba4bc5d8fd41e9b7c9, for GNU/Linux 3.2.0, stripped

/usr/bin/streamzip: Perl script text executable

/usr/bin/zipdetails: Perl script text executable
```


이 예제는 파이프라인 결과가 file 명령어의 명령 인자로 쓰였다.

bash나 예전 쉘 프로그램에서는 명령어를 치환하는 다른 문법이 있다. $ 기호나 괄호를 사용하는 대신 작은 따옴표를 사용한다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ ls -l 'which cp’
```

(이 방법은 우분투에서 작동하지 않는다. 아마 데미안 방식이어서 그런지는 모르겠다.)


---

# 따옴표 기법(Quoting)

다음과 같은 예제를 따라해 보자.

```
kgm0917@DESKTOP-DT2VQLB:~$ echo this is a test

this is a test
```

``` shell
kgm0917@DESKTOP-DT2VQLB:~$ echo The total is $100.00

The total is 00.00
```

첫 번째 에는 쉘이 echo 명령어의 인자에서 불필요한 공백을 삭제하여 단어 분할을 했다.

두 번째에서는 매개변수 확장으로 정의되지 않은 변수로 처리된 $1이 빈 문자열로 치환되었다. 여기서 ‘따옴표 기호를 활용하는 기능’을 제공해 준다.


**따옴표 기호를 활용하는 기능(Quoting)**: 쉘 원치 않는 확장을 선택적으로 감출 수 있도록 하는 것을 의미.




- 큰따옴표 기호

따옴표를 활용한 첫 번째 형태는 쌍 따옴표다. 쌍 따옴표로 텍스트를 묶으면 수레엇 사용하는 모든 특수한 기호들이 가진 의미가 없어지고 대신 일반적인 문자들로 인식된다.

단 `
`$,`, \,`   
기호는 그대로 실행이 된다. 단어 분할, 경로명 확장, 틸드 (~)확장, 괄호 확장 등을 숨길 수는 있지만 매개변수 확장, 산술 확장, 명령어 치환을 그대로 된다.

쌍 따옴표로 파일명에 있는 공백 문제를 해결할 수 있다. 예를 들어 우리는 two words.txt 라는 파일명 때문에 애를 먹고 있는데, 커맨드라인에서 이 이름을 사용하면 하나의 파일이 아니라 단어 분할로 인해 두 개의 별도 명령 인자로 인식되기 때문이다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ ls -l two words.txt

ls: cannot access 'two': No such file or directory

ls: cannot access 'words.txt': No such file or directory
```


큰 따옴표는 이러한 단어 분할 문제를 막고 원하는 결과를 얻을 수 있다. 이 문제로 인한 손상을 복원할 수 있다.


kgm0917@DESKTOP-DT2VQLB:~$ ls -l "two words.txt"

ls: cannot access 'two words.txt': No such file or directory

kgm0917@DESKTOP-DT2VQLB:~$ mv "two words.txt" two_words.txt

[^1]이렇게 하면 귀찮게 큰 따옴표를 입력할 필요가 없다.

명령어 치환 시 큰 따옴표의 효과에 대해서 좀 더 자세히 살펴보려고 한다. 첫째, 단어 분할이 어떤 식으로 이루어지는가를 깊이 들여다본다. 이전 예제에서 단어 분할이 텍스트에 있는 빈칸을 어떻게 삭제하는지 보았다.



``` shell
kgm0917@DESKTOP-DT2VQLB:~$ echo this is a test

this is a test
```

기본적으로 단어 분할은 빈칸, 탭, 개행문자 유무를 확인하고 이들을 단어 사이의 구분자로 처리한다. 이 말은 따옴표가 없는 공백, 탭, 개행문자는 텍스트로 인식하지 않는다는 의미이다. 그저 문자 정보를 분리해주는 기호일 뿐이다. 이 때문에 단어가 다른 인자로 구분되어, 예제에 나타난 문장은 4개의 각기 다른 명령 인자로 표현된 것이다.

그러나 여기에 큰 따옴표를 붙이면 문자 분할은 사라지고, 빈칸은 구분 기호로 처리되지 않으며, 명령 인자에 포함된 일부분으로 인식이 된다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ echo "this is a test"

this is a test
```



- 따옴표 기호

모든 확장을 숨겨야 한다면 따옴표 기호를 사용하면 된다. 따옴표가 없이, 따옴표, 큰 따옴표를 활용한 결과를 비교해보자.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ echo text ~/*.txt {a,b} $(echo foo) $((2+2)) $USE

R

text /home/kgm0917/lazy_dog.txt /home/kgm0917/ls-error.txt /home/kgm0917/ls-output.txt /home/kgm0917/ls.txt a b foo 4 kgm0917

kgm0917@DESKTOP-DT2VQLB:~$ echo "text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER"

text ~/*.txt {a,b} foo 4 kgm0917

kgm0917@DESKTOP-DT2VQLB:~$ echo 'text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER'

text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER
```

예제에서 볼 수 있듯이 각각 따옴표 기호가 늘어나면서 점점 확장이 없어지는 것을 알 수 있다.


- 이스케이프 문자


가끔씩 하나의 문자를 인용하고 싶을 때, 이를 위해서 해당 문자 앞에 백슬래시를 추가하면 되는데, 이를 **이스케이프 문자**(escape character)라고 부른다. 종종 이 문자는 선택적으로 확장되는 것을 막기 위해 큰 따옴표 안에서 사용된다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ echo "The balance for user $USER is: \$5.00"

The balance for user kgm0917 is: $5.00
```


또한 파일명에 있는 어떤 문자가 가지고 있는 특별한 의미를 없애고 싶을 때 흔히 사용된다. 쉘상에 특별한 의미가 있는 문자를 파일명에 사용하고 싶은 경우가 바로 그 예다. $,!,&,(공백) 과 같은 여러 가지가 있다. 이 기호들을 쓰고 싶으면 다음과 같이 하면 된다.

``` shell
kgm0917@DESKTOP-DT2VQLB:~$ mv bad\&filename good_filename

mv: cannot stat 'bad&filename': No such file or directory

(파일이 존재하지 않기에 삭제되지 않는다.)
```


백슬래시 기호를 표시하고 싶다면 `\\`를 입력해라. 단, 따옴표 내에서는 백슬래시의 의미가 사리지고 평범한 문자로 인식이 된다.


- ***==부연 설명==***

**백슬래시 확장 문자열(Backslach Escape Sequences)**

백슬래시는 이스케이프 문자의 역할과 더불어 제어 코드로 불리는 특수한 문자를 대표하는 기호의 일부분으로써 사용된다. ASCII 코드 체계의 처음 32개의 문자들은 텔레타이프와 같은 장치로 명령어를 전송하는 역할을 한다. 이러한 코드들 중 일부는 이미 알고 있는 것들(탭, 백스페이스, 라인피드, 캐리지 리턴)이지만 나머지(null,EOT,ACK)sms 그렇지 않다.


[표 7-2] 백슬래시 확장 문자열

| 확장 문자열 | 뜻                               |
| ------ | ------------------------------- |
| \a     | 벨 (“알림”- 컴퓨터에서 알림소리 발생)         |
| \b     | 백스페이스                           |
| \n     | 새 줄(유닉스와 같은 시스템에서는 라인피드를 생성한다.) |
| \r     | 캐리지 리턴                          |
| \t     | 탭                               |

이 표는 일반적인 백슬래시 확장 문자열의 일부를 보여준다.

echo 옵션에 –e 옵션을 붙여 사용하면 이스케이프 문자를 해석한다. 또는 $‘ ’문자를 다음과 같이 입력해도 되는데, sleep 명령어를 이용해서 지정된 시간(초)동안 기다린 후 종료하는 간단한 프로그램을 작성해보려고 한다. 이것으로 기본적인 카운트다운 타이머를 만들 수 있다.

sleep 10; echo –e “Times up\a”

$ ‘’ 문자를 사용할 수 있다.

sleep 10; echo “time s up” $‘\a’



[^1]: 매개변수 확장, 산술 확장, 명령어 치환 시에는 큰 따옴표 안에서도 그 작업을 그대로 수행한다.