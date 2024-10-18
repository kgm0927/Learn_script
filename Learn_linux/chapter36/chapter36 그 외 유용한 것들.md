
### 드디어 마지막 장이다. 우리는 이장에서 그 밖의 소소한 내용들을 살펴보며 이 여정을 마치려고 한다. 지금까지 광범위하게 살펴보긴 했지만 우리가 다루지 못한 bash 기능들이 여전히 많이 존재한다. 대부분 잘 알려져 있지 않고 주로 bash에 통합되어 유용하게 사용된다. 하지만 자주 사용되지 않았음에도 특정 프로그래밍 문제에 도움을 주는 도구들고 꽤 있다. 우리는 여기서 이들에 대해 살펴볼 것이다.

---
# 그룹 명령과 서브쉘

bash는 명령어들을 그룹화하여 함께 사용할 수 있도록 허용하는 두 가지 방법이 있다. **그룹 명령**을 사용하던지 **서브쉘**을 사용하는 것이다. 각 문법의 예제는 다음과 같다.


그룹 명령:

`{command1; command2; [command3; ...]} `


서브쉘:

`(command1; command2; [command3; ...])`

이 두 방식의 차이점은 그룹 명령은 중괄호로 해당 명령어들을 감싸고 서브쉘은 괄호로 사용한다는 것이다. ==bash가 그룹 명령어를 처리하는 방식 때문에, 중괄호는 반드시 스페이스로 명령어와 구분되어야 하고 마지막 명령어는 중괄호가 닫히기 전에 세미콜론이나 개행으로 끝나야 한다는 것을 명심해야 한다.==


### 리다이렉션 수행

그럼 그룹 명령과 서브쉘은 어떤 경우에 적합할까? 둘은 중요한 차이점(잠시 후 확인하게 될)이 있지만 모두 리다이렉션을 조절하기 위해 사용된다. 복수 명령어들로 리다이렉션을 수행하는 스크립트를 살펴보자.

```shell
┌──(kali㉿kali)-[~]
└─$ ls -l > output.txt
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo "Listing of foo.txt" >> output.txt  
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ cat foo.txt >> output.txt
                                  
```

이는 매우 단순하다. 세 명령의 출력을 output.txt 파일로 리다이렉션한다. 이 코드를 다음과 같이 그룹 명령으로 만들 수 있다.

```shell
{ls -l; echo "Listing of foo.txt"; cat foo.txt} > output.txt
```

서브쉘의 방식은 다음과 같다.

```shell
(ls -l; echo "Listing of foo.txt"; cat foo.txt) > output.txt
```

이러한 방법은 타이핑을 줄여주기도 하지만 파이프라인과 함께 할 때 더 빛을 발한다. 명령어 파이프라인을 생성할 때, 여러 명령의 결과를 하나의 스트림으로 합치게 되면 더 유용하다. 그룹 명령과 서브쉘로 쉽게 말들 수 있다(구현할 수 있다).

```shell
{ls -l; echo "Listing of foo.txt"; cat foo.txt} | lpr
```

세 명령의 결과를 합치고 lpr의 입력과 연결하여 완성된 보고서를 출력한다.


### 프로세스 치환

그룹 명령과 서브쉘이 유사해 보이고 리다이렉션을 위해 스트림을 합치는 데 둘 다 사용되긴 하지만, 둘 사시에는 중요한 차이점이 있다. 그룹의 명령은 그 명령들을 현재 쉘에서 실행하지만 서브쉘은 현재 쉘의 복사본인 자식 쉘에서 수행한다는 것이다. 이는 쉘의 환경이 복사되고 새 개체게 주어진다는 것을 의미한다. 서브쉘을 종료할 때, 복사된 환경은 사라진다. 따라서 서브쉘의 환경(변수 할당 포함)에 발생한 모든 변경된 부분들도 사라지게 된다. ==결론적으로 대부분의 경우 스크립트가 서브쉘을 요구하지 않는 한 서브쉘보다 그룹 명령을 더 선호하며 그룹 명령의 처리 속도가 훨씬 빠르고 메모리를 전게 사용한다.==


우리는 28장에서 서브쉘 환경 문제에 대한 예제를 보았다. 파이프라인에서 [[read]] 명령어가 예측했던 것과 달리 제대로 동작하지 않은 것을 확인했다. 그것을 간략하게 표현하면 이와 같다.

