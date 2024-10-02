
### 파일 압축 및 압축 해제하기

gzip 프로그램은 하나 이상의 파일을 압축할 때 사용된다. 이 프로그램을 실행하면 원본 파일은 압축 버전의 파일로 대체된다. 이와 함께 사용되는 gunzip 프로그램은 압축 파일의 이전의 원본 상태로 복원시켜준다. 다음의 예제를 본다.

``` shell
┌──(kali㉿kali)-[~]
└─$ ls -l /etc > foo.txt      
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ ls -l foo.*         
-rw-rw-r-- 1 kali kali 19944  9월 23일  06:54 foo.txt┌──(kali㉿kali)-[~]
└─$ gzip foo.txt        
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ ls -l foo.*
-rw-rw-r-- 1 kali kali 3487  9월 23일  06:54 foo.txt.gz
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ dir
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos  foo.txt.gz  playground
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ ls -l foo.*
-rw-rw-r-- 1 kali kali 3487  9월 23일  06:54 foo.txt.gz
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ gunzip foo.txt
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ ls -l foo.*
-rw-rw-r-- 1 kali kali 19944  9월 23일  06:54 foo.txt
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ dir 
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos  foo.txt  playground

                                                                  
```

이 예제에서 디렉토리 목록을 저장한 foo.txt라는 이름의 텍스트 파일을 만들었다. 그 다음 gzip을 실행하여 원본 파일을 foo.txt.gz 라는 압축 파일로 바꿨다. `foo.*` 포함된 디렉토리 내용을 보면 원본 파일이 압축 파일로 바뀌었고, 원본에 비해 1/5 수준의 크기밖에 되지 않음을 알 수 있다. 또한 압축 파일은 원본과 똑같은 퍼미션과 날짜, 시간을 가지고 있다는 것을 알 수 있다.

이제는 gunzip 프로그램을 실행하여 파일 압축을 해체해보자. 그럼 우리는 압축 파일이 원본 파일로 바뀌고, 역시 퍼미션과 타임스탬프는 그대로 유지됨을 확인할 수 있다.

gzip은 많은 옵션을 지원하는데, 그 중 일부를 [표 18-1]에서 확인해보도록 하자.


- 표 [18-1] gzip 옵션


| 옵션        | 설명                                                                                                                                       |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| -c        | 표준 출력에 결과를 쓰고, 원본 파일을 유지함. --stdout 및 -to-stdout로도 사용할 수 있다.                                                                             |
| -d        | 압축 해제. gzip과 이 옵션을 함께 쓰면 gunzip과 같은 동작을 하게 된다. --decompress 또는 --uncompress로도 사용할 수 있다.                                                  |
| -f        | 압축 파일이 이미 존재해도 압축을 실행. --force로도 사용할 수 있다.                                                                                               |
| -h        | 도움말. --help로도 사용할 수 있다.                                                                                                                  |
| -l        | 각각의 압축 파일별로 압축 정보를 표시. --list로도 사용할 수 있다.                                                                                                |
| -r        | 커맨드라인의 인자가 디렉토리인 경우, 디렉토리를 순환하면서 포함되어 있는 파일들을 압축한다. --recursive로도 사용할 수 있다.                                                              |
| -t        | 압축 파일의 무결성을 검사한다. --test로도 사용할 수 있다.                                                                                                     |
| -v        | 압축되는 과정을 자세히 표시한다. --verbose로도 사용할 수 있다.                                                                                                 |
| -*number* | 압축 정도를 설정. number에는 1(가장 빠르지만, 압축률은 최소)부터 9(가장 느리지만, 압축률은 최대)까지의 정수만을 올 수 있다. 숫자 1과 9 대신 각각 --test와 --best로도 사용할 수 있다. 기본값은 6으로 설정되어 있다. |

앞의 예제를 다시 살펴보자.

``` shell
┌──(kali㉿kali)-[~]
└─$ ls -l foo.txt       
-rw-rw-r-- 1 kali kali 19944  9월 24일  05:52 foo.txt
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ gzip foo.txt  
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ gzip -tv foo.txt.gz
foo.txt.gz:      OK
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ gzip -d foo.txt.gz 

```


여기서 우리는 foo.txt 파일을 foo.txt.gz라는 압축 파일로 바꾸었다. 그리고 압축 파일의 무결성을 검사하기 위해서 -t와 -v 옵션을 함께 사용한다. 마지막으로 파일을 원본 상태로 되돌린다.

또한 gzip은 표준 입력과 표준 출력을 흥미롭게 활용할 수 있다.

``` shell
┌──(kali㉿kali)-[~]
└─$ ls -l /etc | gzip > foo.txt.gz

```

이 명령은 위에 표시된 디렉토리를 압축한다.

gzip 파일의 압축을 해제하는 gunzip 프로그램은 파일명이 .gz로 끝날 것이라고 가정하고 작업을 진행하기 때문에 굳이 .gz를 쓰지 않아도 된다. 단, 압축 해제 대상의 파일명이 기존에 압축되지 않은 파일명과 혼동되지 않는 경우에만 해당한다.

``` shell
┌──(kali㉿kali)-[~]
└─$ gunzip foo.txt

```

압축된 텍스트 파일의 내용을 보고 싶다면 다음과 같이 할 수 있다.

``` shell
┌──(kali㉿kali)-[~]
└─$ gunzip -c foo.txt | less

gzip: foo.txt: not in gzip format

```

또는 gzip이 지원하는 프로그램인 zcat을 이용해서 gunzip -c와 같은 작업을 실행할 수 있다. gzip 압축 파일에 대해서 cat 명령어처럼 사용하면 된다.

``` shell
┌──(kali㉿kali)-[~]
└─$ zcat foo.txt.gz | less

```

저자주: zless라는 프로그램도 있는데, 이것은 앞에서 사용된 파이프라인의 기능과 동일한 기능을 한다.