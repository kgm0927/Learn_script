
read는 쉘의 내장 명령어로 표준 입력으로 들어온 내용을 한 줄씩 읽어올 때 사용된다. 이 명령어는 키보드 입력을 읽어볼 때나 리다이렉션을 적용하여 파일의 데이터를 읽어올 때 사용될 수 있다.

`read [-options] [variable...]`

`option` 위치에는 다음 표에 나와 있는 옵션 중 하나 이상을 지정하고, `variable`에는 입력 값을 할당할 변수명을 하나 이상 입력한다. ==만약 변수명을 입력하지 않으면 쉘 변수 `REPLY`가 데이터를 갖게 된다.==

- [표 28-1] read 옵션


| 옵션             | 설명                                                                          |
| -------------- | --------------------------------------------------------------------------- |
| -a *array*     | 입력 `array`에 할당한다. 인덱스 0으로 시작함. 배열에 대해서는 35장에서 다룰 것이다.                       |
| -d *delimiter* | `delimiter` 문자열에서 개행 문자가 아닌 가장 첫 번째 문자를 입력의 끝을 가리키는 데 사용한다.                 |
| -e             | Readline을 이용하여 입력을 관리한다. 이것은 커맨드라인과 같은 방식으로 입력 방식을 편집할 수 있게 해준다.            |
| -n *num*       | 입력된 행 전체 대신 `num` 수의 문자만을 읽어온다.                                             |
| -p *prompt*    | `prompt` 문자열을 이용하여 입력을 위한 프롬프트를 띄운다.                                        |
| -r             | Raw 모드, 백슬래시 기호를 이스케이프로 해석하지 않는다.                                           |
| -s             | 묵음 모드. 문자를 입력할 때마다 해당 문자를 다시 표시하지 않는다. 이것은 비밀번호를 입력할 때나 중요한 정보를 입력할 때 유용하다. |
| -t *seconds*   | 타임아웃. 일정 시간(초) 후에 입력을 종료한다. read 명령은 입력 시간이 초과되면 0이 아닌 종료 상태 값을 반환한다.       |
| -u *fd*        | 표준 입력 대신 *fd* 파일 디스크립터를 입력으로 사용한다.                                          |

기본적으로 read 명령은 표준 입력의 필드를 변수에 할당한다. 만약 read 명령어를 이용하여 정수 계산식 스크립트를 수정한다면, 다음과 같이 할 수 있다.

```shell
#!/bin/bash

# test-integer: evaluate the value of an integer.

read int

if [[ "$int"=~ ^-?[0-9]+$ ]]; then
        if [ $int -eq 0 ]; then
                echo "$int is zero."
        else
                if [ $int -lt 0 ]; then
                        echo "$int is negative."
                else
                        echo "$int is positive."
                fi
                if [ $((int % 2)) -eq 0 ]; then
                        echo "$int is even."
                else
                        echo "$int is odd."
                fi
        fi
else
        echo "Input value is not an integer." >&2
        exit 1
fi
       
```

[[echo]] 명령어와 -n 옵션을 함께 사용하여(출력 시 개행하지 않는다) 프롬프트를 띄우고 read 명령어로 int 변수에 입력될 값을 기다린다. 스크립트의 실행 결과는 다음과 같다.

```shell
┌──(kali㉿kali)-[~]
└─$ ./test-integer  
Please enter an integer -> 5
5 is positive.
5 is odd.
```

read 명령은 여러 변수에 값을 할당할 수 있다. 다음 스크립트를 확인해보자.

```shell
┌──(kali㉿kali)-[~]
└─$ cat read-multiple
#!/bin/bash

# read-multiple: read multiple values from keyboard


echo -n "Enter one or more values > "
read var1 var2 var3 var4 var5

echo "var1 = '$var1'"
echo "var2 = '$var2'"
echo "var3 = '$var3'"
echo "var4 = '$var4'"
echo "var5 = '$var5'"
                      
```

이 스크립트에서는, 다섯 개의 값을 할당하고 표시하였다. 여기서 각각각 다른 값이 주어졌을 때 read 명령이 어떻게 동작하는지 살펴보자.

