

### 우리는 이 장부터 프로그램을 만들기 시작할 것이다. 이 프로젝트의 목적은 다양한 쉘 기능들이 프로그램을 만들 때 어떻게 사용되는지 확인하는 것과, 가장 중요한 ==목적== 프로그램을 만드는 것이다.

우리가 만들 것은 **보고서 생성** 프로그램이다. 이 프로그램은 시스템의 상태 정보를 보여주는 것으로, HTML 형식으로 보고서를 작성하여 웹 브라우저를 확인할 수 있도록 한다.

일반적인 프로그램은 몇 가지 단계를 통해 만들어지는데 각 단계가 진행될수록 기능이 추가되고 강화된다. 우리가 만들 프로그램의 첫 단계는 아직 시스템 정보가 없는 아주 간단한 HTML 페이지를 만드는 것이다. 자, 이제 시작해보자.


---
# 1단계: 간단한 HTML 문서 만들기

여기서 먼저 우리가 알아둬야 할 것은 잘 구성된 HTML 문서 형식이다. 다음 내용을 확인해보자.

``` shell


<HTML>
        <HEAD>
                <TITLE>Page Title</TITLE>
        </HEAD>
        <BODY>
                Page body.
        </BODY>
</HTML>

```

이와 같이 텍스트 편집기에 입력한 후 foo.html 이라는 이름으로 파일을 저장해보자. 그런 다음,  file:///home/username/foo.html (현재 내가 하는 곳에서는 file:///home/kali/foo.html)이라는 URL을 파이어폭스에 입력하여 확인해보도록 하자.

우리 프로그램의 첫 번째 단계에서는 표준 출력으로 이 HTML 파일을 출력할 수 있다. 이런 식으로 꽤 쉽게 프로그램을 작성할 수 있다. 그리고 텍스트 편집기를 다시 실행하여 `~/bin/sys_info_page`라는 이름의 새 파일을 만들어보자.


```shell
┌──(kali㉿kali)-[~]
└─$ vim ~/bin/sys_info_page
                            
```

그런 다음 다음과 같이 스크립트를 작성해보자.


```shell

#!/bin/bash

# Program to output a system information page
<HTML>
        <HEAD>
                <TITLE>Page Title</TITLE>
        </HEAD>
        <BODY>
                Page body.
        </BODY>
</HTML>
        
```

우리는 첫 스크립트는 shebang과 주석(주석을 포함하는 것은 언제나 좋은 생각이다) 그리고 출력 결과의 각 행을 위한 echo 명령들을 포함하고 있다. 파일을 저장한 후, 실행 퍼미션을 설정하고 실행시켜보자.

```shell
┌──(kali㉿kali)-[~]
└─$ chmod 755 ~/bin/sys_info_page
┌──(kali㉿kali)-[~/bin]
└─$ ./sys_info_page
<HTML>
        <HEAD>
                <TITLE>Page Title</TITLE>
        </HEAD>
        <BODY>
                Page body.
        </BODY>
</HTML>
         
```

이 프로그램을 실행하면, 화면에는 HTML 문서가 표시되어야 한다. 왜냐하면 스크립트의 echo 명령어로 이 프로그램의 출력을 표준 출력으로 전달했기 때문이다. 이 프로그램을 다시 실행하여 sys_info_page.html 파일로 프로그램 출력 방향을 재지정하면, 이번에는 웹 브라우저에서 내용을 확인할 수 있다.

```shell
┌──(kali㉿kali)-[~/bin]
└─$ ./sys_info_page > sys_info_page.html
                                                                                                                   
┌──(kali㉿kali)-[~/bin]
└─$ firefox sys_info_page.html    
```

지금까지는 순조롭게 진행된다.

프로그램을 작성할 때, 되도록이면 간단명로함을 추구해야 한다. 프로그램이 보기 좋고 이해하기 쉽다면 유지보수하는 데 매우 용이하다. 하지만 타이핑하는 양을 줄인다고 프로그램이 쉬워진다는 것은 아니다. 현재까지 우리가 작성한 프로그램은 괜찮은 편이다. 하지만 더 간편하게 표현할 수 있다. 우리의 프로그램에서 모든 echo 명령을 하나로 표현할 수 있다. 이는 프로그램의 결과에 몇 줄을 추가하도록 한다. 자, 다음과 같이 스크립트를 작성해보자.

