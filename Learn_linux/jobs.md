
---

쉘의 작업 제어 시스템은 터미널에 실행 중인 작업을 나열해준다. jobs 명령어를 사용하면 다음과 같은 목록을 볼 수 있다

```
┌──(kali㉿kali)-[~]

└─$ jobs

[1] + running xlogo
```


우리가 가지고 있는 하나의 작업을 보여준다. 그 작업은 번호가 1이고, 실행 중이며, 명령어는 xlogo라는 것을 뜻한다.

(조금 다른 점은 xlogo &를 했음에도 & 연산자는 나타나지 않는다.)