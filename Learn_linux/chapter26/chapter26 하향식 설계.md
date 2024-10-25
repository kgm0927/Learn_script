
### 프로그램들의 규모가 커지고 복잡해질수록 프로그램을 설계하여 코딩하고 유지보수하는 것도 점점 어려워진다. 이러한 대형 프로젝트를 진행할 때는, 크고 복잡한 작업을 작고 간단한 단위로 나누는 것이 좋을 것이다.

자, 이쯤에서 다음 상황을 상상해보자. 우리는 화성에서 온 외계인에게 지구에서 음식을 사러 시장에 가는 일상적인 일에 대해 설명하려고 한다. 우리는 이를 다음과 같이 여러 단계로 나눠 설명할 수 있을 것이다.

1. 차에 탄다.
2. 시장까지 운전해서 간다.
3. 차를 주차한다.
4. 시장에 들어간다.
5. 필요한 음식을 산다.
6. 차를 빼서 나온다.
7. 집으로 돌아간다.
8. 차를 주차한다.
9. 집으로 들어간다.

하지만 화성에서 온 외계인은 더 구체적인 설명을 원한다. 그렇다면 이 중에서 "주차하는 과정"을 몇 가지로 더 세분화하여 구체적으로 설명하도록 하자.

1. 주차 공간을 찾는다.
2. 그 빈 공간으로 간다.
3. 차의 엔진을 끈다.
4. 사이드 브레이크를 건다.
5. 차에서 나온다.
6. 차를 잠근다.


세 번째 단계인 "엔진을 끄는 과정"을 더 구체적으로 나누어 설명할 수 있다. 즉 "키를 돌려서 시동을 끈다", "키를 꺼낸다"등과 같이 말이다. 이런 식으로 시장에 가는 전체 과정의 단계를 세부적으로 나눠서 표현할 수 있을 것이다.

이렇듯 최상위 단계들을 정의하고 이러한 단계들을 구체적으로 나누어가는 과정을 **하향식 설계**(top-down design)라고 한다. 이러한 설계 방법은 크고 복잡한 작업을 단순하고 작은 단위의 작업으로 세분화시킬 수 있다. 하향식 설계 방식은 프로그램을 설계하는 방법 중 가장 흔히 사용된고, 특히 쉘 프로그래밍에 적합한 방법이기도 하다.

이번 장에서는 하향식 설계를 사용하여 보고서 생성 스크립트를 계속 개발해 나갈 것이다.

---
# 쉘 함수

우리가 작성 중인 스크립트는 다음과 같은 단계에 따라 HTML 문서를 생성하고 있다.

1. 페이지 열기.
2. 페이지 헤더 열기.
3. 페이지 제목 정하기.
4. 페이지 헤더 닫기.
5. 페이지 본문 열기.
6. 페이지 헤더 정보 출력하기.
7. 날짜 및 시간 출력하기.
8. 페이지 논문 닫기.
9. 페이지 닫기.

다음 개발 단계로 진행하기 전에 7번째와 8번째 단계 사이에 몇 가지 작업을 추가할 것이다.


- **시스템 가동시간(uptime)과 부하량** ==**uptime**은 가장 최근에 시스템이 종료되거나 재부팅된 이후부터의 가동시간을 나타내고,== ==**부하량**은 일정 시간마다 프로세서상에서 현재 실행 중인 작업의 평균 개수를 뜻한다.==
- **디스크 사용 공간.** 현재 사용중인 저장 장치의 사용공간
- **홈 공간.** 사용자별 저장 장치 사용 공간


만약 각 작업에 대한 명령어를 알고 있다면, 명령어 치환을 통해 스크립트에 그 명령어를 추가할 수 있다.

```shell
#!/bin/bash

# Program to output a system information page

TITLE="System Information Report For $HOSTNAME"
CURRENT_TIME=$(date+"%x %r %z")
TIME_STAMP="Generated $CURRENT_TIME, by $USER"

cat << _EOF_
<HTML>
        <HEAD>
                <TITLE>System Information Report</TITLE>
        </HEAD>
        <BODY>
                <H1>$TITLE</H1>
                <P>$TIME_STAMP</P>
                $(report_uptime)
                $(report_disk_space)
                $(report_home_space)
        </BODY>
</HTML>
_EOF_

```

