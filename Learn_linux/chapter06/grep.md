

---
# 패턴과 일치하는 라인 출력


grep 명령어는 파일의 텍스트 패턴을 찾을 때 사용하는 강력한 프로그래밍이다.


`grep pattern [file...]`


grep은 파일 내에서 ‘패턴을’ 만났을 때, 그 패턴을 가지고 있는 라인을 출력한다. grep으로 매우 복잡한 패턴도 찾을 수 있다. **정규 표현식**이라는 고급 수준의 패턴의 19장에서 다루게 된다.

우리가 가지고 있는 프로그램 목록에서 이름에 zip 이라는 글자가 포함된 모든 프로그램을 찾고 싶을 때 이를 확인하는 명령어를 만들어 보자.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ ls /bin /usr/bin |sort|uniq|grep zip

gpg-zip

gunzip

gzip

streamzip

zipdetails
```

grep 에는 몇 가지 옵션이 있다.

1. -i 옵션은 검색을 수행할 때 대소문자를 가리지 않도록 한다(리눅스는 대소문자를 구분한다).
2. -v 옵션은 패턴과 일치하지 않는 라인만 출력하도록 한다.

---
# 텍스트를 통한 검색


우리가 정규 표현식과 함께 사용할 메인 프로그램은 오랜 친구인 grep이다. grep이란 실제 global regular expression print란 구절에서 유래된 이름이다. 따라서  ==grep이 정규 표현식과 관련이 있는 것을 볼 수 있다. 기본적으로 grep은 지정된 정규 표현식과 일치하는 표준 출력을 가진 행을 출력한다.==


지금까지 우리는 다음과 같이 지정된 문자열만을 가지고 grep을 사용했다.

```shell
┌──(kali㉿kali)-[~]
└─$ ls /usr/bin | grep zip        
bunzip2
bzip2
bzip2recover
funzip
gpg-zip
gunzip
gzip
p7zip
preunzip
prezip
prezip-bin
streamzip
unzip
unzipsfx
zip
zipcloak
zipdetails
zipgrep
zipinfo
zipnote
zipsplit
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ 

```

이는 /usr/bin 디렉토리에 있는 zip 이라는 문자열을 파일명에 포함한 파일을 나열한다.

grep 프로그램은 이런 식으로 옵션과 인자들을 허용한다.

grep [*options*] regex [*file...*]


*reges*는 정규 표현식을 나타낸다.

[표 19-1]은 흔히 사용되는 grep 옵션들을 보여준다.

- [표 19-1] grep 옵션


| 옵션  | 설명                                                                                              |
| --- | ----------------------------------------------------------------------------------------------- |
| -i  | 대소문자 무시. 대문자와 소문자를 구분하지 않는다. --ignore-case로도 지정할 수 있다.                                          |
| -v  | 반전 매치. 일반적으로 grep은 일치하는 행을 출력한다. 이 옵션은 grep이 일치하지 않는 모든 행을 출력하도록 한다. --invert-match로도 지정할 수 있다. |
| -c  | 일치한 행(-v 옵션을 사용하면 일치하지 않는 행) 자체가 아닌 행의 수를 출력한다. --count로도 지정할 수 있다.                             |
| -l  | 일치한 행 자체가 아닌 이를 포함한 각각의 파일 이름을 출력한다. --files-with-matches로도 지정할 수 있다.                           |
| -L  | -l 옵션과 유사하지만, 일치하는 행이 없는 파일의 이름만을 출력한다. --files-wihtout-match로도 지정할 수 없다.                       |
| -n  | 일치하는 행 앞에 파일의 행 번호를 붙인다. --line-number로도 지정할 수 있다.                                              |
| -h  | 복수 파일 검색에서, 파일명의 출력을 숨긴다. --no-filename로도 지정할 수 있다.                                             |


충분한 grep 탐색을 위해, 검색을 위한 텍스트를 생성하자.


``` shell
┌──(kali㉿kali)-[~]
└─$ ls /bin > dirlist-bin.txt
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ ls /usr/bin > dirlist_usr_bin.txt
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ ls /sbin > dirlist-sblin.txt     
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ ls /usr/sbin > dirlist-usr-sblin.txt
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ ls dirlist*.txt                     
dirlist-bin.txt  dirlist-sblin.txt  dirlist-usr-sblin.txt  dirlist_usr_bin.txt

```

다음과 같이 파일 목록을 간단히 검색할 수 있다.

``` shell
┌──(kali㉿kali)-[~]
└─$ grep bzip dirlist*.txt
dirlist-bin.txt:bzip2
dirlist-bin.txt:bzip2recover
dirlist-usr-sbin.txt:bzip2
dirlist-usr-sbin.txt:bzip2recover

```

이 예제에서 grep은 ==bzip 문자열을 가진 모든 파일 목록을 검색==해서 dirlist-bin.txt 파일 내의 일치된 것을 찾아낸다. 만약 일치된 것 자체보다 일치된 내용을 포함한 파일에만 관심이 있다면 -l 옵션을 사용할 수 있다.

``` shell
┌──(kali㉿kali)-[~]
└─$ grep -l bzip dirlist*.txt
dirlist-bin.txt
dirlist-usr-bin.txt

```

만약 정반대로, 일치하지 않는 파일들만 보고 싶다면 다음과 같이 할 수 있다(잘 안되는 부분이라서 다시 한번 공부해볼 것).

``` shell
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ grep -L bzip dirlist*.txt
dirlist-sbin.txt
dirlist-usr-sbin.txt

```