```shell
┌──(kali㉿kali)-[~/bin]
└─$ cat sys_info_page
#!/bin/bash

# Program to output a system information page

echo "<HTML>
        <HEAD>
                <TITLE>Page Title</TITLE>
        </HEAD>
        <BODY>
                Page body.
        </BODY>
</HTML>"

```

따옴표로 묶인 문자열에는 개행 문자가 포함되어 있기 때문에 여러 행을 포함할 수 있는 것이다. 쉘은 따옴표가 닫힐 때까지 계속 텍스트를 읽을 것이다. 이 방식을 커맨드라인에도 적용해보자.

```shell
┌──(kali㉿kali)-[~/bin]
└─$ echo "<HTML>
dquote> <HEAD>
dquote> <TITLE> Page Title </TITLE>
dquote> </HEAD>
dquote> <BODY>
dquote> Page body.
dquote> </BODY>
dquote> </HTML>"
<HTML>
<HEAD>
<TITLE> Page Title </TITLE>
</HEAD>
<BODY>
Page body.
</BODY>
</HTML>

```

맨 앞의 > 기호는 PS2 쉘 변수에 해당하는 쉘 프롬프트 기호다. ==이 기호에는 쉘에 여러 문장을 입력할 때마다 나타난다.== 이 기능은 현재로선 다소 이해하기 어려운 부분이 있다. ==하지만 여러 줄에 걸쳐 프로그래밍을 작성하는 방법을 다루게 될 것이다.== 그때는 아마도 더 이해하기 쉬워질 것이다.

---
# 2단계: 데이터 입력해보기

이제 우리가 만든 프로그램으로 간단한 문서를 작성할 수 있다. 이번에는 문서에 몇 가지 정보를 추가해보자. 이를 위해서는 다음과 같이 내용을 변경해야 한다.

```shell
#!/bin/bash

# Program to output a system information page

echo "<HTML>
        <HEAD>
                <TITLE>System Information Report</TITLE>
        </HEAD>
        <BODY>
                <H1>System Information Report</H1>
        </BODY>
</HTML>"
          
```

페이지 제목과 보고서 본문에 헤드라인을 추가하였다.


---
# 변수와 상수

앞의 스크립트에 한 가지 살펴보아야 할 사항이 있다. **System Information Report** 문자열이 어떻게 반복되는지 주목해보자. 이 작은 스크립트는 문제가 되지 않는다. 하지만 스크립트가 굉장히 길고 이러한 문자열이 한두 개가 아닌 경우에는 어떠할까? 이 문자열을 다른 것으로 바꾸려면 하나하나 다 바꿔야 하는데 엄청난 작업이 될 것이다. 만약 스크립트를 다시 정리해서 이 문자열이 여러 번이 아니라 단 한 번만 나타나도록 바꾼다면 어떨까? 그렇게 한다면 향후 유지보수하는 데 매우 용이할 것이다. 다음과 같이 해 보자.

```shell
#!/bin/bash

# Program to output a system information page

echo "<HTML>
        <HEAD>
                <TITLE>System Information Report</TITLE>
        </HEAD>
        <BODY>
                <H1>System Information Report</H1>
        </BODY>
</HTML>"

```

title이란 이름의 변수를 생성해서 System Information Report 값을 할당한다. 매개변수 확장으로 이 문자열이 여러 위치에 놓이게 된다.


### 변수와 상수 생성

변수를 어떻게 생성할까? 이미 우리는 사용해보았다. 쉘이 변수를 만나면 자동으로 변수를 생성한다. 보통 프로그램이 언어에서는 변수를 사용하기 전에 미리 "선언"하고 정의해야 한다는 점에서 구별된다. 쉘은 이 부분에 관해서 매우 관대하다. 하지만 일부 문제점이 존재한다. 다음과 같은 커맨드라인 사용 예제를 통해 확인해보자.

```shell
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ foo="yes"
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo $foo
yes
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ echo $fool

                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ 





```