이 명령어들을 사용하기 위한 두 방법을 소개한다. 스크립트를 세 가지로 나눠 작성한 다음 PATH에 정의된 디렉토리에 저장하거나 스크립트 자체를 **쉘 함수**로 정의하여 프로그램에 추가할 수 있다. 앞서 설명했던 것처럼 ==쉘 함수는 "미니스크립트"와 같다.== 즉 스크립트 안에 있는 또 다른 스크립트이며 독립적인 프로그램으로서 동작한다. 쉘 함수를 두 방법으로 정의할 수 있다. 첫 번째 방법은 다음과 같다.

``` shell
fuction name{
commands
return
}
```

*name*의 위치에는 함수 이름이, *commands* 자리에는 함수에서 사용될 일련의 명령어들이 위치한다. 두 번째 방법은 다음과 같다.

```shell
name (){
commands
return
}
```

이 두 형식은 동일한 것이기 때문에 아무거나 사용해도 된다. 다음은 쉘 함수를 사용한 예제다.

```shell
#!/bin/bash

# Shell function demo

function funct{
echo "Step 2"
return
}

# Main Program starts here

echo "Step 1"
funct
echo "Step 3"
```

쉘은 스크립트를 읽어가면서 1번부터 11번(`# Main Program starts here`바로 그 아래까지)까지 행의 내용을 무시한다. 왜냐하면 주석과 함수 정의가 이루어지는 부분이기 때문이다. 12번 행부터 [[echo]] 명령어가 실행되기 시작한다. 13번 행에서 funct이라는 이름의 쉘 함수가 **호출**되고 있다. 그리고 쉘은 명령어를 실행하듯 함수를 실행한다. 그러면 6번 행으로 이동하여 funct로 정의된 함수의 내용이 실행된다. 그리고 7번 행의 내용이 실행된다. **return** 명령어는 함수를 종료하고 프로그램의 실행 위치를, 함수를 호출한 그 다음으로 `echo "Step 3"`으로 이동시킨다. 그 다음 마지막 [[echo]] 명령이 실행된다. ==여기서 주목할 점은 함수 호출이 쉘 함수로서 인식이 되고 다른 프로그램으로 해석되지 않기 위해 쉘 함수는 호출이 되기도 전에 반드시 먼저 정의되어야 한다.==


우리의 보고서 생성기 스크립트에 작은 쉘 함수 정의를 추가해보도록 하자.

``` shell
┌──(kali㉿kali)-[~/bin]
└─$ cat sys_info_page
#!/bin/bash

# Program to output a system information page

TITLE="System Information Report For $HOSTNAME"
CURRENT_TIME=$(date+"%x %r %z")
TIME_STAMP="Generated $CURRENT_TIME, by $USER"


report_uptime(){
        return
}

report_disk_space(){
        return
}
report_home_space(){
        return
}


cat << _EOF_
<HTML>
        <HEAD>
                <TITLE>System Information Report</TITLE>
        </HEAD>
        <BODY>
                <H1>$TITLE</H1>
                <P>$TIME_STAMP</P>
                $(report_uptime)
                $(report_disk_space)
                $(report_home_space)
        </BODY>
</HTML>
_EOF_

```

쉘 함수 이름은 변수명 규칙을 따른다. 함수는 최소한 하나의 명령어를 포함하고 있어야 한다. return 명령어는 이 규칙을 만족시킨다.

---
# 지역 변수

지금까지 작성한 스크립트를 보면 모든 변수(상수를 포함하여)는 **전역 변수**로 선언되었다. 전역 변수는 프로그램 전반에 걸쳐 적용되는 변수다. 이것은 대부분의 경우 문제가 되지 않지만 가끔식 쉘 함수 사용에 혼란을 걸쳐 적용되는 변수다. 이것은 배부분의 경우 문제가 되지 않지만 가끔씩 쉘 함수 사용에 혼란을 가져오기도 한다. 쉘 함수 내부적으로 사용할 **지역 변수**가 종종 필요하게 된다. 지역 변수는 해당 변수가 정의된 쉘 함수 내에서만 유효하며 함수가 종료되는 순간 그 효과 또한 사라진다.

