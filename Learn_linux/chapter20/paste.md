
### 파일들의 행을 합친다.


paste는 [[cut]]의 반대 명령어다. 파일에서 텍스트 열을 추출하는 대신에, 파일에 하나 이상의 텍스트 열을 추가한다. 복수의 파일을 읽어 들이고, 각 파일에서 찾은 필드를 표준 출력의 단일 스트림으로 결합한다. cut과 유사하게, paste는 복수의 파일 인자와(또는) 표준 입력을 받아들인다. paste가 어떻게 동작하는지 보려면, 출시 목록을 연대순으로 만들기 위해 distros.txt 파일에 약간의 수정을 가해야 할 것이다.


sort로 했던 이전 작업에서, 먼저 날짜로 정렬된 배포판 목록을 만들고 그 결과를 `distros-by-date.txt`파일로 저장할 것이다.

```shell
                                                                                                                                                     
┌──(kali㉿kali)-[~]
└─$ sort -k 3.7nbr -k 3.1nbr -k 3.4nbr distros.txt > distros-bydate.txt

```

그 다음, 파일에서 cut으로 첫 두 필드(배포판명과 버전)를 추출하고 그 결과를 distros-versions.txt로 저장할 것이다.

```shell
┌──(kali㉿kali)-[~]
└─$ cut -f 1,2 distros-bydate.txt > distros-versions.txt
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ head distros-versions.txt
Fedora  10
Ubuntu  8.10
SUSE    11.0
Fedora  9
Ubuntu  8.04
Fedora  8
Ubuntu  7.10
SUSE    10.3
Fedora  7
SUSE    10.1

```

준비 과정의 마지막은 출시 일자를 추출하고 distros-dates.txt 파일로 저장하는 것이다.

```shell
┌──(kali㉿kali)-[~]
└─$ cut -f 3 distros-bydate.txt > distros-dates.txt     
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ head distros-dates.txt   
11/25/2008
10/30/2008
06/19/2008
05/13/2008
04/24/2008
11/08/2007
10/18/2007
10/04/2007
05/31/2007
05/11/2007
                    
```

이제 우리는 필요한 부분을 가지고 있다. paste로 날짜 열을 배포한 이름과 버전 앞에 넣고 연대순 목록을 만드는 것으로 완료한다. 이것은 단순히 paste에 준비된 파일들을 순서대로, 인자로 나열하면 된다.

``` shell
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ paste distros-dates.txt distros-versions.txt
11/25/2008      Fedora  10
10/30/2008      Ubuntu  8.10
06/19/2008      SUSE    11.0
05/13/2008      Fedora  9
04/24/2008      Ubuntu  8.04
11/08/2007      Fedora  8
10/18/2007      Ubuntu  7.10
10/04/2007      SUSE    10.3
05/31/2007      Fedora  7
05/11/2007      SUSE    10.1
04/19/2007      Ubuntu  7.04
12/07/2006      SUSE    10.2
10/26/2006      Ubuntu  6.10
10/24/2006      Fedora  6
06/01/2006      Ubuntu  6.06
03/20/2006      Fedora  5

```