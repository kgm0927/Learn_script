


---

# killall로 다수의 프로세스에 시그널 보내기


killall 명령어를 사용하면 명시된 프로그램 또는 사용자 이름과 일치하는 다수의 프로세스에 시그널을 보내는 것도 가능하다. 사용법은 다음과 같다.


`killall [-u user][-signal] name ...`

``` shell
┌──(kali㉿kali)-[~]

└─$ xlogo &

[2] 54025

┌──(kali㉿kali)-[~]

└─$ xlogo &

[3] 54113

┌──(kali㉿kali)-[~]

└─$ killall xlogo

[2] terminated xlogo

[3] - terminated xlogo
```

kill 명령어와 마찬가지로, 사용자가 소유하지 않은 프로세스들에 시그널을 보낼 때는 반드시 슈퍼유저야 함을 명심해라.