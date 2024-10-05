

속도는 느리지만 고성능 압축 프로그램


줄리안 시워드(Julian Seward)가 개발한 bzip2 프로그램은 [[gzip]]과 유사하나 다른 압축 알고리즘을 사용한다. 압축 속도는 느리지만 높은 압축율을 자랑한다. 하지만 gzip과 거의 같은 방식이다. bzip2로 압축된 파일은 .bz2로 표시된다.

``` shell
┌──(kali㉿kali)-[~]
└─$ ls -l /etc > foo.txt          
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ ls -l foo.txt       
-rw-rw-r-- 1 kali kali 19944  9월 24일  06:09 foo.txt
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ bzip2 foo.txt
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ ls -l foo.txt.bz2
-rw-rw-r-- 1 kali kali 3013  9월 24일  06:09 foo.txt.bz2
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ dir          
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos  foo.txt.bz2  playground
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ bunzip2 foo.txt.bz2
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ bunzip2 foo.txt.bz2
bunzip2: Can't open input file foo.txt.bz2: No such file or directory.
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ dir                
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos  foo.txt  playground

```

bzip2 프로그램은 gzip과 같은 방식으로 사용할 수 있다. gzip에서 설명한 모든 옵션(-r을 제외한)들을 bzip2도 지원한다. 단, 압축율을 설정하는 옵션 (-number)의 경우 다른 의미로 사용된다. bzip2와 짝을 이루는 압축 해제 프로그램은 bunzip2와 bzcat이다.

bzip2에는 bzip2recover라는 프로그램이 있는데 손상된 .bz2 파일을 복구시켜 준다.

>[!tip] 무조건 압축을 해야 한다는 강박관념을 버리자.
>필자는 가끔씩 사람들이 이미 효율적인 압축 알고리즘으로 압축된 파일을 또 압축하려고 하는 경우를 보곤 한다.
>`$ gzip picture.jpg`
>절대 그러지 말길! 이러한 작업은 시간과 공간을 낭비하는 것이다. 이미 압축된 파일에 한 번 더 압축하는 것은 결국 파일을 크게 만드는 것과 매한가지다. 그 이유는 모든 압축 기법들은 압축을 설명하는 오버헤드를 포함하기 때문에 이미 중복된 정보가 없는 파일들을 또 다시 압축하면 여지없이 추가적인 오버헤드가 생길 것이다.