```shell
echo "foo" | read
echo $REPLY
```

read 명령이 서브쉘에서 실행되기 때문에 REPLY 변수의 내용물은 항상 비어있다. 그리고 그 REPLY의 사본은 서브쉘이 종료될 때 사라진다.

왜냐하면 파이프라이의 명령들은 항상 서브쉘에서 실행되기 때문이다. 변수를 할당하는 모든 명령은 문제에 직면할 것이다. 다행히도, 쉘은 이 문제를 해결할 **프로세스 치환**이라는 이국적인 형태의 확장을 제공한다.

프로세스 치환은 두 가지 방식으로 표현할 수 있다. 표준 출력을 생성하는 프로세스인 경우

*`<(list)`*

또는 표준 입력을 가져오는 프로세스인 경우

*`>(list)`*

*list*는 명령어들의 목록을 나타낸다.

이 read 문제를 해결하기 위해 이처럼 프로세스 치환을 사용할 수 있다.

```shell
read <<(echo "foo")
echo $REPLY
```

프로세스 치환은 서브쉘의 출력을 리다이렉션의 목적에 맞게 일반적인 파일로 처리하도록 허용한다. 사실, 이것은 확장 형태이기 때문에 우리는 실제 값을 확인할 수 있다.

```shell
┌──(kali㉿kali)-[~]
└─$ echo <(echo "foo")
/proc/self/fd/11
```

[[echo]]를 사용하여 확장 결과를 보면, 서브쉘의 출력이 `/proc/self/fd/11`이라는 파일에 의해 제공된다는 것을 알게 된다.

종종 프로세스 치환은 read를 포함한 반복문에 사용된다. 여기 서브쉘이 생성한 디렉토리 목록의 내용물을 처리하는 [[read]] 루프 예제가 있다.

```shell
#!/bin/bash

# pro-sub : demo of process substitution

while read attr links owner group size date time filename; do

        cat <<- EOF
                Filename:       $filename
                Size:           $size
                Owner:          $owner
                Group:          $group
                Modified:       $date $time
                Links:          $links
                Attributes:     $attr

        EOF
done < <(ls -l | tail -n +2)

```

디렉토리 목록의 모든 행을 read로 반복 수행한다. 스크립트의 마지막 줄에서 그 목록이 생성된다. 이 행은 프로세스 치환의 결과를 반복문으로 표준 입력으로 재지정한다. tail 명령은 꼭 필요하진 않지만 목록의 첫 번째 출을 제거하기 위해 프로세스 치환 파이프라인을 포함한다.

```shell
┌──(kali㉿kali)-[~]
└─$ ./pro-sub | head -n 20
Filename:       22:17 arith-loop
Size:           252
Owner:          kali
Group:          kali
Modified:       Oct 12
Links:          1
Attributes:     -rwxrwxr-x

Filename:       05:57 bin
Size:           4096
Owner:          kali
Group:          kali
Modified:       Oct 11
Links:          2
Attributes:     drwxrwxr-x

Filename:       01:26 case-menu
Size:           585
Owner:          kali
Group:          kali

```

---
# 트랩(Traps)

우리는 [[chapter10 프로세스|10장]]에서 프로그램이 시그널에 어떻게 반응하는지 보았다. 이 기능을 우리 스크립트에도 추가할 수 있다. 우리가 만든 스크립트들은 이 기능이 필요하진 않지만(매우 짧은 실행 시간과 임시 파일을 생성하지 않았기 때문에), 좀 더 크고 복잡한 스크립트들은 시그널 처리 루틴으로 얻을 수 있는 혜택이 있을 것이다.

우리가 크고 복잡한 스크립트를 설계할 때, ==사용자가 스크립트를 실행 중에 로그아웃하거나 시스템을 종료하는 상황을 고려해야 한다.== 이러한 이벤트가 발생하면, 시그널은 영향력이 미치는 모든 프로세스에게 전해지게 된다. 따라서 이 프로세스들이 가리키는 프로그램들은 순차적으로 적절히 프로그램 종료 작업을 진행할 수 있다. 예를 들어, 실행 중에 임시 파일을 생성하는 스크립트를 만든다고 치자. 좋은 설계 방법은 스크립트가 작업을 마칠 때 그 파일을 삭제하는 것이다. 또한 프로그램이 종료되기 직전을 가리키는 시그널을 받으면 파일을 삭제하는 스크립트를 작성하는 것도 괜찮은 방법이다.

