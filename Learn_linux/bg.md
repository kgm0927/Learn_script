
---
# 프로세스 제어


이번에는 프로세스를 제어해보자. 실험을 위해 xlogo라는 작은 프로그램을 실험대상으로 할 것이다. xlogo 프로그램은 X 윈도우 시스템(그래픽 화면을 표시하게 해 주는 하부 엔진)이 제공하는 샘플 프로그램이다. 그것은 단순히 X라는 로고를 표시하는 크기조절 가능한 윈도우이다. 먼저 실험 대상에 대해 알아볼 것이다.

명령어를 입력한 후, 화면 어딘가에 로고를 가진 작은 창이 표시된다.

그 윈도우의 크기 변경으로 xlogo가 실행 중임을 알 수 있다. 만약 로고가 새로운 크기로 다시 그려지면 프로그램은 실행 중이다.

이때 쉘 프롬프트로 돌아가지 않는데, 이는 쉘이 프로그램 종료를 기다리고 있기 때문이다. 우리가 지금까지 실행했던 다른 프로그램도 xlogo와 다를 바 없다. xlogo 창을 닫으면 비로소 프롬프트로 돌아온다.


- 프로세스 인터럽트하기

다시 xlogo 프로그램을 실행하여 무슨 일이 벌어지는지 살펴본다. 먼저 xlogo 명령어를 입력하고 프로그램 실행을 확인한다. 그 다음으로 터미널 창으로 돌아와 ctrl-C를 눌러본다.


``` shell
┌──(kali㉿kali)-[~]

└─$ xlogo

^C

┌──(kali㉿kali)-[~]

└─$
```

터미널에서 ctrl-C를 누르면 프로그램을 중단시킨다. 이는 프로그램 종료를 공손하게 요청하는 것이다. ctrl-C를 누르면 xlogo 창은 닫히고 쉘 프롬프트로 돌아간다.


전부는 아니지만 이 입력으로 프로그램을 중단시킬 수 있다.





- 프로세스를 백그라운드로 전환


xlogo 프로그램의 종료 없이 쉘 프롬프트로 돌아가기를 원한다고 할 때, 프로그램을 백그라운드로 전환하는 것으로 할 것이다. 터미널이 **포그라운드**(쉘 프롬프트처럼 화면에 표시되는 것을 가진)와 **백그라운드**(화면 뒤로 숨겨진 것을 가진)를 가진다고 하자. **==다음 기호처럼 & 기호를 명령어와 함께 사용하면 프로그램을 실행 즉시 백그라운드로 이동한다.==**


``` shell
┌──(kali㉿kali)-[~]

└─$ xlogo &

[1] 66060

┌──(kali㉿kali)-[~]

└─$
```


명령어 입력 후에 xlogo 창이 나타나고 쉘 프롬프트로 돌아온다. 하지만 숫자도 함께 출력되는다. 이 메시지는 **작업 제어**(job control)이라고 부르는 쉘 기능의 일부다. 쉘은 이 메시지로

PID가 66060인 1번([1]) 작업이 시작되었다는 것을 알려준다. ps를 실행하면 이 프로세스를 볼 수 있다.


```
┌──(kali㉿kali)-[~]

└─$ ps

PID TTY TIME CMD

1323 pts/0 00:00:04 zsh

66060 pts/0 00:00:00 xlogo

69463 pts/0 00:00:00 ps
```

또한 쉘의 작업 제어 시스템은 터미널에 실행 중인 작업을 나열해준다. [[jobs]] 명령어를 사용하면 다음과 같은 목록을 볼 수 있다.