==지역 변수는 프로그래머가 이미 전역 변수나 다른 쉘 함수에 존재하는 변수명과 중복하여 사용할 수 있게 해 준다.== 물론 변수명 사용에 잠재적인 혼란이 없도록 한다.

이제 지역 변수를 정의하고 사용하는 방법에 대한 예제 스크립트를 살펴보도록 한다.

```shell
#!/bin/bash

# local-vars: script to demonstrate local variables

foo=0 #global variable too

funct_1(){

        local foo       # variable foo local to funct_1

        foo=1
        echo "funct_1: foo = $foo"
}

funct_2(){

        local foo       # variable foo local to funct_2

        foo=2
        echo "funct_2: foo = $foo"
}

echo "global: foo = $foo"
funct_1
echo "global: foo = $foo"
funct_2
echo "global: foo = $foo"


```

이처럼, 지역 변수는 local이라는 단어를 변수명 앞에 선언함으로써 정의할 수 있다. 이것은 변수를 정의한 쉘 함수 내에 변수를 생성한다. 그 쉘 함수 밖에서 이 변수는 존재하지 않는다. 스크립트를 실행하면 다음과 같은 결과를 볼 수 있다.
```shell
┌──(kali㉿kali)-[~]
└─$ ./local-vars
global: foo = 0
funct_1: foo = 1
global: foo = 0
funct_2: foo = 2
global: foo = 0

```

여기서 우리는 두 개의 쉘 함수 내에 선언된 지역 변수는 함수 밖에서 아무런 효력이 없다는 것을 알 수 있다.

이러한 특징은 쉘 함수가 다른 함수나 스크립트로부터 독립적일 수 있게 한다. 아주 중요한 특징이다. 왜냐하면 ==프로그램의 한 부분으로서 다른 부분의 간섭을 받지 않을 수 있기 때문이다.== 그렇기 때문에 필요한 경우, 쉘 함수만 잘라내어 다른 스크립트에 갖다 붙일 수 있다.

---
# 스크립트 실행 상태 유지

우리가 프로그램을 개발하는 동안, 프로그램을 실행 가능한 상태로 유지할 수 있다면 좋을 것이다. 이 상태를 유지한 채 프로그램을 자주 테스트하면 개발 단계에서 프로그램 오류들을 보다 쉽게 찾아낼 수 있을 것이다. 결국 디버깅 문제가 조금 더 쉬워지게 된다. 예를 들면, 프로그램을 실행하여 약간 변화를 준 다음, 프로그램을 다시 시작하면 문제를 만나게 된다. 이는 가장 최근 변경 내역이 문제의 원인일 수 있다. ==흔히 프로그래머들이 말하는 **스텁**(stub)이라는 빈 함수를 프로그램에 추가하면, 초기 단계에서 프로그램의 논리적 흐름을 확인해 볼 수 있다.== 스텁을 만들 때, 프로그래머에게 그러한 프로그램의 논리적 흐름의 진행을 보여주는 일종의 피드백을 제공하는 것이 좋을 것이다. 현재 스크립트 출력 결과를 살펴보면 타임스탬프가 출력된 다음 몇 줄의 공백이 있음을 알 수 있다. 하지만 그 이유는 정확히 알 수 없다. 왜 그런 것일까?

```shell
┌──(kali㉿kali)-[~/bin]
└─$ ./sys_info_page
./sys_info_page: line 6: date+%x %r %z: command not found
<HTML>
        <HEAD>
                <TITLE>System Information Report</TITLE>
        </HEAD>
        <BODY>
                <H1>System Information Report For kali</H1>
                <P>Generated , by kali</P>



        </BODY>
</HTML>

```

피드백을 볼 수 있는 내용을 함수에 추가해보자.

```shell

report_uptime(){
        echo "Function report_uptime executed"
        return
}

report_disk_space(){
        echo "Function report_disk_space executed"
        return
}
report_home_space(){
        echo "Function report_home_space executed"
        return
}


```