이 내용을 보면 우선 foo 변수에 yes 값을 설정하고, 그 다음 echo 명령어로 그 값을 표시하였다. 그런 다음 변수 이름을 잘못 입력하면 빈칸이 표시됨을 알 수 있다. 이것은 쉘이 foo1이라는 변수를 만나게 되면, 그 변수를 생성하고 기본적으로 빈 값을 설정했기 때문이다. 이 때문에 오타가 발생하지 않도록 주의를 기울여야 한다. 또한 여기서 중요한 것은 이 예제에서 정말로 무슨 일이 벌어지는가에 대한 이해다. 이전에 쉘이 확장하는 방식을 살펴 보았는데 다음과 같은 명령은

```shell
┌──(kali㉿kali)-[~]
└─$ echo $foo 

```

매개변수 확장이 되면 다음과 같은 결과를 보인다.

```shell
┌──(kali㉿kali)-[~]
└─$ echo yes 
yes

```

반면 다음 명령어는
```shell
┌──(kali㉿kali)-[~]
└─$ echo $fool


```

다음과 같은 결과를 반환한다.

```shell
┌──(kali㉿kali)-[~]
└─$ echo    

```

값이 없는 변수는 아무것으로도 확장되지 않는다. 이는 인자가 필요한 명령어를 사용할 때 악영향을 끼칠 수 있다. 다음의 예제에서 확인해보자.

```shell
┌──(kali㉿kali)-[~]
└─$ foo=foo.txt
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ foo1=foo1.txt
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ cp $foo $fool                 
cp: missing destination file operand after 'foo.txt'
Try 'cp --help' for more information.

```

우리는 여기에서 foo와 foo1이라는 변수에 값을 할당하였다. 그러고 나서 cp 명령어를 실행하는 데 일부러 두 번째 인자를 잘못 입력했다. ==확장이 이루어지면, 두 인자에 cp 명령이 실행되어야 함에도 불구하고 앞의 명령인자에만 적용되었다.==

변수 이름을 지정하는 데 몇 가지 규칙을 알아보도록 하자.

- 변수명은 알파벳, 숫자, 밑줄 기호로 구성될 수 있다.
- 변수 명의 첫 글자는 반드시 문자 또는 밑줄로 시작해야 한다.
- 공백 및 구두점 사용은 금한다.

변수라는 단어가 가진 의미는 **변할 수 있는** 값이다. 그리고 상당수의 애플리케이션에서 변수가 이런 의미로 사용되고 있다. 하지만 우리가 만들려는 프로그램에서 사용한 title이란 변수는 **상수** 개념이다. 상수란, 이름이 정의되고 값이 지정된다는 점에서 변수와 같지만 그 값은 변하지 않는다는 점이 다르다. 우리는 기하학 계산을 하는 프로그램에서 프로그램 전반에 걸쳐 숫자로 정의하는 대신 PI 라는 상수를 정의하고 그 값이 2.1415로 할당할 것이다. 물론 쉘은 변수와 상수를 따로 구분하지는 않는다. 이러한 용어를 사용하는 것은 프로그래머의 편의를 위함일 뿐이다. 일반적으로 상수를 정의할 때는 대문자를, 변수는 소문자를 사용한다. 이러한 관습에 따라서 앞의 스크립트를 수정해보도록 하자.

``` shell
#!/bin/bash

# Program to output a system information page

echo "<HTML>
        <HEAD>
                <TITLE>System Information Report</TITLE>
        </HEAD>
        <BODY>
                <H1>System Information Report</H1>
        </BODY>
</HTML>"
              
```

여기서는 쉘 변수 HOSTNAME의 값을 추가하여 보고서 제목에 변화를 주었다. 이 변수가 의미하는 것은 장치의 네트워크명이다.


>[!tip] 저자주
>사실 쉘에서도 상수의 값이 바뀌지 않도록 지정할 수 있다. 내장 명령어인 `declare`를 -r(읽기 전용) 옵션과 함께 사용하면 된다. TITLE을 다음과 같이 설정해보자.
>```
> declare -r TITLE="Page Title"
>```
>쉘은 이후로 TITLE 값의 변경을 막을 것이다. 이러한 기능은 잘 사용되진 않지만 정규적인 스크립트에서는 찾아볼 수 있을 것이다.