```shell
┌──(kali㉿kali)-[~]
└─$ ./read-multiple               
Enter one or more values > a b c d e
var1 = 'a'
var2 = 'b'
var3 = 'c'
var4 = 'd'
var5 = 'e'
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ ./read-multiple               
Enter one or more values > a
var1 = 'a'
var2 = ''
var3 = ''
var4 = ''
var5 = ''
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ ./read-multiple               
Enter one or more values > a b c d e f g
var1 = 'a'
var2 = 'b'
var3 = 'c'
var4 = 'd'
var5 = 'e f g'
               
```

==read 명령은 예상했던 수보다 적게 값을 입력 받으면, 나머지 변수들을 빈 값으로 채운다.== 반면에 더 많은 수의 값을 입력 받은 경우에는, 마지막 변수가 나머지 모든 값을 다 할당받는다.

read 명령어 다음에 변수가 없다면 쉘 변수인 REPLY에 모든 값이 할당될 것이다.

```shell
#!/bin/bash

# read-signle: read multiple values into default variable

echo -n "Enter one or more values > "
read

echo "REPLY = '$REPLY'"

```

이 스크립트를 실행하면 다음과 같이 나타난다.

```shell
┌──(kali㉿kali)-[~]
└─$ ./read-single  
Enter one or more values > a b c d
REPLY = 'a b c d'

```


### 옵션

read 명령어는 [표 28-1]에 나와 있는 옵션들을 지원한다.

read 명령에 다양한 옵션을 활용해서 흥미로운 것들을 만들어낼 수 있다. 예를 들면 -p 옵션으로 프롬프트 문자열을 생성할 수 있다.
(문제)
```shell
#!/bin/bash

# read-single: read multiple values into default variable

echo -n "Enter one or more values > "
read

echo "REPLY = '$REPLY'"
                           
```

-t 옵션과 -s 옵션으로 "비밀" 입력 기능과 입력 시간이 초과하면 종료되는 기능을 추가할 수 있다.

```shell
#!/bin/bash

# read-secret: input a secret passphrase

if read -t 10 -sp "Enter secret passphrase > " secret_pass; then
        echo -e "\nSecret passphrease = '$secret_pass'"
else
        echo -e "\nInput timed out" >&2
        exit 1
fi

```

이 스크립트는 사용자에게 비밀번호를 입력하라는 프롬프트를 띄우고 10초 동안 입력을 기다린다. 그 시간 동안 입력이 없으면 오류 메시지를 띄우고 종료된다. ==**-s** 옵션을 사용하였기 때문에 비밀번호는 때마다 다시 표시되지 않는다.==

### IFS로 입력 필드 구분하기


일반적으로 쉘은 read에 제공된 입력 내용을 단어로 나누는 작업을 수행한다. 지금까지 살펴본 대로, 이는 입력 행에서 하나 이상의 스페이스로 분리된 각 단어들이 read에 의해 별도의 변수에 할당된다는 것을 의미한다. 이러한 방식은 **IFS**(입력 필드 구분자)라고 하는 쉘 변수에 의해 설정된다. IFS의 기본 값은 스페이스, 탭, 개행 문자를 포함하고 있으므로 각각 별도의 항목으로 구분된다.

입력 필드 구분자를 제어하기 위해 IFS의 값을 조절할 수 있다. 예를 들어 `/etc/passwd` 파일은 필드 구분자로 콜론 기호를 사용하여 데이터를 포함하고 있다. IFS 값을 세미콜론으로 변경함으로써, 우리는 read를 사용하여 `/etc/passwd` 파일 내용을 입력 받을 수 있고 성공적으로 각기 다른 변수에 각 항목들을 구분해낼 수 있다. 다음 스크립트를 살펴보자.

```shell
#!/bin/bash

# read-ifs: read fields from a file

FILE=/etc/passwd

read -p "Enter a username > " user_name

file_info=$(grep "^$user_name:" "$FILE") # (1)
# 책에서는 "$FILE"이 아닌 $FILE이라고 되어 있는데 실제로 "$FILE"로 써야 작동된다.

if [ -n "$file_info" ]; then
        IFS=":" read user pw uid gid name home shell <<< "$file_info" # (2)

        echo "User =    '$user'"
        echo "UID =     '$uid'"
        echo "GID =     '$gid'"
        echo "Full Name = '$name'"
        echo "Home Dir. = '$home'"
        echo "Shell =     '$shell'"
else
        echo "No such user '$user_name'" >&2
        exit 1
fi


```

