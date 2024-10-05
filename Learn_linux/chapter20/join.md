
### 공통 필드로 두 파일의 행을 합친다.


어떤 점에서는 join이 파일에 열을 추가하는 paste와 같아 보인다. 하지만 그것은 어쩌면 유일한 방식이다. join은 항상 공유키 필드로 복수의 **테이블**에서 자료를 가져와 원하는 결과의 형태로 합치는 **관계형 데이터베이스**와 관련된 작업이다. join 프로그램도 이와 동일하게 동작한다. ==공유키 필드 기반으로 복수의 파일로부터 자료를 합친다.==


관계형 데이터베이스에서 join 명령이 어떻게 동작하는지 보기 위해 각각 하나의 레코드를 포함한 두 테이블을 가지고 있는 매우 작은 데이터베이스가 있다고 가정하자. 첫 번째 테이블인 CUSTOMERS는 고객 번호(CUSTNUM), 고객의 이름(FNAME), 고객의 성(LNAME) 이렇게 세계의 필드가 있다.

``` shell
CUSTNUM    FNAME    LNAME
========   =====    ======
4681934    John     Smith
```

두 번째 테이블은 ORDER로 주문 번호(ORDERNUM), 고객 번호(CUSTNUM), 주문량(QUAN), 물품(ITEM)순을 네 개의 필드가 있다.

```shell
ORDERNUM    CUSTNUM    QUAN    ITEM
========    =======    =====   ====
3014953305  4681934    1       Blue Widget
```

두 테이블 모두 CUSTNUM 필드를 공유하는 것에 주목하라. 이는 테이블 간의 관계 형성을 허용하는 것으로 매우 중요하다.


join 명령의 실행은 송장을 준비하는 것처럼 유용한 결과를 위해 두 테이블을 합치는 것을 허용한다. 두 테이블의 CUSTUM 필드에서 일치하는 값을 활용하여 join 명령을 수행하면 다음과 같은 결과가 만들어진다.

``` shell

ORDERNUM    CUSTNUM    QUAN    ITEM
========    =======    =====   ====
3014953305  4681934    1       Blue Widget
```

join 프로그램을 시현하기 위해 공유키를 가진 두 개의 파일을 만들어야 한다. 이를 위해, distros-bydate.txt 파일을 사용할 것이다. 이 파일로부터 두 개의 추가적인 파일을 생성할 것이다. 하나는 출시 일자(시현을 위해 공유키 필드가 될)와 배포판 이름을 포함한다.

``` shell
┌──(kali㉿kali)-[~]
└─$ cut -f 1,1  distros-bydate.txt > distros-names.txt  

┌──(kali㉿kali)-[~]
└─$ paste distros-dates.txt distros-names.txt > distros-key-names.txt
                      
┌──(kali㉿kali)-[~]
└─$ head distros-key-names.txt 
11/25/2008      Fedora
10/30/2008      Ubuntu
06/19/2008      SUSE
05/13/2008      Fedora
04/24/2008      Ubuntu
11/08/2007      Fedora
10/18/2007      Ubuntu
10/04/2007      SUSE
05/31/2007      Fedora
05/11/2007      SUSE

```

두 번째 파일은 출시 일자와 버전 번호를 포함한다.

``` shell
┌──(kali㉿kali)-[~]
└─$ cut -f 2,2 distros-bydate.txt > distros-vernums.txt 
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ paste distros-dates.txt distros-vernums.txt > distros-keyvernums.txt
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ head distros-keyvernums.txt
11/25/2008      10
10/30/2008      8.10
06/19/2008      11.0
05/13/2008      9
04/24/2008      8.04
11/08/2007      8
10/18/2007      7.10
10/04/2007      10.3
05/31/2007      7
05/11/2007      10.1
                       
```

이제 두 파일은 공유키("출시 일자" 필드)를 가지게 된다. join이 제대로 동작하려면 파일들이 키 필드를 기준으로 정렬되어 있는 것이 중요하다.

``` shell
┌──(kali㉿kali)-[~]
└─$ join distros-key-names.txt distros-keyvernums.txt | head 
11/25/2008 Fedora 10
10/30/2008 Ubuntu 8.10
06/19/2008 SUSE 11.0
05/13/2008 Fedora 9
04/24/2008 Ubuntu 8.04
11/08/2007 Fedora 8
10/18/2007 Ubuntu 7.10
10/04/2007 SUSE 10.3
05/31/2007 Fedora 7
05/11/2007 SUSE 10.1

```

또한 기본적으로 join은 입력 필드 구분자로 공백문자로 사용하고 출력 필드 구분자로 하나의 스페이스를 이용한다. 이러한 방식은 옵션을 저장하여 변경할 수 있다. 좀 더 자세한 정보는 join의 [[man]] 페이지를 참고하라.