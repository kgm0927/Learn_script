

---
# 표준 입력 재지정

지금까지는 표준 입력을 사용하는 어떤 명령어도 본 적이 없다. 그래서 하나를 소개하려고 한다.


cat- 파일 붙이기


cat 명령어는 다음과 같이 하나 이상의 파일을 읽어 들여서 표준 출력으로 그 내용을 복사한다.

`cat [file...]


cat 명령어가 type과 유사하다고 할 수 있으나, cat 명령어는 페이지 구분 없이 파일을 표시할 수 있다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ cat ls-output.txt

ls: cannot access '/bin/usr': No such file or directory
```

(현재 이런 입력으로는 나오는 것이 없다. 그래서 그냥 책에 있는 내용을 요약하겠다.)

이런 명령을 입력하면 ls-output.txt 파일의 내용을 표시한다. cat 은 주로 짧은 텍스트 파일을 표시할 때 사용하고, 또한 여러 파일을 인자로 허용하기 때문에 하나로 합치는 데에도 사용이 가능하다. 대용량 파일을 다운로드 하려는데, 파일이 여러 개로 나뉘어 있을 경우, 파일명은 다음과 같다.


`movie.mpeg.001 movie.mpeg.002 ... movie.mpeg.099`

다음과 같이 명령어를 사용해서 작업을 수행할 수 있다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ cat movie.mpeg.0* > movie.mpeg
```


와일드카드는 항상 정렬된 순서로 확장되기 때문에 명령 인자는 올바른 순서로 나열될 것이다.

cat 명령어에 아무런 명령 인자 없이 사용하면 어떤 현상이 나타날까?


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ cat
```

아무 일도 일어나지 않는다. 하지만 명령어가 수행해야 하는 직업을 정확히 하고 있다.

cat 명령어에 아무런 명령 인자가 따라오지 않는다면 표준 입력으로부터 데이터를 읽는다. 그리고 기본적으로 표준 입력은 키보드로 연결되어 있기 때문에 무엇인가 입력되기를 기다리고 있는 것이다.

다음과 같이 한다.

``` shell
kgm0917@DESKTOP-DT2VQLB:~$ cat

The quick brown fox jumped over the lazy dog.
```

그 다음 ctrl-D(ctrl키를 누를 채 D를 입력한다.)를 입력하면 표준 입력에 EOF 문자가 입력된다.

``` shell
kgm0917@DESKTOP-DT2VQLB:~$ cat

The quick brown fox jumped over the lazy dog.

The quick brown fox jumped over the lazy dog.
```

파일명 위치에 아무것도 입력하지 않기에, **cat**은 표준 입력을 표준 출력 위치에 복사된다. 따라서 텍스트가 반복된 것을 확인할 수 있다. 이러한 작업은 짧은 텍스트 파일을 만들 때 사용할 수 있다.

짧은 텍스트 파일을 만들 때 사용할 수 있는데, 우리는 lazy_dog.txt라는 텍스트 파일을 생성하기 위해서 다음과 같이 할 수 있다.

``` shell
kgm0917@DESKTOP-DT2VQLB:~$ cat > lazy_dog.txt

The quick brown fox jumped over the lazy dog.
```

명령어 다음에 파일을 쓰고 싶은 텍스트를 입력하고, 끝에 **ctrl-D**를 꼭 입력해야 한다. 이로써 우리가 커맨드라인을 파일을 만들었다. 결과를 보기 위해 cat 명령어를 이용해 파일을 복사할 수 있다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ cat lazy_dog.txt

The quick brown fox jumped over the lazy dog.
```

우리는 cat이 어떻게 표준 입력을 허용하는지 알기 때문에 추가적으로 표준 입력을 재지정해보자.


``` shelll
kgm0917@DESKTOP-DT2VQLB:~$ cat < lazy_dog.txt

