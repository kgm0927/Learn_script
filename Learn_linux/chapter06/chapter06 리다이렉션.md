
이전 챕터: [[chapter05 명령어와 친해지기]]


이 장에서는 커맨드라인의 기능 중 하나인 입출력 방향 지정(I/O 리다이렉션)을 파헤치려고 한다. I/O는 입력/출력을 의미하고, 명령은 리타이렉션을 통해 파일을 입력받을 수 있고, 또한 파일로 출력할 수 있다. 뿐만 아니라 강력한 명령어 파이프라인을 만들기 위해 필요한 명령어들을 연결할 수 있다.


- [[cat]]- 파일 연결하기
- [[sort]]- 텍스트 라인 정렬하기
- [[uniq]]- 중복 줄을 알리거나 생략하기
- [[wc]]- 각 파일의 개행 및 단어 개수, 파일 바이트 출력하기
- [[grep]]- 패턴과 일치하는 라인 출력하기
- [[head/tail]]- 파일의 첫 부분 출력하기/ 파일의 마지막 부분 출력하기
- [[tee]]- 표준 입력을 읽고 표준 출력 및 파일에 쓰기

---

# 표준 입출력과 표준 오류


우리가 지금까지 사용한 많은 프로그램들은 일종의 출력을 만들어 낸다. <mark style="background: #ADCCFFA6;">첫 번째는 프로그램의 결과로,</mark> 즉 **프로그램이 출력하도록 설계한 데이터**다. <mark style="background: #ADCCFFA6;">두 번째는 프로그램이 어떻게 돌아가고 있는지 말해주는</mark> **상태 및 오류 메시지 형식**이다.

“모든 것은 파일이다.”라는 유닉스 정신을 상기하며 다시 보자. [[ls]]과 같은 프로그램은 사실 표준 **출력(stdout)** 이라고 불리는 특수한 파일에 이 명령어에 대한 결과를 보내고 **표준 오류(stderr)** 라는 또 다른 파일에 그 상태 메시지를 전송한다. <mark style="background: #FF5582A6;">기본적으로 표준 출력과 오류 모두 화면에 연결되어 있고 디스크 파일에 따로 저장되지 않는다.</mark>

게다가 많은 프로그램들이 **표준 입력(stdin)** 이라고 부르는 곳에서 입력 내용을 가져오고 극서은 기본적으로 키보드에 직접 연결되어 있다.

<mark style="background: #ADCCFFA6;">입출력 방향 지정(I/O 리다이렉션) 기능으로 출력과 입력의 방향을 변경할 수 있다.</mark> 일반적으로 출력은 화면에 나타나고 입력은 키보드로부터 인식되나 I/O 리다이렉션으로 이런 방식을 변경할 수 있다.


### 표준 출력 재지정


**I/O 리다이렉션**은 <mark style="background: #ADCCFFA6;">출력 방향을 재정의 할 수 있다.</mark> <mark style="background: #BBFABBA6;">화면에 출력하는 대신 다른 파일에 출력되도록 지정하기 위해서는 파일명 앞에 **>** 리다이렉션 연산자를 사용한다.</mark> 이는 명령어 출력결과를 파일에 저장하는 것이 종종 유용하기 때문이다. 예를 들면, [[ls]] 명령어 결과를 화면 대신 ls-output.txt 파일에 보내지도록 지정했다고 하자.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ ls -l /usr/bin > ls-output.txt
```

/usr/bin 디렉토리에 있는 긴 목록을 불러와서 ls-output.txt 파일로 보냈다. 그럼 결과를 확인해보자.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ ls -l ls-output.txt

-rw-r--r-- 1 kgm0917 kgm0917 47411 Jan 28 08:12 ls-output.txt
```

매우 큰 텍스트 파일이 하나 보일 것이다. [[less]] 명령어로 파일을 들여다보면 이 파일 안에 ls의 결과가 정말로 들어있다는 것을 알게 될 것이다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ less ls-output.txt
```

이제 리다이렉션하는 작업을 계속한다. 이번에는 좀 더 복잡하게 들어가 본다. 우선 존재하지 않는 디렉토리의 이름을 바꾸고자 한다.


```shell
kgm0917@DESKTOP-DT2VQLB:~$ ls -l /bin/usr > ls-output.txt

ls: cannot access '/bin/usr': No such file or directory
```


오류 메시지가 나타났다. 당연한 결과다. <mark style="background: #ADCCFFA6;">이는 존재하지 않는 /bin/usr이란 디렉토리를 입력했기 때문이다.</mark> 하지만 왜 오류 메시지는 ls-output.txt 파일이 아닌 화면에 표시되었을까? <mark style="background: #ADCCFFA6;">그 이유는 바로 잘 만들어진 다른 유닉스 프로그램들처럼 ls도 오류 메시지를 표준 출력으로 전송하지 않고 표준 오류로 전송하기 때문이다.</mark> 
표준 오류가 아닌 표준 출력만 재지정했기 때문에 오류 메시지는 여전히 화면에 나타나게 된다. 이제 곧 표준 오류도 재지정하는 법을 다룰 것이다. 그전에 출력 파일을 좀 더 살펴보자.



```shell
kgm0917@DESKTOP-DT2VQLB:~$ ls -l ls-output.txt

