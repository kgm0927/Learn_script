
### 파일을 묶고 압축풀기

zip 프로그램은 파일 압축과 보관을 한 번에 할 수 있는 프로그램이다. 이 프로그램이 지원하는 파일 형식은 윈도우 사용자에게 익숙한 .zip 파일이다. 하지만 리눅스에서 [[gzip]]이 지배적인 압축 프로그램이고 그 다음 [[bzip2]]를 많이 사용한다. 리눅스 사용자는 zip 프로그램으로 파일을 압축하는 대신 주로 윈도우 시스템과 파일을 교환할 때 사용한다.

가장 기본적 사용법은 다음과 같다.


zip *options zipfile file ...*


예를 들어, 놀이터에 zip 아카이브 파일을 하나 만들려면 다음과 같이 한다.

``` shell
┌──(kali㉿kali)-[~]
└─$ zip -r playground.zip playground
  adding: playground/ (stored 0%)
  adding: playground/dir-089/ (stored 0%)
  adding: playground/dir-089/file-G (stored 0%)
  adding: playground/dir-089/file-U (stored 0%)
  adding: playground/dir-089/file-T (stored 0%)
  adding: playground/dir-089/file-D (stored 0%)
  adding: playground/dir-089/file-M (stored 0%)
  adding: playground/dir-089/file-H (stored 0%)
  adding: playground/dir-089/file-X (stored 0%)
  adding: playground/dir-089/file-E (stored 0%)
  adding: playground/dir-089/file-K (stored 0%)
  adding: playground/dir-089/file-Z (stored 0%)
  adding: playground/dir-089/file-R (stored 0%)
  adding: playground/dir-089/file-J (stored 0%)

...
┌──(kali㉿kali)-[~]
└─$ dir
Desktop    Downloads  Pictures  Templates  foo      playground      playground.tbz  playground.zip   remote-stuff
Documents  Music      Public    Videos     foo.txt  playground.tar  playground.tgz  playground2.tar
      

```

**반복 순화을 위한 -r 옵션**을 사용하지 않으면 playground 디렉토리만(하위 내용은 해당하지 않음) 저장할 것이다. .zip 확장자는 자동적으로 붙여진다. 하지만 좀 더 명확히 하기 위해서는 우리는 확장자를 사용할 것이다.

zip 아카이브가 생성되는 동안 다음과 같은 메시지를 연속적으로 보여준다.

``` shell
  adding: playground/dir-041/file-W (stored 0%)
  adding: playground/dir-041/file-O (stored 0%)
  adding: playground/dir-041/file-N (stored 0%)
  adding: playground/dir-041/file-P (stored 0%)
  adding: playground/dir-041/file-I (stored 0%)
  adding: playground/dir-041/file-L (stored 0%)
  adding: playground/dir-041/file-C (stored 0%)
  adding: playground/dir-057/ (stored 0%)
  adding: playground/dir-057/file-G (stored 0%)
  adding: playground/dir-057/file-U (stored 0%)
  adding: playground/dir-057/file-T (stored 0%)
  adding: playground/dir-057/file-D (stored 0%)
  adding: playground/dir-057/file-M (stored 0%)
  adding: playground/dir-057/file-H (stored 0%)
  adding: playground/dir-057/file-X (stored 0%)
  adding: playground/dir-057/file-E (stored 0%)
  adding: playground/dir-057/file-K (stored 0%)
  adding: playground/dir-057/file-Z (stored 0%)
  adding: playground/dir-057/file-R (stored 0%)
  adding: playground/dir-057/file-J (stored 0%)
  adding: playground/dir-057/file-A (stored 0%)
  adding: playground/dir-057/file-B (stored 0%)

```

아카이브에 추가될 각 파일의 상태 메시지를 보여주고 있다. zip 프로그램은 두 가지의 저장 방법중 하나를 이용하여 파일을 아카이브에 포함시킨다. ==여기 나와 있듯이 압축 과정 없이 **"저장"** 할 수도 있고 아니면 **"압축"** 작업을 거칠 수 있다.== 압축 방법 뒤에 나오는 숫자는 압축률을 가리킨다. 우리 놀이터에는 오직 빈 파일만 있기 때문에 압축되지 않는다.