The quick brown fox jumped over the lazy dog.
```

< 리다이렉션 연산자를 사용하여 키보드로 연결된 표준 입력 방향을 lazy_dog.txt 파일로 변경했다. 여기서 우리는 단순히 파일명만을 입력한 것과 똑같은 결과를 볼 수 있다. 이렇게 연산자를 입력하는 것이 파일명만 입력하는 것과 비교해서 특별히 유용한 것은 아니지만 표준 입력을 파일로 재지정했다는 사실을 보여준다.


---

# 파이프라인

표준 입력으로부터 데이터를 읽고, 표준 출력으로 데이터를 전송하는 명령어의 능력은 **파이프라인**이라고 하는 쉘의 기능으로 보다 더 응용될 수 있다. 파이프 연산자인 |(수직 바) 기호를 이용하여 명령어의 표준 출력을 또 다른 명령어의 표준 입력과 연결시킬 수 있다.

command1 | command2

이러한 기능을 충분히 드러내려면 몇 가지의 명령어가 더 필요하다. 우리가 표준 입력을 허용하는 명령어를 이미 알고 있다고 했던 것을 기억하는가? 바로 less 명령어다. 어떠한 명령어든 그 출력 내용을 페이지 단위로 표준 출력에 표시하도록 less 명령어를 사용할 수 있다.

``` shell
kgm0917@DESKTOP-DT2VQLB:~$ ls -l /usr/bin | less
```


이것은 아주 편리하다! 이 기능을 활용하면 표준 출력을 만들어내는 어떤 명령어라도 그 결과를 편리하게 확인할 수 있다.




---
### cat - 파일과 표준 출력을 연결

cat 프로그램은 흥미로운 옵션이 많이 있다. 그 중 다수는 텍스트 내용을 좀 더 시각적으로 보이게 한다. 한 예로 -A 옵션은 텍스트 내의 비출력 문자를 표시한다. 눈에 보이는 텍스트 이외에 내장된 제어 문자를 알고 싶을 때가 있을 것이다. 이 중 가장 흔히 사용하는 것은 탭 문자(스페이스가 이니라)와 개행 문자, MS-DOS 방식의 텍스트 파일에서 종종 나타나는 행 끝 문자들이 있다. 또 다른 흔한 상황은 공백들이 뒤따라오는 텍스트 줄을 포함한 파일이다.

이제 cat을 기초적인 워드 프로세서로 사용하여 테스트 파일을 만들자. 이를 위해서는, cat 명령어를 입력하고 텍스트를 타이핑한다. 각 줄의 끝은 적절히 엔터를 사용하여 나누고 ctrl-D로 cat에 파일의 끝을 알릴 수 있다. 이 예제에서는 선두를 탭으로 입력하고 스페이스 몇 개로 끝을 맺는다.

``` shell
┌──(kali㉿kali)-[~]
└─$ cat > foo.txt   
The quick brown fox jumped over the lazy dog.
^C
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ cat foo.txt  
The quick brown fox jumped over the lazy dog.

```

다음은 -A 옵션을 사용하여 텍스트를 표시한 것이다.

``` shell
┌──(kali㉿kali)-[~]
└─$ cat -A foo.txt
The quick brown fox jumped over the lazy dog.$
                                                  
```

결과에서 볼 수 있듯이, 텍스트 안의 탭 문자는 `^I`로  표현된다. 이 표기법은 "ctrl -I"를 의미하고 탭 문자와 동일하다. 또한 실제 줄 끝에 나타난 $는 스페이스가 뒤따라옴을 가리킨다.

>[!tip] MS-DOS 텍스트 VS. 유닉스 텍스트
>cat으로 비출력 문자를 보기 위해서는 원하는 이유들 중 하나는 숨겨진 캐리지 리턴(carriage return)을 찾기 위해서다. 숨겨진 캐리지 리턴은 어디서 비롯된 것일까? 바로 도스와 윈도우즈! 유닉스와 도스는 텍스트 파일에서 동일한 방식으로 행 끝을 정의하지 않는다. 유닉스는 라인피드(linefeed) 문자(ASCII 10)로 행을 끝낸다. 반면 MS-DOS 계열은 각 행을 마치기 위해 캐리지 리턴(ASCII 13)과 라인피트 문자열을 사용한다.
>
>도스에서 유닉스 포맷으로 파일을 변환하기 위한 여러 방법들이 있다. 많은 리눅스 시스템에서 dos2unix와 unix2dos라는 프로그램으로 텍스트 파일을 도스 포맷과 유닉스 포맷 간의 전환이 가능하다. 하지만 dos2unix 프로그램이 시스템에 없다면, 그래도 걱정은 하지 마라. 도스에서 유닉스 포맷으로 텍스트를 변환하는 절차는 매우 간단하다. 단순히 문제가 되는 캐리지 리턴만을 제거하면 된다. 그것은 이 장에서 후에 논의하게 될 두 쌍의 프로그램에 의해 쉽게 수행된다.

또한 cat은 텍스트를 수정하는 옵션이 있다. 두 가지 가장 유명한 옵션은 줄 번호를 매기는 **-n**과 여러 공백 줄을 제거하는 **-s**이다. 다음과 같이 보여줄 수 있다.


``` shell

┌──(kali㉿kali)-[~]
└─$ cat > foo.txt  
The quick brown fox

jumped over the lazy dog.
^Z
zsh: suspended  cat > foo.txt
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ cat -ns foo.txt
     1  The quick brown fox
     2
     3  jumped over the lazy dog.

```

이 예제에서는 새 버전의 foo.txt 파일을 만든다. 두 줄의 공백으로 구분된 두 줄의 텍스트를 가진 파일이다. cat에 **-ns** 옵션을 사용하면 여분의 공백 줄은 제거되고 남은 줄에는 번호가 매겨진다. 이것이 텍스트를 처리하는 대단한 뭔가는 아니다. 그저 하나의 차이일 뿐이다.