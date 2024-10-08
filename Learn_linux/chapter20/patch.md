
### 원본에 diff 적용하기

patch 프로그램은 텍스트 파일에 수정 내용을 적용하기 위해 사용된다. diff의 출력 결과를 받아서 이전 버전의 파일을 새 버전으로 변환시키는 데 일반적으로 사용된다. 유명한 예를 들어보자. 리눅스 커널은 작은 수정 사항들을 끊임없이 제출하는 기여자들로 구성된 거대하고 느슨한 구조의 팀에 의해 개발된다. 리눅스 커널은 수백만 줄의 코드로 이루어져 있지만 한 기여자에 의해 한 번에 만들어지는 수정 사항은 매우 작다. 소스 기여자들이 매변 작은 수정 사항을 만들 때마다 전체 커널 소스 트리의 개발자들에게 그것을 보낸다는 것은 말도 안 된다. 그 대신에 diff 파일만이 보내진다. ==diff 파일은 커널의 예전 버전와 기여자가 변경한 새 버전 간의 변경 내역을 포함하고 있다.== diff의 수신자는 patch 프로그램을 사용하여 자신의 소스 트리에 변경 내역을 적용한다. diff/patch의 사용은 두 가지 중요한 이점을 제공한다.

- diff 파일은 전체 소스 트리의 크기에 비해 매우 작다.
- diff 파일은 변경 내역을 간결하게 보여준다. 따라서 패치 검토자가 평가를 빠르게 할 수 있게 한다.

물론 diff/patch는 소스 코드뿐만 아니라 모든 텍스트 파일에도 사용이 가능하다. 설정 파일이나 다른 텍스트 파일들에도 동일하게 적용할 수 있다.

patch 용으로 [[diff]] 파일을 준비하기 위해 다음과 같이 GNU 문서에서 제안하는 diff의 사용법을 쓸 수 있다.

`diff -Naur old_file new_file > diff_file`

*old_file*과 *new_file* 위치에는 단일 파일들이나 파일을 포함하는 디렉토리들을 지정할 수 있다. r 옵션은 디렉토리 트리의 재귀 순회를 지원한다.

diff 파일이 생성되면 이전 파일을 새 파일로 패치하기 위해 다음과 같이 적용할 수 있다.

`patch < diff_file`

우리의 테스트 파일로 이를 보여줄 것이다.

``` shell 

                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ diff -Naur file1.txt file2.txt > patchfile.txt 
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ patch <patchfile.txt                                                
patching file file1.txt
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ cat file1.txt               
b
c
d
e

```

이 예제에서는 patchfile.txt라는 diff 파일을 생성하고 patch 프로그램을 사용하여 패치했다. 패치를 위해 그 대상 파일을 지정하지 않았다는 것에 주목해라. diff 파일(통합 방식의)은 이미 헤더에 그 파일명을 포함하고 있다. 패치가 적용되면 이제 file1.txt가 file2.txt와 일치하는 것을 볼 수 있다.

patch는 많은 옵션들과 패치를 분석하고 편집할 수 있는 추가적인 유틸리티 프로그램도 있다.