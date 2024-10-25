
#### 다수의 인자에 접근

하지만 수많은 인자가 주어진다면 어떻게 될까?

```shell
┌──(kali㉿kali)-[~]
└─$ ./posit-param *

Number of arguments: 69
$0 = ./posit-param
$1 = bin
$2 = case-menu
$3 = deletion-script
$4 = Desktop
$5 = dirlist-bin.txt
$6 = dirlist-sbin.txt
$7 = dirlist-usr-bin.txt
$8 = dirlist-usr-sbin.txt
$9 = distros-bydate.txt

                                
```

이 예제 시스템에서 와일드카드 `*`는 69개의 인자들로 확장된다. 이 많은 것들을 어떻게 처리할 수 있을까? 쉘은 비록 어설프긴 하지만 하나의 방법을 제공한다. ==shift 명령어는 실행될 때마다 각 매개변수가 **"하나씩 다음으로 이동"** 하게끔 한다.== 사실 shift를 사용하여 단 하나의 매개변수(절대 바뀌지 않는 `$0`도 포함)를 가져오는 것이 가능하다.

```shell
#!/bin/bash

# posit-param2: script to display all arguments

count=1

while [[ $# -gt 0 ]]; do
        echo "Argument $count = $1"
        count=$((count + 1))
        shift
done

```

shift가 실행될 때마다 `$2`의 값은 `$1`로, `$3`의 값은 `$2`로 차례차례 이동한다. 또한 `$#`의 값은 1씩 감소한다.

`posit-param2` 프로그램은 남은 인자 수를 확인하고 인자가 남는 한 계속되는 루프를 만든다. 현재 인자를 표시하고, 처리된 인자 수를 세기 위해 연수 count루프를 반복할 때마다 증가한다. 그리고 마지막으로는 shift는 다음 인자를 `$1`로 불러온다. 다음은 실행 중인 프로그램이다.

```shell
┌──(kali㉿kali)-[~]
└─$ ./posit-param2 a b c d
Argument 1 = a
Argument 2 = b
Argument 3 = c
Argument 4 = d

```

### 간단한 응용 프로그램

shift 없이도 위치 매개변수를 사용하는 유용한 응용 프로그램을 작성할 수 있다. 이 예제로 간단한 파일 정보 프로그램이다.

```shell
#!/bin/bash

# file_info: simple file information program

PROGNAME=$(basename $0)


if [[ -e $1 ]]; then
        echo -e "\nFile Type:"
        file $1
        echo -e "\nFile Status:"
        stat $1
else
        echo "$PROGRAM: usage:  $PROGRAM file" >&2
        exit 1
fi

```

이 프로그램은 파일 종류(file 명령어로 확인된)와 지정된 파일의 상태([[stat]] 명령어를 사용하여)를 표시한다. 이 프로그램의 한 가지 흥미로운 점은 PROGNAME 변수다. 그것은 basename `$0` 명령으로부터 그 결과를 가져온다.  basename 명령어는 경로명의 앞 부분을 제거하고 파일의 기본 이름만을 남긴다. 이 예제에서 basename은 예제 프로그램의 전체 경로명인 매개변수 `$0`에서 경로명의 선두를 제거한다. 이 값은 이 프로그램 끝의 사용법처럼 메시지를 구성하는데 유용하다. 이러한 방식으로 코딩하면, 그 메시지가 프로그램명에 따라 자동으로 조절되기 때문에 스크립트명을 변경할 수 있다.

### 쉘 함수에서 위치 매개변수 사용


인자를 전달하기 위해 쉘 스크립트에 위치 매개변수를 사용해던 것처럼 쉘 함수에 인자를 전달할 수 있다. 우리는 이제 file_info 스크립트를 쉘 함수로 변환할 것이다.

```shell
file_info(){

# file_info: simple file information program

PROGNAME=$(basename $0)


if [[ -e $1 ]]; then
        echo -e "\nFile Type:"
        file $1
        echo -e "\nFile Status:"
        stat $1
else
        echo "$PROGRAM: usage:  $PROGRAM file" >&2
        exit 1
fi}
```

쉘 함수 file_info를 포함한 스크립트가 파일명 인자와 함께 함수를 호출하면, 그 인자는 함수에 전달된다.

이 기능으로 우리는 스크립트뿐만 아니라 `.bashrc` 파일에서도 사용할 수 있는 유용한 쉘 함수를 작성할 수 있다.


여러분은 PROGNAME 변수가 쉘 변수 FUNCNAME으로 변경된 것을 눈치챘을 것이다. 쉘은 현재 실행된 쉘 함수를 계속 추적하여 자동으로 이 변수를 갱신한다. `$0`은 항상 커맨드라인 첫 번째 항목을 전체 경로명(즉, 프로그램명)을 가지지만 우리가 예측한 것처럼 쉘 함수명은 가지고 있지 않다.