이 스크립트는 사용자에게 시스템을 사용자 계정명을 입력하라는 메시지를 띄우고 `/etc/passwd` 파일에서 해당 사용자 정보를 찾아 각 필드를 표시한다. 이 스크립트에는 흥미로운 점 두 가지가 보인다. 먼저 (1)에서 [[grep]] 명령어의 결과 값을 file_info라는 변수에 할당했다. grep에 사용한 정규 표현식을 통해 입력된 사용자명과 일치하는 내용을 `/etc/passwd` 파일에서 찾게 될 것이다.

두 번째 흥미로운 점은, (2)는 세 가지로 구성되어 있다는 것이다. 변수 할당문, read 명령과 그 인자로 입력된 변수명, 다소 낯선 리다이렉션 연산자다. 우선, 변수 할당 부분부터 살펴보자.

쉘은 명령어가 처리되기 직전에 하나 이상의 변수 할당을 허용한다. 여기서 이 할당은 뒤이어 나오는 명령어에 대한 환경을 변화시킨다. ==이러한 변화는 일시적이고 명령어의 지속 시간 동안만 환경을 변경하는 것이다.== 우리의 경우, IFS 값을 콜론 문자로 변경했다. 우리는 또 다른 방식으로 다음과 같이 코딩할 수 있을 것이다.

```shell
OLD_IFS="$IFS"
IFS=":"
read user pw uid gid name home shel <<< "$file_info"
IFS= "$OLD_IFS"
```

IFS 값을 저장하고 새로운 값을 할당하여 read 명령을 실행한 뒤 IFS의 값을 원래 값으로 복구시킬 수 있다. 분명한 것은 명령 전에 변수 할당이 놓이는 것이 좀 더 간단한 방법이라는 것이다.

`<<<` 연산자는 here 문자열이다. **here 문자열**이란, here 문서와 같은 것으로 다만 길이가 짧은 하나의 문자열로 구성된다. 이 예제에서는 `/etc/passwd` 파일의 데이터를 read 명령의 표준 입력으로 전달하고 있다. 왜 다음과 같이 하지 않고 간접적인 방식으로 하는지 의아할 수도 있다.

```shell
echo "$file_info" | IFS=":" read user pw uid pid name home shell
```

그런 데에는 그만한 이유가 있다.


>[!error] READ 명령어는 파이프라인과 함께 사용할 수 없다
>read 명령어가 일반적으로 표준 입력으로부터 값을 읽어오지만 다음과 같이 사용할 수는 없다.
>``` shell
>echo "foo" | read
>```
>이 명령이 실행될 것이라 기대하겠지만 그렇지 않다. 이 명령은 실행이 성공하긴 하지만 REPLY 변수는 항상 비어있을 것이다. 왜 그럴까?
>이에 대한 설명은 쉘이 어떻게 파이프라인을 다루는가에 대한 방식과 함께 이해해야 한다. bash(그리고 sh와 같은 또 다른 쉘들)에서, 파이프라인은 **서브쉘**을 생성한다. 이것은 쉘과 그 쉘 환경에 대한 복사본으로 파이프라인으로 명령어를 실행할 때 사용된다. 이전 예제에서 read 명령은 서브쉘에서 실행되었다.
>유닉스형 시스템의 서브쉘은 그들이 실행되는 동안 사용할 프로세스용 환경의 복사본을 생성한다. 프로세스가 종료되면, 해당 환경 복사본도 함께 삭제된다. 즉 **서브쉘은 부모 프로세스의 환경은 절대 변경하지 않음**을 의미한다. read  명령은 환경의 일부가 되는 변수를 할당한다. 위 예제에서 read 명령은 foo 값을 서브쉘 환경에 있는 REPLY 변수에 할당한다. 하지만 그 명령이 종료되면 서브쉘과 그 환경은 모두 사라지고 변수 할당에 대한 효력도 잃게 된다.
>here 문자열 사용은 이러한 문제를 피하는 방법 중 하나다. 또 다른 방법들은 36장에서 살펴보도록 하자.