### 변수와 상수에 값 할당

이제부터 우리가 알고 있는 확장에 대해 지식이 빛을 발하는 순간이다. 이미 본 바와 같이 변수는 다음과 같이 값이 할당된다.

` variable=value`

*variable*에는 변수 이름이, value 자리에는 문자열이 들어간다. 다른 프로그래밍 언어와 달리, 쉘은 변수에 할당되는 값의 데이터 형식을 전혀 고려하지 않고 모두 문자열로 인식한다. 물론 declare 명령어와 -i 옵션을 사용하여 정수 값으로도 선언할 수 있다. 하지만 읽기 전용 옵션을 적용한 변수를 설정하는 것이 흔치 않는 것처럼 이 또한 잘 사용되지 않는다.

이 변수 할당문에는 변수명, 등호, 변수 값 사이에 빈칸이 없어야 함을 명심해야 한다. 그렇다면 변수의 값에 문자열이 어떤 식으로 표현될 수 있을까? 문자열로 확장될 수 있는 것이라면 어느 것도 가능하다.

``` shell
a=z                              # "z" 문자열을 변수 a에 할당
b="a string"                     # 빈 칸은 따옴표 안에서만 사용 가능
c="a string and $b"              # 변수처럼 다른 확장이 가능
d=$(ls -l foo.txt)               # 명령어 결괄르 변수 값으로 확장
e=$((5*7))                       # 연산 확장이 값으로 지정된 예
f="\t\ta string\n"               # 이스케이프 기호(탭 또는 개행)가 사용된 예
```


한 줄에 여러 변수를 정의할 수 있다.
```shell
                                                                                                                   
┌──(kali㉿kali)-[~/bin]
└─$ a=5 b="a string"       
                          
```

변수가 확장될 때, 중괄호가 사용되기도 한다. 이는 복잡한 스크립트 내용으로 변수의 이름을 알아보기 힘든 경우에 유용하게 쓰일 수 있다. ==여기서 변수를 사용하여 myfile 이라는 파일명을 myfile1로 바꿔보도록 하자.==
```shell
┌──(kali㉿kali)-[~/bin]
└─$ filename="myfile"                                                  
                                                                                                                   
┌──(kali㉿kali)-[~/bin]
└─$ touch $filename
                                                                                                                   
┌──(kali㉿kali)-[~/bin]
└─$ mv $filename $filename1
mv: missing destination file operand after 'myfile'
Try 'mv --help' for more information.
            
```

파일명 변경에 실패했다. 그 이유는 쉘이 mv 명령어의 두 번째 인자를 새 변수로 해석했기 때문이다. 이 문제는 다음과 같이 쉽게 해결할 수 있다.

```shell
┌──(kali㉿kali)-[~/bin]
└─$ mv $filename ${filename}1

```

중괄호를 활용하여 1이라는 숫자가 filename이라는 변수명의 일부가 아님을 쉘이 인식하도록 한다.

지금까지 살펴본 것을 토대로 우리가 작성하던 보고서에 작성일, 시간, 작성자 이름을 추가해보도록 하자.

```shell
#!/bin/bash

# Program to output a system information page

TITLE="System Information Report For $HOSTNAME"
CURRENT_TIME=$(date+"%x %r %z")
TIME_STAMP="Generated $CURRENT_TIME, by $USER"
echo "<HTML>
        <HEAD>
                <TITLE>System Information Report</TITLE>
        </HEAD>
        <BODY>
                <H1>System Information Report</H1>
        </BODY>
</HTML>"
         
```


---
# Here 문서(Here Documents)

우리는 지금까지 텍스트를 출력하는 두 방법에 대해서 공부했다. 이 두 방법 모두 [[echo]] 명령어를 사용했다. 이번에는 **here 문서** 혹은 **here 스크립트**라고 하는 세 번째 방법에 대해서 소개하고자 한다. here 문서는 I/O 리다이렉션의 추가적인 형태로 텍스트 논문을 스크립트에 삽입할 때 그리고 명령어의 표준 입력으로 보낼 때 사용한다. 이는 다음과 같은 방식으로 사용된다.

