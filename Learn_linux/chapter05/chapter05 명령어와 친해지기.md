
이전 챕터: [[chapter06 리다이렉션]]


이번에 다룰 명령어

- [[type]]- 명령어의 이름이 어떻게 표시되는지 확인
- [[which]]- 실행 프로그램의 위치 표시
- [[man]] –명령어의 man 페이지 표시
- [[apropos]] –적합한 명령어 리스트 표시
- [[info]] –명령어 정보 표시
- [[whatis]] –명령어에 대한 짧은 설명 표시
- alias –명령어에 별칭 붙이기


---
# 명령어란 구체적으로 무엇인가?

명령어는 다음 네 가지 이다.


- 명령어란 /usr/bin 디렉토리에서 본 파일처럼 실행 프로그램을 말한다. 이러한 범주에서 프로그램은 C , C++ 언어로 작성된 프로그램처럼 컴파일된 바이너리 형식의 파일이거나 쉘(shell), 펄(Perl), 파이썬(Python), 루비(Ruby) 같은 스크립트 언어로 만든 프로그램일 수 있다.
- 명령어란 ‘쉘에 내장되어 있는 명령어’이다. bash 쉘- 빌트인(shell builtins)이라고 하는 다수의 명령어를 내부적으로 지원한다. cd 명령어가 바로 그런 예이다.
- 명령어란 ‘쉘 함수’이다. 쉘 함수란 시스템 환경에 포함된 쉘 스크립트의 미니어처 같은 존재이다. 나중에 시스템 환경설정 방법과 쉘 함수 작성벙에 대해 살펴볼 것이기에 지금은 이렇나  함수가 존재한다는 것과 알아보자.
- 명령어란 ‘별칭’이다. 다른 명령어로부터 우리만의 명령어를 새롭게 정의할 수 있다.

---

# 명령어 도움말 보기


명령어가 무엇인지에 대한 이해를 통하여 각 명령어마다 가지고 있는 도움말을 검색할 수 있다.

- [[help]] -쉘 빌트인 도움말을 보기

---
# 별칭으로 나만의 명령어 만들기


우리는 alias 명령어를 이용하여 우리만의 명령어를 만들 것이다.

하지만 시작하기에 앞서 간단한 커맨드라인의 트릭을 알려 주겠다. 바로 세미콜론으로 각 명령어를 구분하여 한 줄에 하나 이상의 명령어를 입력하는 것이 가능하다는 것이다. 다음과 같이 사용한다.

ex)
*command1; command2; command3;*

다음 예제를 본다.

``` shell
kgm0917@DESKTOP-DT2VQLB:~$ cd /usr; ls; cd -

bin include lib32 libexec local share

games lib lib64 libx32 sbin src

/home/kgm0917
```

지금 보는 바와 같이 한줄에 세 개의 명령어가 이어져 있다. 먼저 /usr로 디렉토리를 변경하고 그 다음 디렉토리를 표시한 뒤 cd –를 이용하여 원래의 디렉토리로 돌아왔다. 처음으로 돌아온 셈이다.

이제 alies를 이용하여 이 명령 배열을 새로운 명령어로 변환해보는 것이다. 첫 번째로 해야 할 것은 새 명령어에 지어줄 이름을 생각하는 것이다. test로 시작해보기로 하고 test 라는 이름이 기존에 사용되고 있는지를 알아본다. type 명령어를 통하여 찾아볼 수 있다.

```
kgm0917@DESKTOP-DT2VQLB:/usr/bin$ type test

test is a shell builtin
```

이는 타입이라는 이름이 이미 사용중이라는 의미이다. foo 라는 이름으로 시도해보자.


``` shell
kgm0917@DESKTOP-DT2VQLB:/usr/bin$ type foo

-bash: type: foo: not found
```

이렇게 해서 별칭을 만들어보자.


``` shell
kgm0917@DESKTOP-DT2VQLB:/usr/bin$ alias foo=cd /usr; ls; cd -
```

(ps : 원래 여기서는 = 뒤에 작은 따옴표가 온다. 그런데 오류가 떠서 여기서는 생략한다.)

이 명령의 구조를 알아본다.


alias *name=‘string’*


**alias** 명령어 다음에 별명을 입력하고 곧바로(빈칸 없이)= 기호를 입력한 후 이름을 할당한다는 의미를 지닌 따옴표를 추가한다. 별칭이 정의되고 나면 쉘 어디에서든지 명령어로써 사용할 수 있게 된다.

직접 실습해보자.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ foo

bin include lib32 libexec local share

games lib lib64 libx32 sbin src

/home/kgm0917
```

별칭을 확인하기 위해 **type** 명령어를 사용할 수 있다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ type foo

foo is aliased to `cd /usr; ls; cd -‘
```


별칭을 삭제하려면 다음과 같이 **’unalias‘** 명령어를 입력하면 된다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ unalias foo

kgm0917@DESKTOP-DT2VQLB:~$ type foo

-bash: type: foo: not found
```


별칭을 정할 때 기존 명령어의 이름과 중복되지 않도록 일부러 피했으나 가끔은 중복하여 사용하는 것이 바람직할 때도 있다. 일반적인 명령어를 실행할 때 자주 사용하는 옵션을 적용해 별칭을 만들기도 한다. 예를 들어 ls 명령어는 색상을 표시하기 위해 별칭을 쓰기도 한다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ type ls

ls is aliased to ls —color=auto
```


사용자 환경에 정의된 모든 별칭을 확인하려고 하면 그냥 ‘alias’ 명령어를 사용하면 된다. 여기서 각각의 별칭에 대해 알아본다.

커맨드라인의 사소한 문제는 바로 쉘 셰션이 종료될 때 별칭도 사라진다는 것이다. 이는 로그인 시 환경을 설정하는 파일에 별칭을 추가하는 방법에 대해 나중에 알아볼 것이다.


---
다음 챕터: [[chapter06 리다이렉션]]