zip 파일을 해제할 때 unzip 프로그램을 사용하면 아주 쉽다.

```shell

┌──(kali㉿kali)-[~]
└─$ cd foo                 
                                                                                                                   
┌──(kali㉿kali)-[~/foo]
└─$ unzip ../playground.zip
Archive:  ../playground.zip

```

zip 프로그램에 대해서 알아둬야 할 중요한 것은(tar와는 반대로), 기존 아카이브 파일이 지정되면 파일 자체가 교체되는 것이 아니라 업데이트 된다는 것이다. 즉 기존의 아카이브는 유지되면서 새로운 파일은 추가되고 중복된 파일은 교체된다는 것이다.


unzip 프로그램은 일부 파일을 지정하며 zip 아카이브를 선택적으로 해제할 수 있다.

```shell
┌──(kali㉿kali)-[~]
└─$ unzip -l playground.zip playground/dir-087/file-Z
Archive:  playground.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
        0  2024-09-24 06:43   playground/dir-087/file-Z
---------                     -------
        0                     1 file
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ cd foo
                                                                                                                   
┌──(kali㉿kali)-[~/foo]
└─$ unzip  ../playground.zip playground/dir-087/file-Z
Archive:  ../playground.zip
replace playground/dir-087/file-Z? [y]es, [n]o, [A]ll, [N]one, [r]ename: y
 extracting: playground/dir-087/file-Z  

```

-l 옵션을 사용하면 압축을 해제하는 대신에 단지 아카이브의 내용만을 표시한다. 만약 아무런 파일이 지정되지 않으면, 아카이브의 모든 파일을 표시할 것이다. -v 옵션을 통해 그 내용을 자세하게 볼 수 있다. 아카이브 해제 시 기존 파일과 혼돈이 있을 경우 파일이 교체되기 전 확인 메시지를 표시하도록 설정할 수 있다.

tar처럼 zip 프로그램도 표준 입출력을 활용할 수 있다. 하지만 그리 유용한 방법은 아니다. -@ 옵션으로 파일명들을 zip과 연결할 수도 있다.

``` shell

┌──(kali㉿kali)-[~]
└─$ find playground -name "file-A" | zip -@ file-A.zip  
```

여기서 우리는 -name "file-A"와 일치하는 파일 목록을 불러오기 위해서 find 명령어를 사용하였고 그 다음은 zip과 함께 해당 목록을 연결하였다. 그리하여 선택된 파일들만 포함한 file-A.zip 라는 아카이브가 생성된다.

또한 zip 프로그램은 해당 결과를 표준 출력으로 보낼 수 있지만 일부 소수의 프로그램만 가능하기에 제한적이다. 그리고 아쉽게도 unzip 프로그램은 표준 입력을 허용하지 않는다. 이것은 zip과 unzip 프로그램이 tar 처럼 네트워크 파일을 복사하는 데 함께 사용되는 것을 방지하기 위해서이다.

하지만 zip 프로그램은 표준 입력을 허용하기 때문에 다른 프로그램의 출력을 압축하는 데 활용할 수 있다. 

``` shell
┌──(kali㉿kali)-[~]
└─$ ls -l /etc/ | zip ls-etc.zip -
  adding: - (deflated 83%)

```

이 예제에서 ls의 출력과 zip을 연결하였다. tar 처럼 zip은 마지막의 대시 기호 "입력 파일로 표준 입력을 사용하라"라는 의미로 해석된다.

unzip 프로그램은 -p(파이프라인) 옵션을 사용하면 출력 결과로 표준 출력으로 전송할 수 있다.

```shell
┌──(kali㉿kali)-[~]
└─$ ls -l /etc/ | zip ls-etc.zip -
  adding: - (deflated 83%)
(이렇게 해서 모든 etc에 관련된 출력 결과가 나옴)
```

지금까지 zip와 unzip 으로 할 수 있는 기본적인 것들을 모두 다뤄보았다. 이 두 프로그램 모두 다양한 옵션을 활용하여 그 기능을 확대할 수 있다. 하지만 일부 옵션은 다른 시스템과 고유한 것들이 있다. zip과 unzip의 man 페이지는 잘 짜여 있고 유용한 예제들이 많다.

