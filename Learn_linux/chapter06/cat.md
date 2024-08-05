

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