bash는 이를 위해 **트랩**(trap)이라는 기법을 제공한다. 트랩은 그에 걸맞은 이름을 trap 빌트인 명령으로 구현되었다. trap은 다음과 같은 문법을 사용한다.

`trap argument signal [signal...]`

*argument*는 읽어 들여 명령어로 처리될 문자열이다. 그리고 *signal*는 해석한 명령어를 작동시킬 시그널을 가리킨다.

여기 간단한 예제가 있다.

```shell
#!/bin/bash

# trap-demo : simple signal handling demo
trap "echo 'I am ignoring you.'" SIGINT SIGTERM

for i in {1..5}; do
        echo "Iteration $i of 5"
        sleep 5
done

```


이 스크립트는 실행 중에 SIGINT 또는 SIGTERM 시그널을 받으면 [[echo]] 명령을 실행하는 트랩을 정의한다. 사용자가 스크립트를 멈추려고 `ctrl-c`를 누르면, 프로그램은 다음과 같이 동작할 것이다.

```shell
┌──(kali㉿kali)-[~]
└─$ ./trap-demo
Iteration 1 of 5
^CI am ignoring you.
Iteration 2 of 5
Iteration 3 of 5
Iteration 4 of 5
Iteration 5 of 5
^CI am ignoring you.

```

사용자가 프로그램을 중단하려고 할 때마다 이 메시지를 대신 출력한다.

유용한 명령어 시퀀스를 구성하기 위해 문자열을 생성하는 것이 어색할 수 있다. 그래서 실제로는 명령어로 쉘 함수를 지정한다. 다음 예제에서는 각 시그널을 제어하기 위해 쉘 함수를 분리하여 사용하였다.


```shell
#!/bin/bash

# trap-demo2 : simple signal handling demo

exit_on_signal_SIGINT(){
        echo "Script interrupted." 2>&1
        exit 0
}

exit_on_signal_SIGTERM(){
        echo "Script terminated." 2>&1
        exit 0
}

trap exit_on_signal_SIGINT SIGINT
trap exit_on_SIGTERM SIGTERM


for i in {1..5}; do
        echo "Iteration $i of 5"
        sleep 5
done

```

이 스크립트는 각 시그널을 위한 두 trap 명령어를 사용한다. 결국 각 트립은 특정 시그널을 받을 때 쉘 함수를 명시한 것이다. 각 시그널 제어 함수에 exit 명령을 포함한 것에 주목하라.
exit 명령이 없으면, 스크립트는 함수가 완료된 후에도 계속 실행될 것이다.


이 스크립트가 실행 중에 사용자가 `ctrl-c`를 누르면 결과는 다음과 같다.

```shell
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ ./trap-demo2
Iteration 1 of 5
Iteration 2 of 5
^CScript interrupted.

```

