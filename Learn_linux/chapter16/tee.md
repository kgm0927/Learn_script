

---

# 표준 입력에서 데이터를 읽고, 표준 출력과 파일에 출력


배관 시설에 비유해서, 리눅스는 파이프 모양과 똑같은 “T”라는 tee 명령어를 제공한다. 이 tee 라는 프로그램은 표준 입력으로부터 데이터를 읽어서 표준 출력(배관을 따라 데이터가 계속 이동하는 것을 허용하는 것이다)과 하나 이상의 다른 파일을 동시에 출력한다. 이는 작업이 진행되고 있을 때, 중간 지점의 파이프라인에 있는 내용을 알고 싶을 때 유용하다.


이전의 예제를 한 번더 반복한다. **이번에는 파이프라인 내용에 grep 필터가 적용되기 전 디렉토리의 목록 전부를 ls.txt 파일에 저장할 것이다.**


``` shell
kgm0917@DESKTOP-DT2VQLB:~$ ls /usr/bin |tee ls.txt|grep zip

gpg-zip

gunzip

gzip

streamzip

zipdetails

kgm0917@DESKTOP-DT2VQLB:~$ ls

lazy_dog.txt ls-error.txt ls-output.txt ls.txt movie.mpeg
```


이제 내부를 보면 ls.txt 파일이 만들어져 있음을 알 수 있다.