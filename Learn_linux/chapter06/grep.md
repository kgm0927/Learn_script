

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