이제 스크립트를 다시 실행해보자.

```shell
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~/bin]
└─$ ./sys_info_page
./sys_info_page: line 6: date+%x %r %z: command not found
<HTML>
        <HEAD>
                <TITLE>System Information Report</TITLE>
        </HEAD>
        <BODY>
                <H1>System Information Report For kali</H1>
                <P>Generated , by kali</P>
                Function report_uptime executed
                Function report_disk_space executed
                Function report_home_space executed
        </BODY>
</HTML>

```

여기서 우리는 사실 선언한 세 개의 함수가 실행되고 있다는 것을 알 수 있다.

함수 프레임워크가 제대로 동작하기 위해, 함수에 살을 붙여야 한다. 우선, `report_uptime` 함수를 보자.

```shell
report_uptime(){
        cat <<- _EOF_
                <H2>System Uptime</H2>
                <PRE>$(uptime)</PRE>
                _EOF_
        return
}

```

상당히 직관적인 코드다. here 문서를 활용해서 섹션의 헤더와 uptime 명령어의 결과를 표시하였다. ==`<PRE>` 태그로 정의된 uptime 명령은 그 형식이 유지된다.== `report_disk_space` 함수 또한 비슷하게 수정해보자.

```shell

report_disk_space(){
        cat <<- _EOF_
                <H2>Disk Space Utilization</H2>
                <PRE>$(df -h)</PRE>
                _EOF_
        return
}

```

이 함수는 df -h 명령어를 사용하여 디스크 사용 공간을 확인하였다. 마지막으로 `report_home_space` 함수를 수정해보자.

```shell
report_home_space(){
        cat <<- _EOF_
                <H2>Home Space Utilization</H2>
                <PRE>$(du -sh /home/*)</PRE>
                _EOF_
        return
}

```

==우리는 [[du]] 명령어와 `-sh` 옵션을 함께 사용하여 사용자 공간을 확인하는 작업을 수행했다.== 하지만 이는 완벽한 해결책이 아니다. 우분투와 같은 일부 시스템에서는 통할 수 있으나 그렇지 않을 경우도 있다. ==그 이유는 대부분의 시스템들은 홈 디렉토리에 대한 접근 권한에 대해 보안상의 이유로 읽기 조체도 허용하지 않기 때문이다.== 그러한 시스템에 대해서는 스크립트가 슈퍼유저 권한으로 작성된 스크립트의 `report_home_space` 함수여야만이 실행이 가능하다. 조금 더 나은 해결책이 있다면 스크립트의 실행을 사용자의 권한에 맞게 조정하는 것이다. 이 부분은 27장에서 다뤄보도록 하자.


>[!tip] .BASHRC 파일에서 볼 수 있는 쉘 함수
>쉘 함수는 아주 훌륭한 별칭(alias)의 대체제다. 그리고 실제 개인적 용도로 사용하기 좋은 작은 명령들을 생성하기에 효과적인 방법이다. 별칭은 여러 종류의 명령어들이나 쉘의 기능들을 축약하기엔 매우 한정적인 반면 쉘 함수는 스크립트로 작성될 수만 있다면 어떤 것이든 가능하다. 예를 들어, `report_disk_spcae` 함수가 유용하다고 판단이 된다면 이 함수와 비슷한 기능의 함수의 ds 라는 함수를 .dashrc 파일에 만들어둘 수 있다.
>```
>ds()
>{
>echo "Disk Space Utilization For $HOSTNAME"
>df -h
>}
>```



---
# 마무리 노트

이 장에서는 하향식 설계라고 하는 프로그램 설계 방식에 대해 소개하였다. 그리고 점진적으로 쉘 함수가 발전하는 과정 또한 살펴보았다. 또한 지역 변수의 사용으로 쉘 함수가 독립적으로 운용될 수 있다는 사실도 알게 되었다. 쉘 함수를 여러 프로그램으로 재사용이 될 수 있도록 독립적으로 작성함으로써 많은 시간을 단축할 수 있게 되었다!