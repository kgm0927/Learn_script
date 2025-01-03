
# 실행 파일의 위치 표시

때때로 시스템에 하나의 실행 프로그램이 여러 버전으로 설치되곤 한다. 데스크톱 시스템에서는 흔한 경우는 아니나, 대용량 서버에는 가능하다. <mark style="background: #ADCCFFA6;">실행한 프로그램의 정확한 위치를 파악하기 위해 다음과 같이 which 명령어를 사용한다.</mark>


```
kgm0917@DESKTOP-DT2VQLB:~$ which ls

/usr/bin/ls
```


==which 명령어는 오직 실행 프로그램만을 대상으로 한다.== 실행 프로그램을 대신하는 그 어떤 **빌트인**(builtin)이나 별칭에는 동작하지 않는다. 쉘 빌트(예, [[cd]])에 `which` 명령어를 사용하면 아무런 응답을 못 받거나 오류 메시지를 보게 된다.

```
kgm0917@DESKTOP-DT2VQLB:~$ which cd

kgm0917@DESKTOP-DT2VQLB:~$

(아무것도 나오지 않는다.)
```