*command* << token
text
*token*

**command**는 표준 입력을 허용하는 명령어 이름이고, *token*은 삽입할 텍스트의 끝을 가리키는 문자열을 말한다. 스크립트에 here 문서를 적용해보자.

```shell
#!/bin/bash

# Program to output a system information page

TITLE="System Information Report For $HOSTNAME"
CURRENT_TIME=$(date+"%x %r %z")
TIME_STAMP="Generated $CURRENT_TIME", by "$USER"

cat << EOF
<HTML>
        <HEAD>
                <TITLE>$TITLE</TITLE>
        </HEAD>
        <BODY>
                <H1>$TITLE</H1>
                <P>$TIME_STAMP</P>
        </BODY>
</HTML>
_EOF_
        
```

[[echo]] 명령어를 사용하는 대신 [[cat]] 명령어와 here 문서를 사용하였다. `_EOF_` 문자열(**파일 끝**을 의미)이 token으로 사용되었고 삽입된 텍스트의 끝을 표시해주고 있다. 여기서 주목해야 할 점은 token은 반드시 단독 사용해야 하고 그 줄에 어떠한 빈칸도 허용하지 않는다.

그렇다면 here 문서의 장점은 무엇일까? echo 명령을 사용하는 것과 별반 차이는 없지만 기본적으로 here 문서에서는 쉘이 인식하는 따옴표 및 쌍 따옴표의 의미는 사라진다.다음 예제를 살펴보자.

```shell
┌──(kali㉿kali)-[~]
└─$ foo="some text"  
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ cat << _EOF_    
heredoc> $foo   
heredoc> "$foo"
heredoc> '$foo'
heredoc> \$foo
heredoc> _EOF_
some text
"some text"
'some text'
$foo
       
```

여기서 알 수 있듯이, 쉘은 인용 기호를 전혀 신경쓰지 않는다. 그저 일반적인 문자일 뿐이다. 따라서 우리는 here 문서에서 자유롭게 인용 기호를 사용할 수 있고, 이것은 보고서 프로그램에서 아주 유용하다는 것을 알게 되었다.

here 문서는 표준 입력을 허용하는 명령어와 함께 사용될 수 있다. 다음은 FTP 서버에 파일을 가져오기 위해 [[ftp]] 프로그램으로 일련의 명령어들을 전송하는 here 문서를 활용한 예제다.

```shell
#!/bin/bash

# Script to retrieve a file via FTP


FTP_SERVER=ftp.ml.debian.org
FTP_PATH=/debian/dists/lenny/main/installer-i386/current/images/cdrom
REMOTE_FILE=debian-cd_info.tar.gz

ftp -n << _EOF_
open $FTP_SERVER
user anonymous kali@kali
cd $FTP_PATH
hash
get $REMOTE_FILE
bye
_EOF_
ls -l $REMOTE_FILE

```

여기서 리다이렉션 기호인 << 기호를  <<- 기호로 바꾸면 쉘은 here 문서에서의 선행되어 나오는 탭 기호들을 무시하게 된다. 따라서 here 문서에서 가독성을 향상시키기 위해 들여쓰기가 가능해진다.

```shell
#!/bin/bash

# Script to retrieve a file via FTP


FTP_SERVER=ftp.ml.debian.org
FTP_PATH=/debian/dists/lenny/main/installer-i386/current/images/cdrom
REMOTE_FILE=debian-cd_info.tar.gz

ftp -n <<- _EOF_
open $FTP_SERVER
user anonymous kali@kali
cd $FTP_PATH
hash
get $REMOTE_FILE
bye
_EOF_
ls -l $REMOTE_FILE

```


---
# 마무리 노트

이 장에서는 성공적인 스크립트를 구성하기 위한 절차를 살펴보기 위한 프로젝트를 시작하였다. 변수와 상수에 대한 개념을 살펴보았고 어떻게 스크립트에 적용되는지에 대해서도 알아보았다. 그것들은 향후 매개변수 확장에서 찾아볼 수 있는 수많은 응용 그 첫 번째가 되는 개념이다. 또한 우리는 스크립트를 출력하는 방법과 텍스트를 삽입하는 몇 가지 방법도 알아보았다.