>[!tip] 임시파일
>스크립트에 시그널 핸들러가 포함된 이유는 임시 파일들을 제거하기 위해서다. 임시 파일들은 스크립트가 실행 중에 중간중간에 결과를 유지하기 위해 성성된다. 임시 파일의 이름을 붙이는데 몇 가지 기술이 있다. 전통적으로, 유닉스형 시스템의 프로그램들은 임시 파일을 그런 종류의 파일들을 공유하는 `/tmp` 디렉토리에 생성한다. 하지만 그 디렉토리는 공유되기 때문에 슈퍼유저 특권을 가진 프로그램이 실행될 때 특히 보안 문제가 제기된다. 모든 사용자에게 노출된 파일에 적당한 퍼미션을 설정하는 확실한 절차 외에 임시 파일에 예측할 수 없는 파일명을 붙이는 것 또한 중요하다. 이는 **temp race attack**이라는 공격을 피할 수 있는 방법이다. 예측할 수 없는(여전히 기술적인) 이름을 만드는 방법 중 하나는 다음과 같이 하는 것이다.
>
>`tempfile=/tmp/$(basename $0).$$.$RANDOM`
>
>이것은 프로그램 이름, 프로세스 ID(PID), 난수 순서로 구성된 파일명을 생성할 것이다. 하지만 `$RANDOM` 쉘 변수는 1부터 32767까지 사이의 값만을 반환한다는 것에 유의하라. 이것은 매우 큰 범위는 아니다. 따라서 그 변수개체 하나로는 작심한 공격자를 이기기에 충분하지 않다.
>더 나은 방법은 임시 파일을 생성하고 이름을 붙이는 **mktemp** 프로그램(mktemp 표준 라이브러리 함수와 혼동하지 않기를)을 사용하는 것이다. mktemp 프로그램은 파일명을 만들기 위해 사용되는 인자로 템플릿을 허용한다. 탬플릿은 X 문자의 연속으로 이루어져야 한다. 그것은 해당하는 수 만큼 임의의 글자와 숫자로 교체된다. 더 긴 X 문자들의 나열은 더 긴 임의 문자의 연속을 만든다. 여기 그 자체가 있다.
>
> `tempfile=$(mktemp /tmp/foobar.$$.XXXXXXXXXXXXX)`
> 
> 이는 임시 파일을 생성하고 tempfile 변수에 그 이름을 할당한다. 템플릿의 X 문자들은 임의의 글자와 숫자로 변환된다. 결국 최종 파일명(PID를  얻기위해 특수 매개변수 `$$`의 확장 값 또한 포함)은 다음과 같을 것이다.
> 
> ` /tmp/foobar.6593.UOZuvM6654`
> 
> mktemp의 [[man]] 페이지에는 mktemp가 임시 파일명을 만든다는 것과 파일을 생성하는 것에 대해 기술되어 있다.
> 일반 사용자에 의해 실행된 스크립트에서는, `/tmp` 디렉토리 사용을 피하고 사용자 홈 디렉토리 내의 임시 파일을 위한 디렉토리를 만들어 사용하는 것이 현명할 것이다. 이와 같은 코드로 말이다.
> 
> `[[ -d $HOME/tmp ]] || mkdir $HOME/tmp `
> 


---
# 비동기 실행

비동기 방식은 동시에 하나 이상의 작업을 수행할 때 적합하다. 우리는 이미 최근의 운영체제에서 멀티유저 시스템은 아니더라도 적어도 멀티태스킹은 지원한다는 것을 안다. 스크립트들 역시 멀티태스킹하여 동작하게 만들 수 있다.

이는 항상 스크립트의 실행을 수반한다. 결국 부모 스크립트가 계속 수행되는 동안 추가 작업을 하는 하나 이상의 자기 스크립트를 실행하게 된다. 그러나 이 방식으로는 스크립트 시리즈를 실행하면, 부모와 자식 간을 조율하는 데 문제가 발생할 수 있다. 즉 부모 또는 자식이 상호 의존적이고, 하나의 스크립트가 해당 작업을 완료하기 전에 다른 스크립트의 실행이 완료될 때를 기다려야만 한다면 어떻게 해야 할까?

bash는 이와 같은 **비동기 실행**의 관리를 도와주는 빌트인 명령어를 가지고 있다. wait 명령어는 명시된 프로세스(예, 자식 스크립트)가 완료될 때까지 부모 스크립트의 실행을 멈추게 한다.

### wait

먼저 wait 명령어를 시연할 것이다. 이를 위해 두 스크립트가 필요하다. 다음은 부모 스크립트다.

```shell
#!/bin/bash

# async-parent : Asynchronous execution demo (parent)

echo "Parent: Starting..."
echo "Parent: launching child scipt..."
async-child &
pid=$!
echo "Parent: child (PID=$pid) launched."

echo "Parent: continuing..."
sleep 2

echo "Parent: pausing to wait for child to finish..."
wait $pid

echo "Parent: child is finished. Continuing..."
echo "Parent: parent is done. Exiting."
```

그리고 이것은 자식 스크립트다.

```shell
#!/bin/bash

# async-child: Asynchronous execution demo (child)


echo "Child: child is running..."
sleep 5
echo "Child: child is done. Exiting."
                
```

이 예제에서 자식 스크립트는 매우 간단하다. 실제 동작은 부모 스크립트가 한다. 부모 스크립트 내에서 자식 스크립트는 실행되고 백그라운드로 진입하게 된다. 자식 스크립트의 프로세스 ID는 쉘 매개변수 `$!`의 값을 통하여 pid 변수에 할당되어 기록된다. 이 쉘 매개변수는 항상 백그라운드에 진입한 마지막 작업의 프로세스 ID를 가지게 될 것이다.

부모 스크립트는 계속 실행되고 그 후 자식 프로세스의 PID와 함께 wait 명령을 실행한다. 이는 부모 스크립트가 자신의 종료 시점에서 자식 스크립트가 종료될 때까지 멈추도록 한다.