-rw-r--r-- 1 kgm0917 kgm0917 0 Jan 28 11:02 ls-output.txt
```


<mark style="background: #ADCCFFA6;">현재 파일 크기는 0이다. 그 이유는 > 리다이렉션 연산자로 출력 방향을 지정할 때, 목적 파일은 항상 처음부터 다시 작성되었기 때문이다.</mark> ls 명령어가 아무런 결과를 만들지 못했고 단지 오류 메시지만을 만들었기 때문에, 리다이렉션 명령은 파일을 처음부터 다시 쓴 뒤 오류 때문에 중단되어 잘림 현상이 생겼다.

사실, 언제든 파일을 잘라낼 필요가 있을 때(또는 새로운 빈 파일을 만들 때)다음과 같이 트릭을 사용할 수 있다.


```shell
kgm0917@DESKTOP-DT2VQLB:~$ > ls-output.txt
```

명령어 없이 리다이렉션 연산자만을 사용해서 기존 파일을 분리하거나 새 파일을 만들 수 있다.

그럼 파일 덮어쓰기 때신 **출력할 내용을 파일에 이어서 작성할 수 있을까?** 이를 위해서 `>>` 리다이렉션 연산자를 다음과 같이 사용해보자.


```shell
kgm0917@DESKTOP-DT2VQLB:~$ ls -l /usr/bin >> ls-output.txt
```

`>>`<mark style="background: #ADCCFFA6;">연산자를 사용하면 파일에 이어 쓰기가 가능해진다.</mark> 존재하지 않을 시 `>` 연산자를 사용하는 것처럼 파일이 생성이 된다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ ls -l /usr/bin >> ls-output.txt

kgm0917@DESKTOP-DT2VQLB:~$ ls -l /usr/bin >> ls-output.txt

kgm0917@DESKTOP-DT2VQLB:~$ ls -l /usr/bin >> ls-output.txt

kgm0917@DESKTOP-DT2VQLB:~$ ls -l ls-output.txt

-rw-r--r-- 1 kgm0917 kgm0917 189644 Jan 28 11:18 ls-output.txt
```


같은 명령을 세 번 반복해서 입력했고, 그 결과 파일 크기가 세 배가 되었다.



### 표준 오류 재지정

<mark style="background: #FF5582A6;">표준 오류를 재지정할 때는 리다이렉션 연산자가 필요 없다.</mark> 다만, <mark style="background: #ADCCFFA6;">파일 디스크립터를 참조한다. 프로그램은 번호로 지정된 파일 스트림 중 어디에라도 출력할 수 있다.</mark> 쉘은 내부적으로 이들을 각각 **0,1,2 번 파일 디스크립터**로 표현한다.

쉘은 파일 디스크립터 번호를 이용해서 재지정할 수 있는 표기를 지원한다. <mark style="background: #ADCCFFA6;">표준 오류는 파일 디스크립터 2와 같은데 표준 오류를 재지정할 때 이 표기법을 사용할 수 있다.</mark>


```
kgm0917@DESKTOP-DT2VQLB:~$ ls -l /bin/usr/ 2> ls-error.txt
```

==파일 디스크립터 2는 리다이렉션 연산자 바로 앞에 위치하고 ls-error.txt 파일에 표준 오류 메시지를 보낸다.==



### 표준 출력과 표준 오류를 한 파일로 재지정


명령어의 결과를 모두 한 파일에 저장하고 싶을 시, 동시에 표준 출력과 ==표준 오류를 재지정해야 한다.== 이는 두 가지 방법이 있다.

일반적인 방법으로 쉘의 예전 버전에서 사용할 수 있다.


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ ls -l /bin/usr > ls-output.txt 2>&1
```

위의 방법으로 <mark style="background: #ADCCFFA6;">두 번의 리다이렉션이 이루어진다.</mark> 먼저 표준 출력이 ls-output.txt 파일로 재지정되고, ==**2>&1의 입력으로 파일 디스크립터 2**(표준 오류)가 **파일 디스크립터 1**(표준 출력)으로 재지정되도록 한다.==[^1]


두 번째 방법은 bash의 최신 버전에서 사용 가능한 리다이렉션을 좀 더 간소한 것이다.


```shell
kgm0917@DESKTOP-DT2VQLB:~$ ls -l /bin/usr &> ls-output.txt
```


이 예제에서는 단일 표기법 `&>`를 사용하고 있다. <mark style="background: #ADCCFFA6;">이것은 표준 출력과 표준 오류를 ls-output.txt 파일로 재지정한다.
</mark>


### 원치 않은 출력 제거


명령어의 출력 결과를 원치 않고 그것을 단지 버리고 싶을 때, 특히 오류 및 상태 메시지가 그러하다. <mark style="background: #ADCCFFA6;">시스템은 /dev/null 이라는 특수한 파일로 출력방향을 지정함으로써 이 문제의 해결 방법을 제공하고 있다.</mark> 이 파일은 **비트 버킷**(bit bucket)이라고 불리는 시스템 장치로 입력을 받고 아무것도 수행하지 않는다.

오류 메시지를 숨기기 위해 다음과 같이 해 본다.


```shell
kgm0917@DESKTOP-DT2VQLB:~$ ls -l /bin/usr 2> /dev/null
```


- ***==부연 설명==***

유닉스 세상의 /dev/null

비트 버킷은 오래된 유닉스 개념이다. 그 보편성으로 인해 유닉스 세상에서 자주 출몰한다. 그래서 만일 누군가 여러분의 답변을 “dev null”로 보낸다고 얘기하면 이제 그것이 무엇을 의미하는지 알 수 있을 것다. 더 많은 예제는 위키피디아에서 볼 수 있다.

[^1]: 저자주: 리다이렉션 간의 순서는 매우 중요하다. 표준 오류의 재지정은 항상 표준 출력을 재지정한 뒤에 이루어져야 한다. 그렇지 않으면 올바르게 작동하지 않는다. 앞의 예제에서 > ls-output.txt 2>&1 명령은 표중 오류를 ls-output.txt 파일로 재지정하는 데 만약 2>&1 > ls-output.txt 으로 순서가 바뀌면 표준 오류의 출력 방향은 화면이 된다.

---

다음 챕터: [[chapter07 확장과 인용]]