이것을 실행하면 부모와 자식 스크립트는 다음과 같은 결과를 생성한다.

```shell
┌──(kali㉿kali)-[~]
└─$ ./async-parent
Parent: Starting...
Parent: launching child scipt...
Parent: child (PID=50914) launched.
Parent: continuing...
./async-parent: line 7: async-child: command not found
Parent: pausing to wait for child to finish...
Parent: child is finished. Continuing...
Parent: parent is done. Exiting.

```

---
# 네임드 파이프(Named Pipes)

대부분의 유닉스형 시스템에서 **네임드 파이프**(Named pipe)라 불리는 특수한 종료의 파일을 생성할 수 있다. 네임드 파이프는 두 프로세스를 연결하기 위해 사용되고 다른 종료의 파일들처럼 파일로써 사용될 수 있다. 이것은 자주 사용되진 않지만 알아두면 유용하다.

**클라이언트/서버**라 불리는 일반적인 프로그래밍 아키텍쳐가 있다. 이는 네트워크 연결과 같은 **프로세스 간 통신**이나 네임드 파이프와 같은 통신 수단으로 활용할 수 있다.

클라이언트/서버 시스템의 가장 널리 사용되는 방식은 당연히 웹 브라우저와 그와 통신하는 웹 서버이다. 웹 브라우저는 클라이언트로서 동작하며, 서버에 요청을 한다. 그리고 서버는 브라우전에 웹 페이지로 응답한다.

네임드 파이프는 파일처럼 동작하지만 실제로는 선입선출(FIFO) 버퍼 형태다. 일반적인(이름없는) 파이프들과 함께 데이터는 한 곳으로 들어가 다른 곳으로 나오게 된다. 네임드 파이프는 다음과 같이 설정할 수 있다.

`process1 > named_pipe`

그리고 

`process2 < named_pipe`

이것은 다음과 같이 작동하게 될 것이다.

`process1 | process2`


### 네임드 파이프 설정

우선, 네임드 파이프를 생성해야 한다. mkfifo 명령어를 사용하면 된다.

```shell
┌──(kali㉿kali)-[~]
└─$ mkfifo pipe1         
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ ls -l pipe1       
prw-rw-r-- 1 kali kali 0 Oct 16 02:13 pipe1

```

mkfifo를 사용하여 pipe1이라는 네임드 파이프를 생성한다. 그리고 [[ls]]를 사용하여 그 파일을 확인하면 파일 속성 필드의 첫 글자가 p인 것을 볼 수 있다. 여기서는 p는 네임드 파이프를 나타낸다.


### 네임드 파이프 사용


네임드파이프가 어떻게 동작하는지 보기 위해, 두 개의 터미널 윈도우(혹은 두개의 가상 콘솔)가 필요할 것이다. 첫 번째 터미널에 간단한 명령어를 입력하여 그 출력 결과를 네임드 파이프로 재지정한다.

```shell
┌──(kali㉿kali)-[~]
└─$ ls -l > pipe1   
```

Enter를 누르면 명령어는 멈춘 것처럼 보일 것이다. 그 이유는 파이프 말미에 아직 아무런 데이터를 받지 못했기 때문이다. 이러한 현상을 파이프가 **블록**되었다고 한다. 이 상태는 우리가 프로세스를 끝에 붙이는 것으로 해결될 것이다. 그 후 파이프에서 입력을 읽어 들이기 시작할 것이다. 이제 두 번째 터미널 윈도우에 다음 명령을 입력하자.

```shell
┌──(kali㉿kali)-[~]
└─$ cat < pipe1

```

첫 번째 터미널에서 생성된 디렉토리 목록은 두 번째 터미널에서 [[cat]] 명령의 출력으로 나타난다. 첫 번째 터미널의 [[ls]] 명령은 성공적으로 완료되고 더 이상 블록되지 않는다.


---

# 마무리 노트

자, 우리는 모든 여정을 마쳤다. 이제 남은 것은 실전 뿐이다. 우리가 커맨드라인에 대해 광범위하게 다루긴 했지만 겨우 수박 겉핥기일 뿐이다. 여전히 우리가 찾아서 즐겨야 할 수천여 개의 커맨드라인 프로그램들이 남아있다. 어서, /usr/bin을 샅샅히 파헤쳐보자!